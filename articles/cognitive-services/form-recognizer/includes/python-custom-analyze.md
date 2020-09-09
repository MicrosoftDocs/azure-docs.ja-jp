---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 88be632e17912012618ab559f22f97487ad26c9c
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723497"
---
## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>キーと値のペアおよびテーブルについてフォームを分析する

次に、新しくトレーニングしたモデルを使用してドキュメントを分析し、そこからキーと値のペアおよびテーブルを抽出します。 新しい Python スクリプトから、次のコードを実行して **[Analyze Form](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** API を呼び出します。 スクリプトを実行する前に、次の変更を行います。

1. `<file path>` をお使いのフォームのファイル パス (例: C:\temp\file.pdf) に置き換えます。 リモート ファイルの URL を指定することもできます。 このクイックスタートでは、[サンプル データ セット](https://go.microsoft.com/fwlink/?linkid=2090451)の **Test** フォルダーにあるファイルを使用できます。
1. `<model_id>` を、前のセクションで受信したモデル ID で置き換えます。
1. `<endpoint>` を、Form Recognizer サブスクリプション キーで取得したエンドポイントで置き換えます。 これは、Form Recognizer リソースの **[概要]** タブにあります。
1. `<file type>` を、ファイルの種類で置き換えます。 サポートされている種類: `application/pdf`、`image/jpeg`、`image/png`、`image/tiff`。
1. `<subscription key>` は、実際のサブスクリプション キーで置き換えてください。

    # <a name="v20"></a>[v2.0](#tab/v2-0)
    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```    
    # <a name="v21-preview"></a>[v2.1 プレビュー](#tab/v2-1)
    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.1/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```    


    ---



1. .py 拡張子のファイルにコードを保存します。 たとえば、*form-recognizer-analyze.py* とします。
1. コマンド プロンプト ウィンドウを開きます。
1. プロンプトで、`python` コマンドを使用してサンプルを実行します。 たとえば、「 `python form-recognizer-analyze.py` 」のように入力します。

**Analyze Form** API を呼び出すと、**Operation-Location** ヘッダーを含んだ `201 (Success)` 応答が返されます。 このヘッダーの値は、分析操作の結果を追跡するために使用する ID です。 前出のスクリプトからは、このヘッダーの値がコンソールに出力されます。

## <a name="get-the-analyze-results"></a>分析結果を取得する

Python スクリプトの末尾に次のコードを追加します。 前の呼び出しから得た ID 値を新しい API 呼び出しで使用し、分析結果を取得しています。 **Analyze Form** 操作は非同期なので、このスクリプトでは、結果が得られるまで一定の間隔で API が呼び出されます。 間隔は 1 秒以上あけることをお勧めします。

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET analyze results failed:\n%s" % json.dumps(resp_json))
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Analysis succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % json.dumps(resp_json))
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
print("Analyze operation did not complete within the allocated time.")
```
