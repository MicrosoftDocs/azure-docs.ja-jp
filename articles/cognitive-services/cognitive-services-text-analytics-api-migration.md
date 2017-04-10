---
title: "Text Analytics API のバージョン 2 へのアップグレード | Microsoft Docs"
description: "Azure Machine Learning Text Analytics - バージョン 2 へのアップグレード"
services: cognitive-services
documentationcenter: 
author: onewth
manager: jhubbard
editor: cgronlun
ms.assetid: bbf86f80-f677-42f3-8c17-118b16a23c34
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: onewth
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9b28666e384402935ae342788afff6b6e8c7eab8


---
# <a name="upgrading-to-version-2-of-the-text-analytics-api"></a>Text Analytics API のバージョン 2 へのアップグレード
このガイドでは、コードをアップグレードし、 [この API の最初のバージョン](../machine-learning/machine-learning-apps-text-analytics.md) ではなくバージョン 2 が使われるようにする手順について説明します。 

この API の使用経験がない場合は、**[この API に関するページ](//go.microsoft.com/fwlink/?LinkID=759711)**や**[クイック スタート ガイド](//go.microsoft.com/fwlink/?LinkID=760860)**を参照して、詳しい情報を入手できます。 テクニカル リファレンスについては、**[API の定義](//go.microsoft.com/fwlink/?LinkID=759346)**を参照してください。

### <a name="part-1-get-a-new-key"></a>パート 1. 新しいキーを取得する
最初に、新しい API キーを **Azure ポータル**から取得する必要があります。

1. [Cortana Analytics ギャラリー](//gallery.cortanaintelligence.com/MachineLearningAPI/Text-Analytics-2)から Text Analytics サービスに移動します。 ここには、ドキュメントやコード サンプルへのリンクも用意されています。
2. **[サインアップ]**をクリックします。 Azure 管理ポータルに移動し、サービスにサインアップできます。
3. プランを選択します。 1 か月あたり 5,000 トランザクションの **Free レベル**を選択してもかまいません。 無料プランと同様に、サービスの使用に対しては課金されません。 Azure サブスクリプションにログインする必要があります。 
4. Text Analytics にサインアップすると、 **API キー**が提供されます。 API サービスを使用するときに必要になるため、このキーをメモしておきます。

### <a name="part-2-update-the-headers"></a>パート 2. ヘッダーを更新する
送信されたヘッダー値を次のように更新します。 アカウント キーはもうエンコードされないことに注意してください。

**Version 1**

    Authorization: Basic base64encode(<your Data Market account key>)
    Accept: application/json

**Version 2**

    Content-Type: application/json
    Accept: application/json
    Ocp-Apim-Subscription-Key: <your Azure Portal account key>


### <a name="part-3-update-the-base-url"></a>パート 3. ベース URL を更新する
**Version 1**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/

**Version 2**

    https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/

### <a name="part-4a-update-the-formats-for-sentiment-key-phrases-and-languages"></a>パート 4a. センチメント、キー フレーズ、および言語の形式を更新する
#### <a name="endpoints"></a>エンドポイント
GET エンドポイントは廃止されました。したがって、すべての入力を POST 要求として送信する必要があります。 エンドポイントを次に示すエンドポイントに更新します。

|  | バージョン 1 の単一エンドポイント | バージョン 1 のバッチ エンドポイント | バージョン 2 のエンドポイント |
| --- | --- | --- | --- |
| 呼び出しの種類 |GET |POST |POST |
| センチメント |```GetSentiment``` |```GetSentimentBatch``` |```sentiment``` |
| キー フレーズ |```GetKeyPhrases``` |```GetKeyPhrasesBatch``` |```keyPhrases``` |
| 言語 |```GetLanguage``` |```GetLanguageBatch``` |```languages``` |

#### <a name="input-formats"></a>入力形式
使用できる形式が POST 形式のみになったため、以前に単一ドキュメント エンドポイントを使用していた入力の形式を変更する必要があります。 入力の大文字と小文字は区別されません。

**バージョン 1 (バッチ)**

    {
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Version 2**

    {
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="output-from-sentiment"></a>センチメントからの出力
**Version 1**

    {
      "SentimentBatch":[{
        "Id":"string",
        "Score":"double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Version 2**

    {
      "documents":[{
        "id":"string",
        "score":"double"
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-key-phrases"></a>キー フレーズからの出力
**Version 1**

    {
      "KeyPhrasesBatch":[{
        "Id":"string",
        "KeyPhrases":["string"]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Version 2**

    {
      "documents":[{
        "id":"string",
        "keyPhrases":["string"]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-languages"></a>言語からの出力
**Version 1**

    {
      "LanguageBatch":[{
        "id":"string",
        "detectedLanguages": [{
          "Score":"double"
          "Name":"string",
          "Iso6391Name":"string"
        }]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Version 2**

    {
      "documents":[{
        "id":"string",
        "detectedLanguages": [{
          "score":"double"
          "name":"string",
          "iso6391Name":"string"
        }]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }


### <a name="part-4b-update-the-formats-for-topics"></a>パート 4b. トピックの形式を更新する
#### <a name="endpoints"></a>エンドポイント
|  | バージョン 1 のエンドポイント | バージョン 2 のエンドポイント |
| --- | --- | --- |
| トピックの検出用に送信する (POST) |```StartTopicDetection``` |```topics``` |
| トピックの結果を取得する (GET) |```GetTopicDetectionResult?JobId=<jobId>``` |```operations/<operationId>``` |

#### <a name="input-formats"></a>入力形式
**Version 1**

    {
      "StopWords": [
        "string"
      ],
      "StopPhrases": [
        "string"
      ], 
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Version 2**

    {
      "stopWords": [
        "string"
      ],
      "stopPhrases": [
        "string"
      ],
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="submission-results"></a>送信結果
**バージョン 1 (POST)**

以前は、ジョブが完了すると、次の JSON 出力が返されていました。出力を取得できるように、jobId が URL に付加されています。

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

**バージョン 2 (POST)**

応答に、次のようにヘッダー値が含まれるようになりました。ここで、`operation-location` は、結果のポーリング用のエンドポイントとして使用されます。

    'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

#### <a name="operation-results"></a>操作の結果
**バージョン 1 (GET)**

    {
      "TopicInfo" : [{
        "TopicId" : "string"
        "Score" : "double"
        "KeyPhrase" : "string"
      }],
      "TopicAssignment" : [{
        "Id" : "string",
        "TopicId" : "string",
        "Distance" : "double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**バージョン 2 (GET)**

以前と同様に、出力が返されるまで、 **定期的に出力がポーリングされます** (推奨される間隔は 1 分ごとです)。 

トピックの API が完了すると、ステータス `succeeded` が返されます。 これには、次に示す形式の出力結果が含まれます。

    {
        "status": "succeeded",
        "createdDateTime": "string",
        "operationType": "topics",
        "processingResult": {
            "topics" : [{
            "id" : "string"
            "score" : "double"
            "keyPhrase" : "string"
          }],
          "topicAssignments" : [{
            "topicId" : "string",
            "documentId" : "string",
            "distance" : "double"
          }],
          "errors" : [{
              "id":"string",
              "message":"string"
          }]
        }
    }

### <a name="part-5-test-it"></a>パート 5. テストする
これで準備ができました。 小さなサンプルでコードをテストして、データを正常に処理できることを確認します。




<!--HONumber=Nov16_HO3-->


