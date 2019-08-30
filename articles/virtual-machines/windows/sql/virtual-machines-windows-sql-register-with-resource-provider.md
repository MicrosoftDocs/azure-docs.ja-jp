---
title: Azure 内の SQL Server 仮想マシンを SQL VM リソースプロバイダーに登録する | Microsoft Docs
description: SQL Server VM を SQL VM リソースプロバイダーに登録して管理性を向上させます。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: a4e217ce3fcfae0f7d103c545ff385f2dffe582d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100490"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Azure 内の SQL Server 仮想マシンを SQL VM リソースプロバイダーに登録する

この記事では、Azure 内の SQL Server 仮想マシン (VM) を SQL VM リソースプロバイダーに登録する方法について説明します。 

Azure portal を介して SQL Server VM の Azure Marketplace イメージをデプロイすると、その SQL Server VM が自動的にリソースプロバイダーに登録されます。 Azure Marketplace からイメージを選択するのではなく Azure 仮想マシンに SQL Server を自分でインストールすることを選択した場合や、SQL Server を含むカスタム VHD から Azure VM をプロビジョニングする場合は、以下の目的で、お使いの SQL Server VM をリソースプロバイダーに登録する必要があります。

- **コンプライアンス**:Microsoft 製品の利用規約に従って、お客様は [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を利用している場合に Microsoft にレポートする必要があります。 これを行うには、SQL VM リソースプロバイダーへの登録が必要です。 

- **機能面の利点**: お使いの SQL Server VM をリソースプロバイダーに登録すると、[自動修正](virtual-machines-windows-sql-automated-patching.md)、[自動バックアップ](virtual-machines-windows-sql-automated-backup-v2.md)、監視と管理機能が利用できるようになります。 また、[ライセンス](virtual-machines-windows-sql-ahb.md)や[エディション](virtual-machines-windows-sql-change-edition.md)の柔軟性も実現されます。 これらの機能は、以前は Azure Marketplace の SQL Server VM イメージでしか利用できませんでした。

SQL VM リソースプロバイダーを利用するには、SQL VM リソースプロバイダーをサブスクリプションに登録することも必要です。 これは Azure portal、Azure CLI、または PowerShell を使用して実行できます。 

## <a name="prerequisites"></a>前提条件

SQL Server VM をリソースプロバイダーに登録するには、以下のものが必要になります。 

- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。 
- [Azure CLI](/cli/azure/install-azure-cli) と [PowerShell](/powershell/azure/new-azureps-module-az)。 


## <a name="register-with-sql-vm-resource-provider"></a>SQL VM リソースプロバイダーに登録する
[SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)が VM にインストールされていない場合は、軽量 SQL 管理モードを指定することによって SQL VM リソースプロバイダーに登録できます。 軽量 SQL 管理モードでは、SQL VM リソースプロバイダーによって SQL IaaS 拡張機能が[軽量モード](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode)で自動インストールされ、SQL Server インスタンス メタデータが検証されます。その際、SQL Server サービスの再起動は行われません。 SQL VM リソースプロバイダーへの登録時に必要な SQL Server ライセンスの種類として "PAYG" または "AHUB" を指定する必要があります。

[軽量モード](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode)で SQL VM リソースプロバイダーに登録することにより、コンプライアンスが確保され、柔軟なライセンス管理と SQL Server エディションのインプレース更新が可能になります。 フェールオーバー クラスター インスタンスとマルチインスタンス デプロイは、軽量モードでのみ SQL VM リソースプロバイダーに登録できます。 Azure portal にある指示に従って[完全モード](virtual-machines-windows-sql-server-agent-extension.md#install-in-full-mode)にアップグレードすれば、いつでも SQL Server を再起動して包括的な管理機能セットを有効にすることができます。 


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
SQL Server IaaS 拡張機能が既に VM にインストールされている場合は、次のコード スニペットを使用して SQL VM リソースプロバイダーに登録します。 SQL VM リソースプロバイダーへの登録時に必要な SQL Server ライセンスの種類として、従量課金制 (`PAYG`) または Azure ハイブリッド特典 (`AHUB`) を指定する必要があります。 

次の PowerShell コード スニペットを使用して SQL Server VM を登録します。

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

有料エディション (Enterprise または Standard) の場合:

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type AHUB 

  ```

無料エディション (Developer、Web、Express) の場合:

  ```azurecli-interactive
  # Register Developer, Web, or Express self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```
---

SQL IaaS 拡張機能が既に VM にインストールされている場合、SQL VM リソースプロバイダーに登録すると、Microsoft.SqlVirtualMachine/SqlVirtualMachines という種類のメタデータ リソースが作成されます。 以下は、SQL IaaS 拡張機能が既に VM にインストールされている場合に SQL VM リソースプロバイダーへの登録を行うコード スニペットです。 SQL VM リソースプロバイダーへの登録時に必要な SQL Server ライセンスの種類として "PAYG" または "AHUB" を指定する必要があります。

  ```powershell-interactive
  # Get the existing  Compute VM
   $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
   # Register with SQL VM resource provider
   New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}
  ```


## <a name="register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms"></a>Windows Server 2008 VM 上の SQL Server 2008 または 2008 R2 を登録する

Windows Server 2008 にインストールされている SQL Server 2008 および 2008 R2 は、[エージェントなし](virtual-machines-windows-sql-server-agent-extension.md)モードで SQL VM リソースプロバイダーに登録できます。 このオプションにより、コンプライアンスが確保され、SQL Server VM を Azure portal の限られた機能で監視できるようになります。

次の表では、登録時に指定するパラメーターに使用可能な値を詳細に示しています。

| パラメーター | 使用可能な値                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `AHUB` または `PAYG`                    |
| **sqlImageOffer**  | `SQL2008-WS2008` または `SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Windows Server 2008 インスタンス上の SQL Server 2008 または 2008 R2 インスタンスを登録するには、次の Powershell または Az CLI のコード スニペットを使用します。  

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type AHUB --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```

---

## <a name="verify-registration-status"></a>登録状態を確認する
お使いの SQL Server VM が既に SQL VM リソースプロバイダーに登録されているかどうかは、Azure portal、Azure CLI、または PowerShell を使用して確認できます。 

### <a name="azure-portal"></a>Azure ポータル 

1. [Azure Portal](https://portal.azure.com) にサインインします。 
1. お使いの [SQL Server 仮想マシン](virtual-machines-windows-sql-manage-portal.md)に移動します。
1. 一覧から SQL Server VM を選択します。 お使いの SQL Server VM がここに表示されていない場合は、SQL VM リソースプロバイダーに登録されていない可能性があります。 
1. **[状態]** の値を確認します。 **[状態]** が **[成功]** の場合は、その SQL Server VM が正常に SQL VM リソースプロバイダーに登録されています。 

![SQL RP 登録による状態を確認する](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>コマンド ライン

Az CLI または PowerShell を使用して現在の SQL Server VM の登録状態を確認します。 登録が成功していれば、`ProvisioningState` に `Succeeded` と表示されます。 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> `
  -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```

---

エラーは、SQL Server VM がリソースプロバイダーに登録されていないことを示します。 

## <a name="register-the-sql-vm-resource-provider-with-a-subscription"></a>SQL VM リソースプロバイダーをサブスクリプションに登録する 

お使いの SQL Server VM を SQL VM リソースプロバイダーに登録するには、そのリソースプロバイダーを自分のサブスクリプションに登録する必要があります。 これは Azure portal、Azure CLI、または PowerShell を使用して実行できます。

### <a name="azure-portal"></a>Azure ポータル

1. Azure portal を開き、 **[すべてのサービス]** に移動します。 
1. **[サブスクリプション]** に移動し、目的のサブスクリプションを選択します。  
1. **[サブスクリプション]** ページで、 **[リソースプロバイダー]** に移動します。 
1. フィルター内に「**sql**」と入力し、SQL 関連のリソースプロバイダーを表示します。 
1. 必要なアクションに応じて、**Microsoft.SqlVirtualMachine** プロバイダーで **[登録]** 、 **[再登録]** 、または **[登録解除]** を選択します。 

![プロバイダーの変更](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>コマンド ライン

Az CLI または PowerShell を使用して、SQL VM リソースプロバイダーを自分の Azure サブスクリプションに登録します。 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
次のコード スニペットは、SQL VM リソース プロバイダーを Azure サブスクリプションに登録します。 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>解説

- SQL VM リソースプロバイダーでサポートされているのは、Azure Resource Manager を介してデプロイされた SQL Server VM のみです。 クラシック モデルを介してデプロイされた SQL Server VM はサポートされません。 
- SQL VM リソースプロバイダーでサポートされているのは、パブリック クラウドにデプロイされた SQL Server VM のみです。 プライベート クラウドや政府機関向けクラウドにデプロイされている場合はサポートされません。 
 

## <a name="frequently-asked-questions"></a>よく寄せられる質問 

**Azure Marketplace の SQL Server イメージからプロビジョニングされた SQL Server VM を登録する必要はありますか?**

いいえ。 Azure Marketplace の SQL Server イメージからプロビジョニングされた VM は自動的に登録されます。 SQL VM リソースプロバイダーへの登録が必要になるのは、VM が Azure Marketplace の SQL Server イメージからプロビジョニングされて "*おらず*"、SQL Server を自分でインストールした場合のみです。

**SQL VM リソース プロバイダーは、すべての顧客が使用できますか。** 

はい。 お客様がご自身の SQL Server VM を SQL VM リソースプロバイダーにインストールする必要があるのは、Azure Marketplace の SQL Server イメージを使用せずに自分で SQL Server をインストールした場合やカスタム VHD を購入した場合です。 すべての種類のサブスクリプション (Direct、Enterprise Agreement、Cloud Solution Provider) で所有されているVM は、SQL VM リソースプロバイダーに登録できます。

**SQL Server VM に SQL Server IaaS 拡張機能が既にインストールされている場合、SQL VM リソースプロバイダーに登録する必要はありますか?**

SQL Server VM が自分でインストールしたもので、Azure Marketplace の SQL Server イメージからプロビジョニングしたものではない場合は、SQL Server IaaS 拡張機能をインストール済みであっても SQL VM リソースプロバイダーに登録する必要があります。 SQL VM リソースプロバイダーに登録すると、Microsoft.SqlVirtualMachines という種類の新しいリソースが作成されます。 SQL Server IaaS 拡張機能をインストールしても、そのリソースは作成されません。

**SQL VM リソースプロバイダーへの登録時の既定の管理モードは何ですか?**

SQL VM リソースプロバイダーに登録する際の既定の管理モードは "*完全*" です。 SQL VM リソースプロバイダーへの登録時に SQL Server 管理プロパティが設定されていない場合、このモードは完全管理として設定されます。 SQL Server IaaS 拡張機能が VM にインストールされていることが、完全管理モードで SQL VM リソースプロバイダーに登録するための前提条件になります。

**SQL VM リソースプロバイダーに登録するための前提条件は何ですか?**

軽量モードまたはエージェントなしモードで SQL VM リソース プロバイダーに登録するための前提条件はありません。 完全モードで SQL VM リソースプロバイダーに登録するための前提条件は、SQL Server IaaS 拡張機能が VM にインストールされていることです。

**SQL Server IaaS 拡張機能を VM にインストールしていない場合、SQL VM リソースプロバイダーに登録できますか?**

はい。SQL Server IaaS 拡張機能を VM にインストールしていない場合は、軽量管理モードで SQL VM リソースプロバイダーに登録できます。 軽量モードの場合、SQL VM リソースプロバイダーはコンソール アプリを使用して、その SQL Server インスタンスのバージョンとエディションを確認します。 

SQL VM リソースプロバイダーへの登録時の既定の SQL 管理モードは "_完全_" です。 SQL VM リソース プロバイダーに登録したときに SQL 管理プロパティが設定されていない場合、このモードは完全管理として設定されます。 SQL IaaS 拡張機能を VM にインストールすることが、完全管理モードで SQL VM リソース プロバイダーに登録するための前提条件です。

**SQL VM リソースプロバイダーに登録すると、VM にエージェントがインストールされますか?**

いいえ。 SQL VM リソースプロバイダーに登録した場合は、新しいメタデータ リソースのみが作成されます。 VM にエージェントがインストールされることはありません。

SQL Server IaaS 拡張機能は、完全管理を有効にする場合のみ必要です。 管理モードを軽量から完全にアップグレードすると、SQL Server IaaS 拡張機能がインストールされ、SQL Server が再起動されます。

**SQL Server VM リソースプロバイダーに登録すると、VM 上の SQL Server は再起動されますか?**

いいえ。 SQL VM リソースプロバイダーに登録した場合は、新しいメタデータ リソースのみが作成されます。 VM 上の SQL Server が再起動されることはありません。 

SQL Server の再起動は、SQL Server IaaS 拡張機能をインストールする場合にのみ必要です。 また、SQL Server IaaS 拡張機能は、完全管理を有効にする場合に必要になります。 管理モードを軽量から完全にアップグレードすると、SQL Server IaaS 拡張機能がインストールされ、SQL Server が再起動されます。

**SQL VM リソースプロバイダーに登録するときの軽量モードとエージェントなし管理モードの違いは何ですか?** 

エージェントなし管理モードは、Windows Server 2008 上の SQL Server 2008 と SQL Server 2008 R2 のみに使用できます。 これは、これらのバージョンに唯一使用できる管理モードです。 その他すべてのバージョンの SQL Server に使用可能な 2 つの管理モードとして軽量モードと完全モードがあります。 

エージェントなしモードでは、SQL Server のバージョンとエディションのプロパティをお客様が設定する必要があります。 軽量モードでは、VM が照会され、SQL Server インスタンスのバージョンとエディションが確認されます。

**Azure CLI を使用することで、軽量モードまたはエージェントなしモードで SQL VM リソースプロバイダーに登録できますか?**

いいえ。 この管理モード プロパティを使用できるのは、Azure PowerShell を使用して SQL VM リソースプロバイダーに登録する場合のみです。 Azure CLI では、SQL Server 管理プロパティの設定はサポートされていません。 常に、既定の完全管理モードで SQL VM リソースプロバイダーに登録します。

**SQL Server のライセンスの種類を指定せずに SQL VM リソースプロバイダーに登録できますか?**

いいえ。 SQL VM リソースプロバイダーに登録している場合、SQL Server のライセンスの種類は省略可能なプロパティではありません。 Azure CLI と PowerShell の両方を使用してすべての管理モード (エージェントなし、軽量、完全) で SQL VM リソースマネージャーに登録するときに、SQL Server のライセンスの種類を従量課金制または Azure ハイブリッド特典として設定する必要があります。

**SQL Server IaaS 拡張機能をエージェントなしモードから完全モードにアップグレードできますか?**

いいえ。 エージェントなしモードでは、管理モードを完全または軽量にアップグレードすることはできません。 これは Windows Server 2008 の技術的な制限です。

**SQL Server IaaS 拡張機能を軽量モードから完全モードにアップグレードできますか?**

はい。 管理モードを軽量から完全にアップグレードすることは、PowerShell または Azure portal でサポートされています。 これには、SQL Server の再起動が必要です。

**SQL Server IaaS 拡張機能を完全モードからエージェントなしまたは軽量管理モードにダウングレードすることはできますか?**

いいえ。 SQL Server IaaS 拡張機能の管理モードのダウングレードはサポートされていません。 この管理モードは、完全モードから軽量またはエージェントなしモードにダウングレードできません。また、軽量モードからエージェントなしモードにダウングレードすることもできません。 

管理モードを完全管理から変更するには、SQL Server IaaS 拡張機能を削除してください。 その後、Micorsoft.SqlVirtualMachine リソースを削除し、SQL Server VM を SQL VM リソースプロバイダーに再登録します。

**Azure portal から SQL VM リソースプロバイダーに登録できますか?**

いいえ。 Azure portal では SQL VM リソースプロバイダーへの登録を利用できません。 完全管理モードでの SQL VM リソースプロバイダーへの登録は、Azure CLI または PowerShell でサポートされています。 軽量またはエージェントなし管理モードでの SQL VM リソースプロバイダーへの登録は、Azure PowerShell API のみでサポートされています。

**SQL Server をインストールする前に、VM を SQL VM リソースプロバイダーに登録できますか?**

いいえ。 SQL VM リソースプロバイダーへの登録を成功させるには、VM に少なくとも 1 つの SQL Server インスタンスが必要です。 VM 上に SQL Server インスタンスが存在しない場合、新しい Micosoft.SqlVirtualMachine リソースはエラー状態になります。

**複数の SQL Server インスタンスがある場合、VM を SQL VM リソースプロバイダーに登録できますか?**

はい。 SQL VM リソースプロバイダーが登録する SQL Server インスタンスは 1 つだけです。 複数のインスタンスがある場合、SQL VM リソースプロバイダーは既定の SQL Server インスタンスを登録します。 既定のインスタンスがない場合、軽量モードでの登録のみがサポートされます。 軽量から完全管理モードにアップグレードするには、既定の SQL Server インスタンスが存在するか、VM に名前付き SQL Server インスタンスが 1 つだけ存在する必要があります。

**SQL Server フェールオーバー クラスター インスタンスを SQL VM リソースプロバイダーに登録できますか?**

はい。 Azure VM 上の SQL Server フェールオーバー クラスター インスタンスは SQL VM リソースプロバイダーに軽量モードで登録できます。 ただし、SQL Server フェールオーバー クラスター インスタンスを完全管理モードにアップグレードすることはできません。

**Always On 可用性グループが構成されている場合、自分の VM を SQL VM リソースプロバイダーに登録できますか?**

はい。 Always On 可用性グループ構成に参加している場合、Azure VM 上の SQL Server インスタンスを SQL VM リソースプロバイダーに登録することに制限はありません。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上の SQL Server に関する FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上の SQL Server の価格ガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上の SQL Server のリリース ノート](virtual-machines-windows-sql-server-iaas-release-notes.md)
