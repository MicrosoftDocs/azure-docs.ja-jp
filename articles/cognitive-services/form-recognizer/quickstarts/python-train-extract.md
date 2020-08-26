---
title: クイック スタート:Python で REST API を使用してモデルをトレーニングしてフォーム データを抽出する - Form Recognizer
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、Python で Form Recognizer REST API を使用してモデルをトレーニングし、フォームからデータを抽出します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 83adc5afa0cdf783b45c324ad15a0a95dc3ff464
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88522693"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-python"></a>クイック スタート:Python で REST API を使用して Form Recognizer モデルをトレーニングし、フォーム データを抽出する

このクイックスタートでは、Python で Azure Form Recognizer の REST API を使用してトレーニングし、フォームをスコア付けしてキーと値のペアおよびテーブルを抽出します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。
- インストールされている [Python](https://www.python.org/downloads/) (サンプルをローカルで実行する場合)。
- 同じ種類の少なくとも 5 つのフォームのセット。 このデータをモデルのトレーニングに使用します。 使用するフォームはファイルの種類が異なってもかまいませんが、ドキュメントの種類は同じである必要があります。 このクイックスタートでは、[サンプル データ セット](https://go.microsoft.com/fwlink/?linkid=2090451)を使用できます。 Standard パフォーマンス レベルの Azure Storage アカウントの BLOB ストレージ コンテナーのルートに、トレーニング ファイルをアップロードします。

> [!NOTE]
> このクイックスタートでは、URL によってアクセスされるリモート ドキュメントを使用します。 代わりにローカル ファイルを使用するには、[リファレンス ドキュメント](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)を参照してください。


## <a name="create-a-form-recognizer-resource"></a>Form Recognizer リソースを作成する

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Form Recognizer モデルをトレーニングする

まず、Azure Storage BLOB コンテナー内にトレーニング データのセットが必要です。 主な入力データと同じ種類/構造の入力済みフォーム (PDF ドキュメントやイメージ) が少なくとも 5 つ必要です。 または、2 つの入力済みフォームを持つ 1 つの空のフォームを使用できます。 空のフォームのファイル名には "empty" の語を含める必要があります。 トレーニング データをまとめるためのヒントとオプションについては、[カスタム モデル用のトレーニング データ セットの作成](../build-training-data-set.md)に関する記事を参照してください。

> [!NOTE]
> ラベル付きデータ機能を使用すると、トレーニング データの一部またはすべてにあらかじめ手動でラベルを付けることができます。 これは複雑なプロセスですが、トレーニングされたモデルの精度が向上します。 詳細については、概要に関するページの「[ラベルを使用したトレーニング](../overview.md#train-with-labels)」を参照してください。

Azure BLOB コンテナー内のドキュメントを使用して Form Recognizer モデルをトレーニングするには、次の Python コードを実行して、 **[Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** API を呼び出します。 コードを実行する前に、次の変更を行います。

1. `<SAS URL>` を Azure Blob ストレージ コンテナーの共有アクセス署名 (SAS) URL に置き換えます。 SAS URL を取得するには、Microsoft Azure Storage Explorer を開き、ご利用のコンテナーを右クリックし、 **[共有アクセス署名の取得]** を選択します。 アクセス許可の **[読み取り]** と **[表示]** がオンになっていることを確認し、 **[作成]** をクリックします。 次に、その値を **URL** セクションにコピーします。 それは次の書式になります`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。
1. `<subscription key>` を、前の手順からコピーしたサブスクリプション キーに置き換えます。
1. `<endpoint>` を、Form Recognizer リソースのエンドポイントの URL に置き換えます。
1. `<Blob folder name>` を、フォームが配置されている Blob Storage 内のフォルダーへのパスで置き換えます。 フォームがコンテナーのルートにある場合は、この文字列を空のままにします。

    ```python
    ########### Python Form Recognizer Labeled Async Train #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    post_url = endpoint + r"/formrecognizer/v2.0/custom/models"
    source = r"<SAS URL>"
    prefix = "<Blob folder name>"
    includeSubFolders = False
    useLabelFile = False
    
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
1. .py 拡張子のファイルにコードを保存します。 たとえば、*form-recognizer-train.py* などとします。
1. コマンド プロンプト ウィンドウを開きます。
1. プロンプトで、`python` コマンドを使用してサンプルを実行します。 たとえば、「 `python form-recognizer-train.py` 」のように入力します。

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

トレーニング プロセスが完了すると、次のような内容の JSON と共に `201 (Success)` 応答が返されます。

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

処理が完了すると、次の形式の JSON データと共に `200 (Success)` 応答が返されます。 簡潔にするために、応答は一部省略されています。 主なキーと値のペアの関連付けとテーブルは、`"pageResults"` ノードに存在します。 *includeTextDetails* URL パラメーターを使用してプレーンテキスト抽出を指定した場合、`"readResults"` ノードには、ドキュメント内のすべてのテキストの内容と位置が表示されます。

```bash
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

このクイックスタートでは、Python で Form Recognizer REST API を使用してモデルをトレーニングし、サンプル シナリオで実行しました。 次に、Form Recognizer API の詳細を把握するためにリファレンス ドキュメントを参照します。

> [!div class="nextstepaction"]
> [REST API リファレンス ドキュメント](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
