---
title: 音声合成のクイックスタート - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech SDK を使用してテキストを音声に変換する方法について説明します。 このクイックスタートでは、オブジェクトの構築と設計パターン、サポートされているオーディオ出力形式、Speech CLI、音声合成でのカスタム構成オプションについて説明します。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/11/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp
zone_pivot_groups: speech-full-stack
ms.openlocfilehash: 12ee7fbbd7699d09deae85971ffe411e115609ec
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934696"
---
# <a name="get-started-with-speech-synthesis"></a>音声合成を使ってみる

このクイックスタートでは、Speech SDK を使用してテキスト読み上げ合成を行うための一般的な設計パターンについて説明します。 まずは基本的な構成と合成を行った後、次のようなより高度なカスタム アプリケーション開発の例に進みます。

* インメモリ ストリームとして応答を取得する
* 出力のサンプル レートとビット レートをカスタマイズする
* SSML (音声合成マークアップ言語) を使用して合成要求を送信する
* ニューラル音声を使用する

> [!TIP]
> この記事をスキップしてサンプル コードをご覧になりたい方は、GitHub 上の[クイックスタート サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart)を参照してください。

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

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-spx"
[!INCLUDE [CLI include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="next-steps"></a>次のステップ

* [カスタム音声の概要](how-to-custom-voice.md)
* [SSML を使用して合成を改善する](speech-synthesis-markup.md)
* 書籍やニュース記事など大きなテキスト サンプルで [Long Audio API](long-audio-api.md) の使い方を学ぶ
* GitHub 上の[クイックスタート サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart)を参照してください。
