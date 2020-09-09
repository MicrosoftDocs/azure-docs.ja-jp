---
title: クエリの種類と構成
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search で結果のフィルター処理、選択、並べ替えを行うためのパラメーターを使って、検索クエリを作成するための基本。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: 018c3fb08c7fa0ad35fa567bffbeae48b6fbbce9
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928838"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Azure Cognitive Search でのクエリの種類と構成

Azure Cognitive Search では、クエリにラウンドトリップ処理すべてが指定されます。 要求には、エンジンの実行命令を提供するパラメーターと、応答を整形するパラメーターがあります。 一致基準なしでヌルか既定のパラメーターを使用して、明示的に指定しなかった場合 (`search=*` とした場合) は、フルテキスト検索操作としてすべての検索可能フィールドを対象にクエリが実行され、スコア付けされていない結果セットが任意の順序で返されます。

次の例は、[REST API](/rest/api/searchservice/search-documents) で構成された代表的なクエリです。 この例では、[ホテルのデモ インデックス](search-get-started-portal.md)をターゲットにして、クエリの内容を把握できるように一般的なパラメーターを含めています。

```
{
    "queryType": "simple" 
    "search": "+New York +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

+ **`queryType`** にはパーサーを設定します。設定できるのは、[既定の単純なクエリ パーサー](search-query-simple-examples.md) (フル テキスト検索に最適) または、正規表現、近接検索、ファジー検索、ワイルドカード検索など高度なクエリ構成で使用される[完全な Lucene クエリ パーサー](search-query-lucene-examples.md)です。

+ **`search`** には一致条件を指定します。通常は用語全体またはフレーズですが、ブール演算子が伴う場合もよくあります。 1 つの用語を単独で指定すると、"*用語*" クエリです。 複数の部分を引用符で囲むと、"*フレーズ*" クエリです。 **`search=*`** のように、検索は未定義にすることができます。ただし、一致条件がないために、結果セットは任意の選択されたドキュメントで構成されます。

+ **`searchFields`** は、クエリの実行を特定のフィールドに制限します。 インデックス スキーマ内で "*検索可能*" の属性を持つフィールドは、このパラメーターの候補になります。

応答も、クエリに指定するパラメーターによって形成されます。

+ **`select`** : 応答で返すフィールドを指定します。 select ステートメントでは、インデックスで "*取得可能*" とマークされたフィールドのみを使用できます。

+ **`top`** : 指定した数の最もよく一致するドキュメントを返します。 この例では、10 個のヒットのみが返されます。 top と skip (ここには非表示) を使用して、結果のページを移動することができます。

+ **`count`** : すべてのインデックス全体で一致するドキュメントの数を示します。これは、返されるものよりも大きくなる可能性があります。 

+ **`orderby`** : 評価や場所などの値によって結果を並べ替える場合に使用します。 それ以外の場合、既定では、関連性スコアを使用して結果が順位付けされます。

Azure Cognitive Search では、クエリ実行の対象となるのは常に、要求に含まれている API キーを使用して認証された 1 つのインデックスです。 REST では、両方が要求ヘッダーに指定されます。

### <a name="how-to-run-this-query"></a>このクエリを実行する方法

コードを記述する前に、クエリ ツールを使用して構文を学習し、さまざまなパラメーターを試してみることができます。 最も簡単な方法は、組み込みのポータルツール [Search エクスプローラー](search-explorer.md)です。

この[クイックスタートに従って、ホテルのデモ インデックス](search-get-started-portal.md)を作成した場合は、次のクエリ文字列をエクスプローラーの検索バーに貼り付けて、最初のクエリを実行できます。`search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>インデックスによってクエリ操作が有効になる仕組み

Azure Cognitive Search では、インデックスの設計とクエリの設計は密接に関連しています。 事前に知っておくべき基本的な事実は、各フィールドに属性が設定された*インデックス スキーマ* によって、構築できるクエリの種類が決まるということです。 

フィールドのインデックス属性によって、許可される操作が設定されます。フィールドがインデックス内で "*検索可能*" か、結果に "*取得可能*" か、"*ソート可能*" か、"*フィルター可能*" かなどです。 例のクエリ文字列で `"$orderby": "Rating"` が処理されるのは、Rating フィールドがインデックス スキーマで "*ソート可能*" とマークされているためです。 

