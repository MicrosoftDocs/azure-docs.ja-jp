---
title: クイック スタート:音声を翻訳する、C++ (Windows) - Speech Service
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、ユーザーの音声をキャプチャし、別の言語に変換してコマンド ラインにテキストを出力する C++ アプリケーションを作成します。 このガイドは、Windows ユーザー向けに設計されています。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: erhopf
ms.openlocfilehash: 6ba4e44efc7ff24aa48f9f16840b2248423f7241
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382675"
---
# <a name="quickstart-translate-speech-in-c-on-windows-by-using-the-speech-sdk"></a>クイック スタート:Windows 上で C++ と Speech SDK を使用して音声を翻訳する

[音声認識](quickstart-cpp-windows.md)と[音声合成](quickstart-text-to-speech-cpp-windows.md)のクイックスタートも利用できます。

このクイックスタートでは、コンピューターのマイクからユーザーの音声をキャプチャし、その音声を翻訳して、翻訳されたテキストをコマンド ラインにリアルタイムで表示する C++ アプリケーションを作成します。 このアプリケーションは、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)と Microsoft Visual Studio 2019 (任意のエディション) を使用して作成します。

音声翻訳が可能な言語の完全な一覧については、[言語サポート](language-support.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、Speech Services サブスクリプション キーが必要です。 1 つ無料で取得できます。 詳しくは、「[Speech Service を無料で試す](get-started.md)」を参照してください。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>サンプル コードを追加する

1. ソース ファイル **helloworld.cpp** を開きます。

1. すべてのコードを次のスニペットに置き換えます。

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/cpp-windows/helloworld/helloworld.cpp?range=2-#code)]

1. 同じファイル内で、文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

1. メニュー バーから **[ファイル]**  >  **[すべて保存]** を選択します。

## <a name="build-and-run-the-application"></a>アプリケーションの構築と実行

1. メニュー バーから **[ビルド]**  >  **[ソリューションのビルド]** を選択してアプリケーションをビルドします。 これで、コードは、エラーなしでコンパイルされます。

1. **[デバッグ]**  >  **[デバッグの開始]** の順に選択するか、**F5** キーを押して、**helloworld** アプリケーションを起動します。

1. 英語の語句または文を読み上げます。 その音声が Speech Services に転送され、(このケースではフランス語とドイツ語に) 翻訳されてテキストに文字起こしされます。 そのテキストが Speech Services からアプリケーションに返されて表示されます。

   ![音声翻訳が成功した後のコンソール出力](media/sdk/qs-translate-cpp-windows-output.png)

## <a name="next-steps"></a>次の手順

オーディオ ファイルから音声を読み取る方法や、翻訳されたテキストを合成音声に変換する方法など、追加のサンプルは GitHub で入手できます。

> [!div class="nextstepaction"]
> [GitHub で C++ のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [Custom Speech 用のモデルをトレーニングする](how-to-custom-speech-train-model.md)
