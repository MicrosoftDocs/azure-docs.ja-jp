---
title: 検索結果のフィルター処理
titleSuffix: Azure Cognitive Search
description: Microsoft Azure のホスト型クラウド検索サービスである Azure Cognitive Search で、ユーザーのセキュリティ ID、言語、地理的な場所、または数値でフィルター処理して、クエリの検索結果を減らします。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: ba538f4753c2365406bd88286b6d54cff1a9e9ea
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800824"
---
# <a name="filters-in-azure-cognitive-search"></a>Azure Cognitive Search のフィルター 

"*フィルター*" により、クエリに使用するドキュメントを選択するための、値に基づく条件が提供されます。 フィルターとして指定できるのは、単一の値または OData [フィルター式](search-query-odata-filter.md)です。 フルテキスト検索とは異なり、フィルター値または式では厳密な一致のみが返されます。

[ファセット ナビゲーション](search-filters-facets.md)などの一部の検索エクスペリエンスは実装の一部としてフィルターに依存していますが、クエリのスコープを特定の値に限定する必要がある場合には、いつでもフィルターを使用できます。 一方、クエリを特定のフィールドに限定することが目的である場合には、以下で説明する別の方法があります。

## <a name="when-to-use-a-filter"></a>フィルターを使用する場合

フィルターは、"近くを検索"、ファセット ナビゲーション、ユーザーに閲覧が許可されているドキュメントのみを表示するセキュリティ フィルターなど、一部の検索エクスペリエンスの基盤です。 これらいずれかのエクスペリエンスを実装する場合、フィルターは必須です。 これは、地理座標、ユーザーが選択したファセット カテゴリ、または要求側のセキュリティ ID を提供する検索クエリにアタッチされるフィルターです。

一般的なシナリオには次のようなものがあります。

+ インデックスの内容に基づいて検索結果をスライスする。 ホテルの場所、カテゴリ、アメニティを持つスキーマを指定すると、条件 (シアトル、水辺、眺望) を明示的に一致させるフィルターを作成することができます。 

+ フィルター要件のある検索エクスペリエンスを実装する。

  + [ファセット ナビゲーション](search-faceted-navigation.md)では、フィルターを使用して、ユーザーが選択したファセット カテゴリが渡されます。
  + geo 検索では、"近くを検索" アプリで現在地の座標を渡すためにフィルターを使用します。 
  + [セキュリティ フィルター](search-security-trimming-for-azure-search.md)ではセキュリティ識別子がフィルター条件として渡されます。インデックスの一致は、ドキュメントに対するアクセス権のプロキシとして機能します。

+ "数値検索" を実行する。 数値フィールドは取得可能で、検索結果に表示することができますが、個々に検索することはできません (フルテキスト検索に従うため)。 数値データに基づく選択条件が必要な場合は、フィルターを使用します。

### <a name="alternative-methods-for-reducing-scope"></a>スコープを減らすための代替方法

検索結果を絞り込むには、フィルターが唯一の選択肢ではありません。 目的に応じて、より適した代替方法があります。

+ `searchFields` クエリ パラメーターでは、検索対象が特定のフィールドに限定されます。 たとえば、英語とスペイン語の説明用に別のフィールドを用意したインデックスがある場合、searchFields を使用して、フルテキスト検索の対象にするフィールドを指定できます。 

+ `$select` パラメーターは、結果セットに含めるフィールドを指定するために使用できます。効果的に応答をトリミングしてから、呼び出し元アプリケーションに送信することができます。 このパラメーターでクエリが絞り込まれたり、ドキュメント コレクションが減ったりすることはありませんが、応答の量を小さくする場合、このパラメーターの使用を検討します。 

