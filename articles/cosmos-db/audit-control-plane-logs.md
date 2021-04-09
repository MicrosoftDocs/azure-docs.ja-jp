---
title: Azure Cosmos DB コントロール プレーン操作を監査する方法
description: リージョンの追加、スループットの更新、リージョンのフェールオーバー、VNet の追加など、コントロール プレーン操作を Azure Cosmos DB 内で監査する方法について説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/05/2020
ms.author: sngun
ms.openlocfilehash: 6f3e408343fc75d6587d1a67a0179edf13d56e36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658250"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Azure Cosmos DB コントロール プレーン操作を監査する方法
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB のコントロール プレーンとは、Azure Cosmos アカウントでさまざまな一連の操作を実行できる RESTful サービスです。 これは、パブリック リソース モデル (例: データベース、アカウント) とエンドユーザーに対するさまざまな操作を公開し、リソース モデルに対してアクションを実行します。 Azure Cosmos アカウントまたはコンテナーに対する変更も、コントロール プレーンの操作に含まれます。 たとえば、Azure Cosmos アカウントの作成、リージョンの追加、スループットの更新、リージョンのフェールオーバー、VNet の追加などの操作は、コントロール プレーン操作の一部です。 この記事では、Azure Cosmos DB でコントロール プレーン操作を監査する方法について説明します。 Azure Cosmos アカウントでのコントロール プレーンの操作は、Azure CLI、PowerShell、または Azure portal を使用して実行できます。それに対し、コンテナーには Azure CLI または PowerShell を使用できます。

コントロール プレーン操作の監査が有用なシナリオ例を次に示します。

* ご自分の Azure Cosmos アカウントのファイアウォール規則が変更されたときにアラートを受け取りたい。 このアラートは、お使いの Azure Cosmos アカウントのネットワーク セキュリティを管理する規則が未承認で変更された場合にそれを検出し、即座にアクションをとるために必要です。

* お使いの Azure Cosmos アカウントに新規リージョンの追加または削除があったときにアラートを受け取りたい。 リージョンの追加や削除は、課金やデータ主権要件に影響します。 このアラートは、お使いのアカウントにリージョンが誤って追加または削除された場合に、それを検出するのに役立ちます。

* さらに変更に関する詳細を確認したい場合は、診断ログをご確認ください。 たとえば、VNet が変更されたなどです。

## <a name="disable-key-based-metadata-write-access"></a>キー ベースのメタデータ書き込みアクセスを無効にする

