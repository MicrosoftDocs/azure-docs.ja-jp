---
title: Speech Service による音声翻訳
titleSuffix: Azure Cognitive Services
description: Speech Service を使用すると、音声のエンド ツー エンドでリアルタイムの多言語翻訳機能を、アプリケーション、ツール、デバイスに追加することができます。 同じ API を、音声間の翻訳と、音声テキスト変換の両方に使用できます。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 670d7f160285283bd44371b893c63904b2685926
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934499"
---
# <a name="what-is-speech-translation"></a>音声翻訳とは何か

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Speech Service の音声翻訳では、オーディオ ストリームをリアルタイムに処理し、複数言語の音声間翻訳や音声テキスト翻訳を実行することができます。 Speech SDK を使用すると、アプリケーション、ツール、およびデバイスから、提供されたオーディオのソース トランスクリプションや翻訳出力にアクセスすることができます。 音声の検出中には中間トランスクリプションと翻訳結果が返され、最終的な結果は、合成された音声に変換することができます。

Microsoft の翻訳エンジンは、統計的機械翻訳 (SMT) とニューラル機械翻訳 (NMT) という 2 つのアプローチによって強化されています。 SMT では、高度な統計分析を使用して、いくつかの単語の文脈から考えられる最善の翻訳が推定されます。 NMT では、ニューラル ネットワークを使用して、文全体のコンテキストを踏まえた、正確で自然な翻訳が提供されます。

現在、Microsoft では、特に一般的な言語への翻訳に NMT を使用しています。 [音声間翻訳に使用できるすべての言語](language-support.md#speech-translation)で、NMT が導入されています。 音声テキスト変換では、言語ペアに応じて SMT または NMT を使用できます。 ターゲット言語が NMT によってサポートされている場合、翻訳全体が NMT によって処理されます。 ターゲット言語が NMT によってサポートされていない場合は、2 つの言語間の "ピボット" として英語を使用する NMT と SMT のハイブリッド翻訳となります。

## <a name="core-features"></a>コア機能

Speech SDK および REST API を通じて使用可能な機能を以下に示します。

| 使用事例 | SDK | REST |
|----------|-----|------|
| 認識結果を使用した音声テキスト翻訳。 | はい | いいえ |
| 音声間翻訳。 | はい | いいえ |
| 中間認識と翻訳結果。 | はい | いいえ |

## <a name="get-started-with-speech-translation"></a>音声翻訳を使ってみる

10 分もかからずにコードを実行できるように設計されたクイック スタートが用意されています。 以下の表は、音声翻訳のクイック スタートを言語別に整理して示したものです。

| クイック スタート | プラットフォーム | API リファレンス |
|------------|----------|---------------|
| [C#、.NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [[参照]](https://aka.ms/csspeech/csharpref) |
| [C#、.NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [[参照]](https://aka.ms/csspeech/csharpref) |
| [C#、UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [[参照]](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [[参照]](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows、Linux、macOS | [[参照]](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>サンプル コード

Speech SDK のサンプル コードは、GitHub 上で入手できます。 これらのサンプルでは、ファイルやストリームからの音声の読み取り、連続的および単発の認識/翻訳、カスタム モデルの使用など、一般的なシナリオについて説明されています。

* [音声テキスト変換と翻訳のサンプル (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>移行ガイド

アプリケーション、ツール、または製品で [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview) をご使用の方に向けて、Speech Service への移行に役立つガイドを作成しました。

* [Translator Speech API から Speech Service に移行する](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>リファレンス ドキュメント

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: 音声テキスト変換](rest-speech-to-text.md)
* [REST API: テキスト読み上げ](rest-text-to-speech.md)
* [REST API: 一括文字起こしとカスタマイズ](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>次のステップ

* [Speech サービスのサブスクリプション キーを無料で取得する](get-started.md)
* [Speech SDK を取得する](speech-sdk.md)
