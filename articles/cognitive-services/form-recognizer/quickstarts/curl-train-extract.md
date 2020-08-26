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
ms.openlocfilehash: d0bf5da3633ce8f34e34cbdf0cee12a94c128d72
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88517967"
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

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

**Location** ヘッダーで `201 (Success)` 応答を受信します。 このヘッダーの値は、トレーニング中の新しいモデルの ID です。

## <a name="get-training-results"></a>トレーニング結果を取得する

トレーニング操作の開始後、新しい操作 **[Get Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** を使用して、トレーニングの状態を確認します。 この API 呼び出しにモデル ID を渡して、トレーニングの状態を確認します。

1. `<Endpoint>` を、Form Recognizer サブスクリプション キーで取得したエンドポイントで置き換えます。
1. `<subscription key>` は、実際のサブスクリプション キーで置き換えてください
1. `<model ID>` を、前の手順で受信したモデル ID で置き換えます

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

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

```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```

**Operation-Location** ヘッダーを含む `202 (Success)` 応答を受信します。 このヘッダーの値は、分析操作の結果を追跡するために使用する結果 ID を含みます。 次の手順で使用できるように、この結果 ID を保存します。

## <a name="get-the-analyze-results"></a>分析結果を取得する

次の API を使用して、分析操作の結果に対してクエリを実行します。

1. `<Endpoint>` を、Form Recognizer サブスクリプション キーから取得したエンドポイントで置き換えます。 これは、Form Recognizer リソースの **[概要]** タブにあります。
1. `<result ID>` を、前のセクションで受信した ID で置き換えます。
1. `<subscription key>` は、実際のサブスクリプション キーで置き換えてください。

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

次の形式の JSON 本文を含む `200 (Success)` 応答が送られてきます。 出力は、簡素化するために一部省略されています。 下部の近くにある `"status"` フィールドにご注意ください。 分析操作が完了すると、ここに `"succeeded"` 値が表示されます。 分析操作が完了していない場合は、コマンドを再実行して、サービスに対して再度クエリを実行する必要があります。 呼び出しの間隔は 1 秒以上あけることをお勧めします。

主なキーと値のペアの関連付けとテーブルは、`"pageResults"` ノードに存在します。 *includeTextDetails* URL パラメーターを使用してプレーンテキスト抽出を指定した場合、`"readResults"` ノードには、ドキュメント内のすべてのテキストの内容と位置が表示されます。

```json
{
  "analyzeResult":{
    "readResults":[
      {
        "page":1,
        "width":8.5,
        "height":11.0,
        "angle":0,
        "unit":"inch",
        "lines":[
          {
            "text":"Contoso",
            "boundingBox":[
              0.5278,
              1.0597,
              1.4569,
              1.0597,
              1.4569,
              1.4347,
              0.5278,
              1.4347
            ],
            "words":[
              {
                "text":"Contoso",
                "boundingBox":[
                  0.5278,
                  1.0597,
                  1.4569,
                  1.0597,
                  1.4569,
                  1.4347,
                  0.5278,
                  1.4347
                ]
              }
            ]
          },
          ...
          {
            "text":"PT",
            "boundingBox":[
              6.2181,
              3.3528,
              6.3944,
              3.3528,
              6.3944,
              3.5417,
              6.2181,
              3.5417
            ],
            "words":[
              {
                "text":"PT",
                "boundingBox":[
                  6.2181,
                  3.3528,
                  6.3944,
                  3.3528,
                  6.3944,
                  3.5417,
                  6.2181,
                  3.5417
                ]
              }
            ]
          }
        ]
      }
    ],
    "version":"2.0.0",
    "errors":[

    ],
    "documentResults":[

    ],
    "pageResults":[
      {
        "page":1,
        "clusterId":1,
        "keyValuePairs":[
          {
            "key":{
              "text":"Address:",
              "boundingBox":[
                0.7972,
                1.5125,
                1.3958,
                1.5125,
                1.3958,
                1.6431,
                0.7972,
                1.6431
              ],
              "elements":[
                "#/readResults/0/lines/1/words/0"
              ]
            },
            "value":{
              "text":"1 Redmond way Suite 6000 Redmond, WA 99243",
              "boundingBox":[
                0.7972,
                1.6764,
                2.15,
                1.6764,
                2.15,
                2.2181,
                0.7972,
                2.2181
              ],
              "elements":[
                "#/readResults/0/lines/4/words/0",
                "#/readResults/0/lines/4/words/1",
                "#/readResults/0/lines/4/words/2",
                "#/readResults/0/lines/4/words/3",
                "#/readResults/0/lines/6/words/0",
                "#/readResults/0/lines/6/words/1",
                "#/readResults/0/lines/6/words/2",
                "#/readResults/0/lines/8/words/0"
              ]
            },
            "confidence":0.86
          },
          {
            "key":{
              "text":"Invoice For:",
              "boundingBox":[
                4.3903,
                1.5125,
                5.1139,
                1.5125,
                5.1139,
                1.6431,
                4.3903,
                1.6431
              ],
              "elements":[
                "#/readResults/0/lines/2/words/0",
                "#/readResults/0/lines/2/words/1"
              ]
            },
            "value":{
              "text":"Microsoft 1020 Enterprise Way Sunnayvale, CA 87659",
              "boundingBox":[
                5.1917,
                1.4458,
                6.6583,
                1.4458,
                6.6583,
                2.0347,
                5.1917,
                2.0347
              ],
              "elements":[
                "#/readResults/0/lines/3/words/0",
                "#/readResults/0/lines/5/words/0",
                "#/readResults/0/lines/5/words/1",
                "#/readResults/0/lines/5/words/2",
                "#/readResults/0/lines/7/words/0",
                "#/readResults/0/lines/7/words/1",
                "#/readResults/0/lines/7/words/2"
              ]
            },
            "confidence":0.86
          },
          ...
        ],
        "tables":[
          {
            "caption":null,
            "rows":2,
            "columns":5,
            "cells":[
              {
                "rowIndex":0,
                "colIndex":0,
                "header":true,
                "text":"Invoice Number",
                "boundingBox":[
                  0.5347,
                  2.8722,
                  1.575,
                  2.8722,
                  1.575,
                  3.0028,
                  0.5347,
                  3.0028
                ],
                "elements":[
                  "#/readResults/0/lines/9/words/0",
                  "#/readResults/0/lines/9/words/1"
                ]
              },
              {
                "rowIndex":0,
                "colIndex":1,
                "header":true,
                "text":"Invoice Date",
                "boundingBox":[
                  1.9403,
                  2.8722,
                  2.7569,
                  2.8722,
                  2.7569,
                  3.0028,
                  1.9403,
                  3.0028
                ],
                "elements":[
                  "#/readResults/0/lines/10/words/0",
                  "#/readResults/0/lines/10/words/1"
                ]
              },
              {
                "rowIndex":0,
                "colIndex":2,
                "header":true,
                "text":"Invoice Due Date",
                "boundingBox":[
                  3.3403,
                  2.8722,
                  4.4583,
                  2.8722,
                  4.4583,
                  3.0028,
                  3.3403,
                  3.0028
                ],
                "elements":[
                  "#/readResults/0/lines/11/words/0",
                  "#/readResults/0/lines/11/words/1",
                  "#/readResults/0/lines/11/words/2"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  },
  "lastUpdatedDateTime":"2019-10-07T19:32:18+00:00",
  "status":"succeeded",
  "createdDateTime":"2019-10-07T19:32:15+00:00"
}
```

## <a name="improve-results"></a>結果を改善する

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、cURL で Form Recognizer REST API を使用してモデルをトレーニングし、サンプル シナリオで実行しました。 次に、Form Recognizer API の詳細を把握するためにリファレンス ドキュメントを参照します。

> [!div class="nextstepaction"]
> [REST API リファレンス ドキュメント](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
