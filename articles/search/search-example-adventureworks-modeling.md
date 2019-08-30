---
title: 例:AdventureWorks の在庫データベースをモデル化する - Azure Search
description: リレーショナル データをモデル化し、フラット化されたデータ セットに変換して、Azure Search のインデックス作成と全文検索に利用する方法について説明します。
author: cstone
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: chstone
ms.openlocfilehash: 52ccf3edfca5b3481b038bd5d3449c1dd6354179
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649915"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-search"></a>例:AdventureWorks の在庫データベースを Azure Search 用にモデル化する

構造化データベースのコンテンツをモデル化して効率的な検索インデックスを作成する作業を簡単に行うことができるのはごくまれです。 スケジューリングと変更管理は別として、ソース行を (そのテーブル結合状態から離れて) 検索しやすいエンティティに非正規化するという課題が存在します。 この記事では、オンラインで入手可能な AdventureWorks サンプル データを使用して、データベースから検索への切り替えにおける一般的なエクスペリエンスを中心に説明します。 

## <a name="about-adventureworks"></a>AdventureWorks について

SQL Server インスタンスがある場合は、AdventureWorks サンプル データベースを使い慣れているかもしれません。 このデータベースに含まれるテーブルには、製品情報を公開する 5 つのテーブルがあります。

+ **ProductModel**: 名前
+ **Product**: 名前、色、コスト、サイズ、重量、イメージ、カテゴリ (各行は特定の ProductModel に結合)
+ **ProductDescription**: 説明
+ **ProductModelProductDescription**: ロケール (各行は ProductModel を特定の言語の特定の ProductDescription に結合)
+ **ProductCategory**: 名前、親カテゴリ

このすべてのデータを、検索インデックスに取り込み可能なフラット化行セットにまとめることが目下のタスクです。 

## <a name="considering-our-options"></a>オプションの検討

単純なアプローチとしては、(必要に応じて結合された) Product テーブルのすべての行にインデックスを付けます。これは、最も明確な情報が Product テーブルに含まれているためです。 ただし、このアプローチでは検索インデックスが結果セット内で認識された重複に公開されます。 たとえば、Road-650 モデルには 2 つの色と 6 つのサイズが用意されています。 "ロード バイク" に対するクエリは、サイズと色のみで区別される、同じモデルの 12 個のインスタンスで占められます。 他の 6 つのロード固有のモデルは、検索の地獄と言える 2 ページ目に格下げされます。

  ![製品リスト](./media/search-example-adventureworks/products-list.png "製品リスト")
 
Road-650 モデルには 12 個のオプションがあることに注意してください。 一対多のエンティティ行は、検索インデックスでは複数値フィールドまたは事前集計値フィールドとして表されます。

この問題の解決は、ProductModel テーブルにターゲット インデックスを移動するという単純なものではありません。 そうすることで、検索結果に表す必要のある Product テーブル内の重要なデータが無視されます。

## <a name="use-a-collection-data-type"></a>Collection データ型を使用する

"正しいアプローチ" は、データベース モデルに直接的な並列がない次の検索スキーマ機能を利用することです。**Collection(Edm.String)** 。 Collection データ型は、非常に長い (単一の) 文字列ではなく個々の文字列のリストがある場合に使用されます。 タグまたはキーワードがある場合は、Collection データ型をそのフィールドに使用します。

"色"、"サイズ"、および "イメージ" に対して **Collection(Edm.String)** の複数値のインデックス フィールドを定義することで、重複エントリを含むインデックスを汚染せずにファセットとフィルターを行うための補助的な情報が保持されます。 同様に、集計関数を Product の数値フィールドに適用し、すべての製品の **listPrice** ではなく **minListPrice** にインデックスを付けます。

このような構造を持つインデックスを考慮すると、"マウンテン バイク" の検索では別個の自転車モデルが表示されますが、色、サイズ、最安値などの重要なメタデータは維持されます。 次のスクリーンショットはその例を示しています。

  ![マウンテン バイクの検索例](./media/search-example-adventureworks/mountain-bikes-visual.png "マウンテン バイクの検索例")

## <a name="use-script-for-data-manipulation"></a>スクリプトをデータ操作に使用する

