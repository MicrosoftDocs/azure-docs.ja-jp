---
title: クイック スタート:Custom Vision SDK for Node.js を使用して画像分類プロジェクトを作成する
titlesuffix: Azure Cognitive Services
description: Node.js SDK を使用して、プロジェクトの作成、タグの追加、画像のアップロード、プロジェクトのトレーニング、予測を行います。
services: cognitive-services
author: areddish
manager: daauld
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 07/15/2019
ms.author: areddish
ms.openlocfilehash: 92713fe16e482a3ed65b9593581749270b67a487
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277585"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-nodejs-sdk"></a>クイック スタート:Custom Vision Node.js SDK を使用して画像分類プロジェクトを作成する

この記事では、Custom Vision SDK と Node.js を使用して画像分類モデルを構築する際の足がかりとして役立つ情報とサンプル コードを紹介します。 作成後は、タグの追加、画像のアップロード、プロジェクトのトレーニング、プロジェクトの公開された予測エンドポイント URL の取得、エンドポイントを使用したプログラミングによる画像のテストを行うことができます。 この例は、独自の Node.js アプリケーションを構築するためのテンプレートとしてご利用ください。 分類モデルの構築と使用のプロセスをコード "_なし_" で行う場合は、[ブラウザー ベースのガイダンス](getting-started-build-a-classifier.md)を参照してください。

## <a name="prerequisites"></a>前提条件

- [Node.js 8](https://www.nodejs.org/en/download/) 以降がインストールされている。
- [npm](https://www.npmjs.com/) がインストールされている。

## <a name="install-the-custom-vision-sdk"></a>Custom Vision SDK をインストールする

Custom Vision Service SDK for Node.js をインストールするには、PowerShell で次のコマンドを実行します。

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [node-get-images](includes/node-get-images.md)]

## <a name="add-the-code"></a>コードの追加

目的のプロジェクト ディレクトリに、*sample.js* という新しいファイルを作成します。

### <a name="create-the-custom-vision-service-project"></a>Custom Vision Service プロジェクトを作成する

新しい Custom Vision Service プロジェクトを作成するための次のコードをスクリプトに追加します。 該当する各定義にサブスクリプション キーを挿入します。

```javascript
const util = require('util');
const fs = require('fs');
const TrainingApiClient = require("@azure/cognitiveservices-customvision-training");
const PredictionApiClient = require("@azure/cognitiveservices-customvision-prediction");

const setTimeoutPromise = util.promisify(setTimeout);

const trainingKey = "<your training key>";
const predictionKey = "<your prediction key>";
const predictionResourceId = "<your prediction resource id>";
const sampleDataRoot = "<path to image files>";

const endPoint = "https://southcentralus.api.cognitive.microsoft.com"

const publishIterationName = "classifyModel";

const trainer = new TrainingApiClient(trainingKey, endPoint);

(async () => {
    console.log("Creating project...");
    const sampleProject = await trainer.createProject("Sample Project")
```

### <a name="create-tags-in-the-project"></a>プロジェクトにタグを作成する

プロジェクトに分類タグを作成するため、*sample.js* の末尾に次のコードを追加します。

```javascript
const hemlockTag = await trainer.createTag(sampleProject.id, "Hemlock");
const cherryTag = await trainer.createTag(sampleProject.id, "Japanese Cherry");
```

### <a name="upload-and-tag-images"></a>画像をアップロードし、タグ付けする

サンプルの画像をプロジェクトに追加するには、タグ作成後、次のコードを挿入します。 このコードでは、それぞれの画像を対応するタグと共にアップロードします。 Cognitive Services Node.js SDK Samples プロジェクトをダウンロードした場所に応じて、画像のベース ファイル パスを入力する必要があります。

> [!NOTE]
> 前に Cognitive Services Node.js SDK Samples プロジェクトをダウンロードした場所に応じて、*sampleDataRoot* を画像へのパスに変更する必要があります。

```javascript
console.log("Adding images...");
let fileUploadPromises = [];

const hemlockDir = `${sampleDataRoot}/Hemlock`;
const hemlockFiles = fs.readdirSync(hemlockDir);
hemlockFiles.forEach(file => {
    fileUploadPromises.push(trainer.createImagesFromData(sampleProject.id, fs.readFileSync(`${hemlockDir}/${file}`), { tagIds: [hemlockTag.id] }));
});

const cherryDir = `${sampleDataRoot}/Japanese Cherry`;
const japaneseCherryFiles = fs.readdirSync(cherryDir);
japaneseCherryFiles.forEach(file => {
    fileUploadPromises.push(trainer.createImagesFromData(sampleProject.id, fs.readFileSync(`${cherryDir}/${file}`), { tagIds: [cherryTag.id] }));
});

await Promise.all(fileUploadPromises);
```

### <a name="train-the-classifier-and-publish"></a>分類器をトレーニングしてする公開する

このコードにより、プロジェクトの最初のイテレーションが作成され、そのイテレーションが予測エンドポイントに公開されます。 公開されたイテレーションに付けられた名前は、予測要求を送信するために使用できます。 イテレーションは、公開されるまで予測エンドポイントで利用できません。

```javascript
console.log("Training...");
let trainingIteration = await trainer.trainProject(sampleProject.id);

// Wait for training to complete
console.log("Training started...");
while (trainingIteration.status == "Training") {
    console.log("Training status: " + trainingIteration.status);
    await setTimeoutPromise(1000, null);
    trainingIteration = await trainer.getIteration(sampleProject.id, trainingIteration.id)
}
console.log("Training status: " + trainingIteration.status);

// Publish the iteration to the end point
await trainer.publishIteration(sampleProject.id, trainingIteration.id, publishIterationName, predictionResourceId);
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>予測エンドポイントで公開されたイテレーションを取得して使用する

画像を予測エンドポイントに送信し、予測を取得するには、ファイルの末尾に以下のコードを追加します。

```javascript
    const predictor = new PredictionApiClient(predictionKey, endPoint);
    const testFile = fs.readFileSync(`${sampleDataRoot}/Test/test_image.jpg`);

    const results = await predictor.classifyImage(sampleProject.id, publishIterationName, testFile);

    // Step 6. Show results
    console.log("Results:");
    results.predictions.forEach(predictedResult => {
        console.log(`\t ${predictedResult.tagName}: ${(predictedResult.probability * 100.0).toFixed(2)}%`);
    });
})()
```

## <a name="run-the-application"></a>アプリケーションの実行

*sample.js* を実行します。

```shell
node sample.js
```

アプリケーションの出力は次のテキストのようになります。

```console
Creating project...
Adding images...
Training...
Training started...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Results:
         Hemlock: 94.97%
         Japanese Cherry: 0.01%
```

**<base_image_url>/Images/Test/** 内のテスト画像にタグが適切に付けられていることを確認してください。 [Custom Vision Web サイト](https://customvision.ai)に戻って、新しく作成されたプロジェクトの現在の状態を確認してもかまいません。

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>次の手順

以上、画像の分類処理の各ステップをコードでどのように実装するかを見てきました。 このサンプルで実行したトレーニングのイテレーションは 1 回だけですが、多くの場合、精度を高めるために、モデルのトレーニングとテストは複数回行う必要があります。

> [!div class="nextstepaction"]
> [モデルのテストと再トレーニング](test-your-model.md)
