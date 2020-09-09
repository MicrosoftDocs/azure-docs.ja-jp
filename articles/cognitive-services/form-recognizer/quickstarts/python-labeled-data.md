---
title: クイック スタート:REST API と Python によるラベルを使用したトレーニング - Form Recognizer
titleSuffix: Azure Cognitive Services
description: REST API と Python で Form Recognizer のラベル付けデータ機能を使用して、カスタム モデルをトレーニングする方法について説明します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: e96940960b6ee131068b77bca4818499377ea3dd
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723498"
---
# <a name="train-a-form-recognizer-model-with-labels-using-rest-api-and-python"></a>REST API と Python でラベルを使用して Form Recognizer モデルをトレーニングする

このクイックスタートでは、Form Recognizer REST API と Python を使用して、手動でラベル付けされたデータを使ってカスタム モデルをトレーニングします。 この機能の詳細については、概要に関するページの「[ラベルを使用したトレーニング](../overview.md#train-with-labels)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。
- インストールされている [Python](https://www.python.org/downloads/) (サンプルをローカルで実行する場合)。
- 同じ種類の少なくとも 6 つのフォームのセット。 このデータを使用して、モデルのトレーニングとフォームのテストを行います。 このクイックスタートでは、[サンプル データ セット](https://go.microsoft.com/fwlink/?linkid=2090451)を使用できます。 Standard パフォーマンス レベルの Azure Storage アカウントの BLOB ストレージ コンテナーのルートに、トレーニング ファイルをアップロードします。

> [!NOTE]
> このクイックスタートでは、URL によってアクセスされるリモート ドキュメントを使用します。 代わりにローカル ファイルを使用するには、[v2.0 のリファレンス ドキュメント](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)と [v2.1 のリファレンス ドキュメント](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/TrainCustomModelAsync)を参照してください。

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer リソースを作成する

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-training-data"></a>トレーニング データを設定する

次に、必要な入力データを設定する必要があります。 ラベル付けデータ機能には、ラベルなしでのカスタム モデルのトレーニングに必要な入力要件を超える特殊な入力要件があります。

すべてのトレーニング ドキュメントが同じ形式であることを確認します。 複数の形式のフォームがある場合は、共通する形式に基づいてサブフォルダーに分類します。 トレーニング時には、API に対してサブフォルダーを指定する必要があります。

ラベル付けされたデータを使用してモデルをトレーニングするためには、入力として、サブフォルダーに次のファイルが必要となります。 ここでは、以下のファイルの作成方法について説明しています。

* **ソース フォーム** - データの抽出元となるフォーム。 サポートされる種類は、JPEG、PNG、PDF、TIFF です。
* **OCR レイアウト ファイル** - これらは、各ソース フォームに含まれるすべての読み取り可能テキストのサイズと位置を表す JSON ファイルです。 このデータは、Form Recognizer Layout API を使用して生成します。 
* **ラベル ファイル** - これらは、ユーザーが手動で入力したデータ ラベルを表す JSON ファイルです。

これらのファイルはすべて同じサブフォルダーに、次の形式で格納されている必要があります。

* input_file1.pdf 
* input_file1.pdf.ocr.json
* input_file1.pdf.labels.json 
* input_file2.pdf 
* input_file2.pdf.ocr.json
* input_file2.pdf.labels.json
* ...

> [!TIP]
> Form Recognizer の[サンプル ラベル付けツール](./label-tool.md)を使用してフォームのラベル付けを実行すると、これらのラベル ファイルや OCR レイアウト ファイルが自動的に作成されます。

### <a name="create-the-ocr-output-files"></a>OCR 出力ファイルを作成する

ラベルによるトレーニング用として入力ファイルがこのサービスによって認識されるためには、対応する OCR 結果ファイルが必要です。 特定のソース フォームの OCR 結果を取得するには、次の手順に従います。

1. 要求本文に入力ファイルを含めて、読み取りのレイアウト コンテナーに対して **[Analyze Layout](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** API を呼び出します。 応答の **Operation-Location** ヘッダーにある ID を保存してください。
1. 前の手順で得た操作 ID を使用して、 **[Get Analyze Layout Result](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult)** API を呼び出します。
1. 応答を取得し、その内容をファイルに書き込みます。 それぞれのソース フォームに対応する OCR ファイルでは、元のファイル名に `.ocr.json` が追加されています。 OCR の JSON は、次の形式で出力されている必要があります。 完全な例については、[サンプル OCR ファイル](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.ocr.json)を参照してください。 

    # <a name="v20"></a>[v2.0](#tab/v2-0)
    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```    
    # <a name="v21-preview"></a>[v2.1 プレビュー](#tab/v2-1)
    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```    


    ---



### <a name="create-the-label-files"></a>ラベル ファイルを作成する

ラベル ファイルには、ユーザーが手動で入力したキーと値の関連付けが含まれています。 ラベル付けされたデータのトレーニングにはラベル ファイルが必要ですが、すべてのソース ファイルに、対応するラベル ファイルが存在する必要はありません。 ラベルのないソース ファイルは通常のトレーニング ドキュメントとして扱われます。 信頼できるトレーニングには、ラベル付けされたファイルを 5 つ以上お勧めします。 [サンプル ラベル付けツール](./label-tool.md)などの UI ツールを使用して、これらのファイルを生成できます。

ラベル ファイルを作成するとき、必要に応じて領域 (ドキュメントにおける値の正確な位置) を指定できます。 そうすることで、トレーニングの精度がいっそう向上します。 領域は、左上、右上、右下、左下という 4 つの XY 座標に対応する 8 つの値のセットとして書式設定されます。 座標は、ページのサイズに合わせてスケーリングされた 0 から 1 の範囲の値を取ります。

それぞれのソース フォームに対応するラベル ファイルでは、元のファイル名に `.labels.json` が追加されています。 ラベル ファイルの形式は次のとおりです。 完全な例については、[サンプル ラベル ファイル](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.labels.json)を参照してください。

```json
{
    "document": "Invoice_1.pdf",
    "labels": [
        {
            "label": "Provider",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Contoso",
                    "boundingBoxes": [
                        [
                            0.06334117647058823,
                            0.1053,
                            0.17018823529411767,
                            0.1053,
                            0.17018823529411767,
                            0.12303636363636362,
                            0.06334117647058823,
                            0.12303636363636362
                        ]
                    ]
                }
            ]
        },
        {
            "label": "For",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Microsoft",
                    "boundingBoxes": [
                        [
                            0.6122941176470589,
                            0.1374,
                            0.6841764705882353,
                            0.1374,
                            0.6841764705882353,
                            0.14682727272727272,
                            0.6122941176470589,
                            0.14682727272727272
                        ]
                    ]
                },
                {
                    "page": 1,
                    "text": "1020",
                    "boundingBoxes": [
                        [
                            0.6121882352941176,
                            0.156,
                            0.6462941176470588,
                            0.156,
                            0.6462941176470588,
                            0.1653181818181818,
                            0.6121882352941176,
                            0.1653181818181818
                        ]
                    ]
                },
                ...
```

> [!IMPORTANT]
> 各テキスト要素には 1 つのラベルのみを適用できます。各ラベルは 1 ページにつき 1 回のみ適用できます。 1 つのラベルを複数のページにわたって適用することはできません。


## <a name="train-a-model-using-labeled-data"></a>ラベル付けされたデータを使用してモデルをトレーニングする

ラベル付けされたデータを使用してモデルをトレーニングするには、以下の Python コードを実行して、 **[Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** API を呼び出します。 コードを実行する前に、次の変更を行います。

1. `<Endpoint>` を、Form Recognizer リソースのエンドポイントの URL に置き換えます。
1. `<SAS URL>` を Azure Blob ストレージ コンテナーの共有アクセス署名 (SAS) URL に置き換えます。 SAS URL を取得するには、Microsoft Azure Storage Explorer を開き、ご利用のコンテナーを右クリックし、 **[共有アクセス署名の取得]** を選択します。 アクセス許可の **[読み取り]** と **[表示]** がオンになっていることを確認し、 **[作成]** をクリックします。 次に、その値を **URL** セクションにコピーします。 それは次の書式になります`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。
1. `<Blob folder name>` を、入力データが置かれている BLOB コンテナー内のフォルダー名に置き換えます。 データがルートにある場合は、これを空のままにし、HTTP 要求の本文から `"prefix"` フィールドを削除してください。

# <a name="v20"></a>[v2.0](#tab/v2-0)
```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.0/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```    
# <a name="v21-preview"></a>[v2.1 プレビュー](#tab/v2-1)    
```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.1-preview.1/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```   

---



## <a name="get-training-results"></a>トレーニング結果を取得する

トレーニング操作を開始したら、返された ID を使用して、操作の状態を取得します。 Python スクリプトの末尾に次のコードを追加します。 ここでは、トレーニング呼び出しから得た ID 値を新しい API 呼び出しの中で使用します。 トレーニング操作は非同期なので、このスクリプトでは、トレーニングが完了状態になるまで一定の間隔で API が呼び出されます。 間隔は 1 秒以上あけることをお勧めします。

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = headers)
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
            quit()
        model_status = resp_json["modelInfo"]["status"]
        if model_status == "ready":
            print("Training succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if model_status == "invalid":
            print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
            quit()
        # Training still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET model failed:\n%s" % str(e)
        print(msg)
        quit()
print("Train operation did not complete within the allocated time.")
```

トレーニング プロセスが完了すると、次のような内容の JSON と共に `201 (Success)` 応答が返されます。 簡潔にするために、応答は一部省略されています。

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

`"modelId"` の値は、次の手順で使用するためにコピーします。

[!INCLUDE [analyze forms](../includes/python-custom-analyze.md)]

処理が完了すると、次の形式の JSON データと共に `202 (Success)` 応答が返されます。 簡潔にするために、応答は一部省略されています。 主なキーと値の関係は、`"documentResults"` ノードに存在します。 `"selectionMarks"` ノード (v2.1 プレビューの場合) には、すべての選択マーク (チェック ボックス、ラジオ マーク) と、その状態が "選択済み" と "未選択" のどちらであるかが示されます。 Layout API の結果 (ドキュメントに含まれるすべてのテキストの内容と位置) は、`"readResults"` ノードに存在します。

# <a name="v20"></a>[v2.0](#tab/v2-0)
```json
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T02:16:28Z",
  "lastUpdatedDateTime": "2020-08-21T02:16:35Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "boundingBox": [
              0.5826,
              0.4411,
              2.3387,
              0.4411,
              2.3387,
              0.7969,
              0.5826,
              0.7969
            ],
            "text": "Contoso, Ltd.",
            "words": [
              {
                "boundingBox": [
                  0.5826,
                  0.4411,
                  1.744,
                  0.4411,
                  1.744,
                  0.7969,
                  0.5826,
                  0.7969
                ],
                "text": "Contoso,",
                "confidence": 1
              },
              {
                "boundingBox": [
                  1.8448,
                  0.4446,
                  2.3387,
                  0.4446,
                  2.3387,
                  0.7631,
                  1.8448,
                  0.7631
                ],
                "text": "Ltd.",
                "confidence": 1
              }
            ]
          },
          ...
            ]
          }
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "rowIndex": 0,
                "columnIndex": 0,
                "text": "Training Date",
                "boundingBox": [
                  0.5133,
                  4.2167,
                  1.7567,
                  4.2167,
                  1.7567,
                  4.4492,
                  0.5133,
                  4.4492
                ],
                "elements": [
                  "#/readResults/0/lines/14/words/0",
                  "#/readResults/0/lines/14/words/1"
                ]
              },
              ...
            ]
          },
        ]
      }
    ],
    "documentResults": [
      {
        "docType": "custom:form",
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Receipt No": {
            "type": "string",
            "valueString": "9876",
            "text": "9876",
            "page": 1,
            "boundingBox": [
              7.615,
              1.245,
              7.915,
              1.245,
              7.915,
              1.35,
              7.615,
              1.35
            ],
            "confidence": 1,
            "elements": [
              "#/readResults/0/lines/3/words/3"
            ]
          },
          ...
        }
      }
    ],
    "errors": []
  }
}
```
# <a name="v21-preview"></a>[v2.1 プレビュー](#tab/v2-1) 
```json   
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T02:29:42Z",
  "lastUpdatedDateTime": "2020-08-21T02:29:50Z",
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
            "boundingBox": [
              0.5826,
              0.4411,
              2.3387,
              0.4411,
              2.3387,
              0.7969,
              0.5826,
              0.7969
            ],
            "text": "Contoso, Ltd.",
            "words": [
              {
                "boundingBox": [
                  0.5826,
                  0.4411,
                  1.744,
                  0.4411,
                  1.744,
                  0.7969,
                  0.5826,
                  0.7969
                ],
                "text": "Contoso,",
                "confidence": 1
              },
              {
                "boundingBox": [
                  1.8448,
                  0.4446,
                  2.3387,
                  0.4446,
                  2.3387,
                  0.7631,
                  1.8448,
                  0.7631
                ],
                "text": "Ltd.",
                "confidence": 1
              }
            ]
          },
          ...
        ], 
        "selectionMarks": [
          {
            "boundingBox": [
              3.9737,
              3.7475,
              4.1693,
              3.7475,
              4.1693,
              3.9428,
              3.9737,
              3.9428
            ],
            ...
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "rowIndex": 0,
                "columnIndex": 0,
                "text": "Training Date",
                "boundingBox": [
                  0.5133,
                  4.2167,
                  1.7567,
                  4.2167,
                  1.7567,
                  4.4492,
                  0.5133,
                  4.4492
                ],
                "elements": [
                  "#/readResults/0/lines/12/words/0",
                  "#/readResults/0/lines/12/words/1"
                ]
              },
              ...
            ]
          }
        ] 
      }
    ], 
    "documentResults": [
      {
        "docType": "custom:e1073364-4f3d-4797-8cc4-4bdbcd0dab6b",
        "modelId": "e1073364-4f3d-4797-8cc4-4bdbcd0dab6b",
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "ID #": {
            "type": "string",
            "valueString": "5554443",
            "text": "5554443",
            "page": 1,
            "boundingBox": [
              2.315,
              2.43,
              2.74,
              2.43,
              2.74,
              2.515,
              2.315,
              2.515
            ],
            "confidence": 1,
            "elements": [
              "#/readResults/0/lines/8/words/1"
            ]
          },
          ...
        },
        "docTypeConfidence": 1
      }
    ],
    "errors": []
  }
}
```

---


## <a name="improve-results"></a>結果を改善する

`"documentResults"` ノードの下で、キーと値形式の結果ごとに `"confidence"` 値を調べます。 また、`"readResults"` ノード内の信頼度スコアにも注目してください。こちらはレイアウト操作に対応しています。 レイアウト結果の信頼度は、キーと値の抽出結果の信頼度には影響しません。したがって両方を確認する必要があります。
* レイアウト操作の信頼度スコアが低い場合は、入力ドキュメントの品質の改善を試みてください (「[入力の要件](../overview.md#input-requirements)」を参照)。
* キーと値の抽出操作の信頼度スコアが低い場合は、分析対象となるドキュメントの種類が、トレーニング セットで使用されているドキュメントと同じであることを確認してください。 トレーニング セットに含まれるドキュメントの体裁にばらつきがある場合は、別々のフォルダーに分けて、バリエーションごとに 1 つのモデルをトレーニングすることを検討してください。

### <a name="avoid-cluttered-labels"></a>ラベルが煩雑にならないようにする

同じテキスト行の中で複数の異なるラベルを適用すると、それらのラベルが 1 つのフィールドに結合される場合があります。 たとえば、住所では、市区町村、都道府県、郵便番号をそれぞれ異なるフィールドとしてラベル付けすることが考えられますが、予測時にそれらのフィールドは別々に認識されません。

Microsoft は、お客様にとってこれが不可欠なシナリオであると把握しており、将来の改善に向けて取り組んでいます。 現在、ユーザーの皆様には、散乱した複数のフィールドを 1 つのフィールドとしてラベル付けしておき、抽出結果の後処理で、それぞれの要素に分離することを推奨しています。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Form Recognizer REST API と Python を使用し、手動でラベル付けされたデータを使ってモデルをトレーニングする方法について説明しました。 引き続き API リファレンス ドキュメントを参照して、Form Recognizer API についての理解を深めましょう。

> [!div class="nextstepaction"]
> [REST API リファレンス ドキュメント](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
