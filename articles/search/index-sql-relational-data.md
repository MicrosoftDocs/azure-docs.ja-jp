---
title: インポートおよびインデックス作成用に SQL リレーショナル データをモデル化する
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search でインデックス作成とフルテキスト検索を行うために、フラットな結果セットに非正規化されたリレーショナル データをモデル化する方法について説明します。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6c70b42e7d0f647a3b2b60d29b5098a791e4975f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88924522"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Azure Cognitive Search でのインポートおよびインデックス作成用にリレーショナル SQL データをモデル化する方法

Azure Cognitive Search では、フラットな行セットを[インデックス作成パイプライン](search-what-is-an-index.md)への入力として受け取ります。 ソース データが SQL Server リレーショナル データベース内の結合されたテーブルに由来する場合、この記事では、結果セットを作成する方法と、Azure Cognitive Search インデックスで親子のリレーションシップをモデル化する方法について説明します。

例として、[デモ データ](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels)に基づく架空のホテル データベースについて説明します。 データベースは、50 軒のホテルを含む Hotels$ テーブルと、種類、料金、アメニティが異なる合計 750 室の客室を含む Rooms$ テーブルで構成されているとします。 テーブル間には一対多のリレーションシップがあります。 このアプローチでは、ビューにはホテルごとに 1 行の 50 行を返すクエリが表示され、各行に関連する客室の詳細が埋め込まれています。

   ![Hotels データベースのテーブルとビュー](media/index-sql-relational-data/hotels-database-tables-view.png "Hotels データベースのテーブルとビュー")


## <a name="the-problem-of-denormalized-data"></a>非正規化されたデータの問題

一対多のリレーションシップを使用する場合の課題の 1 つは、結合されたテーブルを基に作成された標準クエリでは非正規化されたデータが返されますが、Azure Cognitive Search シナリオではこれが適切に機能しないことです。 ホテルと客室を結合する次の例を考えてみましょう。

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
このクエリの結果では、すべてのホテル フィールドが返され、その後にすべての Room フィールドが続き、客室の値ごとに予備的なホテル情報が繰り返されます。

   ![部屋のフィールドが追加された場合の非正規化されたデータ、冗長なホテル データ](media/index-sql-relational-data/denormalize-data-query.png "部屋のフィールドが追加された場合の非正規化されたデータ、冗長なホテル データ")


このクエリは表面的には成功しますが (フラットな行セットですべてのデータが提供されます)、想定される検索エクスペリエンスに適したドキュメント構造が提供されません。 インデックス作成中に、Azure Cognitive Search では取り込まれた行ごとに 1 つの検索ドキュメントが作成されます。 検索ドキュメントが上記の結果のようになった場合は、Twin Dome ホテルだけで 7 つの別のドキュメントが表示されるという重複に気付くはずです。 "フロリダのホテル" に関するクエリでは、Twin Dome ホテルのみで 7 個の結果が返され、関連する他のホテルが検索結果の後の方に追いやられます。

ホテルごとに 1 つのドキュメントという想定されるエクスペリエンスを得るには、適切な粒度で、さらに完全な情報を含む行セットを指定する必要があります。 幸いなことに、この記事の手法を採用してこれを簡単に行うことができます。

## <a name="define-a-query-that-returns-embedded-json"></a>埋め込みの JSON を返すクエリを定義する

期待される検索エクスペリエンスを実現するには、データ セットは Azure Cognitive Search 内の検索ドキュメントごとに 1 行で構成されている必要があります。 この例では、ホテルごとに 1 行が必要ですが、客室の詳細情報に含まれる夜間料金、ベッドのサイズと数、またはビーチのビューなど、関心が持たれる他の客室関連のフィールドをユーザーが検索できるようにする必要もあります。

このソリューションでは、2 番目の手順に示すように、入れ子になった JSON として客室の詳細情報をキャプチャし、ビューのフィールドに JSON 構造体を挿入します。 

1. Hotels$ と Rooms$ という 2 つの結合テーブルがあるとします。これらには 50 軒のホテルと 750 の客室に関する詳細情報が含まれ、HotelID フィールドで結合されています。 これらのテーブルには、個別に 50 軒のホテルと 750 室の関連する客室が含まれています。

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

2. 入れ子になったクエリの出力を含む新しい *Rooms* フィールドを追加して、親テーブル (`SELECT * from dbo.Hotels$`) 内のすべてのフィールドで構成されるビューを作成します。 `SELECT * from dbo.Rooms$` の **FOR JSON AUTO** 句では、JSON として出力を構築します。 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   次のスクリーンショットは、一番下に *Rooms* nvarchar フィールドがある結果のビューを示しています。 *Rooms* フィールドは HotelRooms ビューにのみ存在します。

   ![HotelRooms ビュー](media/index-sql-relational-data/hotelsrooms-view.png "HoteRooms ビュー")

1. `SELECT * FROM dbo.HotelRooms` を実行して行セットを取得します。 このクエリでは、JSON コレクションとして客室情報が関連付けられた 50 行 (ホテルごとに 1 行) が返されます。 

   ![HotelRooms ビューからの行セット](media/index-sql-relational-data/hotelrooms-rowset.png "HotelRooms ビューからの行セット")

これで、この行セットを Azure Cognitive Search にインポートする準備ができました。

> [!NOTE]
> このアプローチでは、埋め込みの JSON が [SQL Server の最大列サイズ制限](/sql/sql-server/maximum-capacity-specifications-for-sql-server)を下回ることを前提としています。 

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>一対多リレーションシップの "多" の側に複雑なコレクションを使用する

Azure Cognitive Search 側で、入れ子になった JSON を使用して一対多のリレーションシップをモデル化するインデックス スキーマを作成します。 前のセクションで作成した結果セットは、通常、次に示すインデックス スキーマに対応しています (簡潔にするために一部のフィールドを省略しています)。

次の例は、[複合データ型をモデル化する方法](search-howto-complex-data-types.md#creating-complex-fields)の記事の例と似ています。 この記事で重点的に説明している *Rooms* 構造体は、*hotels* という名前のインデックスのフィールド コレクションに含まれています。 この例は、*Address* の複合型も示しています。これは、コレクションで許可されている複数の任意の数の項目ではなく、固定セットの項目で構成されている *Rooms* とは異なります。

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

前の結果セットと上記のインデックス スキーマがあれば、インデックス作成操作を成功させるために必要なすべてのコンポーネントが用意されています。 フラット化されたデータ セットはインデックス作成の要件を満たしていますが、詳細情報も保持されます。 Azure Cognitive Search インデックスでは、検索結果がホテルベースのエンティティに簡単に分類され、さらに個々の客室とその属性のコンテキストが保持されます。

## <a name="next-steps"></a>次のステップ

独自のデータ セットを使用して、[データのインポート ウィザード](search-import-data-portal.md)を使用してインデックスを作成し、読み込むことができます。 ウィザードによって、*Rooms* に含まれているものなど、埋め込み JSON コレクションが検出され、複合型コレクションを含むインデックス スキーマが推測されます。 

  ![データのインポート ウィザードによって推論されたインデックス](media/index-sql-relational-data/search-index-rooms-complex-collection.png "データのインポート ウィザードによって推論されたインデックス")

データのインポート ウィザードの基本的な手順については、次のクイックスタートを試してください。

> [!div class="nextstepaction"]
> [クイック スタート: Azure portal を使用して検索インデックスを作成する](search-get-started-portal.md)