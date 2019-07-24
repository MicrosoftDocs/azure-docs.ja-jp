---
title: Azure Speech Service とは
titleSuffix: Azure Cognitive Services
description: Azure Speech Services は、音声テキスト変換、テキスト読み上げ、音声翻訳が 1 つの Azure サブスクリプションにまとめられたものです。 Speech SDK、Speech Devices SDK、または REST API を使用して、アプリケーション、ツール、デバイスに音声を簡単に追加できます。 既存のチャット ボットに音声機能を追加したり、翻訳アプリケーションでテキストを音声に変換したり、コール センターの大量のデータを文字起こししたりします。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 04/03/2019
ms.author: erhopf
ms.openlocfilehash: 61f22568aa6e6cf04963b40ad7c47163e87b9800
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/04/2019
ms.locfileid: "59010194"
---
# <a name="what-are-the-speech-services"></a>Speech Service とは

Azure Speech Services は、音声テキスト変換、テキスト読み上げ、音声翻訳が 1 つの Azure サブスクリプションにまとめられたものです。 [Speech SDK](speech-sdk-reference.md)、[Speech Devices SDK](speech-devices-sdk-qsg.md)、または [REST API](rest-apis.md) を使用して、アプリケーション、ツール、デバイスを簡単に音声対応にできます。

> [!IMPORTANT]
> Speech Services は、Bing Speech API、Translator Speech、Custom Speech に代わるものです。 移行の手順については、"*「ハウツー ガイド」の「移行」*" をご覧ください。

Azure Speech Services は次の機能で構成されています。 各機能の一般的なユース ケースの詳細を学習したり、API リファレンスを参照したりするには、次の表のリンクを使用してください。

