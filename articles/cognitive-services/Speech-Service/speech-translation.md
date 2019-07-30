---
title: Azure Speech Services による音声翻訳
titlesuffix: Azure Cognitive Services
description: Speech Services を使用すると、音声のエンド ツー エンドでリアルタイムの多言語翻訳機能を、アプリケーション、ツール、デバイスに追加することができます。 同じ API を、音声間の翻訳と、音声テキスト変換の両方に使用できます。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 428dba9e8ff5ec072d5172fee357fd5319d04ad8
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657726"
---
# <a name="what-is-speech-translation"></a>音声翻訳とは何か

Azure Speech Services の音声翻訳では、オーディオ ストリームをリアルタイムに処理し、複数言語の音声間翻訳や音声テキスト翻訳を実行することができます。 Speech SDK を使用すると、アプリケーション、ツール、およびデバイスから、提供されたオーディオのソース トランスクリプションや翻訳出力にアクセスすることができます。 音声の検出中には中間トランスクリプションと翻訳結果が返され、最終的な結果は、合成された音声に変換することができます。

Microsoft の翻訳エンジンは、統計的機械翻訳 (SMT) とニューラル機械翻訳 (NMT) という 2 つのアプローチによって強化されています。 SMT では、高度な統計分析を使用して、いくつかの単語の文脈から考えられる最善の翻訳が推定されます。 NMT では、ニューラル ネットワークを使用して、文全体のコンテキストを踏まえた、正確で自然な翻訳が提供されます。

現在、Microsoft では、特に一般的な言語への翻訳に NMT を使用しています。 [音声間翻訳に使用できるすべての言語](language-support.md#speech-translation)で、NMT が導入されています。 音声テキスト変換では、言語ペアに応じて SMT または NMT を使用できます。 ターゲット言語が NMT によってサポートされている場合、翻訳全体が NMT によって処理されます。 ターゲット言語が NMT によってサポートされていない場合は、2 つの言語間の "ピボット" として英語を使用する NMT と SMT のハイブリッド翻訳となります。

## <a name="core-features"></a>コア機能

Speech SDK および REST API を通じて使用可能な機能を以下に示します。

| ユース ケース | SDK | REST |
|----------|-----|------|
| 認識結果を使用した音声テキスト翻訳。 | はい | いいえ |
| 音声間翻訳。 | はい | いいえ |
| 中間認識と翻訳結果。 | はい | いいえ |

## <a name="get-started-with-speech-translation"></a>音声翻訳を使ってみる

10 分もかからずにコードを実行できるように設計されたクイック スタートが用意されています。 以下の表は、音声翻訳のクイック スタートを言語別に整理して示したものです。

| クイック スタート | プラットフォーム | API リファレンス |
|------------|----------|---------------|
| [C#、.NET Core](quickstart-translate-speech-dotnetcore-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#、.NET Framework](quickstart-translate-speech-dotnetframework-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#、UWP](quickstart-translate-speech-uwp.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-translate-speech-cpp-windows.md) | Windows | [Browse](https://aka.ms/csspeech/cppref)|
| [Java](quickstart-translate-speech-java-jre.md) | Windows、Linux、macOS | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>サンプル コード

Speech SDK のサンプル コードは、GitHub 上で入手できます。 これらのサンプルでは、ファイルやストリームからの音声の読み取り、連続的および単発の認識/翻訳、カスタム モデルの使用など、一般的なシナリオについて説明されています。

* [音声テキスト変換と翻訳のサンプル (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>移行ガイド

アプリケーション、ツール、または製品で [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview) をご使用の方に向けて、Speech Services への移行に役立つガイドを作成しました。

* [Translator Speech API から Speech Services に移行する](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>リファレンス ドキュメント

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: 音声テキスト変換](rest-speech-to-text.md)
* [REST API: テキスト読み上げ](rest-text-to-speech.md)
* [REST API: 一括文字起こしとカスタマイズ](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>次の手順

* [Speech Services のサブスクリプション キーを無料で取得する](get-started.md)
* [Speech SDK を取得する](speech-sdk.md)
