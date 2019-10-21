---
title: クイック スタート:音声を認識する、.NET Framework (Windows) - Speech Service
titleSuffix: Azure Cognitive Services
description: このガイドでは、.NET framework for Windows と Speech SDK を使用して音声変換コンソール アプリケーションを作成します。 完了すると、お使いのコンピューターのマイクを使用して、リアルタイムに文字起こし (音声テキスト変換) することができます。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: wolfma
ms.openlocfilehash: cb140647394858fbc0a9a00ea125365d5b7a08d5
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327041"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>クイック スタート:.NET Framework (Windows) 用 Speech SDK を使用して音声を認識する

[音声合成](quickstart-text-to-speech-dotnet-windows.md)と[音声翻訳](quickstart-translate-speech-dotnetframework-windows.md)のクイックスタートも利用できます。

必要に応じて、別のプログラミング言語や環境を選択してください。<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

このガイドでは、.NET framework for Windows と Speech SDK を使用して音声変換コンソール アプリケーションを作成します。 完了すると、お使いのコンピューターのマイクを使用して、リアルタイムに文字起こし (音声テキスト変換) することができます。

この記事に示したような Visual Studio プロジェクトをご自身で作成する必要のない簡単なデモについては、最新の [Cognitive Services Speech SDK サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk)を GitHub から入手してください。

## <a name="prerequisites"></a>前提条件

このプロジェクトを完了するには、以下が必要になります。

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。
* 音声サービス用のサブスクリプション キー。 [無料で 1 つ取得します](get-started.md)。
* お使いのコンピューターに備わっているマイクへのアクセス。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>サンプル コードを追加する

1. **Program.cs** を開いて、自動的に生成されたコードを次のサンプルに置き換えます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. `YourSubscriptionKey` という文字列を探し、実際の Speech Services のサブスクリプション キーに置き換えます。

1. `YourServiceRegion` という文字列を探し、ご利用のサブスクリプションに関連付けられている[リージョン](regions.md)に置き換えます。 たとえば、無料試用版サブスクリプションを使用している場合、リージョンは `westus` になります。

1. メニュー バーから **[ファイル]**  >  **[すべて保存]** を選択します。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

1. メニュー バーから **[ビルド]**  >  **[ソリューションのビルド]** を選択してアプリケーションをビルドします。 これで、コードは、エラーなしでコンパイルされます。

1. **[デバッグ]**  >  **[デバッグの開始]** の順に選択するか、**F5** キーを押して、**helloworld** アプリケーションを起動します。

1. デバイスのマイクに向かって英語のフレーズや文章を話します。 その音声が Speech Services に転送され、そこで文字起こしされたテキストが再びアプリケーションに送信されて表示されます。

   ![音声認識ユーザー インターフェイス](media/sdk/qs-csharp-dotnet-windows-10-console-output.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で C# のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [Custom Speech 用のモデルをトレーニングする](how-to-custom-speech-train-model.md)
