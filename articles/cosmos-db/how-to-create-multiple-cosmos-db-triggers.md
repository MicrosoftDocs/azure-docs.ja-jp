---
title: 複数の独立した Cosmos DB 用 Azure Functions トリガーを作成する
description: 複数の独立した Cosmos DB 用 Azure Functions トリガーを構成してイベント駆動型のアーキテクチャを作成する方法について説明します。
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/17/2019
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: 78fff48a97965f0b80456cd3e56ed1507bc784fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336692"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>複数の Cosmos DB 用 Azure Functions トリガーの作成
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、複数の Cosmos DB 用 Azure Functions トリガーが並列で動作し、変更に対して個別に対応するよう構成する方法について説明します。

:::image type="content" source="./media/change-feed-functions/multi-trigger.png" alt-text="Cosmos DB 用 Azure Functions トリガーと連携し、リース コンテナーを共有するサーバーレスのイベントベース Functions" border="false":::

## <a name="event-based-architecture-requirements"></a>イベントベースのアーキテクチャの要件

[Azure Functions](../azure-functions/functions-overview.md) を使用してサーバーレス アーキテクチャを構築する際は、サイズが大きく実行時間の長い関数ではなく、連携する小さい関数のセットを作成することを[お勧めします](../azure-functions/functions-best-practices.md#avoid-long-running-functions)。

[Cosmos DB 用 Azure Functions トリガー](./change-feed-functions.md)を使用してイベントベースのサーバーレス フローを構築する際に、特定の [Azure Cosmos コンテナー](./account-databases-containers-items.md#azure-cosmos-containers)に新しいイベントがあるたびに複数のことを実行したくなる状況に直面します。 トリガーするアクションが互いに独立している場合、理想的な解決策として、実行したい **アクションごとに 1 つの Cosmos DB 用 Azure Functions トリガーを作成** し、すべてで同一の Azure Cosmos コンテナーに対する変更をリッスンします。

## <a name="optimizing-containers-for-multiple-triggers"></a>複数のトリガーのコンテナーの最適化

Cosmos DB 用 Azure Functions トリガーの "*要件*" を考えると、状態を格納するための 2 番目のコンテナー ("*リース コンテナー*" とも呼ばれます) が必要です。 これは、Azure 関数ごとに個別のリース コンテナーが必要になるという意味でしょうか。

ここでは、次の 2 つの選択肢があります。

* **関数ごとにリース コンテナーを 1 つ** 作成する: このアプローチは、[共有スループット データベース](./set-throughput.md#set-throughput-on-a-database)を使用していない限り、追加コストにつながる可能性があります。 コンテナー レベルでの最小スループットは 400 [要求ユニット](./request-units.md)であり、リース コンテナーの場合は進行状況のチェックポイント処理と状態の維持にのみ使用されることを覚えておいてください。
* すべての関数に対して **1 つのリース コンテナーを用意して共有する**: この 2 番目の選択肢では、複数の Azure 関数で同じプロビジョニング済みのスループットを共有および使用できるようになるため、コンテナー上でプロビジョニングされた要求ユニットをより有効に活用できます。

この記事の目的は、2 番目の選択肢で作業を遂行することです。

## <a name="configuring-a-shared-leases-container"></a>共有リース コンテナーの構成

共有リース コンテナーを構成するには、トリガーに対して行う必要がある唯一の追加の構成として、`LeaseCollectionPrefix`[属性](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#attributes-and-annotations) (C# を使用している場合) または `leaseCollectionPrefix`[属性](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) (JavaScript を使用している場合) を追加します。 この属性の値は、その特定のトリガーの論理的な記述子である必要があります。

たとえば、メールを送信するトリガー、マテリアライズドビューを作成するために集計を実行するトリガー、後で分析するために変更を別のストレージに送信するトリガーという 3 つのトリガーがある場合、最初のトリガーには "emails"、2 番目のトリガーには "materialized"、3 番目のトリガーには "analytics" を `LeaseCollectionPrefix` に割り当てることができます。

重要なのは、3 つのすべてのトリガーが **同じリース コンテナー構成 (アカウント、データベース、およびコンテナー名) を使用できる** ということです。

C# の `LeaseCollectionPrefix` 属性を使用した非常に単純なコード サンプルは次のようになります。

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

[FunctionName("SendEmails")]
public static void SendEmails([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "emails")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}

[FunctionName("MaterializedViews")]
public static void MaterializedViews([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "materialized")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}
```

JavaScript の場合は、`leaseCollectionPrefix` 属性を使用して `function.json` ファイルに構成を適用できます。

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "emails"
},
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "materialized"
}
```

> [!NOTE]
> 共有リース コンテナーでプロビジョニングされた要求ユニットを常に監視してください。 これを共有する各トリガーではスループットの平均消費量が増加するため、これを使用している Azure 関数の数を増やすのに合わせてプロビジョニングされたスループットを増やす必要がある場合があります。

## <a name="next-steps"></a>次のステップ

* [Cosmos DB 用 Azure Functions トリガー](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)の全体的な構成を確認する
* すべての言語向けに拡張された[サンプルの一覧](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)を確認する。
* その他のサンプルについては、Azure Cosmos DB および Azure Functions を使用したサーバーレス レシピの [GitHub リポジトリ](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios)を参照する。
