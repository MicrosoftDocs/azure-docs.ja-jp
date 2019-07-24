---
title: インデックスでの検索結果の範囲指定用フィルター - Azure Search
description: Microsoft Azure のホスト型クラウド検索サービスである Azure Search で、ユーザーのセキュリティ ID、言語、地理的な場所、または数値でフィルター処理してクエリの検索結果を減らします。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: a9e8d2cbc067fd92208fac778ba17c58bdc7a5e4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58079147"
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

+ `$select` パラメーターは、結果セットに含めるフィールドを指定するために使用できます。効果的に応答をトリミングしてから、呼び出し元アプリケーションに送信することができます。 このパラメーターでクエリが絞り込まれたり、ドキュメント コレクションが減ったりすることはありませんが、より細分化された応答が目標の場合、このパラメーターの使用を検討します。 

各パラメーターの詳細については、[「Search Documents」(ドキュメントの検索) > 「Request」(要求) > 「Query parameters」(クエリ パラメーター)](https://docs.microsoft.com/rest/api/searchservice/search-documents#request) を参照してください。


## <a name="filters-in-the-query-pipeline"></a>クエリ パイプライン内のフィルター

クエリ時に、フィルター パーサーは入力として条件を受け取り、式をアトミックなブール式に変換し、フィルター ツリーを構築します。フィルター ツリーは、インデックスのフィルター可能なフィールドで評価されます。  

フィルターは検索前に実行され、ドキュメントの取得と関連性のスコア付けのダウンストリーム処理に含める対象ドキュメントを特定します。 フィルターを検索文字列と組み合わせて使用すると、以降の検索操作の対象領域が効果的に少なくなります。 フィルターを単独で使用すると (たとえば、`search=*` ではクエリ文字列が空です)、フィルター条件が唯一の入力になります。 

## <a name="filter-definition"></a>フィルターの定義

フィルターは OData 式です。[Azure Search でサポートされている OData V4 構文のサブセット](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)を使用して記述します。 

各**検索**操作に 1 つのフィルターを指定できますが、フィルター自体には複数のフィールド、複数の条件、および複数の式 (**ismatch** 関数を使用する場合) を含めることができます。 マルチパートのフィルター式では、任意の順序で述語を指定できます。 特定の順序で述語を並べ替えても、感知できるほどパフォーマンスが向上することはありません。

フィルター式に関するハード リミットは、要求の上限です。 要求全体として (フィルターを含め)、POST の場合は最大 16 MB、GET の場合は最大 8 KB です。 ソフト リミットは、フィルター式内の句の数に関連しています。 目安として数百単位の句がある場合、上限に達する危険性があります。 サイズが無制限のフィルターを生成しない方法でアプリケーションを設計することをお勧めします。

次の例は、いくつかの API で典型的なフィルター定義を表しています。

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2017-11-11

# Option 2: Use filter for POST and pass it in the header
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2017-11-11
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

```

## <a name="filter-design-patterns"></a>フィルターの設計パターン

フィルター シナリオのいくつかの設計パターン例を紹介します。 その他のアイデアについては、[「OData expression syntax」(OData 式の構文) > 「Examples」(例)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples) を参照してください。

+ クエリ文字列がないスタンドアロンの **$filter**。関係があるドキュメントをフィルター式で完全に修飾できる場合に役立ちます。 クエリ文字列がない場合、字句または言語の分析、スコア付け、優先度付けはありません。 検索文字列が空である点に注目してください。

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ クエリ文字列と **$filter** の組み合わせ。フィルターによってサブセットが作成され、クエリ文字列は、フィルターされたサブセットに対するフルテキスト検索に用語入力を提供します。 フィルターとクエリ文字列の併用は、最も一般的なコード パターンです。

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ 複合クエリ。"or" で区切られ、それぞれに独自のフィルター条件があります (たとえば、'dog' の 'beagles'、'cat' の 'siamese')。 OR で接続された式は個別に評価され、各式の応答が 1 つの応答に結合されてから、呼び出し元アプリケーションに返されます。 この設計パターンは、search.ismatch 関数で達成されます。 スコア付けなしバージョン (search.ismatch) またはスコア付けありのバージョン (search.ismatchscoring) を使用できます。

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

特定のユース ケースに関する包括的なガイダンスについては、以下の記事を参照してください。

+ [ファセット フィルター](search-filters-facets.md)
+ [言語フィルター](search-filters-language.md)
+ [セキュリティによるトリミング](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>フィルターのフィールド要件

REST API では、フィルター可能の設定は既定で*オン*です。 フィルター可能なフィールドはインデックス サイズが大きくなります。実際にフィルターで使用する予定がないフィールドの場合は、`filterable=FALSE` を設定してください。 フィールド定義の設定の詳細については、「[Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index)」(インデックスの作成) を参照してください。

.NET SDK では、フィルター可能の設定は既定で*オフ*です。 フィルター可能なプロパティを設定する API は [IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute) です。 以下の例では、BaseRate フィールド定義に設定されています。

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="reindexing-requirements"></a>インデックス再作成の要件

フィールドがフィルター可能ではなく、フィルター可能にしたい場合は、新しいフィールドを追加するか、既存のフィールドを再構築する必要があります。 フィールド定義を変更すると、インデックスの物理構造が変わります。 Azure Search の場合、クエリを高速にするために、すべての許可されているパスのインデックスが作成されます。そのため、フィールド定義が変更されると、データ構造の再構築が必要になります。 

個々のフィールドの再構築は影響度の低い操作の可能性があります。既存のドキュメント キーと関連する値をインデックスに送信するマージ操作のみが必要で、各ドキュメントのその他の部分はそのままです。 リビルドが必要になった場合には、[インデックス作成操作 (upload、merge、mergeOrUpload、delete)](search-what-is-data-import.md#indexing-actions) に関するセクションでオプションの一覧を参照してください。


## <a name="text-filter-fundamentals"></a>テキスト フィルターの基礎

テキスト フィルターは、文字列フィールドに対して有効です。検索コーパス内の値に基づいて、任意のドキュメントのコレクションを取得します。

文字列で構成されるテキスト フィルターの場合、字句解析または単語区切り処理がないため、比較は完全一致のみです。 たとえば、フィールド *f* に "sunny day" が含まれる場合、`$filter=f eq 'Sunny'` では一致しませんが、`$filter=f eq 'Sunny day'` は一致します。 

テキスト文字列は大文字と小文字が区別されます。 大文字の単語の小文字化処理はないため、`$filter=f eq 'Sunny day'` で "sunny day" は検索されません。


| アプローチ | 説明 | 
|----------|-------------|
| [search.in()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | 指定されたフィールドに文字列のコンマ区切りの一覧を提供する関数。 この文字列はフィルター条件で構成され、クエリのスコープ内にある各フィールドに適用されます。 <br/><br/>`search.in(f, ‘a, b, c’)` は意味的には `f eq ‘a’ or f eq ‘b’ or f eq ‘c’` と同じですが、値の一覧が大きい場合、実行ははるかに速くなります。<br/><br/>[セキュリティ フィルター](search-security-trimming-for-azure-search.md)や、指定されたフィールドの値に対してマッチング処理を行う未加工のテキストで構成されたるフィルターには、**search.in** 関数を使用することをお勧めします。 このアプローチは、速度を目的に設計されています。 数百から数千単位の値であれば、1 秒未満の応答時間を期待できます。 関数に渡すことができる項目数に明示的な制限はありませんが、指定する文字列数に比例して待機時間は長くなります。 | 
| [search.ismatch()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | フルテキスト検索操作と、厳格なブール型フィルター操作を同じフィルター式に混在させることができる関数です。 1 つの要求で複数のクエリとフィルターを組み合わせることができます。 また、*contains* フィルターに使用して、大きな文字列内の一部の文字列をフィルターすることができます。 |  
| [$filter = フィールド演算子文字列](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | ユーザー定義の式は、フィールド、演算子、および値で構成されます。 | 

## <a name="numeric-filter-fundamentals"></a>数値フィルターの基礎

フルテキスト検索の文脈では、数値フィールドは `searchable` ではありません。 文字列のみがフルテキスト検索の対象になります。 たとえば、検索用語として 99.99 と入力しても、価格が 99.99 ドルの項目は取得されません。 代わりに、ドキュメントの文字列フィールドに 99 という数字が含まれる項目が表示されます。 そのため、数値データがある場合、範囲、ファセット、グループなどのフィルターに使用することが前提となります。 

数値フィールド (価格、サイズ、SKU、ID) を含むドキュメントで、フィールドが `retrievable` とマークされている場合、検索結果にはそれらの値が表示されます。 ここで大事な点は、フルテキスト検索は数値フィールドの種類には適用できないということです。

## <a name="next-steps"></a>次の手順

まずポータルの **Search エクスプローラー**で、**$filter** パラメーターを使用したクエリを送信します。 [real-estate-sample インデックス](search-get-started-portal.md)では、検索バーに次のフィルターされたクエリを貼り付けると、興味深い結果になります。

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

その他の例については、[「OData Filter Expression Syntax」(OData フィルター式の構文) > 「Examples」(例)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples) を参照してください。

## <a name="see-also"></a>関連項目

+ [Azure Search のフルテキスト検索のしくみ](search-lucene-query-architecture.md)
+ [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [単純なクエリ構文](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene クエリ構文](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [サポートされているデータ型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
