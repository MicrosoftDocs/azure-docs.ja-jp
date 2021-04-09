---
title: 'チュートリアル: カスタム アナライザーを作成する'
titleSuffix: Azure Cognitive Search
description: カスタム アナライザーを作成して Azure Cognitive Search における検索結果の質を高める方法について説明します。
manager: luisca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/29/2021
ms.openlocfilehash: f4bde98cfc772f5a80bb52c2e4bc2f5a9c28c78d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99097659"
---
# <a name="tutorial-create-a-custom-analyzer-for-phone-numbers"></a>チュートリアル:電話番号のカスタム アナライザーを作成する

[アナライザー](search-analyzers.md)は、あらゆる検索ソリューションの重要なコンポーネントです。 検索結果の質を高めるためには、アナライザーのしくみとそれが検索結果に及ぼす影響を理解することが大切です。

フリー テキスト フィールドなど一部のケースでは、適切な[言語アナライザー](index-add-language-analyzers.md)を選択するだけで検索結果は向上します。 しかし電話番号や URL、メールアドレスなどを厳密に検索するシナリオでは、カスタム アナライザーの使用が必要となる場合があります。

このチュートリアルでは、Postman と Azure Cognitive Search の [REST API](/rest/api/searchservice/) を使用して次のことを行います。

> [!div class="checklist"]
> * アナライザーのしくみを説明する
> * 電話番号を検索するためのカスタム アナライザーを定義する
> * カスタム アナライザーによってどのようにテキストがトークン化されるかをテストする
> * 検索結果をさらに向上させるためにインデックス作成用と検索用に別々のアナライザーを作成する

## <a name="prerequisites"></a>前提条件

このチュートリアルには、次のサービスとツールが必要です。

