---
title: 'クイック スタート: 音声を認識する (C++ (Windows)) - Speech Services'
titleSuffix: Azure Cognitive Services
description: Windows デスクトップで C++ と Speech SDK を使用して音声を認識する方法について説明します
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: wolfma
ms.openlocfilehash: a3fe14436a569a8354e4bfd58a9ba164881d8fe2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57878683"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>クイック スタート:Windows 上で C++ と Speech SDK を使用して音声を認識する

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、Windows 用の C++ コンソール アプリケーションを作成します。 Cognitive Services [Speech SDK](speech-sdk.md) を使用して、リアルタイムに PC のマイクからの音声をテキストに文字起こします。 このアプリケーションの構築には、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)と Microsoft Visual Studio 2017 (任意のエディション) を使用します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、Speech Services サブスクリプション キーが必要です。 1 つ無料で取得できます。 詳しくは、「[Speech Service を無料で試す](get-started.md)」を参照してください。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>サンプル コードを追加する

1. ソース ファイル *helloworld.cpp* を開きます。 最初の include ステートメント (`#include "stdafx.h"` または `#include "pch.h"`) の下にあるすべてのコードを、以下に置き換えます。

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. 同じファイル内で、文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

1. プロジェクトへの変更内容を保存します。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

1. アプリケーションをビルドします。 メニュー バーから、**[ビルド]** > **[ソリューションのビルド]** を選択します。 コードはエラーなくコンパイルされるはずです。

   ![[ソリューションのビルド] オプションが強調表示された Visual Studio アプリケーションのスクリーン ショット](media/sdk/qs-cpp-windows-06-build.png)

1. アプリケーションを起動します。 メニュー バーから、**[デバッグ]** > **[デバッグの開始]** を選択するか、**F5** キーを押します。

   ![[デバッグの開始] オプションが強調表示された Visual Studio アプリケーションのスクリーン ショット](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. コンソール ウィンドウが表示され、何か言うように求められます。 英語の語句または文を読み上げます。 音声が Speech Services に送信されてテキストに変換され、同じウィンドウに表示されます。

   ![認識が成功した後のコンソール出力のスクリーンショット](media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>次の手順

オーディオ ファイルから音声を読み取る方法など、追加のサンプルは GitHub で入手できます。

> [!div class="nextstepaction"]
> [GitHub で C++ のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)
