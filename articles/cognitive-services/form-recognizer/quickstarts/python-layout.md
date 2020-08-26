---
title: クイック スタート:Python を使用してテキストとレイアウト情報を抽出する - Form Recognizer
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、Python で Form Recognizer の Layout REST API を使用して、フォームからテキストとテーブル データを読み取ります。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 1777d4d09dfb12f490245f726715aed4eefd6227
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88517797"
---
# <a name="quickstart-extract-text-and-layout-information-using-the-form-recognizer-rest-api-with-python"></a>クイック スタート:Python で Form Recognizer の REST API を使用してテキストとレイアウト情報を抽出する

このクイックスタートでは、Python で Azure Form Recognizer の REST API を使用して、テキストのレイアウト情報とテーブル データをフォーム ドキュメントから抽出します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。
- インストールされている [Python](https://www.python.org/downloads/) (サンプルをローカルで実行する場合)。
- フォーム ドキュメント。 [サンプル データ セット](https://go.microsoft.com/fwlink/?linkid=2090451)から、このクイックスタート用の画像をダウンロードします。

> [!NOTE]
> このクイックスタートでは、ローカルに保存されたドキュメントを使用します。 URL でアクセスするリモート ファイルを使用する方法については、[リファレンス ドキュメント](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)を参照してください。


## <a name="create-a-form-recognizer-resource"></a>Form Recognizer リソースを作成する

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-the-form-layout"></a>フォーム レイアウトを分析する

レイアウトの分析を開始するには、下の Python スクリプトを使用して **[Analyze Layout](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** API を呼び出します。 スクリプトを実行する前に、次の変更を行います。

1. `<Endpoint>` を、Form Recognizer サブスクリプションで取得したエンドポイントで置き換えます。
1. `<path to your form>` を、ローカル フォーム ドキュメントのパスに置き換えます。
1. `<subscription key>` を、前の手順からコピーしたサブスクリプション キーに置き換えます。

    ```python
    ########### Python Form Recognizer Async Layout #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.0/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. .py 拡張子のファイルにコードを保存します。 たとえば、*form-recognizer-layout.py* とします。
1. コマンド プロンプト ウィンドウを開きます。
1. プロンプトで、`python` コマンドを使用してサンプルを実行します。 たとえば、「 `python form-recognizer-layout.py` 」のように入力します。

**Operation-Location** ヘッダーを含む `202 (Success)` 応答を受信します。これは、スクリプトによってコンソールに出力されます。 このヘッダーに含まれる操作 ID を使用して、非同期操作の状態のクエリを実行し、結果を取得できます。 次の例の値では、`operations/` の後ろの文字列が操作 ID です。

```console
https://cognitiveservice/formrecognizer/v2.0/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-layout-results"></a>レイアウトの結果を取得する

**Analyze Layout** API を呼び出した後に **[Get Analyze Layout Result](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult)** API を呼び出して、操作の状態と抽出されたデータを取得します。 Python スクリプトの末尾に次のコードを追加します。 このコードにより、操作 ID の値が新しい API 呼び出しで使用されます。 このスクリプトでは、結果が得られるまで一定の間隔で API が呼び出されます。 間隔は 1 秒以上あけることをお勧めします。

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Layout results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Layout Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Layout Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. スクリプトを保存します。
1. もう一度 `python` コマンドを使用してサンプルを実行します。 たとえば、「 `python form-recognizer-layout.py` 」のように入力します。

### <a name="examine-the-response"></a>結果の確認

このスクリプトでは、**Analyze Layout** 操作が完了するまで、コンソールに応答が出力されます。 次に、抽出されたデータが JSON 形式で出力されます。 `"readResults"` ノードには、あらゆるテキスト行が、ページ上の対応する境界ボックスの配置と共に表示されます。 `"pageResults"` フィールドには、テーブル内のあらゆるテキストが、それぞれ対応する行と列の座標と共に表示されます。

次の請求書の画像とそれに対応する JSON 出力をご覧ください。 出力は、簡素化するために一部省略されています。

> [!div class="mx-imgBorder"]
> ![テーブルを含む Contoso の請求書ドキュメント](../media/contoso-invoice.png)

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-11-12T19:55:36Z",
  "lastUpdatedDateTime":"2019-11-12T19:55:43Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "language":"en",
        "angle":0,
        "width":8.5,
        "height":11,
        "unit":"inch",
        "lines":[ 
          { 
            "language":"en",
            "boundingBox":[ 
              0.5384,
              1.1583,
              1.4466,
              1.1583,
              1.4466,
              1.3534,
              0.5384,
              1.3534
            ],
            "text":"Contoso",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.5384,
                  1.1583,
                  1.4466,
                  1.1583,
                  1.4466,
                  1.3534,
                  0.5384,
                  1.3534
                ],
                "text":"Contoso",
                "confidence":1
              }
            ]
          },
          { 
            "language":"en",
            "boundingBox":[ 
              0.7994,
              1.5143,
              1.3836,
              1.5143,
              1.3836,
              1.6154,
              0.7994,
              1.6154
            ],
            "text":"Address:",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.7994,
                  1.5143,
                  1.3836,
                  1.5143,
                  1.3836,
                  1.6154,
                  0.7994,
                  1.6154
                ],
                "text":"Address:",
                "confidence":1
              }
            ]
          },
          ...
          { 
            "language":"en",
            "boundingBox":[ 
              6.2285,
              3.4114,
              6.3919,
              3.4114,
              6.3919,
              3.5119,
              6.2285,
              3.5119
            ],
            "text":"PT",
            "words":[ 
              { 
                "boundingBox":[ 
                  6.2285,
                  3.4114,
                  6.3919,
                  3.4114,
                  6.3919,
                  3.5119,
                  6.2285,
                  3.5119
                ],
                "text":"PT",
                "confidence":1
              }
            ]
          }
        ]
      }
    ],
    "pageResults":[ 
      { 
        "page":1,
        "tables":[ 
          { 
            "rows":2,
            "columns":6,
            "cells":[ 
              { 
                "rowIndex":0,
                "columnIndex":0,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5075,
                  2.8088,
                  1.9061,
                  2.8088,
                  1.9061,
                  3.3219,
                  0.5075,
                  3.3219
                ],
                "elements":[ 
                  "#/readResults/0/lines/8/words/0",
                  "#/readResults/0/lines/8/words/1"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Python で Form Recognizer の REST API を使用して、請求書のテキスト レイアウトを抽出しました。 次に、Form Recognizer API の詳細を把握するためにリファレンス ドキュメントを参照します。

> [!div class="nextstepaction"]
> [REST API リファレンス ドキュメント](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)
