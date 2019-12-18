---
title: チュートリアル:.NET SDK を使用して画像内の顔データを検出して表示する
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、Face API を使用して画像内の顔を検出し、フレームに収める Windows アプリを作成します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: a444294497b82f316e7407999f5203cd13878928
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977966"
---
# <a name="tutorial-create-a-windows-presentation-framework-wpf-app-to-display-face-data-in-an-image"></a>チュートリアル:画像内の顔データを表示する Windows Presentation Framework (WPF) アプリを作成する

このチュートリアルでは、.NET クライアント SDK を通じて Azure Face API を使用し、画像内の顔を検出した後にそのデータを UI に示す方法を学習します。 顔を検出し、それぞれの顔の周囲にフレームを描画して、ステータス バーに顔の説明を表示する WPF アプリケーションを作成します。 

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> - WPF アプリケーションを作成する
> - Face API クライアント ライブラリをインストールする
> - クライアント ライブラリを使用して画像の中にある顔を検出する
> - 検出された顔の周囲にそれぞれフレームを描画する
> - 強調表示された顔の説明をステータス バーに表示する

![顔を検出して四角形のフレームに収めた画像のスクリーンショット](../Images/getting-started-cs-detected.png)

完全なサンプル コードは、GitHub 上の [Cognitive Face CSharp サンプル](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) リポジトリにあります。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 


## <a name="prerequisites"></a>前提条件

- Face API サブスクリプション キー。 無料試用版のサブスクリプション キーは「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=face-api)」から取得できます。 または、[Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページの手順に従って、Face API サービスをサブスクライブし、キーを取得します。 次に、キーとサービス エンドポイント文字列用に、それぞれ `FACE_SUBSCRIPTION_KEY` と `FACE_ENDPOINT` という名前の[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。
- [Visual Studio 2015 または 2017](https://www.visualstudio.com/downloads/) の任意のエディション。

## <a name="create-the-visual-studio-project"></a>Visual Studio プロジェクトの作成

新しい WPF アプリケーション プロジェクトを作成するには、次の手順に従います。

1. Visual Studio で、[新しいプロジェクト] ダイアログを開きます。 **[インストール済み]** 、 **[Visual C#]** の順に展開し、 **[WPF アプリ (.NET Framework)]** を選択します。
1. アプリケーションの名前を **FaceTutorial** に設定し、 **[OK]** をクリックします。
1. 必須の NuGet パッケージを入手します。 ソリューション エクスプローラーでプロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。次のパッケージを見つけてインストールします。
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="add-the-initial-code"></a>初期コードの追加

このセクションでは、顔に固有の機能を持たないアプリの基本的なフレームワークを追加します。

### <a name="create-the-ui"></a>UI を作成する

*MainWindow.xaml* を開き、その内容を次のコードに置き換えます&mdash;このコードにより UI ウィンドウが作成されます。 `FacePhoto_MouseMove` メソッドと `BrowseButton_Click` メソッドは、後で定義するイベント ハンドラーです。

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?name=snippet_xaml)]

### <a name="create-the-main-class"></a>main クラスを作成する

*MainWindow.xaml.cs* を開き、クライアント ライブラリの名前空間を、その他必要な名前空間と共に追加します。 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_using)]

次に、**MainWindow** クラスに次のコードを挿入します。 このコードでは、サブスクリプション キーとエンドポイントを使用して **FaceClient** インスタンスを作成します。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_fields)]

次に、**MainWindow** コンストラクターを追加します。 ここでは、エンドポイント URL 文字列を確認した後、クライアント オブジェクトに関連付けています。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_constructor)]

最後に、**BrowseButton_Click** メソッドと **FacePhoto_MouseMove** メソッドをクラスに追加します。 これらのメソッドは、*MainWindow.xaml* で宣言されているイベント ハンドラーに対応しています。 **BrowseButton_Click** メソッドは **OpenFileDialog** を作成し、ユーザーが .jpg 画像を選択できるようにします。 その後、メイン ウィンドウに画像を表示します。 **BrowseButton_Click** と **FacePhoto_MouseMove** の残りのコードについては、後の手順で挿入します。 また、`faceList` の参照にも注意してください。これは、**DetectedFace** オブジェクトのリストです。 この参照では、アプリが実際の顔データを保存したり呼び出したりします。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_end)] -->

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_end)] -->

### <a name="try-the-app"></a>アプリを試す

アプリをテストするために、メニューにある **[開始]** をクリックします。 アプリ ウィンドウが開いたら、左下隅の **[Browse]** をクリックします。 **[ファイルを開く]** ダイアログが表示されます。 ファイルシステムから画像を選択し、それがウィンドウに表示されることを確認します。 その後、アプリを閉じて次の手順に進みます。

![未変更の顔の画像を表示するスクリーンショット](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>画像をアップロードして顔を検出する

このアプリでは、**FaceClient.Face.DetectWithStreamAsync** メソッドを呼び出して顔を検出します。このメソッドは、ローカルの画像をアップロードするために [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API をラップします。

**MainWindow** クラスで **FacePhoto_MouseMove** メソッドの下に次のメソッドを挿入します。 このメソッドにより、取得する顔の属性の一覧が定義され、送信した画像ファイルが **Stream** に読み込まれます。 その後、これらのオブジェクトの両方が **DetectWithStreamAsync** メソッド呼び出しに渡されます。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_uploaddetect)]

## <a name="draw-rectangles-around-faces"></a>顔の周囲に四角形を描画する

次に、画像内で検出されたそれぞれの顔の周囲に四角形を描画するコードを追加します。 **MainWindow** クラスで **BrowseButton_Click** メソッドの末尾にある `FacePhoto.Source = bitmapSource` 行の後に次のコードを挿入します。 このコードにより、**UploadAndDetectFaces** の呼び出しから検出された顔の一覧が設定されます。 その後、それぞれの顔の周囲に四角形が描画され、変更された画像がメイン ウィンドウに表示されます。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_mid)]

## <a name="describe-the-faces"></a>顔を説明する

**MainWindow** クラスで **UploadAndDetectFaces** メソッドの下に次のメソッドを追加します。 このメソッドにより、取得した顔の属性が、顔を説明する文字列に変換されます。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_facedesc)]

## <a name="display-the-face-description"></a>顔の説明を表示する

**FacePhoto_MouseMove** メソッドに次のコードを追加します。 検出された顔の四角形の上にカーソルを移動すると、このイベント ハンドラーによって顔を説明する文字列が `faceDescriptionStatusBar` に表示されます。

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_mid)]

## <a name="run-the-app"></a>アプリの実行

このアプリケーションを実行し、顔が含まれる画像を参照します。 Face サービスが応答するまで数秒お待ちください。 画像内のそれぞれの顔に赤色の四角形が表示されます。 顔の四角形の上にマウスを移動すると、その顔の説明がステータス バーに表示されます。

![顔を検出して四角形のフレームに収めた画像のスクリーンショット](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Face サービス .NET SDK を使用するための基本的なプロセスを学習し、画像内の顔を検出してフレームに収めるアプリケーションを作成しました。 次は、顔検出の詳細について学習してください。

> [!div class="nextstepaction"]
> [画像内の顔を検出する方法](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