残念ながら、SQL ステートメントだけを使用してこの種類のモデリングを簡単に行うことはできません。 その代わりに、シンプルな NodeJS スクリプトを使用してデータを読み込み、検索しやすい JSON エンティティにマップします。

最終的なデータベース-検索のマッピングは次のようになります。

+ model (Edm.String: searchable, filterable, retrievable): "ProductModel.Name" から
+ description_en (Edm.String: searchable): culture=’en’ であるモデルの "ProductDescription" から
+ color (Collection(Edm.String): searchable, filterable, facetable, retrievable): モデルの "Product.Color" からの一意の値
+ size (Collection(Edm.String): searchable, filterable, facetable, retrievable): モデルの "Product.Size" からの一意の値
+ image (Collection(Edm.String): retrievable): モデルの "Product.ThumbnailPhoto" からの一意の値
+ minStandardCost (Edm.Double: filterable, facetable, sortable, retrievable): モデルのすべての "Product.StandardCost" の集計の最小値
+ minListPrice (Edm.Double: filterable, facetable, sortable, retrievable): モデルのすべての "Product.ListPrice" の集計の最小値
+ minWeight (Edm.Double: filterable, facetable, sortable, retrievable): モデルのすべての "Product.Weight" の集計の最小値
+ products (Collection(Edm.String): searchable, filterable, retrievable): モデルの "Product.Name" からの一意の値

ProductModel テーブルと Product および ProductDescription を結合したら、[lodash](https://lodash.com/) (C# の場合は Linq) を使用して結果セットをすぐに変換します。

```javascript
var records = queryYourDatabase();
var models = _(records)
  .groupBy('ModelName')
  .values()
  .map(function(d) {
    return {
      model: _.first(d).ModelName,
      description: _.first(d).Description,
      colors: _(d).pluck('Color').uniq().compact().value(),
      products: _(d).pluck('ProductName').uniq().compact().value(),
      sizes: _(d).pluck('Size').uniq().compact().value(),
      images: _(d).pluck('ThumbnailPhotoFilename').uniq().compact().value(),
      minStandardCost: _(d).pluck('StandardCost').min(),
      maxStandardCost: _(d).pluck('StandardCost').max(),
      minListPrice: _(d).pluck('ListPrice').min(),
      maxListPrice: _(d).pluck('ListPrice').max(),
      minWeight: _(d).pluck('Weight').min(),
      maxWeight: _(d).pluck('Weight').max(),
    };
  })
  .value();
```

結果の JSON は次のようになります。

```json
[
  {
    "model": "HL Road Frame",
    "colors": [
      "Black",
      "Red"
    ],
    "products": [
      "HL Road Frame - Black, 58",
      "HL Road Frame - Red, 58",
      "HL Road Frame - Red, 62",
      "HL Road Frame - Red, 44",
      "HL Road Frame - Red, 48",
      "HL Road Frame - Red, 52",
      "HL Road Frame - Red, 56",
      "HL Road Frame - Black, 62",
      "HL Road Frame - Black, 44",
      "HL Road Frame - Black, 48",
      "HL Road Frame - Black, 52"
    ],
    "sizes": [
      "58",
      "62",
      "44",
      "48",
      "52",
      "56"
    ],
    "images": [
      "no_image_available_small.gif"
    ],
    "minStandardCost": 868.6342,
    "maxStandardCost": 1059.31,
    "minListPrice": 1431.5,
    "maxListPrice": 1431.5,
    "minWeight": 961.61,
    "maxWeight": 1043.26
  }
]
```

最後に、初期のレコードセットを返す SQL クエリを示します。 [mssql](https://www.npmjs.com/package/mssql) npm モジュールを使用してデータを NodeJS アプリに読み込みました。

```T-SQL
SELECT
  m.Name as ModelName,
  d.Description,
  p.Name as ProductName,
  p.*
FROM 
  SalesLT.ProductModel m
INNER JOIN 
  SalesLT.ProductModelProductDescription md
  ON m.ProductModelId = md.ProductModelId
INNER JOIN 
  SalesLT.ProductDescription d
  ON md.ProductDescriptionId = d.ProductDescriptionId
LEFT JOIN 
  SalesLT.product p
  ON m.ProductModelId = p.ProductModelId
WHERE
  md.Culture='en'
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [例:Azure Search の多層構造ファセットの分類](search-example-adventureworks-multilevel-faceting.md)


