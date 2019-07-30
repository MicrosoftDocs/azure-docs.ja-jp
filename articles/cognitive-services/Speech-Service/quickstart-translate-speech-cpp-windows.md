---
title: クイック スタート:音声を翻訳する、C++ (Windows) - Speech Services
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、ユーザーの音声をキャプチャし、別の言語に変換してコマンド ラインにテキストを出力する、単純な C++ アプリケーションを作成します。 このガイドは、Windows ユーザー向けに設計されています。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: ff577bc0fe7db05c71aab04f0aaa5525f30b255c
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67602891"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c"></a>クイック スタート:Speech SDK for C++ を使用して音声を翻訳する

クイック スタートは[音声認識](quickstart-cpp-windows.md)と[テキスト読み上げ](quickstart-text-to-speech-cpp-windows.md)にも使用できます。

このクイック スタートでは、コンピューターのマイクからユーザーの音声をキャプチャし、その音声を翻訳して、翻訳されたテキストをコマンド ラインにリアルタイムで表示する、単純な C++ アプリケーションを作成します。 このアプリケーションは、64 ビット Windows 上で実行するように設計されており、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)と Microsoft Visual Studio 2017 を使用してビルドされています。

音声翻訳が可能な言語の完全な一覧については、[言語サポート](language-support.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Speech Service の Azure サブスクリプション キー。 [無料で 1 つ取得します](get-started.md)。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>サンプル コードを追加する

1. ソース ファイル *helloworld.cpp* を開きます。 最初の include ステートメント (`#include "stdafx.h"` または `#include "pch.h"`) の下にあるすべてのコードを、以下に置き換えます。

    [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/cpp-windows/helloworld/helloworld.cpp#code)]

1. 同じファイル内で、文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

1. プロジェクトへの変更内容を保存します。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

1. アプリケーションをビルドします。 メニュー バーから、 **[ビルド]**  >  **[ソリューションのビルド]** を選択します。 コードはエラーなくコンパイルされるはずです。

   ![[ソリューションのビルド] オプションが強調表示された Visual Studio アプリケーションのスクリーン ショット](media/sdk/qs-cpp-windows-06-build.png)

1. アプリケーションを起動します。 メニュー バーから、 **[デバッグ]**  >  **[デバッグの開始]** を選択するか、**F5** キーを押します。

   ![[デバッグの開始] オプションが強調表示された Visual Studio アプリケーションのスクリーン ショット](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. コンソール ウィンドウが表示され、何か言うように求められます。 英語の語句または文を読み上げます。 音声が Speech サービスに送信され、翻訳されてテキストに変換され、同じウィンドウに表示されます。

   ![翻訳が成功した後のコンソール出力のスクリーンショット](media/sdk/qs-translate-cpp-windows-output.png)

## <a name="next-steps"></a>次の手順

オーディオ ファイルから音声を読み取る方法や、翻訳されたテキストを合成音声として出力する方法など、追加のサンプルは GitHub で入手できます。

> [!div class="nextstepaction"]
> [GitHub で C++ のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)
