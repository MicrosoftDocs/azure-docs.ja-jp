---
title: "Azure DocumentDB で日付を扱う | Microsoft Docs"
description: "Azure DocumentDB で日付を扱う方法について説明します。"
services: documentdb
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: e587772f-ce9f-498c-a017-a51e7265bb23
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: c18d17d40dff658a8fc47ef2126dd2c21b68606a
ms.lasthandoff: 03/29/2017


---
# <a name="working-with-dates-in-azure-documentdb"></a>Azure DocumentDB で日付を扱う
DocumentDB は、ネイティブの [JSON](http://www.json.org) データ モデルにより、スキーマの柔軟性と豊富なインデックス機能を提供します。 データベース、コレクション、ドキュメント、ストアド プロシージャを含むすべての DocumentDB リソースは、JSON ドキュメントとしてモデル化されて保存されます。 ポータブルであることが求められる JSON (および DocumentDB) では、少数の基本的な型 (String、Number、Boolean、Array、Object、Null) しかサポートされません。 ただし、JSON は柔軟性が高いため、開発者およびフレームワークは、これらのプリミティブ型を使用したり、オブジェクトまたは配列として作成したりすることにより、複雑な型を表現できます。 

基本の型の他に、多くのアプリケーションでは日付やタイムスタンプを表す [DateTime](https://msdn.microsoft.com/library/system.datetime(v=vs.110).aspx) 型が必要です。 この記事では、開発者が、.NET SDK を使用して DocumentDB で日付の格納、取得、クエリを行う方法を説明します。

## <a name="storing-datetimes"></a>DateTimes の格納
既定では、[DocumentDB SDK](documentdb-sdk-dotnet.md) は DateTime 値を [ISO 8601](http://www.iso.org/iso/catalogue_detail?csnumber=40874) 文字列としてシリアル化します。 ほとんどのアプリケーションは、次に示す理由から、DateTime の既定の文字列表現を使用できます。

* 文字列は比較することができます。DateTime 値が文字列に変換されるとき、値の相対的な順序は維持されます。 
* この方法では、JSON 変換のためのカスタム コードまたは属性は必要ありません。
* JSON に格納されている日付を人間が判読できます。
* この方法では、高速クエリ パフォーマンスのために DocumentDB のインデックスを利用できます。

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

このドキュメントは DocumentDB に次のように格納されます。

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

または、DateTimes を Unix タイムスタンプ (1970 年 1 月 1 日からの経過秒数を表す数値) として格納することもできます。 DocumentDB の内部 Timestamp (`_ts`) プロパティはこの方法を使用しています。 [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) クラスを使用して、DateTime を数値としてシリアル化できます。 

## <a name="indexing-datetimes-for-range-queries"></a>範囲クエリ用の DateTimes のインデックス作成
範囲クエリは DateTime 値でよく使用されます。 たとえば、昨日からのすべての注文を探す必要がある場合、または 5 分以内に出荷されたすべての注文を探す必要がある場合は、範囲クエリを実行します。 このようなクエリを効率的に実行するには、文字列に対する範囲インデックス作成用のコレクションを構成する必要があります。

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

インデックス作成ポリシーを構成する方法の詳細については、「[DocumentDB インデックス 作成ポリシー](documentdb-indexing-policies.md)」を参照してください。

## <a name="querying-datetimes-in-linq"></a>LINQ での DateTime のクエリ
DocumentDB .NET SDK は、LINQ 経由で DocumentDB に格納されたデータのクエリを自動的にサポートします。 たとえば、次のスニペットは、3 日以内に出荷された注文をフィルター処理する LINQ クエリです。

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on DocumentDB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

DocumentDB の SQL 言語と LINQ プロバイダーについては、[DocumentDB のクエリ](documentdb-sql-query.md)に関する記事を参照してください。

この記事では、DocumentDB での DateTime の格納、インデックス作成、クエリの方法について説明しました。

## <a name="next-steps"></a>次のステップ
* [GitHub のコード サンプル](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)のダウンロードと実行
* [DocumentDB クエリ](documentdb-sql-query.md)
* [DocumentDB のインデックス作成ポリシー](documentdb-indexing-policies.md)