![ホテル サンプルのインデックス定義](./media/search-query-overview/hotel-sample-index-definition.png "ホテル サンプルのインデックス定義")

上記のスクリーンショットは、ホテル サンプルのインデックス属性の一覧の一部です。 ポータルで、インデックススキーマ全体を確認できます。 インデックス属性について詳しくは、[インデックス作成 REST API](/rest/api/searchservice/create-index) に関するページをご覧ください。

> [!Note]
> クエリの一部の機能は、インデックスの各フィールドではなくインデックス全体に基づいて有効化されます。 このような機能には、[シノニム マップ](search-synonyms.md)、[カスタム アナライザー](index-add-custom-analyzers.md)、[提案機能による作成 (オートコンプリートおよび提案クエリ)](index-add-suggesters.md)、[結果を順位付けするためのスコアリング ロジック](index-add-scoring-profiles.md)が含まれます。

## <a name="elements-of-a-query-request"></a>クエリ要求の要素

クエリは常に 1 つのインデックスを対象とします。 複数のインデックスを結合したり、カスタム データ構造や一時的なデータ構造をクエリの対象にしたりすることはできません。 

クエリ要求の必須要素には、次のコンポーネントが含まれます。

+ サービス エンドポイントとインデックス ドキュメントのコレクション。固定コンポーネントとユーザー定義コンポーネントを含む URL として指定されます。 **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (REST のみ) は必須です。常に複数の API バージョンが使用できるためです。 
+ **`api-key`** (クエリまたは管理者の api-key) が、サービスへの要求を認証します。
+ **`queryType`** (simple または full)。組み込まれている既定の単純構文を使用する場合は省略できます。
+ **`search`** または **`filter`** には一致条件を指定します。空の検索を実行する場合は、未指定にすることができます。 クエリの 2 つの種類については単純なパーサーに関して説明していますが、高度なクエリでも複雑なクエリ式を渡すために search パラメーターが必要です。

その他の検索パラメーターはすべて省略可能です。 属性の完全な一覧は、[インデックス作成 (REST)](/rest/api/searchservice/create-index) に関するページをご覧ください。 処理中にパラメーターがどのように使用されるかについて詳しくは、[Azure Cognitive Search のフルテキスト検索のしくみ](search-lucene-query-architecture.md)に関するページをご覧ください。

## <a name="choose-apis-and-tools"></a>API とツールの選択

次の表は、API とツールを使ってクエリを送信する手法の一覧です。

