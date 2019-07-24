---
title: Lucene クエリ構文 - Azure Search
description: Azure Search で使用される完全な Lucene 構文のリファレンス。
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/25/2019
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: 64a688df3b6ed8602bb440d72e7f061c5f5893d1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885606"
---
# <a name="lucene-query-syntax-in-azure-search"></a>Azure Search での Lucence クエリ構文
特殊なクエリ形式 (ワイルドカード、あいまい検索、近接検索、正規表現などその他多数) に対し、高度な [Lucene Query Parser](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 構文に基づいて Azure Search に対するクエリを作成できます。 Lucene Query Parser 構文の多くは、[Azure Search でそのまま実装](search-lucene-query-architecture.md)されています。ただし、*範囲検索*は例外で、これは Azure Search では `$filter` 式を介して構築されます。 

## <a name="how-to-invoke-full-parsing"></a>完全な解析を呼び出す方法

`queryType` 検索パラメーターを設定して、使用するパーサーを指定します。 有効な値には `simple|full` が含まれます。`simple` が既定値で、`full` が Lucene の値です。 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>完全な構文を示す例

次の例は、`queryType=full` パラメーターで明らかなように、Lucence クエリ構文を使用してインデックスでドキュメントを検出します。 このクエリは、カテゴリ フィールドに "budget" (低料金) が含まれ、すべての検索可能なフィールドに "recently renovated" (最近改修済み) というフレーズが含まれるホテルを返します。 "recently renovated (最近改修済み)" というフレーズを含むドキュメントは、用語ブースト値 (3) の結果、高いランキングになります。  

`searchMode=all` パラメーターは、この例で関連しています。 クエリに演算子があるときは、通常 `searchMode=all` を設定して、*すべて*の条件が確実に一致するようにします。

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2015-02-28&querytype=full
```

 または、次のように POST を使用します。  

```
POST /indexes/hotels/docs/search?api-version=2015-02-28
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

その他の例については、「[Azure Search でクエリを作成するための Lucene クエリ構文例](search-query-lucene-examples.md)」を参照してください。 すべての可能なクエリ パラメーターの指定に関する詳細については、「[ドキュメントの検索 (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)」を参照してください。

> [!NOTE]  
>  Azure Search では、簡潔なキーワード検索に使用できる単純で堅牢なクエリ言語である、[単純なクエリ構文](query-simple-syntax.md)もサポートされます。  

##  <a name="bkmk_syntax"></a> 構文の基礎  
 次の構文の基礎は、Lucene 構文を使用するすべてのクエリに適用されます。  

### <a name="operator-evaluation-in-context"></a>コンテキストでの演算子評価

配置によって、記号を演算子と解釈するか、または文字列内の 1 つの文字と解釈するかが決まります。

たとえば、Lucene の完全構文では、チルダ (~) はあいまい検索と近接検索の両方に使用されます。 引用符で囲まれた語句の後に配置されると、~ は近接検索を呼び出します。 用語の末尾に配置されると、~ はあいまい検索を呼び出します。

用語内に配置される ("business~analyst" など) と、この文字は演算子と評価されません。 この場合は、このクエリが用語または語句のクエリであると想定して、[字句解析](search-lucene-query-architecture.md#stage-2-lexical-analysis)を使用する[フル テキスト検索](search-lucene-query-architecture.md)によって ~ が削除され、"business~analyst" という用語は 2 つに分割されて、business OR analyst になります。

上記の例ではチルダ (~) ですが、すべての演算子に同じ原則が適用されます。

### <a name="escaping-special-characters"></a>特殊文字のエスケープ

 特殊文字を検索テキストの一部として使用するには、エスケープする必要があります。 特殊文字の前に円記号 (\\) 付けることでエスケープできます。 エスケープが必要な特殊文字としては、以下の文字があります。  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 たとえば、ワイルドカード文字をエスケープするには、\\* を使用します。

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>URL での安全でない文字および予約文字のエンコード

 安全でない文字および予約文字はすべて、URL でエンコードするようにしてください。 たとえば、'#' は、URL 内のフラグメント ID またはアンカー ID のため、安全でない文字です。 この文字を URL で使用する場合は、`%23` にエンコードする必要があります。 '&' と '=' は、予約文字の例です。これらの文字は、Azure Search でパラメーターを区切り、値を指定します。 詳細については、「[RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt)」を参照してください。

 安全でない文字は ``" ` < > # % { } | \ ^ ~ [ ]`` です。 予約文字は `; / ? : @ = + &` です。

### <a name="precedence-operators-grouping-and-field-grouping"></a>優先順位の演算子: グループ化とフィールドのグループ化  
 かっこを使用して、かっこで囲まれたステートメント内に演算子を含むサブクエリを作成できます。 たとえば、`motel+(wifi||luxury)` を指定すると、"motel" という用語と "wifi" または "luxury" (あるいはその両方) を含むドキュメントが検索されます。

フィールドのグループ化は似ていますが、グループ化のスコープを 1 つのフィールドに設定します。 たとえば、`hotelAmenities:(gym+(wifi||pool))` を指定すると、"hotelAmenities" のフィールドで "gym" と "wifi"、または "gym" と "pool" が検索されます。  

### <a name="searchmode-parameter-considerations"></a>SearchMode パラメーターに関する考慮事項  
 「[Azure Search での単純なクエリ構文](query-simple-syntax.md)」で説明されているように、クエリに対する `searchMode` の影響は、Lucene クエリ構文にも同様に適用されます。 つまり、`searchMode` を NOT 演算子と組み合わせて使用した場合、パラメーターの設定による影響が明確でなければ異常と思えるクエリ結果になることがあります。 既定値の `searchMode=any` を保持して、NOT 演算子を使用すると、演算は OR アクションとして計算されます。たとえば、"New York" NOT "Seattle" を指定すると、Seattle 以外のすべての都市が返されます。  

##  <a name="bkmk_boolean"></a> ブール演算子 (AND、OR、NOT) 
 テキスト ブール演算子 (AND、OR、NOT) は、常に大文字で指定します。  

### <a name="or-operator-or-or-"></a>OR 演算子 `OR` または `||`

OR 演算子は、縦棒、つまりパイプ文字です。 たとえば、`wifi || luxury` を指定すると、"wifi" または "luxury" (あるいはその両方) を含むドキュメントが検索されます。 OR は、既定の結合演算子のため、除外することもできます。たとえば、`wifi luxury` は `wifi || luxuery` と同等です。

### <a name="and-operator-and--or-"></a>AND 演算子 `AND`、`&&`、または `+`

AND 演算子は、アンパサンドまたはプラス記号です。 たとえば、`wifi && luxury` と指定すると、"wifi" と "luxury" の両方を含むドキュメントが検索されます。 プラス記号 (+) は、必要な用語に使用されます。 たとえば、`+wifi +luxury` は、両方の用語が 1 つのドキュメントのフィールド内のどこかに表示されている必要があることを規定します。


### <a name="not-operator-not--or--"></a>NOT 演算子 `NOT`、`!`、または `-`

NOT 演算子は、感嘆符またはマイナス記号です。 たとえば、`wifi !luxury` を指定すると、"wifi" という用語を含む、および/または"luxury" を含まないドキュメントが検索されます。 `searchMode` オプションは、+ 演算子または || 演算子が指定されていない場合、NOT 演算子を持つ用語で、クエリ内の他の用語との論理積または論理和をとるかどうかを制御します。 `searchMode` は、`any`(既定値) または `all` のどちらにも設定できることを思い出してください。

`searchMode=any` を使用すると、より多くの結果を含めることによりクエリの再現率が増加し、既定で - は "OR NOT" と解釈されます。 たとえば、`wifi -luxury` は、*wifi* という用語を含むドキュメント、または *luxury* という用語を含まないドキュメントのどちらにも一致します。

`searchMode=all` を使用すると、より少ない結果を含めることによりクエリの精度が上がり、既定で - は "AND NOT" と解釈されます。 たとえば、`wifi -luxury` は、`wifi` という用語を含み、かつ `luxury` という用語を含まないドキュメントに一致します。 これはほぼ間違いなく、- 演算子にとってより直感的な動作です。 したがって、再現率ではなく精度で検索を最適化する場合、*そして*ユーザーが検索で `-` 演算子を頻繁に使用する場合は、`searchMode=any` よりも `searchMode=all` を選択することを検討してください。

##  <a name="bkmk_querysizelimits"></a> クエリ サイズの制限  
 Azure Search に送信できるクエリのサイズには制限があります。 具体的には、最大で 1024 句 (AND、OR、その他で区切られた式) を持つことができます。 クエリ内の個々の用語のサイズにも約 32 KB の制限があります。 アプリケーションがプログラムで検索クエリを生成する場合は、無制限のサイズのクエリが生成されないように設計することをお勧めします。  

##  <a name="bkmk_searchscoreforwildcardandregexqueries"></a> ワイルドカード クエリと正規表現クエリのスコアリング
 Azure Search は、テキスト クエリで、頻度に基づいたスコアリング ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) を使用します。 ただし、用語のスコープが広くなる可能性があるワイルドカード クエリと正規表現クエリでは、出現頻度が低い用語の一致に対する優先度付けに偏りが発生するのを避けるために、頻度の係数は無視されます。 すべての一致は、ワイルドカード検索と正規表現検索で同等に扱われます。

##  <a name="bkmk_fields"></a> フィールド スコープ クエリ  
 `fieldname:searchterm` 構造を指定して、フィールド クエリ操作を定義できます。フィールドは 1 つの単語であり、検索用語も 1 つの単語または語句です。ブール演算子を利用することもあります。 たとえば、次のようになります。  

- genre:jazz NOT history  

- artists:("Miles Davis" "John Coltrane")

  複数の文字列を 1 つのエンティティとして評価する場合は、必ず両方の文字列を引用符で囲んでください。この場合は、`artists` フィールドで 2 人の異なるアーティストを検索します。  

  `fieldname:searchterm` に指定されるフィールドは、`searchable` フィールドでなければなりません。  フィールド定義におけるインデックス属性の利用方法の詳細については、「[インデックスの作成](https://docs.microsoft.com/rest/api/searchservice/create-index)」を参照してください。  

##  <a name="bkmk_fuzzy"></a> あいまい検索  
 あいまい検索では、似たような構造の言い回しの一致が検索されます。 [Lucene ドキュメント](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)によると、あいまい検索は [Damerau-Levenshtein Distance](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) を基盤としています。 あいまい検索では、距離の条件を満たす最大 50 個の用語まで用語を展開できます。 

 あいまい検索を実行するには、1 つの単語の終わりにチルダ記号 "~" を使用し、編集距離を指定する任意のパラメーターである 0 から 2 (既定値) の値を指定します。 たとえば、"blue~" または "blue~1" を指定すると、"blue"、"blues"、および "glue" が返されます。

 あいまい検索を適用できるのは用語だけであり、語句には適用できませんが、複数の部分から成る名前または語句の各用語に個別にチルダを追加できます。 たとえば、"Unviersty~ of~ "Wshington~" は "University of Washington" と一致します。
 

##  <a name="bkmk_proximity"></a> 近接検索  
 近接検索は、ある文書で互いに近くにある言葉を検索します。 言葉の終わりにチルダ記号 "~" を挿入し、近接境界となる語数を続けます。 たとえば、`"hotel airport"~5` を指定すると、ドキュメント内で互いに 5 語以内にある "hotel" と "airport" という用語が検索されます。  


##  <a name="bkmk_termboost"></a> 用語ブースト  
 用語ブーストでは、指定用語を含む文書に含まない文書より高い順位が設定されます (ブーストされます)。 これはスコアリング プロファイルとは違います。スコアリング プロファイルは、特定の用語ではなく、特定のフィールドをブーストします。  

次の例はその違いを示しています。 特定のフィールドの一致をブーストするスコアリング プロファイルがあるとします。たとえば、[musicstoreindex の例](index-add-scoring-profiles.md#bkmk_ex)の *genre* です。 用語ブーストでは、特定の用語に他の用語より高い順位を与えます。 たとえば、`rock^2 electronic` を指定すると、genre フィールドに検索用語を含むドキュメントに、インデックスの他の検索可能フィールドより高い順位が与えられます。 さらに、用語のブースト値 (2) の結果、*rock* という検索用語を含むドキュメントには、*electronic* というもう 1 つの用語よりも高い順位が与えられます。  

 用語をブーストするには、キャレット記号 "^" とブースト係数 (数字) を、検索する用語の終わりに使用します。 語句をブーストすることもできます。 ブースト係数が高ければ高いほど、その語句の関連性が他の検索語句に比べて大きくなります。 既定のブースト係数は 1 です。 ブースト係数は正数にする必要がありますが、1 未満 (0.20 など) の数字にすることができます。  

##  <a name="bkmk_regex"></a> 正規表現検索  
 正規表現検索では、スラッシュ "/" の間のコンテンツに基づいて一致が検索されます。[RegExp](https://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html) クラスに詳細があります。  

 たとえば、"motel" または "hotel" を含むドキュメントを検索するには、`/[mh]otel/` を指定します。  正規表現検索では、単一の単語に対して照合が行われます。   

##  <a name="bkmk_wildcard"></a> ワイルドカード検索  
 複数 (*) または単数 (?) の文字のワイルドカード検索で、一般に認識されている構文を使用できます。 Lucene Query Parser では、これらの文字を語句ではなく 1 つの用語に利用することにご注意ください。  

 たとえば、"note" のプレフィックスを持つ単語 ("notebook" や "notepad") を含むドキュメントを検索するには、"note*" と指定します。  

> [!NOTE]  
>  検索の最初の文字として * または ? を使用することはできません。  
>  ワイルドカード検索のクエリでは、テキスト分析は実行されません。 クエリ時、ワイルドカード クエリの用語は、検索インデックス内の分析済みの用語と比較されて、展開されます。

## <a name="see-also"></a>関連項目  

+ [ドキュメントの検索](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [フィルターと並べ替えの OData 式の構文](query-odata-filter-orderby-syntax.md)   
+ [Azure Search での単純なクエリ構文](query-simple-syntax.md)   
