---
title: 文法形式 - Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Knowledge Exploration Service (KES) API の文法形式について説明します。
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 844bd9a88c52fd398fc66c71e59da513c0d7d90d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544326"
---
# <a name="grammar-format"></a>文法形式

文法とは、サービスが解釈できる自然言語のクエリの重み付けセットと、これらの自然言語のクエリをセマンティック クエリ式に変換する方法を指定する、XML ファイルのことです。  文法構文は、音声認識文法の W3C 標準である [SRGS](https://www.w3.org/TR/speech-grammar/) をベースとし、データ インデックスの統合やセマンティック関数をサポートする拡張機能が備わっています。

次に文法で使用できる各構文要素について説明します。  文脈の中でこれらの要素の使用について実践する完全な文法については、[こちらの例](#example)をご覧ください。

### <a name="grammar-element"></a>grammer 要素

`grammar` 要素は、文法仕様 XML の最上位の要素です。  必須の `root` 属性は、文法の開始点を定義するルート ルールの名前を指定します。

```xml
<grammar root="GetPapers">
```

### <a name="import-element"></a>import 要素

`import` 要素は、外部ファイルからスキーマ定義をインポートし、属性の参照を有効にします。 この要素は最上位の `grammar` 要素の子であり、すべての `attrref` 要素の前に表示される必要があります。 必須の `schema` 属性は、文法の XML ファイルと同じディレクトリ内のスキーマ ファイルの名前を指定します。 必須の `name` 要素は、後続の `attrref` 要素がこのスキーマ内で定義された属性を参照するときに使用する、スキーマの別名を指定します。

```xml
  <import schema="academic.schema" name="academic"/>
```

### <a name="rule-element"></a>rule 要素

`rule` 要素は、システムが解釈できるクエリ式のセットを指定する構造単位である、文法のルールを定義します。  この要素は最上位の `grammar` 要素の子である必要があります。  必須の `id` 属性は、`grammar` 要素や `ruleref` 要素から参照される、ルールの名前を指定します。

`rule` 要素は、有効な拡張のセットを定義します。  テキストのトークンは入力クエリと直接照合されます。  `item` 要素は、繰り返しや変更解釈の確率を指定します。  `one-of` 要素は、代替の選択肢を示します。  `ruleref` 要素は、より単純な拡張からより複雑な拡張の構成を有効にします。  `attrref` 要素は、インデックスから属性値に対する照合を許可します。  `tag` 要素は、解釈のセマンティクスを指定し、解釈の確率を変更できます。

```xml
<rule id="GetPapers">...</rule>
```

### <a name="example-element"></a>example 要素

オプションの `example` 要素は、指定された`rule` 定義により受け入れ可能な、フレーズの例を指定します。  これは、ドキュメントや自動テストで使用できます。

```xml
<example>papers about machine learning by michael jordan</example>
```

### <a name="item-element"></a>item 要素

`item` 要素は、文法構成のシーケンスをグループ化します。  これは、拡張シーケンスの繰り返しを示すか、`one-of` 要素と関連して代替を指定するために使用できます。

`item` 要素が `one-of` の子でないときは、カウント値に `repeat` 属性を割り当てることで、かっこで囲まれたシーケンスの繰り返しを指定できます。  カウント値 "*n*" (*n* は整数) は、シーケンスがちょうど *n* 回発生する必要があることを示します。  カウント値 "*m*-*n*" は、シーケンスが *m* 回から *n* 回表示されることを許可します。  カウント値 "*m*-" は、シーケンスが *m* 回以上表示される必要があることを指定します。  オプションの `repeat-logprob` 属性は、追加の繰り返しごとの解釈の確率を許容範囲を超えて変更するために使用できます。

```xml
<item repeat="1-" repeat-logprob="-10">...</item>
```

`one-of` 要素の子として表示される `item` 要素は、拡張の代替のセットを定義します。  この用途では、オプションの `logprob` 属性はさまざまな選択における相対的な対数確率を指定します。  0 から 1 の間の確率 *p* を指定した場合、対応する対数確率は log(*p*) として計算できます。ここで、log() は自然対数関数です。  指定しない場合、`logprob` は既定で 0 になり、解釈の確率は変更されません。  対数確率は常に負の浮動小数点数か 0 になります。

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="one-of-element"></a>one-of 要素

`one-of` 要素は、子の `item` 要素のうちいずれか 1 つの代替拡張を指定します。  `item` 要素のみが `one-of` 要素内に表示されます。  さまざまな選択における相対的な確率は、各子要素 `item` の `logprob` 属性を介して指定できます。

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="ruleref-element"></a>ruleref 要素

`ruleref` 要素は、別の `rule` 要素への参照を介して有効な拡張を指定します。  `ruleref` の使用により、より単純なルールからより複雑な式をビルドできます。  必須の `uri` 属性は、構文 "#*rulename*" を使用して参照先の `rule` の名前を示します。  参照先のルールのセマンティック出力をキャプチャするには、オプションの `name` 属性を使用して、セマンティック出力が割り当てられる変数の名前を指定します。
 
```xml
<ruleref uri="#GetPaperYear" name="year"/>
```

### <a name="attrref-element"></a>attrref 要素

`attrref` 要素はインデックス属性を参照し、インデックス内で観測される属性値に対する照合を許可します。  必須の `uri` 属性は、構文 "*schemaName*#*attrName*" を使用して、インデックスのスキーマ名と属性名を指定します。  *schemaName* という名前のスキーマをインポートする、先行する `import` 要素が必要です。  属性名は、対応するスキーマで定義された属性の名前です。

`attrref` 要素は、照合するユーザー入力に加えて、入力値と一致するインデックス内のオブジェクトのサブセットを選択する、構造化されたクエリ オブジェクトも出力として返します。  オプションの `name` 属性を使用して、クエリ オブジェクトの出力を保存する必要がある場所の変数の名前を指定します。  クエリ オブジェクトを他のクエリ オブジェクトに組み込んで、より複雑な式を形成できます。  詳しくは、「[セマンティクス解釈](SemanticInterpretation.md)」をご覧ください。  

```xml
<attrref uri="academic#Keyword" name="keyword"/>
```

#### <a name="query-completion"></a>クエリの実行

ユーザー クエリを部分的に解釈するときのクエリの実行をサポートするには、参照先の各属性にスキーマ定義の操作として "starts_with" が含まれている必要があります。  ユーザー クエリのプレフィックスを指定すると、`attrref` がそのプレフィックスを完成させるインデックス内のすべての値を照合し、完成したそれぞれの値を文法の別個の解釈として一時停止させます。  

次に例を示します。
* `<attrref uri="academic#Keyword" name="keyword"/>` をクエリ プレフィックス "dat" に対して照合すると、"database" に関するペーパーについての解釈を 1 つ、"data mining" に関するペーパーについての解釈を 1 つなどと生成します。
* `<attrref uri="academic#Year" name="year"/>` をクエリ プレフィックス "200" に対して照合すると、"2000" 年のペーパーについての解釈を 1 つ、"2001" 年のペーパーについての解釈を 1 つなどと生成します。

#### <a name="matching-operations"></a>照合操作

完全一致に加えて、属性の種類の選択にはオプションの `op` 属性を介したプレフィックスおよび不完全一致による照合もサポートします。  インデックス内に一致する値を持つオブジェクトがない場合、その文法パスはブロックされ、サービスはこの文法パス上を通過する解釈を生成しません。   `op` 属性は既定で "eq" になります。

```xml
in <attrref uri="academic#Year" name="year"/>
before <attrref uri="academic#Year" op="lt" name="year"/
```

次の表に、各属性の種類でサポートされている `op` 値の一覧を示します。  これらを使用するには、対応するインデックス操作がスキーマ属性の定義に含まれている必要があります。

| 属性の種類 | op 値 | 説明 | インデックス操作
|----|----|----|----|
| String | eq | 文字列の完全一致 | equals |
| String | starts_with | 文字列のプレフィックス一致 | starts_with |
| Int32、Int64、Double | eq |  数値の等号一致 | equals |
| Int32、Int64、Double | lt、le、gt、ge | 数値の不等号一致 (<、< =、>、> =) | is_between |
| Int32、Int64、Double | starts_with | 小数点表記の値のプレフィックス一致 | starts_with |

次に例を示します。
* `<attrref uri="academic#Year" op="lt" name="year"/>` は入力文字列 "2000" と一致し、2000 年より前に公開されたすべてのペーパーを返します。
* 20 年より前に公開されたペーパーはインデックスにはないため、`<attrref uri="academic#Year" op="lt" name="year"/>` は入力文字列 "20" とは一致しません。
* `<attrref uri="academic#Keyword" op="starts_with" name="keyword"/>` は入力文字列 "dat" と一致し、"database"、"data mining" などに関する単一の解釈で返します。これは、まれなユース ケースです。
* `<attrref uri="academic#Year" op="starts_with" name="year"/>` は入力文字列 "20" と一致し、200 年 から 299 年の間、2000 年から 2999 年の間などで公開されたペーパーを単一の解釈で返します。これは、まれなユース ケースです。

### <a name="tag-element"></a>tag 要素

`tag` 要素は、文法を通過するパスがどのように解釈されるかを指定します。  これにはセミコロンで区切られたステートメントのシーケンスが含まれます。  ステートメントは、リテラルの割り当てや、別の変数への変数になる場合があります。  また、0 または複数のパラメーターが指定された関数の出力が変数に割り当てられる場合もあります。  各関数パラメーターは、リテラルまたは変数を使用して指定できます。  関数が出力を返さない場合、割り当ては省略されます。  変数の範囲は、それに含まれるルールに対してローカルです。

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

文法内の各 `rule` は事前定義された "out" という名前の変数を持ちます。これは、ルールのセマンティック出力を表します。  その値は、ユーザー クエリ入力と一致する `rule` を通じて、パスによって走査された各セマンティック ステートメントを評価することで計算されます。  評価の最後に "out" 変数に割り当てられた値が、そのルールのセマンティック出力になります。  文法に対するユーザー クエリの解釈のセマンティック出力は、ルート ルールのセマンティック出力です。

一部のステートメントは、対数確率の加法補正を導入することで、解釈パスの確率を変えることがあります。  一部のステートメントは、指定された条件が満たされない場合、解釈をまとめて拒否することがあります。

サポートされているセマンティック関数の一覧は、「[Semantic Functions](SemanticInterpretation.md#semantic-functions)」(セマンティック関数) をご覧ください。

## <a name="interpretation-probability"></a>解釈確率

文法による解釈パスの確率は、その過程で発声したすべての `<item>` 要素とセマンティック関数の累積対数確率になります。  これは、特定の入力シーケンスと一致する相対的な確率を表します。

0 から 1 の間の確率 *p* を指定した場合、対応する対数確率は log(*p*) として計算できます。ここで、log() は自然対数関数です。  対数確率を使用することで、システムが単純な加算により解釈パスの結合確率を蓄積できます。  また、このような結合確率の計算において一般的な、浮動小数点のアンダーフローを回避できます。  仕様では、対数確率は常に負の浮動小数点数または 0 であり、値が大きいほど可能性が高いことを示します。

## <a name="example"></a>例

次にある文法の各要素を示す、学術的刊行物のドメインからの XML の例を示します。

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="academic.schema" name="academic"/>
  
  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>
    
    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>
  
    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>
        
      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>
        
        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>
        
        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>
  
  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="lt" name="year"/></item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="gt" name="year"/></item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```
