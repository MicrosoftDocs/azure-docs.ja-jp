---
title: クイック スタート:音声を合成する (.NET Framework (Windows)) - Speech Services
titleSuffix: Azure Cognitive Services
description: このガイドでは、.NET framework for Windows と Speech SDK を使用してテキスト読み上げのコンソール アプリケーションを作成します。 完了すると、テキストから音声を合成し、その音声をスピーカーからリアルタイムで聞くことができます。
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: yinhew
ms.openlocfilehash: 352d3f5f34bd46d8ee1ba2ed45dcc28af2b430ef
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605024"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>クイック スタート:.NET Framework (Windows) 用 Speech SDK を使用して音声を合成する

クイック スタートは[音声認識](quickstart-csharp-dotnet-windows.md)と[音声翻訳](quickstart-translate-speech-dotnetframework-windows.md)にも使用できます。

このガイドでは、.NET framework for Windows と Speech SDK を使用してテキスト読み上げのコンソール アプリケーションを作成します。 完了すると、テキストから音声を合成し、その音声をスピーカーからリアルタイムで聞くことができます。

簡単なデモ (以下に示されているように Visual Studio プロジェクトを自身で構築しない):

最新の [Cognitive Services Speech SDK サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk)を GitHub から取得します。

## <a name="prerequisites"></a>前提条件

このプロジェクトを完了するには、以下が必要になります。

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* 音声サービス用のサブスクリプション キー。 [無料で 1 つ取得します](get-started.md)。
* スピーカー (またはヘッドセット)。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>サンプル コードを追加する

1. `Program.cs` を開いて、自動的に生成されたコードを次のサンプルに置き換えます。

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. `YourSubscriptionKey` という文字列を探し、実際の Speech Services のサブスクリプション キーに置き換えます。

1. `YourServiceRegion` という文字列を探し、実際のサブスクリプションに関連付けられている[リージョン](regions.md)に置き換えます。 たとえば、無料試用版を使用している場合、リージョンは `westus` になります。

1. プロジェクトへの変更内容を保存します。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

1. メニュー バーから、 **[ビルド]**  >  **[ソリューションのビルド]** を選択します。 これで、コードは、エラーなしでコンパイルされます。

    ![[ソリューションのビルド] オプションを強調表示した Visual Studio アプリケーションのスクリーンショット](media/sdk/qs-csharp-dotnet-windows-08-build.png "成功したビルド")

1. メニュー バーから **[デバッグ]**  >  **[デバッグの開始]** の順に選択するか、**F5** キーを押して、アプリケーションを起動します。

    ![[デバッグの開始] オプションを強調表示した Visual Studio アプリケーションのスクリーンショット](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "アプリのデバッグの開始")

1. コンソール ウィンドウが表示され、何かテキストを入力するよう求められます。 いくつかの単語または文を入力してください。 入力したテキストが Speech Services に転送されて音声に合成され、スピーカーで再生されます。

    ![正常に認識された後のコンソールの出力のスクリーンショット](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "正常に認識された後のコンソールの出力")

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で C# のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [音声フォントをカスタマイズする](how-to-customize-voice-font.md)
- [音声サンプルを録音する](record-custom-voice-samples.md)
