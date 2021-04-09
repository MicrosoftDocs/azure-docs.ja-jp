---
title: 複合データ型をモデル化する方法
titleSuffix: Azure Cognitive Search
description: 階層または入れ子になったデータ構造は、ComplexType とCollections データ型を使用して、Azure Cognitive Search インデックスでモデル化できます。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/27/2020
ms.openlocfilehash: b0b2dd9904682121c83b22b9029097e7ee57fb11
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96303760"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Azure Cognitive Search で複合データ型をモデル化する方法

Azure Cognitive Search インデックスの作成に使われる外部データセットは、さまざまな形状をしている可能性があります。 場合によっては、階層や入れ子の下部構造が含まれます。 たとえば、単一の顧客に複数の住所が含まれるケース、単一の SKU に複数の色とサイズが含まれるケース、1 冊の書籍に複数の著者が存在するケースなどが挙げられます。 モデリングの用語では、このような構造は *complex* (複合)、*compound* (複合)、*composite* (複合)、または *aggregate* (集約) データ型と呼ばれることがあります。 Azure Cognitive Search では、この概念に対して **複合型** という単語が使われます。 Azure Cognitive Search では、複合型は **複合フィールド** を使ってモデル化されます。 複合フィールドは、他の複合型も含めて任意のデータ型を使用できる子 (サブフィールド) が含まれるフィールドです。 これは、プログラミング言語の構造化されたデータ型と同様の方法で機能します。

複合フィールドでは、データ型に応じて、ドキュメント内の 1 つのオブジェクトまたはオブジェクトの配列が表されます。 `Edm.ComplexType` 型のフィールドでは単一のオブジェクトが表され、`Collection(Edm.ComplexType)` 型のフィールドではオブジェクトの配列が表されます。

Azure Cognitive Search は、複合型とコレクションをネイティブでサポートしています。 これらの型を使うと、Azure Cognitive Search インデックス内のほとんどすべての JSON 構造をモデル化できます。 Azure Cognitive Search API の以前のバージョンでは、フラット化された行セットのみをインポートできました。 最新のバージョンでは、インデックスはソース データにより密接に調和できるようになりました。 つまり、ソース データに複合型がある場合、インデックスも複合型を持つことができます。

Azure portal の **データのインポート** ウィザードで読み込むことができる [Hotels データ セット](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)から始めることをお勧めします。 ウィザードでは、ソース内の複合型が検出され、検出された構造に基づいてインデックス スキーマが提案されます。

> [!Note]
> 複合型のサポートは、`api-version=2019-05-06` から一般提供されています。 
>
> 検索ソリューションがコレクション内でフラット化されたデータセットの以前の回避策に基づいている場合は、インデックスを変更して、最新の API バージョンでサポートされている複合型を含めることをお勧めします。 API バージョンのアップグレードの詳細については、[最新の REST API バージョンへのアップグレード](search-api-migration.md)または[最新の .NET SDK バージョンへのアップグレード](search-dotnet-sdk-migration-version-9.md)に関する記事を参照してください。

## <a name="example-of-a-complex-structure"></a>複合構造の例

次の JSON ドキュメントは、単純フィールドと複合フィールドで構成されています。 `Address` や `Rooms` などの複合フィールドには、サブフィールドがあります。 `Address` はドキュメント内の単一オブジェクトなので、サブフィールドには単一の値のセットがあります。 対照的に、`Rooms` のサブフィールドには、コレクション内の各オブジェクトに 1 つずつ、複数の値のセットがあります。


```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Tags": ["Free wifi", "on-site parking", "indoor pool", "continental breakfast"]
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "RoomNumber": 1105,
      "BaseRate": 96.99,
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99,
    }
    . . .
  ]
}
```

## <a name="indexing-complex-types"></a>複合型のインデックス作成

インデックス作成時には、1 つのドキュメント内のすべての複合コレクションに対して最大 3,000 個の要素を持つことができます。 複合コレクションの 1 つの要素は、そのコレクションのメンバーです。そのため、部屋 (ホテルの例では唯一の複合コレクション) の場合は、各部屋が 1 つの要素となります。 上の例では、"Secret Point Motel" に 500 室の部屋がある場合、ホテルのドキュメントには 500 の部屋要素が含まれることになります。 入れ子になった複合コレクションでは、外側 (親) の要素に加えて、入れ子になった各要素もカウントされます。

