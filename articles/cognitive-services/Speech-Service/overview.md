---
title: Speech Service とは
titleSuffix: Azure Cognitive Services
description: Speech Services は、音声テキスト変換、テキスト読み上げ、音声翻訳が 1 つの Azure サブスクリプションにまとめられたものです。 Speech SDK、Speech Devices SDK、または REST API を使用して、アプリケーション、ツール、デバイスに音声を簡単に追加できます。 既存のチャット ボットに音声機能を追加したり、翻訳アプリケーションでテキストを音声に変換したり、コール センターの大量のデータを文字起こししたりします。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: da88c6d0195236a6f93f81d3296e4624228125f2
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624271"
---
# <a name="what-are-the-speech-services"></a>Speech Service とは

Speech Services は、音声テキスト変換、テキスト読み上げ、音声翻訳が 1 つの Azure サブスクリプションにまとめられたものです。 [Speech SDK](speech-sdk-reference.md)、[Speech Devices SDK](https://aka.ms/sdsdk-quickstart)、または [REST API](rest-apis.md) を使用して、アプリケーション、ツール、デバイスを簡単に音声対応にできます。

> [!IMPORTANT]
> Speech Services は、Bing Speech API、Translator Speech、Custom Speech に代わるものです。 移行の手順については、" *「ハウツー ガイド」の「移行」* " をご覧ください。

Azure Speech Services は次の機能で構成されています。 各機能の一般的なユース ケースの詳細を学習したり、API リファレンスを参照したりするには、次の表のリンクを使用してください。

| Service | 機能 | 説明 | SDK | REST |
|---------|---------|-------------|-----|------|
| [ 音声テキスト変換 ](speech-to-text.md) | 音声テキスト変換 | 音声テキスト変換では、オーディオ ストリームからテキストへの文字起こしがリアルタイムで行われます。結果のテキストを、アプリケーション、ツール、またはデバイスで使用したり表示したりできます。 音声テキスト変換を [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) で使用し、文字起こしされた音声からユーザーの意図を抽出して、音声コマンドで対応します。 | [ はい ](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [ はい ](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [ バッチ文字起こし ](batch-transcription.md) | バッチ文字起こしでは、大量のデータの音声からテキストへの文字起こしを、非同期で行うことができます。 これは REST ベースのサービスであり、カスタマイズとモデルの管理と同じエンドポイントを使用します。 | いいえ | [ はい ](https://westus.cris.ai/swagger/ui/index) |
| | [ 会話の文字起こし ](conversation-transcription-service.md) | リアルタイムの音声認識、話者識別、およびダイアライゼーションが有効になります。 話者を区別することができるため、対面会議の文字起こしに最適です。 | はい | いいえ |
| | [ カスタム音声モデルの作成 ](#customize-your-speech-experience) | 独自環境での認識と文字起こしに音声テキスト変換を使用している場合は、カスタムの音響、言語、発音モデルを作成してトレーニングし、周囲の雑音や業界固有の語彙に対応できます。 | いいえ | [ はい ](https://westus.cris.ai/swagger/ui/index) |
| [Text-to-Speech](text-to-speech.md) | テキスト読み上げ | テキスト読み上げでは、[ 音声合成マークアップ言語 (SSML)](text-to-speech.md#speech-synthesis-markup-language-ssml) が使用され、入力テキストが人間のような合成音声に変換されます。 標準音声とニューラル音声から選択できます (「[ 言語のサポート ](language-support.md)」を参照)。 | [ はい ](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [ はい ](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [ カスタム音声の作成 ](#customize-your-speech-experience) | ブランドや製品に固有のカスタム音声フォントを作成します。 | いいえ | [ はい ](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [ 音声翻訳 ](speech-translation.md) | 音声翻訳 | 音声翻訳を使用すると、音声のリアルタイムの多言語翻訳がアプリケーション、ツール、デバイスで可能になります。 音声間翻訳や音声テキスト翻訳にはこのサービスを使用します。 | [ はい ](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | いいえ |
| [ 音声優先仮想アシスタント ](voice-first-virtual-assistants.md) | 音声優先仮想アシスタント | 開発者が Azure Speech Services を使用したカスタムの仮想アシスタントを使用すると、アプリケーションとエクスペリエンスに合わせて自然で人間のような会話型インターフェイスを作成できます。 Bot Framework の Direct Line Speech チャネルには、低待機時間と高信頼性で音声入力、音声出力の対話を可能にする、互換性のあるボットへの調整され、編成されたエントリ ポイントが用意されており、これらの機能が強化されます。 | [ はい ](voice-first-virtual-assistants.md) | いいえ |

## <a name="news-and-updates"></a>新機能と更新

Azure Speech Services の新機能について説明します。

* 2019 年 8 月
  * **新しいチュートリアル**: [Speech SDK を使用してボットを音声対応にする (C#)](tutorial-voice-enable-your-bot-speech-sdk.md)
  * `en-US-JessaNeural` 音声の新しい話し方である [`chat`](speech-synthesis-markup.md#adjust-speaking-styles) を追加しました。 
* 2019 年 6 月
  * Speech SDK 1.6.0 がリリースされました。 更新、拡張機能、既知の問題の完全な一覧については、「[ リリース ノート ](releasenotes.md)」を参照してください。
* 2019 年 5 月 - [ 会話の文字起こし ](conversation-transcription-service.md)、[ コール センター文字起こし ](call-center-transcription.md)、[ 音声優先仮想アシスタント ](voice-first-virtual-assistants.md) のドキュメントの公開を開始しました。
* 2019 年 5 月
  * Speech SDK 1.5.1 がリリースされました。 更新、拡張機能、既知の問題の完全な一覧については、「[ リリース ノート ](releasenotes.md)」を参照してください。
  * Speech SDK 1.5.0 がリリースされました。 更新、拡張機能、既知の問題の完全な一覧については、「[ リリース ノート ](releasenotes.md)」を参照してください。

## <a name="try-speech-services"></a>Speech Services を試してみる

ほとんどの一般的なプログラミング言語向けのクイック スタートが提供されており、いずれも 10 分かからずにコードを実行できるように作られています。 各機能の最も人気のあるクイック スタートを次の表に示します。 その他の言語やプラットフォームを調べるには、左側のナビゲーションを使用してください。

| 音声テキスト変換 (SDK) | テキスト読み上げ (SDK) | 翻訳 (SDK) |
|----------------------|----------------------|-------------------|
| [C#、.NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [C#、.NET Framework (Windows)](quickstart-text-to-speech-dotnet-windows.md) | [Java (Windows、Linux)](quickstart-translate-speech-java-jre.md) |
| [JavaScript (ブラウザー)](quickstart-js-browser.md) | [C++ (Windows)](quickstart-text-to-speech-cpp-windows.md) | [C#、.NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) |
| [Python (Windows、Linux、macOS)](quickstart-python.md) | [C++ (Linux)](quickstart-text-to-speech-cpp-linux.md) | [C#、.NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) |
| [Java (Windows、Linux)](quickstart-java-jre.md) | | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) |

> [!NOTE]
> 音声テキスト変換とテキスト読み上げには、REST エンドポイントとクイック スタートも用意されています。

Speech Services を使ってみた後、Speech SDK と LUIS を使用して音声から意図を認識する方法がわかるチュートリアルを試してください。

* [ チュートリアル:Speech SDK、LUIS、C# を使用して音声の意図を認識する ](how-to-recognize-intents-from-speech-csharp.md)
* [ チュートリアル:Speech SDK を使用してボットを音声対応にする (C#)](tutorial-voice-enable-your-bot-speech-sdk.md)
* [ チュートリアル:テキストを翻訳してセンチメントを分析し、翻訳済みテキストから音声を合成する Flask アプリを作成する (REST)](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>サンプル コードを取得する

各 Azure Speech Services のサンプル コードを、GitHub で入手できます。 これらのサンプルでは、ファイルやストリームからの音声の読み取り、連続的な認識と単発の認識、カスタム モデルの使用など、一般的なシナリオについて説明されています。 SDK と REST のサンプルを見るには、次のリンクを使用してください。

* [ 音声変換、テキスト読み上げ、および音声翻訳のサンプル (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [ バッチ文字起こしのサンプル (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [ テキスト読み上げのサンプル (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
* [ 音声優先仮想アシスタントのサンプル (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>音声エクスペリエンスをカスタマイズする

Azure Speech Services は組み込みのモデルで問題なく動作しますが、製品や環境に合わせてエクスペリエンスをさらにカスタマイズおよび調整したいことがあります。 カスタマイズ オプションは、音響モデルのチューニングから、独自ブランドに固有の音声フォントにまで及びます。 カスタム モデルを作成した後は、すべての Azure Speech Services でそれを使用できます。

| Speech Service | モデル | 説明 |
|----------------|-------|-------------|
| 音声テキスト変換 | [ 音響モデル ](how-to-customize-acoustic-models.md) | 自動車や工場など、それぞれに固有の録音条件がある特定の環境で使用されるアプリケーション、ツール、デバイス用のカスタム音響モデルを作成します。 たとえば、アクセント記号付きの音声、特定の背景ノイズ、録音に特定のマイクを使用する場合などです。 |
| | [ 言語モデル ](how-to-customize-language-model.md) | 医療用語や IT の専門用語など、フィールド固有のボキャブラリと文法の文字起こしを向上させるために、カスタム言語モデルを作成します。 |
| | [ 発音モデル ](how-to-customize-pronunciation.md) | カスタムの発音モデルを使用すると、発音形式と単語または用語の表示を定義できます。 製品名や頭字語などのカスタマイズされた用語を処理する場合に便利です。 始めるにあたって必要なのは、発音ファイル (単純な .txt ファイル) のみです。 |
| テキスト読み上げ | [ 音声フォント ](how-to-customize-voice-font.md) | カスタム音声フォントを使用すると、ブランド用に認識性の高い固有の音声を作成できます。 少量のデータだけで始めることができます。 提供するデータを増やすと、いっそう自然で人間のように聞こえる音声フォントになります。 |

## <a name="reference-docs"></a>リファレンス ドキュメント

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: 音声テキスト変換 ](rest-speech-to-text.md)
* [REST API: テキスト読み上げ ](rest-text-to-speech.md)
* [REST API: 一括文字起こしとカスタマイズ ](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Speech Service のサブスクリプション キーを無料で取得する ](get-started.md)
