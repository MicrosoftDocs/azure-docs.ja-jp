---
title: クエリ型
titleSuffix: Azure Cognitive Search
description: Cognitive Search でサポートされているクエリの種類 (フリー テキスト、フィルター、オートコンプリートと検索候補、地理空間検索、システム クエリ、ドキュメント検索など) について説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: c088625528190ad116676fbb51cec9f8de4b1578
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600804"
---
# <a name="querying-in-azure-cognitive-search"></a>Azure Cognitive Search のクエリ

Azure Cognitive Search には、フリー テキスト検索から高度に指定されたクエリ パターンまで、さまざまなシナリオをサポートする豊富なクエリ言語が用意されています。 この記事では、クエリ要求と、作成できるクエリの種類について説明します。

Cognitive Search では、クエリには、ラウンド トリップ **`search`** 操作がすべて指定されており、クエリの実行を通知し、返される応答を整形するパラメーターが含まれています。 パラメーターとパーサーによって、クエリ要求の種類が決まります。 次のクエリ例は、ブール演算子を使用したフリー テキスト クエリであり、[ドキュメントの検索 (REST API)](/rest/api/searchservice/search-documents) を使用し、[hotels-sample-index](search-get-started-portal.md) ドキュメント コレクションを対象としています。

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "queryType": "simple",
    "searchMode": "all",
    "search": "restaurant +view",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

クエリの実行時に使用されるパラメーターは次のとおりです。

+ **`queryType`** にはパーサーを設定します。設定できるのは、[既定の単純なクエリ パーサー](search-query-simple-examples.md) (フル テキスト検索に最適) または、正規表現、近接検索、ファジー検索、ワイルドカード検索など高度なクエリ構成で使用される [完全な Lucene クエリ パーサー](search-query-lucene-examples.md)です。

+ **`searchMode`** は、一致が式の "all" 条件または "any" 条件に基づいているかどうかを指定します。 既定値は any です。

+ **`search`** には一致条件を指定します。通常は用語全体またはフレーズですが、演算子が伴う場合も伴わない場合もあります。 インデックス スキーマ内で "*検索可能*" の属性を持つフィールドは、このパラメーターの候補になります。

+ **`searchFields`** は、クエリの実行を特定の検索可能なフィールドに制限します。 開発時には、選択と検索に同じフィールド リストを使用すると便利です。 そうしないと、一致が結果に表示されないフィールド値に基づく可能性があり、ドキュメントが返された理由に対して不確実性が生じます。

応答を形成するために使用するパラメーター:

+ **`select`** : 応答で返すフィールドを指定します。 select ステートメントでは、インデックスで "*取得可能*" とマークされたフィールドのみを使用できます。

+ **`top`** : 指定した数の最もよく一致するドキュメントを返します。 この例では、10 個のヒットのみが返されます。 top と skip (ここには非表示) を使用して、結果のページを移動することができます。

+ **`count`** : すべてのインデックス全体で一致するドキュメントの数を示します。これは、返されるものよりも大きくなる可能性があります。 

+ **`orderby`** : 評価や場所などの値によって結果を並べ替える場合に使用します。 それ以外の場合、既定では、関連性スコアを使用して結果が順位付けされます。 このパラメーターの候補とするために、このフィールドには *並べ替え可能* の属性を付ける必要があります。

上記の一覧は代表ですが、完全ではありません。 クエリ要求のパラメーターの完全な一覧については、「[ドキュメントの検索 (REST API)](/rest/api/searchservice/search-documents)」をご覧ください。

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>クエリの種類

いくつかの例外はありますが、クエリ要求では、任意の数の検索ドキュメント内で高速スキャンできるように構造化された逆インデックスを反復処理します。この場合、すべてのフィールドで一致が見つかる可能性があります。 Cognitive Search では、一致を検索するための主要な方法として、フルテキスト検索やフィルターがありますが、オートコンプリートや地理的な位置検索など、その他のよく知られた検索エクスペリエンスを実装することもできます。 この記事の残りの部分では、Cognitive Search のクエリをまとめ、詳細情報と例へのリンクを示します。

## <a name="full-text-search"></a>フルテキスト検索

用語入力を収集する検索ボックスが検索アプリに含まれている場合、フルテキスト検索が、おそらくそのエクスペリエンスをサポートするクエリ操作になります。 フルテキスト検索では、インデックス内の検索可能な すべての *検索可能な* フィールドの **`search`** パラメーターに渡された用語または語句を使用できます。 クエリ文字列内の省略可能なブール演算子は、一致条件または例外条件を指定できます。 単純なパーサーと完全なパーサーは、いずれもフルテキスト検索をサポートしています。

