---
title: Azure Cosmos DB で日付を扱う
description: Azure Cosmos DB で DateTime オブジェクトの保存、インデックスの作成、およびクエリを行う方法について説明します。
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 04/03/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ba4d4e63bdd1e795bc1c599d0eae8a595aa0d643
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359525"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Azure Cosmos DB で日付を扱う
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB は、ネイティブの [JSON](https://www.json.org) データ モデルにより、スキーマの柔軟性と豊富なインデックス機能を提供します。 データベース、コンテナー、ドキュメント、ストアド プロシージャを含むすべての Azure Cosmos DB リソースは、JSON ドキュメントとしてモデル化されて保存されます。 ポータブルであることが求められる JSON (および Azure Cosmos DB) では、少数の基本的な型 (String、Number、Boolean、Array、Object、Null) しかサポートされません。 ただし、JSON は柔軟性が高いため、開発者およびフレームワークは、これらのプリミティブ型を使用したり、オブジェクトまたは配列として作成したりすることにより、複雑な型を表現できます。

基本の型の他に、多くのアプリケーションでは日付やタイムスタンプを表す DateTime 型が必要です。 この記事では、開発者が、.NET SDK を使用して Azure Cosmos DB で日付の格納、取得、クエリを行う方法を説明します。

## <a name="storing-datetimes"></a>DateTimes の格納

Azure Cosmos DB は、string、number、boolean、null、array、object などの JSON 型をサポートしています。 DateTime 型は直接サポートされません。 現在、Azure Cosmos DB では、日付のローカリゼーションはサポートされません。 そのため、DateTimes を文字列として格納する必要があります。 Azure Cosmos DB の DateTime 文字列に推奨される形式は、ISO 8601 UTC 標準に準拠した `yyyy-MM-ddTHH:mm:ss.fffffffZ` です。 Azure Cosmos DB のすべての日付を UTC として格納することをお勧めします。 日付文字列をこの形式に変換すると、日付を辞書式で並べ替えることができます。 UTC 以外の日付が格納されている場合は、クライアント側でロジックを処理する必要があります。 現地の DateTime を UTC に変換するには、オフセットが JSON のプロパティとして認識および格納されている必要があります。また、クライアントでオフセットを使用して UTC の DateTime 値を計算できます。

DateTime 文字列をフィルターとして使用する範囲クエリは、DateTime 文字列がすべて UTC にあり、長さが同じ場合にのみサポートされます。 Azure Cosmos DB では、[GetCurrentDateTime](sql-query-getcurrentdatetime.md) システム関数は、`yyyy-MM-ddTHH:mm:ss.fffffffZ` の形式で現在の UTC 日時 ISO 8601 文字列値を返します。

ほとんどのアプリケーションは、次に示す理由から、DateTime の既定の文字列表現を使用できます。

* 文字列は比較することができます。DateTime 値が文字列に変換されるとき、値の相対的な順序は維持されます。
* この方法では、JSON 変換のためのカスタム コードまたは属性は必要ありません。
* JSON に格納されている日付を人間が判読できます。
* この方法では、高速クエリ パフォーマンスのために Azure Cosmos DB のインデックスを利用できます。

たとえば、次のスニペットは、.NET SDK を使用して 2 つの DateTime プロパティ (`ShipDate` と `OrderDate`) を含む`Order` オブジェクトをドキュメントとして格納します。

```csharp
    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await container.CreateItemAsync(
        new Order
        {
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14),
            Total = 113.39
        });
```

このドキュメントは、Azure Cosmos DB に次のように格納されます。

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

または、DateTimes を Unix タイムスタンプ (1970 年 1 月 1 日からの経過秒数を表す数値) として格納することもできます。 Azure Cosmos DB の内部 Timestamp (`_ts`) プロパティは、この方法を使用しています。 [UnixDateTimeConverter](/dotnet/api/microsoft.azure.documents.unixdatetimeconverter) クラスを使用して、DateTime を数値としてシリアル化できます。

## <a name="querying-datetimes-in-linq"></a>LINQ での DateTime のクエリ

SQL .NET SDK は、LINQ 経由で Azure Cosmos DB に格納されたデータのクエリを自動的にサポートします。 たとえば、次のスニペットは、3 日以内に出荷された注文をフィルター処理する LINQ クエリです。

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

次の SQL ステートメントに変換され、Azure Cosmos DB で実行されます。

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2014-09-30T23:14:25.7251173Z")
```

Azure Cosmos DB の SQL クエリ言語と LINQ プロバイダーについては、[LINQ での Cosmos DB のクエリ](sql-query-linq-to-sql.md)に関する記事を参照してください。

## <a name="indexing-datetimes-for-range-queries"></a>範囲クエリ用の DateTimes のインデックス作成

クエリは DateTime 値でよく使用されます。 これらのクエリを効率的に実行するには、クエリのフィルターのプロパティにインデックスが定義されている必要があります。

インデックス作成ポリシーを構成する方法の詳細については、「[Azure Cosmos DB のインデックス作成ポリシー](index-policy.md)」を参照してください。 

## <a name="next-steps"></a>次の手順

* [GitHub のコード サンプル](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples)のダウンロードと実行
* [SQL クエリ](sql-query-getting-started.md)の詳細を確認する
* [Azure Cosmos DB のインデックス作成ポリシー](index-policy.md)の詳細を確認する