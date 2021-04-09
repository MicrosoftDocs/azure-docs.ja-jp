---
title: サービス エンドポイント ポリシーの構成 - Azure HDInsight
description: Azure HDInsight を使用して仮想ネットワークのサービス エンドポイント ポリシーを構成する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/15/2020
ms.openlocfilehash: 992cd994f96b5637d5afd91bccfecde8704d2886
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98940624"
---
# <a name="configure-virtual-network-service-endpoint-policies-for-azure-hdinsight"></a>Azure HDInsight の仮想ネットワーク サービス エンドポイント ポリシーを構成する

この記事では、Azure HDInsight を使用して仮想ネットワークにサービス エンドポイント ポリシーを実装する方法について説明します。

## <a name="background"></a>バックグラウンド

Azure HDInsight では、独自の仮想ネットワークでクラスターを作成できます。 仮想ネットワークからその他の Azure サービス (ストレージ アカウントなど) への送信トラフィックを許可する必要がある場合は、[サービス エンドポイント ポリシー](../virtual-network/virtual-network-service-endpoint-policies-overview.md)を作成できます。 ただし、Azure portal を使用して作成されるサービス エンドポイント ポリシーでは、1 つのアカウント、サブスクリプション内のすべてのアカウント、またはリソース グループ内のすべてのアカウントのポリシーしか作成できません。

ただし、Azure HDInsight では管理サービスとして、各リージョンの特定のストレージ アカウント内の各クラスターからデータとログ ファイルが収集されます。 このデータを仮想ネットワークから HDInsight に接続するには、Azure HDInsight で管理される特定のデータ収集ポイントへの送信トラフィックが許可されるサービス エンドポイント ポリシーを作成する必要があります。

## <a name="service-endpoint-policies-for-hdinsight"></a>HDInsight のサービス エンドポイント ポリシー

これらのサービス エンドポイント ポリシーでは、次の機能がサポートされています。

- クラスターの作成、ジョブの実行、およびプラットフォームの操作 (拡大縮小など) に関するログとテレメトリを収集する。
- クラスターにソフトウェアとライブラリをプロビジョニングするために、新しく作成されたクラスター ノードに仮想ハードディスク (VHD) を接続する。

このデータ フローを有効にするためにサービス エンドポイント ポリシーが作成されていない場合は、クラスターの作成に失敗し、Azure HDInsight でクラスターをサポートできなくなります。

## <a name="create-service-endpoint-policies-for-hdinsight"></a>HDInsight のサービス エンドポイント ポリシーを作成する

新しいクラスターを作成する前に、仮想ネットワークに適切なサービス エンドポイント ポリシーが接続されていることを確認します。 そうでない場合は、クラスターの作成に失敗したり、エラーが発生したりする可能性があります。

必要なサービス エンドポイント ポリシーを作成するには、次のプロセスを使用します。

1. HDInsight クラスターを作成するリージョンを決定します。
1. そのリージョンを[サービス エンドポイント ポリシー リソースのリスト](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json)で検索すると、HDInsight 管理ストレージ アカウントのリソース グループがすべて表示されます。
1. リージョンのリソース グループ リストを選択します。 `Canada Central` のリソースの例は次のとおりです。

    ```json
    "Canada Central":[
        "/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
        "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG"
    ],
    ```

1. Azure CLI または Azure PowerShell で作成されたセットアップ スクリプトに、そのリソース グループ リストを挿入します。

    ```azurecli
    $subscriptionId = "<subscription id>"
    $rgName="<resource group name> "
    $location="<location name>"
    $vnetName="<vnet name>"
    $subnetName="<subnet name>"
    $sepName="<service endpoint policy name>"
    $sepDefName="<service endpoint policy definition name>"
    
    # Set to the right subscription ID
    az account set --subscription $subscriptionId
    
    # setup service endpoint on the virtual network subnet
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoints Microsoft.Storage
    
    # Create Service Endpoint Policy
    az network service-endpoint policy create -g $rgName  -n $sepName -l $location
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",`
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Assign service resources to the SEP policy.
    az network service-endpoint policy-definition create -g $rgName --policy-name $sepName -n $sepDefName --service "Microsoft.Storage" --service-resources $resources
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoint-policy $sepName
    ```

    PowerShell を使用してサービス エンドポイント ポリシーを設定する場合は、次のコード スニペットを使用します。
    
    ```powershell
    #Script to assign SEP 
    $subscriptionId = "<subscription id>"
    $rgName = "<resource group name>"
    $vnetName = "<vnet name>"
    $subnetName = "<subnet Name"
    $location = "Canada Central"
    
    # Connect to your Azure Account
    Connect-AzAccount
    
    # Select the Subscription that you want to use
    Select-AzSubscription -SubscriptionId $subscriptionId
    
    # Retrieve VNet Config
    $vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
    
    # Retrieve Subnet Config
    $subnet = Get-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Declare service endpoint policy definition
    $sepDef = New-AzServiceEndpointPolicyDefinition -Name "SEPHDICanadaCentral" -Description "Service Endpoint Policy Definition" -Service "Microsoft.Storage" -ServiceResource $resources
    
    # Service Endpoint Policy
    $sep= New-AzServiceEndpointPolicy -ResourceGroupName $rgName -Name "SEPHDICanadaCentral" -Location $location -ServiceEndpointPolicyDefinition $sepDef
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    Set-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet -AddressPrefix $subnet.AddressPrefix -ServiceEndpointPolicy $sep
    ```

## <a name="next-steps"></a>次のステップ

* [Azure HDInsight 仮想ネットワーク アーキテクチャ](hdinsight-virtual-network-architecture.md)
* [ネットワーク仮想アプライアンスを構成する](./network-virtual-appliance.md)
