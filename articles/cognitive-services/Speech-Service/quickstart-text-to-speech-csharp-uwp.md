---
title: クイック スタート:音声を合成する、C# (UWP) - Speech Service
titleSuffix: Azure Cognitive Services
description: この記事では、Cognitive Services Speech SDK を使用して C# のユニバーサル Windows プラットフォーム (UWP) アプリケーションを作成する方法について説明します。 リアルタイムでテキストから音声を合成し、デバイスのスピーカーに出力します。 このアプリケーションは、Speech SDK NuGet パッケージと Microsoft Visual Studio 2019 を使用して作成します。
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 65b65c9af377b6a9951f9f328e0732850d3b9c1d
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382217"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>クイック スタート:UWP アプリで Speech SDK を使用して音声を合成する

[音声認識](quickstart-csharp-uwp.md)、[音声翻訳](quickstart-translate-speech-uwp.md)、[音声優先仮想アシスタント](quickstart-virtual-assistant-csharp-uwp.md)のクイックスタートも利用できます。

この記事では、Cognitive Services [Speech SDK](speech-sdk.md) を使用して C# のユニバーサル Windows プラットフォーム (UWP) アプリケーションを開発する方法について説明します。 このプログラムでは、リアルタイムでテキストから音声を合成してデバイスのスピーカーに出力します。 このアプリケーションは、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)と Microsoft Visual Studio 2019 (任意のエディション) を使用して作成します。

> [!NOTE]
> ユニバーサル Windows プラットフォームを使用すると、PC、Xbox、Surface Hub、その他のデバイスなど、Windows 10 をサポートする任意のデバイスで動作するアプリを開発できます。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。
* Speech Service 用の Azure サブスクリプション キー。 [無料で 1 つ取得します](get-started.md)。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>サンプル コードを追加する

今度は、アプリケーションのユーザー インターフェイスを定義する XAML コードを追加し、C# のコードビハインド実装を追加します。

1. **ソリューション エクスプローラー**で、`MainPage.xaml` を開きます。

1. デザイナーの XAML ビューで、次の XAML スニペットを **Grid** タグに挿入します (`<Grid>` と `</Grid>` の間)。

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. **ソリューション エクスプローラー**で、コードビハインドのソース ファイル `MainPage.xaml.cs` を開きます (`MainPage.xaml` にグループ化されています)。

1. その中のすべてのコードを次のスニペットに置き換えます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. ソース ファイルの `Speak_ButtonClicked` ハンドラーから `YourSubscriptionKey` という文字列を見つけて、ご利用のサブスクリプション キーに置き換えます。

1. `Speak_ButtonClicked` ハンドラーから `YourServiceRegion` という文字列を見つけて、ご利用のサブスクリプションに関連付けられている[リージョン](regions.md)に置き換えます。 たとえば、無料試用版サブスクリプションの場合は `westus` を使用します。

1. メニュー バーから **[ファイル]**  >  **[すべて保存]** を選択して変更内容を保存します。

## <a name="build-and-run-the-application"></a>アプリケーションの構築と実行

アプリケーションをビルドしてテストする準備が整いました。

1. メニュー バーから **[ビルド]**  >  **[ソリューションのビルド]** を選択してアプリケーションをビルドします。 これで、コードは、エラーなしでコンパイルされます。

1. **[デバッグ]**  >  **[デバッグの開始]** の順に選択するか、**F5** キーを押して、アプリケーションを起動します。 **[helloworld]** ウィンドウが表示されます。

   ![サンプル UWP 音声合成アプリケーション (C#) - クイックスタート](media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. テキストボックスに何かテキストを入力し、 **[Speak]\(読み上げ\)** をクリックします。 テキストが Speech Services に転送されて音声に合成され、スピーカーで再生されます。

    ![音声合成ユーザー インターフェイス](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で C# のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [カスタム音声モデルを作成して使用する](how-to-custom-voice-create-voice.md)
- [音声サンプルを録音する](record-custom-voice-samples.md)
