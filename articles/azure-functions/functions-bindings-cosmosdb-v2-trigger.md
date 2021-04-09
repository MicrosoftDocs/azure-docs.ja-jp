---
title: Functions 2.x 以降での Azure Cosmos DB のトリガー
description: Azure Functions で Azure Cosmos DB のトリガーを使用する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 6f4e43efeb1882f52bd335d83a3660a94040ab8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729217"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x-and-higher"></a>Azure Functions 2.x 以降での Azure Cosmos DB のトリガー

Azure Cosmos DB のトリガーは [Azure Cosmos DB 変更フィード](../cosmos-db/change-feed.md)を使用して、パーティション間の挿入と更新をリッスンします。 変更フィードは、削除ではなく挿入と更新を発行します。

セットアップと構成の詳細については、[概要](./functions-bindings-cosmosdb-v2.md)に関するページをご覧ください。

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

次の例では、指定されたデータベースとコレクション内に挿入または更新がある場合に呼び出される [C# 関数](functions-dotnet-class-library.md)を示します。

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイルの Cosmos DB トリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、Cosmos DB レコードが追加または変更されるとログ メッセージを書き込みます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

C# スクリプト コードを次に示します。

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

# <a name="java"></a>[Java](#tab/java)

指定されたデータベースとコレクション内に挿入または更新がある場合に、この関数が呼び出されます。

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、その値が Cosmos DB に由来するパラメーター上で `@CosmosDBTrigger` 注釈を使用します。  この注釈は、Java のネイティブ型、POJO、または `Optional<T>` を使用した null 許容値で使用できます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルの Cosmos DB トリガー バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、Cosmos DB レコードが追加または変更されるとログ メッセージを書き込みます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

JavaScript コードを次に示します。

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

次の例は、Cosmos DB でデータの変化に応じて関数を実行する方法を示しています。

```json
{
  "type": "cosmosDBTrigger",
  "name": "Documents",
  "direction": "in",
  "leaseCollectionName": "leases",
  "connectionStringSetting": "MyStorageConnectionAppSetting",
  "databaseName": "Tasks",
  "collectionName": "Items",
  "createLeaseCollectionIfNotExists": true
}
```

_run.ps1_ ファイルで、`$Documents` パラメーターを使用して関数をトリガーするドキュメントにアクセスできます。

```powershell
param($Documents, $TriggerMetadata) 

Write-Host "First document Id modified : $($Documents[0].id)" 
```

# <a name="python"></a>[Python](#tab/python)

次の例は、*function.json* ファイルの Cosmos DB トリガー バインドと、そのバインドが使用される [Python 関数](functions-reference-python.md)を示しています。 この関数は、Cosmos DB レコードが変更されるとログ メッセージを書き込みます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Python コードを次に示します。

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) 属性を使用します。

