---
title: Azure Cosmos DB でテーブル データのクエリを実行する方法
description: Azure Cosmos DB Table API アカウントに格納されているデータを、OData フィルターと LINQ クエリを使用して照会する方法について説明します。
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 05/21/2019
ms.reviewer: sngun
ms.openlocfilehash: 7dc2c00f273f327755dab52a4bda02840d911f96
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74869920"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-table-api"></a>チュートリアル:Table API を使って Azure Cosmos DB に対するクエリを実行する

Azure Cosmos DB [Table API](table-introduction.md) では、キー/値 (テーブル) データに対する OData クエリと [LINQ](https://docs.microsoft.com/rest/api/storageservices/fileservices/writing-linq-queries-against-the-table-service) クエリがサポートされます。  

この記事に含まれるタスクは次のとおりです。 

> [!div class="checklist"]
> * Table API を使用してデータのクエリを実行する

この記事のクエリは、次の `People` サンプル テーブルを使用します。

| パーティション キー | 行キー | Email | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0101 |
| Smith | Ben | Ben@contoso.com| 425-555-0102 |
| Smith | Jeff | Jeff@contoso.com| 425-555-0104 | 

Table API を使用してクエリを実行する方法の詳細については、[テーブルおよびエンティティのクエリ](https://docs.microsoft.com/rest/api/storageservices/fileservices/querying-tables-and-entities)に関するページを参照してください。 

Azure Cosmos DB が提供する Premium 機能の詳細については、[Azure Cosmos DB: Table API](table-introduction.md) に関するページや [Table API を使用した .NET での開発](tutorial-develop-table-dotnet.md)に関するページをご覧ください。 

## <a name="prerequisites"></a>前提条件

クエリを実行するには、Azure Cosmos DB アカウントがあり、コンテナーにエンティティ データがあることが必要です。 どちらもない場合には、 [5 分でできるクイックスタート](create-table-dotnet.md)か[開発者向けチュートリアル](tutorial-develop-table-dotnet.md)を実行して、アカウントを作成し、データベースにデータを設定します。

## <a name="query-on-partitionkey-and-rowkey"></a>PartitionKey と RowKey のクエリ
PartitionKey プロパティと RowKey プロパティによってエンティティの主キーが構成されるため、次のような特別な構文を使用すると、エンティティを特定できます。 

**クエリ**

```
https://<mytableendpoint>/People(PartitionKey='Harp',RowKey='Walter')  
```
**結果**

| パーティション キー | 行キー | Email | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0104 |

または、次のセクションで説明するように、これらのプロパティを `$filter` オプションに含めて指定することもできます。 キーのプロパティ名と定数値では大文字と小文字が区別されます。 PartitionKey プロパティと RowKey プロパティはいずれも文字列型です。 

## <a name="query-by-using-an-odata-filter"></a>OData フィルターを使用したクエリ
フィルター文字列を指定するときは、次のルールに注意してください。 

* OData プロトコル仕様で定義された論理演算子を使用して、プロパティと値を比較します。 プロパティは動的な値とは比較できないので注意してください。 式の片方は、定数である必要があります。 
* プロパティ名、演算子、および定数値は、URL でエンコードされた空白で区切る必要があります。 空白は URL エンコードでは `%20` となります。 
* フィルター文字列のすべての要素は大文字と小文字が区別されます。 
* フィルターで有効な結果を得るためには、定数値をプロパティと同じデータ型にする必要があります。 サポートされているプロパティ型の詳細については、 [Table サービス データ モデル](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model)に関するページを参照してください。 

OData `$filter` を使用して、PartitionKey と Email プロパティによってフィルター処理する方法を、次のサンプル クエリで示します。

**クエリ**

```
https://<mytableapi-endpoint>/People()?$filter=PartitionKey%20eq%20'Smith'%20and%20Email%20eq%20'Ben@contoso.com'
```

さまざまなデータ型のフィルター式の作成方法の詳細については、「[Querying Tables and Entities (テーブルとエンティティのクエリ)](https://docs.microsoft.com/rest/api/storageservices/querying-tables-and-entities)」をご覧ください。

**結果**

| パーティション キー | 行キー | Email | PhoneNumber |
| --- | --- | --- | --- |
| Smith |Ben | Ben@contoso.com| 425-555-0102 |

## <a name="query-by-using-linq"></a>LINQ を使用したクエリ 
LINQ を使用してクエリを実行することもできます。これは、対応する OData クエリ式に変換されます。 .NET SDK を使用してクエリを作成する方法の例を次に示します。

```csharp
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("People");

TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
    .Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("Email", QueryComparisons.Equal,"Ben@contoso.com")
    ));

await table.ExecuteQuerySegmentedAsync<CustomerEntity>(query, null);
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の手順を行いました。

> [!div class="checklist"]
> * Table API を使用してクエリを実行する方法を学習しました。

次のチュートリアルに進んで、データをグローバルに分散する方法について学習できます。

> [!div class="nextstepaction"]
> [データをグローバルに分散する](tutorial-global-distribution-table.md)
