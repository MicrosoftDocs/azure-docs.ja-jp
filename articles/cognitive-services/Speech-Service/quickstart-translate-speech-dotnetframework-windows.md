---
title: クイック スタート:音声を翻訳する、C# (.NET Framework Windows) - Speech Service
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、ユーザーの音声をキャプチャし、別の言語に変換してコマンド ラインにテキストを出力する .NET Framework アプリケーションを作成します。 このガイドは、Windows ユーザー向けに設計されています。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: erhopf
ms.openlocfilehash: bb710a3e3adb13aa3999c13043c8bb93f6b885f1
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327341"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-framework-windows"></a>クイック スタート:Speech SDK for .NET Framework を使用して音声を翻訳する (Windows)

[音声認識](quickstart-csharp-dotnet-windows.md)と[音声合成](quickstart-text-to-speech-dotnet-windows.md)のクイックスタートも利用できます。

このクイックスタートでは、コンピューターのマイクからユーザーの音声をキャプチャし、その音声を翻訳して、翻訳されたテキストをコマンド ラインにリアルタイムで表示する .NET Framework アプリケーションを作成します。 このアプリケーションは、32 ビットまたは 64 ビット Windows 上で動作し、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)と Microsoft Visual Studio 2019 を使用して作成されています。

音声翻訳が可能な言語の完全な一覧については、[言語サポート](language-support.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。
* Speech Service 用の Azure サブスクリプション キー。 [無料で 1 つ取得します](get-started.md)。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>サンプル コードを追加する

1. **Program.cs** を開き、ファイル内のすべてのコードを次のコードに置き換えます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. `YourSubscriptionKey` という文字列を探し、実際のサブスクリプション キーに置き換えます。

1. `YourServiceRegion` という文字列を探し、ご利用のサブスクリプションに関連付けられている[リージョン](regions.md)に置き換えます。 たとえば、無料試用版サブスクリプションを使用している場合、リージョンは `westus` になります。

1. メニュー バーから **[ファイル]**  >  **[すべて保存]** を選択します。

## <a name="build-and-run-the-application"></a>アプリケーションの構築と実行

1. メニュー バーから **[ビルド]**  >  **[ソリューションのビルド]** を選択してアプリケーションをビルドします。 これで、コードは、エラーなしでコンパイルされます。

1. **[デバッグ]**  >  **[デバッグの開始]** の順に選択するか、**F5** キーを押して、**helloworld** アプリケーションを起動します。

1. デバイスのマイクに向かって英語のフレーズや文章を話します。 その音声が Speech Services に転送されて別の言語 (このケースではドイツ語) のテキストに翻訳されます。 翻訳済みのテキストが Speech Services からアプリケーションに返されてウィンドウに表示されます。

   ![音声翻訳ユーザー インターフェイス](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png)

## <a name="next-steps"></a>次の手順

オーディオ ファイルから音声を読み取る方法や、翻訳されたテキストを合成音声として出力する方法など、追加のサンプルは GitHub で入手できます。

> [!div class="nextstepaction"]
> [GitHub で C# のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [Custom Speech 用のモデルをトレーニングする](how-to-custom-speech-train-model.md)
