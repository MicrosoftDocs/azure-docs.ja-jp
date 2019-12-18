---
title: クイック スタート:印刷されたテキストの抽出 - REST、Python
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Python と Computer Vision API を使って、画像内の印刷されたテキストを抽出します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 16f3dda644a7168dcceb701ba80346e2ba42b38a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973732"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-computer-vision-rest-api-and-python"></a>クイック スタート:Computer Vision の REST API と Python を使用して印刷されたテキスト (OCR) を抽出する

> [!NOTE]
> 英語のテキストを抽出する場合は、新しい[読み取り操作](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text)の使用を検討してください。 [Python のクイックスタート](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/python-hand-text)を使用できます。 

このクイックスタートでは、Computer Vision の REST API を使って、光学式文字認識 (OCR) で印刷されたテキストを抽出しています。 [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) メソッドを使って画像内の印刷されたテキストを検出し、認識した文字をマシンで扱うことができる文字ストリームに抽出します。

このクイック スタートは、[MyBinder](https://mybinder.org) 上で Jupyter Notebook を使い、ステップ バイ ステップで実行することができます。 Binder を起動するには、次のボタンを選択します。

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/try/cognitive-services/) を作成してください。

## <a name="prerequisites"></a>前提条件

- サンプルをローカルで実行するには、[Python](https://www.python.org/downloads/) がインストールされている必要があります。
- Computer Vision のサブスクリプション キーが必要です。 無料試用版のキーは「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)」から取得できます。 または、[Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページの手順に従って、Computer Vision をサブスクライブし、キーを取得します。 次に、キーとサービス エンドポイント文字列用に、それぞれ `COMPUTER_VISION_SUBSCRIPTION_KEY` と `COMPUTER_VISION_ENDPOINT` という名前の[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。

## <a name="create-and-run-the-sample"></a>サンプルの作成と実行

このサンプルを作成して実行するには、次の手順を実行します。

1. テキスト エディターに次のコードをコピーします。
1. 必要に応じて、印刷されたテキストを抽出したい別の画像の URL で `image_url` 値を置き換えます。
1. `.py` 拡張子のファイルとして、コードを保存します。 たとえば、「 `get-printed-text.py` 」のように入力します。
1. コマンド プロンプト ウィンドウを開きます。
1. プロンプトで、`python` コマンドを使用してサンプルを実行します。 たとえば、「 `python get-printed-text.py` 」のように入力します。

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Rectangle
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

ocr_url = endpoint + "vision/v2.1/ocr"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/" + \
    "Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'language': 'unk', 'detectOrientation': 'true'}
data = {'url': image_url}
response = requests.post(ocr_url, headers=headers, params=params, json=data)
response.raise_for_status()

analysis = response.json()

# Extract the word bounding boxes and text.
line_infos = [region["lines"] for region in analysis["regions"]]
word_infos = []
for line in line_infos:
    for word_metadata in line:
        for word_info in word_metadata["words"]:
            word_infos.append(word_info)
word_infos

# Display the image and overlay it with the extracted text.
plt.figure(figsize=(5, 5))
image = Image.open(BytesIO(requests.get(image_url).content))
ax = plt.imshow(image, alpha=0.5)
for word in word_infos:
    bbox = [int(num) for num in word["boundingBox"].split(",")]
    text = word["text"]
    origin = (bbox[0], bbox[1])
    patch = Rectangle(origin, bbox[2], bbox[3],
                      fill=False, linewidth=2, color='y')
    ax.axes.add_patch(patch)
    plt.text(origin[0], origin[1], text, fontsize=20, weight="bold", va="top")
plt.axis("off")
```

## <a name="upload-image-from-local-storage"></a>ローカル ストレージからの画像のアップロード

ローカルの画像を分析する場合は、Content-Type ヘッダーを application/octet-stream に設定し、要求本文を JSON データではなくバイト配列に設定します。

```python
image_path = "<path-to-local-image-file>"
# Read the image into a byte array
image_data = open(image_path, "rb").read()
# Set Content-Type to octet-stream
headers = {'Ocp-Apim-Subscription-Key': subscription_key, 'Content-Type': 'application/octet-stream'}
# put the byte array into your post request
response = requests.post(ocr_url, headers=headers, params=params, data = image_data)
```


## <a name="examine-the-response"></a>結果の確認

成功応答が JSON で返されます。 サンプルの Web ページによって成功応答が解析され、次の例のようにコマンド プロンプト ウィンドウに表示されます。

```json
{
  "language": "en",
  "orientation": "Up",
  "textAngle": 0,
  "regions": [
    {
      "boundingBox": "21,16,304,451",
      "lines": [
        {
          "boundingBox": "28,16,288,41",
          "words": [
            {
              "boundingBox": "28,16,288,41",
              "text": "NOTHING"
            }
          ]
        },
        {
          "boundingBox": "27,66,283,52",
          "words": [
            {
              "boundingBox": "27,66,283,52",
              "text": "EXISTS"
            }
          ]
        },
        {
          "boundingBox": "27,128,292,49",
          "words": [
            {
              "boundingBox": "27,128,292,49",
              "text": "EXCEPT"
            }
          ]
        },
        {
          "boundingBox": "24,188,292,54",
          "words": [
            {
              "boundingBox": "24,188,292,54",
              "text": "ATOMS"
            }
          ]
        },
        {
          "boundingBox": "22,253,297,32",
          "words": [
            {
              "boundingBox": "22,253,105,32",
              "text": "AND"
            },
            {
              "boundingBox": "144,253,175,32",
              "text": "EMPTY"
            }
          ]
        },
        {
          "boundingBox": "21,298,304,60",
          "words": [
            {
              "boundingBox": "21,298,304,60",
              "text": "SPACE."
            }
          ]
        },
        {
          "boundingBox": "26,387,294,37",
          "words": [
            {
              "boundingBox": "26,387,210,37",
              "text": "Everything"
            },
            {
              "boundingBox": "249,389,71,27",
              "text": "else"
            }
          ]
        },
        {
          "boundingBox": "127,431,198,36",
          "words": [
            {
              "boundingBox": "127,431,31,29",
              "text": "is"
            },
            {
              "boundingBox": "172,431,153,36",
              "text": "opinion."
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>次の手順

Computer Vision を使用して、光学文字認識 (OCR) を実行し、スマートにクロップされたサムネイルを作成するほか、イメージ内の視覚的な特徴 (顔など) を検出、カテゴライズ、タグ付け、および記述する Python アプリケーションについて説明します。 Computer Vision API を簡単に試す場合は、[Open API テスト コンソール](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)をお試しください。

> [!div class="nextstepaction"]
> [Computer Vision API Python チュートリアル](../Tutorials/PythonTutorial.md)