+ [Postman デスクトップ アプリ](https://www.getpostman.com/)
+ [作成](search-create-service-portal.md)または[既存の検索サービスの用意](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)

## <a name="download-files"></a>ファイルのダウンロード

このチュートリアルのソース コードは、[Azure-Samples/azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples) GitHub リポジトリの [custom-analyzers](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) フォルダーにあります。

## <a name="1---create-azure-cognitive-search-service"></a>1 - Azure Cognitive Search サービスを作成する

このチュートリアルをすべて実行するには、[ポータルで作成](search-create-service-portal.md)できる Azure Cognitive Search サービスが必要です。 このチュートリアルは Free レベルを使用して完了できます。

次の手順のために、検索サービスの名前とその API キーを把握しておく必要があります。 それらの情報の調べ方がわからない場合は、こちらの[クイックスタート](search-create-service-portal.md#get-a-key-and-url-endpoint)を参照してください。


## <a name="2---set-up-postman"></a>2 - Postman を設定する

次に、Postman を起動して、[Azure-Samples/azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples) からダウンロードしたコレクションをインポートします。

コレクションをインポートするには、 **[Files]\(ファイル\)**  >  **[Import]\(インポート\)** に移動して、インポートしたいコレクション ファイルを選択します。

要求ごとに、次の作業を行う必要があります。

1. `<YOUR-SEARCH-SERVICE>` を検索サービスの名前に置き換えます。

1. `<YOUR-ADMIN-API-KEY>` を、検索サービスのプライマリ キーまたはセカンダリ キーに置き換えます。

  :::image type="content" source="media/search-get-started-rest/postman-url.png" alt-text="Postman の要求 URL とヘッダー" border="false":::

Postman に慣れていない場合は、[Azure Cognitive Search REST API を調べる方法](search-get-started-rest.md)に関するページを参照してください。

## <a name="3---create-an-initial-index"></a>3 - 初期インデックスを作成する

この手順では、初期インデックスを作成してドキュメントを読み込み、それらのドキュメントに対するクエリを実行することで、初回検索のパフォーマンスを確認します。

### <a name="create-index"></a>インデックスの作成

まず、`id` と `phone_number` の 2 つのフィールドを持つ単純なインデックスを `tutorial-basic-index` という名前で作成します。 まだアナライザーを定義していないので、`standard.lucene` アナライザーが既定で使用されます。

インデックスを作成するには、次の要求を送信します。

```http
PUT https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "fields": [
      {
        "name": "id",
        "type": "Edm.String",
        "key": true,
        "searchable": true,
        "filterable": false,
        "facetable": false,
        "sortable": true
      },
      {
        "name": "phone_number",
        "type": "Edm.String",
        "sortable": false,
        "searchable": true,
        "filterable": false,
        "facetable": false
      }
    ]
  }
```

### <a name="load-data"></a>データの読み込み

次に、インデックスにデータを読み込みます。 場合によっては、取り込む電話番号の形式を制御できないことも考えられるので、さまざまな種類の形式を背景にテストしてみましょう。 検索ソリューションからは、一致する電話番号がその形式にかかわらずすべて返されるのが理想的です。

データは、次の要求を使用してインデックスに読み込みます。 

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs/index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "value": [
      {
        "@search.action": "upload",  
        "id": "1",
        "phone_number": "425-555-0100"
      },
      {
        "@search.action": "upload",  
        "id": "2",
        "phone_number": "(321) 555-0199"
      },
      {  
        "@search.action": "upload",  
        "id": "3",
        "phone_number": "+1 425-555-0100"
      },
      {  
        "@search.action": "upload",  
        "id": "4",  
        "phone_number": "+1 (321) 555-0199"
      },
      {
        "@search.action": "upload",  
        "id": "5",
        "phone_number": "4255550100"
      },
      {
        "@search.action": "upload",  
        "id": "6",
        "phone_number": "13215550199"
      },
      {
        "@search.action": "upload",  
        "id": "7",
        "phone_number": "425 555 0100"
      },
      {
        "@search.action": "upload",  
        "id": "8",
        "phone_number": "321.555.0199"
      }
    ]  
  }
```

インデックスにデータが投入されたら、いつでも検索を開始することができます。

### <a name="search"></a>検索

直感的な検索を実現するために最も大切なことは、ユーザーによって入力されるクエリの書式を決めつけないことです。 `(425) 555-0100` の検索に使用される形式は、先ほど示したようにさまざまですが、それでも結果が返されることをユーザーは期待します。 この手順では、2 つのサンプル クエリを試して、それらがどのように実行されるかを確認します。

まず、`(425) 555-0100` を検索してみましょう。

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=(425) 555-0100
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  
```

このクエリでは、**期待される 4 つの結果のうち 3 つ** が返されますが、**予期しない結果も 2 つ** 返されます。

```json
{
    "value": [
        {
            "@search.score": 0.05634898,
            "phone_number": "+1 425-555-0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425 555 0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425-555-0100"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "(321) 555-0199"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "+1 (321) 555-0199"
        }
    ]
}
```

次に、書式なしの数値 `4255550100` を検索してみましょう。

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=4255550100
  api-key: <YOUR-ADMIN-API-KEY>
```

結果は先ほどよりも悪く、**4 つある正しい一致のうち 1 つ** しか返されません。

```json
{
    "value": [
        {
            "@search.score": 0.6015292,
            "phone_number": "4255550100"
        }
    ]
}
```

多くの人がこれらの結果に戸惑うことでしょう。 このような結果になる理由を次のセクションで深く掘り下げていきたいと思います。

## <a name="4---debug-search-results"></a>4 - 検索結果をデバッグする

検索結果を理解するためには、まず、アナライザーのしくみを理解することが大切です。 そこから、[Analyze Text API](/rest/api/searchservice/test-analyzer) を使用して既定のアナライザーをテストした後、実際の要件を満たしたアナライザーを作成します。

### <a name="how-analyzers-work"></a>アナライザーのしくみ

[アナライザー](search-analyzers.md)は、クエリ文字列内のテキストとインデックス付きドキュメントを処理する[フル テキスト検索エンジン](search-lucene-query-architecture.md)のコンポーネントです。 シナリオに応じて、さまざまなアナライザーがさまざまな方法でテキストを操作します。 このシナリオでは、電話番号に特化したアナライザーを作成する必要があります。

アナライザーは次の 3 つのコンポーネントから成ります。

+ [**文字フィルター**](#CharFilters): 入力テキスト内の個々の文字を削除または置換します。
+ [**トークナイザー**](#Tokenizers): 入力テキストをトークンに分割します。これらのトークンが、検索インデックスにおけるキーになります。
+ [**トークン フィルター**](#TokenFilters): トークナイザーによって生成されたトークンを処理します。

以下の図を見ると、この 3 つのコンポーネントが連携して文がトークン化されるようすをご覧いただけます。

  :::image type="content" source="media/tutorial-create-custom-analyzer/analyzers-explained.png" alt-text="文をトークン化するアナライザー処理の図":::

その後、高速なフルテキスト検索を可能にする転置インデックスにこれらのトークンが格納されます。  フルテキスト検索は、字句解析中に抽出された一意の語句すべてを、それが出現するドキュメントへと、転置インデックスによってマッピングすることで実現されます。 以下の図に例を示します。

  :::image type="content" source="media/tutorial-create-custom-analyzer/inverted-index-explained.png" alt-text="転置インデックスの例":::

すべての検索は、転置インデックスに格納された語句の検索に帰結します。 ユーザーからクエリが送信されると、次の処理が行われます。

1. クエリが解析されて、検索語が解析されます。
1. 転置インデックスがスキャンされて、一致する語句を含んだドキュメントが検出されます。
1. 最後に、取得されたドキュメントが[類似性アルゴリズム](index-ranking-similarity.md)によってランク付けされます。

  :::image type="content" source="media/tutorial-create-custom-analyzer/query-architecture-explained.png" alt-text="類似性をランク付けするアナライザー処理の図":::

検索語が転置インデックス内の語句と一致しなかった場合、結果は返されません。 クエリの機構について詳しくは、[フルテキスト検索](search-lucene-query-architecture.md)に関する記事を参照してください。

> [!Note]
> [部分的な語句のクエリ](search-query-partial-matching.md)は、この規則の重要な例外です。 これらのクエリ (プレフィックス クエリ、ワイルドカード クエリ、正規表現クエリ) では、通常の語句クエリとは異なり、字句解析プロセスがバイパスされます。 部分的な語句は、小文字化のみが適用されて、インデックス内の語句と比較されます。 これらの種類のクエリをサポートするようにアナライザーが構成されていなかった場合、往々にして予期しない結果となります。一致する語句がインデックスに存在しないためです。

### <a name="test-analyzer-using-the-analyze-text-api"></a>Analyze Text API を使用してアナライザーをテストする

Azure Cognitive Search には、[Analyze Text API](/rest/api/searchservice/test-analyzer) が用意されています。Analyze Text API を使用してアナライザーをテストし、そのテキスト処理の動作を理解することができます。

Analyze Text API は、次の要求を使用して呼び出します。

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "text": "(425) 555-0100",
    "analyzer": "standard.lucene"
  }
```

すると、テキストから抽出されたトークンのリストが返されます。 標準の Lucene アナライザーによって、電話番号が 3 つの独立したトークンに分割されているのがわかります。

```json
{
    "tokens": [
        {
            "token": "425",
            "startOffset": 1,
            "endOffset": 4,
            "position": 0
        },
        {
            "token": "555",
            "startOffset": 6,
            "endOffset": 9,
            "position": 1
        },
        {
            "token": "0100",
            "startOffset": 10,
            "endOffset": 14,
            "position": 2
        }
    ]
}
```

反対に、区切りなしで書式設定された電話番号 `4255550100` は単一のトークンにトークン化されています。

```json
{
  "text": "4255550100",
  "analyzer": "standard.lucene"
}
```

```json
{
    "tokens": [
        {
            "token": "4255550100",
            "startOffset": 0,
            "endOffset": 10,
            "position": 0
        }
    ]
}
```

インデックスが作成されたドキュメントと検索語との両方が解析されることに注意してください。 前の手順の検索結果を振り返ってみると、このような結果が返された理由が見えてきます。

1 つ目のクエリで間違った電話番号が返されたのは、それらの語句の 1 つ (`555`) が、検索した語句の 1 つと一致したためです。 2 つ目のクエリで、数値が 1 つしか返されなかったのは、それが `4255550100` と一致する語句を含む唯一のレコードであったためです。

## <a name="5---build-a-custom-analyzer"></a>5 - カスタム アナライザーを作成する

現状の結果について理解したら、カスタム アナライザーを作成して、トークン化のロジックを改善しましょう。

目標は、インデックスされた文字列やクエリの書式に関係なく、電話番号に対する直感的な検索を実現することです。 そのような結果を得るためには、[文字フィルター](#CharFilters)、[トークナイザー](#Tokenizers)、[トークン フィルター](#TokenFilters)を指定します。

<a name="CharFilters"></a>

### <a name="character-filters"></a>文字フィルター

文字フィルターは、トークナイザーに取り込まれる前のテキストを処理する目的で使用されます。 文字フィルターの一般的な用途として、HTML 要素をフィルターで除去したり、特殊文字を置換したりすることが挙げられます。

電話番号に関しては、空白と特殊文字を除去する必要があります。電話番号の形式によっては、同じ特殊文字や空白が含まれていない場合もあるためです。

```json
"charFilters": [
    {
      "@odata.type": "#Microsoft.Azure.Search.MappingCharFilter",
      "name": "phone_char_mapping",
      "mappings": [
        "-=>",
        "(=>",
        ")=>",
        "+=>",
        ".=>",
        "\\u0020=>"
      ]
    }
  ]
```

上記のフィルターでは、`-` `(` `)` `+` `.` に加え、空白が入力から削除されます。

|入力|出力|  
|-|-|  
|`(321) 555-0199`|`3215550199`|  
|`321.555.0199`|`3215550199`|

<a name="Tokenizers"></a>

### <a name="tokenizers"></a>Tokenizer

トークナイザーは、テキストをトークンに分割すると共に、その過程で一部の文字 (句読点など) を破棄します。 多くの場合、文を個々の単語に分割するのがトークン化の目標です。

このシナリオでは、電話番号を 1 つの語句としてキャプチャしたいので、キーワード トークナイザー `keyword_v2` を使用します。 この問題を解決する方法は他にもあることに注意してください。 以降の「[別のアプローチ](#Alternate)」のセクションを参照してください。

キーワード トークナイザーは常に、1 つの語句として与えられる場合と同じテキストを出力します。

|入力|出力|  
|-|-|  
|`The dog swims.`|`[The dog swims.]`|  
|`3215550199`|`[3215550199]`|

<a name="TokenFilters"></a>

### <a name="token-filters"></a>トークン フィルター

トークン フィルターは、トークナイザによって生成されたトークンを除外したり加工したりします。 トークン フィルターの一般的な用途の 1 つは、lowercase トークン フィルターを使用して、すべての文字を小文字化することです。 また、`the`、`and`、`is` などのストップワードをフィルターで除去する用途にも使用されます。

このシナリオではどちらのフィルターも使用しませんが、電話番号の部分検索に対応できるよう、nGram トークン フィルターを使用します。

```json
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.NGramTokenFilterV2",
    "name": "custom_ngram_filter",
    "minGram": 3,
    "maxGram": 20
  }
]
```

#### <a name="ngramtokenfilterv2"></a>NGramTokenFilterV2

[nGram_v2 トークン フィルター](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)は、`minGram` と `maxGram` パラメーターに基づいて、特定のサイズの n-gram にトークンを分割します。

電話番号のアナライザーの場合、`minGram` は `3` に設定します。ユーザーが検索に使用すると考えられる部分文字列の最小文字数が 3 であるためです。 `maxGram` は、内線番号を含んでいてもすべての電話番号が単一の n-gram に収まるよう、`20` に設定します。

 残念ながら n-gram には、ある程度の擬陽性が返されるという副作用があります。 この点は手順 7. で、n-gram トークン フィルターを含まないアナライザーを検索用に別途作成することで修正します。

|入力|出力|  
|-|-|  
|`[12345]`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`[3215550199]`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

### <a name="analyzer"></a>アナライザー

文字フィルター、トークナイザー、トークン フィルターが揃ったら、アナライザーを定義する準備は完了です。

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_ngram_filter"
    ],
    "charFilters": [
      "phone_char_mapping"
    ]
  }
]
```

|入力|出力|  
|-|-|  
|`12345`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`(321) 555-0199`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

出力に含まれるすべてのトークンが検索できるようになった点に注目してください。 これらのトークンのいずれかがクエリに含まれていれば、電話番号が返されます。

カスタム アナライザーを定義したら、それを次の手順のテストに使用できるよう、インデックスを再作成します。 簡潔にするために、定義したアナライザーを使用して、`tutorial-first-analyzer` という名前の新しいインデックスを Postman コレクションで作成します。

## <a name="6---test-the-custom-analyzer"></a>6 - カスタム アナライザーをテストする

インデックスの作成後は、次の要求を使用して、作成したアナライザーを試すことができます。

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-first-analyzer/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  

  {
    "text": "+1 (321) 555-0199",
    "analyzer": "phone_analyzer"
  }
```

