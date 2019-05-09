---
title: 単純なクエリ構文 - Azure Search
description: Azure Search のフルテキスト検索クエリに使用する単純なクエリ構文のリファレンスです。
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
ms.openlocfilehash: 99729141e5e1478f45ad385cf671c44a8e08f21a
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437494"
---
# <a name="simple-query-syntax-in-azure-search"></a>Azure Search での単純なクエリ構文
Azure Search は、2 つの Lucene ベースのクエリ言語を実装します。[Simple Query Parser](https://lucene.apache.org/core/4_7_0/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) と [Lucene Query Parser](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) です。 Azure Search では、単純なクエリ構文では fuzzy/slop のオプションは除外されます。  

> [!NOTE]  
>  Azure Search では、より複雑なクエリには [Lucene クエリ構文](query-lucene-syntax.md)という代替方法が用意されています。 クエリ解析のアーキテクチャと各構文の利点の詳細については、「[Azure Search のフルテキスト検索のしくみ](search-lucene-query-architecture.md)」を参照してください。

## <a name="how-to-invoke-simple-parsing"></a>単純な解析を呼び出す方法

簡単な構文は既定値です。 呼び出しが必要なのは、フルから単純に構文をリセットする場合のみです。 構文を明示的に設定するには、`queryType` 検索パラメーターを使用します。 有効な値には `simple|full` が含まれます。`simple` が既定値で、`full` が Lucene の値です。 

## <a name="query-behavior-anomalies"></a>クエリの動作の異常

1 つ以上の用語を含むテキストはすべて、クエリ実行の有効な開始ポイントと見なされます。 Azure Search は、テキストの分析中に検出されたバリエーションを含め、それらの用語の一部またはすべてを含むドキュメントをマッチングします。 

単純に思われるかもしれませんが、Azure Search でのクエリ実行には、入力文字列に追加される用語および演算子が増えるにつれて、検索結果が減るのではなく増えるという、予期しない結果を生み出す*可能性がある* 1 つの側面があります。 この拡張が実際に行われるかどうかは、NOT 演算子の組み込みと、AND または OR の動作の観点から NOT の解釈方法を決定する `searchMode` パラメーター設定の組み合わせで決まります。 既定値の `searchMode=Any` と NOT 演算子を指定すると、演算は OR アクションとして計算されます。たとえば、`"New York" NOT Seattle` を指定すると、Seattle 以外のすべての都市が返されます。  

通常、これらの動作は、コンテンツを検索するアプリケーションのユーザー対話パターンでよく見られます。そこでは、組み込みナビゲーション構造が多い e コマースのサイトとは対照的に、ユーザーがクエリに演算子を含める可能性が高くなります。 詳細については、「[NOT 演算子](#not-operator)」を参照してください。 

## <a name="boolean-operators-and-or-not"></a>ブール演算子 (AND、OR、NOT) 

クエリ文字列に演算子を埋め込んで、一致するドキュメントの検出に使用する豊富な条件セットを作成することができます。 

### <a name="and-operator-"></a>AND 演算子 `+`

AND 演算子はプラス記号です。 たとえば、`wifi+luxury` を指定すると、`wifi` と `luxury` の両方を含むドキュメントが検索されます。

### <a name="or-operator-"></a>OR 演算子 `|`

OR 演算子は、縦棒、つまりパイプ文字です。 たとえば、`wifi | luxury` を指定すると、 `wifi` または `luxury` (あるいはその両方) を含むドキュメントが検索されます。

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT 演算子 `-`

NOT 演算子はマイナス記号です。 たとえば、`wifi –luxury` を指定すると、`wifi` という用語を含む、および/または `luxury` を含まないドキュメントが検索されます ("および/または" は `searchMode` によって制御されます)。

> [!NOTE]  
>  `searchMode` オプションは、`+` 演算子または `|` 演算子が指定されていない場合、NOT 演算子を持つ用語で、クエリ内の他の用語との論理積または論理和をとるかどうかを制御します。 `searchMode` は、`any`(既定値) または `all` のどちらにも設定できることを思い出してください。 `any` を使用すると、より多くの結果を含めることによりクエリの再現率が増加し、既定で `-` は "OR NOT" と解釈されます。 たとえば、`wifi -luxury` は、`wifi` という用語を含むドキュメント、または `luxury` という用語を含まないドキュメントのどちらにも一致します。 `all` を使用すると、より少ない結果を含めることによりクエリの精度が上がり、既定で - は "AND NOT" と解釈されます。 たとえば、`wifi -luxury` は、`wifi` という用語を含み、かつ "luxury" という用語を含まないドキュメントに一致します。 これはほぼ間違いなく、`-` 演算子にとってより直感的な動作です。 したがって、再現率ではなく精度で検索を最適化する場合、*そして*ユーザーが検索で `-` 演算子を頻繁に使用する場合は、`searchMode=any` よりも `searchMode=all` を選択することを検討してください。

## <a name="suffix-operator"></a>サフィックス演算子

サフィックス演算子はアスタリスク `*` です。 たとえば、`lux*` は、`lux` で始まる用語を持つドキュメントを検索します。大文字と小文字は区別されません。  

## <a name="phrase-search-operator"></a>語句検索演算子

語句演算子では、語句を引用符 `" "` で囲みます。 たとえば、`Roach Motel` (引用符なし) は、`Roach` と `Motel` (またはそのいずれか) を含むドキュメントを検索します。その際、語句が指定されている場所と順序は関係ありません。一方、`"Roach Motel"` (引用符あり) は、その語句全体を一緒に、その順序で含むドキュメントだけに一致します (テキスト分析は適用されます)。

## <a name="precedence-operator"></a>優先順位演算子

優先順位演算子では、括弧 `( )` で文字列を囲みます。 たとえば、`motel+(wifi | luxury)` を指定すると、"motel" という用語と `wifi` または `luxury` (あるいはその両方) を含むドキュメントが検索されます。  

## <a name="escaping-search-operators"></a>検索演算子のエスケープ  

 上記の記号を検索テキストの実際の一部として使用するためには、円記号を前に付けてエスケープする必要があります。 たとえば、`luxury\+hotel` と指定すると `luxury+hotel` という用語になります。 より標準的なケースで操作を簡単にするために、この規則には、エスケープが必要ない次の 2 つの例外があります。  

- NOT 演算子 `-` のエスケープが必要なのは、この演算子が用語の真ん中に配置されているのではなく、空白の後の最初の文字である場合のみになります。 たとえば、`wi-fi` は 1 つの用語です。一方、GUID (`3352CDD0-EF30-4A2E-A512-3B30AF40F3FD` など) は 1 つのトークンとして扱われます。
- サフィックス演算子 `*` のエスケープが必要なのは、この演算子が用語の真ん中に配置されているのではなく、空白の前の最後の文字の場合のみになります。 たとえば、`wi*fi` は 1 つのトークンとして扱われます。

> [!NOTE]  
>  エスケープでトークンは一緒に保持されますが、分析モードによっては、テキスト分析で分割される可能性があります。 詳細については、「[言語サポート &#40;Azure Search Service REST API&#41;](index-add-language-analyzers.md)」を参照してください。  

## <a name="see-also"></a>関連項目  

+ [ドキュメントの検索 &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene クエリ構文](query-lucene-syntax.md)
+ [OData 式の構文](query-odata-filter-orderby-syntax.md) 
