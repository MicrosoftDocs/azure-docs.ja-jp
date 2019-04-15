---
title: Az.Search モジュールを使用した PowerShell スクリプト - Azure Search
description: PowerShell を使用して Azure Search サービスを作成および構成します。 サービスをスケールアップまたはスケールダウンしたり、管理者とクエリの API キーを管理したり、システム情報のクエリを実行したりできます。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: heidist
ms.openlocfilehash: 8f07468ccff4431e1afdf66aedc72599ddc0c25b
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620599"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>PowerShell を使用して Azure Search サービスを管理する
> [!div class="op_single_selector"]
> * [ポータル](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Windows、Linux、または [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) で PowerShell コマンドレットとスクリプトを実行して、Azure Search を作成および構成できます。 **Az.Search** モジュールは、完全なパリティを含む Azure PowerShell を [Azure Search 管理 REST API シリーズ](https://docs.microsoft.com/rest/api/searchmanagement)に拡張します。 Azure PowerShell と **Az.Search** を使用すると、次のタスクを実行できます。

> [!div class="checklist"]
> * [サブスクリプションのすべての検索サービスを一覧表示する](#list-search-services)
> * [特定の検索サービスに関する情報を取得する](#get-search-service-information)
> * [サービスを作成または削除する](#create-or-delete-a-service)
> * [管理者 API キーを再生成する](#regenerate-admin-keys)
> * [クエリ API キーを作成または削除する](#create-or-delete-query-keys)
> * [レプリカとパーティションを増減してサービスをスケールする](#scale-replicas-and-partitions)

PowerShell は、サービスの名前、リージョン、またはレベルの変更には使用できません。 サービスの作成時に専用のリソースが割り当てられます。 基になるハードウェア (場所またはノードの種類) を変更するには、新しいサービスが必要です。 サービス間でコンテンツを転送するためのツールや API はありません。 すべてのコンテンツ管理は [REST](https://docs.microsoft.com/rest/api/searchservice/) または [.NET](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search) API を使用して行われ、インデックスを移動する場合は、新しいサービスで再作成したり再読み込みしたりする必要があります。 

コンテンツ管理のための専用の PowerShell コマンドはありませんが、インデックスを作成したり読み込んだりする REST または .NET を呼び出す PowerShell スクリプトを記述できます。 **Az.Search** モジュール自体はこれらの操作を提供していません。

PowerShell やその他の API (ポータルのみ) の使用がサポートされていないその他のタスクは、次のとおりです。
+ [AI によって強化されたインデックス作成](cognitive-search-concept-intro.md)のための [Cognitive Services リソースをアタッチする](cognitive-search-attach-cognitive-services.md)。 Cognitive Service は、サブスクリプションやサービスではなく、スキルにアタッチされます。
+ Azure Search の監視に使用される[アドオンの監視ソリューション](search-monitor-usage.md#add-on-monitoring-solutions)または[検索トラフィックの分析](search-traffic-analytics.md)。

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>バージョンの確認とモジュールの読み込み

この記事の例は対話形式で、昇格された権限が必要です。 Azure PowerShell (**Az** モジュール) がインストールされている必要があります。 詳しくは、[Azure PowerShell のインストール](/powershell/azure/overview)に関する記事をご覧ください。

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

## <a name="list-all-azure-search-services-in-your-subscription"></a>サブスクリプションのすべての Azure Search サービスを一覧表示する

次のコマンドは、[**Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources) から、サブスクリプションで既にプロビジョニングされている既存のリソースとサービスに関する情報を返します。 既に作成されている検索サービスの数がわからない場合は、これらのコマンドがその情報を返して、ポータルに移動する手間を省きます。

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
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Az.Search のインポート

[**Az.Search**](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) からのコマンドは、モジュールを読み込むまでは利用できません。

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
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Cmdlet          Get-AzSearchAdminKeyPair            0.7.1      Az.Search
Cmdlet          Get-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          Get-AzSearchService                 0.7.1      Az.Search
Cmdlet          New-AzSearchAdminKey                0.7.1      Az.Search
Cmdlet          New-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          New-AzSearchService                 0.7.1      Az.Search
Cmdlet          Remove-AzSearchQueryKey             0.7.1      Az.Search
Cmdlet          Remove-AzSearchService              0.7.1      Az.Search
Cmdlet          Set-AzSearchService                 0.7.1      Az.Search
```

## <a name="get-search-service-information"></a>検索サービス情報の取得

**Az.Search** がインポートされ、検索サービスが含まれているリソース グループがわかったら、[Get-AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) を実行して、名前、リージョン、レベル、レプリカとパーティションの数などのサービス定義を返します。

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

[**New-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) は[新しい検索サービスの作成](search-create-service-portal.md)に使用されます。

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
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

## <a name="regenerate-admin-keys"></a>管理者キーを再生成する

[**New-AzSearchAdminKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) は、管理者 [API キー](search-security-api-keys.md)をロール オーバーするために使用されます。 認証済みのアクセス用に各サービスで 2 つの管理者キーが作成されます。 キーは要求ごとに必要です。 どちらの管理者キーも機能的に同等で、検索サービスに完全な書き込みアクセス権を付与して、情報を取得したり、オブジェクトを作成または削除したりできるようにします。 2 つのキーが存在しているため、1 つを使いながら、もう 1 つを置き換えることができます。 

一度に再生成できるのは、`primary` または `secondary` キーのいずれかとして指定された 1 つのキーのみです。 中断のないサービスの場合は、必ずすべてのクライアント コードを更新して、プライマリ キーのロール オーバー中はセカンダリ キーを使用するようにしてください。 操作の実行中はキーを変更しないでください。

ご想像のとおり、クライアント コードを更新せずにキーを再生成すると、古いキーを使用する要求は失敗します。 すべての新しいキーを再生成しても完全サービスを利用できなくなるわけではなく、ポータルで引き続きサービスにアクセスできます。 プライマリ キーとセカンダリ キーを再生成した後、新しいキーを使用するようにクライアント コードを更新することができ、操作がそれに応じて再開されます。

API キーの値は、サービスによって生成されます。 Azure Search で使用するためのカスタム キーを指定することはできません。 同様に、管理者 API キーにユーザー定義の名前はありません。 キーへの参照は、`primary` または `secondary` のいずれかの文字列に固定されます。 

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

[**New-AzSearchQueryKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) は、クライアント アプリから Azure Search インデックスへの読み取り専用アクセスのためのクエリ [API キー](search-security-api-keys.md)を作成するために使用します。 クエリ キーは、検索結果を取得する目的で特定のインデックスを認証するために使用します。 クエリ キーは、インデックス、データ ソース、インデクサーなど、サービス上の他の項目への読み取り専用アクセスを付与しません。

Azure Search で使用するためのキーを指定することはできません。 API キーは、サービスによって生成されます。

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>レプリカとパーティションのスケーリング

[**Set-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) は、[レプリカとパーティションを増減させて](search-capacity-planning.md)サービス内での課金対象のリソースを再調整するために使用します。 レプリカまたはパーティションを増やすと、請求書 (固定料金と変動料金の両方が含まれます) に加算されます。 一時的な処理能力の追加の必要がある場合は、レプリカとパーティションを増やしてワークロードを処理することができます。 ポータルの [概要] ページの監視領域には、クエリの待機時間、1 秒あたりのクエリ数、および調整に関するタイルがあり、現在の容量が適切かどうかが表示されます。

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


## <a name="next-steps"></a>次の手順

ポータル、REST API、または .NET SDK を使用して、[インデックス](search-what-is-an-index.md)を作成し、[インデックスのクエリを実行](search-query-overview.md)します。

* [Azure ポータルでの Azure Search インデックスの作成](search-create-index-portal.md)
* [インデクサーをセットアップして他のサービスからデータを読み込む](search-indexer-overview.md)
* [Azure Portal の Search エクスプローラーを使用して Azure Search インデックスを照会する](search-explorer.md)
* [.NET で Azure Search を使用する方法](search-howto-dotnet-sdk.md)