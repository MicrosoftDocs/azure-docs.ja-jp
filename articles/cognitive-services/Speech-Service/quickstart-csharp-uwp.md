---
title: クイック スタート:音声を認識する、C# (UWP) - Speech Service
titleSuffix: Azure Cognitive Services
description: この記事では、Cognitive Services Speech SDK を使用して C# のユニバーサル Windows プラットフォーム (UWP) アプリケーションを作成する方法について説明します。 デバイスのマイクからリアルタイムで音声をテキストに変換します。 このアプリケーションは、Speech SDK NuGet パッケージと Microsoft Visual Studio 2019 を使用して作成します。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: fe5ff376a7895e2ca5246c0b9eb575752b07c7a1
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382269"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>クイック スタート:UWP アプリで Speech SDK を使用して音声を認識する

[音声合成](quickstart-text-to-speech-csharp-uwp.md)、[音声翻訳](quickstart-translate-speech-uwp.md)、[音声優先仮想アシスタント](quickstart-virtual-assistant-csharp-uwp.md)のクイックスタートも利用できます。

必要に応じて、別のプログラミング言語や環境を選択してください。<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、Cognitive Services [Speech SDK](speech-sdk.md) を使用して C# のユニバーサル Windows プラットフォーム (UWP) アプリケーションを開発する方法について説明します。 このプログラムは、デバイスのマイクからリアルタイムで音声をテキストに変換します。 このアプリケーションは、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)と Microsoft Visual Studio 2019 (任意のエディション) を使用して作成します。

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

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. **ソリューション エクスプローラー**で、コードビハインドのソース ファイル `MainPage.xaml.cs` を開きます (`MainPage.xaml` にグループ化されています)。

1. その中のすべてのコードを次のスニペットに置き換えます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. ソース ファイルの `SpeechRecognitionFromMicrophone_ButtonClicked` ハンドラーから `YourSubscriptionKey` という文字列を見つけて、ご利用のサブスクリプション キーに置き換えます。

1. `SpeechRecognitionFromMicrophone_ButtonClicked` ハンドラーから `YourServiceRegion` という文字列を見つけて、ご利用のサブスクリプションに関連付けられている[リージョン](regions.md)に置き換えます。 たとえば、無料試用版サブスクリプションの場合は `westus` を使用します。

1. メニュー バーから **[ファイル]**  >  **[すべて保存]** を選択して変更内容を保存します。

## <a name="build-and-run-the-application"></a>アプリケーションの構築と実行

アプリケーションをビルドしてテストする準備が整いました。

1. メニュー バーから **[ビルド]**  >  **[ソリューションのビルド]** を選択してアプリケーションをビルドします。 これで、コードは、エラーなしでコンパイルされます。

1. **[デバッグ]**  >  **[デバッグの開始]** の順に選択するか、**F5** キーを押して、アプリケーションを起動します。 **[helloworld]** ウィンドウが表示されます。

   ![サンプル UWP 音声認識アプリケーション (C#) - クイックスタート](media/sdk/qs-csharp-uwp-helloworld-window.png)

1. **[Enable Microphone]** を選択し、アクセス許可要求がポップアップ表示されたら、 **[はい]** を選択します。

   ![マイクへのアクセス許可要求](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. **[Speech recognition with microphone input]** を選択し、デバイスのマイクに向かって英語のフレーズを話します。 音声が Speech Services に送信されてテキストに変換され、ウィンドウに表示されます。

   ![音声認識ユーザー インターフェイス](media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で C# のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [クイック スタート:Speech SDK for C# (UWP) を使用して音声を翻訳する](quickstart-translate-speech-uwp.md)
- [Custom Speech 用のモデルをトレーニングする](how-to-custom-speech-train-model.md)
