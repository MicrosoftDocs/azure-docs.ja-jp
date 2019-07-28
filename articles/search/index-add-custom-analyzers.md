---
title: カスタム アナライザーを追加する - Azure Search
description: Azure Search のフルテキスト検索クエリで使用されるテキスト トークナイザーと文字フィルターを変更します。
ms.date: 02/14/2019
services: search
ms.service: search
ms.topic: conceptual
author: yahnoosh
ms.author: jlembicz
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
ms.openlocfilehash: b670b5aeffcd699226a088d8db21a12dbaf40042
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242808"
---
# <a name="add-custom-analyzers-to-an-azure-search-index"></a>Azure Search のインデックスにカスタム アナライザーを追加する

*カスタム アナライザー*は特定の種類の[テキスト アナライザー](search-analyzers.md)であり、既存のトークナイザーとオプションのフィルターのユーザー定義の組み合わせで構成されます。 新しい方法でトークナイザーとフィルターを組み合わせることにより、特定の結果を実現するために、検索エンジンでテキスト処理をカスタマイズできます。 たとえば、"*文字フィルター*" でカスタム アナライザーを作成し、テキスト入力をトークン化する前に HTML マークアップを削除できます。

 複数のカスタム アナライザーを定義してフィルターの組み合わせを変えることはできますが、インデックス作成時の解析と検索時の解析に使用できるアナライザーは、各フィールドにつきそれぞれ 1 つだけです。 カスタム アナライザーがどのようなものであるかについては、「[カスタム アナライザーの例](search-analyzers.md#Custom-analyzer-example)」をご覧ください。

## <a name="overview"></a>概要

 [フルテキスト検索エンジン](search-lucene-query-architecture.md)の役割を簡単に説明すると、クエリと取得を効率的に行えるようにドキュメントを処理して格納することです。 大まかに言えば、ドキュメントから重要な単語を抽出して、インデックスに配置し、インデックスを使用して指定されたクエリの単語に一致するドキュメントを検索します。 ドキュメントと検索クエリから単語を抽出するプロセスを、"*字句解析*" と呼びます。 字句解析を実行するコンポーネントは、"*アナライザー*" と呼ばれます。

 Azure Search では、[アナライザー](#AnalyzerTable)の表で示されている定義済みの言語に依存しないアナライザー、または[言語アナライザー &#40;Azure Search Service REST API&#41;](index-add-language-analyzers.md) に列記されている言語固有のアナライザーから、選択できます。 また、独自のカスタム アナライザーを定義することもできます。  

 カスタム アナライザーを使用すると、テキストをインデックス付け可能で検索可能なトークンに変換するプロセスを制御できます。 それは、定義済みの 1 つのトークナイザー、1 つ以上のトークン フィルター、1 つ以上の文字フィルターで構成されるユーザー定義の構成です。 トークナイザーではテキストがトークンに分割され、トークン フィルターではトークナイザーによって生成されたトークンが変更されます。 文字フィルターは、トークナイザーによって処理される前に、入力テキストを準備するために適用されます。 たとえば、文字フィルターを使用して、特定の文字や記号を置き換えることができます。

 カスタム アナライザーが利用される一般的なシナリオの例を次に示します。  

- 音声検索。 発音フィルターを追加することによって、単語の綴りではなく発音に基づく検索に対応します。  

- 字句解析の無効化。 解析の対象外とする検索可能なフィールドをキーワード アナライザーで作成します。  

- 高速なプレフィックス/サフィックス検索。 Edge N-gram トークン フィルターを追加し、単語のプレフィックスをインデックス化することによって、高速なプレフィックス マッチングに対応します。 リバース トークン フィルターを組み合わせることで、サフィックス マッチングを行うことができます。  

- カスタム トークン化。 たとえば空白文字を区切りとして文をトークンに分割するには、Whitespace トークナイザーを使用します  

- ASCII フォールディング。 検索語句に含まれる ö や ê などの付加記号を正規化するための標準 ASCII フォールディング フィルターを追加します。  

  このページでは、サポートされているアナライザー、トークナイザー、トークン フィルター、文字フィルターの一覧を示します。 また、インデックスの定義に対する変更の説明と使用例も示されています。 Azure Search の実装に利用されている基礎技術の背景については、 [解析パッケージの概要 (Lucene)](https://lucene.apache.org/core/4_10_0/core/org/apache/lucene/codecs/lucene410/package-summary.html)を参照してください。 アナライザーの構成の例については、[Azure Search でのアナライザーの追加](search-analyzers.md#examples)に関するセクションをご覧ください。

## <a name="validation-rules"></a>検証規則  
 アナライザー、トークナイザー、トークン フィルター、文字フィルターの名前は一意でなければならず、事前定義済みのアナライザー、トークナイザー、トークン フィルター、文字フィルターと同じにすることはできません。 既に使用されている名前については、「[プロパティ リファレンス](#PropertyReference)」をご覧ください。

## <a name="create-custom-analyzers"></a>カスタム アナライザーの作成
 インデックスを作成するときに、カスタム アナライザーを定義できます。 このセクションでは、カスタム アナライザーを指定する構文について説明します。 [Azure Search でのアナライザーの追加](search-analyzers.md#examples)に関するセクションのサンプル定義を見て、構文を理解することもできます。  

 アナライザーの定義には、名前、種類、1 つ以上の文字フィルター、最大で 1 つのトークナイザー、トークン化後の処理のための 1 つ以上のトークン フィルターが含まれます。 文字フィルターは、トークン化の前に適用されます。 トークン フィルターと文字フィルターは、左から右に適用されます。

 `tokenizer_name` はトークナイザーの名前、`token_filter_name_1` と `token_filter_name_2` はトークン フィルターの名前、`char_filter_name_1` と `char_filter_name_2` は文字フィルターの名前です (有効な値については、[トークナイザー](#Tokenizers)、[トークン フィルター](#TokenFilters)、文字フィルターの表をご覧ください)。

アナライザーの定義は、より大きなインデックスの一部です。 インデックスの REST については、[Create Index API (インデックス作成 API)](https://docs.microsoft.com/rest/api/searchservice/create-index) に関する記事をご覧ください。

```
"analyzers":(optional)[
   {
      "name":"name of analyzer",
      "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenizer":"tokenizer_name",
      "tokenFilters":[
         "token_filter_name_1",
         "token_filter_name_2"
      ]
   },
   {
      "name":"name of analyzer",
      "@odata.type":"#analyzer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenizers":(optional)[
   {
      "name":"tokenizer_name",
      "@odata.type":"#tokenizer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

> [!NOTE]  
>  作成したカスタム アナライザーは、Azure portal には公開されません。 カスタム アナライザーを追加する唯一の方法は、インデックスを定義するときにコードで API を呼び出すことです。  

 インデックス定義内で、インデックス作成要求本文内のどこにでも次のセクションを配置できますが、通常は末尾に置きます。  

```
{
  "name": "name_of_index",
  "fields": [ ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "analyzers":(optional)[ ],
  "charFilters":(optional)[ ],
  "tokenizers":(optional)[ ],
  "tokenFilters":(optional)[ ]
}
```

文字フィルター、トークナイザー、トークン フィルターの定義は、カスタム オプションを設定する場合にのみ、インデックスに追加されます。 既存のフィルターまたはトークナイザーをそのまま使用するには、アナライザーの定義内で名前を使用して指定します。

<a name="Testing custom analyzers"></a>

## <a name="test-custom-analyzers"></a>カスタム アナライザーのテスト

[REST API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) の**アナライザー テスト操作**を使用して、指定したテキストがアナライザーによってどのようにトークンに分割されるかを確認できます。

**要求**
```
  POST https://[search service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
  Content-Type: application/json
    api-key: [admin key]

  {
     "analyzer":"my_analyzer",
     "text": "Vis-à-vis means Opposite"
  }
```
**応答**
```
  {
    "tokens": [
      {
        "token": "vis_a_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "vis_à_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "means",
        "startOffset": 10,
        "endOffset": 15,
        "position": 1
      },
      {
        "token": "opposite",
        "startOffset": 16,
        "endOffset": 24,
        "position": 2
      }
    ]
  }
```

## <a name="update-custom-analyzers"></a>カスタム アナライザーの更新

アナライザー、トークナイザー、トークン フィルター、文字フィルターは、いったん定義すると変更できません。 インデックスの更新要求で `allowIndexDowntime` フラグが true に設定されている場合に限り、既存のインデックスに新しい定義を追加できます。

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

この操作を行うと、インデックスは少なくとも数秒間オフラインになるため、インデックス付けとクエリの要求は失敗します。 インデックスを更新した後の数分間、インデックスが非常に大きい場合はさらに長く、インデックスのパフォーマンスと書き込み可用性が損なわれる場合がありますが、これらの影響は一時的であり、しばらくすると自動的に解決します。

 <a name="ReferenceIndexAttributes"></a>

## <a name="analyzer-reference"></a>アナライザー リファレンス

次のテーブルでは、インデックス定義のアナライザー、トークナイザー、トークン フィルター、文字フィルターの各セクションで使用される構成プロパティの一覧を示します。 インデックスのアナライザー、トークナイザー、フィルターの構造は、これらの属性で構成されます。 値の割り当てについては、「[プロパティ リファレンス](#PropertyReference)」をご覧ください。

### <a name="analyzers"></a>アナライザー

アナライザーの場合、インデックス属性は定義済みアナライザーまたはカスタム アナライザーのどちらを使用するかにより異なります。

#### <a name="predefined-analyzers"></a>定義済みアナライザー

|||  
|-|-|  
|Name|アルファベット、数字、空白、ダッシュ、アンダースコアのみを含める必要があります。また、最初と最後の文字は英数字にする必要があり、長さは 128 文字までに制限されています。|  
|Type|サポートされているアナライザーの一覧に含まれるアナライザーの種類です。 下記の[アナライザー](#AnalyzerTable)の表の **analyzer_type** 列をご覧ください。|  
|オプション|下記の[アナライザー](#AnalyzerTable)の表に列記されている定義済みアナライザーの有効なオプションを指定する必要があります。|  

#### <a name="custom-analyzers"></a>カスタム アナライザー

|||  
|-|-|  
|Name|アルファベット、数字、空白、ダッシュ、アンダースコアのみを含める必要があります。また、最初と最後の文字は英数字にする必要があり、長さは 128 文字までに制限されています。|  
|Type|"#Microsoft.Azure.Search.CustomAnalyzer" を指定する必要があります。|  
|CharFilters|[文字フィルター](#char-filters-reference)の表に列記されている定義済みの文字フィルターのいずれか、またはインデックスの定義で指定されているカスタム文字フィルターに設定します。|  
|トークナイザー|必須。 下記の[トークナイザー](#Tokenizers)の表に列記されている定義済みのトークナイザーのいずれか、またはインデックスの定義で指定されているカスタム トークナイザーに設定します。|  
|TokenFilters|[トークン フィルター](#TokenFilters)の表に列記されている定義済みのトークン フィルターのいずれか、またはインデックスの定義で指定されているカスタム トークン フィルターに設定します。|  

> [!NOTE]
> 300 文字より長いトークンが生成されないように、カスタム アナライザーを構成する必要があります。 そのようなトークンがあると、ドキュメントのインデックス付けが失敗します。 それらをトリミングまたは無視するには、それぞれ **TruncateTokenFilter** および **LengthTokenFilter** を使用します。  参考として、「[**トークン フィルター**](#TokenFilters)」を確認してください。

<a name="CharFilter"></a>

### <a name="char-filters"></a>文字フィルター

 文字フィルターは、トークナイザーによって処理される前に、入力テキストを準備するために使用します。 たとえば、それらを使用して、特定の文字や記号を置き換えることができます。 1 つのカスタム アナライザーで、複数の文字フィルターを割り当てることができます。 文字フィルターは、その指定順に実行されます。  

|||  
|-|-|  
|Name|アルファベット、数字、空白、ダッシュ、アンダースコアのみを含める必要があります。また、最初と最後の文字は英数字にする必要があり、長さは 128 文字までに制限されています。|  
|Type|サポートされている文字フィルターの一覧に含まれる文字フィルターの種類です。 下記の[文字フィルター](#char-filters-reference)の表の **char_filter_type** 列をご覧ください。|  
|オプション|指定した[文字フィルター](#char-filters-reference)の種類で有効なオプションを使用する必要があります。|  

### <a name="tokenizers"></a>トークナイザー

 トークナイザーでは、連続するテキストがトークンのシーケンスに分割されます (文を単語に分割する場合など)。  

 指定できるトークナイザーは、カスタム アナライザーごとに 1 つだけです。 複数のトークナイザーが必要な場合は、複数のカスタム アナライザーを作成し、インデックス スキーマのフィールドごとに割り当てます。  
カスタム アナライザーでは、既定のオプションまたはカスタマイズされたオプションの定義済みトークナイザーを使用できます。  

|||  
|-|-|  
|Name|アルファベット、数字、空白、ダッシュ、アンダースコアのみを含める必要があります。また、最初と最後の文字は英数字にする必要があり、長さは 128 文字までに制限されています。|  
|Type|サポートされているトークナイザーの一覧のトークナイザー名を使用します。 下記の[トークナイザー](#Tokenizers)の表の **tokenizer_type** 列をご覧ください。|  
|オプション|下記の[トークナイザー](#Tokenizers)の表で示されている特定のトークナイザーの種類の有効なオプションを指定する必要があります。|  

### <a name="token-filters"></a>トークン フィルター

 トークン フィルターは、トークナイザーによって生成されたトークンを除外したり加工したりする目的で使用されます。 たとえば、すべての文字を小文字に変換する lowercase フィルターを指定することができます。   
トークン フィルターは、カスタム アナライザーの中で複数割り当てることができます。 トークン フィルターは、その指定順に実行されます。  

|||  
|-|-|  
|Name|アルファベット、数字、空白、ダッシュ、アンダースコアのみを含める必要があります。また、最初と最後の文字は英数字にする必要があり、長さは 128 文字までに制限されています。|  
|Type|サポートされているトークン フィルターの一覧のトークン フィルター名を使用します。 下記の[トークン フィルター](#TokenFilters)の表の **token_filter_type** 列をご覧ください。|  
|オプション|特定のトークン フィルターの種類の[トークン フィルター](#TokenFilters)を使用する必要があります。|  

<a name="PropertyReference"></a>  

## <a name="property-reference"></a>プロパティ リファレンス

このセクションでは、インデックスのカスタム アナライザー、トークナイザー、文字フィルター、またはトークン フィルターの定義で指定されている属性の有効な値を示します。 Apache Lucene を使用して実装されるアナライザー、トークナイザー、フィルターについては、Lucene の API ドキュメントへのリンクを示してあります。

<a name="AnalyzerTable"></a>

###  <a name="predefined-analyzers-reference"></a>定義済みアナライザー リファレンス

|**analyzer_name**|**analyzer_type**  <sup>1</sup>|**説明とオプション**|  
|-|-|-|  
|[keyword](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (種類は、オプションが使用可能な場合にだけ適用されます) |フィールドの内容全体を 1 つのトークンとして扱います。 これは、郵便番号、ID、製品名などのデータで役立ちます。|  
|[pattern](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PatternAnalyzer|正規表現のパターンを使用してテキストを用語に柔軟に分割します。<br /><br /> **オプション**<br /><br /> lowercase (型: bool) - 用語が小文字かどうかを決定します。 既定値は true です。<br /><br /> [pattern](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (型: string) - トークンの区切り記号に一致する正規表現パターン。 既定値は \w+ です。<br /><br /> [flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (型: string) - 正規表現フラグ。 既定値は空の文字列です。 使用できる値は以下の通りです。CANON_EQ、CASE_INSENSITIVE、COMMENTS、DOTALL、LITERAL、MULTILINE、UNICODE_CASE、UNIX_LINES<br /><br /> stopwords (型: string 配列) - ストップワードのリスト。 既定値は空のリストです。|  
|[simple](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(種類は、オプションが使用可能な場合にだけ適用されます) |非文字でテキストが分割され、それらが小文字に変換されます。 |  
|[standard](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) <br />(standard.lucene とも呼ばれます)|StandardAnalyzer|標準トークナイザー、小文字フィルター、ストップ フィルターで構成される標準の Lucene アナライザー。<br /><br /> **オプション**<br /><br /> maxTokenLength (型: int) - 最大トークン長。 既定値は 255 です。 最大長より長いトークンは分割されます。 使用できる最大トークン長は、300 文字です。<br /><br /> stopwords (型: string 配列) - ストップワードのリスト。 既定値は空のリストです。|  
|standardasciifolding.lucene|(種類は、オプションが使用可能な場合にだけ適用されます) |Ascii フォールディング フィルターの標準アナライザー。 |  
|[stop](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|非文字でテキストが分割され、小文字とストップワードのトークン フィルターが適用されます。<br /><br /> **オプション**<br /><br /> stopwords (型: string 配列) - ストップワードのリスト。 既定値は、英語の定義済みリストです。 |  
|[whitespace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(種類は、オプションが使用可能な場合にだけ適用されます) |空白文字トークナイザーを使用するアナライザー。 255 文字より長いトークンは分割されます。|  

 <sup>1</sup> コードでは、アナライザーの種類に常にプレフィックス "#Microsoft.Azure.Search" が付きます。たとえば、"PatternAnalyzer" は実際には "#Microsoft.Azure.Search.PatternAnalyzer" と指定します。 簡潔にするためプレフィックスを省略しましたが、コードではプレフィックスが必要です。 
 
analyzer_type は、カスタマイズ可能なアナライザーに対してのみ提供されます。 keyword アナライザーのようにオプションがない場合は、関連付けられる #Microsoft.Azure.Search の種類はありません。


<a name="CharFilter"></a>

###  <a name="char-filters-reference"></a>文字フィルター リファレンス

次の表で、Apache Lucene を使用して実装されている文字フィルターについては、Lucene の API ドキュメントへのリンクを示してあります。

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**説明とオプション**|  
|-|-|-|
|[html_strip](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(種類は、オプションが使用可能な場合にだけ適用されます)  |HTML の構造の除去を試みる文字フィルター。|  
|[mapping](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|マッピング オプションで定義されたマッピングを適用する文字フィルター。 一致は最長一致です (特定ポイントでの最長パターン マッチングを優先)。 空の文字列での置換が許可されます。<br /><br /> **オプション**<br /><br /> mappings (型: string 配列) - 次の形式のマッピングのリスト: "a=>b" (文字 "a" のすべての出現が文字 "b" で置換されます)。 必須。|  
|[pattern_replace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatternReplaceCharFilter|入力文字列内の文字を置換する文字フィルター。 正規表現を使用して維持する文字のシーケンスが識別され、置換パターンを使用して置換する文字が識別されます。 例: 入力テキスト = "aa  bb aa bb"、pattern="(aa)\\\s+(bb)" replacement="$1#$2"、結果 = "aa#bb aa#bb"。<br /><br /> **オプション**<br /><br /> pattern (型: string) - 必須。<br /><br /> replacement (型: string) - 必須。|  

 <sup>1</sup> コードでは、文字フィルターの種類に常にプレフィックス "#Microsoft.Azure.Search" が付きます。たとえば、"MappingCharFilter" は実際には "#Microsoft.Azure.Search.MappingCharFilter" と指定します。 テーブルの幅を小さくするためプレフィックスを削除しましたが、コードには忘れずに含めてください。 char_filter_type は、カスタマイズ可能なフィルターに対してのみ提供されることに注意してください。 html_strip のようにオプションがない場合は、関連付けられる #Microsoft.Azure.Search の種類はありません。

<a name="Tokenizers"></a>

###  <a name="tokenizers-reference"></a>トークナイザー リファレンス

次の表で、Apache Lucene を使用して実装されているトークナイザーについては、Lucene の API ドキュメントへのリンクを示してあります。

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**説明とオプション**|  
|-|-|-|  
|[classic](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|ほとんどのヨーロッパ言語のドキュメントの処理に適した文法ベースのトークナイザー。<br /><br /> **オプション**<br /><br /> maxTokenLength (型: int) - 最大トークン長。 既定値は255、最大値は300。 最大長より長いトークンは分割されます。|  
|[edgeNGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|エッジからの入力が指定サイズの n グラムにトークン化されます。<br /><br /> **オプション**<br /><br /> minGram (型: int) - 既定値:1、最大値:300。<br /><br /> maxGram (型: int) - 既定値:2、最大値:300。 minGram より大きい値にする必要があります。<br /><br /> tokenChars (型: string 配列) - トークン内で維持する文字クラス。 使用できる値は以下の通りです。 <br />"letter"、"digit"、"whitespace"、"punctuation"、"symbol"。 既定値は空の配列で、すべての文字が維持されます。 |  
|[keyword_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|入力全体が 1 つのトークンとして生成されます。<br /><br /> **オプション**<br /><br /> maxTokenLength (型: int) - 最大トークン長。 既定値は256、最大値は300。 最大長より長いトークンは分割されます。|  
|[letter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(種類は、オプションが使用可能な場合にだけ適用されます)  |非文字でテキストを分割します。 255 文字より長いトークンは分割されます。|  
|[lowercase](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(種類は、オプションが使用可能な場合にだけ適用されます)  |非文字でテキストが分割され、それらが小文字に変換されます。 255 文字より長いトークンは分割されます。|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| 言語固有のルールを使用してテキストが分割されます。<br /><br /> **オプション**<br /><br /> maxTokenLength (型: int) - 最大トークン長、既定値は255、最大値は300。 最大長より長いトークンは分割されます。 300 文字より長いトークンは、最初に長さ 300 文字のトークンに分割された後、設定されている maxTokenLength に基づいて各トークンが分割されます。<br /><br />isSearchTokenizer (型: bool) - 検索トークナイザーとして使用する場合は true に設定し、インデックス付けトークナイザーとして使用する場合は false に設定します。 <br /><br /> language (型: string) - 使用する言語。既定値は "english" です。 使用できる値は、以下のとおりです。<br />"bangla"、"bulgarian"、"catalan"、"chineseSimplified"、"chineseTraditional"、"croatian"、"czech"、"danish"、"dutch"、"english"、"french"、"german"、"greek"、"gujarati"、"hindi"、"icelandic"、"indonesian"、"italian"、"japanese"、"kannada"、"korean"、"malay"、"malayalam"、"marathi"、"norwegianBokmaal"、"polish"、"portuguese"、"portugueseBrazilian"、"punjabi"、"romanian"、"russian"、"serbianCyrillic"、"serbianLatin"、"slovenian"、"spanish"、"swedish"、"tamil"、"telugu"、"thai"、"ukrainian"、"urdu"、"vietnamese" |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| 言語固有のルールを使用してテキストが分割され、基本フォームに単語が減らされます<br /><br /> **オプション**<br /><br />maxTokenLength (型: int) - 最大トークン長、既定値は255、最大値は300。 最大長より長いトークンは分割されます。 300 文字より長いトークンは、最初に長さ 300 文字のトークンに分割された後、設定されている maxTokenLength に基づいて各トークンが分割されます。<br /><br /> isSearchTokenizer (型: bool) - 検索トークナイザーとして使用する場合は true に設定し、インデックス付けトークナイザーとして使用する場合は false に設定します。<br /><br /> language (型: string) - 使用する言語。既定値は "english" です。 使用できる値は、以下のとおりです。<br />"arabic"、"bangla"、"bulgarian"、"catalan"、"croatian"、"czech"、"danish"、"dutch"、"english"、"estonian"、"finnish"、"french"、"german"、"greek"、"gujarati"、"hebrew"、"hindi"、"hungarian"、"icelandic"、"indonesian"、"italian"、"kannada"、"latvian"、"lithuanian"、"malay"、"malayalam"、"marathi"、"norwegianBokmaal"、"polish"、"portuguese"、"portugueseBrazilian"、"punjabi"、"romanian"、"russian"、"serbianCyrillic"、"serbianLatin"、"slovak"、"slovenian"、"spanish"、"swedish"、"tamil"、"telugu"、"turkish"、"ukrainian"、"urdu" |
|[nGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|入力が指定サイズの n グラムにトークン化されます。<br /><br /> **オプション**<br /><br /> minGram (型: int) - 既定値:1、最大値:300。<br /><br /> maxGram (型: int) - 既定値:2、最大値:300。 minGram より大きい値にする必要があります。 <br /><br /> tokenChars (型: string 配列) - トークン内で維持する文字クラス。 使用できる値: "letter"、"digit"、"whitespace"、"punctuation"、"symbol"。 既定値は空の配列で、すべての文字が維持されます。 |  
|[path_hierarchy_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|パスのような階層のトークナイザー。<br /><br /> **オプション**<br /><br /> delimiter (型: string) - 既定値: '/。<br /><br /> replacement (型: string) - 設定した場合、区切り記号文字が置き換えられます。 既定値は、delimiter の値と同じです。<br /><br /> maxTokenLength (型: int) - 最大トークン長。 既定値は300、最大値は300。 maxTokenLength より長いパスは無視されます。<br /><br /> reverse (型: bool) - true の場合、逆の順序でトークンが生成されます。 既定値は false です。<br /><br /> skip (型: bool) - スキップする最初のトークン。 既定値は 0 です。|  
|[pattern](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer|このトークナイザーでは、正規表現のパターン マッチングを使用して個別のトークンが作成されます。<br /><br /> **オプション**<br /><br /> [pattern](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html) (型: string) - 正規表現パターン。 既定値は \W+ です。 <br /><br /> [flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (型: string) - 正規表現フラグ。 既定値は空の文字列です。 使用できる値は以下の通りです。CANON_EQ、CASE_INSENSITIVE、COMMENTS、DOTALL、LITERAL、MULTILINE、UNICODE_CASE、UNIX_LINES<br /><br /> group (型: int) - トークンの抽出に使用するグループ。 既定値は -1 (分割) です。|
|[standard_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|[Unicode テキスト セグメント化ルール](https://unicode.org/reports/tr29/)に従ってテキストを分割します。<br /><br /> **オプション**<br /><br /> maxTokenLength (型: int) - 最大トークン長。 既定値は255、最大値は300。 最大長より長いトークンは分割されます。|  
|[uax_url_email](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|URL と電子メールが 1 つのトークンとしてトークン化されます。<br /><br /> **オプション**<br /><br /> maxTokenLength (型: int) - 最大トークン長。 既定値は255、最大値は300。 最大長より長いトークンは分割されます。|  
|[whitespace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(種類は、オプションが使用可能な場合にだけ適用されます) |空白文字によりテキストが分割されます。 255 文字より長いトークンは分割されます。|  

 <sup>1</sup> コードでは、トークナイザーの種類に常にプレフィックス "#Microsoft.Azure.Search" が付きます。たとえば、"ClassicTokenizer" は実際には "#Microsoft.Azure.Search.ClassicTokenizer" と指定します。 テーブルの幅を小さくするためプレフィックスを削除しましたが、コードには忘れずに含めてください。 tokenizer_type は、カスタマイズ可能なトークナイザーに対してのみ提供されることに注意してください。 letter トークナイザーのようにオプションがない場合は、関連付けられる #Microsoft.Azure.Search の種類はありません。

<a name="TokenFilters"></a>

###  <a name="token-filters-reference"></a>トークン フィルター リファレンス

次の表で、Apache Lucene を使用して実装されているトークン フィルターについては、Lucene の API ドキュメントへのリンクを示してあります。

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**説明とオプション**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(種類は、オプションが使用可能な場合にだけ適用されます)  |アラビア語ノーマライザーを適用して正書法を正規化するトークン フィルター。|  
|[apostrophe](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(種類は、オプションが使用可能な場合にだけ適用されます)  |アポストロフィ以降 (アポストロフィ自体を含む) のすべての文字が除去されます。 |  
|[asciifolding](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|ASCII の最初の 127 文字 ("基本ラテン" Unicode ブロック) に含まれないアルファベット、数字、記号の Unicode 文字が、同等の ASCII に変換されます (ある場合)。<br /><br /> **オプション**<br /><br /> preserveOriginal (型: bool) - true の場合、元のトークンが保持されます。 既定値は false です。|  
|[cjk_bigram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|StandardTokenizer から生成される CJK 用語のバイグラムが形成されます。<br /><br /> **オプション**<br /><br /> ignoreScripts (型: string 配列) - 無視するスクリプト。 使用できる値: "han"、"hiragana"、"katakana"、"hangul"。 既定値は空のリストです。<br /><br /> outputUnigrams (型: bool) - 常にユニグラムとバイグラムの両方を出力する場合は、true に設定します。 既定値は false です。|  
|[cjk_width](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(種類は、オプションが使用可能な場合にだけ適用されます)  |CJK の幅の違いが正規化されます。 全角 ASCII 書体が同等の基本ラテンにフォールドされ、半角カタカナ書体が同等の仮名にフォールドされます。 |  
|[classic](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(種類は、オプションが使用可能な場合にだけ適用されます)  |英語の所有格を削除し、頭字語からドットを削除します。 |  
|[common_grams](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|インデックス付けの間に、頻繁に発生する用語に対してバイグラムが作成されます。 1 つの用語も、バイグラムがオーバーレイされてインデックス付けされます。<br /><br /> **オプション**<br /><br /> commonWords (型: string 配列) - 一般的な単語のセット。 既定値は空のリストです。 必須。<br /><br /> ignoreCase (型: bool) - true の場合、マッチングで大文字と小文字は区別されません。 既定値は false です。<br /><br /> queryMode (型: bool) - バイグラムが生成された後、一般的な単語と一般的な単語の前にある 1 つの用語が削除されます。 既定値は false です。|  
|[dictionary_decompounder](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|多くのゲルマン言語に出現する複合語を分解します。<br /><br /> **オプション**<br /><br /> wordList (type: string 配列) - 照合対象の単語のリスト。 既定値は空のリストです。 必須。<br /><br /> minWordSize (型: int) - この値より長い単語のみが処理されます。 既定値は 5 です。<br /><br /> minSubwordSize (型: int) - この値より長いサブ単語のみが出力されます。 既定値は 2 です。<br /><br /> maxSubwordSize (型: int) - この値より短いサブ単語のみが出力されます。 既定値は 15 です。<br /><br /> onlyLongestMatch (型: bool) - 最長一致のサブ単語のみが出力に追加されます。 既定値は false です。|  
|[edgeNGram_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|入力トークンの先頭または末尾から指定されたサイズの n グラムが生成されます。<br /><br /> **オプション**<br /><br /> minGram (型: int) - 既定値:1、最大値:300。<br /><br /> maxGram (型: int) - 既定値:2、最大値: 300。 minGram より大きい値にする必要があります。<br /><br /> side (型: string) - n グラムを生成する入力の側が指定されます。 使用できる値: "front"、"back" |  
|[elision](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|省略記号が削除されます。 たとえば、"l'avion" (プレーン) は、"avion" (プレーン) に変換されます。<br /><br /> **オプション**<br /><br /> articles (type: string 配列) - 削除する冠詞のセット。 既定値は空のリストです。 冠詞セットのリストがない場合は、既定でフランス語のすべての冠詞が削除されます。|  
|[german_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(種類は、オプションが使用可能な場合にだけ適用されます)  |[German2 snowball アルゴリズム](https://snowballstem.org/algorithms/german2/stemmer.html)のヒューリスティックに従って、ドイツ語の文字が正規化されます。|  
|[hindi_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(種類は、オプションが使用可能な場合にだけ適用されます)  |ヒンディー語のテキストが正規化され、スペルのバリエーションの違いが削除されます。 |  
|[indic_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|インドの言語でのテキストの Unicode 表現が正規化されます。
|[keep](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|指定した単語のリストに含まれるテキストを含むトークンのみを保持するトークン フィルター。<br /><br /> **オプション**<br /><br /> keepWords (型: string 配列) - 保持する単語のリスト。 既定値は空のリストです。 必須。<br /><br /> keepWordsCase (型: bool) - true の場合、最初にすべての単語が小文字に変更されます。 既定値は false です。|  
|[keyword_marker](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|用語がキーワードとしてマークされます。<br /><br /> **オプション**<br /><br /> keywords (型: string 配列) - キーワードとしてマークする単語のリスト。 既定値は空のリストです。 必須。<br /><br /> ignoreCase (型: bool) - true の場合、最初にすべての単語が小文字に変更されます。 既定値は false です。|  
|[keyword_repeat](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(種類は、オプションが使用可能な場合にだけ適用されます)  |各入力トークンが 2 回出力されます (キーワードとして 1 回、非キーワードとして 1 回)。 |  
|[kstem](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(種類は、オプションが使用可能な場合にだけ適用されます)  |英語用の高パフォーマンスの kstem フィルター。 |  
|[length](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LengthTokenFilter|長すぎる単語または短すぎる単語が削除されます。<br /><br /> **オプション**<br /><br /> min (型: int) - 最小数。 既定値は0、最大値は300。<br /><br /> max (型: int) - 最大数。 既定値は300、最大値は300。|  
|[limit](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft.Azure.Search.LimitTokenFilter|インデックス付けの間に、トークンの数が制限されます。<br /><br /> **オプション**<br /><br /> maxTokenCount (型: int) - 生成するトークンの最大数。 既定値は 1 です。<br /><br /> consumeAllTokens (型: bool) - maxTokenCount に達した場合でも、入力のすべてのトークンを使用する必要があるかどうかが示されます。 既定値は false です。|  
|[lowercase](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(種類は、オプションが使用可能な場合にだけ適用されます)  |トークンのテキストが小文字に正規化されます。 |  
|[nGram_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|指定サイズの n グラムが生成されます。<br /><br /> **オプション**<br /><br /> minGram (型: int) - 既定値:1、最大値:300。<br /><br /> maxGram (型: int) - 既定値:2、最大値: 300。 minGram より大きい値にする必要があります。|  
|[pattern_capture](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|Java の正規表現を使用して複数のトークンが生成されます (1 つ以上のパターンのキャプチャ グループごとに 1 つ)。<br /><br /> **オプション**<br /><br /> patterns (型: string 配列) - 各トークンと照合するパターンのリスト。 必須。<br /><br /> preserveOriginal (型: bool) - パターンのいずれかが一致した場合でも元のトークンを返す場合は、true に設定します。既定値: true |  
|[pattern_replace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|ストリームの各トークンにパターンを適用するトークン フィルター。指定した置換文字列に一致が置き換えられます。<br /><br /> **オプション**<br /><br /> pattern (型: string) - 必須。<br /><br /> replacement (型: string) - 必須。|  
|[persian_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(種類は、オプションが使用可能な場合にだけ適用されます) |ペルシャ語の正規化が適用されます。 |  
|[phonetic](https://lucene.apache.org/core/4_10_3/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|PhoneticTokenFilter|音声一致用のトークンが作成されます。<br /><br /> **オプション**<br /><br /> encoder (型: string) - 使用する音声エンコーダー。 使用できる値: "metaphone"、"doubleMetaphone"、"soundex"、"refinedSoundex"、"caverphone1"、"caverphone2"、"cologne"、"nysiis"、"koelnerPhonetik"、"haasePhonetik"、"beiderMorse"。 既定値: "metaphone"。 既定値は metaphone です。<br /><br /> 詳しくは、[encoder (エンコーダー)](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) に関するページをご覧ください。<br /><br /> replace (型: bool) - エンコードされたトークンで元のトークンを置き換える必要がある場合は true、シノニムとして追加する場合は false。 既定値は true です。|  
|[porter_stem](https://lucene.apache.org/core/4_10_3/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(種類は、オプションが使用可能な場合にだけ適用されます)  |[Porter ステミング アルゴリズム](https://tartarus.org/~martin/PorterStemmer/)に従ってトークン ストリームを変換します。 |  
|[reverse](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(種類は、オプションが使用可能な場合にだけ適用されます)  |トークンの文字列が反転されます。 |  
|[scandinavian_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(種類は、オプションが使用可能な場合にだけ適用されます)  |交換可能なスカンジナビア語の文字の使用を正規化します。 |  
|[scandinavian_folding](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(種類は、オプションが使用可能な場合にだけ適用されます)  |スカンジナビア語の文字が åÅäæÄÆ->a および öÖøØ->o にフォールドされます。 また、重母音 aa、ae、ao、oe、oo の使用を判別し、最初の 1 つだけが残されます。 |  
|[shingle](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenFilter|トークンの組み合わせが 1 つのトークンとして作成されます。<br /><br /> **オプション**<br /><br /> maxShingleSize (型: int) - 既定値は 2 です。<br /><br /> minShingleSize (型: int) - 既定値は 2 です。<br /><br /> outputUnigrams (型: bool) - true の場合、出力ストリームには入力トークン (ユニグラム) と共にシングルが含まれます。 既定値は true です。<br /><br /> outputUnigramsIfNoShingles (型: bool) - true の場合、シングルが使用できない場合、outputUnigrams==false の動作がオーバーライドされます。 既定値は false です。<br /><br /> tokenSeparator (型: string) - 隣接するトークンを結合してシングルを形成するときに使用する文字列。 既定値は " " です。<br /><br /> filterToken (型: string) - 位置トークンがない各位置に挿入する文字列。 既定値は "_" です。|  
|[snowball](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Snowball トークン フィルター。<br /><br /> **オプション**<br /><br /> language (型: string) - 使用できる値: "armenian"、"basque"、"catalan"、"danish"、"dutch"、"english"、"finnish"、"french"、"german"、"german2"、"hungarian"、"italian"、"kp"、"lovins"、"norwegian"、"porter"、"portuguese"、"romanian"、"russian"、"spanish"、"swedish"、"turkish"|  
|[sorani_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|ソラニー語テキストの Unicode 表現が正規化されます。<br /><br /> **オプション**<br /><br /> なし。|  
|stemmer|StemmerTokenFilter|言語固有のステミング フィルター。<br /><br /> **オプション**<br /><br /> language (型: string) - 使用できる値: <br /> -   ["arabic"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   ["armenian"](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   ["basque"](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   ["brazilian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />-   "bulgarian"<br />-   ["catalan"](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   ["czech"](https://portal.acm.org/citation.cfm?id=1598600)<br />-   ["danish"](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   ["dutch"](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   ["english"](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["lightEnglish"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalEnglish"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["possessiveEnglish"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porter2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["lovins"](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   ["finnish"](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />-   "lightFinnish"<br />-   ["french"](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["lightFrench"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalFrench"](https://dl.acm.org/citation.cfm?id=318984)<br />-   "galician"<br />-   "minimalGalician"<br />-   ["german"](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["german2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["lightGerman"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   "minimalGerman"<br />-   ["greek"](https://sais.se/mthprize/2007/ntais2007.pdf)<br />-   "hindi"<br />-   ["hungarian"](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["lightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["indonesian"](https://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf)<br />-   ["irish"](https://snowballstem.org/otherapps/oregan/)<br />-   ["italian"](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["lightItalian"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["sorani"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   ["latvian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   ["norwegian"](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["lightNorwegian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNorwegian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["portuguese"](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortuguese"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimalPortuguese"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugueseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   ["romanian"](https://snowballstem.org/otherapps/romanian/)<br />-   ["russian"](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["lightRussian"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   ["spanish"](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["lightSpanish"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["swedish"](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />-   "lightSwedish"<br />-   ["turkish"](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|辞書でステミングされた用語はキーワードとしてマークされ、チェーンにステミングされるのを防ぎます。 すべてのステミング フィルターの前に配置する必要があります。<br /><br /> **オプション**<br /><br /> rules (型: string 配列) - "word => stem" の形式のステミング ルール (例: "ran => run")。 既定値は空のリストです。  必須。|  
|[stopwords](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|トークン ストリームからストップワードが削除されます。 既定では、フィルターに英語の定義済みストップワード リストが使用されます。<br /><br /> **オプション**<br /><br /> stopwords (型: string 配列) - ストップワードのリスト。 stopwordsList が指定されている場合は指定できません。<br /><br /> stopwordsList (型: string) - ストップワードの定義済みのリスト。 stopwords が指定されている場合は指定できません。 使用できる値: "arabic"、"armenian"、"basque"、"brazilian"、"bulgarian"、"catalan"、"czech"、"danish"、"dutch"、"english"、"finnish"、"french"、"galician"、"german"、"greek"、"hindi"、"hungarian"、"indonesian"、"irish"、"italian"、"latvian"、"norwegian"、"persian"、"portuguese"、"romanian"、"russian"、"sorani"、"spanish"、"swedish"、"thai"、"turkish"。既定値: "english"。 stopwords が指定されている場合は指定できません。 <br /><br /> ignoreCase (型: bool) - true の場合、最初にすべての単語を小文字に変更されます。 既定値は false です。<br /><br /> removeTrailing (型: bool) - true の場合、最後の検索語句がストップワードである場合は無視されます。 既定値は true です。
|[synonym](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynonymTokenFilter|トークン ストリーム内の 1 つまたは複数の単語のシノニムが照合されます。<br /><br /> **オプション**<br /><br /> synonyms (型: string 配列) - 必須。 次の 2 つの形式のいずれかでのシノニムのリスト:<br /><br /> \- incredible, unbelievable, fabulous => amazing - => 記号の左側にあるすべての用語が、右側のすべての用語に置き換えられます。<br /><br /> \- incredible, unbelievable, fabulous, amazing - 同等の単語のコンマ区切りリスト。 このリストの解釈方法を変更するには、expand オプションを設定します。<br /><br /> ignoreCase (型: bool) - マッチングに対するケース フォールド入力。 既定値は false です。<br /><br /> expand (型: bool) - true の場合、シノニムのリスト内のすべての単語 (=> 表記が使用されていない場合) が相互にマップされます。 <br />次のリスト: incredible, unbelievable, fabulous, amazing は、incredible, unbelievable, fabulous, amazing => incredible, unbelievable, fabulous, amazing と同等です<br /><br />- false の場合、次のリスト: incredible, unbelievable, fabulous, amazing は、incredible, unbelievable, fabulous, amazing => incredible と同等です。|  
|[trim](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(種類は、オプションが使用可能な場合にだけ適用されます)  |先頭と末尾の空白文字がトークンからトリミングされます。 |  
|[truncate](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|TruncateTokenFilter|用語が特定の長さに切り詰められます。<br /><br /> **オプション**<br /><br /> length (型: int) - 既定値は300、最大値は300。 必須。|  
|[unique](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniqueTokenFilter|前のトークンと同じテキストのトークンが除外されます。<br /><br /> **オプション**<br /><br /> onlyOnSamePosition (型: bool) - 設定した場合、同じ位置にある重複のみが削除されます。 既定値は true です。|  
|[uppercase](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(種類は、オプションが使用可能な場合にだけ適用されます)  |トークンのテキストが大文字に正規化されます。 |  
|[word_delimiter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|単語がサブ単語に分割され、部分語のグループに対してオプションの変換が実行されます。<br /><br /> **オプション**<br /><br /> generateWordParts (型: bool) - 単語が部分ごとに生成されます。たとえば、"AzureSearch" は "Azure" "Search" になります。 既定値は true です。<br /><br /> generateNumberParts (型: bool) - 数字のサブ単語が生成されます。 既定値は true です。<br /><br /> catenateWords (型: bool) - 単語部分の最大の連続が連結されます。たとえば、"Azure-Search" は "AzureSearch" になります。 既定値は false です。<br /><br /> catenateNumbers (型: bool) - 数字部分の最大の連続が連結されます。たとえば、"1-2" は "12" になります。 既定値は false です。<br /><br /> catenateAll (型: bool) - すべてのサブ単語部分が連結されます。たとえば、"Azure-Search-1" は "AzureSearch1" になります。 既定値は false です。<br /><br /> splitOnCaseChange (型: bool) - true の場合、大文字と小文字が変わったところで単語が分割されます。たとえば、"AzureSearch" は "Azure" "Search" になります。 既定値は true です。<br /><br /> preserveOriginal - 元の単語が維持され、サブ単語リストに追加されます。 既定値は false です。<br /><br /> splitOnNumerics (型: bool) - true の場合、数字で分割されます。たとえば、"Azure1Search" は "Azure" "1" "Search" になります。 既定値は true です。<br /><br /> stemEnglishPossessive (型: bool) - 各サブ単語で、末尾の "'s" が削除されます。 既定値は true です。<br /><br /> protectedWords (型: string 配列) - 区切られないように保護するトークン。 既定値は空のリストです。|  

 <sup>1</sup> コードでは、トークン フィルターの種類に常にプレフィックス "#Microsoft.Azure.Search" が付きます。たとえば、"ArabicNormalizationTokenFilter" は実際には "#Microsoft.Azure.Search.ArabicNormalizationTokenFilter" と指定します。  テーブルの幅を小さくするためプレフィックスを削除しましたが、コードには忘れずに含めてください。  


## <a name="see-also"></a>関連項目  
 [Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Azure Search でのアナライザーの例](search-analyzers.md#examples)    
 [インデックスを作成する &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
