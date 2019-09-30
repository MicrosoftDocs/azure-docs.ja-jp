---
title: 音声テキスト変換 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech Service の音声変換機能 (音声テキスト変換とも呼ばれる) を使用すると、音声ストリームをリアルタイムでテキストに変換できます。アプリケーション、ツール、デバイスでは、そのテキストを利用または表示したり、コマンド入力としてアクションを実行したりできます。 このサービスは、Microsoft が Cortana や Office 製品で使用するのと同じ認識テクノロジが採用されており、翻訳やテキスト読み上げの機能とシームレスに連携します。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: fa80ebea93ae897ba01ff54bdb797ed4194665cd
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70068859"
---
# <a name="what-is-speech-to-text"></a>音声変換の概要

Azure Speech Services の音声変換 (音声テキスト変換とも呼ばれる) を使用すると、音声ストリームをリアルタイムでテキストに変換できます。アプリケーション、ツール、デバイスでは、そのテキストを利用または表示したり、コマンド入力としてアクションを実行したりできます。 このサービスは、Microsoft が Cortana や Office 製品で使用するのと同じ認識テクノロジが採用されており、翻訳やテキスト読み上げの機能とシームレスに連携します。  使用可能な音声変換の言語の詳細については、[サポートされる言語](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text)に関するページを参照してください。

既定では、音声変換サービスでは、汎用言語モデルが使用されます。 このモデルは、Microsoft が所有するデータでトレーニングされて、クラウドにデプロイされています。 このモデルは、会話や口述のシナリオに最適です。 独自環境での認識と文字起こしに音声テキスト変換を使用している場合は、カスタムの音響、言語、発音モデルを作成してトレーニングし、周囲の雑音や業界固有の語彙に対応できます。

Speech SDK や REST API を使用することにより、マイクからの音声のキャプチャや、ストリームからの読み取り、ストレージからの音声ファイルへのアクセスを簡単行うことができます。 Speech SDK では、音声認識用として、WAV/PCM 16 ビット、16 kHz/8 kHz、単一チャネル オーディオがサポートされています。 [音声変換 REST エンドポイント](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) や [バッチ文字起こしサービス](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats)を使用したオーディオ形式もサポートされています。

## <a name="core-features"></a>コア機能

Speech SDK および REST API を通じて使用可能な機能を以下に示します。

| ユース ケース | SDK | REST |
|----------|-----|------|
| 短い発話の文字起こし (15 秒未満)。 最終的な文字起こしの結果のみがサポートされます。 | はい | はい |
| 長い発話とストリーミング音声の継続的な文字起こし (15 秒超)。 中間および最終の文字起こしの結果がサポートされます。 | はい | いいえ |
| [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis) の使用による認識結果からの意図の抽出。 | はい | いいえ\* |
| 音声ファイルの非同期での一括文字起こし。 | いいえ | はい\** |
| 音声モデルの作成および管理。 | いいえ | はい\** |
| カスタム モデル デプロイの作成および管理。 | いいえ | はい\** |
| 正確性テストを作成し、ベースライン モデルとカスタム モデルの正確性を測定して比較する。 | いいえ | はい\** |
| サブスクリプションの管理。 | いいえ | はい\** |

\* *LUIS の意図とエンティティは、個別の LUIS サブスクリプションを使用して派生させることができます。このサブスクリプションでは、SDK で LUIS を自動的に呼び出し、エンティティおよび意図の結果を提供できます。REST API の場合は、LUIS を自分で呼び出すことで、LUIS サブスクリプションを使用して意図とエンティティを派生させることができます。*

\** *これらのサービスは、cris.ai エンドポイントを使用して提供されます。[Swagger リファレンス](https://westus.cris.ai/swagger/ui/index)に関するページを参照してください。*

## <a name="get-started-with-speech-to-text"></a>音声変換の概要

ほとんどの一般的なプログラミング言語向けのクイック スタートが提供されており、いずれも 10 分かからずにコードを実行できるように作られています。 [次の表](https://aka.ms/csspeech#5-minute-quickstarts)は、プラットフォームと言語別に整理された Speech SDK クイック スタートの完全な一覧です。  [こちら](https://aka.ms/csspeech#reference)で API リファレンスも参照できます。

音声変換 REST サービスを使用する場合は、[REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) に関するページを参照してください。

## <a name="tutorials-and-sample-code"></a>チュートリアルおよびサンプル コード

Speech Services を使ってみた後、Speech SDK と LUIS を使用して音声から意図を認識する方法がわかるチュートリアルを試してください。

* [チュートリアル:Speech SDK、LUIS、C# を使用して音声の意図を認識する](how-to-recognize-intents-from-speech-csharp.md)

Speech SDK のサンプル コードは、GitHub 上で入手できます。 これらのサンプルでは、ファイルやストリームからの音声の読み取り、連続的な認識と単発の認識、カスタム モデルの使用など、一般的なシナリオについて説明されています。

* [音声変換のサンプル (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [バッチ文字起こしのサンプル (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>カスタマイズ

音声サービスで使用される標準のベースライン モデルに加えて、使用可能なデータを使用してニーズに合わせてモデルをカスタマイズしたり、会話のスタイル、語彙、周囲の雑音など、音声認識の障壁となっている課題を解消したりすることができます。「[Custom Speech とは](how-to-custom-speech.md)」を参照してください。

> [!NOTE]
> カスタマイズのオプションは、言語やロケールによって異なります ([サポートされる言語](supported-languages.md)に関するページを参照してください)。

## <a name="migration-guides"></a>移行ガイド

> [!WARNING]
> Bing Speech は、2019 年 10 月 15 日に使用停止になる予定です。

アプリケーション、ツール、または製品で Bing Speech API または Custom Speech をご使用の方に向けて、Speech Services への移行に役立つガイドを作成しました。

* [Bing Speech から Speech Service に移行する](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [Custom Speech から Speech Services への移行](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)に関するページ

## <a name="reference-docs"></a>リファレンス ドキュメント

* [Speech SDK](https://aka.ms/csspeech)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: 音声テキスト変換](rest-speech-to-text.md)
* [REST API: テキスト読み上げ](rest-text-to-speech.md)
* [REST API: 一括文字起こしとカスタマイズ](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>次の手順

* [Speech Services のサブスクリプション キーを無料で取得する](get-started.md)
* [Speech SDK を取得する](speech-sdk.md)
