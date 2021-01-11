---
title: 部分的な語句、パターン、特殊文字
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search クエリ要求で、ワイルドカード、正規表現、プレフィックスのクエリを使用し、完全な用語または用語の一部を照合します。 一致させるのが難しい、特殊文字を含むパターンを完全に一致させるには、完全なクエリ構文とカスタム アナライザーを使用して解決できます。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 357f44149cb17976556c1e4609f6f2af531b80ee
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935774"
---
# <a name="partial-term-search-and-patterns-with-special-characters-wildcard-regex-patterns"></a>部分的な用語検索と特殊文字を含むパターン (ワイルドカード、正規表現、パターン)

"*部分的な用語検索*" とは、用語全体ではなく、用語の先頭、中間、または末尾 (プレフィックス、インフィックス、またはサフィックス クエリとも呼ばれます) のみを含む用語のフラグメントで構成されるクエリを指します。 部分的な用語検索ではフラグメントを組み合わせて使用することもできます。多くの場合、ダッシュやスラッシュなどの特殊文字をクエリ文字列の一部として使います。 一般的なユースケースには、電話番号、URL、コード、またはハイフンでつながれた複合語の一部が含まれます。

期待される形式のトークンがインデックスに含まれていない場合、特殊文字を含む部分的な用語検索とクエリ文字列が問題になる可能性があります。 インデックス作成の[字句分析フェーズ](search-lucene-query-architecture.md#stage-2-lexical-analysis) (既定の標準アナライザーを想定) では、特殊文字は破棄され、複合語は分割され、空白は削除されます。このいずれも、一致が見つからない場合にクエリが失敗する原因となる可能性があります。 たとえば、`+1 (425) 703-6214` (`"1"`、`"425"`、`"703"`、`"6214"` としてトークン化) のような電話番号は、実際にはインデックスに存在しないため、`"3-62"` クエリに表示されません。 

解決策として、インデックスの作成中に、空白や特殊文字などを必要に応じて含む完全な文字列が保持されているアナライザーを呼び出します。これにより、クエリ文字列にスペースと文字を含めることができます。 同様に、より小さな部分にトークン化されていない完全な文字列を使用すると、"starts with" または "ends with" クエリのパターン マッチングが有効になります。ここで、指定するパターンは、字句解析によって変換されない用語に対して評価することができます。 完全な文字列用に追加のフィールドを作成することに加えて、用語全体のトークンを出力するコンテンツ保持アナライザーを使用することは、パターン マッチングに対しても、特殊文字を含むクエリ文字列でのマッチングに対してもソリューションとなります。

> [!TIP]
> Postman および REST API に精通している場合は、[クエリ例のコレクション](https://github.com/Azure-Samples/azure-search-postman-samples/)をダウンロードして、この記事で説明されている部分的な用語および特殊文字のクエリを実行してください。

## <a name="what-is-partial-term-search-in-azure-cognitive-search"></a>Azure Cognitive Search での部分的な用語検索とは

Azure Cognitive Search では、インデックス内のトークン化された用語全体がスキャンされます。ワイルドカード プレースホルダー演算子 (`*` と `?`) を含めるか、またはクエリを正規表現として書式設定しない限り、部分的な用語に対する一致は見つかりません。 部分的な用語は、次の方法を使用して指定します。

+ [正規表現クエリ](query-lucene-syntax.md#bkmk_regex)は、Apache Lucene で有効な任意の正規表現とすることができます。 

+ [プレフィックス一致を使用したワイルドカード演算子](query-simple-syntax.md#prefix-search)は、一般的に認識されたパターンであり、用語の先頭の後にサフィックス演算子 `*` または `?` が続きます ("Cap'n Jack's Waterfront Inn" または "Gacc Capital" に一致する `search=cap*` など)。 プレフィックス一致は、シンプルで完全な Lucene クエリ構文でサポートされています。

+ [挿入およびサフィックス一致を使用したワイルドカード](query-lucene-syntax.md#bkmk_wildcard)では、`*` および `?` の演算子が用語の中または先頭に配置され、正規表現の構文 (式はスラッシュで囲む) が必要になります。 たとえば、クエリ文字列 (`search=/.*numeric*./`) の場合、サフィックスおよび挿入一致として、"alphanumeric" および "alphanumerical"の結果が返されます。

部分的な用語またはパターン検索、およびファジー検索のようなその他のいくつかのクエリ フォームでは、クエリを実行するときにアナライザーは使用されません。 演算子と区切り記号の存在を基にパーサーによって検出されるこれらのクエリ フォームの場合、クエリ文字列は字句解析なしでエンジンに渡されます。 これらのクエリ フォームでは、フィールドに指定されたアナライザーは無視されます。

> [!NOTE]
> URL フラグメント内のスラッシュなどの文字が部分的なクエリ文字列に含まれている場合、エスケープ文字の追加が必要になることがあります。 JSON では、スラッシュ `/` はバックスラッシュ `\` によってエスケープされます。 このため、`search=/.*microsoft.com\/azure\/.*/` は URL フラグメント「microsoft.com/azure/」の構文です。

## <a name="solving-partialpattern-search-problems"></a>部分的検索またはパターン検索の問題の解決

フラグメントまたはパターンまたは特殊文字を検索する必要がある場合は、単純なトークン化ルールで動作するカスタム アナライザーを使用して、既定のアナライザーをオーバーライドできます。これにより、文字列全体がインデックス内に保持されます。 少し前に戻ると、手順は次のようになります。

+ 文字列の完全なバージョンを格納するためのフィールドを定義する (クエリ時に分析されたテキストと分析されていないテキストが必要であると想定)
+ 適切なレベルの細分性でトークンを生成するさまざまなアナライザーを評価してその中から選択する
+ フィールドにアナライザーを割り当てる
+ インデックスを構築してテストする

> [!TIP]
> アナライザーの評価は、インデックスの再構築を頻繁に行う必要がある反復的なプロセスです。 [インデックスの作成](/rest/api/searchservice/create-index)、[インデックスの削除](/rest/api/searchservice/delete-index)、[ドキュメントの読み込み](/rest/api/searchservice/addupdate-or-delete-documents)、および[ドキュメントの検索](/rest/api/searchservice/search-documents)の REST API である Postman を使用することで、これらの手順を簡単に行うことができます。 ドキュメントの読み込みの場合、要求本文には、テストする代表的な小さいデータセットが含まれている必要があります (たとえば、電話番号や製品コードを含むフィールドなど)。 同じ Postman コレクション内のこれらの API を使用すると、これらの手順を迅速に実行できます。

## <a name="duplicate-fields-for-different-scenarios"></a>さまざまなシナリオでフィールドを複製する

アナライザーによって、インデックス内で用語をトークン化する方法が決定されます。 アナライザーはフィールドごとに割り当てられるので、インデックス内にフィールドを作成し、さまざまなシナリオに合わせて最適化できます。 たとえば、最初のフルテキスト検索をサポートするために "featureCode" と "featureCodeRegex" を定義し、2 番目に高度なパターン マッチングを適用することができます。 各フィールドに割り当てられたアナライザーによって、各フィールドの内容をインデックス内でトークン化する方法が決められます。  

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_custom_analyzer"
},
```

## <a name="choose-an-analyzer"></a>アナライザーを選択する

用語全体のトークンを生成するアナライザーを選択する場合は、次のアナライザーが一般的な選択肢となります。

| アナライザー | 動作 |
|----------|-----------|
| [言語アナライザー](index-add-language-analyzers.md) | 複合語または文字列のハイフン、母音の変化、および動詞の形を保持します。 クエリ パターンにダッシュが含まれている場合は、言語アナライザーを使用するだけで十分な場合があります。 |
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | フィールド全体の内容は、1 つの用語としてトークン化されます。 |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | 空白文字のみを区切ります。 ダッシュまたはその他の文字を含む用語は、1 つのトークンとして扱われます。 |
| [カスタム アナライザー](index-add-custom-analyzers.md) | (推薦) カスタム アナライザーを作成すると、トークナイザーとトークン フィルターの両方を指定できます。 前述のアナライザーはそのまま使用する必要があります。 カスタム アナライザーでは、使用するトークナイザーとトークン フィルターを選択できます。 <br><br>推奨される組み合わせは、[小文字のトークン フィルター](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)と[キーワード トークナイザー](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)です。 定義済みの[キーワード アナライザー](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)単体では大文字のテキストを小文字に変換できないため、クエリが失敗する可能性があります。 カスタム アナライザーには、小文字のトークン フィルターを追加するためのメカニズムが用意されています。 |

Postman のような Web API テスト ツールを使用している場合は、[テスト アナライザーの REST 呼び出し](/rest/api/searchservice/test-analyzer)を追加して、トークン化された出力を検査できます。

操作するには、設定されたインデックスが必要です。 既存のインデックスと、ダッシュまたは用語の一部を含むフィールドが指定されている場合は、特定の用語に対してさまざまなアナライザーを試して、どのトークンが生成されるかを確認できます。  

1. まず、標準アナライザーを使用して、用語が既定でトークン化されているかどうかを確認します。

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. 応答を評価して、インデックス内でテキストがどのようにトークン化されるかを確認します。 各用語がどのように小文字に変換され、分割されているかに注目してください。 これらのトークンに一致するクエリによってのみ、このドキュメントが結果として返されます。 "10-NOR" を含むクエリは失敗します。

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. 次に、`whitespace` または `keyword` アナライザーを使用するように要求を変更します。

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. これで、応答は 1 つのトークンで構成され、文字列の一部としてダッシュが保持されます。 パターンまたは用語の一部 ("10-NOR" など) を検索する必要がある場合、これでクエリ エンジンが一致を見つけるための基礎となります。


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> 多くの場合、クエリ ツリーの構築時に、クエリ パーサーにより検索式内の用語が小文字に変換されることに注意してください。 インデックス作成時にテキスト入力が小文字に変換されないアナライザーを使用していて、期待した結果が得られない場合は、これが原因である可能性があります。 この問題を解決するため、以下の「カスタム アナライザーを使用する」セクションで説明されているように、小文字のトークン フィルターを追加します。

## <a name="configure-an-analyzer"></a>アナライザーを構成する
 
アナライザーを評価する場合も、特定の構成を使用する場合も、アナライザーをフィールド定義に指定する必要があります。また、組み込みのアナライザーを使用しない場合は、アナライザー自体を構成することもできます。 アナライザーをスワップする場合は、通常、インデックスを再構築 (削除、再作成、および再読み込み) する必要があります。 

### <a name="use-built-in-analyzers"></a>組み込みアナライザーを使用する

組み込みまたは定義済みのアナライザーは、フィールド定義の `analyzer` プロパティに名前を使用して指定できます。インデックスに追加の構成は必要ありません。 次の例では、フィールドに `whitespace` アナライザーを設定する方法を示します。 

他のシナリオと、他の組み込みアナライザーの詳細については、[定義済みアナライザー リスト](/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)に関するページを参照してください。 

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```

### <a name="use-custom-analyzers"></a>カスタム アナライザーを使用する

[カスタム アナライザー](index-add-custom-analyzers.md)を使用している場合は、ユーザー定義のトークナイザー、トークン フィルターに可能な構成設定を組み合わせてインデックスで定義します。 次に、組み込みアナライザーの場合と同様に、フィールド定義で参照します。

用語全体のトークン化が目的である場合は、**キーワード トークナイザー**で構成されるカスタム アナライザーと**小文字のトークン フィルター**を選択することをお勧めします。

+ キーワード トークナイザーは、フィールドの内容全体に対して 1 つのトークンを作成します。
+ lowercase トークン フィルターは、大文字を小文字に変換します。 通常、クエリ パーサーでは、大文字のテキスト入力が小文字になります。 小文字への変換により、トークン化された用語を使用して入力を均質化します。

次の例は、キーワード トークナイザーと lowercase トークン フィルターを提供するカスタム アナライザーを示しています。

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> `keyword_v2` トークナイザーおよび `lowercase` トークン フィルターはシステムで認識され、既定の構成を使用しています。そのため、最初に定義しなくても名前で参照できます。

## <a name="build-and-test"></a>ビルドおよびテスト

シナリオをサポートするアナライザーとフィールド定義を使ってインデックスを定義したら、部分的な文字列クエリをテストできるように、代表的な文字列を含むドキュメントを読み込みます。 

前のセクションではロジックについて説明しました。 このセクションでは、ソリューションをテストする際に呼び出す必要がある各 API について説明します。 前述のように、Postman など対話型の Web テストツールを使用すると、これらのタスクを迅速に実行できます。

+ [[インデックスの削除]](/rest/api/searchservice/delete-index) では、同じ名前の既存のインデックスが削除されるため、再び作成できるようになります。

+ [[インデックスの作成]](/rest/api/searchservice/create-index) では、アナライザーの定義やアナライザーの仕様を含むフィールドなど、検索サービスでインデックス構造が作成されます。

+ [[ドキュメントの読み込み]](/rest/api/searchservice/addupdate-or-delete-documents) では、インデックスと同じ構造のドキュメントおよび検索可能なコンテンツをインポートします。 この手順の後、インデックスはクエリやテストを実行できるようになります。

+ [テスト アナライザー](/rest/api/searchservice/test-analyzer)については「[アナライザーの選択](#choose-an-analyzer)」で紹介しました。 さまざまなアナライザーを使用して、インデックスの一部の文字列をテストし、用語のトークン化方法を理解します。

+ [[ドキュメントの検索]](/rest/api/searchservice/search-documents) では、ワイルドカードと正規表現について[単純な構文](query-simple-syntax.md)または[完全な Lucene 構文](query-lucene-syntax.md)のいずれかを使用してクエリ要求を作成する方法を説明します。

  "+1 (425) 703-6214" に対して "3-6214" のクエリを実行して一致を検索するなど、部分的な用語クエリの場合は、単純な構文 (`search=3-6214&queryType=simple`) を使用できます。

  "alphanumeric" に対して "num" または "numeric" のクエリを実行して一致を検索するなど、インフィックスおよびサフィックス クエリの場合は、完全な Lucene 構文と正規表現 (`search=/.*num.*/&queryType=full`) を使用します。

## <a name="tune-query-performance"></a>クエリ パフォーマンスの調整

Keyword_v2 トークナイザーと小文字のトークン フィルターを含む推奨構成を実装する場合、インデックス内の既存のトークンに対する追加のトークン フィルター処理が原因で、クエリのパフォーマンスが低下することがあります。 

次の例では、[EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) を追加して、プレフィックスを高速に一致させるようにします。 文字を含む 2 から 25 文字の組み合わせに対して、追加のトークンが生成されます (MS、MSF、MSFT、MSFT/、MSFT/S、MSFT/SQ、MSFT/SQL に限らない)。 

ご想像のとおり、追加のトークン化によってインデックスが大きくなります。 より大きなインデックスを収容するのに十分な容量がある場合は、このアプローチでその応答時間を短縮することがより適切な解決策となる可能性があります。

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

## <a name="next-steps"></a>次のステップ

この記事では、アナライザーがどのようにクエリの問題に寄与し、クエリの問題を解決するかについて説明します。 次の手順として、アナライザーによるインデックス作成とクエリ処理への影響について詳しく見ていきます。 特に、Analyze Text API を使用してトークン化された出力を返すことを検討してください。これにより、アナライザーがインデックス用に作成した内容を正確に確認できます。

+ [言語アナライザー](search-language-support.md)
+ [Azure Cognitive Search でのテキスト処理のためのアナライザー](search-analyzers.md)
+ [Text API の分析 (REST)](/rest/api/searchservice/test-analyzer)
+ [フルテキスト検索のしくみ (クエリ アーキテクチャ)](search-lucene-query-architecture.md)