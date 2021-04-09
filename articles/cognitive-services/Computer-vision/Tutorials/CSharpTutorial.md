---
title: サンプル:画像処理アプリの探索 (C#)
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services で Computer Vision API を使用する基本的な Windows アプリについて説明します。 OCR を実行し、サムネイルを作成して、イメージ内の視覚的な特徴を操作します。
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 0a1878268beb135151a23b87d03839f3a51f4a96
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102432589"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>サンプル:画像処理アプリの探索 (C#)

Computer Vision を使用して、光学文字認識 (OCR) を実行し、スマートにトリミングされたサムネイルを作成するほか、画像内の視覚的な特徴 (顔など) を検出、分類、タグ付け、および記述する、基本的な Windows アプリケーションについて詳しく説明します。 下の例では、イメージ URL またはローカルに保存されたファイルを送信できます。 このオープン ソースの例をテンプレートとして使用し、Computer Vision API と、.NET Framework の一部である Windows Presentation Foundation (WPF) を使用して、Windows 用の独自アプリをビルドできます。

> [!div class="checklist"]
> * GitHub からサンプル アプリを取得する
> * Visual Studio でサンプル アプリを開いてビルドする
> * サンプル アプリを実行し、対話してさまざまなシナリオを実行する
> * サンプル アプリに含まれるさまざまなシナリオを調べる

## <a name="prerequisites"></a>前提条件

サンプル アプリを調べる前に、次の前提条件を満たしていることを確認します。

* [Visual Studio 2015 ](https://visualstudio.microsoft.com/downloads/)以降が必要です。
* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision リソースを作成"  target="_blank">Computer Vision リソースを作成</a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 対象のアプリケーションを Computer Vision サービスに接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="get-the-sample-app"></a>サンプル アプリを入手する

Computer Vision のサンプル アプリは、GitHub の `Microsoft/Cognitive-Vision-Windows` リポジトリから入手できます。 このリポジトリには、Git サブモジュールとして `Microsoft/Cognitive-Common-Windows` リポジトリも含まれています。 コマンド ラインから `git clone --recurse-submodules` コマンドを使用するか、または GitHub Desktop を使用し、サブモジュールも含めて、このリポジトリを再帰的に複製できます。

たとえば、Computer Vision サンプル アプリのリポジトリをコマンド プロンプトから再帰的に複製するには、次のコマンドを実行します。

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> このリポジトリを ZIP ファイルとしてダウンロードしないでください。 リポジトリを ZIP としてダウンロードすると、サブモジュールが含まれません。

### <a name="get-optional-sample-images"></a>オプションのサンプル画像を入手する

必要に応じて、GitHub の `Microsoft/Cognitive-Face-Windows` リポジトリから入手できる [Face](../../Face/Overview.md) サンプル アプリに含まれるサンプル画像を使用することができます。 そのサンプル アプリの `/Data` フォルダーには、人の複数の画像が含まれます。 このリポジトリも、Computer Vision サンプル アプリで説明した方法を使用して、再帰的に複製できます。

たとえば、Face サンプル アプリのリポジトリをコマンド プロンプトから再帰的に複製するには、次のコマンドを実行します。

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>Visual Studio でサンプル アプリを開いてビルドする

サンプル アプリを実行して調べるには、その前にまず、サンプル アプリをビルドして、Visual Studio が依存関係を解決できるようにする必要があります。 サンプル アプリを開いてビルドするには、次の手順のようにします。

1. Visual Studio ソリューション ファイル `/Sample-WPF/VisionAPI-WPF-Samples.sln` を Visual Studio で開きます。
1. Visual Studio ソリューションに 2 つのプロジェクトが含まれていることを確認します。  

   * SampleUserControlLibrary
   * VisionAPI-WPF-Samples  

   SampleUserControlLibrary プロジェクトを使用できない場合は、`Microsoft/Cognitive-Vision-Windows` リポジトリを再帰的に複製したことを確認します。
1. Visual Studio で、Ctrl + Shift + B キーを押すか、リボン メニューから **[ビルド]** を選択し、 **[ソリューションのビルド]** を選択して、ソリューションをビルドします。

## <a name="run-and-interact-with-the-sample-app"></a>サンプル アプリを実行して操作する

サンプル アプリを実行し、サムネイルの生成や画像のタグ付けなどのさまざまなタスクを実行するときに、サンプル アプリがユーザーや Computer Vision クライアント ライブラリとどのように相互作用するかを確認できます。 サンプル アプリを実行して操作するには、次のようにします。

1. ビルドが完了した後、**F5** キーを押すか、リボン メニューから **[デバッグ]** を選択し、 **[デバッグの開始]** を選択してサンプル アプリを実行します。
1. サンプル アプリが表示されたら、ナビゲーション ウィンドウの **[Subscription Key Management]\(サブスクリプション キーの管理\)** を選択して、[Subscription Key Management]\(サブスクリプション キーの管理\) ページを表示します。
   ![[Subscription Key Management]\(サブスクリプション キーの管理\) ページ](../Images/Vision_UI_Subscription.PNG)  
1. **[Subscription Key]\(サブスクリプション キー\)** にサブスクリプション キーを入力します。
1. **[エンドポイント]** にエンドポイント URL を入力します。  
   [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. 次にサンプル アプリを実行したときにサブスクリプション キーとエンドポイント URL を入力したくない場合は、 **[Save Setting]\(設定の保存\)** を選択して、サブスクリプション キーとエンドポイント URL をお使いのコンピューターに保存します。 以前に保存したサブスクリプション キーとエンドポイント URL を削除する場合は、 **[Delete Setting]\(設定の削除\)** を選択します。

   > [!NOTE]
   > サンプル アプリは、分離ストレージと `System.IO.IsolatedStorage` を使用して、サブスクリプション キーとエンドポイント URL を格納します。

1. ナビゲーション ウィンドウの **[Select a scenario]\(シナリオの選択\)** で、サンプル アプリに現在含まれるシナリオのいずれかを選択します。  

   | シナリオ | 説明 |
   |----------|-------------|
   |Analyze Image (画像を分析する) | [Analyze Image (画像の分析)](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) 操作を使用して、ローカルまたはリモートの画像を分析します。 分析のための視覚的な特徴と言語を選択して、画像と結果の両方を表示できます。  |
   |Analyze Image with Domain Model (ドメイン モデルで画像を分析する) | [List Domain Specific Models (ドメイン固有モデルの一覧表示)](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) 操作を使用して選択できるドメイン モデルの一覧を表示し、[Recognize Domain Specific Content (ドメイン固有コンテンツの認識)](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) 操作を使用して、選択したドメイン モデルを使用してローカルまたはリモートの画像を分析します。 分析用の言語を選択することもできます。 |
   |Describe Image (画像を説明する) | [Describe Image (画像の説明)](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) 操作を使用して、ローカルまたはリモートの画像の人が判読できる説明を作成します。 説明用の言語を選択することもできます。 |
   |Generate Tags (タグを生成する) | [Tag Image (画像のタグ付け)](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) 操作を使用して、ローカルまたはリモートの画像の視覚的な特徴にタグを付けます。 タグに使用する言語を選択することもできます。 |
   |Recognize Text (OCR) (テキストを認識する (OCR)) | [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) 操作を使用して、画像から印刷されたテキストを認識して抽出します。 使用する言語を選択するか、Computer Vision に言語を自動検出させることができます。 |
   |Recognize Text V2 (English) (テキストを認識する V2 (英語)) | [Recognize Text (テキストの認識)](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) 操作と [Get Recognize Text Operation Result (テキストの認識操作の結果の取得)](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) 操作を使用して、画像から印刷テキストまたは手書きテキストを非同期的に認識して抽出します。 |
   |Get Thumbnail (サムネイルを取得する) | [Get Thumbnail (サムネイルの取得)](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) 操作を使用して、ローカルまたはリモートの画像のサムネイルを生成します。 |

   次のスクリーンショットは、Analyze Image (画像を分析する) シナリオでサンプル画像を分析した後に表示されるページです。
   ![[画像分析] ページのスクリーン ショット](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>サンプル アプリを調べる

Computer Vision サンプル アプリの Visual Studio ソリューションには、2 つのプロジェクトが含まれています。

* SampleUserControlLibrary  
  SampleUserControlLibrary プロジェクトでは、複数の Cognitive Services サンプルによって共有される機能が提供されます。 プロジェクトには次のものが含まれます。
  * SampleScenarios  
    タイトル バー、ナビゲーション ウィンドウで、コンテンツ ウィンドウなど、サンプルに対して標準化されたプレゼンテーションを提供するユーザー コントロールです。 Computer Vision のサンプル アプリは、MainWindow.xaml ウィンドウでこのコントロールを使用して、シナリオ ページを表示し、サブスクリプション キーやエンドポイント URL などのシナリオ間で共有される情報にアクセスします。
  * SubscriptionKeyPage  
    サンプル アプリのサブスクリプション キーとエンドポイント URL を入力するための標準化されたレイアウトを提供するページです。 Computer Vision のサンプル アプリは、このページを使用して、シナリオのページで使用されるサブスクリプション キーとエンドポイント URL を管理します。
  * VideoResultControl  
    動画の情報に対する標準化されたプレゼンテーションを提供するユーザー コントロールです。 Computer Vision のサンプル アプリでは、このコントロールは使用されません。
* VisionAPI-WPF-Samples  
  Computer Vision のサンプル アプリのメイン プロジェクトであり、Computer Vision の興味深い機能がすべて含まれます。 プロジェクトには次のものが含まれます。
  * AnalyzeInDomainPage.xaml  
    Analyze Image with Domain Model (ドメイン モデルで画像を分析する) シナリオのシナリオ ページです。
  * AnalyzeImage.xaml  
    Analyze Image (画像を分析する) シナリオのシナリオ ページです。
  * DescribePage.xaml  
    Describe Image (画像を説明する) シナリオのシナリオ ページです。
  * ImageScenarioPage.cs  
    ImageScenarioPage クラスです。サンプル アプリのすべてのシナリオ ページはこのクラスから派生されます。 このクラスは、すべてのシナリオ ページで共有される、資格情報の提供や出力の書式設定などの機能を管理します。
  * MainWindow.xaml  
    サンプル アプリのメイン ウィンドウです。SampleScenarios コントロールを使用して、SubscriptionKeyPage とシナリオ ページを提供します。
  * OCRPage.xaml  
    Recognize Text (OCR) (テキストを認識する (OCR)) シナリオのシナリオ ページです。
  * RecognizeLanguage.cs  
    RecognizeLanguage クラスです。サンプル アプリのさまざまなメソッドでサポートされる言語に関する情報を提供します。
  * TagsPage.xaml  
    Generate Tags (タグを生成する) シナリオのシナリオ ページです。
  * TextRecognitionPage.xaml  
    Recognize Text V2 (English) (テキストを認識する V2 (英語)) シナリオのシナリオ ページです。
  * ThumbnailPage.xaml  
    Get Thumbnail (サムネイルを取得する) シナリオのシナリオ ページです。

### <a name="explore-the-sample-code"></a>サンプル コードの検証

サンプル アプリを調べやすいように、サンプル コードの主要な部分は、`KEY SAMPLE CODE STARTS HERE` で始まり `KEY SAMPLE CODE ENDS HERE` で終わるコメント ブロックで囲まれています。 サンプル コードのこれらの主要部分には、Computer Vision API クライアント ライブラリを使用してさまざまなタスクを実行する方法の学習に最も関係のあるコードが含まれます。 Visual Studio で `KEY SAMPLE CODE STARTS HERE` を検索することにより、Computer Vision サンプル アプリのコードの最も関連のあるセクション間を移動できます。 

たとえば、以下で示されていて AnalyzePage.xaml に含まれる `UploadAndAnalyzeImageAsync` メソッドでは、クライアント ライブラリを使用し、`ComputerVisionClient.AnalyzeImageInStreamAsync` メソッドを呼び出すことによって、ローカル画像を分析する方法が示されています。

```csharp
private async Task<ImageAnalysis> UploadAndAnalyzeImageAsync(string imageFilePath)
{
    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE STARTS HERE
    // -----------------------------------------------------------------------

    //
    // Create Cognitive Services Vision API Service client.
    //
    using (var client = new ComputerVisionClient(Credentials) { Endpoint = Endpoint })
    {
        Log("ComputerVisionClient is created");

        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features.
            //
            Log("Calling ComputerVisionClient.AnalyzeImageInStreamAsync()...");
            VisualFeatureTypes[] visualFeatures = GetSelectedVisualFeatures();
            string language = (_language.SelectedItem as RecognizeLanguage).ShortCode;
            ImageAnalysis analysisResult = await client.AnalyzeImageInStreamAsync(imageFileStream, visualFeatures, null, language);
            return analysisResult;
        }
    }

    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE ENDS HERE
    // -----------------------------------------------------------------------
}
```

### <a name="explore-the-client-library"></a>クライアント ライブラリを調べる

このサンプル アプリでは、Azure Cognitive Services の Computer Vision API 用の C# シン クライアント ラッパーである Computer Vision API クライアント ライブラリが使用されています。 このクライアント ライブラリは、NuGet で [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) パッケージから入手できます。 Visual Studio アプリケーションをビルドするときは、その対応する NuGet パッケージからクライアント ライブラリを取得しました。 `Microsoft/Cognitive-Vision-Windows` リポジトリの `/ClientLibrary` フォルダーで、クライアント ライブラリのソース コードを表示することもできます。

クライアント ライブラリの機能の中心になるのは `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` 名前空間内の `ComputerVisionClient` クラスですが、Computer Vision と対話するときに `ComputerVisionClient` クラスによって使用されるモデルは、`Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models` 名前空間内で見つかります。 サンプル アプリに含まれるさまざまな XAML シナリオ ページでは、これらの名前空間に対して次の `using` ディレクティブが使われています。

```csharp
// -----------------------------------------------------------------------
// KEY SAMPLE CODE STARTS HERE
// Use the following namespace for ComputerVisionClient.
// -----------------------------------------------------------------------
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
// -----------------------------------------------------------------------
// KEY SAMPLE CODE ENDS HERE
// -----------------------------------------------------------------------
```

Computer Vision のサンプル アプリに含まれるシナリオを調べると、`ComputerVisionClient` クラスに含まれているさまざまなメソッドのことをさらに詳しく学習できます。

## <a name="explore-the-analyze-image-scenario"></a>Analyze Image (画像を分析する) シナリオを調べる

このシナリオは、AnalyzePage.xaml ページによって管理されます。 分析のための視覚的な特徴と言語を選択して、画像と結果の両方を表示できます。 シナリオ ページでは、画像のソースに応じて、次のメソッドのいずれかを使用してこれが行われます。

* UploadAndAnalyzeImageAsync  
  このメソッドは、ローカルの画像に対して使用されます。画像は、`Stream` としてエンコードされていて、`ComputerVisionClient.AnalyzeImageInStreamAsync` メソッドを呼び出すことによって Computer Vision に送信される必要があります。
* AnalyzeUrlAsync  
  このメソッドは、リモートの画像に対して使用されます。画像の URL は、`ComputerVisionClient.AnalyzeImageAsync` メソッドを呼び出すことによって Computer Vision に送信されます。

`UploadAndAnalyzeImageAsync` メソッドでは、指定されたサブスクリプション キーとエンドポイント URL を使用して、新しい `ComputerVisionClient` インスタンスが作成されます。 サンプル アプリはローカル画像を分析しているため、Computer Vision にその画像の内容を送信する必要があります。 アプリは、`imageFilePath` で指定されたローカル ファイルを `Stream` として読み取るために開いた後、シナリオ ページで選択された視覚的な特徴と言語を取得します。 `ComputerVisionClient.AnalyzeImageInStreamAsync` メソッドを呼び出し、ファイル、視覚的な特徴、言語に対する `Stream` を渡した後、結果を `ImageAnalysis` インスタンスとして返します。 `ImageScenarioPage` クラスから継承されたメソッドは、返された結果をシナリオ ページに表示します。

`AnalyzeUrlAsync` メソッドでは、指定されたサブスクリプション キーとエンドポイント URL を使用して、新しい `ComputerVisionClient` インスタンスが作成されます。 シナリオ ページで選択された視覚的な特徴と言語を取得します。 `ComputerVisionClient.AnalyzeImageInStreamAsync` メソッドを呼び出し、画像の URL、視覚的な特徴、言語を渡した後、結果を `ImageAnalysis` インスタンスとして返します。 `ImageScenarioPage` クラスから継承されたメソッドは、返された結果をシナリオ ページに表示します。

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>Analyze Image with Domain Model (ドメイン モデルで画像を分析する) シナリオを調べる

このシナリオは、AnalyzeInDomainPage.xaml ページによって管理されます。 `celebrities` や `landmarks` などのドメイン モデルと、画像のドメイン固有の分析を実行するための言語を選択することができ、画像と結果の両方が表示されます。 シナリオ ページでは、画像のソースに応じて、次のメソッドが使用されます。

* GetAvailableDomainModelsAsync  
  このメソッドは、Computer Vision から利用可能なドメイン モデルの一覧を取得し、`ComputerVisionClient.ListModelsAsync` メソッドを使用してページの `_domainModelComboBox` コンボ ボックス コントロールを設定します。
* UploadAndAnalyzeInDomainImageAsync  
  このメソッドは、ローカルの画像に対して使用されます。画像は、`Stream` としてエンコードされていて、`ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` メソッドを呼び出すことによって Computer Vision に送信される必要があります。
* AnalyzeInDomainUrlAsync  
  このメソッドは、リモートの画像に対して使用されます。画像の URL は、`ComputerVisionClient.AnalyzeImageByDomainAsync` メソッドを呼び出すことによって Computer Vision に送信されます。

`UploadAndAnalyzeInDomainImageAsync` メソッドでは、指定されたサブスクリプション キーとエンドポイント URL を使用して、新しい `ComputerVisionClient` インスタンスが作成されます。 サンプル アプリはローカル画像を分析しているため、Computer Vision にその画像の内容を送信する必要があります。 アプリは、`imageFilePath` で指定されたローカル ファイルを `Stream` として読み取るために開いた後、シナリオ ページで選択された言語を取得します。 `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` メソッドを呼び出し、ファイル、ドメイン モデルの名前、言語に対する `Stream` を渡した後、結果を `DomainModelResults` インスタンスとして返します。 `ImageScenarioPage` クラスから継承されたメソッドは、返された結果をシナリオ ページに表示します。

`AnalyzeInDomainUrlAsync` メソッドでは、指定されたサブスクリプション キーとエンドポイント URL を使用して、新しい `ComputerVisionClient` インスタンスが作成されます。 シナリオ ページで選択された言語を取得します。 `ComputerVisionClient.AnalyzeImageByDomainAsync` メソッドを呼び出し、画像の URL、視覚的な特徴、言語を渡した後、結果を `DomainModelResults` インスタンスとして返します。 `ImageScenarioPage` クラスから継承されたメソッドは、返された結果をシナリオ ページに表示します。

## <a name="explore-the-describe-image-scenario"></a>Describe Image (画像を説明する) シナリオを調べる

このシナリオは、DescribePage.xaml ページによって管理されます。 言語を選択して人間が判読できる画像の説明を作成し、画像と結果の両方を表示できます。 シナリオ ページでは、画像のソースに応じて、次のメソッドが使用されます。

* UploadAndDescribeImageAsync  
  このメソッドは、ローカルの画像に対して使用されます。画像は、`Stream` としてエンコードされていて、`ComputerVisionClient.DescribeImageInStreamAsync` メソッドを呼び出すことによって Computer Vision に送信される必要があります。
* DescribeUrlAsync  
  このメソッドは、リモートの画像に対して使用されます。画像の URL は、`ComputerVisionClient.DescribeImageAsync` メソッドを呼び出すことによって Computer Vision に送信されます。

`UploadAndDescribeImageAsync` メソッドでは、指定されたサブスクリプション キーとエンドポイント URL を使用して、新しい `ComputerVisionClient` インスタンスが作成されます。 サンプル アプリはローカル画像を分析しているため、Computer Vision にその画像の内容を送信する必要があります。 アプリは、`imageFilePath` で指定されたローカル ファイルを `Stream` として読み取るために開いた後、シナリオ ページで選択された言語を取得します。 `ComputerVisionClient.DescribeImageInStreamAsync` メソッドを呼び出し、ファイル、候補の最大数 (この場合は 3)、言語に対する `Stream` を渡した後、結果を `ImageDescription` インスタンスとして返します。 `ImageScenarioPage` クラスから継承されたメソッドは、返された結果をシナリオ ページに表示します。

`DescribeUrlAsync` メソッドでは、指定されたサブスクリプション キーとエンドポイント URL を使用して、新しい `ComputerVisionClient` インスタンスが作成されます。 シナリオ ページで選択された言語を取得します。 `ComputerVisionClient.DescribeImageAsync` メソッドを呼び出し、画像の URL、候補の最大数 (この場合は 3)、言語を渡した後、結果を `ImageDescription` インスタンスとして返します。 `ImageScenarioPage` クラスから継承されたメソッドは、返された結果をシナリオ ページに表示します。

## <a name="explore-the-generate-tags-scenario"></a>Generate Tags (タグを生成する) シナリオを調べる

このシナリオは、TagsPage.xaml ページによって管理されます。 画像の視覚的な特徴にタグを付けるための言語を選択して、画像と結果の両方を表示できます。 シナリオ ページでは、画像のソースに応じて、次のメソッドが使用されます。

* UploadAndGetTagsForImageAsync  
  このメソッドは、ローカルの画像に対して使用されます。画像は、`Stream` としてエンコードされていて、`ComputerVisionClient.TagImageInStreamAsync` メソッドを呼び出すことによって Computer Vision に送信される必要があります。
* GenerateTagsForUrlAsync  
  このメソッドは、リモートの画像に対して使用されます。画像の URL は、`ComputerVisionClient.TagImageAsync` メソッドを呼び出すことによって Computer Vision に送信されます。

`UploadAndGetTagsForImageAsync` メソッドでは、指定されたサブスクリプション キーとエンドポイント URL を使用して、新しい `ComputerVisionClient` インスタンスが作成されます。 サンプル アプリはローカル画像を分析しているため、Computer Vision にその画像の内容を送信する必要があります。 アプリは、`imageFilePath` で指定されたローカル ファイルを `Stream` として読み取るために開いた後、シナリオ ページで選択された言語を取得します。 `ComputerVisionClient.TagImageInStreamAsync` メソッドを呼び出し、ファイルと言語に対する `Stream` を渡した後、結果を `TagResult` インスタンスとして返します。 `ImageScenarioPage` クラスから継承されたメソッドは、返された結果をシナリオ ページに表示します。

`GenerateTagsForUrlAsync` メソッドでは、指定されたサブスクリプション キーとエンドポイント URL を使用して、新しい `ComputerVisionClient` インスタンスが作成されます。 シナリオ ページで選択された言語を取得します。 `ComputerVisionClient.TagImageAsync` メソッドを呼び出し、画像の URL と言語を渡した後、結果を `TagResult` インスタンスとして返します。 `ImageScenarioPage` クラスから継承されたメソッドは、返された結果をシナリオ ページに表示します。

## <a name="explore-the-recognize-text-ocr-scenario"></a>Recognize Text (OCR) (テキストを認識する (OCR)) シナリオを調べる

このシナリオは、OCRPage.xaml ページによって管理されます。 画像から印刷されたテキストを認識して抽出するための言語を選択して、画像と結果の両方を表示できます。 シナリオ ページでは、画像のソースに応じて、次のメソッドが使用されます。

* UploadAndRecognizeImageAsync  
  このメソッドは、ローカルの画像に対して使用されます。画像は、`Stream` としてエンコードされていて、`ComputerVisionClient.RecognizePrintedTextInStreamAsync` メソッドを呼び出すことによって Computer Vision に送信される必要があります。
* RecognizeUrlAsync  
  このメソッドは、リモートの画像に対して使用されます。画像の URL は、`ComputerVisionClient.RecognizePrintedTextAsync` メソッドを呼び出すことによって Computer Vision に送信されます。

`UploadAndRecognizeImageAsync` メソッドでは、指定されたサブスクリプション キーとエンドポイント URL を使用して、新しい `ComputerVisionClient` インスタンスが作成されます。 サンプル アプリはローカル画像を分析しているため、Computer Vision にその画像の内容を送信する必要があります。 アプリは、`imageFilePath` で指定されたローカル ファイルを `Stream` として読み取るために開いた後、シナリオ ページで選択された言語を取得します。 `ComputerVisionClient.RecognizePrintedTextInStreamAsync` メソッドを呼び出し、向きが検出されていないことを示して、ファイルと言語に対する `Stream` を渡した後、結果を `OcrResult` インスタンスとして返します。 `ImageScenarioPage` クラスから継承されたメソッドは、返された結果をシナリオ ページに表示します。

`RecognizeUrlAsync` メソッドでは、指定されたサブスクリプション キーとエンドポイント URL を使用して、新しい `ComputerVisionClient` インスタンスが作成されます。 シナリオ ページで選択された言語を取得します。 `ComputerVisionClient.RecognizePrintedTextAsync` メソッドを呼び出し、向きが検出されていないことを示して、画像の URL と言語を渡した後、結果を `OcrResult` インスタンスとして返します。 `ImageScenarioPage` クラスから継承されたメソッドは、返された結果をシナリオ ページに表示します。

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Recognize Text V2 (English) (テキストを認識する V2 (英語)) シナリオを調べる

このシナリオは、TextRecognitionPage.xaml ページによって管理されます。 画像から印刷されたテキストまたは手書きのテキストを非同期的に認識して抽出するための認識モードと言語を選択して、画像と結果の両方を表示できます。 シナリオ ページでは、画像のソースに応じて、次のメソッドが使用されます。

* UploadAndRecognizeImageAsync  
  このメソッドは、ローカルの画像に対して使用されます。画像は、`Stream` としてエンコードされていて、`RecognizeAsync` メソッドを呼び出し、`ComputerVisionClient.RecognizeTextInStreamAsync` メソッドにパラメーター化された委任を渡すことによって、Computer Vision に送信される必要があります。
* RecognizeUrlAsync  
  このメソッドは、リモートの画像に対して使用されます。画像の URL は、`RecognizeAsync` メソッドを呼び出し、`ComputerVisionClient.RecognizeTextAsync` メソッドにパラメーター化された委任を渡すことによって、Computer Vision に送信されます。
* RecognizeAsync このメソッドでは、`UploadAndRecognizeImageAsync` メソッドと `RecognizeUrlAsync` メソッドの両方に対する非同期呼び出し、および `ComputerVisionClient.GetTextOperationResultAsync` メソッドを呼び出すことによる結果のポーリングが処理されます。

Computer Vision のサンプル アプリに含まれる他のシナリオとは違って、このシナリオは非同期であり、処理を開始するために呼び出すメソッドと、その処理の状態を確認して結果を返すために呼び出すメソッドが異なります。 このシナリオの論理的な流れは、他のシナリオとやや異なります。

`UploadAndRecognizeImageAsync` メソッドは、`Stream` として読み取るためのローカル ファイル (`imageFilePath` で指定されています) を開き、`RecognizeAsync` メソッドを呼び出して次のものを渡します。

* `GetHeadersAsyncFunc` では、`ComputerVisionClient.RecognizeTextInStreamAsync`メソッドのパラメーター化された非同期委任のラムダ式と、パラメーターとしてのファイルおよび認識モードに対する `Stream`。
* `GetOperationUrlFunc` では、`Operation-Location` 応答ヘッダー値を取得するための委任に対するラムダ式。

`RecognizeUrlAsync` メソッドは、`RecognizeAsync` メソッドを呼び出して、次のものを渡します。

* `GetHeadersAsyncFunc` では、`ComputerVisionClient.RecognizeTextAsync`メソッドのパラメーター化された非同期委任のラムダ式と、パラメーターとしてのリモート画像の URL および認識モード。
* `GetOperationUrlFunc` では、`Operation-Location` 応答ヘッダー値を取得するための委任に対するラムダ式。

`RecognizeAsync` メソッドが完了すると、`UploadAndRecognizeImageAsync` メソッドと `RecognizeUrlAsync` メソッドの両方が、結果を `TextOperationResult` のインスタンスとして返します。 `ImageScenarioPage` クラスから継承されたメソッドは、返された結果をシナリオ ページに表示します。

`RecognizeAsync` メソッドは、`GetHeadersAsyncFunc` で渡された `ComputerVisionClient.RecognizeTextInStreamAsync` メソッドまたは `ComputerVisionClient.RecognizeTextAsync` メソッドのいずれかに対するパラメーター化された委任を呼び出して、応答を待ちます。 このメソッドは、その後、`GetOperationUrlFunc` で渡された委任を呼び出して、応答から `Operation-Location` 応答ヘッダーの値を取得します。 この値は、Computer Vision から `GetHeadersAsyncFunc` で渡されたメソッドの結果を取得するために使用される URL です。

その後、`RecognizeAsync` メソッドは `ComputerVisionClient.GetTextOperationResultAsync` メソッドを呼び出し、`Operation-Location` 応答ヘッダーから取得した URL を渡して、`GetHeadersAsyncFunc` で渡されたメソッドの状態と結果を取得します。 状態においてメソッドの完了、成功、または失敗が示されていない場合、`RecognizeAsync` メソッドは、`ComputerVisionClient.GetTextOperationResultAsync` をさらに 3 回呼び出します。呼び出しの間には、3 秒間待機します。 `RecognizeAsync` メソッドは、呼び出し元のメソッドに結果を返します。

## <a name="explore-the-get-thumbnail-scenario"></a>Get Thumbnail (サムネイルを取得する) シナリオを調べる

このシナリオは、ThumbnailPage.xaml ページによって管理されます。 スマート トリミングを使用するかどうかを指示し、必要な高さと幅を指定して、画像からサムネイルを生成し、画像と結果の両方を表示できます。 シナリオ ページでは、画像のソースに応じて、次のメソッドが使用されます。

* UploadAndThumbnailImageAsync  
  このメソッドは、ローカルの画像に対して使用されます。画像は、`Stream` としてエンコードされていて、`ComputerVisionClient.GenerateThumbnailInStreamAsync` メソッドを呼び出すことによって Computer Vision に送信される必要があります。
* ThumbnailUrlAsync  
  このメソッドは、リモートの画像に対して使用されます。画像の URL は、`ComputerVisionClient.GenerateThumbnailAsync` メソッドを呼び出すことによって Computer Vision に送信されます。

`UploadAndThumbnailImageAsync` メソッドでは、指定されたサブスクリプション キーとエンドポイント URL を使用して、新しい `ComputerVisionClient` インスタンスが作成されます。 サンプル アプリはローカル画像を分析しているため、Computer Vision にその画像の内容を送信する必要があります。 `Stream` として読み取るために、`imageFilePath` で指定されたローカル ファイルを開きます。 `ComputerVisionClient.GenerateThumbnailInStreamAsync` メソッドを呼び出して、幅、高さ、ファイルに対する `Stream`、およびスマート トリミングを使用するかどうかを渡し、結果を `Stream` として返します。 `ImageScenarioPage` クラスから継承されたメソッドは、返された結果をシナリオ ページに表示します。

`RecognizeUrlAsync` メソッドでは、指定されたサブスクリプション キーとエンドポイント URL を使用して、新しい `ComputerVisionClient` インスタンスが作成されます。 `ComputerVisionClient.GenerateThumbnailAsync` メソッドを呼び出して、幅、高さ、画像の URL、およびスマート トリミングを使用するかどうかを渡し、結果を `Stream` として返します。 `ImageScenarioPage` クラスから継承されたメソッドは、返された結果をシナリオ ページに表示します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったときは、`Microsoft/Cognitive-Vision-Windows` リポジトリを複製したフォルダーを削除します。 サンプル画像を使用した場合は、`Microsoft/Cognitive-Face-Windows` リポジトリを複製したフォルダーも削除します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Face サービスを使ってみる](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
