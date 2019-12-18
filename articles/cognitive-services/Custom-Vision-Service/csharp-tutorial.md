---
title: クイック スタート:Custom Vision SDK for C# を使って画像分類プロジェクトを作成する
titleSuffix: Azure Cognitive Services
description: NET SDK と C# を使用して、プロジェクトの作成、タグの追加、画像のアップロード、プロジェクトのトレーニング、予測を行います。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: anroth
ms.openlocfilehash: db98464ecefaaf177161a1e417496ee7c994cff0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978647"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>クイック スタート:Custom Vision .NET SDK を使って画像分類プロジェクトを作成する

この記事では、Custom Vision SDK と C# を使用して画像分類モデルを構築する際の足掛かりとして役立つ情報とサンプル コードを紹介します。 作成後、タグを追加し、イメージをアップロードし、プロジェクトをトレーニングし、プロジェクトの既定の予測エンドポイント URL を取得し、エンドポイントを使用してイメージをプログラミングでテストできます。 この例は、独自の .NET アプリケーションを構築するためのテンプレートとしてご利用ください。 分類モデルの構築と使用のプロセスをコード "_なし_" で行う場合は、[ブラウザーベースのガイダンス](getting-started-build-a-classifier.md)を参照してください。

## <a name="prerequisites"></a>前提条件

- [Visual Studio 2015 または 2017](https://www.visualstudio.com/downloads/) の任意のエディション
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Custom Vision SDK とサンプル コードを入手する

Custom Vision を使用する .NET アプリを作成するには、Custom Vision NuGet パッケージが必要となります。 これらのパッケージは、これからダウンロードするサンプル プロジェクトに含まれていますが、ここから個別にアクセスすることもできます。

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

[Cognitive Services .NET サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples) プロジェクトを複製またはダウンロードします。 **CustomVision/ImageClassification** フォルダーに移動して、_ImageClassification.csproj_ を Visual Studio で開きます。

この Visual Studio プロジェクトは、__My New Project__ という名前の新しい Custom Vision プロジェクトを作成します。作成したプロジェクトには、[Custom Vision Web サイト](https://customvision.ai/)からアクセスすることができます。 その後、イメージをアップロードして分類子のトレーニングおよびテストを行います。 このプロジェクトでは、木が __Hemlock__ (ドクニンジン) であるか __Japanese Cherry__ (桜) であるかを判別することが、分類子の目的となります。

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>コードの理解

_Program.cs_ ファイルを開いて、コードを詳しく調べます。 トレーニング キーと予測キーの[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)し、それぞれ `CUSTOM_VISION_TRAINING_KEY`、`CUSTOM_VISION_PREDICTION_KEY` という名前を付けます。 スクリプトによってこれらの変数が検索されます。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_keys)]

また、Custom Vision Web サイトの [設定] ページからエンドポイント URL を取得します。 それを `CUSTOM_VISION_ENDPOINT` という環境変数に保存します。 スクリプトによって、この環境変数への参照がクラスのルートに保存されます。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_endpoint)]

このプロジェクトの主な機能は、以下のコード行によって実行されます。

### <a name="create-a-new-custom-vision-service-project"></a>新しい Custom Vision Service プロジェクトを作成する

作成したプロジェクトは、先ほどアクセスした [Custom Vision Web サイト](https://customvision.ai/)に表示されます。 プロジェクトを作成するときに他のオプションを指定するには、[CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) メソッドを参照してください ([分類子の構築](getting-started-build-a-classifier.md)に関する Web ポータル ガイドで説明されています)。   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>プロジェクトにタグを作成する

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>画像をアップロードし、タグ付けする

このプロジェクトの画像を追加します。 これらは、_Program.cs_ の **LoadImagesFromDisk** メソッドで参照します。 1 回のバッチで最大 64 個の画像をアップロードできます。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]

### <a name="train-the-classifier-and-publish"></a>分類器をトレーニングしてする公開する

このコードにより、プロジェクトの最初のイテレーションが作成され、そのイテレーションが予測エンドポイントに公開されます。 イテレーションの名前を使用することで予測要求を送信できます。 イテレーションは、公開されるまで予測エンドポイントで利用できません。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

### <a name="set-the-prediction-endpoint"></a>予測エンドポイントを設定する

予測エンドポイントは、現在のモデルに画像を送信して分類予測を取得する際に使用できる参照です。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction_endpoint)]

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>既定の予測エンドポイントに画像を送信する

このスクリプトでは、**LoadImagesFromDisk** メソッドでテスト画像が読み込まれ、モデルの予測出力がコンソールに表示されます。 `publishedModelName` 変数の値は、Custom Vision ポータルの **[パフォーマンス]** タブにある "として公開される" の値に対応している必要があります。 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction)]

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

以上、画像分類処理の各ステップをコードでどのように実装するかを見てきました。 このサンプルで実行したトレーニングのイテレーションは 1 回だけですが、多くの場合、精度を高めるために、モデルのトレーニングとテストは複数回行う必要があります。

> [!div class="nextstepaction"]
> [モデルのテストと再トレーニング](test-your-model.md)
