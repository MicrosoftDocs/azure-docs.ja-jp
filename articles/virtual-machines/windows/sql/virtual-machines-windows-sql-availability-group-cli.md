---
title: Azure CLI を使用して Azure VM で SQL Server の Always On 可用性グループを構成する
description: Azure CLI を使用して、Azure の SQL Server VM で Windows フェールオーバー クラスター、可用性グループ リスナー、および内部ロード バランサーを作成します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 58174704051709a720950ac51591a1d53b9d01bb
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100566"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Azure CLI を使用して Azure VM で SQL Server の Always On 可用性グループを構成する
この記事では、[Azure CLI](/cli/azure/sql/vm?view=azure-cli-latest/) を使用して、Windows フェールオーバー クラスターをデプロイしたり、クラスターに SQL Server VM を追加したり、Always On 可用性グループの内部ロード バランサーおよびリスナーを作成したりする方法について説明します。 Always On 可用性グループのデプロイは、引き続き SQL Server Management Studio (SSMS) を使用して手動で実行されます。 

## <a name="prerequisites"></a>前提条件
Azure CLI を使用して Always On 可用性グループの設定を自動化するには、次の前提条件が満たされている必要があります。 
- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- ドメイン コントローラーを含むリソース グループ。 
- [SQL VM リソースプロバイダーに登録されている](virtual-machines-windows-sql-register-with-resource-provider.md) "*同じ可用性セットまたは別の可用性ゾーン*" にある、1 つ以上のドメイン参加済みの、[SQL Server 2016 (以降の) Enterprise Edition を実行している Azure の VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。  
- [Azure CLI](/cli/azure/install-azure-cli)。 
- 2 つの使用可能な (どのエンティティでも使用されていない) IP アドレス。 1 つは内部ロード バランサー用です。 もう 1 つは、可用性グループと同じサブネット内の可用性グループ リスナー用です。 既存のロード バランサーを使用している場合は、使用可能な IP アドレスが可用性グループ リスナー用に 1 つだけ必要です。 

## <a name="permissions"></a>アクセス許可
Azure CLI を使用して Always On 可用性グループを構成するには、次のアカウントのアクセス許可が必要です。 

- ドメインで**コンピューター オブジェクトを作成する**ためのアクセス許可を持っている既存のドメイン ユーザー アカウント。 たとえばドメイン管理者アカウントは、一般に十分なアクセス許可を持っています (例: account@domain.com)。 _クラスターを作成するには、このアカウントが、各 VM でローカル管理者グループに含まれている必要もあります。_
- SQL Server サービスを制御するドメイン ユーザー アカウント。 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>手順 1:クラウド監視としてのストレージ アカウントを作成する
クラスターには、クラウド監視として機能するストレージ アカウントが必要です。 いずれかの既存のストレージ アカウントを使用するか、または新しいストレージ アカウントを作成できます。 既存のストレージ アカウントを使用する場合は、次のセクションに進んでください。 

次のコード スニペットは、ストレージ アカウントを作成します。 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> 古いバージョンの Azure CLI を使用している場合は、エラー `az sql: 'vm' is not in the 'az sql' command group` が表示されることがあります。 このエラーを回避するには、[最新バージョンの Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) をダウンロードしてください。

## <a name="step-2-define-windows-failover-cluster-metadata"></a>手順 2:Windows フェールオーバー クラスターのメタデータを定義する
Azure CLI の [az sql vm group](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) コマンド グループは、可用性グループをホストする Windows Server フェールオーバー クラスター (WSFC) サービスのメタデータを管理するものです。 クラスター メタデータには、Active Directory ドメイン、クラスター アカウント、クラウド監視として使用されるストレージ アカウント、および SQL Server バージョンが含まれています。 [az sql vm group create](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) を使用して WSFC のメタデータを定義し、最初の SQL Server VM が追加されたら、定義のとおりにクラスターが作成されるようにします。 

次のコード スニペットは、クラスターのメタデータを定義します。
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

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>手順 3:クラスターに SQL Server VM を追加する
クラスターに最初の SQL Server VM を追加すると、クラスターが作成されます。 [az sql vm add-to-group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) コマンドは、前に指定された名前でクラスターを作成し、SQL Server VM にクラスター ロールをインストールした後、それらの VM をクラスターに追加します。 その後に `az sql vm add-to-group` コマンドを使用すると、新しく作成されたクラスターに SQL Server VM が追加されます。 

次のコード スニペットはクラスターを作成し、そこに最初の SQL Server VM を追加します。 

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

## <a name="step-4-create-the-availability-group"></a>手順 4:可用性グループの作成
[SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio)、[PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)、[Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql) のいずれかを使用して、通常どおりに可用性グループを手動で作成します。 

>[!IMPORTANT]
> この時点では、リスナーを作成 "*しないでください*"。これは、以降のセクションで Azure CLI を使用して行います。  

## <a name="step-5-create-the-internal-load-balancer"></a>手順 5:内部ロード バランサーを作成する

Always On 可用性グループ リスナーには、Azure Load Balancer の内部インスタンスが必要です。 内部ロード バランサーでは、より高速なフェールオーバーと再接続を可能にする可用性グループ リスナー用の "フローティング" IP アドレスが提供されます。 可用性グループ内の SQL Server VM が同じ可用性セットの一部である場合は、Basic ロード バランサーを使用できます。 それ以外の場合は、Standard ロード バランサーを使用する必要があります。  

> [!NOTE]
> 内部ロード バランサーは、SQL Server VM インスタンスと同じ仮想ネットワークに存在する必要があります。 

次のコード スニペットは、内部ロード バランサーを作成するものです。

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> 各 SQL Server VM 用のパブリック IP リソースには、Standard Load Balancer と互換性のある Standard SKU が必要です。 VM のパブリック IP リソースの SKU を決定するには、 **[リソース グループ]** に移動し、目的の SQL Server VM 用の **[パブリック IP アドレス]** リソースを選択して、 **[概要]** ウィンドウの **[SKU]** で値を見つけます。  

## <a name="step-6-create-the-availability-group-listener"></a>手順 6:可用性グループ リスナーを作成する
可用性グループを手動で作成したら、[az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create) を使用してリスナーを作成できます。 

"*サブネット リソース ID*" は、仮想ネットワーク リソースのリソース ID に追加された `/subnets/<subnetname>` の値です。 サブネット リソース ID を識別するには、次の操作を行います。
   1. [Azure portal](https://portal.azure.com) で、ご利用のリソース グループに移動します。 
   1. 仮想ネットワーク リソースを選択します。 
   1. **[設定]** ウィンドウで **[プロパティ]** を選択します。 
   1. 仮想ネットワークのリソース ID を特定し、その最後に `/subnets/<subnetname>` を追加してサブネット リソース ID を作成します。 例:
      - 仮想ネットワークのリソース ID は `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet` です。
      - サブネット名は `default` です。
      - そのため、サブネット リソース ID は `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default` です。


次のコード スニペットは、可用性グループ リスナーを作成するものです。

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

## <a name="modify-the-number-of-replicas-in-an-availability-group"></a>可用性グループ内のレプリカの数を変更する
Azure でホストされている SQL Server VM に可用性グループをデプロイすると複雑さが増します。 リソースは、リソースプロバイダーおよび仮想マシン グループによって管理されるようになります。 このため、可用性グループにレプリカを追加したり、可用性グループからレプリカを削除したりする場合は、SQL Server VM に関する情報でリスナー メタデータを更新する手順が別途発生します。 可用性グループ内のレプリカの数を変更する場合は、[az sql vm group ag-listener update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) コマンドを使用して、SQL Server VM のメタデータでリスナーを更新する必要もあります。 


### <a name="add-a-replica"></a>レプリカを追加する

可用性グループに新しいレプリカを追加するには、次の操作を行います。

1. クラスターに SQL Server VM を追加します。
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
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

### <a name="remove-a-replica"></a>レプリカの削除

可用性グループからレプリカを削除するには、次の操作を行います。

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

## <a name="remove-the-availability-group-listener"></a>可用性グループ リスナーを削除する
Azure CLI で構成された可用性グループ リスナーを後で削除する必要が生じた場合は、SQL VM リソースプロバイダーを使用する必要があります。 リスナーは SQL VM リソースプロバイダーを介して登録されるため、SQL Server Management Studio を使用して削除するだけでは十分ではありません。 

最適な方法は、Azure CLI で次のコード スニペットを使用して、SQL VM リソースプロバイダーを通じて削除することです。 そうすることで、SQL VM リソースプロバイダーから可用性グループ リスナー メタデータが削除されます。 また、可用性グループから物理的にリスナーが削除されます。 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。 

* [SQL Server VM の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server VM の FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server VM のリリース ノート](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [SQL Server VM のライセンス モデルの切り替え](virtual-machines-windows-sql-ahb.md)
* [Always On 可用性グループの概要 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Always On 可用性グループ用のサーバー インスタンスの構成 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [可用性グループの管理 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [可用性グループの監視 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Always On 可用性グループ用の Transact-SQL ステートメントの概要 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Always On 可用性グループ用の PowerShell コマンドレットの概要 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
