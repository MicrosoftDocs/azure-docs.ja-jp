---
title: インデックスでの検索結果の範囲指定用フィルター - Azure Search
description: Microsoft Azure のホスト型クラウド検索サービスである Azure Search で、ユーザーのセキュリティ ID、言語、地理的な場所、または数値でフィルター処理してクエリの検索結果を減らします。
author: heidisteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 4b5d198506473c598f058c881f781a06e191df88
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653439"
---
# <a name="filters-in-azure-search"></a>Azure Search のフィルター 

*フィルター*は、Azure Search クエリに使用されているドキュメントを選択するための条件を提供します。 フィルター処理されていない検索には、インデックスのすべてのドキュメントが含まれます。 フィルターによって、検索クエリの範囲がドキュメントのサブセットに絞り込まれます。 たとえば、フィルターによって、フルテキスト検索を特定のブランドや色、特定のしきい値を超える価格帯の製品のみに制限することができます。

一部の検索エクスペリエンスは実装の一部としてフィルター要件を課しますが、*値ベース* (製品の種類を "書籍"、カテゴリを "ノンフィクション"、出版元を "Simon & Schuster" に検索範囲を指定するなど) の条件を使用して検索を制限したいときにいつでもフィルターを使用できます。

値ベースではなく、特定のデータ*構造* (検索範囲を顧客レビュー フィールドに指定するなど) を対象にした検索が目標の場合、以下のように代替の方法があります。

## <a name="when-to-use-a-filter"></a>フィルターを使用する場合

フィルターは、"近くを検索"、ファセット ナビゲーション、ユーザーに閲覧が許可されているドキュメントのみを表示するセキュリティ フィルターなど、一部の検索エクスペリエンスの基盤です。 これらいずれかのエクスペリエンスを実装する場合、フィルターは必須です。 これは、地理座標、ユーザーが選択したファセット カテゴリ、または要求側のセキュリティ ID を提供する検索クエリにアタッチされるフィルターです。

次のようなシナリオ例があります。

1. フィルターを使用して、インデックスのデータ値に基づいてインデックスをスライスします。 市区町村、住宅の種類、および設備が設定つれたスキーマがある場合、条件 (シアトル、コンドミニアム、ウォーターフロント) を満たすドキュメントを明示的に選択するフィルターを作成できます。 

   同じ入力を使用したフルテキスト検索からは、多くの場合、似た結果が生成されますが、フィルターはインデックスのコンテンツに対してフィルター用語との完全一致を必須とするため、より正確です。 

2. 検索エクスペリエンスにフィルター要件がある場合はフィルターを使用します。

   * [ファセット ナビゲーション](search-faceted-navigation.md)では、フィルターを使用して、ユーザーが選択したファセット カテゴリが渡されます。
   * geo 検索では、"近くを検索" アプリで現在地の座標を渡すためにフィルターを使用します。 
   * セキュリティ フィルターはセキュリティ識別子をフィルター条件として渡します。インデックスの一致は、ドキュメントに対するアクセス権のプロキシとして機能します。

3. 数値フィールドに対する検索条件が必要な場合は、フィルターを使用します。 

   数値フィールドはドキュメントで取得可能で、検索結果に表示することができますが、個々に検索することはできません (フルテキスト検索のため)。 数値データに基づく選択条件が必要な場合は、フィルターを使用します。

### <a name="alternative-methods-for-reducing-scope"></a>スコープを減らすための代替方法

検索結果を絞り込むには、フィルターが唯一の選択肢ではありません。 目的に応じて、より適した代替方法があります。

 + `searchFields` クエリ パラメーターは、検索対象を特定のフィールドに限定します。 たとえば、英語とスペイン語の説明用に別のフィールドを用意したインデックスがある場合、searchFields を使用して、フルテキスト検索の対象にするフィールドを指定できます。 

+ `$select` パラメーターは、結果セットに含めるフィールドを指定するために使用できます。効果的に応答をトリミングしてから、呼び出し元アプリケーションに送信することができます。 このパラメーターでクエリが絞り込まれたり、ドキュメント コレクションが減ったりすることはありませんが、応答の量を小さくする場合、このパラメーターの使用を検討します。 