Cognitive Search では、フルテキスト検索は Apache Lucene クエリ エンジン上に構築されています。 フルテキスト検索のクエリ文字列は、字句解析を使用してスキャンを効率化します。 解析には、すべての用語の小文字化、"the" などのストップ ワードの削除、プリミティブな原形への用語の単純化が含まれます。 標準のアナライザーは Standard Lucene です。

一致する用語が見つかった場合、クエリ エンジンは、ドキュメント キーまたは ID を使用してフィールド値をアセンブルする一致を含む検索ドキュメントを再構築し、ドキュメントを関連性の順にランク付けし、応答の上位 50 (既定) を返すか、 **`top`** を指定した場合は別の番号を返します。

フルテキスト検索を実装している場合は、コンテンツがトークン化される方法を理解すると、クエリの異常のデバッグに役立ちます。 ハイフンでつながれた文字列または特殊文字に対するクエリでは、既定の Standard Lucene 以外のアナライザーを使用してインデックスに正しいトークンが含まれていることを確認することが必要になります。 既定値をオーバーライドして、[言語アナライザー](index-add-language-analyzers.md#language-analyzer-list)や、字句解析を変更する[特別なアナライザー](index-add-custom-analyzers.md#built-in-analyzers)を使用できます。 たとえば、フィールドの内容全体を 1 つのトークンとして扱う [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) です。 これは、郵便番号、ID、製品名などのデータで役立ちます。 詳細については、「[部分的な用語検索と特殊文字を含むパターン](search-query-partial-matching.md)」をご覧ください。

大きなテキスト ブロック (コンテンツ フィールドや長い説明) が含まれているインデックスでブール演算子が頻繁な使用されることが多いですが、これが予想される場合は、 **`searchMode=Any|All`** パラメーターを使用してクエリをテストし、その設定がブール検索に与える影響を評価してください。

## <a name="autocomplete-and-suggested-queries"></a>オートコンプリートとクエリ候補

[オートコンプリートや結果候補](search-autocomplete-tutorial.md)は、逐次検索エクスペリエンスでの部分文字列入力に基づいて連続するクエリ要求を実行する **`search`** に代わる代替手段です。 [このチュートリアル](tutorial-csharp-type-ahead-and-suggestions.md)で説明しているように、 **`autocomplete`** と **`suggestions`** パラメーターは一緒に使用することも、個別に使用することもできますが、 **`search`** と一緒に使用することはできません。 完成した用語とクエリ候補はどちらもインデックスの内容から派生されます。 このエンジンは、インデックスに存在しない文字列や候補を返しません。 詳細については、「[オートコンプリート (REST API)](/rest/api/searchservice/autocomplete)」と「[検索候補 (REST API)](/rest/api/searchservice/suggestions)」をご覧ください。

## <a name="filter-search"></a>フィルター検索

フィルターは、Cognitive Search を含むアプリで広く使用されています。 アプリケーション ページでは、フィルターは多くの場合、ユーザー向けのフィルター処理のためにリンク ナビゲーション構造のファセットとして視覚化されます。 フィルターは、インデックス付きコンテンツのスライスを公開するために、内部的にも使用されます。 たとえば、製品カテゴリに対してフィルターを使用して検索ページを初期化したり、インデックスに英語とフランス語の両方のフィールドが含まれている場合は、言語を初期化したりすることができます。

次の表に示すように、特殊なクエリ フォームを呼び出すフィルターが必要な場合もあります。 指定されていない検索 ( **`search=*`** ) を含むフィルターを使用することも、用語、語句、演算子、およびパターンを含むクエリ文字列を含むフィルターを使用することもできます。

| ユーザー シナリオ | 説明 |
|-----------------|-------------|
| 範囲フィルター | Azure Cognitive Search では、範囲クエリは filter パラメーターを使用して作成されます。 詳細と例については、「[範囲フィルターの例](search-query-simple-examples.md#example-5-range-filters)」をご覧ください。 |
| 地理的な位置の検索 | 検索可能なフィールドが [Edm.GeographyPoint 型](/rest/api/searchservice/supported-data-types)の場合は、"近くを検索" またはマップに基づいた検索コントロールのフィルター式を作成できます。 地理空間検索を操作するフィールドには座標が含まれます。 詳細および例については、「[地理空間検索の例](search-query-simple-examples.md#example-6-geo-search)」をご覧ください。 |
| ファセット ナビゲーション | ファセット ナビゲーション構造は、ファセットでの `onclick` イベントに応答してフィルターを呼び出するときに、ユーザー向けのナビゲーションに役立ちます。 そのため、ファセットとフィルターは連携しています。 ファセット ナビゲーションを追加する場合は、エクスペリエンスを完了するためのフィルターが必要です。 詳細については、[ファセット フィルターの作成方法](search-filters-facets.md)に関するページをご覧ください。 |

> [!NOTE]
> フィルター式で使用されるテキストは、クエリ処理中には分析されません。 テキスト入力は、照合に成功するか失敗するかのどちらかになる逐語的な文字パターンで、大文字と小文字が区別されます。 フィルター式は [OData 構文](query-odata-filter-orderby-syntax.md)を使用して構築され、インデックス内のすべての *フィルター可能な* フィールドの **`filter`** パラメーターで渡されます。 詳細については、「[Azure Cognitive Search のフィルター](search-filters.md)」をご覧ください。

## <a name="document-look-up"></a>ドキュメントの検索

前述のクエリ フォームとは対照的に、このフォームでは、対応するインデックス検索またはスキャンを行わずに、[ID による検索ドキュメント](/rest/api/searchservice/lookup-document)を 1 つ取得します。 1 つのドキュメントのみが要求され、返されます。 ユーザーが検索結果で項目を選択する場合、ドキュメントの取得と詳細ページでのフィールドの設定が典型的な応答になり、ドキュメントの検索はこれをサポートする操作になります。

## <a name="advanced-search-fuzzy-wildcard-proximity-regex"></a>高度な検索: あいまい、ワイルドカード、近接、正規表現

高度なクエリ フォームは、特定のクエリ動作をトリガーする完全な Lucene パーサーと演算子に依存します。

| クエリの種類 | 使用法 | 例と詳細 |
|------------|--------|------------------------------|
| [フィールド検索](query-lucene-syntax.md#bkmk_fields) | **`search`** パラメーター、 **`queryType=full`**  | 1 つのフィールドを対象とする複合クエリ式を作成します。 <br/>[フィールド検索の例](search-query-lucene-examples.md#example-1-fielded-search) |
| [あいまい検索](query-lucene-syntax.md#bkmk_fuzzy) | **`search`** パラメーター、 **`queryType=full`** | 構造やスペリングが似ている語句を照合します。 <br/>[あいまい検索の例](search-query-lucene-examples.md#example-2-fuzzy-search) |
| [近接検索](query-lucene-syntax.md#bkmk_proximity) | **`search`** パラメーター、 **`queryType=full`** | ドキュメント内で近くにある語句を検索します。 <br/>[近接検索の例](search-query-lucene-examples.md#example-3-proximity-search) |
| [用語ブースト](query-lucene-syntax.md#bkmk_termboost) | **`search`** パラメーター、 **`queryType=full`** | ブーストされた語を含むドキュメントの順位を、含まないドキュメントよりも引き上げます。 <br/>[用語ブーストの例](search-query-lucene-examples.md#example-4-term-boosting) |
| [正規表現検索](query-lucene-syntax.md#bkmk_regex) | **`search`** パラメーター、 **`queryType=full`** | 正規表現の内容に基づいて照合します。 <br/>[正規表現の例](search-query-lucene-examples.md#example-5-regex) |
|  [ワイルドカードまたはプレフィックス検索](query-lucene-syntax.md#bkmk_wildcard) | **_`~`_* または **`?`** を使用した **`search`** パラメーター、 **`queryType=full`**| プレフィックスとチルダ (`~`) または 1 つの文字 (`?`) に基づいて照合します。 <br/>[ワイルドカード検索の例](search-query-lucene-examples.md#example-6-wildcard-search) |

## <a name="next-steps"></a>次の手順

クエリの実装について詳しく見るには、構文ごとに例を確認します。 フルテキスト検索を初めて使用する場合は、クエリ エンジンの機能をよく理解しておくことをお勧めします。

+ [簡易クエリの例](search-query-simple-examples.md)
+ [高度なクエリを作成するための Lucene 構文のクエリの例](search-query-lucene-examples.md)
+ [Azure Cognitive Search でのフルテキスト検索のしくみ](search-lucene-query-architecture.md)git