| 手法 | 説明 |
|-------------|-------------|
| [Search エクスプローラー (ポータル)](search-explorer.md) | 検索バーのほか、インデックスと API バージョンの選択に関するオプションが用意されています。 結果は JSON ドキュメントとして返されます。 探索、テスト、検証用に推奨されます。 <br/>[詳細情報。](search-get-started-portal.md#query-index) | 
| [Postman またはその他の REST ツール](search-get-started-postman.md) | Web テスト ツールは、REST 呼び出しを作成するための優れた選択肢です。 REST API では、Azure Cognitive Search で利用可能なすべての操作をサポートします。 この記事では、Azure Cognitive Search に要求を送信するために HTTP 要求のヘッダーと本文を設定する方法について学習します。  |
| [SearchIndexClient (.NET)](/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Azure Cognitive Search のインデックスに対してクエリを実行するために使用できるクライアント。  <br/>[詳細情報。](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Search Documents (REST API)](/rest/api/searchservice/search-documents) | インデックスに対する GET メソッドまたは POST メソッド。追加の入力には、クエリ パラメーターを使用します。  |

## <a name="choose-a-parser-simple--full"></a>パーサーの選択: simple | full

Azure Cognitive Search は、Apache Lucene を基盤としており、一般的なクエリと特殊なクエリの処理用に 2 つのクエリ パーサーが用意されています。 単純なパーサーを使用する要求は、[単純なクエリ構文](query-simple-syntax.md)を使用して形成されます。これは自由な形式のテキスト クエリにおける処理速度と有効性のために既定として選択されます。 この構文では、AND、OR、NOT、フレーズ、サフィックス、優先順位の演算子など、一般的な各種検索演算子がサポートされています。

[完全な Lucene クエリ構文](query-Lucene-syntax.md#bkmk_syntax)は、要求に `queryType=full` を追加することで有効になります。これは、[Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) の一部として開発されたもので、広く採用されている表現性の高いクエリ言語です。 完全な構文は、単純な構文を拡張したものです。 単純な構文で記述するすべてのクエリは、完全な Lucene パーサーで実行できます。 

次の例で示すのは、同じクエリでも、queryType 設定が異なると、異なる結果が生成されるということです。 最初のクエリでは、`historic` の後の `^3` が検索語句の一部として扱われます。 このクエリの最上位の結果は "Marquis Plaza & Suites" であり、その説明には *ocean* が含まれています

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

完全な Lucene パーサーを使用した同じクエリでは、`^3` がフィールド内の語句ブースターとして解釈されます。 パーサーを切り替えると順位が変更され、*historic* という用語が含まれた結果が表示されます。

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>クエリの種類

Azure Cognitive Search では、幅広いクエリの種類がサポートされます。 

| クエリの種類 | 使用法 | 例と詳細 |
|------------|--------|-------------------------------|
| 自由形式のテキスト検索 | search パラメーターといずれかのパーサー| フルテキスト検索は、インデックスのすべての "*検索可能*" フィールドで 1 つ以上の語句をスキャンし、Google や Bing などの検索エンジンに期待するのと同様に機能します。 最初の例はフルテキスト検索です。<br/><br/>フルテキスト検索では、標準の Lucene アナライザーを使用した語彙分析が行われ (既定)、すべての語句を小文字に変換し、"the" のようなストップワードを除去します。 語彙分析を変更する[英語以外のアナライザー](index-add-language-analyzers.md#language-analyzer-list)や[言語に関係なく使える特別なアナライザー](index-add-custom-analyzers.md#AnalyzerTable)を使用して、既定値をオーバーライドできます。 たとえば、フィールドの内容全体を 1 つのトークンとして扱う [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) です。 これは、郵便番号、ID、製品名などのデータで役立ちます。 | 
| フィルター検索 | [OData フィルター式](query-odata-filter-orderby-syntax.md)といずれかのパーサー | フィルター クエリは、インデックスのすべての "*フィルター処理可能*" フィールドでブール式を評価します。 検索クエリと異なり、フィルター クエリはフィールドの内容を厳密に照合します。たとえば、文字列フィールドでは大文字と小文字が区別されます。 もう 1 つの違いは、フィルター クエリは OData 構文で表されることです。 <br/>[フィルター式の例](search-query-simple-examples.md#example-3-filter-queries) |
| 地理空間検索 | フィールドの [Edm.GeographyPoint 型](/rest/api/searchservice/supported-data-types)、フィルター式、いずれかのパーサー | Edm.GeographyPoint 型のフィールドに格納された座標が、"近くを探す" つまりマップに基づいた検索コントロールで使用されます。 <br/>[地理空間検索の例](search-query-simple-examples.md#example-5-geo-search)|
| 範囲検索 | フィルター式と単純なパーサー | Azure Cognitive Search では、範囲クエリは filter パラメーターを使用して作成されます。 <br/>[範囲フィルターの例](search-query-simple-examples.md#example-4-range-filters) | 
| [フィールド検索](query-lucene-syntax.md#bkmk_fields) | search パラメーターと完全なパーサー | 1 つのフィールドを対象とする複合クエリ式を作成します。 <br/>[フィールド検索の例](search-query-lucene-examples.md#example-2-fielded-search) |
| [あいまい検索](query-lucene-syntax.md#bkmk_fuzzy) | search パラメーターと完全なパーサー | 構造やスペリングが似ている語句を照合します。 <br/>[あいまい検索の例](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [近接検索](query-lucene-syntax.md#bkmk_proximity) | search パラメーターと完全なパーサー | ドキュメント内で近くにある語句を検索します。 <br/>[近接検索の例](search-query-lucene-examples.md#example-4-proximity-search) |
| [用語ブースト](query-lucene-syntax.md#bkmk_termboost) | search パラメーターと完全なパーサー | ブーストされた語を含むドキュメントの順位を、含まないドキュメントよりも引き上げます。 <br/>[用語ブーストの例](search-query-lucene-examples.md#example-5-term-boosting) |
| [正規表現検索](query-lucene-syntax.md#bkmk_regex) | search パラメーターと完全なパーサー | 正規表現の内容に基づいて照合します。 <br/>[正規表現の例](search-query-lucene-examples.md#example-6-regex) |
|  [ワイルドカードまたはプレフィックス検索](query-lucene-syntax.md#bkmk_wildcard) | search パラメーターと完全なパーサー | プレフィックスとチルダ (`~`) または 1 つの文字 (`?`) に基づいて照合します。 <br/>[ワイルドカード検索の例](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>検索結果の管理 

クエリの結果は、REST API では JSON ドキュメントとしてストリーム配信されますが、.NET の API を使用する場合は、ビルトインのシリアル化が適用されます。 検索結果は、クエリでパラメーターを設定し、応答に使用するフィールドを明示的に選択することで加工することができます。

クエリにパラメーターを使うことで、次のような方法で結果セットの構造を指定できます。

+ 結果に含めるドキュメント数を制限またはグループ化する (既定では 50 件)
+ 結果に含めるフィールドを選択する
+ 並べ替え順を設定する
+ 検索語句強調表示を追加して、検索結果の本文中で一致した語句を目立たせる

### <a name="tips-for-unexpected-results"></a>予期しない結果が生じた場合のヒント

結果の実質 (構造ではなく実質的な中身) が、予期しない内容になっている場合も少なくありません。 クエリの結果が想定と異なる場合は、次のような変更をクエリに加えて、結果が改善されるかどうかを試してみてください。

+ **`searchMode=any`** (既定) を **`searchMode=all`** に変更し、いずれかの条件への一致ではなく、すべての条件への一致を強制します。 これは特に、ブール演算子がクエリに含まれているときに当てはまります。

+ テキスト解析または字句解析が必要であるものの、クエリの種類が言語処理の妨げになっている場合は、クエリ手法を変更します。 フルテキスト検索では、スペル ミスや単語の単数形と複数形、不規則動詞、不規則名詞までもが、テキスト解析または字句解析によって自動的に修正されます。 ファジー検索やワイルドカード検索など、一部のクエリでは、クエリ解析パイプラインの過程で語彙解析が行われません。 シナリオによっては、従来より正規表現が回避策として使用されています。 

### <a name="paging-results"></a>ページングの結果
Azure Cognitive Search では、検索結果のページングを簡単に実装できます。 **`top`** および **`skip`** パラメーターを使用すると、管理しやすい並べ替えられたサブセットとして検索結果一式を取得できるようにする検索要求をスムーズに発行できます。また、このような検索結果により、検索 UI の利便性を簡単に高めることができます。 こうした結果の少数のサブセットを取得する際には、検索結果一式に含まれるドキュメントの数を取得することもできます。

検索結果のページングの詳細については、[Azure Cognitive Search での検索結果のページング方法](search-pagination-page-layout.md)に関する記事をご覧ください。

### <a name="ordering-results"></a>結果の並べ替え
検索クエリに対する結果を受け取るときに、Azure Cognitive Search から指定のフィールドの値に基づいて並べ替えられた結果が返されるように要求することができます。 既定では、Azure Cognitive Search では、[TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) から派生する各ドキュメントの検索スコアのランクに基づいて検索結果を並べ替えます。

検索スコア以外の値に基づいて並べ替えられた結果が Azure Cognitive Search から返されるようにするには、 **`orderby`** 検索パラメーターを使います。 フィールド名を含める **`orderby`** パラメーターの値と、地理空間値の [ **`geo.distance()` 関数**](query-odata-filter-orderby-syntax.md)の呼び出しを指定できます。 結果を昇順で要求する場合は各式の後に `asc` を指定し、降順で要求する場合は **`desc`** を指定します。 既定のランクは昇順です。


### <a name="hit-highlighting"></a>検索結果の強調表示
Azure Cognitive Search では、検索クエリに一致する検索結果の特定の部分を正確に強調表示できます。これは、 **`highlight`** 、 **`highlightPreTag`** 、 **`highlightPostTag`** の各パラメーターを使用して簡単に行えます。 一致するテキストを強調表示する*検索可能*フィールドを指定できるほか、Azure Cognitive Search から返される一致テキストの先頭と末尾に追加する文字列タグを正確に指定することもできます。

## <a name="see-also"></a>関連項目

+ [Azure Cognitive Search のフルテキスト検索のしくみ (クエリ解析アーキテクチャ)](search-lucene-query-architecture.md)
+ [Search エクスプローラー](search-explorer.md)
+ [.NET におけるクエリの実行方法](./search-get-started-dotnet.md)
+ [REST におけるクエリの実行方法](./search-get-started-powershell.md)