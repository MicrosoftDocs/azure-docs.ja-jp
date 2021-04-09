---
title: テキスト読み上げクイックスタート - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech SDK を使用してテキストを音声に変換する方法について説明します。 このクイックスタートでは、オブジェクトの構築と設計パターン、サポートされているオーディオ出力形式、Speech CLI、音声合成でのカスタム構成オプションについて説明します。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/01/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-twenty-four
keywords: テキスト読み上げ
ms.openlocfilehash: 7a41c4d9c1074b376da3de556caf63ced0bc84ec
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102428196"
---
# <a name="get-started-with-text-to-speech"></a>テキスト読み上げの概要

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [Objective-C and Swift Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-objectivec-swift.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-curl"
[!INCLUDE [REST include](includes/how-to/text-to-speech-basics/text-to-speech-basics-curl.md)]
::: zone-end

::: zone pivot="programmer-tool-spx"
[!INCLUDE [CLI Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="get-position-information"></a>位置情報を取得する

プロジェクトでは、ある単語が音声変換によって読み上げられるタイミングに基づいて特定のアクションを実行できるように、そのタイミングを把握することが必要な場合があります。 たとえば、読み上げられた単語を強調表示するには、強調表示する内容、強調表示するタイミング、および強調表示する期間を把握する必要があります。

これは、`WordBoundary` 内で使用可能な `SpeechSynthesizer` イベントを使用して実現できます。 このイベントは、新しい音声それぞれの先頭で発生し、音声ストリーム内での時間オフセットと入力プロンプト内でのテキスト オフセットを提供します。

* `AudioOffset` は、合成の開始から次の単語の開始までの間の出力オーディオの経過時間を報告します。 これは 100 ナノ秒単位 (HNS) で測定されます (10,000 HNS は 1 ミリ秒に相当します)。
* `WordOffset` は、読み上げられようとしている単語の直前に、入力文字列 (元のテキストまたは [SSML](speech-synthesis-markup.md)) の文字位置を報告します。

> [!NOTE]
> `WordBoundary` イベントは、出力オーディオ データが使用可能になると発生します。これは、出力デバイスへの再生よりも高速です。 ストリームのタイミングを "リアルタイム" に適切に同期することは、呼び出し元で行う必要があります。

`WordBoundary` を使用する例は、GitHub の[テキスト読み上げのサンプル](https://aka.ms/csspeech/samples)にあります。

## <a name="next-steps"></a>次のステップ

* [カスタム音声の概要](how-to-custom-voice.md)
* [SSML を使用して合成を改善する](speech-synthesis-markup.md)
* 書籍やニュース記事など大きなテキスト サンプルで [Long Audio API](long-audio-api.md) の使い方を学ぶ
* GitHub 上の[クイックスタート サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart)を参照してください。
