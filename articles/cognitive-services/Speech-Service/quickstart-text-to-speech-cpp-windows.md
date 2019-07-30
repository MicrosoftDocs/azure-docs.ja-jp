---
title: クイック スタート:音声を合成する (C++ (Windows)) - Speech Services
titleSuffix: Azure Cognitive Services
description: Windows デスクトップで C++ と Speech SDK を使用して音声を合成する方法について説明します
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: yinhew
ms.openlocfilehash: dfad425d6da2e3dabec138ef4f108957ebea7ee9
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67602913"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>クイック スタート:Windows 上で C++ と Speech SDK を使用して音声を合成する

クイック スタートは[音声認識](quickstart-cpp-windows.md)と[音声翻訳](quickstart-translate-speech-cpp-windows.md)にも使用できます。

この記事では、Windows 用の C++ コンソール アプリケーションを作成します。 Cognitive Services [Speech SDK](speech-sdk.md) を使用して、テキスからリアルタイムに音声を合成し、PC のスピーカーで再生します。 このアプリケーションの構築には、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)と Microsoft Visual Studio 2017 (任意のエディション) を使用します。

この記事に説明されている機能は、[Speech SDK 1.5.0](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/1.5.0) から利用できます。

音声合成が可能な言語と音声の完全な一覧については、[言語サポート](language-support.md#text-to-speech)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、Speech Services サブスクリプション キーが必要です。 1 つ無料で取得できます。 詳しくは、「[Speech Service を無料で試す](get-started.md)」を参照してください。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>サンプル コードを追加する

1. ソース ファイル *helloworld.cpp* を開きます。 最初の include ステートメント (`#include "stdafx.h"` または `#include "pch.h"`) の下にあるすべてのコードを、以下に置き換えます。

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. 同じファイル内で、文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

1. プロジェクトへの変更内容を保存します。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

1. アプリケーションをビルドします。 メニュー バーから、 **[ビルド]**  >  **[ソリューションのビルド]** を選択します。 コードはエラーなくコンパイルされるはずです。

   ![[ソリューションのビルド] オプションが強調表示された Visual Studio アプリケーションのスクリーン ショット](media/sdk/qs-cpp-windows-06-build.png)

1. アプリケーションを起動します。 メニュー バーから、 **[デバッグ]**  >  **[デバッグの開始]** を選択するか、**F5** キーを押します。

   ![[デバッグの開始] オプションが強調表示された Visual Studio アプリケーションのスクリーン ショット](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. コンソール ウィンドウが表示され、何かテキストを入力するように求められます。 いくつかの単語または文を入力してください。 入力したテキストが Speech Services に転送されて音声に合成され、スピーカーで再生されます。

   ![合成が成功した後のコンソール出力のスクリーンショット](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>次の手順

オーディオ ファイルに音声を保存する方法など、追加のサンプルは GitHub で入手できます。

> [!div class="nextstepaction"]
> [GitHub で C++ のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [音声フォントをカスタマイズする](how-to-customize-voice-font.md)
- [音声サンプルを録音する](record-custom-voice-samples.md)
