---
title: クイック スタート:Custom Vision SDK for Java を使用して物体検出プロジェクトを作成する
titleSuffix: Azure Cognitive Services
description: Java SDK を使用して、プロジェクトの作成、タグの追加、画像のアップロード、プロジェクトのトレーニング、物体の検出を行います。
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: cad7c6464cff05bee7e2b086d22f9e696dfa1c4b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970314"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-sdk-for-java"></a>クイック スタート:Custom Vision SDK for Java を使用して物体検出プロジェクトを作成する

この記事では、Custom Vision SDK と Java を使用して物体検出モデルを構築する基本的な方法について説明します。 作成後、タグ付きのリージョンを追加し、画像をアップロードし、プロジェクトをトレーニングし、プロジェクトの既定の予測エンドポイント URL を取得し、エンドポイントを使用して画像をプログラミングでテストできます。 この例は、独自の Java アプリケーションを構築するためのテンプレートとしてご利用ください。

## <a name="prerequisites"></a>前提条件

- 任意の Java IDE
- [JDK 7 または 8](https://aka.ms/azure-jdks) がインストールされていること。
- Maven がインストールされていること
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Custom Vision SDK とサンプル コードを入手する

Custom Vision を使用する Java アプリを作成するには、Custom Vision maven パッケージが必要となります。 これらのパッケージは、これからダウンロードするサンプル プロジェクトに含まれていますが、ここから個別にアクセスすることもできます。

Maven Central Repository から Custom Vision SDK をインストールできます。
- [Training SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Prediction SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

[Cognitive Services Java SDK サンプル](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master) プロジェクトを複製またはダウンロードします。 **Vision/CustomVision/** フォルダーに移動します。

この Java プロジェクトは、__Sample Java OD Project__ という名前の新しい Custom Vision 物体検出プロジェクトを作成します。作成したプロジェクトには、[Custom Vision Web サイト](https://customvision.ai/)からアクセスすることができます。 その後、イメージをアップロードして分類子のトレーニングおよびテストを行います。 このプロジェクトでは、木が __Hemlock__ (ドクニンジン) であるか __Japanese Cherry__ (桜) であるかを判別することが、分類子の目的となります。

[!INCLUDE [get-keys](includes/get-keys.md)]

このプログラムは、キー データを環境変数として格納するように構成されています。 これらの変数は、PowerShell で **Vision/CustomVision** フォルダーに移動することによって設定します。 次のコマンドを入力してください。

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>コードの理解

Java IDE で `Vision/CustomVision` プロジェクトを読み込み、_CustomVisionSamples.java_ ファイルを開きます。 **runSample** メソッドを探して、**ImageClassification_Sample** メソッド呼び出しをコメント アウトしてください&mdash;このメソッドで画像分類のシナリオが実行されますが、このガイドの対象外です。 このクイック スタートの主な機能は、**ObjectDetection_Sample** メソッドに実装されます。このメソッドの定義に移動し、コードを詳しく調べてみましょう。 

### <a name="create-a-new-custom-vision-service-project"></a>新しい Custom Vision Service プロジェクトを作成する

トレーニング クライアントと物体検出プロジェクトを作成するコード ブロックに移動します。 作成したプロジェクトは、先ほどアクセスした [Custom Vision Web サイト](https://customvision.ai/)に表示されます。 プロジェクトを作成するときに他のオプションを指定するには、[CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) メソッドのオーバーロードを参照してください ([検出器の構築](get-started-build-detector.md)に関する Web ポータル ガイドで説明されています)。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

### <a name="add-tags-to-your-project"></a>タグをプロジェクトに追加する

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

### <a name="upload-and-tag-images"></a>画像をアップロードし、タグ付けする

物体検出プロジェクトで画像にタグを付ける際は、タグ付けする各物体の領域を正規化座標を使用して指定する必要があります。 `regionMap` マップの定義に移動します。 このコードは、それぞれのサンプル画像とそのタグ付け領域とを関連付けるものです。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

次に、プロジェクトに画像を追加するコード ブロックに進みます。 画像は、プロジェクトの **src/main/resources** フォルダーから読み取られ、その適切なタグと領域座標と共にサービスにアップロードされます。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

前のコード スニペットでは、リソース ストリームとして画像を取得してサービスにアップロードする 2 つのヘルパー関数を使用しています (1 回のバッチで最大 64 個の画像をアップロードできます)。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-project-and-publish"></a>プロジェクトをトレーニングして公開する

このコードにより、プロジェクトの最初のイテレーションが作成され、そのイテレーションが予測エンドポイントに公開されます。 公開されたイテレーションに付けられた名前は、予測要求を送信するために使用できます。 イテレーションは、公開されるまで予測エンドポイントで利用できません。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]

### <a name="use-the-prediction-endpoint"></a>予測エンドポイントを使用する

予測エンドポイントは、現在のモデルに画像を送信して分類予測を取得する際に使用できる参照で、ここでは `predictor` で表されます。 このサンプルでは、どこか他の場所で、予測キーの環境変数を使用して `predictor` が定義されています。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_prediction_od)]

## <a name="run-the-application"></a>アプリケーションの実行

Maven を使用してソリューションをコンパイルおよび実行するには、PowerShell でプロジェクト ディレクトリから次のコマンドを実行します。

```powershell
mvn compile exec:java
```

ログと予測結果のコンソール出力が表示されます。 テスト画像にタグが適切に付けられていること、また検出の領域が正しいことを確認してください。

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>次の手順

以上、物体検出処理の各ステップをコードでどのように実装するかを見てきました。 このサンプルで実行したトレーニングのイテレーションは 1 回だけですが、多くの場合、精度を高めるために、モデルのトレーニングとテストは複数回行う必要があります。 次のガイドでは、画像の分類について取り上げていますが、その原理は物体の検出と似ています。

> [!div class="nextstepaction"]
> [モデルのテストと再トレーニング](test-your-model.md)
