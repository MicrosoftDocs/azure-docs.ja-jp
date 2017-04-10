---
title: "Machine Learning APIs: テキスト分析 | Microsoft Docs"
description: "Microsoft の Machine Learning テキスト分析 API を使用し、センチメント分析、キー フレーズの抽出、言語検出、トピック検出の非構造化テキストを分析できます。"
services: machine-learning
documentationcenter: 
author: onewth
manager: jhubbard
editor: cgronlun
ms.assetid: 5b60694e-5521-4e4d-bf6a-1a92fdf94b65
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: onewth
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: ../cognitive-services/cognitive-services-text-analytics-quick-start
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f4389705a81b531bd706cbabc0b4c3b171febd5f
ms.lasthandoff: 11/17/2016


---
# <a name="machine-learning-apis-text-analytics-for-sentiment-key-phrase-extraction-language-detection-and-topic-detection"></a>Machine Learning API: センチメントのテキスト分析、キー フレーズ抽出、言語検出、トピック検出
> [!NOTE]
> このガイドはバージョン 1 の API 用です。 バージョン 2 については、[**こちらのドキュメントを参照**](../cognitive-services/cognitive-services-text-analytics-quick-start.md)してください。 この API の推奨バージョンは現在、バージョン 2 となっています。
> 
> 

