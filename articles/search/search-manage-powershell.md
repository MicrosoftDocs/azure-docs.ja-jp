---
title: Az.Search モジュールを使用した PowerShell スクリプト
titleSuffix: Azure Cognitive Search
description: PowerShell を使用して Azure Cognitive Search サービスを作成および構成します。 サービスのスケールアップまたはスケールダウン、管理者およびクエリの API キーの管理、システム情報のクエリを実行できます。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: efb5d498c627a6731d2a90623c81eefabd0042a0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462781"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>PowerShell を使用して Azure Cognitive Search サービスを管理する
> [!div class="op_single_selector"]
> * [ポータル](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

Windows、Linux、または [Azure Cloud Shell](../cloud-shell/overview.md) で PowerShell コマンドレットとスクリプトを実行して、Azure Cognitive Search を作成および構成できます。 **Az.Search** モジュールでは、完全なパリティを保持する [Azure PowerShell](/powershell/) を、[Search 管理 REST API](/rest/api/searchmanagement) に拡張し、次のタスクを実行できるようにします。

> [!div class="checklist"]
> * [サブスクリプション内の検索サービスを一覧表示する](#list-search-services)
> * [サービス情報を返す](#get-search-service-information)
> * [サービスを作成または削除する](#create-or-delete-a-service)
> * [プライベート エンドポイントを使用してサービスを作成する](#create-a-service-with-a-private-endpoint)
> * [管理者 API キーを再生成する](#regenerate-admin-keys)
> * [クエリ API キーを作成または削除する](#create-or-delete-query-keys)
> * [レプリカとパーティションを使用してスケールアップまたはスケールダウンする](#scale-replicas-and-partitions)
> * [共有プライベート リンク リソースを作成する](#create-a-shared-private-link-resource)

場合によっては、上記の一覧には "*ない*" タスクについて質問されることがあります。 現在、**Az.Search** モジュールや管理 REST API を使用して、サーバー名、リージョン、レベルを変更することはできません。 サービスの作成時に専用のリソースが割り当てられます。 そのため、基になるハードウェア (場所またはノードの種類) を変更するには、新しいサービスが必要です。 同様に、サービス間で、インデックスなどのコンテンツを転送するためのツールや API はありません。

サービス内では、コンテンツの作成と管理は、[Search サービス REST API](/rest/api/searchservice/) または [.NET SDK](/dotnet/api/overview/azure/search.documents-readme) を介して行われます。 コンテンツ専用の PowerShell コマンドはありませんが、インデックスを作成したり読み込んだりする REST または .NET API を呼び出す PowerShell スクリプトを記述できます。

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>バージョンの確認とモジュールの読み込み

この記事の例は対話形式で、昇格された権限が必要です。 Azure PowerShell (**Az** モジュール) がインストールされている必要があります。 詳しくは、[Azure PowerShell のインストール](/powershell/azure/)に関する記事をご覧ください。

### <a name="powershell-version-check-51-or-later"></a>PowerShell のバージョン チェック (5.1 以降)

サポートされているどのオペレーティング システムでも、ローカルの PowerShell は 5.1 以降である必要があります。

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Azure PowerShell の読み込み

**Az** がインストールされているかどうか不明な場合は、検証手順として次のコマンドを実行します。 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

一部のシステムでは、モジュールの自動読み込みは行われません。 上記のコマンドでエラーが発生する場合は、モジュールを読み込んでみてください。これが失敗した場合は、インストール手順に戻って、手順を飛ばしていなかったかどうか確認してください。

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>ブラウザーでサインイン トークンを使用して Azure に接続する

ポータルのサインイン資格情報を使用して、PowerShell でサブスクリプションに接続することができます。 または、[サービス プリンシパルを使用して非対話的に認証](../active-directory/develop/howto-authenticate-service-principal-powershell.md)することができます。

```azurepowershell-interactive
Connect-AzAccount
```

複数の Azure サブスクリプションを保持している場合は、使用する Azure サブスクリプションを設定します。 現在のサブスクリプションを一覧表示するには、次のコマンドを実行します。

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

サブスクリプションを指定するには、次のコマンドを実行します。 次の例では、サブスクリプション名は `ContosoSubscription`です。

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>サブスクリプション内のサービスを一覧表示する

次のコマンドは、[**Az.Resources**](/powershell/module/az.resources) から、サブスクリプションで既にプロビジョニングされている既存のリソースとサービスに関する情報を返します。 既に作成されている検索サービスの数がわからない場合は、これらのコマンドがその情報を返して、ポータルに移動する手間を省きます。

最初のコマンドは、すべての検索サービスを返します。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

サービスの一覧からは、特定のリソースに関する情報を返します。

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

結果は次の出力のようになります。

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Az.Search のインポート

[**Az.Search**](/powershell/module/az.search) からのコマンドは、モジュールを読み込むまでは利用できません。

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>すべての Az.Search コマンドの一覧表示

検証手順として、モジュールで提供されるコマンドの一覧を返します。

```azurepowershell-interactive
Get-Command -Module Az.Search
```

結果は次の出力のようになります。

```
CommandType     Name                                               Version    Source                                                                
-----------     ----                                               -------    ------                                                                
Cmdlet          Get-AzSearchAdminKeyPair                           0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchPrivateEndpointConnection              0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchPrivateLinkResource                    0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchQueryKey                               0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchAdminKey                               0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchQueryKey                               0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchPrivateEndpointConnection           0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchQueryKey                            0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchService                             0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchSharedPrivateLinkResource           0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchPrivateEndpointConnection              0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search   
```

古いバージョンのパッケージがある場合は、モジュールを更新して最新の機能を取得してください。

```azurepowershell-interactive
Update-Module -Name Az.Search
```

## <a name="get-search-service-information"></a>検索サービス情報の取得

**Az.Search** がインポートされ、検索サービスが含まれているリソース グループがわかったら、[Get-AzSearchService](/powershell/module/az.search/get-azsearchservice) を実行して、名前、リージョン、レベル、レプリカとパーティションの数などのサービス定義を返します。

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

結果は次の出力のようになります。

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>サービスの作成または削除

[**New-AzSearchService**](/powershell/module/az.search/new-azsearchservice) は [新しい検索サービスの作成](search-create-service-portal.md)に使用されます。

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3 -HostingMode Default
``` 
結果は次の出力のようになります。

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

### <a name="create-a-service-with-ip-rules"></a>IP 規則を使用してサービスを作成する

セキュリティ要件によっては、[IP ファイアウォールが構成された](service-configure-firewall.md)検索サービスを作成したい場合があります。 そのためには、下に示すように、最初に IP 規則を定義して、それらを `IPRuleList` パラメーターに渡します。

```azurepowershell-interactive
$ipRules = @([pscustomobject]@{Value="55.5.63.73"},
        [pscustomobject]@{Value="52.228.215.197"},
        [pscustomobject]@{Value="101.37.221.205"})

 New-AzSearchService -ResourceGroupName <resource-group-name> `
                      -Name <search-service-name> `
                      -Sku Standard `
                      -Location "West US" `
                      -PartitionCount 3 -ReplicaCount 3 `
                      -HostingMode Default `
                      -IPRuleList $ipRules
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>システム割り当てマネージド ID を使用してサービスを作成する

場合によっては、たとえば[マネージド ID を使用してデータ ソースに接続する](search-howto-managed-identities-storage.md)ときなど、[システム割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を有効にする必要が生じることがあります。 これを行うには、`-IdentityType SystemAssigned` をコマンドに追加します。

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName <resource-group-name> `
                      -Name <search-service-name> `
                      -Sku Standard `
                      -Location "West US" `
                      -PartitionCount 3 -ReplicaCount 3 `
                      -HostingMode Default `
                      -IdentityType SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>プライベート エンドポイントを使用してサービスを作成する

Azure Cognitive Search の [プライベートエンドポイント](../private-link/private-endpoint-overview.md) は、仮想ネットワーク上のクライアントが[プライベート リンク](../private-link/private-link-overview.md)を介して、検索インデックス内のデータに安全にアクセスできるようにします。 プライベート エンドポイントは、検索サービスのために[仮想ネットワークのアドレス空間](../virtual-network/private-ip-addresses.md)の IP アドレスを使用します。 クライアントと検索サービス間のネットワーク　トラフィックは、仮想ネットワークおよび Microsoft バックボーン ネットワーク上のプライベートリンクを経由することで、パブリック インターネット上での露出を排除します。 詳細については、[Azure Cognitive Search 用のプライベート エンドポイントの作成](service-create-private-endpoint.md)に関するドキュメントを参照してください。

次の例では、プライベート エンドポイントを使用して検索サービスを作成する方法を示します。

最初に、`PublicNetworkAccess` を `Disabled` に設定した検索サービスをデプロイします。

```azurepowershell-interactive
$searchService = New-AzSearchService `
    -ResourceGroupName <resource-group-name> `
    -Name <search-service-name> `
    -Sku Standard `
    -Location "West US" `
    -PartitionCount 1 -ReplicaCount 1 `
    -HostingMode Default `
    -PublicNetworkAccess Disabled
```

次に、仮想ネットワーク、プライベート ネットワーク接続、プライベート エンドポイントを作成します。

```azurepowershell-interactive
# Create the subnet
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
    -Name <subnet-name> `
    -AddressPrefix 10.1.0.0/24 `
    -PrivateEndpointNetworkPolicies Disabled 

# Create the virtual network
$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName <resource-group-name> `
    -Location "West US" `
    -Name <virtual-network-name> `
    -AddressPrefix 10.1.0.0/16 `
    -Subnet $subnetConfig

# Create the private network connection
$privateLinkConnection = New-AzPrivateLinkServiceConnection `
    -Name <private-link-name> `
    -PrivateLinkServiceId $searchService.Id `
    -GroupId searchService

# Create the private endpoint
$privateEndpoint = New-AzPrivateEndpoint `
    -Name <private-endpoint-name> `
    -ResourceGroupName <resource-group-name> `
    -Location "West US" `
    -Subnet $virtualNetwork.subnets[0] `
    -PrivateLinkServiceConnection $privateLinkConnection
```

最後に、プライベート DNS ゾーンを作成します。 

```azurepowershell-interactive
## Create private dns zone
$zone = New-AzPrivateDnsZone `
    -ResourceGroupName <resource-group-name> `
    -Name "privatelink.search.windows.net"

## Create dns network link
$link = New-AzPrivateDnsVirtualNetworkLink `
    -ResourceGroupName <resource-group-name> `
    -ZoneName "privatelink.search.windows.net" `
    -Name "myLink" `
    -VirtualNetworkId $virtualNetwork.Id

## Create DNS configuration 
$config = New-AzPrivateDnsZoneConfig `
    -Name "privatelink.search.windows.net" `
    -PrivateDnsZoneId $zone.ResourceId

## Create DNS zone group
New-AzPrivateDnsZoneGroup `
    -ResourceGroupName <resource-group-name> `
    -PrivateEndpointName <private-endpoint-name> `
    -Name 'myZoneGroup' `
    -PrivateDnsZoneConfig $config
```

プライベート エンドポイントを PowerShell で作成する場合の詳細については、こちらの[プライベート リンクのクイックスタート](../private-link/create-private-endpoint-powershell.md)に関する記事を参照してください。

### <a name="manage-private-endpoint-connections"></a>プライベート エンドポイント接続を管理する

プライベー ト エンドポイント接続を作成するだけでなく、接続を `Get`、`Set`、`Remove` することもできます。

[Get-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Get-AzSearchPrivateEndpointConnection) は、プライベート エンドポイント接続を取得するためと、その状態を確認するために使用します。

```azurepowershell-interactive
Get-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name>
```

[Set-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Set-AzSearchPrivateEndpointConnection) は、接続を更新するために使用します。 次の例では、プライベート エンドポイント接続を拒否に設定します。

```azurepowershell-interactive
Set-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <pe-connection-name> -Status Rejected  -Description "Rejected"
```

[Remove-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Remove-AzSearchPrivateEndpointConnection) は、プライベート エンドポイント接続を削除するために使用します。

```azurepowershell-interactive
 Remove-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <pe-connection-name>
```

## <a name="regenerate-admin-keys"></a>管理者キーを再生成する

[**New-AzSearchAdminKey**](/powershell/module/az.search/new-azsearchadminkey) は、管理者 [API キー](search-security-api-keys.md)をロール オーバーするために使用されます。 認証済みのアクセス用に各サービスで 2 つの管理者キーが作成されます。 キーは要求ごとに必要です。 どちらの管理者キーも機能的に同等で、検索サービスに完全な書き込みアクセス権を付与して、情報を取得したり、オブジェクトを作成または削除したりできるようにします。 2 つのキーが存在しているため、1 つを使いながら、もう 1 つを置き換えることができます。 

一度に再生成できるのは、`primary` または `secondary` キーのいずれかとして指定された 1 つのキーのみです。 中断のないサービスの場合は、必ずすべてのクライアント コードを更新して、プライマリ キーのロール オーバー中はセカンダリ キーを使用するようにしてください。 操作の実行中はキーを変更しないでください。

ご想像のとおり、クライアント コードを更新せずにキーを再生成すると、古いキーを使用する要求は失敗します。 すべての新しいキーを再生成しても完全サービスを利用できなくなるわけではなく、ポータルで引き続きサービスにアクセスできます。 プライマリ キーとセカンダリ キーを再生成した後、新しいキーを使用するようにクライアント コードを更新することができ、操作がそれに応じて再開されます。

API キーの値は、サービスによって生成されます。 Azure Cognitive Search で使用するためのカスタム キーを指定することはできません。 同様に、管理者 API キーにユーザー定義の名前はありません。 キーへの参照は、`primary` または `secondary` のいずれかの文字列に固定されます。 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

結果は次の出力のようになります。 一度に 1 つのキーしか変更しない場合でも、両方のキーが返されます。

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>クエリ キーの作成または削除

[**New-AzSearchQueryKey**](/powershell/module/az.search/new-azsearchquerykey) は、クライアント アプリから Azure Cognitive Search インデックスへの読み取り専用アクセスのためのクエリ [API キー](search-security-api-keys.md)を作成するために使用します。 クエリ キーは、検索結果を取得する目的で特定のインデックスを認証するために使用します。 クエリ キーは、インデックス、データ ソース、インデクサーなど、サービス上の他の項目への読み取り専用アクセスを付与しません。

Azure Cognitive Search で使用するキーを指定することはできません。 API キーは、サービスによって生成されます。

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>レプリカとパーティションのスケーリング

[**Set-AzSearchService**](/powershell/module/az.search/set-azsearchservice) は、[レプリカとパーティションを増減させて](search-capacity-planning.md)サービス内での課金対象のリソースを再調整するために使用します。 レプリカまたはパーティションを増やすと、請求書 (固定料金と変動料金の両方が含まれます) に加算されます。 一時的な処理能力の追加の必要がある場合は、レプリカとパーティションを増やしてワークロードを処理することができます。 ポータルの [概要] ページの監視領域には、クエリの待機時間、1 秒あたりのクエリ数、および調整に関するタイルがあり、現在の容量が適切かどうかが表示されます。

リソース割り当ての追加または削除には時間がかかることがあります。 容量の調整は、既存のワークロードを続行できるように、バックグラウンドで行われます。 追加の容量は準備ができるとすぐに受信要求に使用され、追加の構成は必要ありません。 

容量の削除には中断を伴うことがあります。 要求が破棄されないようにするため、容量を減らす前にすべてのインデックス作成およびインデクサー ジョブを停止することをお勧めします。 それができない場合は、新しいターゲット レベルに達するまでレプリカやパーティションを一度に 1 つずつ減らして、容量を段階的に減らすことを検討してください。

一度コマンドを送信したら、それを途中で終了する方法はありません。 数を変更するには、コマンドが終了するまで待つ必要があります。

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

結果は次の出力のようになります。

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-a-shared-private-link-resource"></a>共有プライベート リンク リソースを作成する

Azure Cognitive Search API によって作成された、セキュリティで保護されたリソースのプライベート エンドポイントは、"*共有プライベート リンク リソース*" と呼ばれます。 これは、[Azure Private Link サービス](https://azure.microsoft.com/services/private-link/)と統合されているストレージ アカウントなどのリソースへのアクセスを "共有" しているためです。

インデクサーを使用して Azure Cognitive Search のデータにインデックスを作成し、データ ソースがプライベート ネットワーク上にある場合は、データに到達するために発信[プライベート エンドポイント接続](../private-link/private-endpoint-overview.md)を作成できます。

Azure Cognitive Search から発信プライベート エンドポイントを作成するための Azure リソースの完全な一覧は、関連する **グループ ID** 値と共に、[こちら](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis)で参照できます。

[New-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/New-AzSearchSharedPrivateLinkResource) は、共有プライベート リンク リソースを作成するために使用します。 このコマンドを実行する前にデータ ソースでいくつかの構成が必要になる場合がある点に注意してください。

```azurepowershell-interactive
New-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name> -PrivateLinkResourceId /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage -GroupId <group-id> -RequestMessage "Please approve" 
```

[Get-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/Get-AzSearchSharedPrivateLinkResource) を使用すると、共有プライベート リンク リソースを取得し、それらの状態を表示することができます。

```azurepowershell-interactive
Get-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name>
```

これを使用する前に、次のコマンドで接続を承認する必要があります。

```azurepowershell-interactive
Approve-AzPrivateEndpointConnection `
    -Name <spl-name> `
    -ServiceName <search-service-name> `
    -ResourceGroupName <resource-group-name> `
    -Description = "Approved"
```

[Remove-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/Remove-AzSearchSharedPrivateLinkResource) は、共有プライベート リンク リソースを削除するために使用します。

```azurepowershell-interactive
$job = Remove-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name> -Force -AsJob

$job | Get-Job
```

共有プライベート リンク リソースの設定の詳細については、[プライベート エンドポイント経由でのインデクサー接続の作成](search-indexer-howto-access-private.md)に関するドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

ポータル、REST API、または .NET SDK を使用して、[インデックス](search-what-is-an-index.md)を作成し、[インデックスのクエリを実行](search-query-overview.md)します。

* [Azure portal で Azure Cognitive Search インデックスを作成する](search-get-started-portal.md)
* [インデクサーをセットアップして他のサービスからデータを読み込む](search-indexer-overview.md)
* [Azure Portal の Search エクスプローラーを使用して Azure Cognitive Search インデックスを照会する](search-explorer.md)
* [.NET での Azure Cognitive Search の使用方法](search-howto-dotnet-sdk.md)
