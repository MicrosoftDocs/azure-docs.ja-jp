---
title: 可用性グループの構成 (PowerShell & Az CLI)
description: PowerShell または Azure CLI を使用して、Azure の SQL Server VM で Windows フェールオーバー クラスター、可用性グループ リスナー、および内部ロード バランサーを作成します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 865ee3a5aeb8a2dd06d8759ba04d02259d2b4bee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359967"
---
# <a name="use-powershell-or-az-cli-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>PowerShell または Az CLI を使用して Azure VM で SQL Server の可用性グループを構成する 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、[PowerShell](/powershell/scripting/install/installing-powershell) または [Azure CLI](/cli/azure/sql/vm) を使用して、Windows フェールオーバー クラスターのデプロイ、クラスターへの SQL Server VM の追加、Always On 可用性グループの内部ロード バランサーおよびリスナーの作成を行う方法について説明します。 

可用性グループのデプロイは、引き続き SQL Server Management Studio (SSMS) または Transact-SQL (T-SQL) を使用して手動で実行されます。 

この記事では PowerShell と Az CLI を使用して可用性グループ環境を構成しますが、 [Azure portal](availability-group-azure-portal-configure.md)から [Azure クイックスタート テンプレート](availability-group-quickstart-template-configure.md)を使用して構成するか、[手動](availability-group-manually-configure-tutorial.md)で構成することもできます。 

## <a name="prerequisites"></a>前提条件

Always On 可用性グループを構成するには、次の前提条件を満たしている必要があります。 

- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- ドメイン コントローラーを含むリソース グループ。 
- [SQL IaaS Agent 拡張機能に登録](sql-agent-extension-manually-register-single-vm.md)されているのと "*同じ*" 可用性セットまたは "*異なる*" 可用性ゾーンにある、1 つ以上のドメイン参加済みの、[SQL Server 2016 (またはそれ以降の) Enterprise エディションを実行している Azure の VM](./create-sql-vm-portal.md)。  
- 最新バージョンの [PowerShell](/powershell/scripting/install/installing-powershell) または [Azure CLI](/cli/azure/install-azure-cli)。 
- 2 つの使用可能な (どのエンティティでも使用されていない) IP アドレス。 1 つは内部ロード バランサー用です。 もう 1 つは、可用性グループと同じサブネット内の可用性グループ リスナー用です。 既存のロード バランサーを使用している場合は、使用可能な IP アドレスが可用性グループ リスナー用に 1 つだけ必要です。 

## <a name="permissions"></a>アクセス許可

Azure CLI を使用して Always On 可用性グループを構成するには、次のアカウントのアクセス許可が必要です。 

- ドメインで **コンピューター オブジェクトを作成する** ためのアクセス許可を持っている既存のドメイン ユーザー アカウント。 たとえばドメイン管理者アカウントは、一般に十分なアクセス許可を持っています (例: account@domain.com)。 _クラスターを作成するには、このアカウントが、各 VM でローカル管理者グループに含まれている必要もあります。_
- SQL Server を制御するドメイン ユーザー アカウント。 
 
## <a name="create-a-storage-account"></a>ストレージ アカウントの作成 

クラスターには、クラウド監視として機能するストレージ アカウントが必要です。 いずれかの既存のストレージ アカウントを使用するか、または新しいストレージ アカウントを作成できます。 既存のストレージ アカウントを使用する場合は、次のセクションに進んでください。 

次のコード スニペットは、ストレージ アカウントを作成します。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> 古いバージョンの Azure CLI を使用している場合は、エラー `az sql: 'vm' is not in the 'az sql' command group` が表示されることがあります。 このエラーを回避するには、[最新バージョンの Azure CLI](/cli/azure/install-azure-cli-windows) をダウンロードしてください。


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the storage account
# example: New-AzStorageAccount -ResourceGroupName SQLVM-RG -Name cloudwitness `
#    -SkuName Standard_LRS -Location West US -Kind StorageV2 `
#    -AccessTier Hot -EnableHttpsTrafficOnly

New-AzStorageAccount -ResourceGroupName <resource group name> -Name <name> `
    -SkuName Standard_LRS -Location <region> -Kind StorageV2 `
    -AccessTier Hot -EnableHttpsTrafficOnly
