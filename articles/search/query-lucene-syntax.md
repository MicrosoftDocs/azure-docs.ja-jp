---
title: Lucene クエリ構文
titleSuffix: Azure Cognitive Search
description: ワイルドカード、ファジー検索、RegEx などの高度なクエリ構成のために Azure Cognitive Search で使用される、完全な Lucene クエリ構文のリファレンス。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/23/2020
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 6ea8bc2551df4f85e4b856dc9cf1c06a9bd571fd
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923451"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search での Lucence クエリ構文

特殊なクエリ形式 (ワイルドカード、あいまい検索、近接検索、正規表現などその他多数) に対し、高度な [Lucene Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 構文に基づいて Azure Cognitive Search に対するクエリを作成できます。 Lucene Query Parser 構文の多くは、[Azure Cognitive Search でそのまま実装](search-lucene-query-architecture.md)されています。ただし、*範囲検索*は例外で、これは Azure Cognitive Search では `$filter` 式を介して構築されます。 

> [!NOTE]
> 完全な Lucene 構文は、[Search Documents](/rest/api/searchservice/search-documents) API の **search** パラメーターで渡されるクエリ式に使用されます。その API の [$filter](search-filters.md) パラメーターで使用される [OData 構文](query-odata-filter-orderby-syntax.md)と混同しないでください。 これらのさまざまな構文には、クエリの作成や文字列のエスケープなどを行うための独自の規則があります。

## <a name="invoke-full-parsing"></a>完全な解析の呼び出し

`queryType` 検索パラメーターを設定して、使用するパーサーを指定します。 有効な値には `simple|full` が含まれます。`simple` が既定値で、`full` が Lucene の値です。 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>完全な構文を示す例

次の例は、`queryType=full` パラメーターで明らかなように、Lucence クエリ構文を使用してインデックスでドキュメントを検出します。 このクエリは、カテゴリ フィールドに "budget" (低料金) が含まれ、すべての検索可能なフィールドに "recently renovated" (最近改修済み) というフレーズが含まれるホテルを返します。 "recently renovated (最近改修済み)" というフレーズを含むドキュメントは、用語ブースト値 (3) の結果、高いランキングになります。  

`searchMode=all` パラメーターは、この例で関連しています。 クエリに演算子があるときは、通常 `searchMode=all` を設定して、*すべて*の条件が確実に一致するようにします。

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2020-06-30&querytype=full
```

 または、次のように POST を使用します。  

```
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

その他の例については、[Azure Cognitive Search でクエリを作成するための Lucene クエリ構文例](search-query-lucene-examples.md)に関する記事を参照してください。 すべての可能なクエリ パラメーターの指定に関する詳細については、「[ドキュメントの検索 &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/Search-Documents)」を参照してください。

> [!NOTE]  
>  Azure Cognitive Search では、簡潔なキーワード検索に使用できる単純で堅牢なクエリ言語である、[単純なクエリ構文](query-simple-syntax.md)もサポートされます。  

##  <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a> 構文の基礎  

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

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a> クエリ サイズの上限

 Azure Cognitive Search に送信できるクエリのサイズには制限があります。 具体的には、最大で 1024 句 (AND、OR、その他で区切られた式) を持つことができます。 クエリ内の個々の用語のサイズにも約 32 KB の制限があります。 アプリケーションがプログラムで検索クエリを生成する場合は、無制限のサイズのクエリが生成されないように設計することをお勧めします。  

### <a name="precedence-operators-grouping"></a>優先順位の演算子 (グループ化)

 かっこを使用して、かっこで囲まれたステートメント内に演算子を含むサブクエリを作成できます。 たとえば、`motel+(wifi||luxury)` を指定すると、"motel" という用語と "wifi" または "luxury" (あるいはその両方) を含むドキュメントが検索されます。

フィールドのグループ化は似ていますが、グループ化のスコープを 1 つのフィールドに設定します。 たとえば、`hotelAmenities:(gym+(wifi||pool))` を指定すると、"hotelAmenities" のフィールドで "gym" と "wifi"、または "gym" と "pool" が検索されます。  

##  <a name="boolean-search"></a><a name="bkmk_boolean"></a> ブール検索

 テキスト ブール演算子 (AND、OR、NOT) は、常に大文字で指定します。  

### <a name="or-operator-or-or-"></a>OR 演算子 `OR` または `||`

OR 演算子は、縦棒、つまりパイプ文字です。 たとえば、`wifi || luxury` を指定すると、"wifi" または "luxury" (あるいはその両方) を含むドキュメントが検索されます。 OR は、既定の結合演算子のため、除外することもできます。たとえば、`wifi luxury` は `wifi || luxury` と同等です。

### <a name="and-operator-and--or-"></a>AND 演算子 `AND`、`&&`、または `+`

AND 演算子は、アンパサンドまたはプラス記号です。 たとえば、`wifi && luxury` と指定すると、"wifi" と "luxury" の両方を含むドキュメントが検索されます。 プラス記号 (+) は、必要な用語に使用されます。 たとえば、`+wifi +luxury` は、両方の用語が 1 つのドキュメントのフィールド内のどこかに表示されている必要があることを規定します。

### <a name="not-operator-not--or--"></a>NOT 演算子 `NOT`、`!`、または `-`

NOT 演算子はマイナス記号です。 たとえば、`wifi –luxury` を指定すると、`wifi` という用語を含む、または `luxury` を含まないドキュメントが検索されます。

クエリ要求の **searchMode** パラメーターでは、NOT 演算子がついた用語を、クエリ内の他の用語と AND 演算するか OR 演算するかどうかが制御されます (他の用語に `+` または `|` 演算子がないと仮定します)。 有効な値は、`any` または `all` です。

`searchMode=any` にすると、より多くの結果を含めることによりクエリの再現率が増加し、既定で `-` は "OR NOT" と解釈されます。 たとえば、`wifi -luxury` は、`wifi` という用語を含むドキュメント、または `luxury` という用語を含まないドキュメントのどちらにも一致します。

`searchMode=all` にすると、より少ない結果を含めることによりクエリの精度が上がり、既定で - は "AND NOT" と解釈されます。 たとえば、`wifi -luxury` は、`wifi` という用語を含み、かつ "luxury" という用語を含まないドキュメントに一致します。 これはほぼ間違いなく、`-` 演算子にとってより直感的な動作です。 したがって、再現率ではなく精度で検索を最適化し、"*かつ*" ユーザーが検索で `-` 演算子を頻繁に使用する場合は、`searchMode=any` よりも `searchMode=all` を選択することを検討してください。

**searchMode** 設定を決定するときは、さまざまなアプリケーションでのクエリのユーザー操作パターンを検討してください。 情報を検索するユーザーは、より多くの組み込みのナビゲーション構造を持つ eコマース サイトとは対照的に、クエリに演算子を含める可能性が高くなります。

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

##  <a name="scoring-wildcard-and-regex-queries"></a><a name="bkmk_searchscoreforwildcardandregexqueries"></a> ワイルドカード クエリと正規表現クエリのスコアリング

Azure Cognitive Search は、テキスト クエリで、頻度に基づいたスコアリング ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) を使用します。 ただし、用語のスコープが広くなる可能性があるワイルドカード クエリと正規表現クエリでは、出現頻度が低い用語の一致に対する優先度付けに偏りが発生するのを避けるために、頻度の係数は無視されます。 すべての一致は、ワイルドカード検索と正規表現検索で同等に扱われます。

## <a name="see-also"></a>関連項目

+ [単純な検索のクエリ例](search-query-simple-examples.md)
+ [完全な Lucene 検索のクエリ例](search-query-lucene-examples.md)
+ [ドキュメントの検索](/rest/api/searchservice/Search-Documents)
+ [フィルターと並べ替えの OData 式の構文](query-odata-filter-orderby-syntax.md)   
+ [Azure Cognitive Search でのシンプルなクエリ構文](query-simple-syntax.md)