これで、電話番号から生成されたトークンのコレクションを確認できます。

```json
{
    "tokens": [
        {
            "token": "132",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "1321",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "13215",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        ...
        ...
        ...
    ]
}
```

## <a name="7---build-a-custom-analyzer-for-queries"></a>7 - クエリ用のカスタム アナライザーを作成する

カスタム アナライザーを使用して、インデックスに対していくつかのサンプル クエリを実行してみると、再現率が向上して、一致するすべての電話番号が返されるようになったことに気付くでしょう。 ただし、n-gram トークン フィルターが原因で、ある程度の擬陽性の結果も返されます。 これは、n-gram トークン フィルターではよくある副作用です。

擬陽性を避けるために、ここでは、クエリ用のアナライザーを別途作成します。 このアナライザーは既に作成したアナライザーと同じですが、`custom_ngram_filter` が **ありません**。

```json
    {
      "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
      "name": "phone_analyzer_search",
      "tokenizer": "custom_tokenizer_phone",
      "tokenFilters": [],
      "charFilters": [
        "phone_char_mapping"
      ]
    }
```

インデックスの定義では、`indexAnalyzer` と `searchAnalyzer` の両方を指定することになります。

```json
    {
      "name": "phone_number",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "indexAnalyzer": "phone_analyzer",
      "searchAnalyzer": "phone_analyzer_search"
    }
```

