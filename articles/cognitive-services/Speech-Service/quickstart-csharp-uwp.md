---
title: 'クイック スタート: 音声を認識する、C# (UWP) - Speech Services'
titleSuffix: Azure Cognitive Services
description: この記事では、Cognitive Services Speech SDK を使用して C# のユニバーサル Windows プラットフォーム (UWP) アプリケーションを作成する方法について説明します。 デバイスのマイクからリアルタイムで音声をテキストに変換します。 このアプリケーションの構築には、Speech SDK NuGet パッケージと Microsoft Visual Studio 2017 を使用します。
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/23/2019
ms.author: lisaweixu
ms.custom: seodec18
ms.openlocfilehash: 25b3474e33351d6365af37d78f442768aba88625
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405914"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>クイック スタート: UWP アプリで Speech SDK を使用して音声を認識する

[テキスト読み上げ](quickstart-text-to-speech-csharp-uwp.md)、[音声翻訳](quickstart-translate-speech-uwp.md)、[音声優先仮想アシスタント](quickstart-virtual-assistant-csharp-uwp.md)のクイックスタートも利用できます。

必要に応じて、別のプログラミング言語や環境を選択します。<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、Cognitive Services [Speech SDK](speech-sdk.md) を使用して C# のユニバーサル Windows プラットフォーム (UWP、Windows バージョン 1709 以降) アプリケーションを開発する方法について説明します。 このプログラムは、デバイスのマイクからリアルタイムで音声をテキストに変換します。 このアプリケーションの構築には、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)と Microsoft Visual Studio 2017 (任意のエディション) を使用します。

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

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. コードビハインド ソース ファイル `MainPage.xaml.cs` を開きます (`MainPage.xaml` の下にグループ化されているものを見つけます)。 その中のすべてのコードを次の内容に置き換えます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. このファイル内の `SpeechRecognitionFromMicrophone_ButtonClicked` ハンドラーで、文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. `SpeechRecognitionFromMicrophone_ButtonClicked` ハンドラーで、文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md)に置き換えます (たとえば、無料試用版サブスクリプションでは `westus`)。

1. プロジェクトのすべての変更を保存します。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

1. アプリケーションをビルドします。 メニュー バーから、 **[ビルド]**  >  **[ソリューションのビルド]** を選択します。 これで、コードは、エラーなしでコンパイルされます。

    ![[ソリューションのビルド] オプションを強調表示した Visual Studio アプリケーションのスクリーンショット](media/sdk/qs-csharp-uwp-08-build.png "成功したビルド")

1. アプリケーションを起動します。 メニュー バーから、 **[デバッグ]**  >  **[デバッグの開始]** を選択するか、**F5** キーを押します。

    ![[デバッグの開始] オプションを強調表示した Visual Studio アプリケーションのスクリーンショット](media/sdk/qs-csharp-uwp-09-start-debugging.png "アプリのデバッグの開始")

1. ウィンドウがポップアップ表示されます。 **[Enable Microphone]** を選択して、ポップアップ表示されるアクセス許可要求を確認します。

    ![アクセス許可要求のスクリーンショット](media/sdk/qs-csharp-uwp-10-access-prompt.png "アプリのデバッグの開始")

1. **[Speech recognition with microphone input]** を選択し、デバイスのマイクに向かって英語のフレーズを話します。 音声が Speech Services に送信されてテキストに変換され、ウィンドウに表示されます。

    ![音声認識ユーザー インターフェイスのスクリーンショット](media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で C# のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [音声を変換する](how-to-translate-speech-csharp.md)
- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)