```

---

## <a name="define-cluster-metadata"></a>クラスター メタデータを定義する

Azure CLI の [az sql vm group](/cli/azure/sql/vm/group) コマンド グループは、可用性グループをホストする Windows Server フェールオーバー クラスター (WSFC) サービスのメタデータを管理するものです。 クラスター メタデータには、Active Directory ドメイン、クラスター アカウント、クラウド監視として使用されるストレージ アカウント、および SQL Server バージョンが含まれています。 [az sql vm group create](/cli/azure/sql/vm/group#az-sql-vm-group-create) を使用して WSFC のメタデータを定義し、最初の SQL Server VM が追加されたら、定義のとおりにクラスターが作成されるようにします。 

次のコード スニペットは、クラスターのメタデータを定義します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Define the cluster metadata
# example: $group = New-AzSqlVMGroup -Name Cluster -Location West US ' 
#  -ResourceGroupName SQLVM-RG -Offer SQL2017-WS2016
#  -Sku Enterprise -DomainFqdn domain.com -ClusterOperatorAccount vmadmin@domain.com
#  -ClusterBootstrapAccount vmadmin@domain.com  -SqlServiceAccount sqlservice@domain.com 
#  -StorageAccountUrl '<ex:https://cloudwitness.blob.core.windows.net/>' `
#  -StorageAccountPrimaryKey '4Z4/i1Dn8/bpbseyWX'

$group = New-AzSqlVMGroup -Name <name> -Location <regio> 
  -ResourceGroupName <resource group name> -Offer <SQL201?-WS201?> 
  -Sku Enterprise -DomainFqdn <FQDN> -ClusterOperatorAccount <domain account> 
  -ClusterBootstrapAccount <domain account>  -SqlServiceAccount <service account> 
  -StorageAccountUrl '<ex:StorageAccountUrl>' `
  -StorageAccountPrimaryKey '<PublicKey>'
```

---

## <a name="add-vms-to-the-cluster"></a>クラスターに VM を追加する