このように変更すれば準備は完了です。 インデックスを再作成して、データのインデックスを作成し、クエリをテストして、検索が適切に実行されることを確認します。 Postman コレクションを使用している場合、`tutorial-second-analyzer` という名前の 3 つ目のインデックスが作成されます。

<a name="Alternate"></a>

## <a name="alternate-approaches"></a>別のアプローチ

前出のアナライザーは、検索の柔軟性を最大化する設計となっています。 しかしその代償として、重要ではない可能性のある語句が多数インデックスに格納されています。

以下に示したのは、このタスクに使用できる別のアナライザーの例です。 

このアナライザーはうまく機能しますが、例外があります。`14255550100` などの入力データでは、電話番号を論理的に切り分けることが難しくなるのです。 たとえばこのアナライザーでは、国番号 (`1`) と市外局番 (`425`) を区切ることができません。 ユーザーが国番号を含めずに検索した場合、この不一致が原因で、前述の数字が返されない可能性があります。

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer_shingles",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_shingle_filter"
    ]
  }
],
"tokenizers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.StandardTokenizerV2",
    "name": "custom_tokenizer_phone",
    "maxTokenLength": 4
  }
],
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.ShingleTokenFilter",
    "name": "custom_shingle_filter",
    "minShingleSize": 2,
    "maxShingleSize": 6,
    "tokenSeparator": ""
  }
]
```

下の例を見るとわかるように、電話番号は、ユーザーが一般に検索するであろうまとまりに分割されます。

|入力|出力|  
|-|-|  
|`(321) 555-0199`|`[321, 555, 0199, 321555, 5550199, 3215550199]`|

要件によっては、これが問題へのより効率的なアプローチとなる可能性があります。

## <a name="reset-and-rerun"></a>リセットして再実行する

簡潔にするために、このチュートリアルでは、新しいインデックスを 3 つ作成しました。 しかしインデックスは、開発の早い段階で削除し、再作成するのが一般的です。 インデックスは、Azure portal または次の API 呼び出しを使用して削除することができます。

```http
DELETE https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  api-key: <YOUR-ADMIN-API-KEY>
```

## <a name="takeaways"></a>重要なポイント

このチュートリアルでは、カスタム アナライザーを作成してテストするプロセスを紹介しました。 インデックスを作成してデータのインデックスを作成し、そのインデックスに対してクエリを実行して、返される検索結果を確認しました。 そこから Analyze Text API を使用して、実行中の字句解析プロセスを見てきました。

このチュートリアルで定義したアナライザーは、電話番号を検索するための簡単なソリューションとなっていますが、同じプロセスを使用すれば、あらゆるシナリオを対象にしたカスタム電話番号アナライザーを作成することができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

所有するサブスクリプションを使用している場合は、プロジェクトの終了時に、不要になったリソースを削除することをお勧めします。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ペインにある [すべてのリソース] または [リソース グループ] リンクを使って、リソースを検索および管理できます。

## <a name="next-steps"></a>次のステップ

カスタム アナライザーの作成方法がわかったら、高度な検索環境を構築するために用意されているさまざまなフィルター、トークナイザー、アナライザーを見てみましょう。

> [!div class="nextstepaction"]
> [Azure Cognitive Search のカスタム アナライザー](index-add-custom-analyzers.md)