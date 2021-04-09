---
title: Azure Event Grid のトピックまたはドメインに対してプライベート エンドポイントを構成する
description: この記事では Azure Event Grid のトピックまたはドメインに対してプライベート エンドポイントを構成する方法について説明します。
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: f8a9ac46596b1c2611ce9df387ac995e8149e7b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102425347"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains"></a>Azure Event Grid のトピックまたはドメインに対してプライベート エンドポイントを構成する
[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用すると、パブリック インターネットを経由せずに[プライベート リンク](../private-link/private-link-overview.md)上で安全に仮想ネットワークからトピックおよびドメインへ直接、イベントのイングレスを行えるようになります。 プライベート エンドポイントは、トピックまたはドメインの VNet アドレス空間からの IP アドレスを使用します。 概念の詳細については、[ネットワーク セキュリティ](network-security.md)に関する記事をご覧ください。

この記事ではトピックまたはドメインに対してプライベート エンドポイントを構成する方法について説明します。

## <a name="use-azure-portal"></a>Azure Portal の使用 
このセクションでは、Azure portal を使用して、トピックまたはドメインに対してプライベート エンドポイントを作成する方法について説明します。

> [!NOTE]
> このセクションで示す手順は、多くがトピックに関するものです。 同様の手順を使用して、**ドメイン** のプライベート エンドポイントを作成できます。 

1. [Azure portal](https://portal.azure.com) にサインインし、ご自身のトピックまたはドメインに移動します。
2. トピックページの **[ネットワーク]** タブに切り替えます。 ツールバーで **[+ プライベート エンドポイント]** を選択します。

    ![プライベート エンドポイントの追加](./media/configure-private-endpoints/add-button.png)
2. **[基本]** ページで、次の手順を行います。 
    1. プライベート エンドポイントを作成する **Azure サブスクリプション** を選択します。 
    2. プライベート エンドポイント用の **Azure リソース グループ** を選択します。 
    3. エンドポイントの **名前** を入力します。 
    4. エンドポイントの **リージョン** を選択します。 プライベート エンドポイントが存在するリージョンは仮想ネットワークと同じでなければなりませんが、プライベート リンク リソース (この例ではイベント グリッド トピック) のリージョンとは異なっていてもかまいません。 
    5. 次に、 **[Next:Resource >]/(次へ: リソース >/)** ボタンがページの下部にあるのでクリックします。 

      ![プライベート エンドポイント - 基本ページ](./media/configure-private-endpoints/basics-page.png)
3. **[リソース]** ページで、次の手順を行います。 
    1. 接続方法について、 **[マイ ディレクトリ内の Azure リソースに接続します]** を選択した場合は、次の手順に従います。 この例では、ディレクトリ内の Azure リソースに接続する方法を示します。 
        1. **トピックまたはドメイン** が存在する **Azure サブスクリプション** を選択します。 
        1. **リソースの種類** については、 **[リソースの種類]** に **Microsoft.EventGrid/topics** または **Microsoft.EventGrid/domains** を選択します。
        2. **[リソース]** については、ドロップダウン リストからトピックまたはドメインを選択します。 
        3. **ターゲット サブリソース** が **[トピック]** または **[ドメイン]** に設定されていることを確認します (選択したリソースの種類に基づく)。    
        4. **Next:次へ: 構成 >/** ボタンがページの下部にあるので選択します。 

            ![[プライベート エンドポイントの作成] ページの [リソース] を示すスクリーンショット。](./media/configure-private-endpoints/resource-page.png)
    2. **[リソース ID またはエイリアスを使ってリソースに接続します]** を選択した場合は、次の手順に従います。
        1. リソースの ID を入力します。 (例: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`)。  
        2. **[リソース]** には、 **[トピック]** または **[ドメイン]** を入力してください。 
        3. (省略可能) 要求メッセージを追加します。 
        4. **Next:次へ: 構成 >/** ボタンがページの下部にあるので選択します。 

            ![[プライベート エンドポイント - リソース] ページ](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. **[構成]** ページで、プライベート エンドポイントのデプロイ先とする仮想ネットワーク内のサブネットを選択します。 
    1. **[仮想ネットワーク]** を選択します。 ドロップダウン リストには、現在選択されているサブスクリプションおよび場所内の仮想ネットワークのみが一覧表示されます。 
    2. 選択した仮想ネットワーク内の **サブネット** を選択します。 
    3. **Next:次へ: タグ >** ボタンがページの下部にあるので選択します。 

    ![[プライベート エンドポイント - 構成] ページ](./media/configure-private-endpoints/configuration-page.png)
5. **[タグ]** ページでは、プライベート エンドポイント リソースに関連付ける任意のタグ (名前と値) を作成します。 次に、ページの下部にある **[確認と作成]** ボタンを選択します。 
6. **[確認と作成]** では、すべての設定を確認し、 **[作成]** を選択してプライベート エンドポイントを作成します。 

    ![[プライベート エンドポイント - 確認と作成] ページ](./media/configure-private-endpoints/review-create-page.png)
    

### <a name="manage-private-link-connection"></a>プライベート リンク接続を管理する

プライベート エンドポイントを作成する際は、接続を承認する必要があります。 プライベート エンドポイントの作成対象のリソースが自分のディレクトリ内にある場合、十分なアクセス許可があれば、接続要求を承認することができます。 別のディレクトリ内の Azure リソースに接続している場合は、そのリソースの所有者が接続要求を承認するまで待機する必要があります。

プロビジョニングの状態には次の 4 つがあります。

| サービス アクション | サービス コンシューマーのプライベート エンドポイントの状態 | 説明 |
|--|--|--|
| なし | 保留中 | 接続が手動で作成されており、プライベート リンク リソースの所有者からの承認を待っています。 |
| 承認 | Approved | 接続が自動または手動で承認され、使用する準備が整っています。 |
| Reject | 拒否 | プライベート リンク リソースの所有者によって接続が拒否されました。 |
| [削除] | [Disconnected]\(切断済み\) | プライベート リンク リソースの所有者によって接続が削除されました。プライベート エンドポイントは情報が多くなり、クリーンアップのために削除する必要があります。 |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>プライベート エンドポイント接続を管理する方法
次のセクションでは、プライベート エンドポイント接続を承認または拒否する方法について説明します。 

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 検索バーで、 **[Event Grid トピック]** または **[Event Grid ドメイン]** に入力します。
1. 管理する **トピック** または **ドメイン** を選択します。
1. **[ネットワーク]** タブを選択します。
1. 保留中の接続がある場合は、プロビジョニング状態に **[保留]** と表示されている接続が一覧表示されます。 

### <a name="to-approve-a-private-endpoint"></a>プライベート エンドポイント接続を承認するには
保留状態のプライベート エンドポイントを承認できます。 承認するには、次の手順に従います。 

> [!NOTE]
> このセクションで示す手順は、多くがトピックに関するものです。 同様の手順を使用して、**ドメイン** のプライベート エンドポイントを承認できます。 

1. 承認する **プライベート エンドポイント** を選択し、ツールバーの **[承認]** を選択します。

    ![プライベート エンドポイント - 保留状態](./media/configure-private-endpoints/pending.png)
1. **[接続の承認]** ダイアログ ボックスで、コメントを入力し (省略可能)、 **[はい]** を選択します。 

    ![プライベート エンドポイント - 承認](./media/configure-private-endpoints/approve.png)
1. エンドポイントの状態が **[承認済み]** と表示されていることを確認します。 

    ![プライベート エンドポイント - 承認済み状態](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>プライベート エンドポイント接続を拒否するには
保留状態または承認済み状態のプライベート エンドポイントを拒否できます。 拒否するには、次の手順に従います。 

> [!NOTE]
> このセクションで示す手順は、トピックに関するものです。 同様の手順を使用して、**ドメイン** のプライベート エンドポイントを拒否できます。 

1. 拒否する **プライベート エンドポイント** を選択し、ツールバーの **[拒否]** を選択します。

    ![[ネットワーク] - [プライベート エンドポイント接続] で [拒否] が選択されたことを示すスクリーンショット。](./media/configure-private-endpoints/reject-button.png)
1. **[接続の拒否]** ダイアログ ボックスで、コメントを入力し (省略可能)、 **[はい]** を選択します。 

    ![プライベート エンドポイント - 拒否](./media/configure-private-endpoints/reject.png)
1. エンドポイントの状態が **[拒否]** と表示されていることを確認します。 

    ![プライベート エンドポイント - 拒否状態](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > 拒否された後は、Azure portal でプライベート エンドポイントを承認できません。 


## <a name="use-azure-cli"></a>Azure CLI の使用
プライベート エンドポイントを作成するには、次の例で示すように、[az network private-endpoint create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create) メソッドを使用します。

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

例で使用するパラメーターの説明については、[az network private-endpoint create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create) のドキュメントを参照してください。 この例では、次の点に注意してください。 

- `private-connection-resource-id` には、**トピック** または **ドメイン** のリソース ID を指定します。 前の例では、種類にトピックを使用しています。
- `group-ids` の場合、`topic` または `domain` を指定します。 前の例では、`topic` を使用しています。 

プライベート エンドポイントを削除するには、次の例で示すように、[az network private-endpoint delete](/cli/azure/network/private-endpoint?#az-network-private-endpoint-delete) メソッドを使用します。

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> このセクションで示す手順は、トピックに関するものです。 同様の手順を使用して、**ドメイン** のプライベート エンドポイントを作成できます。 



### <a name="prerequisites"></a>前提条件
次のコマンドを実行することで、CLI の Azure Event Grid 拡張機能を更新します。 

```azurecli-interactive
az extension update -n eventgrid
```

拡張機能がインストールされていない場合は、次のコマンドを実行してインストールします。 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="create-a-private-endpoint"></a>プライベート エンドポイントの作成
プライベート エンドポイントを作成するには、次の例で示すように、[az network private-endpoint create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create) メソッドを使用します。

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

例で使用するパラメーターの説明については、[az network private-endpoint create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create) のドキュメントを参照してください。 この例では、次の点に注意してください。 

- `private-connection-resource-id` には、**トピック** または **ドメイン** のリソース ID を指定します。 前の例では、種類にトピックを使用しています。
- `group-ids` の場合、`topic` または `domain` を指定します。 前の例では、`topic` を使用しています。 

プライベート エンドポイントを削除するには、次の例で示すように、[az network private-endpoint delete](/cli/azure/network/private-endpoint?#az-network-private-endpoint-delete) メソッドを使用します。

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> このセクションで示す手順は、トピックに関するものです。 同様の手順を使用して、**ドメイン** のプライベート エンドポイントを作成できます。 

#### <a name="sample-script"></a>サンプル スクリプト
次の Azure リソースを作成するサンプル スクリプトを示します。

- Resource group
- 仮想ネットワーク
- 仮想ネットワーク内のサブネット
- Azure Event Grid トピック
- トピックのプライベート エンドポイント

> [!NOTE]
> このセクションで示す手順は、トピックに関するものです。 同様の手順を使用して、ドメインのプライベート エンドポイントを作成できます。

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# verify that the topic was created.
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

```

### <a name="approve-a-private-endpoint"></a>プライベート エンドポイントの承認
次のサンプル CLI スニペットでは、プライベート エンドポイント接続を承認する方法を示します。 

```azurecli-interactive
az eventgrid topic private-endpoint-connection approve \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name  $endpointName \
    --description "connection approved"
```


### <a name="reject-a-private-endpoint"></a>プライベート エンドポイントの拒否
次のサンプル CLI スニペットでは、プライベート エンドポイント接続を拒否する方法を示します。 

```azurecli-interactive
az eventgrid topic private-endpoint-connection reject \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name $endpointName \
    --description "Connection rejected"
```

### <a name="disable-public-network-access"></a>パブリック ネットワーク アクセスの無効化
既定では、Event Grid のトピックまたはドメインに対してパブリック ネットワーク アクセスが有効になっています。 プライベート エンドポイント経由でのアクセスのみを許可するには、次のコマンドを実行して、パブリック ネットワーク アクセスを無効にします。  

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```


## <a name="use-powershell"></a>PowerShell の使用
このセクションでは、PowerShell を使用して、トピックまたはドメインに対してプライベート エンドポイントを作成する方法について説明します。 

### <a name="prerequisite"></a>前提条件
「[方法:リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](../active-directory/develop/howto-create-service-principal-portal.md)」の手順に従って、Azure Active Directory アプリケーションを作成し、**ディレクトリ (テナント) ID**、**アプリケーション (クライアント) ID**、および **アプリケーション (クライアント) シークレット** の値を書き留めます。 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>REST API 呼び出し用のトークンとヘッダーを準備する 
次の前提条件コマンドを実行して、REST API 呼び出しで使用する認証トークンを取得し、Authorization やその他のヘッダー情報を設定します。 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>エンドポイント ネットワーク ポリシーを無効にしてサブネットを作成する

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>プライベート エンドポイントを使用して Event Grid トピックを作成する

> [!NOTE]
> このセクションで示す手順は、トピックに関するものです。 同様の手順を使用して、**ドメイン** のプライベート エンドポイントを作成できます。 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-06-01"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

エンドポイントが作成されたことを確認すると、次のような結果が表示されます。

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>プライベート エンドポイント接続を承認する
次のサンプル PowerShell スニペットでは、プライベート エンドポイントを承認する方法を示します。 

> [!NOTE]
> このセクションで示す手順は、トピックに関するものです。 同様の手順を使用して、**ドメイン** のプライベート エンドポイントを承認できます。 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>プライベート エンドポイント接続を拒否する
次の例では、PowerShell を使用して、プライベート エンドポイントを拒否する方法を示します。 前の GET コマンドの結果から、プライベート エンドポイントの GUID を取得できます。 

> [!NOTE]
> このセクションで示す手順は、トピックに関するものです。 同様の手順を使用して、**ドメイン** のプライベート エンドポイントを拒否できます。 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01" ` 
    -Headers $Headers
```

API を介して拒否された後でも、接続を承認できます。 Azure portal を使用した場合、拒否されたエンドポイントを承認することはできません。 

## <a name="next-steps"></a>次のステップ
* IP ファイアウォール設定を構成する方法については、[Azure Event Grid トピックまたはドメインに対する IP ファイアウォールの構成](configure-firewall.md)に関する記事を参照してください。
* ネットワーク接続の問題をトラブルシューティングするには、[ネットワーク接続の問題のトラブルシューティング](troubleshoot-network-connectivity.md)に関するページを参照してください。