この制限は、複合型 (アドレスなど) や文字列コレクション (タグなど) ではなく、複合コレクションにのみ適用されます。

## <a name="creating-complex-fields"></a>複合フィールドの作成

他のインデックス定義と同様に、ポータル、[REST API](/rest/api/searchservice/create-index)、または [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindex) を使用して、複合型を含むスキーマを作成できます。 

次の例は、単純フィールド、コレクション、および複合型を含む JSON インデックス スキーマを示しています。 最上位レベルのフィールドと同様に、複合型内の各サブフィールドには型があり、場合によっては属性がある点に注意してください。 スキーマは、上記のデータ例に対応しています。 `Address` はコレクションではない複合フィールドです (1 つのホテルには 1 つの住所があります)。 `Rooms` は複合コレクション フィールドです (1 つのホテルには多くの部屋があります)。

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
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
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>複合フィールドの更新

一般にフィールドに適用されるすべての[再インデックス作成規則](search-howto-reindex.md)は、複合フィールドにも適用されます。 ここで、主な規則をいくつか言い換えてみます。フィールドの複合型への追加にインデックスの再構築は必要ありませんが、修正する場合には、ほとんどの場合で必要となります。

### <a name="structural-updates-to-the-definition"></a>定義に対する構造的な更新

新しいサブフィールドは、インデックスを再構築することなく、いつでも複合フィールドに追加できます。 たとえば、インデックスに最上位レベルのフィールドを追加する場合と同様に、"ZipCode" を `Address` に、"Amenities" を `Rooms` に追加することができます。 既存のドキュメントでは、データを更新してそれらのフィールドに明示的にデータを入力するまで、新しいフィールドの値は null です。

最上位レベルのフィールドと同様に、複合型内の各サブフィールドには型があり、場合によっては属性がある点に注意してください

### <a name="data-updates"></a>データの更新

`upload` アクションを使用してインデックス内の既存のドキュメントを更新する処理は、複合フィールドと単純フィールドで同じように機能し、すべてのフィールドが置き換えられます。 ただし、`merge` (または既存のドキュメントに適用された場合は `mergeOrUpload`) は、すべてのフィールドで同じようには機能しません。 具体的には、`merge` ではコレクション内の要素のマージがサポートされていません。 この制限は、プリミティブ型のコレクションと複合コレクションに対して存在します。 コレクションを更新するには、完全なコレクション値を取得し、変更して、新しいコレクションを Index API 要求に含める必要があります。

## <a name="searching-complex-fields"></a>複合フィールドの検索

自由形式の検索式は、複合型では期待どおりに機能します。 ドキュメント内の任意の場所にある検索可能なフィールドまたはサブフィールドが一致すると、そのドキュメント自体が一致します。

複数の用語と演算子があり、[Lucene 構文](query-lucene-syntax.md)で可能なように、一部の用語にフィールド名を指定すると、より細かい表現のクエリにすることができます。 たとえば、このクエリは、"Portland" と "OR" という 2 つの用語を Address フィールドの 2 つのサブフィールドに対して照合を試みます。

> `search=Address/City:Portland AND Address/State:OR`

このようなクエリは、フィルターとは異なり、フルテキスト検索では "*非相関*" です。 フィルターでは、複合コレクションのサブフィールドに対するクエリを、[`any` または `all`](search-query-odata-collection-operators.md) の範囲変数を使って相関させることができます。 上の Lucene クエリでは、"Portland, Maine" および "Portland, Oregon" の両方と共に Oregon の他の都市を含むドキュメントが返されます。 このようになるのは、各句がドキュメント全体のそのフィールドのすべての値に適用され、"現在のサブ ドキュメント" という概念がないためです。 これについて詳しくは、[Azure Cognitive Search での OData コレクション フィルター](search-query-understand-collection-filters.md)に関する記事をご覧ください。

## <a name="selecting-complex-fields"></a>複合フィールドの選択

