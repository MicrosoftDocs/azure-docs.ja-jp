---
title: クイック スタート:音声を合成する、.NET Framework (Windows) - Speech Service
titleSuffix: Azure Cognitive Services
description: このガイドでは、.NET framework for Windows と Speech SDK を使用してテキスト読み上げのコンソール アプリケーションを作成します。 完了すると、テキストから音声を合成し、その音声をスピーカーからリアルタイムで聞くことができます。
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: 1a411455e4a6dea22e092cdfc8e70ee23b656435
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327450"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>クイック スタート:.NET Framework (Windows) 用 Speech SDK を使用して音声を合成する

[音声認識](quickstart-csharp-dotnet-windows.md)と[音声翻訳](quickstart-translate-speech-dotnetframework-windows.md)のクイックスタートも利用できます。

このガイドでは、.NET framework for Windows と Speech SDK を使用してテキスト読み上げのコンソール アプリケーションを作成します。 完了すると、テキストから音声を合成し、その音声をスピーカーからリアルタイムで聞くことができます。

この記事に示したような Visual Studio プロジェクトをご自身で作成する必要のない簡単なデモについては、最新の [Cognitive Services Speech SDK サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk)を GitHub から入手してください。

## <a name="prerequisites"></a>前提条件

このプロジェクトを完了するには、以下が必要になります。

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。
* 音声サービス用のサブスクリプション キー。 [無料で 1 つ取得します](get-started.md)。
* スピーカー (またはヘッドセット)。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>サンプル コードを追加する

1. **Program.cs** を開いて、自動的に生成されたコードを次のサンプルに置き換えます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. `YourSubscriptionKey` という文字列を探し、実際の Speech Services のサブスクリプション キーに置き換えます。

1. `YourServiceRegion` という文字列を探し、ご利用のサブスクリプションに関連付けられている[リージョン](regions.md)に置き換えます。 たとえば、無料試用版サブスクリプションを使用している場合、リージョンは `westus` になります。

1. メニュー バーから **[ファイル]**  >  **[すべて保存]** を選択します。

## <a name="build-and-run-the-application"></a>アプリケーションの構築と実行

1. メニュー バーから **[ビルド]**  >  **[ソリューションのビルド]** を選択してアプリケーションをビルドします。 これで、コードは、エラーなしでコンパイルされます。

1. **[デバッグ]**  >  **[デバッグの開始]** の順に選択するか、**F5** キーを押して、**helloworld** アプリケーションを起動します。

1. 英語の語句または文を入力します。 そのテキストが Speech Services に転送され、そこで合成された音声がアプリケーションに送信されてスピーカーで再生されます。

   ![音声合成ユーザー インターフェイス](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で C# のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [カスタム音声を作成する](how-to-custom-voice-create-voice.md)
- [カスタム音声サンプルを録音する](record-custom-voice-samples.md)
