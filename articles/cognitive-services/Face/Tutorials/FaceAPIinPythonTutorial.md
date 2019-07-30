---
title: 'クイック スタート: Python SDK を使用して画像内の顔を検出してフレームに収める'
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Face API を使用してリモート画像内の顔を検出してフレームに収める、Python スクリプトを作成します。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 07/15/2019
ms.author: sbowles
ms.openlocfilehash: 2f2245b4f6e4b38e0b071678ac0f3bddeb72f7ec
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277521"
---
# <a name="quickstart-create-a-python-script-to-detect-and-frame-faces-in-an-image"></a>クイック スタート:画像内の顔を検出してフレームに収める Python スクリプトの作成

このクイック スタートでは、リモート画像内の人の顔を検出する Python スクリプトを作成します。このスクリプトでは、Python SDK を介して Azure Face API を使用します。 このアプリケーションでは、選択した画像が表示され、検出されたそれぞれの顔の周囲にフレームが描画されます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

## <a name="prerequisites"></a>前提条件

- Face API サブスクリプション キー。 無料試用版のサブスクリプション キーは「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=face-api)」から取得できます。 または、[Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページの手順に従って、Face API サービスをサブスクライブし、キーを取得します。
- [Python 2.7 以降または 3.5 以降](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) ツール

## <a name="get-the-face-sdk"></a>Face SDK を取得する

Face Python SDK をインストールするには、コマンド プロンプトを開いて次のコマンドを実行します。

```shell
pip install cognitive_face
```

## <a name="detect-faces-in-an-image"></a>画像内の顔を検出する

_FaceQuickstart.py_ という名前の新しい Python スクリプトを作成し、次のコードを追加します。 このコードでは、顔検出のコア機能を処理します。 `<Subscription Key>` は、実際のキーの値に置き換える必要があります。 また、`BASE_URL` の値も、実際のキーに合った正しいリージョン識別子を使用するように変更する必要があります (全リージョンのエンドポイント一覧については、[Face API のドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)を参照)。 無料試用版のサブスクリプション キーは、**westus** リージョンで生成されます。 必要に応じて、`img_url` を、使用する任意の画像の URL に設定します。

このスクリプトでは、**cognitive_face.face.detect** メソッドを呼び出して顔を検出します。これは、[Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API をラップし、顔のリストを返します。

```python
import cognitive_face as CF

# Replace with a valid subscription key (keeping the quotes in place).
KEY = '<Subscription Key>'
CF.Key.set(KEY)

# Replace with your regional Base URL
BASE_URL = 'https://westus.api.cognitive.microsoft.com/face/v1.0/'
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)
```

### <a name="try-the-app"></a>アプリを試す

`python FaceQuickstart.py` コマンドを使用してアプリを実行します。 次のように、コンソール ウィンドウにはテキスト応答が表示されます。

```console
[{'faceId': '26d8face-9714-4f3e-bfa1-f19a7a7aa240', 'faceRectangle': {'top': 124, 'left': 459, 'width': 227, 'height': 227}}]
```

出力には検出された顔の一覧が示されます。 リスト内の各項目は **dict** インスタンスであり、ここでは `faceId` は検出された顔の一意の ID で、`faceRectangle` は検出された顔の位置を示します。 

> [!NOTE]
> Face ID は 24 時間後に有効期限が切れます。顔のデータを長期間保持する場合は、明示的にそれを保存する必要があります。

## <a name="draw-face-rectangles"></a>顔の四角形を描画する

前のコマンドから受け取った座標を使うと、画像に四角形を描画して、それぞれの顔を視覚的に表すことができます。 Pillow の Image モジュールを使用するには、Pillow のインストール (`pip install pillow`) が必要になります。 *FaceQuickstart.py* の先頭に次のコードを追加します。

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

次に、スクリプトの末尾に次のコードを追加します。 このコードにより、四角形の座標を解析するためのシンプルな関数が作成され、Pillow を使用して元の画像の上に四角形が描画されます。 その後、その画像が既定の画像ビューアーに表示されます。

```python
# Convert width height to a point in a rectangle


def getRectangle(faceDictionary):
    rect = faceDictionary['faceRectangle']
    left = rect['left']
    top = rect['top']
    bottom = left + rect['height']
    right = top + rect['width']
    return ((left, top), (bottom, right))


# Download the image from the url
response = requests.get(img_url)
img = Image.open(BytesIO(response.content))

# For each face returned use the face rectangle and draw a red box.
draw = ImageDraw.Draw(img)
for face in faces:
    draw.rectangle(getRectangle(face), outline='red')

# Display the image in the users default image browser.
img.show()
```

## <a name="run-the-app"></a>アプリの実行

最初に既定の画像ビューアーを選択するように求められることがあります。 その後、次のような画像が表示されます。 また、コンソール ウィンドウには四角形のデータが出力されていることがわかります。

![顔の周囲に赤い四角形が描画されている若い女性](../images/face-rectangle-result.png)

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Face API Python SDK を使用するための基本的なプロセスを学習し、画像内の顔を検出してフレームに収めるスクリプトを作成しました。 次に、さらに複雑な例で Python SDK の使用について調査してください。 GitHub 上の Cognitive Face Python サンプルに移動し、自分のプロジェクト フォルダーにそれを複製した後、_README.md_ ファイル内の手順に従います。

> [!div class="nextstepaction"]
> [Cognitive Face Python サンプル](https://github.com/Microsoft/Cognitive-Face-Python)