## <a name="overview"></a>Overview
テキスト分析 API は、Azure Machine Learning で構築されたテキスト分析 [Web サービス](https://datamarket.azure.com/dataset/amla/text-analytics)です。 API を使用して、センチメント分析、キー フレーズの抽出、言語検出、トピック検出などのタスクの非構造化テキストを分析することができます。 この API を使用するためにトレーニング データは必要ありません。自分のテキスト データを使用するだけです。 この API は、高度な自然言語の処理手法を使用し、このクラスで最高の予測機能を提供します。

[デモ サイト](https://text-analytics-demo.azurewebsites.net/)で実際のテキスト分析をご覧いただけます。このサイトには、C# と Python のテキスト分析方法に関する[サンプル](https://text-analytics-demo.azurewebsites.net/Home/SampleCode)もあります。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

- - -
## <a name="sentiment-analysis"></a>センチメント分析
API は、0 と 1 の間の数値スコアを返します。 1 に近いスコアは正のセンチメントを表し、0 に近いスコアは負のセンチメントを表します。 センチメントのスコアは、分類の手法を使用して生成されます。 分類子への入力機能には、会話の一部であることを示すタグと単語の埋め込みから生成される、n-gram 機能が含まれます。 現在、サポートされている言語は英語だけです。

## <a name="key-phrase-extraction"></a>キー フレーズの抽出
API は、入力テキストの要点を示す文字列のリストを返します。 Microsoft Office の高度な自然言語処理ツールキットの手法を採用しています。 現在、サポートされている言語は英語だけです。

## <a name="language-detection"></a>言語検出
この API は、検出した言語と 0 と 1 の間の数値スコアを返します。 1 に近いスコアは、100% の確実性で正しい言語が特定されたことを示します。 合計で 120 種類の言語がサポートされています。

## <a name="topic-detection"></a>トピック検出
これは新しくリリースされた API であり、送信されたテキスト レコードの一覧に基づき検出されたトピックの上位を返します。 トピックはキー フレーズ、つまり、1 つまたは複数の関連単語で特定されます。 この API を利用するには、100 件以上のテキスト レコードを送信する必要がありますが、数百から数千単位のレコードからトピックを検出するように設計されています。 この API では、送信されるテキスト レコードあたり 1 トランザクションが請求されることに注意してください。 この API は、レビューやユーザー フィードバックなど、人間が書いた短いテキストで効果的に機能するように設計されています。

- - -
## <a name="api-definition"></a>API の定義
### <a name="headers"></a>ヘッダー
要求には次のような正しいヘッダーを必ず追加します。

    Authorization: Basic <creds>
    Accept: application/json

    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

アカウントのアカウント キーは [Azure データ マーケット](https://datamarket.azure.com/account/keys)で確認できます。 現在、入力形式および出力形式として受け付けられるのは JSON だけであることに注意してください。 XML はサポートされていません。

- - -
## <a name="single-response-apis"></a>シングル リソース API
### <a name="getsentiment"></a>GetSentiment
**URL**    

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**要求の例**

次の呼び出しでは、語句 "Hello World" のセンチメント分析を要求しています。

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

次のような応答が返されます。

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
        "Score":1.0
    }

- - -
### <a name="getkeyphrases"></a>GetKeyPhrases
**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**要求の例**

次の呼び出しでは、テキスト "ユニークな装飾がされていてスタッフも親しみやすく、素晴らしいホテルでした" のキー フレーズを要求しています。

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
    Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

次のような応答が返されます。

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
      "KeyPhrases":[
        "wonderful hotel",
        "unique decor",
        "friendly staff"
      ]
    }

- - -
### <a name="getlanguage"></a>GetLanguage
**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**要求の例**

次の GET 呼び出しでは、テキスト *Hello World*

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
    Text=Hello+World

次のような応答が返されます。

    {
      "UnknownLanguage": false,
      "DetectedLanguages": [{
        "Name": "English",
        "Iso6391Name": "en",
        "Score": 1.0
      }]
    }

**省略可能なパラメーター**

`NumberOfLanguagesToDetect` は省略可能なパラメーターです。 既定値は 1 です。

- - -
## <a name="batch-apis"></a>Batch API
Text Analytics サービスでは、センチメントとキー フレーズの抽出をバッチ モードで実行できます。 スコアが付けられたレコードはそれぞれ 1 つのトランザクションとして数えられることに注意してください。 たとえば、1 回の呼び出しで 1000 個のレコードのセンチメントを要求する場合、1000 のトランザクションが推論されます。

システムに入力された ID はシステムから返された ID であることに注意してください。 Web サービスは、ID が一意であることを確認しません。 一意であることを確認するのは呼び出し元の役割です。 

### <a name="getsentimentbatch"></a>GetSentimentBatch
**URL**    

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**要求の例**

次の POST 呼び出しでは、要求の本文に、"Hello World"、"Hello Foo World"、"Hello My World" のフレーズのセンチメントを要求しています。

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

要求本文:

    {"Inputs":
    [
        {"Id":"1","Text":"hello world"},
        {"Id":"2","Text":"hello foo world"},
        {"Id":"3","Text":"hello my world"},
    ]}

次の応答では、テキスト ID に関連付けられているスコアの一覧を取得します。

    {
      "odata.metadata":"<url>", 
      "SentimentBatch":
      [
        {"Score":0.9549767,"Id":"1"},
        {"Score":0.7767222,"Id":"2"},
        {"Score":0.8988889,"Id":"3"}
      ],  
      "Errors":[]
    }


- - -
### <a name="getkeyphrasesbatch"></a>GetKeyPhrasesBatch
**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**要求の例**

この例では、次のテキストのキー フレーズのセンチメントの一覧を要求しています。 

* "ユニークな装飾がされていてスタッフも親しみやすく、素晴らしいホテルでした"
* "非常に興味深い話題が出た素晴らしいビルド会議でした"
* "激しい交通渋滞で、空港に到着するのに 3 時間かかりました"

この要求は、エンドポイントへの POST 呼び出しとして作成されています。

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

要求本文:

    {"Inputs":
    [
        {"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
        {"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
        {"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
    ]}

次の応答では、テキスト ID に関連付けられているキー フレーズの一覧を取得します。

    { "odata.metadata":"<url>",
         "KeyPhrasesBatch":
        [
           {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
           {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
           {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
        ],
        "Errors":[]
    }

- - -
### <a name="getlanguagebatch"></a>GetLanguageBatch

下の POST 呼び出しでは、2 つのテキスト入力の言語検出を要求しています。

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

要求本文:

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

これで次の応答が返されます。英語が最初の入力で検出され、フランス語が 2 つ目の入力で検出されます。

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "English",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "French",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

- - -
## <a name="topic-detection-apis"></a>トピック検出 API
これは新しくリリースされた API であり、送信されたテキスト レコードの一覧に基づき検出されたトピックの上位を返します。 トピックはキー フレーズ、つまり、1 つまたは複数の関連単語で特定されます。 この API では、送信されるテキスト レコードあたり 1 トランザクションが請求されることに注意してください。

この API を利用するには、100 件以上のテキスト レコードを送信する必要がありますが、数百から数千単位のレコードからトピックを検出するように設計されています。

### <a name="topics--submit-job"></a>トピック – 送信ジョブ
**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection

**要求の例**

下の POST 呼び出しでは、100 件の記事からなる 1 セットに対してトピックを要求しています。最初と最後の Input と 2 つの StopPhrases が表示されています。

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection HTTP/1.1

要求本文:

    {"Inputs":[
        {"Id":"1","Text":"I loved the food at this restaurant"},
        ...,
        {"Id":"100","Text":"I hated the decor"}
    ],
    "StopPhrases":[
        "restaurant", “visitor"
    ]}

下の応答では、送信されたジョブの JobId を取得します。

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

トピックとして返すべきではない 1 つの単語または複数の語句の一覧を利用し、 非常に一般的なトピックを除外できます。 たとえば、ホテルのレビューに関するデータセットで、"hotel" と "hostel" は理にかなったストップ フレーズとなります。  

### <a name="topics--poll-for-job-results"></a>トピック – ジョブ結果のポーリング
**URL**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult

**要求の例**

"ジョブの送信" 手順から返された JobId を渡し、結果を取得します。 応答に「Status=’Complete’」が含まれるまで、このエンドポイントを 1 分ごとに呼び出すことが推奨されます。 ジョブのレコードが数千単位になる場合、完了に 10 分以上かかります。

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult?JobId=<JobId>


処理中の応答は次のようになります。

    {
        "odata.metadata":"<url>",
        "Status":"Running",
         "TopicInfo":[],
        "TopicAssignment":[],
        "Errors":[]
    }


この API は、JSON 形式の出力を次の形式で返します。

    {
        "odata.metadata":"<url>",
        "Status":"Finished",
        "TopicInfo":[
        {
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Score":8.0,
            "KeyPhrase":"food"
        },
        ...
        {
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Score":6.0,
            "KeyPhrase":"decor"
            }
          ],
        "TopicAssignment":[
        {
            "Id":"1",
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Distance":0.7809
        },
        ...
        {
            "Id":"100",
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Distance":0.8034
        }
        ],
        "Errors":[]


応答の各部分のプロパティは次のとおりです。

**TopicInfo プロパティ**

| キー | Description |
|:--- |:--- |
| TopicId |各トピックの一意の識別子。 |
| Score |トピックに割り当てられているレコードの数。 |
| KeyPhrase |トピックの要約となる単語または語句。 1 つの単語の場合もあれば、複数の単語の場合もあります。 |

**TopicAssignment プロパティ**

| キー | Description |
|:--- |:--- |
| ID |レコードの識別子。 入力に含まれる ID と同じです。 |
| TopicId |レコードが割り当てられているトピック ID。 |
| Distance |レコードがトピックに属する確実性。 Distance がゼロに近ければ、それだけ確実性が高くなります。 |


