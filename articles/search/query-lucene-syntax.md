---
title: Lucene クエリ構文
titleSuffix: Azure Cognitive Search
description: ワイルドカード、ファジー検索、RegEx などの高度なクエリ構成のために Azure Cognitive Search で使用される、完全な Lucene クエリ構文のリファレンス。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: fc3662d8198e6ab6ab215ac1e9e8eac585f4250b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801589"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search での Lucence クエリ構文

クエリを作成する場合、ワイルドカード、あいまい検索、近接検索、正規表現など、特殊なクエリ フォームに対して [Lucene Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 構文を選択できます。 Lucene Query Parser 構文の多くは、[Azure Cognitive Search でそのまま実装](search-lucene-query-architecture.md)されています。ただし、*範囲検索* は例外で、これは **`$filter`** 式を介して構築されます。 

完全な Lucene 構文を使用するには、queryType を "full" に設定し、ワイルドカード、あいまい検索、または完全な構文でサポートされている他のクエリ形式のいずれかのクエリ式を渡します。 REST では、クエリ式は [ドキュメントの検索 (REST API)](/rest/api/searchservice/search-documents) 要求の **`search`** パラメーターで指定されます。

## <a name="example-full-syntax"></a>例 (完全な構文)

次の例は、完全な構文を使用して構成された検索要求です。 この特定の例では、フィールド内検索と用語ブーストが示されています。 カテゴリ フィールドに "budget" という用語が含まれているホテルを検索します。 "recently renovated (最近改修済み)" というフレーズを含むすべてのドキュメントは、用語ブースト値 (3) の結果、高いランキングになります。  

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
  "queryType": "full",
  "search": "category:budget AND \"recently renovated\"^3",
  "searchMode": "all"
}
```

どのクエリの種類にも固有ではありませんが、この例では **`searchMode`** パラメーターが関連しています。 クエリに演算子があるときは、通常 `searchMode=all` を設定して、*すべて* の条件が確実に一致するようにします。  

その他の例については、「[Lucene クエリ構文例](search-query-lucene-examples.md)」をご覧ください。 searchMode を含む、クエリ要求とパラメーターの詳細については、[ドキュメントの検索 (REST API)](/rest/api/searchservice/Search-Documents) に関するページを参照してください。

## <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a> 構文の基礎  

次の構文の基礎は、Lucene 構文を使用するすべてのクエリに適用されます。  

### <a name="operator-evaluation-in-context"></a>コンテキストでの演算子評価

配置によって、記号を演算子と解釈するか、または文字列内の 1 つの文字と解釈するかが決まります。

たとえば、Lucene の完全構文では、チルダ (~) はあいまい検索と近接検索の両方に使用されます。 引用符で囲まれた語句の後に配置されると、~ は近接検索を呼び出します。 用語の末尾に配置されると、~ はあいまい検索を呼び出します。

用語内に配置される ("business~analyst" など) と、この文字は演算子と評価されません。 この場合は、このクエリが用語または語句のクエリであると想定して、[字句解析](search-lucene-query-architecture.md#stage-2-lexical-analysis)を使用する[フル テキスト検索](search-lucene-query-architecture.md)によって ~ が削除され、"business~analyst" という用語は 2 つに分割されて、business OR analyst になります。

上記の例ではチルダ (~) ですが、すべての演算子に同じ原則が適用されます。

### <a name="escaping-special-characters"></a>特殊文字のエスケープ

検索文字列の一部として検索演算子を使用するには、円記号 (`\`) 1 つを前に付けて文字をエスケープします。 たとえば、`https://` のワイルドカード検索で、`://` がクエリ文字列の一部である場合、`search=https\:\/\/*` を指定します。 同様に、エスケープされた電話番号パターンは、`\+1 \(800\) 642\-7676` のようになります。

