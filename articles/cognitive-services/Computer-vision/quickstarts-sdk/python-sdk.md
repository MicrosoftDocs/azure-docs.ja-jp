---
title: クイック スタート:Python SDK
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、画像の分析、説明の取得、テキストの認識、サムネイルの生成などの一般的なタスクに Python SDK を使用する方法を学習します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: c03568ece97bdaad86f4564debf9f3b2fa14c6ed
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786645"
---
# <a name="azure-cognitive-services-computer-vision-sdk-for-python"></a>Python 用の Azure Cognitive Services Computer Vision SDK

Computer Vision サービスを使用すると、開発者は、イメージを処理して情報を返すための高度なアルゴリズムにアクセスできます。 Computer Vision のアルゴリズムでは、ユーザーが関心を寄せた視覚的特徴に応じて、さまざまな方法で画像のコンテンツを分析します。

* [イメージを分析する](#analyze-an-image)
* [主題の領域の一覧を取得する](#get-subject-domain-list)
* [領域別に画像を分析する](#analyze-an-image-by-domain)
* [画像のテキスト説明を取得する](#get-text-description-of-an-image)
* [画像から手書きのテキストを読み取る](#get-text-from-image)
* [サムネイルを生成する](#generate-thumbnail)

このサービスの詳細については、「[Computer Vision とは][computervision_docs]」を参照してください。

その他のドキュメントをお探しですか?

* [SDK のリファレンス ドキュメント](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)
* [Cognitive Services Computer Vision のドキュメント](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="prerequisites"></a>前提条件

* [Python 3.6 以降][python]
* 無料の [Computer Vision キー][computervision_resource]とそれに関連付けられたエンドポイント。ComputerVisionAPI クライアント オブジェクトのインスタンスを作成するときに、これらの値が必要となります。 次のいずれかの方法を使用してこれらの値を取得してください。

### <a name="if-you-dont-have-an-azure-subscription"></a>Azure のサブスクリプションをお持ちでない場合

Computer Vision サービスの **[試用][computervision_resource]** エクスペリエンスで 7 日間有効な無料のキーを作成してください。 キーが作成されたら、キーとエンドポイント名をコピーします。 この情報は、[クライアントを作成](#create-client)する際に必要となります。

キーの作成後は、次の情報を記録しておいてください。

* キー値: 32 文字の文字列 (`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` 形式)
* キーのエンドポイント: ベース エンドポイント URL: https\://westcentralus.api.cognitive.microsoft.com

### <a name="if-you-have-an-azure-subscription"></a>Azure サブスクリプションをお持ちの場合

以下の [Azure CLI][azure_cli] コマンドを使用して、ご利用のサブスクリプションにリソースを作成するのが最も簡単です。 これで、多くのコグニティブ サービスで使用できる Cognitive Service キーが作成されます。 "_既存_" のリソース グループ名 ("my-cogserv-group" など) と新しい Computer Vision リソース名 ("my-computer-vision-resource" など) を選択する必要があります。

```Bash
RES_REGION=westeurope
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

az cognitiveservices account create \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --location $RES_REGION \
    --kind CognitiveServices \
    --sku S0 \
    --yes
```

<!--
## Installation

Install the Azure Cognitive Services Computer Vision SDK with [pip][pip], optionally within a [virtual environment][venv].

### Configure a virtual environment (optional)

Although not required, you can keep your base system and Azure SDK environments isolated from one another if you use a [virtual environment][virtualenv]. Execute the following commands to configure and then enter a virtual environment with [venv][venv], such as `cogsrv-vision-env`:

```Bash
python3 -m venv cogsrv-vision-env
source cogsrv-vision-env/bin/activate
```
-->

### <a name="install-the-sdk"></a>SDK のインストール

pip を使用して、Python 用の Azure Cognitive Services Computer Vision SDK [パッケージ ][pypi_computervision] をインストールします。

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Authentication

Computer Vision リソースを作成した後は、クライアント オブジェクトをインスタンス化するために、その**エンドポイント**とその**アカウント キー**の 1 つが必要になります。

[ComputerVisionClient][ref_computervisionclient] クライアント オブジェクトのインスタンスを作成するときに、これらの値を使用します。

たとえば、Bash ターミナルを使用して環境変数を設定します。

```Bash
ACCOUNT_ENDPOINT=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>
```

### <a name="for-azure-subscription-users-get-credentials-for-key-and-endpoint"></a>Azure サブスクリプション ユーザーのために、キーとエンドポイントの資格情報を取得する

エンドポイントとキーを覚えていない場合は、次のメソッドで見つけることができます。 キーとエンドポイントを作成する必要がある場合、[Azure サブスクリプション所有者](#if-you-have-an-azure-subscription)または [Azure サブスクリプションのないユーザー](#if-you-dont-have-an-azure-subscription)にこのメソッドを使用できます。

次の [Azure CLI][cloud_shell] スニペットを使用して、2 つの環境変数に Computer Vision アカウントの**エンドポイント**とその**キー**の 1 つを設定します (これらの値は、[Azure portal][azure_portal] 内で見つけることもできます)。 このスニペットは Bash シェル用にフォーマットされています。

```Bash
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

export ACCOUNT_ENDPOINT=$(az cognitiveservices account show \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query endpoint \
    --output tsv)

export ACCOUNT_KEY=$(az cognitiveservices account keys list \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query key1 \
    --output tsv)
```


### <a name="create-client"></a>クライアントの作成

環境変数からエンドポイントとキーを取得してから、[ComputerVisionClient][ref_computervisionclient] クライアント オブジェクトを作成します。

```Python
from azure.cognitiveservices.vision.computervision import ComputerVisionClient
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

# Get endpoint and key from environment variables
import os
endpoint = os.environ['ACCOUNT_ENDPOINT']
key = os.environ['ACCOUNT_KEY']

# Set credentials
credentials = CognitiveServicesCredentials(key)

# Create client
client = ComputerVisionClient(endpoint, credentials)
```

## <a name="examples"></a>例

以降のタスクはいずれも、使用するには [ComputerVisionClient][ref_computervisionclient] クライアント オブジェクトが必要となります。

### <a name="analyze-an-image"></a>イメージを分析する

[`analyze_image`][ref_computervisionclient_analyze_image] を使用して、特定の特徴について画像を分析できます。visual_features プロパティを使用して、画像に対して実行する分析の種類を設定します。 一般的な値は `VisualFeatureTypes.tags` と `VisualFeatureTypes.description` です。

```Python
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

image_analysis = client.analyze_image(url,visual_features=[VisualFeatureTypes.tags])

for tag in image_analysis.tags:
    print(tag)
```

### <a name="get-subject-domain-list"></a>主題の領域の一覧を取得する

[`list_models`][ref_computervisionclient_list_models] を使用して、画像の分析に使用する主題の領域を確認します。 [領域別に画像を分析する](#analyze-an-image-by-domain)場合に、これらの領域名が使用されます。 領域の例は `landmarks` です。

```Python
models = client.list_models()

for x in models.models_property:
    print(x)
```

### <a name="analyze-an-image-by-domain"></a>領域別に画像を分析する

[`analyze_image_by_domain`][ref_computervisionclient_analyze_image_by_domain] を使用して、主題の領域別に画像を分析できます。 正しい領域名を使用するために、[サポートされている主題の領域の一覧](#get-subject-domain-list)を取得します。

```Python
# type of prediction
domain = "landmarks"

# Public domain image of Eiffel tower
url = "https://images.pexels.com/photos/338515/pexels-photo-338515.jpeg"

# English language response
language = "en"

analysis = client.analyze_image_by_domain(domain, url, language)

for landmark in analysis.result["landmarks"]:
    print(landmark["name"])
    print(landmark["confidence"])
```

### <a name="get-text-description-of-an-image"></a>画像のテキスト説明を取得する

[`describe_image`][ref_computervisionclient_describe_image] を使用して、画像の、言語に基づくテキスト説明を取得できます。 画像に関連するキーワードのテキスト分析を実行する場合は、`max_description` プロパティを使用して、いくつかの説明を要求します。 次の画像のテキスト説明の例には、`a train crossing a bridge over a body of water`、`a large bridge over a body of water`、`a train crossing a bridge over a large body of water` が含まれています。

```Python
domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

analysis = client.describe_image(url, max_descriptions, language)

for caption in analysis.captions:
    print(caption.text)
    print(caption.confidence)
```

### <a name="get-text-from-image"></a>画像からテキストを取得する

画像から手書きのテキストや印刷されたテキストを取得できます。 これには、SDK に対する 2 つの呼び出し ([`batch_read_file`](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) と [`get_read_operation_result`](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python)) が必要です。 `batch_read_file` の呼び出しは非同期です。 `get_read_operation_result` 呼び出しの結果では、テキスト データを抽出する前に、[`TextOperationStatusCodes`][ref_computervision_model_textoperationstatuscodes] を使用して、最初の呼び出しが完了しているかどうかを確認する必要があります。 結果には、テキストと、テキストの境界ボックスの座標が含まれます。

```Python
# import models
from azure.cognitiveservices.vision.computervision.models import TextOperationStatusCodes
import time

url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# Async SDK call
rawHttpResponse = client.batch_read_file(url, custom_headers,  raw)

# Get ID from returned headers
operationLocation = rawHttpResponse.headers["Operation-Location"]
idLocation = len(operationLocation) - numberOfCharsInOperationId
operationId = operationLocation[idLocation:]

# SDK call
while True:
    result = client.get_read_operation_result(operationId)
    if result.status not in ['NotStarted', 'Running']:
        break
    time.sleep(1)

# Get data
if result.status == TextOperationStatusCodes.succeeded:
    for textResult in result.recognition_results:
        for line in textResult.lines:
            print(line.text)
            print(line.bounding_box)
```

### <a name="generate-thumbnail"></a>サムネイルを生成する

[`generate_thumbnail`][ref_computervisionclient_generate_thumbnail] を使用して、画像のサムネイル (JPG) を生成できます。 サムネイルは、縦横比が元の画像と同じである必要はありません。

この例を使用するには、**Pillow** をインストールします。

```bash
pip install Pillow
```

Pillow のインストール後、次のコード例のパッケージを使用して、サムネイル画像を生成します。

```Python
# Pillow package
from PIL import Image

# IO package to create local image
import io

width = 50
height = 50
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"

thumbnail = client.generate_thumbnail(width, height, url)

for x in thumbnail:
    image = Image.open(io.BytesIO(x))

image.save('thumbnail.jpg')
```

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="general"></a>全般

Python SDK を使用して [ComputerVisionClient][ref_computervisionclient] クライアント オブジェクトを操作する場合、ComputerVisionErrorException クラスを使用してエラーを返します。 このサービスによって返されるエラーは、REST API 要求に対して返される同じ HTTP 状態コードに対応しています。

たとえば、無効なキーを使用して画像を分析しようとすると、`401` エラーが返されます。 次のスニペットでは、例外をキャッチし、エラーに関する追加情報を表示することで、[エラー][ref_httpfailure]が適切に処理されます。

```Python

domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

try:
    analysis = client.describe_image(url, max_descriptions, language)

    for caption in analysis.captions:
        print(caption.text)
        print(caption.confidence)
except HTTPFailure as e:
    if e.status_code == 401:
        print("Error unauthorized. Make sure your key and endpoint are correct.")
    else:
        raise
```

### <a name="handle-transient-errors-with-retries"></a>再試行による一時的なエラーを処理する

[ComputerVisionClient][ref_computervisionclient] クライアントの操作中に、このサービスによって適用されたレート制限によって一時的な障害が発生したり、ネットワークの停止など、他の一時的な問題が発生したりする可能性があります。 これらの種類の障害の処理については、クラウド設計パターン ガイドの「[再試行パターン][azure_pattern_retry]」および「[サーキット ブレーカー パターン][azure_pattern_circuit_breaker]」を参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [コンテンツ タグの画像への適用](../concept-tagging-images.md)

<!-- LINKS -->
[pip]: https://pypi.org/project/pip/
[python]: https://www.python.org/downloads/

[azure_cli]: https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create
[azure_pattern_circuit_breaker]: https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker
[azure_pattern_retry]: https://docs.microsoft.com/azure/architecture/patterns/retry
[azure_portal]: https://portal.azure.com
[azure_sub]: https://azure.microsoft.com/free/

[cloud_shell]: https://docs.microsoft.com/azure/cloud-shell/overview

[venv]: https://docs.python.org/3/library/venv.html
[virtualenv]: https://virtualenv.pypa.io

[source_code]: https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-vision-computervision

[pypi_computervision]:https://pypi.org/project/azure-cognitiveservices-vision-computervision/
[pypi_pillow]:https://pypi.org/project/Pillow/

[ref_computervision_sdk]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision?view=azure-python
[ref_computervision_computervisionerrorexception]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.computervisionerrorexception?view=azure-python
[ref_httpfailure]: https://docs.microsoft.com/python/api/msrest/msrest.exceptions.httpoperationerror?view=azure-python


[computervision_resource]: https://azure.microsoft.com/try/cognitive-services/?

[computervision_docs]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/home

[ref_computervisionclient]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_analyze_image]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_list_models]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_analyze_image_by_domain]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_describe_image]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_get_text_operation_result]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_generate_thumbnail]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python


[ref_computervision_model_visualfeatures]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python

[ref_computervision_model_textoperationstatuscodes]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.textoperationstatuscodes?view=azure-python

[computervision_request_units]:https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/
