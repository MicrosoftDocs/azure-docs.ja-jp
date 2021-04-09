---
title: az search モジュールを使用した Azure CLI スクリプト
titleSuffix: Azure Cognitive Search
description: Azure CLI を使用して Azure Cognitive Search サービスを作成および構成します。 サービスのスケールアップまたはスケールダウン、管理者およびクエリの API キーの管理、システム情報のクエリを実行できます。
manager: luisca
author: DerekLegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: ee6b0e1b745e86c72843af88c0f6d17f91512e15
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176758"
---
# <a name="manage-your-azure-cognitive-search-service-with-the-azure-cli"></a>Azure CLI を使用して Azure Cognitive Search サービスを管理する
> [!div class="op_single_selector"]
> * [ポータル](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

Windows、macOS、Linux、または [Azure Cloud Shell](../cloud-shell/overview.md) で Azure CLI コマンドとスクリプトを実行して、Azure Cognitive Search を作成および構成できます。 [**az search**](/cli/azure/search) モジュールを使用すると、完全なパリティを保持する [Azure CLI](/cli/) を、[Search Management REST API](/rest/api/searchmanagement) に拡張し、次のタスクを実行できるようにします。

> [!div class="checklist"]
> * [サブスクリプション内の検索サービスを一覧表示する](#list-search-services)
> * [サービス情報を返す](#get-search-service-information)
> * [サービスを作成または削除する](#create-or-delete-a-service)
> * [プライベート エンドポイントを使用してサービスを作成する](#create-a-service-with-a-private-endpoint)
> * [管理者 API キーを再生成する](#regenerate-admin-keys)
> * [クエリ API キーを作成または削除する](#create-or-delete-query-keys)
> * [レプリカとパーティションを使用してスケールアップまたはスケールダウンする](#scale-replicas-and-partitions)
> * [共有プライベート リンク リソースを作成する](#create-a-shared-private-link-resource)

場合によっては、上記の一覧には "*ない*" タスクについて質問されることがあります。 現在、**az search** モジュールや管理 REST API を使用して、サーバー名、リージョン、レベルを変更することはできません。 サービスの作成時に専用のリソースが割り当てられます。 そのため、基になるハードウェア (場所またはノードの種類) を変更するには、新しいサービスが必要です。 同様に、サービス間で、インデックスなどのコンテンツを転送するためのツールや API はありません。

サービス内では、コンテンツの作成と管理は、[Search サービス REST API](/rest/api/searchservice/) または [.NET SDK](/dotnet/api/overview/azure/search.documents-readme) を介して行われます。 コンテンツ専用の PowerShell コマンドはありませんが、インデックスを作成したり読み込んだりする REST または .NET API を呼び出すスクリプトを記述できます。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>サブスクリプション内のサービスを一覧表示する

次のコマンドは、[**az resource**](/cli/azure/resource) から、サブスクリプションで既にプロビジョニングされている既存のリソースとサービスに関する情報を返します。 既に作成されている検索サービスの数がわからない場合は、これらのコマンドがその情報を返して、ポータルに移動する手間を省きます。

最初のコマンドは、すべての検索サービスを返します。

```azurecli-interactive
az resource list --resource-type Microsoft.Search/searchServices --output table
```

サービスの一覧からは、特定のリソースに関する情報を返します。

```azurecli-interactive
az resource list --name <service-name>
```

## <a name="list-all-az-search-commands"></a>すべての az search コマンドの一覧表示

CLI を使用して、[**az search**](/cli/azure/search) で使用できるサブグループとコマンドに関する情報を表示できます。 それ以外に関しては、[ドキュメント](/cli/azure/search)を参照してください。

`az search` で利用可能なサブグループを表示するには、次のコマンドを実行します。

```azurecli-interactive
az search --help
```

応答は、次の出力のようになります。

```
Group
    az search : Manage Azure Search services, admin keys and query keys.
        WARNING: This command group is in preview and under development. Reference and support
        levels: https://aka.ms/CLI_refstatus
Subgroups:
    admin-key                    : Manage Azure Search admin keys.
    private-endpoint-connection  : Manage Azure Search private endpoint connections.
    private-link-resource        : Manage Azure Search private link resources.
    query-key                    : Manage Azure Search query keys.
    service                      : Manage Azure Search services.
    shared-private-link-resource : Manage Azure Search shared private link resources.

For more specific examples, use: az find "az search"
```

各サブグループごとに、複数の利用可能なコマンドが用意されています。 次の行を実行すると、`service` サブグループ内の使用可能なコマンドを確認できます。

```azurecli-interactive
az search service --help
```

また、特定のコマンドに使用できる引数を確認することもできます。

```azurecli-interactive
az search service create --help
```

## <a name="get-search-service-information"></a>検索サービス情報の取得

検索サービスが含まれているリソース グループを把握している場合は、[**az search service show**](/cli/azure/search/service#az_search_service_show) を実行すると、名前、リージョン、レベル、レプリカとパーティションの数などのサービス定義が返されます。

```azurecli-interactive
az search service show --name <service-name> --resource-group <resource-group-name>
```

## <a name="create-or-delete-a-service"></a>サービスの作成または削除

[新しい検索サービスを作成](search-create-service-portal.md)するには、[**az search service create**](/cli/azure/search/service#az_search_service_show) コマンドを使用します。

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1
``` 

結果は次の出力のようになります。

```
{
  "hostingMode": "default",
  "id": "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp",
  "identity": null,
  "location": "West US",
  "name": "my-demo-searchapp",
  "networkRuleSet": {
    "bypass": "None",
    "ipRules": []
  },
  "partitionCount": 1,
  "privateEndpointConnections": [],
  "provisioningState": "succeeded",
  "publicNetworkAccess": "Enabled",
  "replicaCount": 1,
  "resourceGroup": "demo-westus",
  "sharedPrivateLinkResources": [],
  "sku": {
    "name": "standard"
  },
  "status": "running",
  "statusDetails": "",
  "tags": null,
  "type": "Microsoft.Search/searchServices"
}
```

### <a name="create-a-service-with-ip-rules"></a>IP 規則を使用してサービスを作成する

セキュリティ要件によっては、[IP ファイアウォールが構成された](service-configure-firewall.md)検索サービスを作成したい場合があります。 そのためには、下に示すように、パブリック IP (v4) アドレスまたは CIDR 範囲を `ip-rules` 引数に渡します。 規則はコンマ (`,`) またはセミコロン (`;`) で区切る必要があります。

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --ip-rules "55.5.63.73;52.228.215.197;101.37.221.205"
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>システム割り当てマネージド ID を使用してサービスを作成する

場合によっては、たとえば[マネージド ID を使用してデータ ソースに接続する](search-howto-managed-identities-storage.md)ときなど、[システム割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を有効にする必要が生じることがあります。 これを行うには、`--identity-type SystemAssigned` をコマンドに追加します。

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --identity-type SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>プライベート エンドポイントを使用してサービスを作成する

Azure Cognitive Search の [プライベートエンドポイント](../private-link/private-endpoint-overview.md) は、仮想ネットワーク上のクライアントが[プライベート リンク](../private-link/private-link-overview.md)を介して、検索インデックス内のデータに安全にアクセスできるようにします。 プライベート エンドポイントは、検索サービスのために[仮想ネットワークのアドレス空間](../virtual-network/private-ip-addresses.md)の IP アドレスを使用します。 クライアントと検索サービス間のネットワーク　トラフィックは、仮想ネットワークおよび Microsoft バックボーン ネットワーク上のプライベートリンクを経由することで、パブリック インターネット上での露出を排除します。 詳細については、[Azure Cognitive Search 用のプライベート エンドポイントの作成](service-create-private-endpoint.md)に関するドキュメントを参照してください。

次の例では、プライベート エンドポイントを使用して検索サービスを作成する方法を示します。

最初に、`PublicNetworkAccess` を `Disabled` に設定した検索サービスをデプロイします。

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --public-access Disabled
```

次に、仮想ネットワークとプライベート エンドポイントを作成します。

```azurecli-interactive
# Create the virtual network
az network vnet create \
    --resource-group <resource-group-name> \
    --location "West US" \
    --name <virtual-network-name> \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name <subnet-name> \
    --subnet-prefixes 10.1.0.0/24

# Update the subnet to disable private endpoint network policies
az network vnet subnet update \
    --name <subnet-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --disable-private-endpoint-network-policies true

# Get the id of the search service
id=$(az search service show \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --query [id] \
    --output tsv)

# Create the private endpoint
az network private-endpoint create \
    --name <private-endpoint-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --private-connection-resource-id $id \
    --group-id searchService \
    --connection-name <private-link-connection-name>  
```

最後に、プライベート DNS ゾーンを作成します。 

```azurecli-interactive
## Create private DNS zone
az network private-dns zone create \
    --resource-group <resource-group-name> \
    --name "privatelink.search.windows.net"

## Create DNS network link
az network private-dns link vnet create \
    --resource-group <resource-group-name> \
    --zone-name "privatelink.search.windows.net" \
    --name "myLink" \
    --virtual-network <virtual-network-name> \
    --registration-enabled false

## Create DNS zone group
az network private-endpoint dns-zone-group create \
   --resource-group <resource-group-name>\
   --endpoint-name <private-endpoint-name> \
   --name "myZoneGroup" \
   --private-dns-zone "privatelink.search.windows.net" \
   --zone-name "searchServiceZone"
```

プライベート エンドポイントを PowerShell で作成する場合の詳細については、こちらの [Private Link のクイックスタート](https://docs.microsoft.com/azure/private-link/create-private-endpoint-cli)に関する記事を参照してください

### <a name="manage-private-endpoint-connections"></a>プライベート エンドポイント接続を管理する

プライベー ト エンドポイント接続を作成するだけでなく、接続を `show`、`update`、`delete` することもできます。

プライベート エンドポイント接続を取得し、その状態を確認するには、[**az search private-endpoint-connection show**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_show) を使用します。

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

接続を更新するには、[**az search private-endpoint-connection update**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_update) を使用します。 次の例では、プライベート エンドポイント接続を拒否に設定します。

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
    --status Rejected \
    --description "Rejected" \
    --actions-required "Please fix XYZ"
```

プライベート エンドポイント接続を削除するには、[**az search private-endpoint-connection delete**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_delete) を使用します。

```azurecli-interactive
az search private-endpoint-connection delete \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

## <a name="regenerate-admin-keys"></a>管理者キーを再生成する

管理者 [API キー](search-security-api-keys.md)をロール オーバーするには、[**az search admin-key renew**](/cli/azure/search/admin-key#az_search_admin_key_renew) を使用します。 認証済みのアクセス用に各サービスで 2 つの管理者キーが作成されます。 キーは要求ごとに必要です。 どちらの管理者キーも機能的に同等で、検索サービスに完全な書き込みアクセス権を付与して、情報を取得したり、オブジェクトを作成または削除したりできるようにします。 2 つのキーが存在しているため、1 つを使いながら、もう 1 つを置き換えることができます。 

一度に再生成できるのは、`primary` または `secondary` キーのいずれかとして指定された 1 つのキーのみです。 中断のないサービスの場合は、必ずすべてのクライアント コードを更新して、プライマリ キーのロール オーバー中はセカンダリ キーを使用するようにしてください。 操作の実行中はキーを変更しないでください。

ご想像のとおり、クライアント コードを更新せずにキーを再生成すると、古いキーを使用する要求は失敗します。 すべての新しいキーを再生成しても完全サービスを利用できなくなるわけではなく、ポータルで引き続きサービスにアクセスできます。 プライマリ キーとセカンダリ キーを再生成した後、新しいキーを使用するようにクライアント コードを更新することができ、操作がそれに応じて再開されます。

API キーの値は、サービスによって生成されます。 Azure Cognitive Search で使用するためのカスタム キーを指定することはできません。 同様に、管理者 API キーにユーザー定義の名前はありません。 キーへの参照は、`primary` または `secondary` のいずれかの文字列に固定されます。 

```azurecli-interactive
az search admin-key renew \
    --resource-group <resource-group-name> \
    --service-name <search-service-name> \
    --key-kind primary
```

結果は次の出力のようになります。 一度に 1 つのキーしか変更しない場合でも、両方のキーが返されます。

```   
{
  "primaryKey": <alphanumeric-guid>,
  "secondaryKey": <alphanumeric-guid>  
}
```

## <a name="create-or-delete-query-keys"></a>クエリ キーの作成または削除

クライアント アプリから Azure Cognitive Search インデックスへの読み取り専用アクセスのためのクエリ [API キー](search-security-api-keys.md)を作成するには、[**az search query-key create**](/cli/azure/search/query-key#az_search_query_key_create) を使用します。 クエリ キーは、検索結果を取得する目的で特定のインデックスを認証するために使用します。 クエリ キーは、インデックス、データ ソース、インデクサーなど、サービス上の他の項目への読み取り専用アクセスを付与しません。

Azure Cognitive Search で使用するキーを指定することはできません。 API キーは、サービスによって生成されます。

```azurecli-interactive
az search query-key create \
    --name myQueryKey \
    --resource-group <resource-group-name> \
    --service-name <search-service-name>
```

## <a name="scale-replicas-and-partitions"></a>レプリカとパーティションのスケーリング

[レプリカとパーティションを増減する](search-capacity-planning.md)には、[**az search service update**](/cli/azure/search/service#az_search_service_update) を使用します。 レプリカまたはパーティションを増やすと、請求書 (固定料金と変動料金の両方が含まれます) に加算されます。 一時的な処理能力の追加の必要がある場合は、レプリカとパーティションを増やしてワークロードを処理することができます。 ポータルの [概要] ページの監視領域には、クエリの待機時間、1 秒あたりのクエリ数、および調整に関するタイルがあり、現在の容量が適切かどうかが表示されます。

リソース割り当ての追加または削除には時間がかかることがあります。 容量の調整は、既存のワークロードを続行できるように、バックグラウンドで行われます。 追加の容量は準備ができるとすぐに受信要求に使用され、追加の構成は必要ありません。 

容量の削除には中断を伴うことがあります。 要求が破棄されないようにするため、容量を減らす前にすべてのインデックス作成およびインデクサー ジョブを停止することをお勧めします。 それができない場合は、新しいターゲット レベルに達するまでレプリカやパーティションを一度に 1 つずつ減らして、容量を段階的に減らすことを検討してください。

一度コマンドを送信したら、それを途中で終了する方法はありません。 数を変更するには、コマンドが終了するまで待つ必要があります。

```azurecli-interactive
az search service update \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --partition-count 6 \
    --replica-count 6
```

レプリカとパーティション数だけでなく、`ip-rules`、`public-access`、および `identity-type` も更新することができます。

## <a name="create-a-shared-private-link-resource"></a>共有プライベート リンク リソースを作成する

Azure Cognitive Search API によって作成された、セキュリティで保護されたリソースのプライベート エンドポイントは、"*共有プライベート リンク リソース*" と呼ばれます。 これは、[Azure Private Link サービス](https://azure.microsoft.com/services/private-link/)と統合されているストレージ アカウントなどのリソースへのアクセスを "共有" しているためです。

インデクサーを使用して Azure Cognitive Search のデータにインデックスを作成し、データ ソースがプライベート ネットワーク上にある場合は、データに到達するために発信[プライベート エンドポイント接続](../private-link/private-endpoint-overview.md)を作成できます。

Azure Cognitive Search から発信プライベート エンドポイントを作成するための Azure リソースの完全な一覧は、関連する **グループ ID** 値と共に、[こちら](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis)で参照できます。

共有プライベート リンク リソースを作成するには、[**az search shared-private-link-resource create**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list) を使用します。 このコマンドを実行する前にデータ ソースでいくつかの構成が必要になる場合がある点に注意してください。

```azurecli-interactive
az search shared-private-link-resource create \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> \
    --group-id blob \
    --resource-id "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage"  \
    --request-message "Please approve" 
```


共有プライベート リンク リソースを取得し、その状態を表示するには、[**az search shared-private-link-resource list**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list) を使用します。

```azurecli-interactive
az search shared-private-link-resource list \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

これを使用する前に、次のコマンドで接続を承認する必要があります。 プライベート エンドポイント接続の ID は、子リソースから取得する必要があります。 この例では、az storage から接続 ID を取得します。

```azurecli-interactive
id = (az storage account show -n myBlobStorage --query "privateEndpointConnections[0].id")

az network private-endpoint-connection approve --id $id
```

共有プライベート リンク リソースを削除するには、[**az search shared-private-link-resource delete**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_delete) を使用します。

```azurecli-interactive
az search shared-private-link-resource delete \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

共有プライベート リンク リソースの設定の詳細については、[プライベート エンドポイント経由でのインデクサー接続の作成](search-indexer-howto-access-private.md)に関するドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

ポータル、REST API、または .NET SDK を使用して、[インデックス](search-what-is-an-index.md)を作成し、[インデックスのクエリを実行](search-query-overview.md)します。

* [Azure portal で Azure Cognitive Search インデックスを作成する](search-get-started-portal.md)
* [インデクサーをセットアップして他のサービスからデータを読み込む](search-indexer-overview.md)
* [Azure Portal の Search エクスプローラーを使用して Azure Cognitive Search インデックスを照会する](search-explorer.md)
* [.NET での Azure Cognitive Search の使用方法](search-howto-dotnet-sdk.md)
