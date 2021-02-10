---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: efd2f35a24ac762ce78889d99c375613239f2604
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500115"
---
.NET 用 Custom Vision クライアント ライブラリの使用を開始します。 以下の手順に従って、パッケージをインストールし、画像分類モデルを構築するためのコード例を試してみましょう。 プロジェクトを作成し、タグを追加し、プロジェクトをトレーニングして、プロジェクトの予測エンドポイント URL を使用してプログラムでテストします。 この例は、独自の画像認識アプリを構築するためのテンプレートとしてご利用ください。

> [!NOTE]
> コードを記述 "_せずに_" 分類モデルの構築とトレーニングを行う場合は、[ブラウザーベースのガイダンス](../../getting-started-build-a-classifier.md)を参照してください。

.NET 用 Custom Vision クライアント ライブラリを使用すると、次のことができます。

* 新しい Custom Vision プロジェクトを作成する
* プロジェクトにタグを追加する
* 画像をアップロードし、タグ付けする
* プロジェクトをトレーニングする
* 現在のイテレーションを公開する
* 予測エンドポイントをテストする

[リファレンス ドキュメント](/dotnet/api/overview/azure/cognitiveservices/client/customvision) | ライブラリのソース コード [(トレーニング)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Training) [(予測)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Prediction) | パッケージ (NuGet) [(トレーニング)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/) [(予測)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/) | [サンプル](/samples/browse/?products=azure&term=vision&terms=vision)


## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) または現在のバージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="Custom Vision リソースを作成"  target="_blank">Custom Vision リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、トレーニングおよび予測リソースを作成し、キーとエンドポイントを取得します。 デプロイするまで待ち、 **[リソースに移動]** ボタンをクリックします。
    * 対象のアプリケーションを Custom Vision に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Visual Studio を使用して、新しい .NET Core アプリケーションを作成します。 

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする 

新しいプロジェクトを作成したら、**ソリューション エクスプローラー** でプロジェクト ソリューションを右クリックし、 **[NuGet パッケージの管理]** を選択して、クライアント ライブラリをインストールします。 パッケージ マネージャーが開いたら、 **[参照]** を選択し、 **[プレリリースを含める]** をオンにして、`Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training` と `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction` を検索します。 最新バージョンを選択し、 **[インストール]** を選択します。 

#### <a name="cli"></a>[CLI](#tab/cli)

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`custom-vision-quickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイル (*program.cs*) を使用する単純な "Hello World" C# プロジェクトが作成されます。 

```console
dotnet new console -n custom-vision-quickstart
```

新しく作成されたアプリ フォルダーにディレクトリを変更します。 次を使用してアプリケーションをビルドできます。

```console
dotnet build
```

ビルドの出力に警告やエラーが含まれないようにする必要があります。 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする 

次のコマンドを使用して、アプリケーション ディレクトリ内に .NET 用 Custom Vision クライアント ライブラリをインストールします。

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training --version 2.0.0
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction --version 2.0.0
```

---

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs) にあり、このクイックスタートのコード例が含まれています。

プロジェクト ディレクトリから *program.cs* ファイルを開いて、次の `using` ディレクティブを追加します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_imports)]


アプリケーションの **Main** メソッドで、リソースのキーとエンドポイントの変数を作成します。 さらに、後で使用するいくつかの基本的なオブジェクトを宣言します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_creds)]

> [!IMPORTANT]
> Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Custom Vision リソースが正常にデプロイされた場合、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 キーとエンドポイントは、リソースの **[キー] および [エンドポイント]** ページの **[リソース管理]** にあります。 トレーニング キーと予測キーの両方およびトレーニング リソースのエンドポイントを取得する必要があります。
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 詳細については、Cognitive Services の[セキュリティ](../../../cognitive-services-security.md)に関するページを参照してください。

アプリケーションの **Main** メソッドで、このクイックスタートで使用するメソッドの呼び出しを追加します。 これらは後で実装します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_maincalls)]

## <a name="object-model"></a>オブジェクト モデル

