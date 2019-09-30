---
title: クイック スタート:音声を合成する、C# (.NET Core) - Speech Service
titleSuffix: Azure Cognitive Services
description: Windows の .NET Core で C# と Speech SDK を使用して音声を合成する方法について説明します
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: ccb6ec5b6f29c57a6136c571558d4126d0607710
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607680"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-core"></a>クイック スタート:.NET Core 用 Speech SDK を使用して音声を合成する

クイック スタートは[音声認識](quickstart-csharp-dotnetcore-windows.md)と[音声翻訳](quickstart-translate-speech-dotnetcore-windows.md)にも使用できます。

この記事では、Cognitive Services [Speech SDK](speech-sdk.md) を使用してWindows の .NET Core 用の C# コンソール アプリケーションを作成します。 リアルタイムでテキストから音声を合成し、PC のスピーカーに出力します。 このアプリケーションの構築には、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)と Microsoft Visual Studio 2017 以降 (任意のエディション) を使用します。

> [!NOTE]
> .NET Core は、[.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) 仕様を実装した、オープン ソース、クロスプラットフォームの .NET プラットフォームです。

このクイック スタートを完了するには、Speech Services サブスクリプション キーが必要です。 1 つ無料で取得できます。 詳しくは、「[Speech Service を無料で試す](get-started.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* [.NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 以降
* Speech Service 用の Azure サブスクリプション キー。 [無料で 1 つ取得します](get-started.md)。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>サンプル コードを追加する

1. `Program.cs` を開き、その中のすべてのコードを次の内容に置き換えます。

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnetcore/helloworld/Program.cs#code)]

1. 同じファイル内で、文字列 `YourSubscriptionKey` をサブスクリプション キーに置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

1. プロジェクトへの変更内容を保存します。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

1. アプリケーションをビルドします。 メニュー バーから、 **[ビルド]**  >  **[ソリューションのビルド]** を選択します。 コードはエラーなくコンパイルされるはずです。

    ![[ソリューションのビルド] オプションを強調表示した Visual Studio アプリケーションのスクリーンショット](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "成功したビルド")

1. アプリケーションを起動します。 メニュー バーから、 **[デバッグ]**  >  **[デバッグの開始]** を選択するか、**F5** キーを押します。

    ![[デバッグの開始] オプションを強調表示した Visual Studio アプリケーションのスクリーンショット](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "アプリのデバッグの開始")

1. コンソール ウィンドウが表示され、何かテキストを入力するように求められます。 いくつかの単語または文を入力してください。 入力したテキストが Speech Services に転送されて音声に合成され、スピーカーで再生されます。

    ![正常に合成された後のコンソールの出力のスクリーンショット](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "正常に合成された後のコンソールの出力")

## <a name="next-steps"></a>次の手順

音声を合成してオーディオ ファイルに保存する方法など、その他のサンプルは GitHub で入手できます。

> [!div class="nextstepaction"]
> [GitHub で C# のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [音声フォントをカスタマイズする](how-to-customize-voice-font.md)
- [音声サンプルを録音する](record-custom-voice-samples.md)
