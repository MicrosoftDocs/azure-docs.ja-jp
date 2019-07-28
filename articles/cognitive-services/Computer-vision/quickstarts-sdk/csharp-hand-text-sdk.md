---
title: クイック スタート:手書きのテキストを抽出する - SDK、C#
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Computer Vision Windows C# クライアント ライブラリを使って、画像からテキストを抽出します。
services: cognitive-services
author: patrickfarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ba72fdce42a8313903a26aead4a1c1922a9bb586
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603460"
---
# <a name="quickstart-extract-handwritten-text-using-the-computer-vision-c-sdk"></a>クイック スタート:Computer Vision C# SDK を使用して手書きテキストを抽出する

このクイック スタートでは、C# 用の Computer Vision SDK を使って、画像から手書きテキストまたは印字されたテキストを抽出します。 このガイド内のコードは、必要に応じて、GitHub の [Cognitive Services Csharp Vision](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision) リポジトリからサンプル アプリ一式としてダウンロードすることができます。

## <a name="prerequisites"></a>前提条件

* Computer Vision のサブスクリプション キー。 無料試用版のキーは「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)」から取得できます。 または、[Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページの手順に従って、Computer Vision をサブスクライブし、キーを取得します。
* [Visual Studio 2015 または 2017](https://www.visualstudio.com/downloads/) の任意のエディション。
* [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) クライアント ライブラリの NuGet パッケージ。 パッケージをダウンロードする必要はありません。 インストールの手順は、以降で説明しています。

## <a name="create-and-run-the-sample-app"></a>サンプル アプリの作成と実行

このサンプルを実行するには、次の手順を実行します。

1. Visual Studio で、新しい Visual C# コンソール アプリを作成します。
1. Computer Vision クライアント ライブラリの NuGet パッケージをインストールします。
    1. メニューの **[ツール]** で **[NuGet パッケージ マネージャー]** を選択し、 **[ソリューションの NuGet パッケージの管理]** を選択します。
    1. **[参照]** タブをクリックし、 **[検索]** ボックスに「Microsoft.Azure.CognitiveServices.Vision.ComputerVision」と入力します。
    1. **[Microsoft.Azure.CognitiveServices.Vision.ComputerVision]** が表示されたら選択し、対象のプロジェクト名の横のチェック ボックスをオンにして、 **[インストール]** をクリックします。
1. `Program.cs` を以下のコードに置き換えます。 `BatchReadFileAsync` メソッドと `BatchReadFileInStreamAsync` メソッドは、それぞれリモート画像とローカル画像の[バッチ読み取り API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) をラップします。 `GetReadOperationResultAsync` メソッドは、[取得、読み取り操作結果 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) をラップします。

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

    using System;
    using System.IO;
    using System.Threading.Tasks;

    namespace ExtractText
    {
        class Program
        {
            // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
            private const string subscriptionKey = "<Subscription key>";

            // localImagePath = @"C:\Documents\LocalImage.jpg"
            private const string localImagePath = @"<LocalImage>";

            private const string remoteImageUrl =
                "https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg";

            private const int numberOfCharsInOperationId = 36;

            static void Main(string[] args)
            {
                ComputerVisionClient computerVision = new ComputerVisionClient(
                    new ApiKeyServiceClientCredentials(subscriptionKey),
                    new System.Net.Http.DelegatingHandler[] { });

                // You must use the same region as you used to get your subscription
                // keys. For example, if you got your subscription keys from westus,
                // replace "westcentralus" with "westus".
                //
                // Free trial subscription keys are generated in the westcentralus
                // region. If you use a free trial subscription key, you shouldn't
                // need to change the region.

                // Specify the Azure region
                computerVision.Endpoint = "https://westcentralus.api.cognitive.microsoft.com";

                Console.WriteLine("Images being analyzed ...");
                var t1 = ExtractRemoteTextAsync(computerVision, remoteImageUrl);
                var t2 = ExtractLocalTextAsync(computerVision, localImagePath);

                Task.WhenAll(t1, t2).Wait(5000);
                Console.WriteLine("Press ENTER to exit");
                Console.ReadLine();
            }

            // Read text from a remote image
            private static async Task ExtractRemoteTextAsync(
                ComputerVisionClient computerVision, string imageUrl)
            {
                if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
                {
                    Console.WriteLine(
                        "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                    return;
                }

                // Start the async process to read the text
                BatchReadFileHeaders textHeaders =
                    await computerVision.BatchReadFileAsync(
                        imageUrl);

                await GetTextAsync(computerVision, textHeaders.OperationLocation);
            }

            // Recognize text from a local image
            private static async Task ExtractLocalTextAsync(
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
                    // Start the async process to recognize the text
                    BatchReadFileInStreamHeaders textHeaders =
                        await computerVision.BatchReadFileInStreamAsync(
                            imageStream);

                    await GetTextAsync(computerVision, textHeaders.OperationLocation);
                }
            }

            // Retrieve the recognized text
            private static async Task GetTextAsync(
                ComputerVisionClient computerVision, string operationLocation)
            {
                // Retrieve the URI where the recognized text will be
                // stored from the Operation-Location header
                string operationId = operationLocation.Substring(
                    operationLocation.Length - numberOfCharsInOperationId);

                Console.WriteLine("\nCalling GetHandwritingRecognitionOperationResultAsync()");
                ReadOperationResult result =
                    await computerVision.GetReadOperationResultAsync(operationId);

                // Wait for the operation to complete
                int i = 0;
                int maxRetries = 10;
                while ((result.Status == TextOperationStatusCodes.Running ||
                        result.Status == TextOperationStatusCodes.NotStarted) && i++ < maxRetries)
                {
                    Console.WriteLine(
                        "Server status: {0}, waiting {1} seconds...", result.Status, i);
                    await Task.Delay(1000);

                    result = await computerVision.GetReadOperationResultAsync(operationId);
                }

                // Display the results
                Console.WriteLine();
                var recResults = result.RecognitionResults;
                foreach (TextRecognitionResult recResult in recResults)
                {
                    foreach (Line line in recResult.Lines)
                    {
                        Console.WriteLine(line.Text);
                    }
                }
                Console.WriteLine();
            }
        }
    }
    ```

1. `<Subscription Key>` を、有効なサブスクリプション キーに置き換えます。
1. 必要に応じて、`computerVision.Endpoint` をサブスクリプション キーに関連付けられている Azure リージョンに変更します。
1. `<LocalImage>` を、ローカル画像のパスとファイル名に置き換えます。
1. 必要に応じて、`remoteImageUrl` を別の画像に設定します。
1. プログラムを実行します。

## <a name="examine-the-response"></a>結果の確認

成功した場合、各画像について認識されたテキストの行が応答に出力されます。

```console
Calling GetHandwritingRecognitionOperationResultAsync()

Calling GetHandwritingRecognitionOperationResultAsync()
Server status: Running, waiting 1 seconds...
Server status: Running, waiting 1 seconds...

dog
The quick brown fox jumps over the lazy
Pack my box with five dozen liquor jugs
```

「[クイック スタート:REST と C# を使用して手書きテキストを抽出するクイック スタート](../QuickStarts/CSharp-hand-text.md#examine-the-response)で、API 呼び出しから返される生の JSON の出力例を参照してください。

## <a name="next-steps"></a>次の手順

画像の分析、著名人やランドマークの検出、サムネイルの作成、印刷されたテキストや手書きテキストの抽出に使用される Computer Vision API の詳細を確認します。

> [!div class="nextstepaction"]
> [Computer Vision API の詳細](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
