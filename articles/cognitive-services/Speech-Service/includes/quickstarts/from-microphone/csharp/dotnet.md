---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 63fc699cfd2f02a322f71324519b2f0b5b22234b
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926439"
---
## <a name="prerequisites"></a>前提条件

開始する前に、以下の操作を行います。

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech リソースを作成する <span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [開発環境を設定し、空のプロジェクトを作成する](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)
> * オーディオ キャプチャ用のマイクにアクセスできることを確認する

## <a name="open-your-project-in-visual-studio"></a>Visual Studio でプロジェクトを開きます。

最初の手順として、ご利用のプロジェクトを Visual Studio で開いていることを確認します。

1. **Visual Studio 2019** を起動します。
2. プロジェクトを読み込んで *Program.cs* を開きます。

## <a name="source-code"></a>ソース コード

*Program.cs* ファイルの内容を次の C# コードに置き換えます。

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace Speech.Recognition
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();

            Console.WriteLine("Please press any key to continue...");
            Console.ReadLine();
        }

        static async Task RecognizeSpeechAsync()
        {
            var config =
                SpeechConfig.FromSubscription(
                    "YourSubscriptionKey",
                    "YourServiceRegion");

            using var recognizer = new SpeechRecognizer(config);
            
            var result = await recognizer.RecognizeOnceAsync();
            switch (result.Reason)
            {
                case ResultReason.RecognizedSpeech:
                    Console.WriteLine($"We recognized: {result.Text}");
                    break;
                case ResultReason.NoMatch:
                    Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    break;
                case ResultReason.Canceled:
                    var cancellation = CancellationDetails.FromResult(result);
                    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");
    
                    if (cancellation.Reason == CancellationReason.Error)
                    {
                        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                        Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
                        Console.WriteLine($"CANCELED: Did you update the subscription info?");
                    }
                    break;
            }
        }
    }
}
```

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>コードの説明

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>アプリをビルドして実行する

これで、アプリをリビルドし、音声サービスを使用して音声認識機能をテストする準備ができました。

1. **コードをコンパイルする** - Visual Studio のメニュー バーで、 **[ビルド]**  >  **[ソリューションのビルド]** の順に選択します。
2. **アプリを起動する** - メニュー バーから **[デバッグ]**  >  **[デバッグの開始]** の順に選択するか、<kbd>F5</kbd> キーを押します。
3. **認識を開始する** - 英語で語句を読み上げるよう求めるメッセージが表示されます。 音声が Speech Service に送信され、テキストとして文字起こしされて、コンソールに表示されます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