Azure Cosmos DB でコントロール プレーン操作を監査する前に、アカウントでのキーベースのメタデータ書き込みアクセスを無効にします。 キーベースのメタデータ書き込みアクセスが無効になっている場合、アカウント キーを使用して Azure Cosmos アカウントに接続しているクライアントは、アカウントにアクセスできません。 書き込みアクセスを無効にするには、`disableKeyBasedMetadataWriteAccess` プロパティを true に設定します。 このプロパティを設定すると、適切な Azure ロールと資格情報を持つユーザーは、すべてのリソースに変更を加えることができます。 このプロパティを設定する方法の詳細については、「[SDK からの変更を防止する](role-based-access-control.md#prevent-sdk-changes)」の記事を参照してください。 

`disableKeyBasedMetadataWriteAccess` が有効になった後で、SDK ベースのクライアントで作成または更新操作を実行すると、「"*Azure Cosmos DB エンドポイント経由での 'ContainerNameorDatabaseName' リソースに対する "POST" 操作は許可されません*"」エラーが返されます。 お使いのアカウントでのこのような操作へのアクセスを有効にするか、Azure Resource Manager、Azure CLI、または Azure PowerShell を使用して作成/更新操作を実行する必要があります。 切り替えて戻るには、[Cosmos SDK からの変更の防止](role-based-access-control.md#prevent-sdk-changes)に関する記事の説明に従って、Azure CLI を使用して disableKeyBasedMetadataWriteAccess を **false** に設定します。 `disableKeyBasedMetadataWriteAccess` の値を、true ではなく false に変更してください。

メタデータ書き込みアクセスを無効にする場合は、次の点を考慮してください。

* SDK またはアカウント キーを使用して、アプリケーションが上記のリソース (コレクションの作成、スループットの更新、... など) を変更するメタデータ呼び出しを行わないようにして評価します。

* 現時点では、Azure portal はメタデータ操作にアカウント キーを使用するため、これらの操作はブロックされます。 または、Azure CLI、SDK、または Resource Manager テンプレートのデプロイを使用して、上記の操作を実行します。

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>コントロール プレーン操作の診断ログを有効にする

Azure portal を使用して、コントロール プレーン操作の診断ログを有効にすることができます。 有効にすると、関連する詳細と共に、この操作の開始イベントと完了イベントが、ペアで診断ログに記録されます。 たとえば、リージョンのフェールオーバー イベントは *RegionFailoverStart* と *RegionFailoverComplete* で完了します。

コントロールプレーン操作のログ記録を有効にするには、次の手順を使用します。

1. [Azure portal](https://portal.azure.com) にサインインし、Azure Cosmos アカウントに移動します。

1. **[診断設定]** ウィンドウを開き、作成するログの **名称** を指定します。

1. ログの種類として **[ControlPlaneRequests]** を選択し、 **[Log Analytics に送信]** オプションを選択します。

また、ログをストレージ アカウントに保存したり、イベント ハブにストリーム配信したりすることもできます。 この記事では、ログ分析にログを送信してから、クエリを実行する方法について説明します。 有効にした後、診断ログが有効になるまで数分かかります。 その時点より後に実行されたすべてのコントロール プレーン操作を追跡できます。 次のスクリーンショットは、コントロール プレーン ログを有効化する方法を示したものです。

:::image type="content" source="./media/audit-control-plane-logs/enable-control-plane-requests-logs.png" alt-text="コントロール プレーン要求のログを有効にする":::

## <a name="view-the-control-plane-operations"></a>コントロール プレーン操作の表示

ログを有効にした後、次の手順を使用して、特定のアカウントの操作を追跡します。

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. 左側のナビゲーションから **[モニター]** タブを開き、 **[ログ]** ペインを選択します。 これにより、スコープ内の特定のアカウントでクエリを簡単に実行できる UI が開きます。 コントロール プレーンのログを表示するには、次のクエリを実行します。

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

次のスクリーンショットでは、Azure Cosmos アカウントの整合性レベルが変更されたときのログがキャプチャされています。

:::image type="content" source="./media/audit-control-plane-logs/add-ip-filter-logs.png" alt-text="VNet が追加されたときのコントロール プレーンのログ":::

次のスクリーンショットは、Cassandra アカウントのキースペースまたはテーブルが作成されたとき、およびスループットが更新されたときにログをキャプチャします。 次のスクリーンショットに示すように、データベースやコンテナーでの作成と更新の操作に関するコントロール プレーンのログは、個別にログに記録されます。

:::image type="content" source="./media/audit-control-plane-logs/throughput-update-logs.png" alt-text="スループットが更新されたときのコントロール プレーンのログ":::

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>特定の操作に関連付けられている ID を識別する

さらにデバッグする場合は、アクティビティ ID の使用または操作のタイムスタンプにより、**アクティビティ ログ** 内の特定の操作を特定できます。 タイムスタンプは、アクティビティ ID が明示的に渡されていない一部の Resource Manager クライアントに使用されます。 アクティビティ ログには、操作が開始された ID の詳細が示されます。 次のスクリーンショットは、アクティビティ ID を使用し、アクティビティ ログでその ID に関連付けられている操作を検索する方法を示しています。

:::image type="content" source="./media/audit-control-plane-logs/find-operations-with-activity-id.png" alt-text="アクティビティ ID を使用して操作を検索する":::

## <a name="control-plane-operations-for-azure-cosmos-account"></a>Azure Cosmos アカウントのコントロール プレーン操作

アカウント レベルで行えるコントロール プレーン操作は、次のとおりです。 ほとんどの操作は、アカウント レベルで追跡されます。 これらの操作は、Azure Monitor のメトリックとして用意されています。

* 追加されたリージョン
* 削除されたリージョン
* 削除されたアカウント
* リージョンのフェールオーバー
* 作成されるアカウント
* 削除された仮想ネットワーク
* 更新されたアカウント ネットワーク設定
* 更新されたアカウント レプリケーション設定
* 更新されたアカウント キー
* 更新されたアカウント バックアップ設定
* 更新されたアカウント診断設定

## <a name="control-plane-operations-for-database-or-containers"></a>データベースまたはコンテナーのコントロール プレーン操作

データベースおよびコンテナー レベルで行えるコントロール プレーン操作は、次のとおりです。 これらの操作は、Azure Monitor のメトリックとして用意されています。

* 作成された SQL Database
* 更新された SQL Database
* 更新された SQL Database のスループット
* 削除された SQL Database
* 作成された SQL コンテナー
* 更新された SQL コンテナー
* 更新された SQL コンテナーのスループット
* 削除された SQL コンテナー
* 作成された Cassandra キースペース
* 更新された Cassandra キースペース
* 更新された Cassandra キースペースのスループット
* 削除された Cassandra キースペース
* 作成された Cassandra テーブル
* 更新された Cassandra テーブル
* 更新された Cassandra テーブルのスループット
* 削除された Cassandra テーブル
* 作成された Gremlin データベース
* 更新された Gremlin データベース
* 更新された Gremlin データベースのスループット
* 削除された Gremlin データベース
* 作成された Gremlin グラフ
* 更新された Gremlin グラフ
* 更新された Gremlin グラフのスループット
* 削除された Gremlin グラフ
* 作成された Mongo データベース
* 更新された Mongo データベース
* 更新された Mongo データベースのスループット
* 削除された Mongo データベース
* 作成された Mongo コレクション
* 更新された Mongo コレクション
* 更新された Mongo コレクションのスループット
* 削除された Mongo コレクション
* 作成された AzureTable テーブル
* 更新された AzureTable テーブル
* 更新された AzureTable テーブルのスループット
* 削除された AzureTable テーブル

## <a name="diagnostic-log-operations"></a>診断ログの操作

診断ログ内のさまざまな操作の名前は、次のとおりです。

* RegionAddStart、RegionAddComplete
* RegionRemoveStart、RegionRemoveComplete
* AccountDeleteStart、AccountDeleteComplete
* RegionFailoverStart、RegionFailoverComplete
* AccountCreateStart、AccountCreateComplete
* AccountUpdateStart、AccountUpdateComplete
* VirtualNetworkDeleteStart、VirtualNetworkDeleteComplete
* DiagnosticLogUpdateStart、DiagnosticLogUpdateComplete

API 固有の操作の操作名の形式は、次のとおりです。

* ApiKind + ApiKindResourceType + OperationType
* ApiKind + ApiKindResourceType + "Throughput" + operationType

**例** 

* CassandraKeyspacesCreate
* CassandraKeyspacesUpdate
* CassandraKeyspacesThroughputUpdate
* SqlContainersUpdate

*ResourceDetails* プロパティには、リソース本文全体が要求ペイロードとして含まれ、これには更新が要求されたすべてのプロパティが含まれます。

## <a name="diagnostic-log-queries-for-control-plane-operations"></a>コントロール プレーン操作の診断ログのクエリ

次に、コントロール プレーン操作の診断ログを取得する例をいくつか示します。

```kusto
AzureDiagnostics 
| where Category startswith "ControlPlane"
| where OperationName contains "Update"
| project httpstatusCode_s, statusCode_s, OperationName, resourceDetails_s, activityId_g
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where TimeGenerated >= todatetime('2020-05-14T17:37:09.563Z')
| project TimeGenerated, OperationName, apiKind_s, apiKindResourceType_s, operationType_s, resourceDetails_s
```

```kusto
AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName startswith "SqlContainersUpdate"
```

```kusto
AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName startswith "SqlContainersThroughputUpdate"
```

activityId と、コンテナー削除操作を開始した呼び出し元を取得するクエリ。

```kusto
(AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName == "SqlContainersDelete"
| where TimeGenerated >= todatetime('9/3/2020, 5:30:29.300 PM')
| summarize by activityId_g )
| join (
AzureActivity
| parse HTTPRequest with * "clientRequestId\": \"" activityId_g "\"" * 
| summarize by Caller, HTTPRequest, activityId_g)
on activityId_g
| project Caller, activityId_g
```

インデックスまたは ttl 更新を取得するためのクエリを実行します。 その後、このクエリの出力と前の更新を比較し、インデックスまたは ttl の変更を確認できます。

```Kusto
AzureDiagnostics
| where Category =="ControlPlaneRequests"
| where  OperationName == "SqlContainersUpdate"
| project resourceDetails_s
```

**出力:**

```json
{id:skewed,indexingPolicy:{automatic:true,indexingMode:consistent,includedPaths:[{path:/*,indexes:[]}],excludedPaths:[{path:/_etag/?}],compositeIndexes:[],spatialIndexes:[]},partitionKey:{paths:[/pk],kind:Hash},defaultTtl:1000000,uniqueKeyPolicy:{uniqueKeys:[]},conflictResolutionPolicy:{mode:LastWriterWins,conflictResolutionPath:/_ts,conflictResolutionProcedure:}
```

## <a name="next-steps"></a>次のステップ

* [Azure Monitor for Azure Cosmos DB の詳細](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Azure Cosmos DB のメトリックを使用した監視とデバッグ](use-metrics.md)
