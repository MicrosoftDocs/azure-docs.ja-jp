---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: 49b920ede2b0af306af00875a3368cffd853f89b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947142"
---
このガイドでは、Go 用の Custom Vision クライアント ライブラリを使用して画像分類モデルを構築する際の足がかりとして役立つ手順とサンプル コードを紹介します。 プロジェクトを作成し、タグを追加し、プロジェクトをトレーニングして、プロジェクトの予測エンドポイント URL を使用してプログラムでテストします。 この例は、独自の画像認識アプリを構築するためのテンプレートとしてご利用ください。

> [!NOTE]
> 分類モデルの構築と使用をコードを記述 "_することなく_" 行う場合は、[ブラウザーベースのガイダンス](../../getting-started-build-a-classifier.md)を参照してください。

## <a name="prerequisites"></a>前提条件

- [Go 1.8+](https://golang.org/doc/install)
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-client-library"></a>Custom Vision クライアント ライブラリをインストールする

Go 用の Custom Vision で画像分析アプリを作成するには、Custom Vision のサービス クライアント ライブラリが必要です。 PowerShell で次のコマンドを実行します。

```shell
go get -u github.com/Azure/azure-sdk-for-go/...
```

または、`dep` を使用している場合は、リポジトリ内で次を実行します。
```shell
dep ensure -add github.com/Azure/azure-sdk-for-go
```

[!INCLUDE [get-keys](../../includes/get-keys.md)]

[!INCLUDE [python-get-images](../../includes/python-get-images.md)]

## <a name="add-the-code"></a>コードの追加

目的のプロジェクト ディレクトリに、*sample.go* という新しいファイルを作成します。

## <a name="create-the-custom-vision-project"></a>Custom Vision プロジェクトを作成する

新しい Custom Vision Service プロジェクトを作成するための次のコードをスクリプトに追加します。 該当する各定義にサブスクリプション キーを挿入します。 また、Custom Vision Web サイトの [設定] ページからエンドポイント URL を取得します。

プロジェクトを作成するときに他のオプションを指定するには、[CreateProject](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) メソッドを参照してください ([分類子の構築](../../getting-started-build-a-classifier.md)に関する Web ポータル ガイドで説明されています)。

```go
import(
    "context"
    "bytes"
    "fmt"
    "io/ioutil"
    "path"
    "log"
    "time"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/training"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/prediction"
)

var (
    training_key string = "<your training key>"
    prediction_key string = "<your prediction key>"
    prediction_resource_id = "<your prediction resource id>"
    endpoint string = "<your endpoint URL>"
    project_name string = "Go Sample Project"
    iteration_publish_name = "classifyModel"
    sampleDataDirectory = "<path to sample images>"
)

func main() {
    fmt.Println("Creating project...")

    ctx = context.Background()

    trainer := training.New(training_key, endpoint)

    project, err := trainer.CreateProject(ctx, project_name, "sample project", nil, string(training.Multilabel))
    if (err != nil) {
        log.Fatal(err)
    }
```

## <a name="create-tags-in-the-project"></a>プロジェクトにタグを作成する

プロジェクトに分類タグを作成するため、*sample.go* の最後に次のコードを追加します。

```go
// Make two tags in the new project
hemlockTag, _ := trainer.CreateTag(ctx, *project.ID, "Hemlock", "Hemlock tree tag", string(training.Regular))
cherryTag, _ := trainer.CreateTag(ctx, *project.ID, "Japanese Cherry", "Japanese cherry tree tag", string(training.Regular))
```

## <a name="upload-and-tag-images"></a>画像をアップロードし、タグ付けする

サンプルの画像をプロジェクトに追加するには、タグ作成後、次のコードを挿入します。 このコードでは、それぞれの画像を対応するタグと共にアップロードします。 1 回のバッチで最大 64 個の画像をアップロードできます。

> [!NOTE]
> 画像のパスは、事前に Cognitive Services Go SDK Samples プロジェクトをダウンロードした場所に応じて変更する必要があります。

```go
fmt.Println("Adding images...")
japaneseCherryImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Japanese Cherry"))
if err != nil {
    fmt.Println("Error finding Sample images")
}

hemLockImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Hemlock"))
if err != nil {
    fmt.Println("Error finding Sample images")
}

for _, file := range hemLockImages {
    imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Hemlock", file.Name()))
    imageData := ioutil.NopCloser(bytes.NewReader(imageFile))

    trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ hemlockTag.ID.String() })
}

for _, file := range japaneseCherryImages {
    imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Japanese Cherry", file.Name()))
    imageData := ioutil.NopCloser(bytes.NewReader(imageFile))
    trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ cherryTag.ID.String() })
}
```

## <a name="train-and-publish-the-project"></a>プロジェクトをトレーニングして公開する

このコードにより、予測モデルの最初のイテレーションが作成され、そのイテレーションが予測エンドポイントに公開されます。 公開されたイテレーションに付けられた名前は、予測要求を送信するために使用できます。 イテレーションは、公開されるまで予測エンドポイントで利用できません。

```go
fmt.Println("Training...")
iteration, _ := trainer.TrainProject(ctx, *project.ID)
for {
    if *iteration.Status != "Training" {
        break
    }
    fmt.Println("Training status: " + *iteration.Status)
    time.Sleep(1 * time.Second)
    iteration, _ = trainer.GetIteration(ctx, *project.ID, *iteration.ID)
}
fmt.Println("Training status: " + *iteration.Status)

trainer.PublishIteration(ctx, *project.ID, *iteration.ID, iteration_publish_name, prediction_resource_id))
```

## <a name="use-the-prediction-endpoint"></a>予測エンドポイントを使用する

画像を予測エンドポイントに送信し、予測を取得するには、ファイルの末尾に以下のコードを追加します。

```go
    fmt.Println("Predicting...")
    predictor := prediction.New(prediction_key, endpoint)

    testImageData, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Test", "test_image.jpg"))
    results, _ := predictor.ClassifyImage(ctx, *project.ID, iteration_publish_name, ioutil.NopCloser(bytes.NewReader(testImageData)), "")

    for _, prediction := range *results.Predictions    {
        fmt.Printf("\t%s: %.2f%%", *prediction.TagName, *prediction.Probability * 100)
        fmt.Println("")
    }
}
```

## <a name="run-the-application"></a>アプリケーションの実行

*sample.go* を実行します。

```shell
go run sample.go
```

アプリケーションの出力は次のテキストのようになります。

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

**<base_image_url>/Images/Test/** 内のテスト画像にタグが適切に付けられていることを確認してください。 [Custom Vision Web サイト](https://customvision.ai)に戻って、新しく作成されたプロジェクトの現在の状態を確認してもかまいません。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>次のステップ

物体検出処理の各ステップをコードでどのように実装するかを見てきました。 このサンプルで実行したトレーニングのイテレーションは 1 回だけですが、多くの場合、精度を高めるために、モデルのトレーニングとテストは複数回行う必要があります。

> [!div class="nextstepaction"]
> [モデルのテストと再トレーニング](../../test-your-model.md)

* Custom Vision とは
* [SDK のリファレンス ドキュメント (トレーニング)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/customvision/training)
* [SDK のリファレンス ドキュメント (予測)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.1/customvision/prediction)
