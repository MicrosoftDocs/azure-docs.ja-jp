---
title: アプリ内の検索ナビゲーション用ファセット フィルター - Azure Search
description: Microsoft Azure のホスト型クラウド検索サービスである Azure Search で、ユーザーのセキュリティ ID、地理的な場所、または数値で条件をフィルター処理してクエリの検索結果を減らします。
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 5/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: a2fe29cf1d7c183aa62e6b86a4b29479d1f34ff8
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649886"
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>Azure Search でファセット フィルターを作成する方法 

ファセット ナビゲーションは、検索アプリでクエリ結果の自律フィルター処理に使用されます。アプリケーションでドキュメントのグループ (カテゴリやブランドなど) を検索範囲として指定する UI コントロールを提供する場合、Azure Search はこのエクスペリエンスを支えるデータ構造を提供します。 この記事では、提供する検索エクスペリエンスを支えるファセット ナビゲーション構造の基本的な作成手順を簡単に説明します。 

> [!div class="checklist"]
> * フィルターとファセットに適したフィールドを選択する
> * フィールドに属性を設定する
> * インデックスを作成し、データを読み込む
> * クエリにファセット フィルターを追加する
> * 結果を処理する

ファセットは動的であり、クエリで返されます。 検索の応答で、結果のナビゲートに使用されるファセット カテゴリが提供されます。 ファセットの知識がない場合、次の例はファセット ナビゲーション構造を示しています。

  ![](./media/search-filters-facets/facet-nav.png)

ファセット ナビゲーションを初めて使用する場合、詳細については、 「[Azure Search でファセット ナビゲーションを実装する方法](search-faceted-navigation.md)」をご覧ください。

## <a name="choose-fields"></a>フィールドの選択

ファセットは、単一値フィールドとコレクションで計算できます。 ファセット ナビゲーションに最適なのは、カーディナリティが低い (検索コーパス内のドキュメント (色、国/地域、またはブランド名のリストなど) 全体にわたって繰り返される異なる値が少ない) フィールドです。 

ファセットは、インデックスの作成時に `facetable` 属性を `true` に設定することで、フィールドごとに有効になります。 通常、エンド ユーザーが選択したファセットに基づいて検索アプリケーションがこのようなフィールドでフィルター処理を行うことができるように、これらのフィールドの `filterable` 属性も `true` に設定する必要があります。 

REST API を使用してインデックスを作成すると、ファセット ナビゲーションで使用される可能性があるすべての[フィールドの型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)が、既定で `facetable` とマークされます。

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ 数値フィールドの型: `Edm.Int32`、`Edm.Int64`、`Edm.Double`
+ 上記の型のコレクション (例: `Collection(Edm.String)` または `Collection(Edm.Double)`)

ファセット ナビゲーションでは、`Edm.GeographyPoint` フィールドや `Collection(Edm.GeographyPoint)` フィールドを使用することはできません。 ファセットはカーディナリティが低いフィールドで最適に機能します。 地理座標の解像度が原因で、特定のデータセット内で任意の 2 つの座標セットが等しくなることはまれです。 そのため、ファセットは地理座標ではサポートされていません。 場所でファセットするには、都市フィールドまたは地域フィールドが必要です。

## <a name="set-attributes"></a>属性の設定

フィールドの使用方法を制御するインデックスの属性は、インデックス内の個々 のフィールド定義に追加されます。 次の例では、ファセットに有用な、カーディナリティが低いフィールドは、`category` (hotel、motel、hostel)、`tags`、`rating` で構成されています。 次の例では、わかりやすいように、これらのフィールドには `filterable` および `facetable` 属性が明示的に設定されています。 

> [!Tip]
> パフォーマンスとストレージの最適化のためのベスト プラクティスとして、ファセットとして使用されることのないフィールドではファセットを無効にします。 具体的には、ID や製品名などの一意の値の文字列フィールドは、ファセット ナビゲーションで誤って (無駄に) 使用されないように、`"facetable": false` に設定する必要があります。


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> このインデックス定義は、「[REST API を使用した Azure Search インデックスの作成](https://docs.microsoft.com/azure/search/search-create-index-rest-api)」からコピーされたものです。 フィールド定義の表面的な違い以外は全く同じです。 `category`、`tags`、`parkingIncluded`、`smokingAllowed`、および `rating` フィールドに `filterable` および `facetable` 属性がで明示的に追加されています。 REST API を使用する場合、実際には、`filterable` および `facetable` はこれらのフィールドでは既定で有効になります。 .NET SDK を使用する場合、これらの属性は明示的に有効にする必要があります。

## <a name="build-and-load-an-index"></a>インデックスの作成と読み込み

(言うまでもありませんが) 中間の手順として、クエリを作成する前に[インデックスを作成して設定する](https://docs.microsoft.com/azure/search/search-get-started-dotnet#1---create-index)必要があります。 ここでは、完全を期すためにこの手順に触れました。 インデックスを使用できるかどうかを判断する 1 つの方法として、[ポータル](https://portal.azure.com)でインデックスの一覧を確認します。

## <a name="add-facet-filters-to-a-query"></a>クエリへのファセット フィルターの追加

アプリケーション コードでは、有効なクエリのすべての部分 (検索式、ファセット、フィルター、スコアリング プロファイルなど、要求の作成に使用されるすべてのもの) を指定するクエリを作成します。 次の例では、宿泊施設の種類、評価、他のアメニティに基づいてファセット ナビゲーションを作成する要求を作成します。

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>クリック イベントでフィルター処理された結果を返す

エンド ユーザーがファセット値をクリックすると、クリック イベントのハンドラーはフィルター式を使用してユーザーの意図を実現します。 `$filter` ファセットの場合、"motel" というカテゴリのクリックは、この種類の宿泊施設を選択する `category` 式によって実装されます。 ユーザーが "motel" をクリックしてモーテルだけを表示するように指示すると、アプリケーションが送信する次のクエリに `$filter=category eq 'motel'` が含まれます。

次のコード スニペットでは、ユーザーがカテゴリ ファセットから値を選択した場合に、カテゴリをフィルターに追加します。

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

ユーザーが `tags` のようなコレクション フィールドのファセット値 (例: "pool" という値) をクリックすると、アプリケーションでは `$filter=tags/any(t: t eq 'pool')` というフィルター構文が使用されます。

## <a name="tips-and-workarounds"></a>ヒントと対処方法

### <a name="initialize-a-page-with-facets-in-place"></a>ファセットが設定されたページを初期化する

ファセットが設定されたページを初期化する場合は、ページの初期化の一環としてクエリを送信して、初期ファセット構造でページをシードできます。

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>フィルター処理された結果のファセット ナビゲーション構造を非同期に保持する

Azure Search のファセット ナビゲーションの課題の 1 つは、現在の結果に対してのみファセットが存在することです。 実際には、手順を遡って検索コンテンツで代替パスを探索することで、ユーザーが逆方向にナビゲートできるようにファセットの静的なセットを保持するのが一般的です。 

これは一般的なユース ケースですが、現時点ではファセット ナビゲーション構造ですぐに利用できるものではありません。 静的なファセットを必要とする開発者は、通常、結果に適用されるクエリと、ナビゲーションのためにファセットの静的リストの作成に使用されるクエリの 2 つのフィルター処理されたクエリを発行してこの制限に対処しています。

## <a name="see-also"></a>関連項目

+ [Azure Search のフィルター](search-filters.md)
+ [Index REST API の作成](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
