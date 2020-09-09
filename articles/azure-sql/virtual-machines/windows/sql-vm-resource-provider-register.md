---
title: SQL VM リソース プロバイダーに登録する
description: Azure SQL Server 仮想マシンを SQL VM リソース プロバイダーに登録して、Azure Marketplace の外部にデプロイされた SQL Server 仮想マシンの機能を有効にし、コンプライアンスと管理容易性を向上させます。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6c52275735a6558a625e2118761d7ba98509dbe1
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497071"
---
# <a name="register-a-sql-server-vm-in-azure-with-the-sql-vm-resource-provider-rp"></a>Azure の SQL Server VM を SQL VM リソース プロバイダー (RP) に登録する
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、Azure 内の SQL Server 仮想マシン (VM) を SQL VM リソース プロバイダー (RP) に登録する方法について説明します。 リソース プロバイダーに登録すると、仮想マシンのリソースとは別のリソースである、**SQL 仮想マシン**の "_リソース_" がサブスクリプション内に作成されます。 リソース プロバイダーから SQL Server VM を登録解除すると、**SQL 仮想マシン**の "_リソース_" は削除されますが、実際の仮想マシンは削除されません。 

Azure portal を介して SQL Server VM の Azure Marketplace イメージをデプロイすると、その SQL Server VM が自動的にリソースプロバイダーに登録されます。 ただし、Azure 仮想マシンに SQL Server を自分でインストールすること、またはカスタム VHD から Azure 仮想マシンをプロビジョニングすることを選択する場合は、次の目的で SQL Server VM をリソース プロバイダーに登録する必要があります。

- **機能面の利点**: SQL Server VM をリソース プロバイダーに登録すると、[自動修正](automated-patching.md)、[自動バックアップ](automated-backup.md)、および監視と管理の機能が利用できるようになります。 また、[ライセンス](licensing-model-azure-hybrid-benefit-ahb-change.md)や[エディション](change-sql-server-edition.md)の柔軟性も実現されます。 これらの機能は、以前は Azure Marketplace からデプロイされた SQL Server VM イメージでしか利用できませんでした。 

- **コンプライアンス**:SQL VM リソース プロバイダーに登録すると、製品の条件に指定されているとおりに Azure ハイブリッド特典が有効になっていることを Microsoft に通知する要件を満たす簡単な方法が提供されます。 このプロセスにより、リソースごとにライセンス登録フォームを管理する必要がなくなります。  

- **無料の管理**: 3 つのすべての管理モードでの SQL VM リソース プロバイダーへの登録は完全に無料です。 リソース プロバイダーまたは管理モードの変更に関連する追加のコストは発生しません。 

- **簡略化されたライセンス管理**:SQL VM リソース プロバイダーに登録すると、SQL Server ライセンスの管理が簡略化されるほか、[Azure portal](manage-sql-vm-portal.md)、Azure CLI、または PowerShell を使用して、Azure ハイブリッド特典が有効になった SQL Server VM をすばやく識別することができます。 

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