クラスターに最初の SQL Server VM を追加すると、クラスターが作成されます。 [az sql vm add-to-group](/cli/azure/sql/vm#az-sql-vm-add-to-group) コマンドは、前に指定された名前でクラスターを作成し、SQL Server VM にクラスター ロールをインストールした後、それらの VM をクラスターに追加します。 その後に `az sql vm add-to-group` コマンドを使用すると、新しく作成されたクラスターに SQL Server VM が追加されます。 

次のコード スニペットはクラスターを作成し、そこに最初の SQL Server VM を追加します。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
クラスターに他の SQL Server VM を追加するには、このコマンドを使用します。 SQL Server VM 名の `-n` パラメーターのみを変更してください。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Add SQL Server VMs to cluster
# example: $sqlvm1 = Get-AzSqlVM -Name SQLVM1 -ResourceGroupName SQLVM-RG
# $sqlvm2 = Get-AzSqlVM -Name SQLVM2  -ResourceGroupName SQLVM-RG

# $sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

# $sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  - ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

$sqlvm1 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
$sqlvm2 = Get-AzSqlVM -Name <VM2 Name> -ResourceGroupName <Resource Group Name>

$sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm1.ResourceId -SqlVM $sqlvmconfig1

$sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm2.ResourceId -SqlVM $sqlvmconfig2
```

---


## <a name="validate-cluster"></a>クラスターを検証する 

Microsoft によってフェールオーバー クラスターがサポートされるためには、クラスター検証に合格する必要があります。 任意の方法 (リモート デスクトップ プロトコル (RDP) など) を使用して VM に接続し、先に進む前に、クラスターが検証に合格していることを確認します。 これに失敗すると、クラスターはサポートされていない状態のままになります。 

フェールオーバー クラスター マネージャー (FCM) または次の PowerShell コマンドを使用して、クラスターを検証できます。

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

## <a name="create-availability-group"></a>可用性グループを作成する

[SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio)、[PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)、[Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql) のいずれかを使用して、通常どおりに可用性グループを手動で作成します。 

>[!IMPORTANT]
> この時点では、リスナーを作成 "*しないでください*"。これは、以降のセクションで Azure CLI を使用して行います。  

## <a name="create-internal-load-balancer"></a>内部ロード バランサーを作成する

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Always On 可用性グループ リスナーには、Azure Load Balancer の内部インスタンスが必要です。 内部ロード バランサーでは、より高速なフェールオーバーと再接続を可能にする可用性グループ リスナー用の "フローティング" IP アドレスが提供されます。 可用性グループ内の SQL Server VM が同じ可用性セットの一部である場合は、Basic ロード バランサーを使用できます。 それ以外の場合は、Standard ロード バランサーを使用する必要があります。  

> [!NOTE]
> 内部ロード バランサーは、SQL Server VM インスタンスと同じ仮想ネットワークに存在する必要があります。 

次のコード スニペットは、内部ロード バランサーを作成するものです。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the internal load balancer
# example: New-AzLoadBalancer -name sqlILB -ResourceGroupName SQLVM-RG  `
#  -Sku Standard -Location West US

New-AzLoadBalancer -name sqlILB -ResourceGroupName <resource group name> `
   -Sku Standard -Location <region>
```

---

>[!IMPORTANT]
> 各 SQL Server VM 用のパブリック IP リソースには、Standard Load Balancer と互換性のある Standard SKU が必要です。 VM のパブリック IP リソースの SKU を決定するには、 **[リソース グループ]** に移動し、目的の SQL Server VM 用の **[パブリック IP アドレス]** リソースを選択して、 **[概要]** ウィンドウの **[SKU]** で値を見つけます。  

## <a name="create-listener"></a>リスナーを作成する

可用性グループを手動で作成したら、[az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener#az-sql-vm-group-ag-listener-create) を使用してリスナーを作成できます。 

"*サブネット リソース ID*" は、仮想ネットワーク リソースのリソース ID に追加された `/subnets/<subnetname>` の値です。 サブネット リソース ID を識別するには、次の操作を行います。
   1. [Azure portal](https://portal.azure.com) で、ご利用のリソース グループに移動します。 
   1. 仮想ネットワーク リソースを選択します。 
   1. **[設定]** ウィンドウで **[プロパティ]** を選択します。 
   1. 仮想ネットワークのリソース ID を特定し、その最後に `/subnets/<subnetname>` を追加してサブネット リソース ID を作成します。 次に例を示します。
      - 仮想ネットワークのリソース ID は `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet` です。
      - サブネット名は `default` です。
      - そのため、サブネット リソース ID は `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default` です。


次のコード スニペットは、可用性グループ リスナーを作成するものです。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive

# example: New-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
#    -AvailabilityGroupName SQLAG  -GroupName Cluster `
#    -IpAddress 10.0.0.27 -LoadBalancerResourceId sqlilb `
#    -ProbePort 59999 `
#    -SubnetId /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#    -SqlVirtualMachineId sqlvm1 sqlvm2


New-AzAvailabilityGroupListener -Name <listener name> -ResourceGroupName <resource group name> `
   -AvailabilityGroupName <availability group name> -GroupName <cluster name> `
   -IpAddress <ag listener IP address> -LoadBalancerResourceId <lbid> `
   -ProbePort <Load Balancer probe port, default 59999> `
   -SubnetId <subnet resource id> `
   -SqlVirtualMachineId <names of SQL VM's hosting AG replicas>
```

---

## <a name="modify-number-of-replicas"></a>レプリカの数を変更する 
Azure でホストされている SQL Server VM に可用性グループをデプロイすると複雑さが増します。 リソースは、リソースプロバイダーおよび仮想マシン グループによって管理されるようになります。 このため、可用性グループにレプリカを追加したり、可用性グループからレプリカを削除したりする場合は、SQL Server VM に関する情報でリスナー メタデータを更新する手順が別途発生します。 可用性グループ内のレプリカの数を変更する場合は、[az sql vm group ag-listener update](/cli/azure/sql/vm/group/ag-listener#az-sql-vm-group-ag-listener-update) コマンドを使用して、SQL Server VM のメタデータでリスナーを更新する必要もあります。 


### <a name="add-a-replica"></a>レプリカを追加する

可用性グループに新しいレプリカを追加するには、次の操作を行います。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. クラスター グループに SQL Server VM を追加します。
   ```azurecli-interactive

   # Add the SQL Server VM to the cluster group
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```

1. SQL Server Management Studio を使用して、SQL Server インスタンスを可用性グループ内のレプリカとして追加します。
1. リスナーに SQL Server VM メタデータを追加します。

   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. クラスター グループに SQL Server VM を追加します。

   ```powershell-interactive
   # Add the SQL Server VM to the cluster group
   # example: $sqlvm3 = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG 
   # $group = Get-AzSqlVMGroup -ResourceGroupName SQLVM-RG -Name Cluster
   # $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   #  -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
   #  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
   #  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

   $sqlvm3 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
   $group = Get-AzSqlVMGroup  -ResourceGroupName <resource group name> -Name <name>
   $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

   Update-AzSqlVM -ResourceId $sqlvm3.ResourceId -SqlVM $sqlvmconfig3
   ```

1. SQL Server Management Studio を使用して、SQL Server インスタンスを可用性グループ内のレプリカとして追加します。
1. リスナーに SQL Server VM メタデータを追加します。

   ```powershell-interactive
   # Update the listener metadata with the new VM
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #   -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs, along with new SQL VM> 

   ```

---

### <a name="remove-a-replica"></a>レプリカの削除

可用性グループからレプリカを削除するには、次の操作を行います。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. SQL Server Management Studio を使用して、可用性グループからレプリカを削除します。 
1. リスナーから SQL Server VM メタデータを削除します。
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. クラスターから SQL Server VM を削除します。
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. SQL Server Management Studio を使用して、可用性グループからレプリカを削除します。 
1. リスナーから SQL Server VM メタデータを削除します。

   ```powershell-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #  -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs that remain>

   ```
1. クラスターから SQL Server VM を削除します。

   ```powershell-interactive
   # Remove the SQL VM from the cluster
   # example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
   #  $sqlvm. SqlVirtualMachineGroup = ""
   #  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

   $sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
      $sqlvm. SqlVirtualMachineGroup = ""
    
    Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
   ```

---

## <a name="remove-listener"></a>リスナーを削除する
Azure CLI で構成された可用性グループ リスナーを後で削除する必要が生じた場合は、SQL IaaS Agent 拡張機能を使用する必要があります。 リスナーは SQL IaaS Agent 拡張機能を介して登録されるため、SQL Server Management Studio を使用して削除するだけでは十分ではありません。 

最適な方法は、Azure CLI で次のコード スニペットを使用して、SQL IaaS Agent 拡張機能を通じて削除することです。 このようにすることで、SQL IaaS Agent 拡張機能から可用性グループ リスナー メタデータが削除されます。 また、可用性グループから物理的にリスナーが削除されます。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Remove the availability group listener
# example: Remove-AzAvailabilityGroupListener -Name AGListener `
#   -ResourceGroupName SQLVM-RG -SqlVMGroupName Cluster

Remove-AzAvailabilityGroupListener -Name <Listener> `
   -ResourceGroupName <Resource Group Name> -SqlVMGroupName <cluster name>
```

---

## <a name="remove-cluster"></a>クラスターの削除

クラスターからすべてのノードを削除して破棄し、SQL IaaS Agent 拡張機能からクラスター メタデータを削除します。 これは Azure CLI か PowerShell を使用して実行できます。 


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

まず、クラスターからすべての SQL Server VM を削除します。 

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

これらがクラスター内にある VM のすべてであった場合、クラスターは破棄されます。 削除された SQL Server VM とは別に、クラスター内に他の VM がある場合、他の VM は削除されず、クラスターは破棄されません。 

次に、SQL IaaS Agent 拡張機能からクラスターのメタデータを削除します。 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> Cluster --resource-group <resource group name>
```



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

まず、クラスターからすべての SQL Server VM を削除します。 これにより、クラスターからノードが物理的に削除され、クラスターが破棄されます。 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

これらがクラスター内にある VM のすべてであった場合、クラスターは破棄されます。 削除された SQL Server VM とは別に、クラスター内に他の VM がある場合、他の VM は削除されず、クラスターは破棄されません。 

次に、SQL IaaS Agent 拡張機能からクラスターのメタデータを削除します。 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。 

* [SQL Server VM の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server VM の FAQ](frequently-asked-questions-faq.md)
* [SQL Server VM のリリース ノート](../../database/doc-changes-updates-release-notes.md)
* [SQL Server VM のライセンス モデルの切り替え](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [Always On 可用性グループの概要 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Always On 可用性グループ用のサーバー インスタンスの構成 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [可用性グループの管理 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [可用性グループの監視 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Always On 可用性グループ用の Transact-SQL ステートメントの概要 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Always On 可用性グループ用の PowerShell コマンドレットの概要 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)