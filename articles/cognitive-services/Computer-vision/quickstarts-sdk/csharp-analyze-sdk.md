---
title: クイック スタート:イメージを分析する - SDK、C#
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Computer Vision Windows C# クライアント ライブラリを使って、画像を分析します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 48c96ac55ca2192506f9190a9dd9be9c1ee0058f
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603468"
---
# <a name="quickstart-analyze-an-image-using-the-computer-vision-sdk-and-c"></a>クイック スタート:Computer Vision SDK と C# による画像の分析

このクイック スタートでは、C# 用の Computer Vision クライアント ライブラリを使ってローカル画像とリモート画像の両方を分析し、視覚的特徴を抽出します。 このガイド内のコードは、必要に応じて、GitHub の [Cognitive Services Csharp Vision](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision) リポジトリからサンプル アプリ一式としてダウンロードすることができます。

## <a name="prerequisites"></a>前提条件

* Computer Vision のサブスクリプション キー。 無料試用版のサブスクリプション キーは「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)」から取得できます。 または、[Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページの手順に従って、Computer Vision をサブスクライブし、キーを取得します。
* [Visual Studio 2015 または 2017](https://www.visualstudio.com/downloads/) の任意のエディション。
* [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) クライアント ライブラリの NuGet パッケージ。 パッケージをダウンロードする必要はありません。 インストールの手順は、以降で説明しています。

## <a name="create-and-run-the-sample-application"></a>サンプル アプリケーションを作成して実行する

このサンプルを実行するには、次の手順を実行します。

1. Visual Studio で、新しい Visual C# コンソール アプリを作成します。
1. Computer Vision クライアント ライブラリの NuGet パッケージをインストールします。
    1. メニューの **[ツール]** で **[NuGet パッケージ マネージャー]** を選択し、 **[ソリューションの NuGet パッケージの管理]** を選択します。
    1. **[参照]** タブをクリックし、 **[検索]** ボックスに「Microsoft.Azure.CognitiveServices.Vision.ComputerVision」と入力します。
    1. **[Microsoft.Azure.CognitiveServices.Vision.ComputerVision]** が表示されたら選択し、対象のプロジェクト名の横のチェック ボックスをオンにして、 **[インストール]** をクリックします。
1. *Program.cs* の内容を次のコードに置き換えます。 `AnalyzeImageAsync` メソッドと `AnalyzeImageInStreamAsync` メソッドは、それぞれリモート画像とローカル画像の[画像分析 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) をラップします。

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading.Tasks;

    namespace ImageAnalyze
    {
        class Program
        {
            // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
            private const string subscriptionKey = "<SubscriptionKey>";

            // localImagePath = @"C:\Documents\LocalImage.jpg"
            private const string localImagePath = @"<LocalImage>";

            private const string remoteImageUrl =
                "https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg";

            // Specify the features to return
            private static readonly List<VisualFeatureTypes> features =
                new List<VisualFeatureTypes>()
            {
                VisualFeatureTypes.Categories, VisualFeatureTypes.Description,
                VisualFeatureTypes.Faces, VisualFeatureTypes.ImageType,
                VisualFeatureTypes.Tags
            };

            static void Main(string[] args)
            {
                ComputerVisionClient computerVision = new ComputerVisionClient(
                    new ApiKeyServiceClientCredentials(subscriptionKey),
                    new System.Net.Http.DelegatingHandler[] { });

                // You must use the same region as you used to get your subscription
                // keys. For example, if you got your subscription keys from westus,
                // replace "westcentralus" with "westus".
                //
                // Free trial subscription keys are generated in the "westus"
                // region. If you use a free trial subscription key, you shouldn't
                // need to change the region.

                // Specify the Azure region
                computerVision.Endpoint = "https://westcentralus.api.cognitive.microsoft.com";

                Console.WriteLine("Images being analyzed ...");
                var t1 = AnalyzeRemoteAsync(computerVision, remoteImageUrl);
                var t2 = AnalyzeLocalAsync(computerVision, localImagePath);

                Task.WhenAll(t1, t2).Wait(5000);
                Console.WriteLine("Press ENTER to exit");
                Console.ReadLine();
            }

            // Analyze a remote image
            private static async Task AnalyzeRemoteAsync(
                ComputerVisionClient computerVision, string imageUrl)
            {
                if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
                {
                    Console.WriteLine(
                        "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                    return;
                }

                ImageAnalysis analysis =
                    await computerVision.AnalyzeImageAsync(imageUrl, features);
                DisplayResults(analysis, imageUrl);
            }

            // Analyze a local image
            private static async Task AnalyzeLocalAsync(
                ComputerVisionClient computerVision, string imagePath)
            {
                if (!File.Exists(imagePath))
                {
                    Console.WriteLine(
                        "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                    return;
                }

                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    ImageAnalysis analysis = await computerVision.AnalyzeImageInStreamAsync(
                        imageStream, features);
                    DisplayResults(analysis, imagePath);
                }
            }

            // Display the most relevant caption for the image
            private static void DisplayResults(ImageAnalysis analysis, string imageUri)
            {
                Console.WriteLine(imageUri);
                if (analysis.Description.Captions.Count != 0)
                {
                    Console.WriteLine(analysis.Description.Captions[0].Text + "\n");
                }
                else
                {
                    Console.WriteLine("No description generated.");
                }
            }
        }
    }
    ```

1. `<Subscription Key>` を、有効なサブスクリプション キーに置き換えます。
1. 必要に応じて、`computerVision.Endpoint` をサブスクリプション キーに関連付けられている Azure リージョンに変更します。
1. `<LocalImage>` を、ローカル画像のパスとファイル名に置き換えます。
1. 必要に応じて、`remoteImageUrl` を別の画像 URL に設定します。
1. プログラムを実行します。

## <a name="examine-the-response"></a>結果の確認

成功した場合、各画像について最も関連性の高いキャプションが応答に表示されます。 `DisplayResults` メソッドに変更を加えることで、別の画像データを出力することができます。 詳細については、[AnalyzeLocalAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions.analyzeimageinstreamasync?view=azure-dotnet) メソッドを参照してください。

[API のクイック スタートの C# を使ったローカル画像の分析](../QuickStarts/CSharp-analyze.md#examine-the-response)で生の JSON 出力の例を参照してください。

```console
https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg
a large waterfall over a rocky cliff
```

## <a name="next-steps"></a>次の手順

画像の分析、著名人やランドマークの検出、サムネイルの作成、印刷されたテキストや手書きテキストの抽出に使用される Computer Vision API の詳細を確認します。

> [!div class="nextstepaction"]
> [Computer Vision API の詳細](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