エスケープが必要な特殊文字には次の例があります。  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> エスケープすることでトークンが一緒に保持されますが、インデックスを作成するときの[字句解析](search-lucene-query-architecture.md#stage-2-lexical-analysis)で削除される場合があります。たとえば、標準の Lucene アナライザーでは、ハイフン、スペースなどの文字で単語が分割されます。 クエリ文字列で特殊文字が必要な場合は、それらをインデックスに保持するアナライザーが必要になることがあります。 選択肢には、ハイフンでつながれた単語を保持する Microsoft 自然[言語アナライザー](index-add-language-analyzers.md)や、より複雑なパターン用のカスタム アナライザーなどが含まれます。 詳細については、[部分的な語句、パターン、特殊文字](search-query-partial-matching.md)に関する記事を参照してください。

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>URL での安全でない文字および予約文字のエンコード

安全でない文字および予約文字はすべて、URL でエンコードするようにしてください。 たとえば、'#' は、URL 内のフラグメント ID またはアンカー ID のため、安全でない文字です。 この文字を URL で使用する場合は、`%23` にエンコードする必要があります。 '&' と '=' は、予約文字の例です。これらの文字は、Azure Cognitive Search でパラメーターを区切り、値を指定します。 詳細については、「[RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt)」を参照してください。

安全でない文字は ``" ` < > # % { } | \ ^ ~ [ ]`` です。 予約文字は `; / ? : @ = + &` です。

## <a name="boolean-operators"></a><a name="bkmk_boolean"></a> ブール演算子

照合の精度を向上させるために、クエリ文字列にブール演算子を埋め込むことができます。 完全な構文は、文字演算子に加え、テキスト演算子をサポートします。 テキスト ブール演算子 (AND、OR、NOT) は、常に大文字で指定します。

|テキスト演算子 | 文字 | 例 | 使用法 |
|--------------|----------- |--------|-------|
| AND | `&`, `+` | `wifi + luxury` | 照合で含める必要がある用語を指定します。 この例では、クエリ エンジンは `wifi` と `luxury` の両方を含むドキュメントを検索します。 プラス記号 (`+`) は、必要な用語に使用されます。 たとえば、`+wifi +luxury` は、両方の用語が 1 つのドキュメントのフィールド内のどこかに表示されている必要があることを規定します。|
| OR | `|` | `wifi | luxury` | いずれかの用語が見つかった場合に一致を検索します。 この例では、クエリ エンジンは、`wifi` または `luxury` のいずれか、あるいはその両方を含むドキュメントに対して一致を返します。 OR は、既定の結合演算子のため、除外することもできます。たとえば、`wifi luxury` は `wifi | luxury` と同等です。|
| NOT | `!`, `-` | `wifi –luxury` | 用語を除外するドキュメントに対して一致を返します。 たとえば、`wifi –luxury` を指定すると、`wifi` という用語を含むが、`luxury` を含まないドキュメントが検索されます。 <br/><br/>クエリ要求の `searchMode` パラメーターでは、NOT 演算子がついた用語を、クエリ内の他の用語と AND 演算するか OR 演算するかどうかが制御されます (他の用語に `+` または `|` 演算子がないと仮定します)。 有効な値は、`any` または `all` です。  <br/><br/>`searchMode=any` にすると、より多くの結果を含めることによりクエリの再現率が増加し、既定で `-` は "OR NOT" と解釈されます。 たとえば、`wifi -luxury` は、`wifi` という用語を含むドキュメント、または `luxury` という用語を含まないドキュメントのどちらにも一致します。  <br/><br/>`searchMode=all` にすると、より少ない結果を含めることによりクエリの精度が上がり、既定で - は "AND NOT" と解釈されます。 たとえば、`wifi -luxury` は、`wifi` という用語を含み、かつ "luxury" という用語を含まないドキュメントに一致します。 これはほぼ間違いなく、`-` 演算子にとってより直感的な動作です。 したがって、再現率ではなく精度で検索を最適化し、"*かつ*" ユーザーが検索で `-` 演算子を頻繁に使用する場合は、`searchMode=any` よりも `searchMode=all` を選択することを検討してください。<br/><br/>`searchMode` 設定を決定するときは、さまざまなアプリケーションでのクエリのユーザー操作パターンを検討してください。 情報を検索するユーザーは、より多くの組み込みのナビゲーション構造を持つ eコマース サイトとは対照的に、クエリに演算子を含める可能性が高くなります。 |

##  <a name="fielded-search"></a><a name="bkmk_fields"></a> フィールド検索

`fieldName:searchExpression` 構文を使用して、フィールド検索操作を定義できます。検索式は、単一の単語、単一の語句、またはかっこで囲まれた複雑な式が可能であり、必要に応じてブール演算子も使用できます 例として、次のようなものがあります。  

- genre:jazz NOT history  

- artists:("Miles Davis" "John Coltrane")

複数の文字列を 1 つのエンティティとして評価する場合は、必ず両方の文字列を引用符で囲んでください。この場合は、`artists` フィールドで 2 人の異なるアーティストを検索します。  

`fieldName:searchExpression` に指定されるフィールドは、`searchable` フィールドでなければなりません。  フィールド定義におけるインデックス属性の利用方法の詳細については、「[インデックスの作成](/rest/api/searchservice/create-index)」を参照してください。  

> [!NOTE]
> 各フィールド検索式にはフィールド名が明示的に指定されるため、フィールド検索式を使用する際は `searchFields` パラメーターを使用する必要はありません。 ただし、いくつかの部分で特定のフィールドをスコープにし、他の部分は複数のフィールドに適用できるクエリを実行する場合は、`searchFields` パラメーターを引き続き使用できます。 たとえば、クエリ `search=genre:jazz NOT history&searchFields=description` は、`genre` フィールドの `jazz` のみと一致し、`description` フィールドの `NOT history` と一致します。 `fieldName:searchExpression` に指定されたフィールド名は常に `searchFields` パラメーターに優先するため、この例では `searchFields` パラメーターに `genre` を含める必要はありません。

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a> あいまい検索

あいまい検索を使用すると、2 つ以下の距離条件を満たす用語を最大 50 の用語まで拡張して、似た構造を持つ用語の一致を見つけることができます。 詳細については、[あいまい検索](search-query-fuzzy.md)に関するページを参照してください。

あいまい検索を実行するには、1 つの単語の終わりにチルダ記号 "~" を使用し、編集距離を指定する任意のパラメーターである 0 から 2 (既定値) の値を指定します。 たとえば、"blue~" または "blue~1" を指定すると、"blue"、"blues"、および "glue" が返されます。

あいまい検索を適用できるのは用語だけであり、語句には適用できませんが、複数の部分から成る名前または語句の各用語に個別にチルダを追加できます。 たとえば、"Unviersty~ of~ "Wshington~" は "University of Washington" と一致します。
 
##  <a name="proximity-search"></a><a name="bkmk_proximity"></a> 近接検索

近接検索は、ある文書で互いに近くにある言葉を検索します。 言葉の終わりにチルダ記号 "~" を挿入し、近接境界となる語数を続けます。 たとえば、`"hotel airport"~5` を指定すると、ドキュメント内で互いに 5 語以内にある "hotel" と "airport" という用語が検索されます。  

##  <a name="term-boosting"></a><a name="bkmk_termboost"></a> 用語ブースト

用語ブーストでは、指定用語を含む文書に含まない文書より高い順位が設定されます (ブーストされます)。 これはスコアリング プロファイルとは違います。スコアリング プロファイルは、特定の用語ではなく、特定のフィールドをブーストします。  

次の例はその違いを示しています。 特定のフィールドの一致をブーストするスコアリング プロファイルがあるとします。たとえば、[musicstoreindex の例](index-add-scoring-profiles.md#bkmk_ex)の *genre* です。 用語ブーストでは、特定の用語に他の用語より高い順位を与えます。 たとえば、`rock^2 electronic` を指定すると、genre フィールドに検索用語を含むドキュメントに、インデックスの他の検索可能フィールドより高い順位が与えられます。 さらに、用語のブースト値 (2) の結果、*rock* という検索用語を含むドキュメントには、*electronic* というもう 1 つの用語よりも高い順位が与えられます。  

 用語をブーストするには、キャレット記号 "^" とブースト係数 (数字) を、検索する用語の終わりに使用します。 語句をブーストすることもできます。 ブースト係数が高ければ高いほど、その語句の関連性が他の検索語句に比べて大きくなります。 既定のブースト係数は 1 です。 ブースト係数は正数にする必要がありますが、1 未満 (0.20 など) の数字にすることができます。  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a> 正規表現検索  
 正規表現検索では、[RegExp クラス](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)に関するページで説明されているように、Apache Lucene で有効なパターンに基づいて一致が検出されます。 Azure Cognitive Search では、正規表現はスラッシュ `/` で囲まれます。

 たとえば、"motel" または "hotel" を含むドキュメントを検索するには、`/[mh]otel/` を指定します。 正規表現検索では、単一の単語に対して照合が行われます。

一部のツールと言語では、追加のエスケープ文字要件が適用されます。 JSON の場合、スラッシュを含む文字列は、バック スラッシュでエスケープされます。たとえば "microsoft.com/azure/" は、`search=/.*microsoft.com\/azure\/.*/` になります。この場合、正規の式を設定するのは `search=/.* <string-placeholder>.*/` で、`microsoft.com\/azure\/` はエスケープされたスラッシュを含む文字列です。

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a> ワイルドカード検索

複数 (`*`) または単数 (`?`) の文字のワイルドカード検索で、一般に認識されている構文を使用できます。 たとえば、`search=alpha*` のクエリ式では、"alphanumeric" または "alphabetical" が返されます。 Lucene Query Parser では、これらの文字を語句ではなく 1 つの用語に利用することにご注意ください。

完全な Lucene 構文では、プレフィックス、挿入辞、およびサフィックスの一致がサポートされています。 ただし、プレフィックスの一致だけが必要な場合は、単純な構文を使用できます (プレフィックスの一致は両方でサポートされています)。

文字列の前に `*` や `?` がある場合 (`search=/.*numeric./` など) のサフィックスの一致や挿入辞の一致では、Lucene の完全な構文と正規表現のスラッシュ `/` 区切り記号が必要になります。 検索の最初の文字として * または ? を `/` なしで、用語の最初の文字として、または用語内で使用することはできません。 

> [!NOTE]  
> 原則として、パターン マッチングは低速であるため、用語内の文字シーケンスのトークンを作成するエッジ n-gram トークン化など、別の方法を検討することもできます。 インデックスのサイズは大きくなりますが、パターンの構成やインデックスを作成する文字列の長さによっては、クエリの実行速度が速くなる場合があります。
>

### <a name="impact-of-an-analyzer-on-wildcard-queries"></a>ワイルドカード クエリに対するアナライザーの影響

クエリの解析中には、プレフィックス、サフィックス、ワイルドカードとして定式化されたクエリ、または正規の式は、[語彙分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)をバイパスして、クエリ ツリーにそのまま渡されます。 クエリで指定した形式の文字列がインデックスに含まれている場合に限って、一致が検出されます。 部分一致やパターンをうまく検出するためには、ほとんどの場合、文字列の整合性を維持するインデックスの作成時にアナライザーが必要になります。 詳細については、「[Azure Cognitive Search クエリでの部分一致検索](search-query-partial-matching.md)」を参照してください。

"terminat*" という検索クエリで "terminate"、"termination"、"terminates" などの用語を含む結果を返す状況を考えてください。

en.lucene (English Lucene) アナライザーを使用する場合、各用語の語幹処理が積極的に適用されます。 たとえば、"terminate"、"termination"、"terminates" はすべて、インデックスではトークン "termi" にトークン化されます。 一方、ワイルドカードやあいまい検索を使用するクエリの用語はまったく分析されません。そのため、"terminat*" クエリに一致する結果はありません。

一方、Microsoft analyzers (この場合、en.microsoft アナライザー) はもう少し高度であり、語幹処理の代わりに見出し語選定が使用されます。 つまり、生成されたトークンはすべて正しい英単語になります。 たとえば、"terminate"、"terminates"、"termination" はほとんどの場合、インデックスに全体として残り、ワイルドカードやあいまい検索に多く依存するシナリオに最適です。

## <a name="scoring-wildcard-and-regex-queries"></a> ワイルドカード クエリと正規表現クエリのスコアリング

Azure Cognitive Search は、テキスト クエリで、頻度に基づいたスコアリング ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) を使用します。 ただし、用語のスコープが広くなる可能性があるワイルドカード クエリと正規表現クエリでは、出現頻度が低い用語の一致に対する優先度付けに偏りが発生するのを避けるために、頻度の係数は無視されます。 すべての一致は、ワイルドカード検索と正規表現検索で同等に扱われます。

## <a name="special-characters"></a>特殊文字

場合によっては、"❤" のような絵文字や "€" のような記号などの特殊文字を検索することが必要です。 そのような場合は、使用しているアナライザーによってそれらの文字が除外されないことを確認します。標準アナライザーでは多くの特殊文字が無視され、インデックスから除外されます。

特殊文字をトークン化するアナライザーには、"whitespace" アナライザーが含まれ、ここでは、空白で区切られた任意の文字シーケンスがトークンとして考慮されます (したがって "❤" という文字列はトークンと見なされます)。 また、Microsoft English アナライザー ("en.microsoft") のような言語アナライザーでは、"€" という文字列がトークンとして見なされます。 [アナライザーをテスト](/rest/api/searchservice/test-analyzer)して、特定のクエリに対して生成されるトークンを確認できます。

Unicode 文字を使用する場合は、クエリ URL でシンボルが適切にエスケープされていることを確認します (たとえば、"❤" の場合は、エスケープ シーケンス `%E2%9D%A4+` を使用します)。 Postman では、この変換が自動的に行われます。  

## <a name="precedence-grouping"></a>優先順位 (グループ化)

かっこを使用して、かっこで囲まれたステートメント内に演算子を含むサブクエリを作成できます。 たとえば、`motel+(wifi|luxury)` を指定すると、"motel" という用語と "wifi" または "luxury" (あるいはその両方) を含むドキュメントが検索されます。

フィールドのグループ化は似ていますが、グループ化のスコープを 1 つのフィールドに設定します。 たとえば、`hotelAmenities:(gym+(wifi|pool))` を指定すると、"hotelAmenities" のフィールドで "gym" と "wifi"、または "gym" と "pool" が検索されます。  

## <a name="query-size-limits"></a> クエリ サイズの上限

Azure Cognitive Search に送信できるクエリのサイズには制限があります。 具体的には、最大で 1024 句 (AND、OR、その他で区切られた式) を持つことができます。 クエリ内の個々の用語のサイズにも約 32 KB の制限があります。 アプリケーションがプログラムで検索クエリを生成する場合は、無制限のサイズのクエリが生成されないように設計することをお勧めします。  

## <a name="see-also"></a>関連項目

+ [単純な検索のクエリ例](search-query-simple-examples.md)
+ [完全な Lucene 検索のクエリ例](search-query-lucene-examples.md)
+ [ドキュメントの検索](/rest/api/searchservice/Search-Documents)
+ [フィルターと並べ替えの OData 式の構文](query-odata-filter-orderby-syntax.md)   
+ [Azure Cognitive Search でのシンプルなクエリ構文](query-simple-syntax.md)
