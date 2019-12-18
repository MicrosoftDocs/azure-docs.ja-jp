---
title: クイック スタート:Custom Vision SDK for Python を使用して画像分類プロジェクトを作成する
titleSuffix: Azure Cognitive Services
description: Python SDK を使用して、プロジェクトの作成、タグの追加、画像のアップロード、プロジェクトのトレーニング、予測を行います。
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: 2ea49eaf6fd48207996a92a997f15e5db3d3b199
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74961082"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-python-sdk"></a>クイック スタート:Custom Vision Python SDK を使用して画像分類プロジェクトを作成する

この記事では、Custom Vision SDK と Python を使用して画像分類モデルを構築する基本的な方法について説明します。 作成後は、タグの追加、画像のアップロード、プロジェクトのトレーニング、プロジェクトの公開された予測エンドポイント URL の取得、エンドポイントを使用したプログラミングによる画像のテストを行うことができます。 この例は、独自の Python アプリケーションを構築するためのテンプレートとしてご利用ください。 分類モデルの構築と使用のプロセスをコード "_なし_" で行う場合は、[ブラウザー ベースのガイダンス](getting-started-build-a-classifier.md)を参照してください。

## <a name="prerequisites"></a>前提条件

- [Python 2.7 以降または 3.5 以降](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) ツール
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="install-the-custom-vision-sdk"></a>Custom Vision SDK をインストールする

Custom Vision Service SDK for Python をインストールするには、PowerShell で次のコマンドを実行します。

```powershell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]

## <a name="add-the-code"></a>コードの追加

目的のプロジェクト ディレクトリに、*sample.py* という新しいファイルを作成します。

### <a name="create-the-custom-vision-service-project"></a>Custom Vision Service プロジェクトを作成する

新しい Custom Vision Service プロジェクトを作成するための次のコードをスクリプトに追加します。 該当する各定義にサブスクリプション キーを挿入します。 また、Custom Vision Web サイトの [設定] ページからエンドポイント URL を取得します。

プロジェクトを作成するときに他のオプションを指定するには、[create_project](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.custom_vision_training_client.customvisiontrainingclient?view=azure-python#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config- ) メソッドを参照してください ([分類子の構築](getting-started-build-a-classifier.md)に関する Web ポータル ガイドで説明されています)。  

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateEntry

ENDPOINT = "<your API endpoint>"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"
prediction_resource_id = "<your prediction resource id>"

publish_iteration_name = "classifyModel"

trainer = CustomVisionTrainingClient(training_key, endpoint=ENDPOINT)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My New Project")
```

### <a name="create-tags-in-the-project"></a>プロジェクトにタグを作成する

プロジェクトに分類タグを作成するため、*sample.py* の最後に次のコードを追加します。

```Python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

### <a name="upload-and-tag-images"></a>画像をアップロードし、タグ付けする

サンプルの画像をプロジェクトに追加するには、タグ作成後、次のコードを挿入します。 このコードでは、それぞれの画像を対応するタグと共にアップロードします。 1 回のバッチで最大 64 個の画像をアップロードできます。

> [!NOTE]
> 画像のパスは、事前に Cognitive Services Python SDK Samples プロジェクトをダウンロードした場所に応じて変更する必要があります。

```Python
base_image_url = "<path to project>"

print("Adding images...")

image_list = []

for image_num in range(1, 11):
    file_name = "hemlock_{}.jpg".format(image_num)
    with open(base_image_url + "images/Hemlock/" + file_name, "rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[hemlock_tag.id]))

for image_num in range(1, 11):
    file_name = "japanese_cherry_{}.jpg".format(image_num)
    with open(base_image_url + "images/Japanese Cherry/" + file_name, "rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[cherry_tag.id]))

upload_result = trainer.create_images_from_files(project.id, images=image_list)
if not upload_result.is_batch_successful:
    print("Image batch upload failed.")
    for image in upload_result.images:
        print("Image status: ", image.status)
    exit(-1)
```

### <a name="train-the-classifier-and-publish"></a>分類器をトレーニングしてする公開する

このコードにより、プロジェクトの最初のイテレーションが作成され、そのイテレーションが予測エンドポイントに公開されます。 公開されたイテレーションに付けられた名前は、予測要求を送信するために使用できます。 イテレーションは、公開されるまで予測エンドポイントで利用できません。

```Python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Publish it to the project endpoint
trainer.publish_iteration(project.id, iteration.id, publish_iteration_name, prediction_resource_id)
print ("Done!")
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>予測エンドポイントで公開されたイテレーションを取得して使用する

画像を予測エンドポイントに送信し、予測を取得するには、ファイルの末尾に以下のコードを追加します。

```python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient

# Now there is a trained endpoint that can be used to make a prediction
predictor = CustomVisionPredictionClient(prediction_key, endpoint=ENDPOINT)

with open(base_image_url + "images/Test/test_image.jpg", "rb") as image_contents:
    results = predictor.classify_image(
        project.id, publish_iteration_name, image_contents.read())

    # Display the results.
    for prediction in results.predictions:
        print("\t" + prediction.tag_name +
              ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>アプリケーションの実行

*sample.py* を実行します。

```powershell
python sample.py
```

アプリケーションの出力は次のテキストのようになります。

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

**<base_image_url>/Images/Test/** 内のテスト画像にタグが適切に付けられていることを確認してください。 [Custom Vision Web サイト](https://customvision.ai)に戻って、新しく作成されたプロジェクトの現在の状態を確認してもかまいません。

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>次の手順

以上、画像の分類処理の各ステップをコードでどのように実装するかを見てきました。 このサンプルで実行したトレーニングのイテレーションは 1 回だけですが、多くの場合、精度を高めるために、モデルのトレーニングとテストは複数回行う必要があります。

> [!div class="nextstepaction"]
> [モデルのテストと再トレーニング](test-your-model.md)
