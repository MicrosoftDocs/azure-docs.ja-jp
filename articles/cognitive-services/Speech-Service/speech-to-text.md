---
title: 音声変換 - Speech Service
titleSuffix: Azure Cognitive Services
description: 音声変換機能を使用すると、オーディオ ストリームをテキストにリアルタイムで文字起こしできます。 アプリケーション、ツール、またはデバイスで、このテキスト入力を使用、表示することや、このテキスト入力に基づきアクションを実行することができます。 このサービスは、テキスト読み上げ (音声合成) と音声翻訳機能とシームレスに連携します。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: 90a5ec8c58865c3aa1b210db51b9ffeb7169d641
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640414"
---
# <a name="what-is-speech-to-text"></a>音声変換の概要

Speech Service の音声変換 (音声認識とも呼ばれる) を使用すると、オーディオ ストリームをテキストにリアルタイムで文字起こしできます。 アプリケーション、ツール、またはデバイスで、このテキストを使用、表示することや、このテキストをコマンド入力としてアクションを実行することができます。 このサービスでは、Microsoft が Cortana や Office 製品に対して使用するのと同じ認識テクノロジが採用されています。 これは、<a href="./speech-translation.md" target="_blank">翻訳 <span class="docon docon-navigate-external x-hidden-focus"></span></a> および<a href="./text-to-speech.md" target="_blank">テキスト読み上げ <span class="docon docon-navigate-external x-hidden-focus"></span></a> サービス オファリングとシームレスに連携します。 使用可能な音声変換の言語の詳細については、[サポートされる言語](language-support.md#speech-to-text)に関するページを参照してください。

既定では、音声変換サービスでは、汎用言語モデルが使用されます。 このモデルは、Microsoft が所有するデータでトレーニングされて、クラウドにデプロイされています。 このモデルは、会話や口述のシナリオに最適です。 独自の環境で認識と文字起こしのために音声変換を使用している場合は、カスタムの音響、言語、および発音モデルを作成してトレーニングできます。 カスタマイズは、アンビエント ノイズや業界固有の語彙に対処する場合に役立ちます。

追加の参照テキストを入力として使用する音声変換サービスでは、音声の発音を評価するための[発音評価](rest-speech-to-text.md#pronunciation-assessment-parameters)機能も有効化されており、発話音声の精度と流暢性に関するフィードバックを話者に提供します。 言語学習者は、発音評価を使用して練習を行い、即座にフィードバックを得て、発音を改善することができます。そのため、自信を持って話し、発表することができます。 教師がこの機能を使用すれば、複数の話者の発音をリアルタイムに評価することができます。 現在、この機能は米国英語をサポートしており、専門家によって行われる音声評価との高度な相関関係があります。

> [!NOTE]
> Bing Speech は、2019 年 10 月 15 日に使用が停止されました。 アプリケーション、ツール、または製品で Bing Speech API を使用している場合、Speech サービスへの移行に役立つガイドが作成されています。
> - [Bing Speech から Speech Service に移行する](how-to-migrate-from-bing-speech.md)

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="get-started-with-speech-to-text"></a>音声変換の概要

音声変換サービスは、[Speech SDK](speech-sdk.md) を介して利用できます。 さまざまな言語とプラットフォームで、いくつかの一般的なシナリオがクイックスタートとして用意されています。

 - [クイック スタート: マイク入力を使用して音声を認識する](quickstarts/speech-to-text-from-microphone.md)
 - [クイック スタート: ファイルから音声を認識する](quickstarts/speech-to-text-from-file.md)
 - [クイック スタート: BLOB ストレージに格納された音声を認識する](quickstarts/from-blob.md)

音声変換 REST サービスを使用する場合は、[REST API](rest-speech-to-text.md) に関するページを参照してください。

 - [クイック スタート: 参照入力による発音評価](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="tutorials-and-sample-code"></a>チュートリアルおよびサンプル コード

Speech Service を使ってみた後、Speech SDK と LUIS を使用して音声から意図を認識する方法がわかるチュートリアルを試してください。

- [チュートリアル:C# を使用して、Speech SDK と LUIS で音声の意図を認識する](how-to-recognize-intents-from-speech-csharp.md)

Speech SDK のサンプル コードは、GitHub 上で入手できます。 これらのサンプルでは、ファイルやストリームからの音声の読み取り、連続的な認識と単発の認識、カスタム モデルの使用など、一般的なシナリオについて説明されています。

- [音声変換のサンプル (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [バッチ文字起こしのサンプル (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [発音評価サンプル (REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>カスタマイズ

標準の Speech Service モデルに加えて、カスタム モデルも作成できます。 カスタマイズにより、話し方、ボキャブラリ、背景ノイズなどの音声認識の障壁を克服できます。「[カスタム音声](how-to-custom-speech.md)」を参照してください。 カスタマイズのオプションは、言語やロケールによって異なります。[サポートされる言語](supported-languages.md)に関するページを参照して、サポートについて確認してください。

## <a name="batch-transcription"></a>バッチ文字起こし

バッチ文字起こしは、ストレージ内の大量のオーディオを文字起こしできる一連の REST API 操作です。 Shared Access Signatures (SAS) URI でオーディオ ファイルを示して、非同期に文字起こしの結果を受け取ることができます。 Batch Transcription API の使用方法の詳細については、[使用方法](batch-transcription.md)に関するページを参照してください。

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>次のステップ

- [Speech サービスのサブスクリプション キーを無料で取得する](get-started.md)
- [Speech SDK を取得する](speech-sdk.md)
