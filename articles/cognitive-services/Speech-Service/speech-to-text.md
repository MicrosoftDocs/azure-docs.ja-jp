---
title: Azure Speech Services による音声変換
titleSuffix: Azure Cognitive Services
description: Azure Speech Services の音声変換 (音声テキスト変換とも呼ばれる) を使用すると、音声ストリームをリアルタイムでテキストに変換できます。アプリケーション、ツール、デバイスでは、そのテキストを利用または表示したり、コマンド入力としてアクションを実行したりできます。 このサービスは、Microsoft が Cortana や Office 製品で使用するのと同じ認識テクノロジが採用されており、翻訳やテキスト読み上げの機能とシームレスに連携します。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: afe69d396c42023df8fcf5e4a6772771afc75c76
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606249"
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

ほとんどの一般的なプログラミング言語向けのクイック スタートが提供されており、いずれも 10 分かからずにコードを実行できるように作られています。 以下の表は、Speech SDK クイック スタートの詳細を言語別に整理して示したものです。

| クイック スタート | プラットフォーム | API リファレンス |
|------------|----------|---------------|
| [C#、.NET Core](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnetcore-windows) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#、.NET Framework](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnet-windows) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#、UWP](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-uwp) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-windows) | Windows | [Browse](https://aka.ms/csspeech/cppref)|
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-linux) | Linux | [Browse](https://aka.ms/csspeech/cppref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-android) | Android | [Browse](https://aka.ms/csspeech/javaref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-jre) | Windows、Linux、macOS | [Browse](https://aka.ms/csspeech/javaref) |
| [JavaScript、ブラウザー](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-browser) | Browser、Windows、Linux、macOS | [Browse](https://aka.ms/AA434tv) |
| [JavaScript、Node.js](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-node) | Windows、Linux、macOS | [Browse](https://aka.ms/AA434tv) |
| [Objective-C](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-objectivec-ios) | iOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| [Python](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-python) | Windows、Linux、macOS | [Browse](https://aka.ms/AA434tr)  |

音声変換 REST サービスを使用する場合は、[REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) に関するページを参照してください。

## <a name="tutorials-and-sample-code"></a>チュートリアルおよびサンプル コード

Speech Services を使ってみた後、Speech SDK と LUIS を使用して音声から意図を認識する方法がわかるチュートリアルを試してください。

* [チュートリアル:Speech SDK、LUIS、C# を使用して音声の意図を認識する](how-to-recognize-intents-from-speech-csharp.md)

Speech SDK のサンプル コードは、GitHub 上で入手できます。 これらのサンプルでは、ファイルやストリームからの音声の読み取り、連続的な認識と単発の認識、カスタム モデルの使用など、一般的なシナリオについて説明されています。

* [音声変換のサンプル (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [バッチ文字起こしのサンプル (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>カスタマイズ

Speech Services 音声サービスで使用される汎用モデルのほかに、エクスペリエンスに固有の音響、言語、および発音の各モデルをカスタムで作成できます。 カスタマイズ オプションの一覧を以下に示します。

| モデル | 説明 |
|-------|-------------|
| [音響モデル](how-to-customize-acoustic-models.md) | 自動車や工場のような特定の環境で、特定の記録条件の下、アプリケーション、ツール、またはデバイスを使用する場合は、カスタム音響モデルを作成すると便利です。 たとえば、アクセント記号付きの音声、特定の背景ノイズ、録音に特定のマイクを使用する場合などです。 |
| [言語モデル](how-to-customize-language-model.md) | 医療用語や IT の専門用語など、業界固有のボキャブラリと文法の文字起こしを向上させるには、カスタム言語モデルを作成します。 |
| [発音モデル](how-to-customize-pronunciation.md) | カスタムの発音モデルを使用すると、発音形式と単語または用語の表示を定義できます。 製品名や頭字語などのカスタマイズされた用語を処理する場合に便利です。 始めるにあたって必要なのは、発音ファイル (単純な .txt ファイル) のみです。 |

> [!NOTE]
> カスタマイズのオプションは、言語やロケールによって異なります ([サポートされる言語](supported-languages.md)に関するページを参照してください)。

## <a name="migration-guides"></a>移行ガイド

> [!WARNING]
> Bing Speech は、2019 年 10 月 15 日に使用停止になる予定です。

アプリケーション、ツール、または製品で Bing Speech API または Custom Speech をご使用の方に向けて、Speech Services への移行に役立つガイドを作成しました。

* [Bing Speech から Speech Service に移行する](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [Custom Speech から Speech Services への移行](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)に関するページ

## <a name="reference-docs"></a>リファレンス ドキュメント

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: 音声テキスト変換](rest-speech-to-text.md)
* [REST API: テキスト読み上げ](rest-text-to-speech.md)
* [REST API: 一括文字起こしとカスタマイズ](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>次の手順

* [Speech Services のサブスクリプション キーを無料で取得する](get-started.md)
* [Speech SDK を取得する](speech-sdk.md)
