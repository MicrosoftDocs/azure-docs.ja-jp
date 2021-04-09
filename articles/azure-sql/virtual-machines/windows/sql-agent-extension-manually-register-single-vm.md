---
title: SQL IaaS Agent 拡張機能に登録する
description: Azure SQL Server 仮想マシンを SQL IaaS Agent 拡張機能に登録して、Azure Marketplace の外部にデプロイされた SQL Server 仮想マシンの機能を有効にし、コンプライアンスと管理容易性を向上させます。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell, contperf-fy21q2
ms.openlocfilehash: 983cdab0c5f5b856537c661c7427a83099f30ed4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181433"
---
# <a name="register-sql-server-vm-with-sql-iaas-agent-extension"></a>SQL Server VM を SQL IaaS Agent 拡張機能に登録する
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

ご使用の SQL Server VM を [SQL IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に登録すると、Azure VM 上の SQL Server に対する数多くの機能面の利点を活用できるようになります。 

この記事では、SQL IaaS Agent 拡張機能に 1 つの SQL Server VM を登録する方法について説明します。 また、すべての SQL Server VM を[自動的に](sql-agent-extension-automatic-registration-all-vms.md)登録することや、[複数の VM を一括でスクリプト化](sql-agent-extension-manually-register-vms-bulk.md)して登録することもできます。


## <a name="overview"></a>概要

[SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に登録すると、仮想マシン リソースとは "_別の_" リソースである、**SQL 仮想マシン** "_リソース_" がサブスクリプション内に作成されます。 拡張機能から SQL Server VM を登録解除すると、**SQL 仮想マシン** "_リソース_" は削除されますが、実際の仮想マシンは削除されません。

Azure portal を介して SQL Server VM の Azure Marketplace イメージをデプロイすると、その SQL Server VM が自動的に拡張機能に登録されます。 ただし、Azure 仮想マシンに SQL Server を自分でインストールすること、またはカスタム VHD から Azure 仮想マシンをプロビジョニングすることを選択する場合は、機能面のすべての利点と管理の容易性を活用できるようにするために、SQL Server VM を SQL IaaS Agent 拡張機能に登録する必要があります。 

SQL IaaS Agent 拡張機能を利用するには、最初に [サブスクリプションを **Microsoft.SqlVirtualMachine** プロバイダーに登録する](#register-subscription-with-rp)必要があります。これにより、SQL IaaS 拡張機能は、その特定のサブスクリプション内にリソースを作成できます。

> [!IMPORTANT]
> SQL IaaS Agent 拡張機能を使用すると、Azure 仮想マシン内で SQL Server を使用する際に、お客様に追加のメリットを提供するという明確な目的のためにデータが収集されます。 Microsoft は、お客様の事前の同意なく、ライセンスの監査にこのデータを使用することはありません。 詳細については、「[SQL Server のプライバシーの補足情報](/sql/sql-server/sql-server-privacy#non-personal-data)」を参照してください。

## <a name="prerequisites"></a>前提条件

SQL Server VM を拡張機能に登録するには、以下のものが必要になります。 

- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- パブリック クラウドまたは Azure Government クラウドにデプロイされた Azure Resource Model の [Windows Server 2008 (またはそれ以降) の仮想マシン](../../../virtual-machines/windows/quick-create-portal.md)と [SQL Server 2008 (またはそれ以降)](https://www.microsoft.com/sql-server/sql-server-downloads)。 
- [Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell (最低 5.0)](/powershell/azure/install-az-ps) の最新バージョン。 


## <a name="register-subscription-with-rp"></a>リソースプロバイダーを使用してサブスクリプションを登録する

SQL Server VM を SQL IaaS Agent 拡張機能に登録するには、最初にサブスクリプションを **Microsoft.SqlVirtualMachine** プロバイダーに登録する必要があります。 これにより、SQL IaaS Agent 拡張機能は、サブスクリプション内にリソースを作成できます。  これは Azure portal、Azure CLI、または Azure PowerShell を使用して実行できます。

### <a name="azure-portal"></a>Azure portal

1. Azure portal を開き、 **[すべてのサービス]** に移動します。 
1. **[サブスクリプション]** に移動し、目的のサブスクリプションを選択します。  
1. **[サブスクリプション]** ページで、 **[拡張機能]** に移動します。 
1. フィルターに「**sql**」と入力し、SQL 関連の拡張機能を表示します。 
1. 必要なアクションに応じて、**Microsoft.SqlVirtualMachine** プロバイダーで **[登録]** 、 **[再登録]** 、または **[登録解除]** を選択します。 

   ![プロバイダーの変更](./media/sql-agent-extension-manually-register-single-vm/select-resource-provider-sql.png)


### <a name="command-line"></a>コマンド ライン

Azure CLI または Azure PowerShell を使用して、Azure サブスクリプションを **Microsoft.SqlVirtualMachine** プロバイダーに登録します。 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

```azurecli-interactive
# Register the SQL IaaS Agent extension to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL IaaS Agent extension to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-extension"></a>拡張機能に登録する

[SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md#management-modes)には 3 つの管理モードがあります。 

フル管理モードで拡張機能を登録すると、SQL Server サービスが再起動されるため、最初に軽量モードで拡張機能を登録してから、メンテナンス期間中に[フルにアップグレード](#upgrade-to-full)することをお勧めします。 

### <a name="lightweight-management-mode"></a>軽量管理モード

Azure CLI または Azure PowerShell を使用して、軽量モードで SQL Server VM を拡張機能に登録します。 これにより、SQL Server サービスは再起動されません。 その後、いつでもフル モードにアップグレードできますが、これを行うと SQL Server サービスが再起動されるので、予定メンテナンス期間まで待つことをお勧めします。 

SQL Server のライセンスの種類として、使用した分を支払う従量課金制 (`PAYG`)、独自のライセンスを使用する Azure ハイブリッド特典 (`AHUB`)、または [無料の DR レプリカ ライセンス](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)をアクティブ化するディザスター リカバリー (`DR`) のいずれかを指定します。

フェールオーバー クラスター インスタンスとマルチインスタンス デプロイは、軽量モードでのみ SQL IaaS Agent 拡張機能に登録できます。 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLI を使用して軽量モードで SQL Server VM を登録します。 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type <license_type> 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell を使用して軽量モードで SQL Server VM を登録します。  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license_type>  -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>フル管理モード

SQL Server VM をフル モードで登録すると、SQL Server サービスが再起動されます。 注意して進めてください。 

SQL Server VM をフル モードで直接登録するには (さらに、場合によっては SQL Server サービスを再起動するには)、次の Azure PowerShell コマンドを使用します。 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>NoAgent 管理モード 

Windows Server 2008 (_R2 ではない_) にインストールされている SQL Server 2008 および 2008 R2 は、[NoAgent モード](sql-server-iaas-agent-extension-automate-management.md#management-modes)で SQL IaaS Agent 拡張機能に登録できます。 このオプションにより、コンプライアンスが確保され、SQL Server VM を Azure portal の限られた機能で監視できるようになります。


**[ライセンスの種類]** には、`AHUB`、`PAYG`、または `DR` を指定します。 **[image offer]\(イメージ オファー\)** には、`SQL2008-WS2008` または `SQL2008R2-WS2008` を指定します。

Windows Server 2008 インスタンス上の SQL Server 2008 (`SQL2008-WS2008`) または 2008 R2 (`SQL2008R2-WS2008`) を登録するには、次の Azure CLI または Azure PowerShell のコード スニペットを使用します。 


# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLI を使用して NoAgent モードで SQL Server 仮想マシンを登録します。 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type <license type>  --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer <image offer> 
 ```
 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)


Azure PowerShell を使用して、NoAgent モードで SQL Server 仮想マシンを登録します。 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType <license type> -SqlManagementType NoAgent -Sku Standard -Offer <image offer>
  ```

---

## <a name="verify-mode"></a>検証モード

Azure PowerShell を次のように使用して、SQL Server IaaS Agent の現在のモードを確認できます。 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>フルへのアップグレード  

"*軽量*" モードで拡張機能に登録された SQL Server VM は、Azure portal、Azure CLI、または Azure PowerShell を使用して "_フル_" にアップグレードできます。 "_No-Agent_" モードの SQL Server VM は、OS が Windows 2008 R2 以上にアップグレードされた後、"_フル_" にアップグレードできます。 ダウングレードすることはできません。それを行うには、SQL Server VM を SQL IaaS Agent 拡張機能から[登録解除](#unregister-from-extension)する必要があります。 それにより、**SQL 仮想マシン** の "_リソース_" が削除されますが、実際の仮想マシンは削除されません。 

> [!NOTE]
> SQL IaaS 拡張機能の管理モードをフルにアップグレードすると、SQL Server サービスが再起動されます。 場合によっては、再起動によって、SQL Server サービスに関連付けられたサービス プリンシパル名 (SPN) が間違ったユーザー アカウントに変更されることがあります。 管理モードをフルにアップグレードした後に接続の問題が発生した場合は、[SPN を登録解除してから再登録してください](/sql/database-engine/configure-windows/register-a-service-principal-name-for-kerberos-connections)。


### <a name="azure-portal"></a>Azure portal

Azure portal を使用して拡張機能をフル モードにアップグレードするには、これらの手順に従います。 

1. [Azure portal](https://portal.azure.com) にサインインします。
1. [SQL 仮想マシン](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) リソースに移動します。 
1. ご利用の SQL Server VM を選択し、 **[概要]** を選択します。 
1. IaaS モードが NoAgent または軽量である SQL Server VM では、**SQL IaaS 拡張機能で利用できるのはライセンスの種類とエディションの更新のみ** という内容のメッセージを選択します。

   ![ポータルでモードを変更するための選択](./media/sql-agent-extension-manually-register-single-vm/change-sql-iaas-mode-portal.png)

1. **[仮想マシンで SQL Server サービスを再起動することに同意します]** チェック ボックスをオンにし、 **[確認]** を選択して IaaS モードを "フル" にアップグレードします。 

    ![仮想マシンで SQL Server サービスを再起動することに同意するためのチェック ボックス](./media/sql-agent-extension-manually-register-single-vm/enable-full-mode-iaas.png)

### <a name="command-line"></a>コマンド ライン

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

拡張機能をフル モードにアップグレードするには、次の Azure CLI コード スニペットを実行します。

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

拡張機能をフル モードにアップグレードするには、次の Azure PowerShell コード スニペットを実行します。

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL IaaS Agent extension in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>登録状態を確認する
お使いの SQL Server VM が既に SQL IaaS Agent 拡張機能に登録されているかどうかは、Azure portal、Azure CLI、または Azure PowerShell を使用して確認できます。 

### <a name="azure-portal"></a>Azure portal 

Azure portal を使用して登録状態を確認するには、これらの手順に従います。 

1. [Azure portal](https://portal.azure.com) にサインインします。 
1. ご利用の [SQL Server VM](manage-sql-vm-portal.md) にアクセスします。
1. 一覧から SQL Server VM を選択します。 お使いの SQL Server VM がここに表示されていない場合は、SQL IaaS Agent 拡張機能に登録されていない可能性があります。 
1. **[状態]** の値を確認します。 **[状態]** が **[成功]** の場合は、その SQL Server VM が正常に SQL IaaS Agent 拡張機能に登録されています。 

   ![SQL RP 登録による状態を確認する](./media/sql-agent-extension-manually-register-single-vm/verify-registration-status.png)

### <a name="command-line"></a>コマンド ライン

Azure CLI または Azure PowerShell を使用して現在の SQL Server VM の登録状態を確認します。 登録が成功していれば、`ProvisioningState` に `Succeeded` と表示されます。 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLI を使用して登録状態を確認するには、次のコード スニペットを実行します。  

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell を使用して登録状態を確認するには、次のコード スニペットを実行します。

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

エラーは、SQL Server VM が拡張機能に登録されていないことを示します。 


## <a name="unregister-from-extension"></a>拡張機能から登録を解除する

SQL IaaS Agent 拡張機能との SQL Server VM の登録を解除するには、Azure portal または Azure CLI を使用して、SQL 仮想マシン "*リソース*" を削除します。 SQL 仮想マシン "*リソース*" を削除しても、SQL Server VM は削除されません。 ただし、"*リソース*" を削除しようとしたときに仮想マシンが誤って削除される可能性があるため、注意して慎重に手順に従ってください。 

SQL IaaS Agent 拡張機能との SQL 仮想マシンの登録を解除するには、管理モードをフルからダウングレードする必要があります。 

### <a name="azure-portal"></a>Azure portal

Azure portal を使用して拡張機能から SQL Server VM の登録を解除するには、これらの手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. SQL VM リソースに移動します。 
  
   ![SQL 仮想マシン リソース](./media/sql-agent-extension-manually-register-single-vm/sql-vm-manage.png)

1. **[削除]** を選択します。 

   ![上部のナビゲーションの [削除] を選択します](./media/sql-agent-extension-manually-register-single-vm/delete-sql-vm-resource.png)

1. SQL 仮想マシンの名前を入力し、**仮想マシンの横にあるチェック ボックスをオフにします**。

   ![実際の仮想マシンが削除されないように仮想マシンのボックスをオフにし、[削除] を選択して SQL VM リソースの削除を続行します](./media/sql-agent-extension-manually-register-single-vm/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > 仮想マシン名の横にあるチェックボックスをオフにしないと、仮想マシンが完全に "*削除*" されます。 拡張機能から SQL Server VM の登録を解除する一方で、"*実際の仮想マシンを削除しない*" 場合は、このチェックボックスをオフにしてください。 

1. **[削除]** を選択して、SQL Server VM ではなく、SQL 仮想マシン "*リソース*" の削除を確認します。 

### <a name="command-line"></a>コマンド ライン

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して拡張機能から SQL Server VM の登録を解除するには、[az sql vm delete](/cli/azure/sql/vm#az-sql-vm-delete) コマンドを使用します。 これにより、SQL Server VM "*リソース*" が削除されますが、仮想マシンは削除されません。 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell を使用して拡張機能から SQL Server VM の登録を解除するには、[Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm) コマンドを使用します。 これにより、SQL Server VM "*リソース*" が削除されますが、仮想マシンは削除されません。 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---


## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上の SQL Server に関する FAQ](frequently-asked-questions-faq.md)  
* [Windows VM 上の SQL Server の価格ガイダンス](pricing-guidance.md)
* [Windows VM 上の SQL Server のリリース ノート](../../database/doc-changes-updates-release-notes.md)
