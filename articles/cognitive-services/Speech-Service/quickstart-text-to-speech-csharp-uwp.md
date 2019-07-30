---
title: クイック スタート:音声を合成する、C# (UWP) - Speech Services
titleSuffix: Azure Cognitive Services
description: この記事では、Cognitive Services Speech SDK を使用して C# のユニバーサル Windows プラットフォーム (UWP) アプリケーションを作成する方法について説明します。 リアルタイムでテキストから音声を合成し、デバイスのスピーカーに出力します。 このアプリケーションの構築には、Speech SDK NuGet パッケージと Microsoft Visual Studio 2017 を使用します。
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: c11fbff883d2699bdd0a107fc462524d92ee410d
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467219"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>クイック スタート:UWP アプリで Speech SDK を使用して音声を合成する

[音声認識](quickstart-csharp-uwp.md)、[音声翻訳](quickstart-translate-speech-uwp.md)、[音声優先仮想アシスタント](quickstart-virtual-assistant-csharp-uwp.md)のクイックスタートも利用できます。

この記事では、Cognitive Services [Speech SDK](speech-sdk.md) を使用して C# のユニバーサル Windows プラットフォーム (UWP、Windows バージョン 1709 以降) アプリケーションを開発する方法について説明します。 このプログラムでは、リアルタイムでテキストから音声を合成してデバイスのスピーカーに出力します。 このアプリケーションの構築には、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)と Microsoft Visual Studio 2017 (任意のエディション) を使用します。

> [!NOTE]
> ユニバーサル Windows プラットフォームを使用すると、PC、Xbox、Surface Hub、その他のデバイスなど、Windows 10 をサポートする任意のデバイスで動作するアプリを開発できます。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Speech Service の Azure サブスクリプション キー。 [無料で 1 つ取得します](get-started.md)。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>サンプル コードを追加する

1. アプリケーションのユーザー インターフェイスを定義するには、XAML を使用します。 ソリューション エクスプローラーで `MainPage.xaml` を開きます。 デザイナーの XAML ビューで、次の XAML スニペットを Grid タグに挿入します (`<Grid>` と `</Grid>` の間)。

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. コードビハインド ソース ファイル `MainPage.xaml.cs` を開きます (`MainPage.xaml` の下にグループ化されているものを見つけます)。 その中のすべてのコードを次の内容に置き換えます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. このファイル内の `Speak_ButtonClicked` ハンドラーで、文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. `Speak_ButtonClicked` ハンドラーで、文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md)に置き換えます (たとえば、無料試用版サブスクリプションでは `westus`)。

1. プロジェクトのすべての変更を保存します。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

1. アプリケーションをビルドします。 メニュー バーから、 **[ビルド]**  >  **[ソリューションのビルド]** を選択します。 これで、コードは、エラーなしでコンパイルされます。

    ![[ソリューションのビルド] オプションを強調表示した Visual Studio アプリケーションのスクリーンショット](media/sdk/qs-csharp-uwp-08-build.png "成功したビルド")

1. アプリケーションを起動します。 メニュー バーから、 **[デバッグ]**  >  **[デバッグの開始]** を選択するか、**F5** キーを押します。

    ![[デバッグの開始] オプションを強調表示した Visual Studio アプリケーションのスクリーンショット](media/sdk/qs-csharp-uwp-09-start-debugging.png "アプリのデバッグの開始")

1. テキストボックスに何かテキストを入力し、 **[Speak]\(読み上げ\)** をクリックします。 テキストが Speech Services に転送されて音声に合成され、スピーカーで再生されます。

    ![音声合成ユーザー インターフェイスのスクリーンショット](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で C# のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [音声フォントをカスタマイズする](how-to-customize-voice-font.md)
- [音声サンプルを録音する](record-custom-voice-samples.md)
