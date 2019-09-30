---
title: クイック スタート:音声を合成する、C++ (Windows) - Speech Service
titleSuffix: Azure Cognitive Services
description: Windows デスクトップで C++ と Speech SDK を使用して音声を合成する方法について説明します
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: yinhew
ms.openlocfilehash: 686b21d3e02266af77687778c32f0d1ca6d55154
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383067"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>クイック スタート:Windows 上で C++ と Speech SDK を使用して音声を合成する

[音声認識](quickstart-cpp-windows.md)と[音声翻訳](quickstart-translate-speech-cpp-windows.md)のクイックスタートも利用できます。

この記事では、Windows 用の C++ コンソール アプリケーションを作成します。 Cognitive Services [Speech SDK](speech-sdk.md) を使用して、テキスからリアルタイムに音声を合成し、PC のスピーカーで再生します。 このアプリケーションは、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)と Microsoft Visual Studio 2019 (任意のエディション) を使用して作成します。

音声合成が可能な言語と音声の完全な一覧については、[言語サポート](language-support.md#text-to-speech)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、Speech Services サブスクリプション キーが必要です。 1 つ無料で取得できます。 詳しくは、「[Speech Service を無料で試す](get-started.md)」を参照してください。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>サンプル コードを追加する

1. ソース ファイル **helloworld.cpp** を開きます。

1. すべてのコードを次のスニペットに置き換えます。

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. 同じファイル内で、文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

1. メニュー バーから **[ファイル]**  >  **[すべて保存]** を選択します。

## <a name="build-and-run-the-application"></a>アプリケーションの構築と実行

1. メニュー バーから **[ビルド]**  >  **[ソリューションのビルド]** を選択してアプリケーションをビルドします。 これで、コードは、エラーなしでコンパイルされます。

1. **[デバッグ]**  >  **[デバッグの開始]** の順に選択するか、**F5** キーを押して、**helloworld** アプリケーションを起動します。

1. 英語の語句または文を入力します。 そのテキストが Speech Services に転送され、そこで合成された音声がアプリケーションに送信されてスピーカーで再生されます。

   ![音声合成が成功した後のコンソール出力](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>次の手順

オーディオ ファイルに音声を保存する方法など、追加のサンプルは GitHub で入手できます。

> [!div class="nextstepaction"]
> [GitHub で C++ のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [カスタム音声を作成する](how-to-custom-voice-create-voice.md)
- [カスタム音声サンプルを録音する](record-custom-voice-samples.md)
