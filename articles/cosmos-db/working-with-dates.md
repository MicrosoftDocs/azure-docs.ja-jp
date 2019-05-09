---
title: Azure Cosmos DB で日付を扱う
description: Azure Cosmos DB で日付を扱う方法について説明します。
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/25/2017
ms.openlocfilehash: 77205c497b901a62bd6880512f4e780d5a6d25f2
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54044214"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Azure Cosmos DB で日付を扱う
Azure Cosmos DB は、ネイティブの [JSON](https://www.json.org) データ モデルにより、スキーマの柔軟性と豊富なインデックス機能を提供します。 データベース、コンテナー、ドキュメント、ストアド プロシージャを含むすべての Azure Cosmos DB リソースは、JSON ドキュメントとしてモデル化されて保存されます。 ポータブルであることが求められる JSON (および Azure Cosmos DB) では、少数の基本的な型 (String、Number、Boolean、Array、Object、Null) しかサポートされません。 ただし、JSON は柔軟性が高いため、開発者およびフレームワークは、これらのプリミティブ型を使用したり、オブジェクトまたは配列として作成したりすることにより、複雑な型を表現できます。 

基本の型の他に、多くのアプリケーションでは日付やタイムスタンプを表す [DateTime](https://msdn.microsoft.com/library/system.datetime(v=vs.110).aspx) 型が必要です。 この記事では、開発者が、.NET SDK を使用して Azure Cosmos DB で日付の格納、取得、クエリを行う方法を説明します。

## <a name="storing-datetimes"></a>DateTimes の格納
既定では、[Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) は DateTime 値を [ISO 8601](https://www.iso.org/iso/catalogue_detail?csnumber=40874) 文字列としてシリアル化します。 ほとんどのアプリケーションは、次に示す理由から、DateTime の既定の文字列表現を使用できます。

* 文字列は比較することができます。DateTime 値が文字列に変換されるとき、値の相対的な順序は維持されます。 
* この方法では、JSON 変換のためのカスタム コードまたは属性は必要ありません。
* JSON に格納されている日付を人間が判読できます。
* この方法では、高速クエリ パフォーマンスのために Azure Cosmos DB のインデックスを利用できます。

たとえば、次のスニペットは、.NET SDK を使用して 2 つの DateTime プロパティ (`ShipDate` と `OrderDate`) を含む`Order` オブジェクトをドキュメントとして格納します。

    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await client.CreateDocumentAsync("/dbs/orderdb/colls/orders", 
        new Order 
        { 
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });

このドキュメントは、Azure Cosmos DB に次のように格納されます。

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

または、DateTimes を Unix タイムスタンプ (1970 年 1 月 1 日からの経過秒数を表す数値) として格納することもできます。 Azure Cosmos DB の内部 Timestamp (`_ts`) プロパティは、この方法を使用しています。 [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) クラスを使用して、DateTime を数値としてシリアル化できます。 

## <a name="indexing-datetimes-for-range-queries"></a>範囲クエリ用の DateTimes のインデックス作成
範囲クエリは DateTime 値でよく使用されます。 たとえば、昨日からのすべての注文を探す必要がある場合、または 5 分以内に出荷されたすべての注文を探す必要がある場合は、範囲クエリを実行します。 このようなクエリを効率的に実行するには、文字列に対する範囲インデックス作成用のコレクションを構成する必要があります。

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

インデックス作成ポリシーを構成する方法の詳細については、「[Azure Cosmos DB のインデックス作成ポリシー](index-policy.md)」を参照してください。

## <a name="querying-datetimes-in-linq"></a>LINQ での DateTime のクエリ
SQL .NET SDK は、LINQ 経由で Azure Cosmos DB に格納されたデータのクエリを自動的にサポートします。 たとえば、次のスニペットは、3 日以内に出荷された注文をフィルター処理する LINQ クエリです。

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on Azure Cosmos DB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

Azure Cosmos DB の SQL クエリ言語と LINQ プロバイダーについては、[Cosmos DB のクエリ](how-to-sql-query.md)に関する記事を参照してください。

この記事では、Azure Cosmos DB での DateTime の格納、インデックス作成、クエリの方法について説明しました。

## <a name="next-steps"></a>次の手順
* [GitHub のコード サンプル](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)のダウンロードと実行
* [SQL クエリ](how-to-sql-query.md)の詳細を確認する
* [Azure Cosmos DB のインデックス作成ポリシー](index-policy.md)の詳細を確認する
