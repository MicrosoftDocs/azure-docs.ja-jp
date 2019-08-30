---
title: "\"単純な\" 検索構文を利用したクエリの例 - Azure Search"
description: フルテキスト検索、フィルター検索、地理検索、ファセット検索、および Azure Search インデックスのクエリに使用されるその他のクエリ文字列の単純なクエリの例。
author: HeidiSteen
manager: nitinme
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: df84686e512db90351d5a9815706890bce49848b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647626"
---
# <a name="query-examples-using-the-simple-search-syntax-in-azure-search"></a>Azure Search における "単純な" 検索構文を利用したクエリの例

[単純なクエリ構文](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)では、既定のクエリ パーサーを呼び出して、Azure Search インデックスに対してフルテキスト検索クエリを実行します。 単純クエリ アナライザーは高速で、フルテキスト検索、フィルター検索、ファセット検索、地理検索などの Azure Search で一般的なシナリオに対応します。 この記事では、単純な構文を使用するときに利用できるクエリ操作をデモンストレーションする例について詳しく説明します。

代替のクエリ構文には[完全な Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 構文があります。この構文は、あいまい検索、ワイルドカード検索などのより複雑なクエリ構造をサポートし、処理に時間がかかります。 完全な構文の詳細と例については、[Lucene 構文のクエリの例](search-query-lucene-examples.md)に関するページを参照してください。

## <a name="formulate-requests-in-postman"></a>Postman で要求を作成する

次の例では、[City of New York OpenData](https://nycopendata.socrata.com/) イニシアティブが提供するデータセットに基づいて利用可能なジョブで構成される NYC ジョブ検索インデックスを活用します。 このデータが最新のものであるとか、完全であるとはお考えにならないでください。 インデックスは、Microsoft が提供するサンドボックス サービス上にあります。つまり、これらのクエリを試すのに Azure サブスクリプションまたは Azure Search は必要ありません。

必要になるのは、GET で HTTP 要求を発行するための Postman または同等のツールです。 詳細については、[REST クライアントを使用した探索](search-get-started-postman.md)に関するページを参照してください。

### <a name="set-the-request-header"></a>要求ヘッダーを設定する

1. 要求ヘッダーで、**Content-Type** を `application/json` に設定します。

2. **api-key** を追加して文字列 `252044BE3886FE4A8E3BAA4F595114BB` に設定します。 これは、NYC ジョブ インデックスをホストするサンドボックス検索サービスのクエリ キーです。

要求ヘッダーを指定した後は、**search=** 文字列のみを入れ替えることで、この記事のすべてのクエリに対して要求ヘッダーを再利用できます。 

  ![Postman の要求ヘッダー](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>要求 URL を設定する

要求は、Azure Search エンドポイントと検索文字列を含む URL と GET コマンドを組み合わせたものです。

  ![Postman の要求ヘッダー](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

URL は、次の要素から構成されます。

+ **`https://azs-playground.search.windows.net/`** は、Azure Search 開発チームによって管理されているサンドボックス検索サービスです。 
+ **`indexes/nycjobs/`** は、そのサービスのインデックス コレクション内の NYC ジョブ インデックスです。 要求にはサービス名とインデックスの両方が必要です。
+ **`docs`** は、検索可能なすべてのコンテンツを含むドキュメント コレクションです。 要求ヘッダーに指定されたクエリ api-key は、ドキュメント コレクションを対象とする読み取り操作に対してのみ機能します。
+ **`api-version=2019-05-06`** は、すべての要求に必須のパラメーターである api-version を設定します。
+ **`search=*`** はクエリ文字列です。最初のクエリでは null で、最初の 50 件の結果が返されます (既定値)。

## <a name="send-your-first-query"></a>初めてクエリを送信する

確認手順として、次の要求を GET に貼り付け、 **[送信]** をクリックします。 結果は冗長な JSON ドキュメントとして返されます。 ドキュメント全体が返され、すべてのフィールドとすべての値を確認することができます。

次の URL を検証手順として REST クライアントに貼り付けて、ドキュメントの構造を表示します。

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

クエリ文字列 **`search=*`** は、null または空の検索に相当する未指定の検索です。 これは特に有用ではありませんが、実行できる最も簡単な検索です。

オプションで、URL に **`$count=true`** を追加して、検索基準に一致するドキュメントの数を返すことができます。 空の検索文字列では、これはインデックス内のすべてのドキュメントです (NYC ジョブの場合は約 2800)。

## <a name="how-to-invoke-simple-query-parsing"></a>単純なクエリの解析を呼び出す方法

対話型クエリでは何も指定する必要はありません。既定値は simple です。 コードでは、以前に完全なクエリ構文用に **queryType=full** を呼び出している場合は、**queryType=simple** を使用して既定値にリセットすることができます。

## <a name="example-1-field-scoped-query"></a>例 1:フィールド スコープ クエリ

この最初の例はパーサー固有ではありませんが、最初の基本的なクエリの概念である包含を紹介するために利用します。 この例では、クエリの実行とその応答の範囲をいくつかの特定のフィールドに制限しています。 ツールが Postman または Search エクスプローラーの場合、読みやすい JSON 応答を構成する方法を理解することが重要です。 

簡潔にするため、このクエリでは *business_title* フィールドのみを対象として、肩書きのみが返されるよう指定しています。 構文は、クエリの実行を business_title フィールドのみに制限する **searchFields** と、応答に含まれるフィールドを指定する **select** です。

### <a name="partial-query-string"></a>部分クエリ文字列

```http
searchFields=business_title&$select=business_title&search=*
```

次のクエリは、コンマ区切りのリストに複数のフィールドを持つ同じクエリです。

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>完全な URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=business_title&$select=business_title&search=*
```

このクエリの応答は、次のスクリーンショットのようになります。

  ![Postman の応答のサンプル](media/search-query-lucene-examples/postman-sample-results.png)

応答の検索スコアに気付いたかもしれません。 検索がフルテキスト検索でなかった、または適用された基準がないという理由でランクがない場合は、1 の均一のスコアが発生します。 条件なしの null 検索では、任意の順序で行が返されます。 実際の基準を含めると、検索スコアは意味のある値に変化します。

## <a name="example-2-look-up-by-id"></a>例 2:ID による参照

この例は少し特殊ですが、検索動作を評価する際、検索結果から除外されている理由や検索結果に含まれている理由を理解するために、特定のドキュメントの内容全体を調べることが必要になる場合があります。 1 つのドキュメントを全部返すには、[参照操作](https://docs.microsoft.com/rest/api/searchservice/lookup-document)を使用してドキュメント ID を渡します。

すべてのドキュメントは一意の識別子を持ちます。 参照クエリの構文を試す場合、使用する ID を見つけるために、最初にドキュメント ID の一覧を返します。 NYC ジョブの場合、識別子は `id` フィールドに格納されます。

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=id&$select=id&search=*
```

次の例は、前の応答で最初に出現した `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5" に基づいて特定のドキュメントを返す検索クエリです。 次のクエリでは、選択したフィールドだけでなく、ドキュメント全体が返されます。 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2019-05-06&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>例 3:フィルター クエリ

[フィルター構文](https://docs.microsoft.com/azure/search/search-query-odata-filter) は、**search** と一緒に使用することも、単独で使用することもできる OData 式です。 search パラメーターがないスタンドアロン フィルターは、関心があるドキュメントをフィルター式で完全に修飾できる場合に役立ちます。 クエリ文字列がない場合、字句または言語の分析なし、スコア付けなし (すべて 1 にスコア付け)、および優先度付けなしになります。 検索文字列が空である点に注目してください。

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "select=job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

一緒に使用した場合、フィルターが最初にインデックス全体に適用され、次にフィルター処理の結果に対して検索が実行されます。 フィルターはクエリのパフォーマンス向上に役立つ手法です。フィルターを使うと、検索クエリで処理が必要なドキュメントの数が減ります。

  ![フィルター クエリの応答](media/search-query-simple-examples/filtered-query.png)

Postman で GET を使用して試す場合は、次の文字列を貼り付けることができます。

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

フィルターと検索を組み合わせる別の強力な方法は、フィルター式の中で **`search.ismatch*()`** を使用することです。この場合、フィルター内で検索クエリを使用できます。 次のフィルター式では、*plan* でワイルドカードを使用して、plan、planner、planning などの用語が含まれる business_title を選択します。

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

この関数の詳細については、[「フィルターの例」の search.ismatch](https://docs.microsoft.com/azure/search/search-query-odata-full-text-search-functions#examples) を参照してください。

## <a name="example-4-range-filters"></a>例 4:範囲フィルター

範囲フィルターは、任意のデータ型の **`$filter`** 式を通してサポートされます。 次の例では、数値フィールドと文字列フィールドを検索します。 

範囲フィルターではデータ型が重要であり、数値フィールドにある数値データと文字列フィールドにある文字列データで最もうまく機能します。 Azure Search では数値文字列を比較できないため、文字列フィールドの数値データは範囲には適していません。 

次の例は、読みやすくするために POST 形式になっています (数値範囲の後ろにテキスト範囲が続きます)。

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  ![数値範囲用の範囲フィルター](media/search-query-simple-examples/rangefilternumeric.png)


```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  ![テキスト範囲用の範囲フィルター](media/search-query-simple-examples/rangefiltertext.png)

Postman で GET を使用して試すこともできます。

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> 検索アプリケーションでは値の範囲に対するファセットが一般に必要になります。 ファセット ナビゲーション構造に対するフィルターの作成に関する情報と例については、[ *「ファセット ナビゲーションを実装する方法」の「範囲に基づくフィルター」* ](search-faceted-navigation.md#filter-based-on-a-range)を参照してください。

## <a name="example-5-geo-search"></a>例 5:地理空間検索

サンプル インデックスには、緯度と経度の座標を持つ geo_location フィールドが含まれています。 この例では、[geo.distance 関数](https://docs.microsoft.com/azure/search/search-query-odata-geo-spatial-functions#examples)を使用して、開始点を中心に、指定された距離 (キロメートル単位) に収まるドキュメントをフィルター処理します。 クエリの最後の値 (4) を調整して、クエリのサーフェス領域を拡大または縮小できます。

次の例は、読みやすくするために POST 形式になっています。

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
結果をさらに読みやすくするために、検索結果は、ジョブ ID、役職、および勤務地を含むようにトリミングされます。 開始座標は、インデックス内のランダムなドキュメントから取得されています (ここではスタテン島の勤務地)。

Postman で GET を使用して試すこともできます。

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>例 6:検索の精度

用語のクエリは、単一の用語を対象とし、おそらくその多くは個別に評価されます。 語句のクエリは引用符で囲まれ、逐語的な文字列として評価されます。 一致の精度は、演算子と searchMode によって制御されます。

例 1: **`&search=fire`** は、150 件の結果を返します。すべての一致に、ドキュメントのどこかに出現する単語 fire が含まれています。

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire
```

例 2: **`&search=fire department`** は、2,002 件の結果を返します。 fire または department を含むドキュメントについて一致が返されます。

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire department
```

例 3: **`&search="fire department"`** は、82 件の結果を返します。 文字列を二重引用符で囲むと両方の用語に対して逐語的検索を実行することを表します。一致は、結合された用語で構成されるインデックス内のトークン化された用語で検出されます。 これが、 **`search=+fire +department`** のような検索と同等でない理由です。 どちらの用語も必須ですが、これらは個別にスキャンされます。 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>例 7:searchMode によるブール値

単純な構文では、文字形式のブール演算子 (`+, -, |`) がサポートされています。 searchMode パラメーターは、精度と再現率のトレードオフを示します。`searchMode=any` は再現率を優先し (任意の条件で一致すると結果セットとしてドキュメントが適格になります)、`searchMode=all` は精度を優先します (すべての条件が一致する必要があります)。 既定値は `searchMode=any` です。これは、クエリに複数の演算子を含めて、絞り込んだ結果ではなくより広範な結果を取得した場合に、混乱を招く可能性があります。 これは特に NOT に当てはまります。NOT では、特定の用語を "含まない" すべてのドキュメントが結果に含まれます。

既定の searchMode (any) を使用した場合、2,800 件のドキュメントが返されます。これは、複合語 "fire department" を含むドキュメントと、用語 "Metrotech Center" を含まないすべてのドキュメントの合計です。

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  ![検索モード any](media/search-query-simple-examples/searchmodeany.png)

searchMode を `all` に変更すると、条件に累積的な効果が適用され、より小さな結果セット (21 個のドキュメント) が返されます。これは、"fire department" という語句全体を含むドキュメントから、Metrotech Center のアドレスのジョブを除いた結果です。

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  ![検索モード all](media/search-query-simple-examples/searchmodeall.png)

## <a name="example-8-structuring-results"></a>例 8:結果の構造化

いくつかのパラメーターは、検索結果に含まれるフィールド、各バッチで返されるドキュメントの数、および並べ替え順を制御します。 この例では、前の例のいくつかを再利用し、 **$select** ステートメントと逐語検索基準を使用して結果を特定のフィールドに限定し、82 件の一致を返します 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
前の例に付加して、タイトルで並べ替えることができます。 この並べ替えは、インデックス内で civil_service_title が *sortable* であるために機能します。

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

結果のページングは、 **$top** パラメーターを使用して実装できます。このケースでは、上位 5 つのドキュメントが返されます。

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

次の 5 件を取得するには、最初のバッチをスキップします。

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>次の手順
コードでクエリを指定してみてください。 次のリンクでは、既定の単純な構文を使用して .NET と REST API の両方の検索クエリを設定する方法について説明しています。

* [.NET SDK を使用した Azure Search インデックスの照会](search-query-dotnet.md)
* [REST API を使用した Azure Search インデックスの照会](search-create-index-rest-api.md)

追加の構文リファレンス、クエリ アーキテクチャ、およびサンプルについては、次のリンク先を参照してください。

+ [高度なクエリを作成するための Lucene 構文のクエリの例](search-query-lucene-examples.md)
+ [Azure Search のフルテキスト検索のしくみ](search-lucene-query-architecture.md)
+ [単純なクエリ構文](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [完全な Lucene クエリ](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [フィルター構文と並べ替え構文](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