この属性のコンストラクターにはデータベース名とコレクションを指定します。 これらの設定や構成できる他のプロパティについて詳しくは、「[トリガー - 構成](#configuration)」をご覧ください。 メソッド シグネチャでの `CosmosDBTrigger` 属性の例を次に示します。

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run([CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
        IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

完全な例については、「[トリガー](#example)」を参照してください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)から、`@CosmosDBInput` 注釈を Cosmos DB からデータを読み取るパラメーターに使用します。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

属性は、PowerShell ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

---

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `CosmosDBTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | `cosmosDBTrigger` に設定する必要があります。 |
|**direction** | 該当なし | `in` に設定する必要があります。 このパラメーターは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし | 変更されるドキュメントの一覧を表す、関数コードで使用する変数の名前。 |
|**connectionStringSetting**|**ConnectionStringSetting** | 監視されている Azure Cosmos DB アカウントに接続するために使用される接続文字列を含めたアプリ設定の名前。 |
|**databaseName**|**DatabaseName**  | 監視されているコレクションを使用する Azure Cosmos DB データベースの名前。 |
|**collectionName** |**CollectionName** | 監視されているコレクションの名前。 |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (省略可能) リース コレクションを保持する Azure Cosmos DB アカウントへの接続文字列を含むアプリ設定の名前。 この値を設定しない場合、`connectionStringSetting` という値が使用されます。 このパラメーターは、ポータルでバインドが作成されるときに自動で設定されます。 リース コレクションの接続文字列には書き込み権限が必要です。|
|**leaseDatabaseName** |**LeaseDatabaseName** | (省略可能) リースの保存のために使用するコレクションを保持しているデータベースの名前。 この値を設定しない場合、`databaseName` の設定の値が使用されます。 このパラメーターは、ポータルでバインドが作成されるときに自動で設定されます。 |
|**leaseCollectionName** | **LeaseCollectionName** | (省略可能) リースの保存のために使用するコレクションの名前。 この値を設定しない場合、`leases` という値が使用されます。 |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (省略可能) `true` を設定すると、リース コレクションが存在していない場合に自動でリース コレクションを作成します。 既定値は `false` です。 |
|**leasesCollectionThroughput**| **leasesCollectionThroughput**| (省略可能) リース コレクションの作成に割り当てる要求ユニットの数を定義します。 この設定は、`createLeaseCollectionIfNotExists` が `true` に設定されている場合のみ使用できます。 このパラメーターは、ポータルでバインドが作成されるときに自動で設定されます。
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| (省略可能) 設定すると、この関数のリース コレクションで作成されたリースへのプレフィックスとして値が追加されます。 プレフィックスを使用すると、異なるプレフィックスを使用して、2 つの別の Azure Functions が同じリース コレクションを共有できるようになります。
|**feedPollDelay**| **FeedPollDelay**| (省略可能) 現在のすべての変更がドレインされた後、フィードの新しい変更についてパーティションをポーリングする間の遅延時間 (ミリ秒)。 既定値は 5,000 ミリ秒 (5 秒) です。
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (省略可能) 設定すると、パーティションが既知のホスト インスタンス間で均等に分散されているかどうかを計算するタスクを開始する間隔がミリ秒単位で定義されます。 既定値は 13,000 (13 秒) です。
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (省略可能) 設定すると、パーティションを表すリースでリースを取得する間隔がミリ秒単位で定義されます。 この間隔内にリースが更新されない場合、リースは期限切れとなり、パーティションの所有権は別のインスタンスに移動します。 既定値は 60,000 (60 秒) です。
|**leaseRenewInterval**| **LeaseRenewInterval**| (省略可能) 設定すると、インスタンスが現在保持しているパーティションのすべてのリースの更新間隔がミリ秒単位で定義されます。 既定値は 17,000 (17 秒) です。
|**checkpointFrequency**| **CheckpointFrequency**| (省略可能) 設定すると、リース チェックポイントの間隔がミリ秒単位で定義されます。 既定値は、常に各関数呼び出しの後です。
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| (省略可能) 設定すると、関数呼び出しごとに、受信するアイテムの最大数がこのプロパティによって設定されます。 監視対象のコレクションでストアド プロシージャによって操作が実行されると、変更フィードから項目が読み取られるとき、[トランザクション スコープ](../cosmos-db/stored-procedures-triggers-udfs.md#transactions)が保持されます。 その結果、受信した項目数が指定した値よりも多くなり、同じトランザクションで変更された項目が 1 つのアトミック バッチの一部として返される可能性があります。
|**startFromBeginning**| **StartFromBeginning**| (省略可能) このオプションを指定すると、現在の時刻から開始するのではなく、コレクションの変更履歴の先頭から変更を読み取るようにトリガーに指示できます。 以降の実行ではチェックポイントが既に保存されているため、先頭からの読み取りが機能するのは、トリガーが初めて開始されたときのみです。 既にリースが作成されているときにこのオプションを `true` に設定しても効果はありません。 |
|**preferredLocations**| **PreferredLocations**| (省略可能) Azure Cosmos DB サービスの geo レプリケートされたデータベース アカウントの優先される場所 (リージョン) を定義します。 複数の値はコンマで区切る必要があります。 たとえば、"East US,South Central US,North Europe" などです。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用法

トリガーは、パーティションに _リース_ を保存するために使用する 2 つ目のコレクションを必要とします。 トリガーが機能するためには、監視されるコレクションと、リースを含むコレクションの両方が使用できる必要があります。

>[!IMPORTANT]
> 複数の関数が同じコレクションの Cosmos DB トリガーを使用するように構成している場合、各関数では専用のリース コレクションを使用するか、または各関数に対して異なる `LeaseCollectionPrefix`を指定する必要があります。 専用のリース コレクションを使用していない場合は、関数のうちの 1 つしかトリガーされません。 プレフィックスの詳細については、「[構成](#configuration)」セクションを参照してください。

トリガーは、ドキュメントが更新または挿入されたかどうかを示すわけではなく、単にドキュメント自体を提供します。 更新と挿入を別の方法で処理する必要がある場合は、挿入または更新のタイムスタンプを実装することでこれを実現できます。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB ドキュメントを読み込む (入力バインド)](./functions-bindings-cosmosdb-v2-input.md)
- [Azure Cosmos DB ドキュメントに変更を保存する (出力バインド)](./functions-bindings-cosmosdb-v2-output.md)