各パラメーターの詳細については、[「Search Documents」(ドキュメントの検索) > 「Request」(要求) > 「Query parameters」(クエリ パラメーター)](https://docs.microsoft.com/rest/api/searchservice/search-documents#request) を参照してください。


## <a name="how-filters-are-executed"></a>フィルターの実行方法

クエリ時に、フィルター パーサーは入力として条件を受け取り、式をツリーとして表されるアトミックなブール式に変換し、フィルター ツリーをインデックスのフィルター可能なフィールドで評価します。

フィルターは検索と並行して実行され、ドキュメントの取得と関連性のスコア付けのためにダウンストリーム処理に含めるドキュメントを特定します。 フィルターを検索文字列と組み合わせて使用すると、以降の検索操作での再指定を効果的に減らすことができます。 フィルターを単独で使用すると (たとえば、`search=*` ではクエリ文字列が空です)、フィルター条件が唯一の入力になります。 

## <a name="defining-filters"></a>フィルターを定義する

フィルターは OData 式です。[Azure Search でサポートされている OData V4 構文のサブセット](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)を使用して記述します。 

各**検索**操作に 1 つのフィルターを指定できますが、フィルター自体には複数のフィールド、複数の条件、および複数のフルテキスト検索式 (**ismatch** 関数を使用する場合) を含めることができます。 マルチパートのフィルター式では、任意の順序で述語を指定できます (演算子の優先順位の規則に従います)。 特定の順序で述語を並べ替えても、感知できるほどパフォーマンスが向上することはありません。

フィルター式に関する制限の 1 つとして、要求サイズの上限があります。 要求全体として (フィルターを含め)、POST の場合は最大 16 MB、GET の場合は最大 8 KB です。 また、フィルター式内の句の数にも制限があります。 目安として数百単位の句がある場合、上限に達する危険性があります。 サイズが無制限のフィルターを生成しない方法でアプリケーションを設計することをお勧めします。

次の例は、いくつかの API で典型的なフィルター定義を表しています。

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2019-05-06

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>フィルターの使用パターン

以下に、フィルター シナリオの使用パターン例をいくつか紹介します。 その他のアイデアについては、[「OData expression syntax」(OData 式の構文) > 「Examples」(例)](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples) を参照してください。

+ クエリ文字列がないスタンドアロンの **$filter**。関係があるドキュメントをフィルター式で完全に修飾できる場合に役立ちます。 クエリ文字列がない場合、字句または言語の分析、スコア付け、優先度付けはありません。 検索文字列がアスタリスクのみであることに注意してください。これは、"すべてのドキュメントを照合する" ことを意味しています。

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ クエリ文字列と **$filter** の組み合わせ。フィルターによってサブセットが作成され、クエリ文字列は、フィルターされたサブセットに対するフルテキスト検索に用語入力を提供します。 フィルターとクエリ文字列の併用は、最も一般的な使用パターンです。

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ 複合クエリ。"or" で区切られ、それぞれに独自のフィルター条件があります (たとえば、'dog' の 'beagles'、'cat' の 'siamese')。 `or` で結合された式は個別に評価され、各式に一致するドキュメントの和集合が応答で返されます。 この使用パターンは、`search.ismatchscoring` 関数を使用して実行されます。 また、スコア付けなしバージョンである `search.ismatch` を使用することもできます。

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

  `or` の代わりに `and` を指定して、`search.ismatchscoring` によるフルテキスト検索とフィルターを結合することもできますが、これは検索要求で `search` パラメーターと `$filter` パラメーターを使用することと機能的に同じです。 たとえば、次の 2 つのクエリでは同じ結果が生成されます。

  ```
  $filter=search.ismatchscoring('pool') and rating ge 4

  search=pool&$filter=rating ge 4
  ```

特定のユース ケースに関する包括的なガイダンスについては、以下の記事を参照してください。

+ [ファセット フィルター](search-filters-facets.md)
+ [言語フィルター](search-filters-language.md)
+ [セキュリティによるトリミング](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>フィルターのフィールド要件

REST API では、フィルター可能の設定は単純型フィールドの場合は既定で "*オン*" です。 フィルター可能なフィールドはインデックス サイズが大きくなります。実際にフィルターで使用する予定がないフィールドの場合は、`"filterable": false` を設定してください。 フィールド定義の設定の詳細については、「[Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index)」(インデックスの作成) を参照してください。

.NET SDK では、フィルター可能の設定は既定で*オフ*です。 対応する [Field](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) オブジェクトの [IsFilterable プロパティ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) を `true` に設定することで、フィールドをフィルター可能にすることができます。 また、これは、[IsFilterable 属性](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute)を使用して宣言によって行うこともできます。 次の例では、属性は、インデックス定義にマップされるモデル クラスの `BaseRate` プロパティで設定されています。

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>既存のフィールドをフィルター可能にする

既存のフィールドを変更してフィルター可能にすることはできません。 代わりに、新しいフィールドを追加するか、インデックスを再構築する必要があります。 インデックスの再構築の詳細については、「[Azure Search インデックスを再構築する方法](search-howto-reindex.md)」をご覧ください。

## <a name="text-filter-fundamentals"></a>テキスト フィルターの基礎

テキスト フィルターでは、文字列フィールドと、フィルターに指定したリテラル文字列とを照合します。 フルテキスト検索とは異なり、テキスト フィルターには字句解析や単語区切り処理がないため、比較は完全一致のみです。 たとえば、フィールド *f* に "sunny day" が含まれる場合、`$filter=f eq 'Sunny'` は一致しませんが、`$filter=f eq 'sunny day'` は一致します。 

テキスト文字列は大文字と小文字が区別されます。 大文字の単語の小文字化処理はないため、`$filter=f eq 'Sunny day'` で "sunny day" は検索されません。

### <a name="approaches-for-filtering-on-text"></a>テキストをフィルター処理するためのアプローチ

| アプローチ | 説明 | いつ使用するか |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | 区切られた文字列のリストに対してフィールドを照合する関数です。 | [セキュリティ フィルター](search-security-trimming-for-azure-search.md)や、多数の未加工テキスト値を文字列フィールドと照合する必要があるフィルターに推奨されます。 **search.in** 関数は、速度を重視して設計されているため、`eq` と `or` を使用して各文字列とフィールドを明示的に比較するよりもはるかに高速です。 | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | フルテキスト検索操作と、厳格なブール型フィルター操作を同じフィルター式に混在させることができる関数です。 | 1 つの要求で複数の検索フィルターを組み合わせる場合は、**search.ismatch** (または、このスコア付けバージョンである **search.ismatchscoring**) を使用します。 また、*contains* フィルターに使用して、大きな文字列内の一部の文字列をフィルターすることができます。 |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | ユーザー定義の式は、フィールド、演算子、および値で構成されます。 | 文字列フィールドと文字列値の間の完全一致を検索する場合は、これを使用します。 |

## <a name="numeric-filter-fundamentals"></a>数値フィルターの基礎

フルテキスト検索の文脈では、数値フィールドは `searchable` ではありません。 文字列のみがフルテキスト検索の対象になります。 たとえば、検索用語として 99.99 と入力しても、価格が 99.99 ドルの項目は取得されません。 代わりに、ドキュメントの文字列フィールドに 99 という数字が含まれる項目が表示されます。 そのため、数値データがある場合、範囲、ファセット、グループなどのフィルターに使用することが前提となります。 

数値フィールド (価格、サイズ、SKU、ID) を含むドキュメントで、フィールドが `retrievable` とマークされている場合、検索結果にはそれらの値が表示されます。 ここで大事な点は、フルテキスト検索は数値フィールドの種類には適用できないということです。

## <a name="next-steps"></a>次の手順

まずポータルの **Search エクスプローラー**で、 **$filter** パラメーターを使用したクエリを送信します。 [real-estate-sample インデックス](search-get-started-portal.md)では、検索バーに次のフィルターされたクエリを貼り付けると、興味深い結果になります。

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

その他の例については、[「OData Filter Expression Syntax」(OData フィルター式の構文) > 「Examples」(例)](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples) を参照してください。

## <a name="see-also"></a>関連項目

+ [Azure Search のフルテキスト検索のしくみ](search-lucene-query-architecture.md)
+ [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [単純なクエリ構文](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene クエリ構文](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [サポートされているデータ型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
