---
title: クイック スタート:ドメイン固有のコンテンツ - REST、Python
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、ドメイン モデルを利用し、Python と Computer Vision API を使って画像内の著名人やランドマークを識別します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 771c2a6ea89f3bda4c671a053d505ba995f0d1db
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853094"
---
# <a name="quickstart-use-a-domain-model-using-the-rest-api-and-python-in-computer-vision"></a>クイック スタート:Computer Vision で REST API と Python を使ったドメイン モデルを利用する

このクイックスタートでは、ドメイン モデルを利用し、Computer Vision の REST API を使って、リモートで保存された画像内のランドマークや (場合によっては) 著名人を識別します。 [ドメイン固有のコンテンツの認識](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200)メソッドを使用すると、ドメイン固有のモデルを適用して、画像内のコンテンツを認識できます。

このクイック スタートは、[MyBinder](https://mybinder.org) 上で Jupyter Notebook を使い、ステップ バイ ステップで実行することができます。 Binder を起動するには、次のボタンを選択します。

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* [Python](https://www.python.org/downloads/)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision リソースを作成"  target="_blank">Computer Vision リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを Computer Vision サービスに接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* キーとエンドポイント URL 用に、それぞれ `COMPUTER_VISION_SUBSCRIPTION_KEY` と `COMPUTER_VISION_ENDPOINT` という名前の[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。

## <a name="create-and-run-the-landmarks-sample"></a>ランドマーク サンプルの作成と実行

ランドマーク サンプルを作成して実行するには、次の手順を実行します。

1. テキスト エディターに次のコードをコピーします。
1. 必要に応じて、ランドマークを検出する別のイメージの URL で `image_url` 値を置き換えます。
1. `.py` 拡張子のファイルとして、コードを保存します。 たとえば、「 `get-landmarks.py` 」のように入力します。
1. コマンド プロンプト ウィンドウを開きます。
1. プロンプトで、`python` コマンドを使用してサンプルを実行します。 たとえば、「 `python get-landmarks.py` 」のように入力します。

```python
import os
import sys
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
    subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
else:
    print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n**Restart your shell or IDE for changes to take effect.**")
    sys.exit()

if 'COMPUTER_VISION_ENDPOINT' in os.environ:
    endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

landmark_analyze_url = endpoint + "vision/v3.0/models/landmarks/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/f/f6/" + \
    "Bunker_Hill_Monument_2005.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'model': 'landmarks'}
data = {'url': image_url}
response = requests.post(
    landmark_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant landmark for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["landmarks"] is not []
print(analysis)
landmark_name = analysis["result"]["landmarks"][0]["name"].capitalize()

# Display the image and overlay it with the landmark name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(landmark_name, size="x-large", y=-0.1)
plt.show()
```

## <a name="examine-the-response-for-the-landmarks-sample"></a>ランドマーク サンプルの応答を調べる

成功応答が JSON で返されます。 サンプルの Web ページによって成功応答が解析され、次の例のようにコマンド プロンプト ウィンドウに表示されます。

```json
{
  "result": {
    "landmarks": [
      {
        "name": "Bunker Hill Monument",
        "confidence": 0.9768505096435547
      }
    ]
  },
  "requestId": "659a10cd-44bb-44db-9147-a295b853b2b8",
  "metadata": {
    "height": 1600,
    "width": 1200,
    "format": "Jpeg"
  }
}
```

## <a name="create-and-run-the-celebrities-sample"></a>著名人サンプルの作成と実行

ランドマーク サンプルを作成して実行するには、次の手順を実行します。

1. テキスト エディターに次のコードをコピーします。
1. 必要に応じて、コードに次の変更を加えます。
    1. `subscription_key` 値を、サブスクリプション キーに置き換えます。
    1. 必要に応じて、`vision_base_url` 値を、サブスクリプション キーを取得した Azure リージョンの Computer Vision リソースのエンドポイント URL に置き換えます。
    1. 必要に応じて、著名人を検出する別のイメージの URL で `image_url` 値を置き換えます。
1. `.py` 拡張子のファイルとして、コードを保存します。 たとえば、「 `get-celebrities.py` 」のように入力します。
1. コマンド プロンプト ウィンドウを開きます。
1. プロンプトで、`python` コマンドを使用してサンプルを実行します。 たとえば、「 `python get-celebrities.py` 」のように入力します。

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/"

celebrity_analyze_url = vision_base_url + "models/celebrities/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/d/d9/" + \
    "Bill_gates_portrait.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'model': 'celebrities'}
data = {'url': image_url}
response = requests.post(
    celebrity_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant celebrity for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["celebrities"] is not []
print(analysis)
celebrity_name = analysis["result"]["celebrities"][0]["name"].capitalize()

# Display the image and overlay it with the celebrity name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(celebrity_name, size="x-large", y=-0.1)
plt.show()
```

## <a name="examine-the-response-for-the-celebrities-sample"></a>著名人サンプルの応答を調べる

成功応答が JSON で返されます。 サンプルの Web ページによって成功応答が解析され、次の例のようにコマンド プロンプト ウィンドウに表示されます。


```json
{
  "result": {
    "celebrities": [
      {
        "faceRectangle": {
          "top": 123,
          "left": 156,
          "width": 187,
          "height": 187
        },
        "name": "Bill Gates",
        "confidence": 0.9993845224380493
      }
    ]
  },
  "requestId": "f14ec1d0-62d4-4296-9ceb-6b5776dc2020",
  "metadata": {
    "height": 521,
    "width": 550,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、両方のサンプルのファイルを削除します。

## <a name="next-steps"></a>次のステップ

次は、Computer Vision を使用して光学文字認識 (OCR) を実行する Python アプリケーションについて説明します。これは、スマートにクロップされたサムネイルを作成し、イメージ内の視覚的な特徴を検出、カテゴライズ、タグ付け、および記述します。

> [!div class="nextstepaction"]
> [Computer Vision API Python チュートリアル](../Tutorials/PythonTutorial.md)

* Computer Vision API を簡単に試す場合は、[Open API テスト コンソール](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)をお試しください。