| Service | 機能 | 説明 | SDK | REST |
|---------|---------|-------------|-----|------|
| [音声テキスト変換](speech-to-text.md) | 音声テキスト変換 | 音声テキスト変換では、オーディオ ストリームからテキストへの文字起こしがリアルタイムで行われます。結果のテキストを、アプリケーション、ツール、またはデバイスで使用したり表示したりできます。 音声テキスト変換を [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) で使用し、文字起こしされた音声からユーザーの意図を抽出して、音声コマンドで対応します。 | [はい](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [はい](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [バッチ文字起こし](batch-transcription.md) | バッチ文字起こしでは、大量のデータの音声からテキストへの文字起こしを、非同期で行うことができます。 これは REST ベースのサービスであり、カスタマイズとモデルの管理と同じエンドポイントを使用します。 | いいえ  | [はい](https://westus.cris.ai/swagger/ui/index) |
| | [カスタマイズ](#customize-your-speech-experience) | 独自環境での認識と文字起こしに音声テキスト変換を使用している場合は、カスタムの音響、言語、発音モデルを作成してトレーニングし、周囲の雑音や業界固有の語彙に対応できます。 | いいえ  | [はい](https://westus.cris.ai/swagger/ui/index) |
| [テキスト読み上げ](text-to-speech.md) | テキスト読み上げ | テキスト読み上げでは、入力テキストが人間のような合成音声に変換されます。 標準音声とニューラル音声から選択できます (「[言語のサポート](language-support.md)」を参照)。 | [はい](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [はい](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [カスタマイズ](#customize-your-speech-experience) | ブランドや製品に固有のカスタム音声フォントを作成します。 | いいえ  | [はい](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [音声翻訳](speech-translation.md) | 音声翻訳 | 音声翻訳を使用すると、音声のリアルタイムの多言語翻訳がアプリケーション、ツール、デバイスで可能になります。 音声間翻訳や音声テキスト翻訳にはこのサービスを使用します。 | [はい](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | いいえ  |

## <a name="news-and-updates"></a>新機能と更新

Azure Speech Services の新機能について説明します。

* 2019 年 4 月 - Windows および Linux における C++、C#、Java 向けにテキスト読み上げ (Beta) をサポートする Speech SDK 1.4.0 をリリースしました。 また、この SDK では、Linux における C++ および C# 向けとして、新たに MP3 および Opus/Ogg のオーディオ形式がサポートされます。 更新、拡張機能、既知の問題の完全な一覧については、「[リリース ノート](releasenotes.md)」を参照してください。 
* 2019 年 3 月 - 特定のリージョンで利用可能な音声の完全なリストを返す、テキスト読み上げ (TTS) の新しいエンドポイントが利用可能になりました。 加えて、TTS で新しいリージョンがサポートされます。 詳細については、[テキスト読み上げ API リファレンス (REST)](rest-text-to-speech.md) のページを参照してください。
* 2019年 2 月 - [Unity (ベータ)](quickstart-csharp-unity.md) をサポートする Speech SDK 1.3.0 がリリースされました。 `AudioInput` クラスのサポートが追加されました。このクラスでは、オーディオのストリーミング ソースを選択できます。 拡張機能と既知の問題の完全な一覧については、「[リリース ノート](releasenotes.md)」をご覧ください。
* 2018年 12 月 - [Python](quickstart-python.md)、[Node.js](quickstart-js-node.md)、および Ubuntu 18.04 LTS をサポートする Speech SDK 1.2.0 がリリースされました。 詳しくは、「[リリース ノート](releasenotes.md)」をご覧ください。

## <a name="try-speech-services"></a>Speech Services を試してみる

ほとんどの一般的なプログラミング言語向けのクイック スタートが提供されており、いずれも 10 分かからずにコードを実行できるように作られています。 各機能の最も人気のあるクイック スタートを次の表に示します。 その他の言語やプラットフォームを調べるには、左側のナビゲーションを使用してください。

| 音声テキスト変換 (SDK) | 翻訳 (SDK) | テキスト読み上げ (REST) | テキスト読み上げ (SDK) |
|-------------------|-------------------|-----------------------|-----------------------|
| [C#、.NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [Java (Windows または Linux)](quickstart-translate-speech-java-jre.md) | [Python (Windows、Linux、macOS)](quickstart-python-text-to-speech.md) | [C#、.NET Framework (Windows)](quickstart-text-to-speech-dotnet-windows.md) |
| [JavaScript (ブラウザー)](quickstart-js-browser.md) | [C#、.NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) | [C#、.NET Core (Windows、Linux、macOS)](quickstart-dotnet-text-to-speech.md) | [C++ (Windows)](quickstart-text-to-speech-cpp-windows.md) |
| [Python (Windows、Linux、macOS)](quickstart-python.md) | [C#、.NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) | [Node.js (Windows、Linux、macOS)](quickstart-nodejs-text-to-speech.md) | [C++ (Linux)](quickstart-text-to-speech-cpp-linux.md) |
| [Java (Windows または Linux)](quickstart-java-jre.md) | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) | |

Speech Services を使ってみた後、Speech SDK と LUIS を使用して音声から意図を認識する方法がわかるチュートリアルを試してください。

* [チュートリアル:Speech SDK、LUIS、C# を使用して音声の意図を認識する](how-to-recognize-intents-from-speech-csharp.md)

## <a name="get-sample-code"></a>サンプル コードを取得する

各 Azure Speech Services のサンプル コードを、GitHub で入手できます。 これらのサンプルでは、ファイルやストリームからの音声の読み取り、連続的な認識と単発の認識、カスタム モデルの使用など、一般的なシナリオについて説明されています。 SDK と REST のサンプルを見るには、次のリンクを使用してください。

* [音声変換、テキスト読み上げ、音声翻訳のサンプル (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [バッチ文字起こしのサンプル (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [テキスト読み上げのサンプル (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customize-your-speech-experience"></a>音声エクスペリエンスをカスタマイズする

Azure Speech Services は組み込みのモデルで問題なく動作しますが、製品や環境に合わせてエクスペリエンスをさらにカスタマイズおよび調整したいことがあります。 カスタマイズ オプションは、音響モデルのチューニングから、独自ブランドに固有の音声フォントにまで及びます。 カスタム モデルを作成した後は、すべての Azure Speech Services でそれを使用できます。

| Speech Service | モデル | 説明 |
|----------------|-------|-------------|
| 音声テキスト変換 | [音響モデル](how-to-customize-acoustic-models.md) | 自動車や工場など、それぞれに固有の録音条件がある特定の環境で使用されるアプリケーション、ツール、デバイス用のカスタム音響モデルを作成します。 たとえば、アクセント記号付きの音声、特定の背景ノイズ、録音に特定のマイクを使用する場合などです。 |
| | [言語モデル](how-to-customize-language-model.md) | 医療用語や IT の専門用語など、フィールド固有のボキャブラリと文法の文字起こしを向上させるために、カスタム言語モデルを作成します。 |
| | [発音モデル](how-to-customize-pronunciation.md) | カスタムの発音モデルを使用すると、発音形式と単語または用語の表示を定義できます。 製品名や頭字語などのカスタマイズされた用語を処理する場合に便利です。 始めるにあたって必要なのは、発音ファイル (単純な .txt ファイル) のみです。 |
| テキスト読み上げ | [音声フォント](how-to-customize-voice-font.md) | カスタム音声フォントを使用すると、ブランド用に認識性の高い固有の音声を作成できます。 少量のデータだけで始めることができます。 提供するデータを増やすと、いっそう自然で人間のように聞こえる音声フォントになります。 |

## <a name="reference-docs"></a>リファレンス ドキュメント

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API:音声テキスト変換](rest-speech-to-text.md)
* [REST API:テキスト読み上げ](rest-text-to-speech.md)
* [REST API:バッチ文字起こしとカスタマイズ](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Speech Services のサブスクリプション キーを無料で取得する](get-started.md)
