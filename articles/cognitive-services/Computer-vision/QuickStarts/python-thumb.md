---
title: クイック スタート:サムネイルの生成 - REST、Python
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Computer Vision API と Python を使って、画像からサムネイルを生成します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5203467eadadf1180eabf7b5a51b113ae273ee7c
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603550"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>クイック スタート:Computer Vision の REST API と Python を使用したサムネイルを生成する

このクイック スタートでは、Computer Vision の REST API を使って、画像からサムネイルを生成します。 [サムネイル取得](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)メソッドでは、必要な高さと幅を指定できます。また、Computer Vision では、スマート トリミングを使ってインテリジェントに関心領域を識別し、その領域に基づいてトリミングの座標を生成します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/try/cognitive-services/) を作成してください。

## <a name="prerequisites"></a>前提条件

- Computer Vision のサブスクリプション キーが必要です。 無料試用版のキーは「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)」から取得できます。 または、[Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページの手順に従って、Computer Vision をサブスクライブし、キーを取得します。
- コード エディター ([Visual Studio Code](https://code.visualstudio.com/download) など)

## <a name="create-and-run-the-sample"></a>サンプルの作成と実行

サンプルを作成して実行するために、コード エディターに次のコードをコピーします。 

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

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the "westcentralus" region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

thumbnail_url = vision_base_url + "generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers,
                         params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

続けて次の作業を行います。
1. `subscription_key` 値を、サブスクリプション キーに置き換えます。
1. 必要に応じて、サブスクリプション キーを取得した Azure リージョンの Computer Vision リソースのエンドポイント URL で `vision_base_url` 値を置き換えます。
1. 必要に応じて、サムネイルを生成する別のイメージの URL で `image_url` 値を置き換えます。
1. `.py` 拡張子のファイルとして、コードを保存します。 たとえば、「 `get-thumbnail.py` 」のように入力します。
1. コマンド プロンプト ウィンドウを開きます。
1. プロンプトで、`python` コマンドを使用してサンプルを実行します。 たとえば、「 `python get-thumbnail.py` 」のように入力します。

## <a name="examine-the-response"></a>結果の確認

成功応答は、サムネイル画像データを表すバイナリ データとして返されます。 この画像が表示されることが必要です。 要求が失敗した場合、応答がコマンド プロンプト ウィンドウに表示され、エラー コードが記録されている必要があります。

## <a name="run-in-jupyter-optional"></a>Jupyter での実行 (任意)

このクイック スタートは、必要に応じて [MyBinder](https://mybinder.org) 上で Jupyter Notebook を使い、ステップ バイ ステップで実行することができます。 Binder を起動するには、次のボタンを選択します。

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>次の手順

この後は、サムネイル生成機能について詳しく学びましょう。

> [!div class="nextstepaction"]
> [サムネイルの生成](../concept-generating-thumbnails.md)
