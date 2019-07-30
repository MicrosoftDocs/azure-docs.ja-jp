---
title: Azure Speech Services によるテキスト読み上げ
titleSuffix: Azure Cognitive Services
description: Azure Speech Services のテキスト読み上げは、アプリケーション、ツール、デバイスは、テキストを人間のような自然な合成音声に変換できるサービスです。 音声は、標準音声およびニューラル音声から選択できますが、製品やブランドに固有のカスタム音声を独自に作成することもできます。 標準音声は、45 を超える言語およびロケールで 75 種類以上が用意されています。ニューラル音声は、4 つの言語およびロケールで 5 種類が用意されています。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: erhopf
ms.openlocfilehash: da7259585ad66ac9b58131ce834d82e7b3d4bcf2
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466961"
---
# <a name="what-is-text-to-speech"></a>テキスト読み上げの概要

Azure Speech Services のテキスト読み上げは、アプリケーション、ツール、デバイスは、テキストを人間のような自然な合成音声に変換できるサービスです。 音声は、標準音声およびニューラル音声から選択できますが、製品やブランドに固有のカスタム音声を独自に作成することもできます。 標準音声は、45 を超える言語およびロケールで 75 種類以上が用意されています。ニューラル音声は、4 つの言語およびロケールで 5 種類が用意されています。 詳細については、[サポートされる言語](language-support.md#text-to-speech)に関するページを参照してください。

テキスト読み上げテクノロジを使用すると、コンテンツ作成者は、ユーザーとさまざまな方法で対話できます。 テキスト読み上げでは、ユーザーが音声を使ってコンテンツと対話できるため、アクセシビリティが向上します。 視覚障碍や学習障碍をお持ちの方も、車の運転中にナビゲーション情報が必要な方も、テキスト読み上げを使用することにより、これまでのエクスペリエンスを向上させることができます。 テキスト読み上げは、音声ボットや仮想アシスタントにも役立つアドオンです。


音声合成マークアップ言語 (SSML) (XML ベースのマークアップ言語) を活用することにより、テキスト読み上げサービスを使用する開発者は、入力テキストを合成音声に変換する方法を指定できます。 SSML では、ピッチ、読み方、読み上げ速度、音量などを調整できます。 詳細については、[SSML](#speech-synthesis-markup-language-ssml) に関する記事を参照してください。

### <a name="standard-voices"></a>標準音声

標準音声は、統計的パラメトリック音声合成手法や連結音声合成手法を使用して作成されます。 このような音声は非常にわかりやすく、自然に聞こえます。 さまざまな音声オプションを使用して、アプリケーションが 45 を超える言語で簡単に話せるようにすることができます。 これらの音声は、略語、頭字語の展開、日付/時刻の解釈、多音字などのサポートを含め、高度な発音精度を実現しています。 標準音声を使用して、ユーザーが音声でコンテンツとやり取りできるようにすることで、アプリケーションとサービスのアクセシビリティを向上できます。

### <a name="neural-voices"></a>ニューラル音声

ニューラル音声は、話し言葉のアクセントとイントネーションのパターンを照合し、音声の単位をコンピューターの音声に合成する際に、ディープ ニューラル ネットワークを使用して従来のテキスト読み上げシステムの限界を克服しています。 標準のテキスト読み上げでは、独立したモデルによって管理される別々の言語分析と音響予測の手順に韻律が分解されます。これにより、こもった音声合成になることがあります。 ニューラル機能では韻律予測と音声合成が同時に行われ、その結果、より滑らかで自然に聞こえる音声になります。

ニューラル音声を使用すると、チャットボットや仮想アシスタントとの対話をより自然で魅力的なものにできます。また、電子書籍などのデジタル テキストをオーディオブックに変換したり、カーナビゲーション システムを強化したりすることもできます。 ニューラル音声では、人間のような自然な韻律と明瞭な発音により、ユーザーが AI システムとやりとりする際のリスニング疲労が大幅に軽減されます。

ニューラル音声は、ニュートラルや陽気な雰囲気など、さまざまなスタイルをサポートしています。 たとえば、Jessa (en-US) の声は陽気な雰囲気を表現でき、温かみのある楽しい会話に最適化されています。 トーン、ピッチ、速度などの音声出力は、[音声合成マークアップ言語](speech-synthesis-markup.md)を使用して調整できます。 利用可能なすべての音声の一覧については、[サポートされている言語](language-support.md#text-to-speech)に関するページを参照してください。

ニューラル音声の利点の詳細については、「[Microsoft's new neural text-to-speech service helps machines speak like people (Microsoft の新しいニューラル テキスト読み上げサービスを利用してマシンが人間のように話せるようにする)](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/)」を参照してください。

### <a name="custom-voices"></a>カスタム音声

音声をカスタマイズすることで、ブランド独自の認識性の高い音声を作成できます。 カスタム音声フォントを作成するには、スタジオで録音を行い、関連するスクリプトをトレーニング データとしてアップロードします。 サービスは、録音に合わせて調整された一意の音声モデルを作成します。 このカスタム音声フォントを使用して、音声を合成できます。 詳細については、[カスタム音声](how-to-customize-voice-font.md)に関するページを参照してください。

## <a name="speech-synthesis-markup-language-ssml"></a>音声合成マークアップ言語 (SSML)

音声合成マークアップ言語 (SSML) は、XML ベースのマークアップ言語であり、これにより、開発者はテキスト読み上げサービスを使用して、入力テキストを合成音声に変換する方法を指定することができます。 プレーンテキストと比較して、SSML では、開発者が音声合成出力のピッチ、読み方、読み上げ速度、音量などを微調整できます。 通常の句読点は、ピリオドの後の一時停止、または文が疑問符で終わるときの正しいイントネーションの使用など、自動的に処理されます。

テキスト読み上げサービスに送信されたテキスト入力はすべて、SSML として構造化される必要があります。 詳細については、「[音声合成マークアップ言語 (SSML)](speech-synthesis-markup.md)」を参照してください。

### <a name="pricing-note"></a>価格に関する注意

テキスト読み上げサービスを使用している場合、句読点を含めて、文字が音声に変換されるごとに課金されます。 SSML ドキュメント自体は課金対象外ですが、テキストが音声に変換される方法を調整するために使用される省略可能な要素 (音素やピッチなど) は、課金対象の文字としてカウントされます。 課金対象の一覧を次に示します。

* 要求の SSML 本文でテキスト読み上げサービスに渡されたテキスト
* `<speak>` と `<voice>` タグを除く、SSML 形式の要求本文のテキスト フィールド内のすべてのアークアップ
* 文字、句読点、スペース、タブ、マークアップ、すべての空白文字
* Unicode で定義されているすべてのコード ポイント

詳細については、[価格](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)に関するページを参照してください。

> [!IMPORTANT]
> 中国語、日本語、韓国語の言語文字はそれぞれ、2 文字としてカウントされ課金されます。

## <a name="core-features"></a>コア機能

以下の表は、テキスト読み上げのコア機能を示しています。

| ユース ケース | SDK | REST |
|----------|-----|------|
| テキストを音声に変換する。 | はい | はい |
| 音声適応のためのデータセットをアップロードする。 | いいえ | はい\* |
| 音声フォント モデルを作成および管理する。 | いいえ | はい\* |
| デプロイする音声フォントを作成および管理する。 | いいえ | はい\* |
| 音声フォント テストを作成および管理する。 | いいえ | はい\* |
| サブスクリプションを管理する。 | いいえ | はい\* |

\**これらのサービスは、cris.ai エンドポイントを使用して提供されます。[Swagger リファレンス](https://westus.cris.ai/swagger/ui/index)に関するページを参照してください。このようなカスタム音声トレーニングおよび管理の API では、要求を 5 秒間あたり 25 個に制限する調整が実装されます。一方、音声合成 API 自体では、最高値として 1 秒あたり 200 個の要求を許容する調整が実装されます。スロットリングが実行されると、その旨がメッセージ ヘッダーを介して通知されます。*

## <a name="get-started-with-text-to-speech"></a>テキスト読み上げを使ってみる

10 分もかからずにコードを実行できるように設計されたクイック スタートが用意されています。 以下の表は、テキスト読み上げのクイック スタートを言語別に整理して示したものです。

### <a name="sdk-quickstarts"></a>SDK のクイック スタート

| クイック スタート (SDK) | プラットフォーム | API リファレンス |
|------------|----------|---------------|
| [C#、.NET Core](quickstart-text-to-speech-dotnetcore.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#、.NET Framework](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#、UWP](quickstart-text-to-speech-csharp-uwp.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#、Unity](quickstart-text-to-speech-csharp-unity.md) | Windows、Android | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Browse](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Browse](https://aka.ms/csspeech/cppref) |

### <a name="rest-quickstarts"></a>REST クイック スタート

| クイック スタート (REST) | プラットフォーム | API リファレンス |
|------------|----------|---------------|
| [C#、.NET Core](quickstart-dotnet-text-to-speech.md) | Windows、macOS、Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.JS](quickstart-nodejs-text-to-speech.md) | Window、macOS、Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Window、macOS、Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>サンプル コード

テキスト読み上げのサンプル コードは、GitHub 上で入手できます。 これらのサンプルに含まれるテキスト読み上げ会話は、人気の高いプログラミング言語で作成されています。

* [テキスト読み上げのサンプル (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [テキスト読み上げのサンプル (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>リファレンス ドキュメント

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: 音声テキスト変換](rest-speech-to-text.md)
* [REST API: テキスト読み上げ](rest-text-to-speech.md)
* [REST API: 一括文字起こしとカスタマイズ](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>次の手順

* [無料の Speech Services サブスクリプションを取得する](get-started.md)
* [カスタム音声フォントを作成する](how-to-customize-voice-font.md)