|名前|説明|
|---|---|
|[CustomVisionTrainingClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | このクラスでは、モデルの作成、トレーニング、および公開を処理します。 |
|[CustomVisionPredictionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| このクラスでは、画像分類予測のために、モデルに対するクエリ実行を処理します。|
|[PredictionModel](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel)| このクラスでは、単一の画像に対して単一の予測を定義します。 これには、オブジェクト ID と名前、および信頼度スコアのプロパティが含まれます。|

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、.NET 用 Custom Vision クライアント ライブラリを使用して次のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [新しい Custom Vision プロジェクトを作成する](#create-a-new-custom-vision-project)
* [プロジェクトにタグを追加する](#add-tags-to-the-project)
* [画像をアップロードし、タグ付けする](#upload-and-tag-images)
* [プロジェクトをトレーニングする](#train-the-project)
* [現在のイテレーションを公開する](#publish-the-current-iteration)
* [予測エンドポイントをテストする](#test-the-prediction-endpoint)


## <a name="authenticate-the-client"></a>クライアントを認証する

新しいメソッドで、エンドポイントとキーを使用してトレーニング クライアントと予測クライアントをインスタンス化します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>新しい Custom Vision プロジェクトを作成する

次のコードでは、画像分類プロジェクトを作成します。 作成したプロジェクトは、[Custom Vision Web サイト](https://customvision.ai/)に表示されます。 プロジェクトを作成するときに他のオプションを指定するには、[CreateProject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true) メソッドを参照してください ([分類子の構築](../../getting-started-build-a-classifier.md)に関する Web ポータル ガイドで説明されています)。  

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>プロジェクトにタグを追加する

このメソッドでは、モデルをトレーニングするタグを定義します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_addtags)]

## <a name="upload-and-tag-images"></a>画像をアップロードし、タグ付けする

最初に、このプロジェクト用のサンプル画像をダウンロードします。 [サンプル画像フォルダー](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images)の内容をお使いのローカル デバイスに保存します。

> [!NOTE]
> トレーニングを完了するために、より広範なイメージのセットが必要ですか。 Trove (Microsoft Garage プロジェクト) を使用すると、トレーニング目的で画像のセットを収集して購入することができます。 画像を収集したら、それらをダウンロードした後、通常の方法で Custom Vision プロジェクトにインポートできます。 詳細については、[Trove ページ](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3)を参照してください。

次に、このディレクトリ内の画像をアップロードするためのヘルパー メソッドを定義します。 画像が保存されている場所を指すように **GetFiles** 引数を編集する必要がある場合があります。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_loadimages)]

次に、画像をアップロードするためのメソッドを定義し、フォルダーの場所に従ってタグを適用します (画像は既に並べ替えられています)。 画像のアップロードとタグ付けは、反復的に実行することも、バッチで実行することもできます (バッチあたり最大 64 個)。 このコード スニペットには、両方の例が含まれています。 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]


## <a name="train-the-project"></a>プロジェクトをトレーニングする

このメソッドは、プロジェクトにおけるトレーニングの初回イテレーションを作成するものです。 トレーニングが完了するまで、サービスに対してクエリが実行されます。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

> [!TIP]
> 選択したタグでトレーニングする
>
> 必要に応じて、適用したタグのサブセットのみでトレーニングできます。 ある特定のタグが十分に適用されていないが、他のタグが十分に適用されている場合に、これを行うことができます。 [TrainProject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true) 呼び出しで、*trainingParameters* パラメーターを使用します。 [TrainingParameters](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters) を構築し、その **SelectedTags** プロパティを、使用するタグの ID の一覧に設定します。 モデルは、そのリストのタグのみを認識するようにトレーニングします。

## <a name="publish-the-current-iteration"></a>現在のイテレーションを公開する

このメソッドでは、モデルの現在のイテレーションをクエリの実行に使用できるようにします。 モデル名は、予測要求を送信するための参照として使用できます。 `predictionResourceId` には、独自の値を入力する必要があります。 予測リソース ID は、Azure portal の対象のリソースの **[概要]** タブに、**サブスクリプション ID** として表示されます。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>予測エンドポイントをテストする

この部分のスクリプトでは、テスト画像を読み込み、モデル エンドポイントを照会して、予測データをコンソールに出力します。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_test)]


## <a name="run-the-application"></a>アプリケーションの実行

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

IDE ウィンドウの上部にある **[デバッグ]** ボタンをクリックして、アプリケーションを実行します。

#### <a name="cli"></a>[CLI](#tab/cli)

アプリケーション ディレクトリから `dotnet run` コマンドを使用してアプリケーションを実行します。

```dotnet
dotnet run
```

---

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

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>次のステップ

これで、コードでの画像分類処理の各ステップが完了しました。 このサンプルで実行したトレーニングのイテレーションは 1 回だけですが、多くの場合、精度を高めるために、モデルのトレーニングとテストは複数回行う必要があります。

> [!div class="nextstepaction"]
> [モデルのテストと再トレーニング](../../test-your-model.md)

* Custom Vision とは
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs) にあります。
* [SDK のリファレンス ドキュメント](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
