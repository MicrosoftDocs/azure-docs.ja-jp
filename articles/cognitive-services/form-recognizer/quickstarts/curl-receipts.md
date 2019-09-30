---
title: クイック スタート:cURL を使用してレシートのデータを抽出する - Form Recognizer
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、cURL で Form Recognizer REST API を使用して、レシートの画像からデータを抽出します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: badd674030ec014f2e70050c3c45599a26b17882
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71073827"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-curl"></a>クイック スタート:cURL で Form Recognizer REST API を使用してレシートのデータを抽出する

このクイックスタートでは、cURL で Azure Form Recognizer REST API を使用して、レシート内の重要な情報を抽出および特定します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件
このクイック スタートを完了するには、以下が必要です。
- アクセスが制限された Form Recognizer プレビューへのアクセス。 プレビューへのアクセスを取得するには、[Form Recognizer アクセス要求](https://aka.ms/FormRecognizerRequestAccess)フォームに記入して送信します。
- インストールされた [cURL](https://curl.haxx.se/windows/)。
- レシートの画像の URL。 このクイックスタートでは、[サンプルの画像](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true)を使用できます。

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer リソースを作成する

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>レシートを分析する

レシートの分析を開始するには、下の cURL コマンドを使用して **Analyze Receipt** API を呼び出します。 コマンドを実行する前に、次の変更を行います。

1. `<Endpoint>` を、Form Recognizer サブスクリプション キーで取得したエンドポイントで置き換えます。 これは、Form Recognizer リソースの **[概要]** タブにあります。
1. `<your receipt URL>` を、レシートの画像の URL アドレスに置き換えます。
1. `<subscription key>` を、前の手順からコピーしたサブスクリプション キーに置き換えます。

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"url\": \"<your receipt URL>\"}"
```

**Operation-Location** ヘッダーを含む `202 (Success)` 応答を受信します。 このヘッダーの値に含まれる操作 ID を使用して、操作の状態のクエリを実行し、結果を取得できます。 次の例では、`operations/` の後ろの文字列が操作 ID です。

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>レシートの結果を取得する

**Analyze Receipt** API を呼び出した後に **Get Receipt Result** API を呼び出して、操作の状態と抽出されたデータを取得します。

1. `<operationId>` を、前の手順の操作 ID に置き換えます。
1. `<subscription key>` は、実際のサブスクリプション キーで置き換えてください。

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/operations/<operationId>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>結果の確認

JSON 出力で `200 (Success)` 応答を受信します。 最初のフィールド `"status"` は、操作の状態を示します。 操作が完了した場合、`"recognitionResults"` フィールドにはレシートから抽出された各テキスト行が含まれ、`"understandingResults"` フィールドにはレシートの最も重要な部分のキー/値の情報が含まれます。 操作が完了していない場合、`"status"` の値は `"Running"` または `"NotStarted"` になり、手動またはスクリプトでもう一度 API を呼び出す必要があります。 呼び出しの間隔は 1 秒以上あけることをお勧めします。

次のレシートの画像とそれに対応する JSON 出力をご覧ください。 出力は、読みやすくするために一部省略されています。

![Contoso ストアのレシート](../media/contoso-receipt.png)

```json
{
  "status": "Succeeded",
  "recognitionResults": [{
    "page": 1,
    "clockwiseOrientation": 0.36,
    "width": 1688,
    "height": 3000,
    "unit": "pixel",
    "lines": [{
      "boundingBox": [616, 291, 1050, 278, 1053, 384, 620, 397],
      "text": "Contoso",
      "words": [{
        "boundingBox": [619, 292, 1051, 284, 1052, 382, 620, 398],
        "text": "Contoso"
      }]
    }, {
      "boundingBox": [322, 588, 501, 600, 497, 655, 318, 643],
      "text": "Contoso",
      "words": [{
        "boundingBox": [330, 590, 501, 602, 499, 654, 326, 644],
        "text": "Contoso"
      }]
    },
    ...
    ]
  }],
  "understandingResults": [{
    "pages": [1],
    "fields": {
      "Subtotal": {
        "valueType": "numberValue",
        "value": 1098.99,
        "text": "1098.99",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/14/words/1"
        }]
      },
      "Total": {
        "valueType": "numberValue",
        "value": 1203.39,
        "text": "1203.39",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/18/words/1"
        }]
      },
      "Tax": {
        "valueType": "numberValue",
        "value": 104.4,
        "text": "$104.40",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/16/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/16/words/1"
        }]
      },
      "MerchantAddress": {
        "valueType": "stringValue",
        "value": "123 Main Street Redmond, WA 98052",
        "text": "123 Main Street Redmond, WA 98052",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/2/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/2"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/2"
        }]
      },
      "MerchantName": {
        "valueType": "stringValue",
        "value": "Contoso",
        "text": "Contoso",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/1/words/0"
        }]
      },
      "MerchantPhoneNumber": {
        "valueType": "stringValue",
        "value": null,
        "text": "123-456-7890",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/4/words/0"
        }]
      },
      "TransactionDate": {
        "valueType": "stringValue",
        "value": "2019-06-10",
        "text": "6/10/2019",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/0"
        }]
      },
      "TransactionTime": {
        "valueType": "stringValue",
        "value": "13:59:00",
        "text": "13:59",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/1"
        }]
      }
    }
  }]
}
```

## <a name="next-steps"></a>次の手順

このクイックスタートでは、cURL で Form Recognizer REST API を使用して、レシートの内容を抽出しました。 次に、Form Recognizer API の詳細を把握するためにリファレンス ドキュメントを参照します。

> [!div class="nextstepaction"]
> [REST API リファレンス ドキュメント](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
