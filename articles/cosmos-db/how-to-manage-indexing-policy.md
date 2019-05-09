---
title: Azure Cosmos DB でデータベース アカウントを管理する方法について
description: Azure Cosmos DB でデータベース アカウントを管理する方法について
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: c27cee4842c0e65e1737f100a215cff82a0fd439
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033096"
---
# <a name="manage-indexing-in-azure-cosmos-db"></a>Azure Cosmos DB でインデックス作成を管理する

Azure Cosmos DB では、コンテナーによって自動的にすべての項目にインデックスが付けられるようにするかどうかを選択できます。 既定では、Azure Cosmos コンテナー内のすべての項目に自動的にインデックスが付けられますが、自動インデックス作成を無効にすることもできます。 インデックス作成が無効になっている場合、項目にはそれらのセルフリンクを通じて、または項目の ID (ドキュメント ID など) を使用したクエリを通じて、アクセスすることができます。REST API で **x-ms-documentdb-enable-scan** ヘッダーを渡すか、.NET SDK を使用して **EnableScanInQuery** 要求オプションを渡せば、インデックスを使用しなくても、結果を提供するように明示的に要求することができます。

自動インデックス作成が無効になっている場合でも、特定の項目を選択してインデックスに追加できます。 反対に、自動インデックス作成を有効にしたまま、特定の項目を選択して除外できます。 インデックス作成を有効または無効にする構成は、項目のサブセットにクエリを実行する必要がある場合に役立ちます。  

書き込みスループットと要求ユニットは、インデックス付けが必要な値の数に比例しますが、その数はインデックス作成ポリシーに含まれるセットによって指定されます。 クエリ パターンをよく理解している場合は、パスの包含/除外サブセットを明示的に選択して、書き込みスループットを向上させることができます。

## <a name="manage-indexing-using-azure-portal"></a>Azure portal を使用してインデックス作成を管理する

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. 新しい Azure Cosmos アカウントを作成するか、既存のアカウントを選択します。

3. **[データ エクスプローラー]** ウィンドウを開きます。

4. 既存のコンテナーを選択し、展開して以下の値を変更します。

   * **[Scale & Settings]\(スケールと設定\)** ウィンドウを開きます。
   * **indexingMode** を *consistent* から *none* に変更するか、インデックス作成で特定のパスを包含/除外します。
   * **[OK]** をクリックして変更を保存します。

   ![Azure portal を使用してインデックス作成を管理する](./media/how-to-manage-indexing/how-to-manage-indexing-portal.png)

## <a name="manage-indexing-using-azure-sdks"></a>Azure SDK を使用してインデックス作成を管理する

### <a id="dotnet"></a>.NET SDK

次の例は、[SQL API .NET SDK](sql-api-sdk-dotnet.md) と [RequestOptions.IndexingDirective](/dotnet/api/microsoft.azure.documents.client.requestoptions.indexingdirective) プロパティを使用して、項目を明示的に含める方法を示しています。

```csharp
// To override the default behavior to exclude (or include) a document in indexing,
// use the RequestOptions.IndexingDirective property.
client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new { id = "myDocumentId", isRegistered = true },
    new RequestOptions { IndexingDirective = IndexingDirective.Include });
```

## <a name="next-steps"></a>次の手順

以下の記事で、インデックス作成についての詳細を参照してください。

* [インデックス作成の概要](index-overview.md)
* [インデックス作成ポリシー](index-policy.md)
* [インデックスの種類](index-types.md)
* [インデックスのパス](index-paths.md)