SQL VM リソース プロバイダーを利用するには、最初に[サブスクリプションをリソース プロバイダーに登録する](#register-subscription-with-rp)必要があります。これにより、リソース プロバイダーは、その特定のサブスクリプション内にリソースを作成することができます。

## <a name="prerequisites"></a>前提条件

SQL Server VM をリソース プロバイダーに登録するには、次のものが必要になります。 

- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- パブリック クラウドまたは Azure Government クラウドにデプロイされた Azure Resource Model の [SQL Server VM](create-sql-vm-portal.md)。 
- [Azure CLI](/cli/azure/install-azure-cli) または [PowerShell](/powershell/azure/new-azureps-module-az) の最新バージョン。 

## <a name="management-modes"></a>管理モード

[SQL IaaS 拡張機能](sql-server-iaas-agent-extension-automate-management.md)がまだインストールされていない場合、SQL VM リソース プロバイダーに登録すると、登録プロセス中に指定した 3 つの管理モードのいずれかで、SQL Server IaaS 拡張機能が自動的にインストールされます。 管理モードを指定しないと、フル管理モードで SQL IaaS 拡張機能がインストールされます。  

SQL IaaS 拡張機能が既に手動でインストールされている場合は、既にフル管理モードになっており、フル モードでリソース プロバイダーに登録しても、SQL Server サービスは再起動されません。

次の 3 つの管理モードがあります。

- **軽量**モードでは SQL Server の再起動は必要ありませんが、サポートされるのは、SQL Server のライセンスの種類とエディションの変更のみになります。 このオプションは、複数のインスタンスがあるか、フェールオーバー クラスター インスタンス (FCI) に参加している SQL Server VM に使用します。 軽量モードを使用する場合、メモリまたは CPU への影響はなく、また関連コストはありません。 最初に軽量モードで SQL Server VM を登録してから、予定メンテナンス期間中にフル モードにアップグレードすることをお勧めします。  

- **フル** モードでは、すべての機能が提供されますが、SQL Server の再起動とシステム管理者権限が必要です。 これは、SQL IaaS 拡張機能を手動でインストールするときに既定でインストールされるオプションです。 単一のインスタンスがある SQL Server VM を管理する場合に使用します。 フル モードでは、メモリと CPU に与える影響が最小の 2 つの Windows サービスがインストールされます。これらは、タスク マネージャーを通して監視できます。 完全管理モードの使用に関連するコストはありません。 

- **NoAgent** モードは Windows Server 2008 にインストールされた SQL Server 2008 および SQL Server 2008 R2 の専用モードです。 NoAgent モードを使用する場合、メモリまたは CPU への影響はありません。 NoAgent 管理者モードの使用に関連するコストはありません。 

PowerShell を使用して、次のように SQL Server IaaS エージェントの現在のモードを確認できます。 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>リソースプロバイダーを使用してサブスクリプションを登録する

お使いの SQL Server VM を SQL VM リソースプロバイダーに登録するには、まずそのリソースプロバイダーに自分のサブスクリプションを登録する必要があります。 これにより、SQL VM リソース プロバイダーは、サブスクリプション内にリソースを作成できるようになります。  これは Azure portal、Azure CLI、または PowerShell を使用して実行できます。

### <a name="azure-portal"></a>Azure portal

1. Azure portal を開き、 **[すべてのサービス]** に移動します。 
1. **[サブスクリプション]** に移動し、目的のサブスクリプションを選択します。  
1. **[サブスクリプション]** ページで、 **[リソースプロバイダー]** に移動します。 
1. フィルター内に「**sql**」と入力し、SQL 関連のリソースプロバイダーを表示します。 
1. 必要なアクションに応じて、**Microsoft.SqlVirtualMachine** プロバイダーで **[登録]** 、 **[再登録]** 、または **[登録解除]** を選択します。 

   ![プロバイダーの変更](./media/sql-vm-resource-provider-register/select-resource-provider-sql.png)


### <a name="command-line"></a>コマンド ライン

Azure CLI または PowerShell を使用して、SQL VM リソース プロバイダーを自分の Azure サブスクリプションに登録します。 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-rp"></a>RP に登録する

### <a name="lightweight-management-mode"></a>軽量管理モード

[SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)が仮想マシンにインストールされていない場合は、軽量モードで SQL VM リソース プロバイダーに登録することをお勧めします。 これにより、SQL IaaS 拡張機能は[軽量モード](#management-modes)でインストールされ、SQL Server サービスの再起動が回避されます。 その後、いつでもフル モードにアップグレードできますが、これを行うと SQL Server サービスが再起動されるので、予定メンテナンス期間まで待つことをお勧めします。 

SQL Server のライセンスの種類として、使用した分を支払う従量課金制 (`PAYG`)、独自のライセンスを使用する Azure ハイブリッド特典 (`AHUB`)、または [無料の DR レプリカ ライセンス](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)をアクティブ化するディザスター リカバリー (`DR`) のいずれかを指定します。

フェールオーバー クラスター インスタンスとマルチインスタンス デプロイは、軽量モードでのみ SQL VM リソース プロバイダーに登録できます。 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLI を使用して軽量モードで SQL Server VM を登録します。 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用して軽量モードで SQL Server VM を登録します。  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>フル管理モード


SQL IaaS 拡張機能が既に仮想マシンに手動でインストールされている場合は、SQL Server サービスを再起動せずに、SQL Server VM をフル モードで登録できます。 **ただし、SQL IaaS 拡張機能がインストールされていない場合、フル モードで登録すると SQL IaaS 拡張機能はフル モードでインストールされ、SQL Server サービスが再起動されます。注意して進めてください。**


SQL Server VM をフル モードで直接登録するには (さらに、場合によっては SQL Server サービスを再起動するには)、次の PowerShell コマンドを使用します。 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>NoAgent 管理モード 

Windows Server 2008 (_R2 ではない_) にインストールされている SQL Server 2008 および 2008 R2 は、[NoAgent](#management-modes) モードで SQL VM リソース プロバイダーに登録できます。 このオプションにより、コンプライアンスが確保され、SQL Server VM を Azure portal の限られた機能で監視できるようになります。

**sqlLicenseType** として `AHUB`、`PAYG`、または `DR`、**sqlImageOffer** として `SQL2008-WS2008` または `SQL2008R2-WS2008` を指定します。 

Windows Server 2008 インスタンス上の SQL Server 2008 または 2008 R2 を登録するには、次の Azure CLI または PowerShell のコード スニペットを使用します。 


# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLI を使用して NoAgent モードで SQL Server 2008 仮想マシンを登録します。 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
Azure CLI を使用して NoAgent モードで SQL Server 2008 R2 仮想マシンを登録します。 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用して、NoAgent モードで SQL Server 2008 仮想マシンを登録します。 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  PowerShell を使用して、NoAgent モードで SQL Server 2008 R2 仮想マシンを登録します。 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full"></a>フルへのアップグレード  

"*軽量*" の IaaS 拡張機能がインストールされている SQL Server VM の場合、Azure portal、Azure CLI、または PowerShell を使用してモードを "_フル_" にアップグレードできます。 "_No-Agent_" モードの SQL Server VM は、OS が Windows 2008 R2 以上にアップグレードされた後、"_フル_" にアップグレードできます。 ダウングレードすることはできません。そうするには、SQL Server VM を SQL VM リソース プロバイダーから[登録解除](#unregister-from-rp)する必要があります。 それにより、**SQL 仮想マシン**の "_リソース_" が削除されますが、実際の仮想マシンは削除されません。 

PowerShell を使用して、次のように SQL Server IaaS エージェントの現在のモードを確認できます。 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

エージェントのモードを "フル" にアップグレードするには: 


### <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) にサインインします。
1. [SQL 仮想マシン](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) リソースに移動します。 
1. ご利用の SQL Server VM を選択し、 **[概要]** を選択します。 
1. IaaS モードが NoAgent または軽量である SQL Server VM では、**SQL IaaS 拡張機能で利用できるのはライセンスの種類とエディションの更新のみ**という内容のメッセージを選択します。

   ![ポータルでモードを変更するための選択](./media/sql-vm-resource-provider-register/change-sql-iaas-mode-portal.png)

1. **[仮想マシンで SQL Server サービスを再起動することに同意します]** チェック ボックスをオンにし、 **[確認]** を選択して IaaS モードを "フル" にアップグレードします。 

    ![仮想マシンで SQL Server サービスを再起動することに同意するためのチェック ボックス](./media/sql-vm-resource-provider-register/enable-full-mode-iaas.png)

### <a name="command-line"></a>コマンド ライン

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

次の Azure CLI コード スニペットを実行します。

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

次の PowerShell コード スニペットを実行します。

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>登録状態を確認する
お使いの SQL Server VM が既に SQL VM リソースプロバイダーに登録されているかどうかは、Azure portal、Azure CLI、または PowerShell を使用して確認できます。 

### <a name="azure-portal"></a>Azure portal 

1. [Azure portal](https://portal.azure.com) にサインインします。 
1. ご利用の [SQL Server VM](manage-sql-vm-portal.md) にアクセスします。
1. 一覧から SQL Server VM を選択します。 お使いの SQL Server VM がここに表示されていない場合は、SQL VM リソースプロバイダーに登録されていない可能性があります。 
1. **[状態]** の値を確認します。 **[状態]** が **[成功]** の場合は、その SQL Server VM が正常に SQL VM リソースプロバイダーに登録されています。 

   ![SQL RP 登録による状態を確認する](./media/sql-vm-resource-provider-register/verify-registration-status.png)

### <a name="command-line"></a>コマンド ライン

Azure CLI または PowerShell を使用して現在の SQL Server VM の登録状態を確認します。 登録が成功していれば、`ProvisioningState` に `Succeeded` と表示されます。 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

エラーは、SQL Server VM がリソースプロバイダーに登録されていないことを示します。 


## <a name="unregister-from-rp"></a>RP から登録を解除する

SQL VM リソース プロバイダーから SQL Server VM の登録を解除するには、Azure portal または Azure CLI を使用して、SQL 仮想マシン "*リソース*" を削除します。 SQL 仮想マシン "*リソース*" を削除しても、SQL Server VM は削除されません。 ただし、"*リソース*" を削除しようとしたときに仮想マシンが誤って削除される可能性があるため、注意して慎重に手順に従ってください。 

SQL VM リソース プロバイダーから SQL 仮想マシンの登録を解除するには、管理モードをフルからダウングレードする必要があります。 

### <a name="azure-portal"></a>Azure portal

Azure portal を使用してリソース プロバイダーから SQL Server VM の登録を解除するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. SQL VM リソースに移動します。 
  
   ![SQL 仮想マシン リソース](./media/sql-vm-resource-provider-register/sql-vm-manage.png)

1. **[削除]** を選択します。 

   ![SQL VM リソース プロバイダーの削除](./media/sql-vm-resource-provider-register/delete-sql-vm-resource-provider.png)

1. SQL 仮想マシンの名前を入力し、**仮想マシンの横にあるチェック ボックスをオフにします**。

   ![SQL VM リソース プロバイダーの削除](./media/sql-vm-resource-provider-register/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > 仮想マシン名の横にあるチェックボックスをオフにしないと、仮想マシンが完全に "*削除*" されます。 リソース プロバイダーから SQL Server VM の登録を解除するが、"*実際の仮想マシンを削除しない*" 場合は、このチェックボックスをオフにしてください。 

1. **[削除]** を選択して、SQL Server VM ではなく、SQL 仮想マシン "*リソース*" の削除を確認します。 

### <a name="command-line"></a>コマンド ライン

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI を使用してリソース プロバイダーから SQL Server VM の登録を解除するには、[az sql vm delete](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) コマンドを使用します。 これにより、SQL Server VM "*リソース*" が削除されますが、仮想マシンは削除されません。 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Azure CLI を使用してリソース プロバイダーから SQL Server VM の登録を解除するには、[New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) コマンドを使用します。 これにより、SQL Server VM "*リソース*" が削除されますが、仮想マシンは削除されません。 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>制限事項

SQL VM リソース プロバイダーでは次のものだけがサポートされます。
- Azure Resource Manager を介してデプロイされた SQL Server VM。 クラシック モデルを介してデプロイされた SQL Server VM はサポートされません。 
- パブリック クラウドまたは Azure Government クラウドにデプロイされた SQL Server VM。 その他のプライベート クラウドや政府機関向けクラウドにデプロイされている場合はサポートされません。 


## <a name="frequently-asked-questions"></a>よく寄せられる質問 

**Azure Marketplace の SQL Server イメージからプロビジョニングされた SQL Server VM を登録する必要はありますか?**

いいえ。 Azure Marketplace の SQL Server イメージからプロビジョニングされた VM は自動的に登録されます。 SQL VM リソースプロバイダーへの登録が必要になるのは、VM が Azure Marketplace の SQL Server イメージからプロビジョニングされて "*おらず*"、SQL Server を自分でインストールした場合のみです。

**SQL VM リソース プロバイダーは、すべての顧客が使用できますか。** 

はい。 お客様がご自身の SQL Server VM を SQL VM リソースプロバイダーにインストールする必要があるのは、Azure Marketplace の SQL Server イメージを使用せずに自分で SQL Server をインストールした場合やカスタム VHD を購入した場合です。 すべての種類のサブスクリプション (Direct、Enterprise Agreement、Cloud Solution Provider) で所有されているVM は、SQL VM リソースプロバイダーに登録できます。

**SQL Server VM に SQL Server IaaS 拡張機能が既にインストールされている場合、SQL VM リソースプロバイダーに登録する必要はありますか?**

SQL Server VM が自分でインストールしたもので、Azure Marketplace の SQL Server イメージからプロビジョニングしたものではない場合は、SQL Server IaaS 拡張機能をインストール済みであっても SQL VM リソースプロバイダーに登録する必要があります。 SQL VM リソース プロバイダーに登録すると、Microsoft.SqlVirtualMachine という種類の新しいリソースが作成されます。 SQL Server IaaS 拡張機能をインストールしても、そのリソースは作成されません。

**SQL VM リソースプロバイダーへの登録時の既定の管理モードは何ですか?**

SQL VM リソースプロバイダーに登録する際の既定の管理モードは "*完全*" です。 SQL VM リソース プロバイダーへの登録時に SQL Server 管理プロパティが設定されていない場合、モードはフル管理として設定され、SQL Server サービスが再起動されます。 最初に軽量モードで SQL VM リソース プロバイダーに登録してから、予定メンテナンス期間中にフルにアップグレードすることをお勧めします。 

**SQL VM リソースプロバイダーに登録するための前提条件は何ですか?**

軽量モードまたはエージェントなしモードで SQL VM リソース プロバイダーに登録するための前提条件はありません。 フル モードで SQL VM リソース プロバイダーに登録するための前提条件は、SQL Server IaaS 拡張機能が VM にインストールされていることです。さもないと、SQL Server サービスが再起動されます。 

**SQL Server IaaS 拡張機能を VM にインストールしていない場合、SQL VM リソースプロバイダーに登録できますか?**

はい。SQL Server IaaS 拡張機能を VM にインストールしていない場合は、軽量管理モードで SQL VM リソースプロバイダーに登録できます。 軽量モードの場合、SQL VM リソースプロバイダーはコンソール アプリを使用して、その SQL Server インスタンスのバージョンとエディションを確認します。 

SQL VM リソースプロバイダーへの登録時の既定の SQL 管理モードは "_完全_" です。 SQL VM リソース プロバイダーに登録したときに SQL 管理プロパティが設定されていない場合、このモードは完全管理として設定されます。 最初に軽量モードで SQL VM リソース プロバイダーに登録してから、予定メンテナンス期間中にフルにアップグレードすることをお勧めします。 

**SQL VM リソースプロバイダーに登録すると、VM にエージェントがインストールされますか?**

いいえ。 SQL VM リソースプロバイダーに登録した場合は、新しいメタデータ リソースのみが作成されます。 VM にエージェントがインストールされることはありません。

SQL Server IaaS 拡張機能は、完全管理を有効にする場合のみ必要です。 管理モードを軽量から完全にアップグレードすると、SQL Server IaaS 拡張機能がインストールされ、SQL Server が再起動されます。

**SQL VM リソース プロバイダーに登録すると、自分の VM 上の SQL Server が再起動されますか?**

これは、登録時に指定されたモードによって異なります。 軽量または NoAgent モードが指定されている場合、SQL Server サービスは再起動されません。 ただし、管理モードをフルと指定するか、管理モードを空白のままにすると、SQL IaaS 拡張機能はフル管理モードでインストールされ、SQL Server サービスが再起動されます。 

**SQL VM リソースプロバイダーに登録するときの軽量モードとエージェントなし管理モードの違いは何ですか?** 

エージェントなし管理モードは、Windows Server 2008 上の SQL Server 2008 と SQL Server 2008 R2 のみに使用できます。 これは、これらのバージョンに唯一使用できる管理モードです。 その他すべてのバージョンの SQL Server に使用可能な 2 つの管理モードとして軽量モードと完全モードがあります。 

エージェントなしモードでは、SQL Server のバージョンとエディションのプロパティをお客様が設定する必要があります。 軽量モードでは、VM が照会され、SQL Server インスタンスのバージョンとエディションが確認されます。

**SQL Server のライセンスの種類を指定せずに SQL VM リソースプロバイダーに登録できますか?**

いいえ。 SQL VM リソースプロバイダーに登録している場合、SQL Server のライセンスの種類は省略可能なプロパティではありません。 すべての管理モード (エージェントなし、軽量、フル) で SQL VM リソース プロバイダーに登録する際、SQL Server のライセンスの種類を従量課金制または Azure ハイブリッド特典として設定する必要があります。

**SQL Server IaaS 拡張機能をエージェントなしモードから完全モードにアップグレードできますか?**

いいえ。 エージェントなしモードでは、管理モードを完全または軽量にアップグレードすることはできません。 これは Windows Server 2008 の技術的な制限です。 最初に OS を Windows Server 2008 R2 以上にアップグレードする必要があります。その後、フル管理モードにアップグレードできます。 

**SQL Server IaaS 拡張機能を軽量モードから完全モードにアップグレードできますか?**

はい。 管理モードを軽量から完全にアップグレードすることは、PowerShell または Azure portal でサポートされています。 これには、SQL Server サービスの再起動が必要です。

**SQL Server IaaS 拡張機能を完全モードからエージェントなしまたは軽量管理モードにダウングレードすることはできますか?**

いいえ。 SQL Server IaaS 拡張機能の管理モードのダウングレードはサポートされていません。 この管理モードは、完全モードから軽量またはエージェントなしモードにダウングレードできません。また、軽量モードからエージェントなしモードにダウングレードすることもできません。 

管理モードを完全管理から変更するには、SQL Server "*リソース*" を削除することで SQL VM リソース プロバイダーから SQL Server VM の[登録を解除](#unregister-from-rp)し、別の管理モードでその SQL Server VM を SQL VM リソース プロバイダーに再登録します。

**Azure portal から SQL VM リソースプロバイダーに登録できますか?**

いいえ。 Azure portal では SQL VM リソースプロバイダーへの登録を利用できません。 SQL VM リソース プロバイダーへの登録は、Azure CLI または PowerShell でのみサポートされます。 

**SQL Server をインストールする前に、VM を SQL VM リソースプロバイダーに登録できますか?**

いいえ。 SQL VM リソース プロバイダーに正常に登録するには、VM に少なくとも 1 つの SQL Server (データベース エンジン) インスタンスが必要です。 VM 上に SQL Server インスタンスが存在しない場合、新しい Microsoft.SqlVirtualMachine リソースはエラー状態になります。

**複数の SQL Server インスタンスがある場合、VM を SQL VM リソースプロバイダーに登録できますか?**

はい。 SQL VM リソース プロバイダーが登録する SQL Server インスタンス (データベース エンジン) は 1 つだけです。 複数のインスタンスがある場合、SQL VM リソースプロバイダーは既定の SQL Server インスタンスを登録します。 既定のインスタンスがない場合、軽量モードでの登録のみがサポートされます。 軽量から完全管理モードにアップグレードするには、既定の SQL Server インスタンスが存在するか、VM に名前付き SQL Server インスタンスが 1 つだけ存在する必要があります。

**SQL Server フェールオーバー クラスター インスタンスを SQL VM リソースプロバイダーに登録できますか?**

はい。 Azure VM 上の SQL Server フェールオーバー クラスター インスタンスは SQL VM リソースプロバイダーに軽量モードで登録できます。 ただし、SQL Server フェールオーバー クラスター インスタンスを完全管理モードにアップグレードすることはできません。

**Always On 可用性グループが構成されている場合、自分の VM を SQL VM リソースプロバイダーに登録できますか?**

はい。 Always On 可用性グループ構成に参加している場合、Azure VM 上の SQL Server インスタンスを SQL VM リソースプロバイダーに登録することに制限はありません。

**SQL VM リソース プロバイダーに登録する場合、または完全管理モードにアップグレードする場合、どのようなコストがかかりますか?**
[なし] : SQL VM リソース プロバイダーへの登録に関連する料金も、3 種類のいずれの管理モードの使用に関連する料金もかかりません。 ご利用の SQL Server VM をリソース プロバイダーを使用して管理する場合は、完全に無料です。 

**さまざまな管理モードを使用すると、パフォーマンスにどのような影響がありますか?**
*NoAgent* および*軽量*管理性モードを使用する場合、影響はありません。 OS にインストールされている 2 つのサービスから、*完全*管理モードを使用する場合の影響は最小限に抑えられます。 これらは、タスク マネージャーを使用して監視し、組み込みの Windows サービス コンソールに表示することができます。 

これらの 2 つのサービス名は次のとおりです。
- `SqlIaaSExtensionQuery` (表示名 - `Microsoft SQL Server IaaS Query Service`)
- `SQLIaaSExtension` (表示名 - `Microsoft SQL Server IaaS Agent`)


## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上の SQL Server に関する FAQ](frequently-asked-questions-faq.md)
* [Windows VM 上の SQL Server の価格ガイダンス](pricing-guidance.md)
* [Windows VM 上の SQL Server のリリース ノート](../../database/doc-changes-updates-release-notes.md)
