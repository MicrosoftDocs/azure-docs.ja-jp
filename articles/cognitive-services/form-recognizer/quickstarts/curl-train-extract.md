---
title: クイック スタート:cURL を使用してモデルをトレーニングし、フォーム データを抽出する - Form Recognizer
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、cURL で Form Recognizer REST API を使用してモデルをトレーニングし、フォームからデータを抽出します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: f4e26d74ff0922841d2ceb2ce4eb06b96c697a9f
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719098"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>クイック スタート:cURL で REST API を使用して Form Recognizer モデルをトレーニングし、フォーム データを抽出する

このクイックスタートでは、cURL で Azure Form Recognizer の REST API を使用してトレーニングし、フォームをスコア付けしてキーと値のペアおよびテーブルを抽出します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。
- インストールされた [cURL](https://curl.haxx.se/windows/)。
- 同じ種類の少なくとも 6 つのフォームのセット。 そのうちの 5 つを使用してモデルをトレーニングした後、6 つ目のフォームでそれをテストします。 使用するフォームはファイルの種類が異なってもかまいませんが、ドキュメントの種類は同じである必要があります。 このクイックスタートでは、[サンプル データ セット](https://go.microsoft.com/fwlink/?linkid=2090451)を使用できます。 Standard パフォーマンス レベルの Azure Storage アカウントの BLOB ストレージ コンテナーのルートに、トレーニング ファイルをアップロードします。 テスト ファイルは別個のフォルダーに置くことができます。

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer リソースを作成する

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Form Recognizer モデルをトレーニングする

まず、Azure Storage Blob にトレーニング データのセットが必要です。 主な入力データと同じ種類/構造の入力済みフォーム (PDF ドキュメントやイメージ) が少なくとも 5 つ必要です。 または、2 つの入力済みフォームを持つ 1 つの空のフォームを使用できます。 空のフォームのファイル名には "empty" の語を含める必要があります。 トレーニング データをまとめるためのヒントとオプションについては、[カスタム モデル用のトレーニング データ セットの作成](../build-training-data-set.md)に関する記事を参照してください。

> [!NOTE]
> ラベル付きデータ機能を使用すると、トレーニング データの一部またはすべてにあらかじめ手動でラベルを付けることができます。 これは複雑なプロセスですが、トレーニングされたモデルの精度が向上します。 この機能の詳細については、概要に関するページの「[ラベルを使用したトレーニング](../overview.md#train-with-labels)」を参照してください。



Azure BLOB コンテナー内のドキュメントを使用して Form Recognizer モデルをトレーニングするには、次の cURL コマンドを実行して、 **[Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** API を呼び出します。 コマンドを実行する前に、次の変更を行います。

1. `<Endpoint>` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `<subscription key>` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `<SAS URL>` を Azure Blob ストレージ コンテナーの共有アクセス署名 (SAS) URL に置き換えます。 SAS URL を取得するには、Microsoft Azure Storage Explorer を開き、ご利用のコンテナーを右クリックし、 **[共有アクセス署名の取得]** を選択します。 アクセス許可の **[読み取り]** と **[表示]** がオンになっていることを確認し、 **[作成]** をクリックします。 次に、その値を **URL** セクションにコピーします。 それは次の書式になります`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。

    # <a name="v20"></a>[v2.0](#tab/v2-0)    
    ```bash
    curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
    ```
    # <a name="v21-preview"></a>[v2.1 プレビュー](#tab/v2-1)    
    ```bash
    curl -i -X POST "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
    ```
    
    ---



**Location** ヘッダーで `201 (Success)` 応答を受信します。 このヘッダーの値は、トレーニング中の新しいモデルの ID です。

## <a name="get-training-results"></a>トレーニング結果を取得する

トレーニング操作の開始後、新しい操作 **[Get Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** を使用して、トレーニングの状態を確認します。 この API 呼び出しにモデル ID を渡して、トレーニングの状態を確認します。

1. `<Endpoint>` を、Form Recognizer サブスクリプション キーで取得したエンドポイントで置き換えます。
1. `<subscription key>` は、実際のサブスクリプション キーで置き換えてください
1. `<model ID>` を、前の手順で受信したモデル ID で置き換えます



# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
# <a name="v21-preview"></a>[v2.1 プレビュー](#tab/v2-1)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```ce\": \""<SAS URL>"\"}"
```
    
---

次の形式の JSON 本文を含む `200 (Success)` 応答が送られてきます。 `"status"` フィールドに注目します。 トレーニングが完了すると、この値は `"ready"` になります。 モデルのトレーニングが完了していない場合は、コマンドを再実行して、サービスに対して再度クエリを実行する必要があります。 呼び出しの間隔は 1 秒以上あけることをお勧めします。

`"modelId"` フィールドには、トレーニング中のモデルの ID が含まれています。 これは、次の手順で必要になります。

```json
{
  "modelInfo":{
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{
    "trainingDocuments":[
      {
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      }
    ],
    "errors":[

    ]
  },
  "keys":{
    "0":[
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>キーと値のペアおよびテーブルについてフォームを分析する

次に、新しくトレーニングしたモデルを使用してドキュメントを分析し、そこからキーと値のペアおよびテーブルを抽出します。 次の cURL コマンドを実行して、 **[Analyze Form](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** API を呼び出します。 コマンドを実行する前に、次の変更を行います。

1. `<Endpoint>` を、Form Recognizer サブスクリプション キーから取得したエンドポイントで置き換えます。 これは、Form Recognizer リソースの **[概要]** タブにあります。
1. `<model ID>` を、前のセクションで受信したモデル ID で置き換えます。
1. `<SAS URL>` を、Azure Storage にある実際のファイルの SAS URL に置き換えます。 「トレーニング」セクションの手順に従いますが、取得するのは、BLOB コンテナー全体の SAS URL ではなく、分析対象となる特定のファイルの SAS URL です。
1. `<subscription key>` は、実際のサブスクリプション キーで置き換えてください。

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```
# <a name="v21-preview"></a>[v2.1 プレビュー](#tab/v2-1)    
```bash
```bash
curl -v "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```
    
---



**Operation-Location** ヘッダーを含む `202 (Success)` 応答を受信します。 このヘッダーの値は、分析操作の結果を追跡するために使用する結果 ID を含みます。 次の手順で使用できるように、この結果 ID を保存します。

## <a name="get-the-analyze-results"></a>分析結果を取得する

次の API を使用して、分析操作の結果に対してクエリを実行します。

1. `<Endpoint>` を、Form Recognizer サブスクリプション キーから取得したエンドポイントで置き換えます。 これは、Form Recognizer リソースの **[概要]** タブにあります。
1. `<result ID>` を、前のセクションで受信した ID で置き換えます。
1. `<subscription key>` は、実際のサブスクリプション キーで置き換えてください。


# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
# <a name="v21-preview"></a>[v2.1 プレビュー](#tab/v2-1)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.1-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
    
---

次の形式の JSON 本文を含む `200 (Success)` 応答が送られてきます。 出力は、簡素化するために一部省略されています。 下部の近くにある `"status"` フィールドにご注意ください。 分析操作が完了すると、ここに `"succeeded"` 値が表示されます。 分析操作が完了していない場合は、コマンドを再実行して、サービスに対して再度クエリを実行する必要があります。 呼び出しの間隔は 1 秒以上あけることをお勧めします。

主なキーと値のペアの関連付けとテーブルは、`"pageResults"` ノードに存在します。 *includeTextDetails* URL パラメーターを使用してプレーンテキスト抽出を指定した場合、`"readResults"` ノードには、ドキュメント内のすべてのテキストの内容と位置が表示されます。

このサンプル JSON 出力は、簡素化するために一部省略されています。

# <a name="v20"></a>[v2.0](#tab/v2-0)
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
              }
            ]
          }, 
        ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```    
# <a name="v21-preview"></a>[v2.1 プレビュー](#tab/v2-1)    
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
                ]
              }
            ]
          }, 
          ...
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ], 
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ], 
    "documentResults": [],
    "errors": []
  }
}
``` 

---


## <a name="improve-results"></a>結果を改善する

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、cURL で Form Recognizer REST API を使用してモデルをトレーニングし、サンプル シナリオで実行しました。 次に、Form Recognizer API の詳細を把握するためにリファレンス ドキュメントを参照します。

> [!div class="nextstepaction"]
> [REST API リファレンス ドキュメント](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