各パラメーターの詳細については、[「Search Documents」(ドキュメントの検索) > 「Request」(要求) > 「Query parameters」(クエリ パラメーター)](/rest/api/searchservice/search-documents#query-parameters) を参照してください。

## <a name="how-filters-are-executed"></a>フィルターの実行方法

クエリ時に、フィルター パーサーは入力として条件を受け取り、式をツリーとして表されるアトミックなブール式に変換し、フィルター ツリーをインデックスのフィルター可能なフィールドで評価します。

フィルターは検索と並行して実行され、ドキュメントの取得と関連性のスコア付けのためにダウンストリーム処理に含めるドキュメントを特定します。 フィルターを検索文字列と組み合わせて使用すると、以降の検索操作での再指定を効果的に減らすことができます。 フィルターを単独で使用すると (たとえば、`search=*` ではクエリ文字列が空です)、フィルター条件が唯一の入力になります。 

## <a name="defining-filters"></a>フィルターを定義する

フィルターは OData 式であり、Cognitive Search によってサポートされる[フィルター構文](search-query-odata-filter.md)で表現されます。

各 **検索** 操作に 1 つのフィルターを指定できますが、フィルター自体には複数のフィールド、複数の条件、および複数のフルテキスト検索式 (**ismatch** 関数を使用する場合) を含めることができます。 マルチパートのフィルター式では、任意の順序で述語を指定できます (演算子の優先順位の規則に従います)。 特定の順序で述語を並べ替えても、感知できるほどパフォーマンスが向上することはありません。

フィルター式に関する制限の 1 つとして、要求サイズの上限があります。 要求全体として (フィルターを含め)、POST の場合は最大 16 MB、GET の場合は最大 8 KB です。 また、フィルター式内の句の数にも制限があります。 目安として数百単位の句がある場合、上限に達する危険性があります。 サイズが無制限のフィルターを生成しない方法でアプリケーションを設計することをお勧めします。

次の例は、いくつかの API で典型的なフィルター定義を表しています。

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2020-06-30&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>フィルターの使用パターン

以下に、フィルター シナリオの使用パターン例をいくつか紹介します。 その他のアイデアについては、[「OData expression syntax」(OData 式の構文) > 「Examples」(例)](./search-query-odata-filter.md#examples) を参照してください。

+ クエリ文字列がないスタンドアロンの **$filter**。関係があるドキュメントをフィルター式で完全に修飾できる場合に役立ちます。 クエリ文字列がない場合、字句または言語の分析、スコア付け、優先度付けはありません。 検索文字列がアスタリスクのみであることに注意してください。これは、"すべてのドキュメントを照合する" ことを意味しています。

  ```http
  {
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu"
  }
  ```

+ クエリ文字列と **$filter** の組み合わせ。フィルターによってサブセットが作成され、クエリ文字列は、フィルターされたサブセットに対するフルテキスト検索に用語入力を提供します。 用語 (walking distance theaters) を追加することにより、結果に検索スコアが導入され、用語に最も一致するドキュメントが上位にランク付けされます。 フィルターとクエリ文字列の併用は、最も一般的な使用パターンです。

  ```http
  {
    "search": "walking distance theaters",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'"
  }

+ Compound queries, separated by "or", each with its own filter criteria (for example, 'beagles' in 'dog' or 'siamese' in 'cat'). Expressions combined with `or` are evaluated individually, with the union of documents matching each expression sent back in the response. This usage pattern is achieved through the `search.ismatchscoring` function. You can also use the non-scoring version, `search.ismatch`.

   ```
   # <a name="match-on-hostels-rated-higher-than-4-or-5-star-motels"></a>4 つ星より上のホステルか、5 つ星のモーテルに一致します。
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # <a name="match-on-luxury-or-high-end-in-the-description-field-or-on-category-exactly-equal-to-luxury"></a>description フィールド内に 'luxury' または 'high-end' が含まれているか、カテゴリが 'Luxury' とまったく同一の場合に一致します。
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  It is also possible to combine full-text search via `search.ismatchscoring` with filters using `and` instead of `or`, but this is functionally equivalent to using the `search` and `$filter` parameters in a search request. For example, the following two queries produce the same result:

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

Follow up with these articles for comprehensive guidance on specific use cases:

+ [Facet filters](search-filters-facets.md)
+ [Security trimming](search-security-trimming-for-azure-search.md) 

## Field requirements for filtering

In the REST API, filterable is *on* by default for simple fields. Filterable fields increase index size; be sure to set `"filterable": false` for fields that you don't plan to actually use in a filter. For more information about settings for field definitions, see [Create Index](/rest/api/searchservice/create-index).

In the .NET SDK, the filterable is *off* by default. You can make a field filterable by setting the [IsFilterable property](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) of the corresponding [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) object to `true`. In the example below, the attribute is set on the `BaseRate` property of a model class that maps to the index definition.

```csharp
[IsFilterable, IsSortable, IsFacetable]
public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>既存のフィールドをフィルター可能にする

既存のフィールドを変更してフィルター可能にすることはできません。 代わりに、新しいフィールドを追加するか、インデックスを再構築する必要があります。 インデックスの再構築またはフィールドへの再入力の詳細については、[Azure Cognitive Search のインデックスを再構築する方法](search-howto-reindex.md)に関するページをご覧ください。

## <a name="text-filter-fundamentals"></a>テキスト フィルターの基礎

テキスト フィルターでは、文字列フィールドと、フィルターに指定したリテラル文字列とを照合します: `$filter=Category eq 'Resort and Spa'`

フルテキスト検索とは異なり、テキスト フィルターには字句解析や単語区切り処理がないため、比較は完全一致のみです。 たとえば、フィールド *f* に "sunny day" が含まれる場合、`$filter=f eq 'Sunny'` は一致しませんが、`$filter=f eq 'sunny day'` は一致します。 

テキスト文字列は大文字と小文字が区別されます。 大文字の単語の小文字化処理はないため、`$filter=f eq 'Sunny day'` で "sunny day" は検索されません。

### <a name="approaches-for-filtering-on-text"></a>テキストをフィルター処理するためのアプローチ

| アプローチ | 説明 | 使用する場合 |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | 区切られた文字列のリストに対してフィールドを照合する関数です。 | [セキュリティ フィルター](search-security-trimming-for-azure-search.md)や、多数の未加工テキスト値を文字列フィールドと照合する必要があるフィルターに推奨されます。 **search.in** 関数は、速度を重視して設計されているため、`eq` と `or` を使用して各文字列とフィールドを明示的に比較するよりもはるかに高速です。 | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | フルテキスト検索操作と、厳格なブール型フィルター操作を同じフィルター式に混在させることができる関数です。 | 1 つの要求で複数の検索フィルターを組み合わせる場合は、**search.ismatch** (または、このスコア付けバージョンである **search.ismatchscoring**) を使用します。 また、*contains* フィルターに使用して、大きな文字列内の一部の文字列をフィルターすることができます。 |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | ユーザー定義の式は、フィールド、演算子、および値で構成されます。 | 文字列フィールドと文字列値の間の完全一致を検索する場合は、これを使用します。 |

## <a name="numeric-filter-fundamentals"></a>数値フィルターの基礎

フルテキスト検索の文脈では、数値フィールドは `searchable` ではありません。 文字列のみがフルテキスト検索の対象になります。 たとえば、検索用語として 99.99 と入力しても、価格が 99.99 ドルの項目は取得されません。 代わりに、ドキュメントの文字列フィールドに 99 という数字が含まれる項目が表示されます。 そのため、数値データがある場合、範囲、ファセット、グループなどのフィルターに使用することが前提となります。 

数値フィールド (価格、サイズ、SKU、ID) を含むドキュメントで、フィールドが `retrievable` とマークされている場合、検索結果にはそれらの値が表示されます。 ここで大事な点は、フルテキスト検索は数値フィールドの種類には適用できないということです。

## <a name="next-steps"></a>次のステップ

まずポータルの **Search エクスプローラー** で、**$filter** パラメーターを使用したクエリを送信します。 [real-estate-sample インデックス](search-get-started-portal.md)では、検索バーに次のフィルターされたクエリを貼り付けると、興味深い結果になります。

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

その他の例については、[「OData Filter Expression Syntax」(OData フィルター式の構文) > 「Examples」(例)](./search-query-odata-filter.md#examples) を参照してください。

## <a name="see-also"></a>関連項目

+ [Azure Cognitive Search でのフルテキスト検索のしくみ](search-lucene-query-architecture.md)
+ [Search Documents REST API](/rest/api/searchservice/search-documents)
+ [単純なクエリ構文](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene クエリ構文](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [サポートされるデータ型](/rest/api/searchservice/supported-data-types)