`$select` パラメーターは、検索結果に返すフィールドを選択するために使用されます。 このパラメーターを使用して複合フィールドの特定のサブフィールドを選択するには、スラッシュ (`/`) で区切った親フィールドとサブフィールドを含めます。

> `$select=HotelName, Address/City, Rooms/BaseRate`

検索結果に必要な場合は、インデックス内のフィールドを Retrievable とマークする必要があります。 `$select` ステートメントでは Retrievable とマークされたフィールドのみを使用できます。

## <a name="filter-facet-and-sort-complex-fields"></a>複合フィールドのフィルター処理、ファセット、並べ替え

検索のフィルター処理とフィールド検索に使用されるものと同じ [OData パス構文](query-odata-filter-orderby-syntax.md)を、検索要求内のフィールドのファセット、並べ替え、および選択にも使用できます。 複合型の場合、sortable または facetable とマークできるサブフィールド管理する規則が適用されます。 これらの規則について詳しくは、[Create Index API reference (Create Index API リファレンス)](/rest/api/searchservice/create-index) に関する記事をご覧ください。

### <a name="faceting-sub-fields"></a>サブフィールドのファセット

型が `Edm.GeographyPoint` または `Collection(Edm.GeographyPoint)` でない限り、すべてのサブフィールドは facetable とマークすることができます。

ファセットの結果で返されるドキュメント数は、複合コレクション (部屋) 内のサブドキュメントではなく、親ドキュメント (ホテル) に対して計算されます。 たとえば、ホテルに 20 室の型 "suite" があるとします。 facet パラメーターを `facet=Rooms/Type` とすると、ファセット数は、部屋に対する 20 ではなく、ホテルに対する 1 になります。

### <a name="sorting-complex-fields"></a>複合フィールドの並べ替え

並べ替え操作は、サブドキュメント (Rooms) ではなく、ドキュメント (Hotels) に適用されます。 Rooms のような複合型のコレクションがある場合は、Rooms では並べ替えることがまったくできないことを認識することが重要です。 実際のところ、どのコレクションでも並べ替えることはできません。

並べ替え操作は、フィールドが単純フィールドでも、または複合型のサブフィールドでも、フィールドがドキュメントごとに単一値の場合に機能します。 たとえば、ホテルごとに住所は 1 つだけなので `Address/City` は並べ替え可能であり、`$orderby=Address/City` では都市別にホテルが並べ替えられます。

### <a name="filtering-on-complex-fields"></a>複合フィールドでのフィルター処理

フィルター式で複合フィールドのサブフィールドを参照することができます。 フィールドのファセット、並べ替え、選択に使われるのと同じ [OData パス構文](query-odata-filter-orderby-syntax.md)を使うだけです。 たとえば、次のフィルターではカナダのすべてのホテルが返されます。

> `$filter=Address/Country eq 'Canada'`

複合コレクションのフィールドでフィルター処理するには、**ラムダ式** と [`any` および `all` 演算子](search-query-odata-collection-operators.md)を使うことができます。 その場合、ラムダ式の **範囲変数** はサブフィールドを持つオブジェクトです。 それらのサブフィールドを標準の OData パス構文で参照することができます。 たとえば、次のフィルターでは、デラックス ルームが少なくとも 1 つはあり、すべてが禁煙室であるすべてのホテルが返されます。

> `$filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)`

最上位レベルの単純フィールドと同様に、複合フィールド内の単純サブフィールドは、インデックス定義で **filterable** 属性が `true` に設定されている場合にのみ、フィルターに含めることができます。 詳しくは、[Create Index API リファレンス](/rest/api/searchservice/create-index) に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

**データのインポート** ウィザードで [Hotels データ セット](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)を試してください。 データにアクセスするには、readme に記載されている Cosmos DB 接続情報が必要です。

その情報を入手したら、ウィザードの最初の手順は新しい Azure Cosmos DB データ ソースを作成することです。 ウィザードの手順を進め、ターゲットのインデックス ページに移動すると、複合型のインデックスが表示されます。 このインデックスを作成して読み込み、クエリを実行して新しい構造を理解してください。

> [!div class="nextstepaction"]
> [クイックスタート: インポート、インデックス作成、クエリのポータル ウィザード](search-get-started-portal.md)
