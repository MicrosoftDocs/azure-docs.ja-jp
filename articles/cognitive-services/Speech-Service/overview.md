---
title: 音声サービスとは
titleSuffix: Azure Cognitive Services
description: Speech Service は、音声テキスト変換、テキスト読み上げ、音声翻訳が 1 つの Azure サブスクリプションにまとめられたものです。 Speech SDK、Speech Devices SDK、または REST API を使用して、アプリケーション、ツール、デバイスに音声を追加します。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 06/25/2020
ms.author: trbye
ms.openlocfilehash: 82099172a933496f015ae8fc575c1919a879e1f9
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167744"
---
# <a name="what-is-the-speech-service"></a>音声サービスとは

音声サービスは、音声テキスト変換、テキスト読み上げ、音声翻訳が 1 つの Azure サブスクリプションにまとめられたものです。 [Speech CLI](spx-overview.md)、[Speech SDK](speech-sdk-reference.md)、[Speech Devices SDK](https://aka.ms/sdsdk-quickstart)、[Speech Studio](https://speech.microsoft.com/)、または [REST API](rest-apis.md) を使用して、アプリケーション、ツール、デバイスを簡単に音声対応にできます。

> [!IMPORTANT]
> Speech Service は、Bing Speech API と Translator Speech に代わるものです。 移行の手順については、" _「ハウツー ガイド」の「移行」_ " をご覧ください。

Speech Service は次の機能で構成されています。 各機能の一般的なユース ケースの詳細を学習したり、API リファレンスを参照したりするには、次の表のリンクを使用してください。

| サービス | 機能 | 説明 | SDK | REST |
|---------|---------|-------------|-----|------|
| [音声テキスト変換](speech-to-text.md) | リアルタイムの音声テキスト変換 | 音声テキスト変換では、オーディオ ストリームまたはローカル ファイルからテキストへの文字起こしまたは翻訳がリアルタイムで行われ、結果のテキストをアプリケーション、ツール、またはデバイスで使用したり表示したりできます。 音声テキスト変換を [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) で使用し、文字起こしされた音声からユーザーの意図を抽出して、音声コマンドで対応します。 | [はい](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [はい](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [一括音声テキスト変換](batch-transcription.md) | 一括音声テキスト変換では、Azure Blob Storage に格納された大量の音声データを対象に、音声からテキストへの文字起こしを非同期で行うことができます。 音声からテキストへの変換に加え、一括音声テキスト変換は、ダイアライゼーションや感情分析にも対応します。 | いいえ | [はい](https://westus.cris.ai/swagger/ui/index) |
| | [マルチデバイスの会話](multi-device-conversation.md) | 会話の中で複数のデバイスまたはクライアントを接続して、音声ベースまたはテキストベースのメッセージを送信し、文字起こしや翻訳を容易に実現します。| はい | いいえ |
| | [会話の文字起こし](conversation-transcription-service.md) | リアルタイムの音声認識、話者識別、およびダイアライゼーションが有効になります。 話者を区別することができるため、対面会議の文字起こしに最適です。 | はい | いいえ |
| | [カスタム音声モデルの作成](#customize-your-speech-experience) | 独自環境での認識と文字起こしに音声テキスト変換を使用している場合は、カスタムの音響、言語、発音モデルを作成してトレーニングし、周囲の雑音や業界固有の語彙に対応できます。 | いいえ | [はい](https://westus.cris.ai/swagger/ui/index) |
| [音声合成](text-to-speech.md) | テキスト読み上げ | テキスト読み上げでは、[音声合成マークアップ言語 (SSML)](speech-synthesis-markup.md) が使用され、入力テキストが人間のような合成音声に変換されます。 標準音声とニューラル音声から選択できます (「[言語のサポート](language-support.md)」を参照)。 | [はい](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [はい](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Custom Voice の作成](#customize-your-speech-experience) | ブランドや製品に固有のカスタム音声フォントを作成します。 | いいえ | [はい](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [音声翻訳](speech-translation.md) | 音声翻訳 | 音声翻訳を使用すると、音声のリアルタイムの多言語翻訳がアプリケーション、ツール、デバイスで可能になります。 音声間翻訳や音声テキスト翻訳にはこのサービスを使用します。 | [はい](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | いいえ |
| [音声アシスタント](voice-assistants.md) | 音声アシスタント | Speech Service を使用した音声アシスタントにより、開発者は、そのアプリケーションとエクスペリエンスのための自然で人間のような会話型インターフェイスを作成できます。 音声アシスタント サービスは、Bot Framework の Direct Line Speech チャネルまたは統合カスタム コマンド (プレビュー) サービスを使用してタスクを遂行するアシスタントの実装とデバイスとの間に、高速で信頼性の高い対話を実現します。 | [はい](voice-assistants.md) | いいえ |
| [Speaker Recognition](speaker-recognition-overview.md) | 話者の検証と識別 | Speaker Recognition サービスは、固有の音声特性によって話者を検証および識別するアルゴリズムを提供します。 Speaker Recognition は、"だれが話しているのか" という質問に回答するために使用されます。 | はい | [はい](https://docs.microsoft.com/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service"></a>Speech Service を試す

ほとんどの一般的なプログラミング言語向けのクイック スタートが提供されており、いずれも 10 分もかからずにコードを実行できるように作られています。 各機能の最も人気のあるクイック スタートを次の表に示します。 その他の言語やプラットフォームを調べるには、左側のナビゲーションを使用してください。

| 音声テキスト変換 (SDK) | テキスト読み上げ (SDK) | 翻訳 (SDK) |
|----------------------|----------------------|-------------------|
| [オーディオ ファイルから音声を認識する](quickstarts/speech-to-text-from-file.md) | [オーディオ ファイルに音声を合成する](quickstarts/text-to-speech-audio-file.md) | [音声をテキストに変換する](quickstarts/translate-speech-to-text.md) |
| [マイクを使用して音声を認識する](quickstarts/speech-to-text-from-microphone.md) | [スピーカーに音声を合成する](quickstarts/text-to-speech.md) | [音声を複数のターゲット言語に翻訳する](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [BLOB ストレージに格納された音声を認識する](quickstarts/from-blob.md) | [長い形式の音声の非同期合成](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [音声間翻訳](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> 音声テキスト変換とテキスト読み上げには、REST エンドポイントとクイックスタートも用意されています。

音声サービスを使用する機会が得られたら、さまざまなシナリオを解決する方法を説明するチュートリアルを試してみてください。

- [チュートリアル:Speech SDK、LUIS、C# を使用して音声の意図を認識する](how-to-recognize-intents-from-speech-csharp.md)
- [チュートリアル:Speech SDK を使用してボットを音声対応にする (C#)](tutorial-voice-enable-your-bot-speech-sdk.md)
- [チュートリアル:テキストを翻訳してセンチメントを分析し、翻訳済みテキストから音声を合成する Flask アプリを作成する (REST)](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>サンプル コードを取得する

Speech Service のサンプル コードは、GitHub 上で入手できます。 これらのサンプルでは、ファイルやストリームからの音声の読み取り、連続的な認識と単発の認識、カスタム モデルの使用など、一般的なシナリオについて説明されています。 SDK と REST のサンプルを見るには、次のリンクを使用してください。

- [音声変換、テキスト読み上げ、および音声翻訳のサンプル (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [バッチ文字起こしのサンプル (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [テキスト読み上げのサンプル (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [音声アシスタントのサンプル (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>音声エクスペリエンスをカスタマイズする

Speech Service は組み込みのモデルで問題なく動作しますが、製品や環境に合わせてエクスペリエンスをさらにカスタマイズおよび調整したいことがあります。 カスタマイズ オプションは、音響モデルのチューニングから、独自ブランドに固有の音声フォントにまで及びます。

| Speech Service | プラットフォーム | 説明 |
| -------------- | -------- | ----------- |
| 音声テキスト変換 | [カスタム音声](https://aka.ms/customspeech) | 音声認識モデルをニーズと使用可能なデータに合わせてカスタマイズします。 話し方、ボキャブラリ、背景ノイズといった音声認識の障壁を克服可能です。 |
| テキスト読み上げ | [Custom Voice](https://aka.ms/customvoice) | お客様のスピーチ データを使用して、テキスト読み上げアプリ用の認識可能な独自の音声を作成します。 音声パラメーターのセットを調整することで、音声出力をさらに細かく調整できます。 |

## <a name="reference-docs"></a>リファレンス ドキュメント

- [Speech SDK](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [REST API: 音声テキスト変換](rest-speech-to-text.md)
- [REST API: テキスト読み上げ](rest-text-to-speech.md)
- [REST API: 一括文字起こしとカスタマイズ](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Speech サービスのサブスクリプション キーを無料で取得する](get-started.md)
