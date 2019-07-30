---
title: クイック スタート:Custom Vision SDK for C# を使って画像分類プロジェクトを作成する
titlesuffix: Azure Cognitive Services
description: NET SDK と C# を使用して、プロジェクトの作成、タグの追加、画像のアップロード、プロジェクトのトレーニング、予測を行います。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: anroth
ms.openlocfilehash: 28ea62ffa7a2b163b984c089649c1cd99d5e4556
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827552"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>クイック スタート:Custom Vision .NET SDK を使って画像分類プロジェクトを作成する

この記事では、Custom Vision SDK と C# を使用して画像分類モデルを構築する際の足掛かりとして役立つ情報とサンプル コードを紹介します。 作成後、タグを追加し、イメージをアップロードし、プロジェクトをトレーニングし、プロジェクトの既定の予測エンドポイント URL を取得し、エンドポイントを使用してイメージをプログラミングでテストできます。 この例は、独自の .NET アプリケーションを構築するためのテンプレートとしてご利用ください。 分類モデルの構築と使用のプロセスをコード "_なし_" で行う場合は、[ブラウザー ベースのガイダンス](getting-started-build-a-classifier.md)を参照してください。

## <a name="prerequisites"></a>前提条件

- [Visual Studio 2015 または 2017](https://www.visualstudio.com/downloads/) の任意のエディション

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Custom Vision SDK とサンプル コードを入手する

Custom Vision を使用する .NET アプリを作成するには、Custom Vision NuGet パッケージが必要となります。 これらのパッケージは、これからダウンロードするサンプル プロジェクトに含まれていますが、ここから個別にアクセスすることもできます。

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

[Cognitive Services .NET サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples) プロジェクトを複製またはダウンロードします。 **CustomVision/ImageClassification** フォルダーに移動して、_ImageClassification.csproj_ を Visual Studio で開きます。

この Visual Studio プロジェクトは、__My New Project__ という名前の新しい Custom Vision プロジェクトを作成します。作成したプロジェクトには、[Custom Vision Web サイト](https://customvision.ai/)からアクセスすることができます。 その後、イメージをアップロードして分類子のトレーニングおよびテストを行います。 このプロジェクトでは、木が __Hemlock__ (ドクニンジン) であるか __Japanese Cherry__ (桜) であるかを判別することが、分類子の目的となります。

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>コードの理解

_Program.cs_ ファイルを開いて、コードを詳しく調べます。 **Main** メソッド内の該当する各定義にサブスクリプション キーを挿入してください。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=21-30)]

Endpoint パラメーターは、Custom Vision リソースを含む Azure リソース グループが作成されたリージョンを指す必要があります。 この例では、米国中南部リージョンであると想定して、以下を使用しています。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=14-14)]

このプロジェクトの主な機能は、以下のコード行によって実行されます。

### <a name="create-a-new-custom-vision-service-project"></a>新しい Custom Vision Service プロジェクトを作成する

作成したプロジェクトは、先ほどアクセスした [Custom Vision Web サイト](https://customvision.ai/)に表示されます。 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=32-34)]

### <a name="create-tags-in-the-project"></a>プロジェクトにタグを作成する

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=36-38)]

### <a name="upload-and-tag-images"></a>画像をアップロードし、タグ付けする

このプロジェクトの画像を追加します。 これらは、_Program.cs_ の **LoadImagesFromDisk** メソッドで参照します。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=40-55)]

### <a name="train-the-classifier-and-publish"></a>分類器をトレーニングしてする公開する

このコードにより、プロジェクトの最初のイテレーションが作成され、そのイテレーションが予測エンドポイントに公開されます。 公開されたイテレーションに付けられた名前は、予測要求を送信するために使用できます。 イテレーションは、公開されるまで予測エンドポイントで利用できません。

```csharp
var iteration = trainingApi.TrainProject(project.Id);
// The returned iteration will be in progress, and can be queried periodically to see when it has completed
while (iteration.Status == "Training")
{
        Thread.Sleep(1000);

        // Re-query the iteration to get it's updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
}

// The iteration is now trained. Publish it to the prediction end point.
var publishedModelName = "treeClassModel";
var predictionResourceId = "<target prediction resource ID>";
trainingApi.PublishIteration(project.Id, iteration.Id, publishedModelName, predictionResourceId);
Console.WriteLine("Done!\n");
```

### <a name="set-the-prediction-endpoint"></a>予測エンドポイントを設定する

予測エンドポイントは、現在のモデルに画像を送信して分類予測を取得する際に使用できる参照です。

```csharp
// Create a prediction endpoint, passing in obtained prediction key
CustomVisionPredictionClient endpoint = new CustomVisionPredictionClient()
{
        ApiKey = predictionKey,
        Endpoint = SouthCentralUsEndpoint
};
```

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>既定の予測エンドポイントに画像を送信する

このスクリプトでは、**LoadImagesFromDisk** メソッドでテスト画像が読み込まれ、モデルの予測出力がコンソールに表示されます。

```csharp
// Make a prediction against the new project
Console.WriteLine("Making a prediction:");
var result = endpoint.ClassifyImage(project.Id, publishedModelName, testImage);

// Loop over each prediction and write out the results
foreach (var c in result.Predictions)
{
        Console.WriteLine($"\t{c.TagName}: {c.Probability:P1}");
}
```

## <a name="run-the-application"></a>アプリケーションの実行

アプリケーションを実行すると、コンソール ウィンドウが開いて、次の出力が書き込まれます。

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

**Images/Test/** 内のテスト画像にタグが適切に付けられていることを確認してください。 任意のキーを押してアプリケーションを終了します。 [Custom Vision Web サイト](https://customvision.ai)に戻って、新しく作成されたプロジェクトの現在の状態を確認してもかまいません。

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>次の手順

以上、画像の分類処理の各ステップをコードでどのように実装するかを見てきました。 このサンプルで実行したトレーニングのイテレーションは 1 回だけですが、多くの場合、精度を高めるために、モデルのトレーニングとテストは複数回行う必要があります。

> [!div class="nextstepaction"]
> [モデルのテストと再トレーニング](test-your-model.md)
