---
title: Speech サービス API 向けの Docker コンテナーをインストールし、実行する
titleSuffix: Azure Cognitive Services
description: 音声サービスの Docker コンテナーを使用し、音声認識、文字起こし、生成などをオンプレミスで実行します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
ms.custom: cog-serv-seo-aug-2020
keywords: オンプレミス、Docker、コンテナー
ms.openlocfilehash: 1eb8e6d990b0b3e6212736036466be9f11d05b01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102201126"
---
# <a name="install-and-run-docker-containers-for-the-speech-service-apis"></a>Speech サービス API 向けの Docker コンテナーをインストールし、実行する 

コンテナーを使用すると、独自の環境で一部の Speech サービス API を実行できます。 コンテナーは、特定のセキュリティ要件とデータ ガバナンス要件に適しています。 この記事では、Speech コンテナーをダウンロード、インストール、実行する方法について説明します。

Speech コンテナーでは、堅牢なクラウド機能とエッジの局所性の両方のために最適化された音声アプリケーション アーキテクチャを構築できます。 利用できるコンテナーはいくつかあり、クラウド ベースの Azure Speech Services と同じ[価格](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)が使用されます。


> [!IMPORTANT]
> 現在、次の音声コンテナーが一般公開されています。
> * 標準音声変換
> * カスタム音声変換
> * 標準テキスト読み上げ
> * ニューラル テキスト読み上げ
>
> 次の音声コンテナーは、限定的なプレビュー段階にあります。
> * カスタム テキスト読み上げ
> * 音声言語検出 
>
> 音声コンテナーを使用するには、オンライン要求を送信し、承認を受けている必要があります。 詳細については、以下の「**コンテナーを実行するための承認を要求する**」セクションを参照してください。

| コンテナー | 特徴 | 最新 |
|--|--|--|
| 音声テキスト変換 | 中間結果を使用して、センチメントを分析し、リアルタイムの音声録音またはバッチ音声録音を文字起こしします。  | 2.10.0 |
| カスタム音声変換 | [Custom Speech ポータル](https://speech.microsoft.com/customspeech)のカスタム モデルを利用し、連続するリアルタイムの音声またはバッチ音声録音を、中間結果を含むテキストに文字起こしします。 | 2.10.0 |
| テキスト読み上げ | テキストを、プレーンテキストの入力または音声合成マークアップ言語 (SSML) を含む自然な音声に変換します。 | 1.12.0 |
| カスタム テキスト読み上げ | [Custom Voice ポータル](https://aka.ms/custom-voice-portal)のカスタム モデルを利用し、テキストを、プレーンテキストの入力または音声合成マークアップ言語 (SSML) を含む自然な音声に変換します。 | 1.12.0 |
| 音声言語検出 | オーディオ ファイルで話されている言語を検出します。 | 1.0 |
| Neural Text-to-speech | ディープ ニューラル ネットワーク テクノロジを使用してテキストを自然な響きの音声に変換することで、合成音声がより自然なものになります。 | 1.4.0 |

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。

## <a name="prerequisites"></a>前提条件

Speech コンテナーを使用する前の前提条件は次のとおりです。

| 必須 | 目的 |
|--|--|
| Docker エンジン | [ホスト コンピューター](#the-host-computer)に Docker エンジンをインストールしておく必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上で Docker 環境の構成を行うパッケージが用意されています。 Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。<br><br> コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 <br><br> **Windows では**、Linux コンテナーをサポートするように Docker を構成することも必要です。<br><br> |
| Docker に関する知識 | レジストリ、リポジトリ、コンテナー、コンテナー イメージなど、Docker の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。 |
| Speech リソース | これらのコンテナーを使用するためには、以下が必要です。<br><br>関連付けられている API キーとエンドポイント URI を取得するための Azure _Speech_ リソース。 どちらの値も、Azure portal の **Speech** の [概要] ページと [キー] ページで確認できます。 コンテナーを起動するには、両方が必要です。<br><br>**{API_KEY}** : **[キー]** ページにある 2 つの利用可能なリソース キーのどちらか<br><br>**{ENDPOINT_URI}** : **[概要]** ページに提示されているエンドポイント |

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>ホスト コンピューター

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Advanced Vector Extension のサポート

**ホスト** とは、Docker コンテナーを実行するコンピューターのことです。 ホストは、[高度なベクター拡張機能](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2) を *サポートしている必要があります*。 次のコマンドを使用して、Linux ホストでの AVX2 サポートを確認できます。

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> AVX2 をサポートするにはホスト コンピューターが *必須* です。 AVX2 サポートがないと、コンテナーは正しく機能 *しません*。

### <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

次の表に、各 Speech コンテナーに割り当てるリソースの最小値と推奨値を示します。

| コンテナー | 最小値 | 推奨 |
|-----------|---------|-------------|
| 音声テキスト変換 | 2 コア、2 GB のメモリ | 4 コア、4 GB メモリ |
| カスタム音声変換 | 2 コア、2 GB のメモリ | 4 コア、4 GB メモリ |
| テキスト読み上げ | 1 コア、2 GB メモリ | 2 コア、3 GB のメモリ |
| カスタム テキスト読み上げ | 1 コア、2 GB メモリ | 2 コア、3 GB のメモリ |
| 音声言語検出 | 1 コア、1 GB のメモリ | 1 コア、1 GB のメモリ |
| Neural Text-to-speech | 6 コア、12 GB のメモリ | 8 コア、16 GB のメモリ |

* 各コアは少なくとも 2.6 ギガヘルツ (GHz) 以上にする必要があります。

コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

> [!NOTE]
> 最小および推奨値は、Docker の制限に基づくもので、ホスト マシンのリソースに基づくものでは *ありません*。 たとえば、音声テキスト変換コンテナーは、大規模な言語モデルの一部をメモリ マップするため、ファイル全体がメモリに収まるようにすることを "*お勧めします*"。これには、追加で 4 から 6 GB が必要です。 また、モデルがメモリにページングされているため、どちらかのコンテナーの最初の実行に時間がかかる場合があります。

## <a name="request-approval-to-the-run-the-container"></a>コンテナーを実行するための承認を要求する

コンテナーへのアクセスを要求するには、[要求フォーム](https://aka.ms/csgate)に記入して送信します。 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]


## <a name="get-the-container-image-with-docker-pull"></a>`docker pull` によるコンテナー イメージの取得

Speech のコンテナー イメージは、次のコンテナー レジストリで入手できます。

# <a name="speech-to-text"></a>[音声テキスト変換](#tab/stt)

| コンテナー | リポジトリ |
|-----------|------------|
| 音声テキスト変換 | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[カスタム音声変換](#tab/cstt)

| コンテナー | リポジトリ |
|-----------|------------|
| カスタム音声変換 | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[テキスト読み上げ](#tab/tts)

| コンテナー | リポジトリ |
|-----------|------------|
| テキスト読み上げ | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest` |

# <a name="neural-text-to-speech"></a>[Neural Text-to-speech](#tab/ntts)

| コンテナー | リポジトリ |
|-----------|------------|
| Neural Text-to-speech | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[カスタム テキスト読み上げ](#tab/ctts)

| コンテナー | リポジトリ |
|-----------|------------|
| カスタム テキスト読み上げ | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest` |

# <a name="speech-language-detection"></a>[音声言語検出](#tab/lid)

> [!TIP]
> 最も役に立つ結果を得るために、音声テキスト変換またはカスタム音声テキスト変換コンテナーと共に音声言語検出コンテナーを使用することをお勧めします。 

| コンテナー | リポジトリ |
|-----------|------------|
| 音声言語検出 | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Speech コンテナー用の docker pull

# <a name="speech-to-text"></a>[音声テキスト変換](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Speech-to-text コンテナー用の docker pull

Microsoft Container Registry からコンテナー イメージをダウンロードするには、[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用します。

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest
```

> [!IMPORTANT]
> `latest` タグにより、`en-US` ロケールがプルされます。 追加のロケールについては、「[音声テキスト変換ロケール](#speech-to-text-locales)」を参照してください。

#### <a name="speech-to-text-locales"></a>音声テキスト変換ロケール

`latest` を除くすべてのタグは、次の形式であり、大文字と小文字が区別されます。

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

次のタグは、この形式の例です。

```
2.6.0-amd64-en-us
```

**音声テキスト変換** コンテナーのサポートされている全ロケールについては、「[音声テキスト変換イメージ タグ](../containers/container-image-tags.md#speech-to-text)」を参照してください。

# <a name="custom-speech-to-text"></a>[カスタム音声変換](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>カスタム音声変換コンテナー用の docker pull

Microsoft Container Registry からコンテナー イメージをダウンロードするには、[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用します。

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest
```

> [!NOTE]
> Custom Speech コンテナーの `locale` と `voice` は、コンテナーによって取り込まれるカスタム モデルによって決定されます。

# <a name="text-to-speech"></a>[テキスト読み上げ](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Text-to-speech コンテナー用の docker pull

Microsoft Container Registry からコンテナー イメージをダウンロードするには、[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用します。

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest
```

> [!IMPORTANT]
> `latest` タグにより、`en-US` ロケールと `ariarus` 音声がプルされます。 追加のロケールについては、「[テキスト読み上げロケール](#text-to-speech-locales)」を参照してください。

#### <a name="text-to-speech-locales"></a>テキスト読み上げロケール

`latest` を除くすべてのタグは、次の形式であり、大文字と小文字が区別されます。

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

次のタグは、この形式の例です。

```
1.8.0-amd64-en-us-ariarus
```

サポートされているロケールと **テキスト読み上げ** コンテナーの対応音声についてはすべて、「[テキスト読み上げイメージ タグ](../containers/container-image-tags.md#text-to-speech)」を参照してください。

> [!IMPORTANT]
> *Text-to-speech* HTTP POST を構築するときは、[Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md) メッセージには、`name` 属性を含む `voice` 要素が必要になります。 値はそれに対応するコンテナーのロケールと音声であり、["短い名前"](language-support.md#standard-voices) とも呼ばれています。 たとえば、`latest` タグには `en-US-AriaRUS` という音声名が与えられます。

# <a name="neural-text-to-speech"></a>[Neural Text-to-speech](#tab/ntts)

#### <a name="docker-pull-for-the-neural-text-to-speech-container"></a>Neural Text-to-speech コンテナー用の docker pull

Microsoft Container Registry からコンテナー イメージをダウンロードするには、[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用します。

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest
```

> [!IMPORTANT]
> `latest` タグにより、`en-US` ロケールと `arianeural` 音声がプルされます。 追加のロケールについては、[Neural Text-to-speech ロケール](#neural-text-to-speech-locales)に関する記事を参照してください。

#### <a name="neural-text-to-speech-locales"></a>Neural Text-to-speech ロケール

`latest` を除くすべてのタグは、次の形式であり、大文字と小文字が区別されます。

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>
```

次のタグは、この形式の例です。

```
1.3.0-amd64-en-us-arianeural
```

サポートされているロケール、および **Neural Text-to-speech** コンテナーの対応音声についてはすべて、[Neural Text-to-speech イメージ タグ](../containers/container-image-tags.md#neural-text-to-speech)に関する記事を参照してください。

> [!IMPORTANT]
> *Neural Text-to-speech* HTTP POST を構築するときは、[Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md) メッセージには、`name` 属性を含む `voice` 要素が必要になります。 値はそれに対応するコンテナーのロケールと音声であり、["短い名前"](language-support.md#neural-voices) とも呼ばれています。 たとえば、`latest` タグには `en-US-AriaNeural` という音声名が与えられます。

# <a name="custom-text-to-speech"></a>[カスタム テキスト読み上げ](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>カスタム テキスト読み上げコンテナー用の docker pull

Microsoft Container Registry からコンテナー イメージをダウンロードするには、[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用します。

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest
```

> [!NOTE]
> Custom Speech コンテナーの `locale` と `voice` は、コンテナーによって取り込まれるカスタム モデルによって決定されます。

# <a name="speech-language-detection"></a>[音声言語検出](#tab/lid)

#### <a name="docker-pull-for-the-speech-language-detection-container"></a>音声言語検出コンテナー用の docker pull

Microsoft Container Registry からコンテナー イメージをダウンロードするには、[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用します。

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest
```

***

## <a name="how-to-use-the-container"></a>コンテナーを使用する方法

コンテナーを[ホスト コンピューター](#the-host-computer)上に用意できたら、次の手順を使用してコンテナーを操作します。

1. 必要な課金設定を使用して[コンテナーを実行](#run-the-container-with-docker-run)します。 `docker run` コマンドの他の[例](speech-container-configuration.md#example-docker-run-commands)もご覧いただけます。
1. [コンテナーの予測エンドポイントに対するクエリを実行します](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>`docker run` によるコンテナーの実行

コンテナーを実行するには、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用します。 `{Endpoint_URI}` と `{API_Key}` の値を取得する方法の詳細については、「[必須パラメーターの収集](#gathering-required-parameters)」を参照してください。 `docker run` コマンドの追加[例](speech-container-configuration.md#example-docker-run-commands)も利用できます。

# <a name="speech-to-text"></a>[音声テキスト変換](#tab/stt)

標準 "*音声テキスト変換*" コンテナーを実行するには、次の `docker run` コマンドを実行します。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから *Speech-to-text* コンテナーを実行します
* 4 つの CPU コアと 4 ギガバイト (GB) のメモリを割り当てます。
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます。
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。

> [!NOTE]
> コンテナーによる Speech SDK への圧縮オーディオ入力のサポートには、GStreamer が使用されます。
> コンテナーに GStreamer をインストールするには、「[Speech SDK でコーデック圧縮オーディオを使用する](how-to-use-codec-compressed-audio-input-streams.md)」での GStreamer に関する Linux の手順に従ってください。

#### <a name="diarization-on-the-speech-to-text-output"></a>音声テキスト変換出力でのダイアライゼーション
ダイアライゼーションは、既定で有効です。 応答でダイアライゼーションを取得するには、`diarize_speech_config.set_service_property` を使用します。

1. 語句の出力形式を `Detailed` に設定します。
2. ダイアライゼーションのモードを設定します。 サポートされているモードは `Identity` と `Anonymous` です。
```python
diarize_speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Format',
    value='Detailed',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)

diarize_speech_config.set_service_property(
    name='speechcontext-phraseDetection.speakerDiarization.mode',
    value='Identity',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```
> [!NOTE]
> "Identity" モードでは `"SpeakerId": "Customer"` または `"SpeakerId": "Agent"` が返されます。
> "Anonymous" モードでは `"SpeakerId": "Speaker 1"` または `"SpeakerId": "Speaker 2"` が返されます


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>音声テキスト変換の出力のセンチメントを分析する 
v2.6.0 以降の音声テキスト変換コンテナーでは、プレビュー版ではなく TextAnalytics 3.0 API エンドポイントを使用する必要があります。 次に例を示します。
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0/sentiment`
* `https://localhost:5000/text/analytics/v3.0/sentiment`

> [!NOTE]
> Text Analytics `v3.0` API は、Text Analytics `v3.0-preview.1` と下位互換性がありません。 サポートされている最新のセンチメント機能を使用するには、`v2.6.0` の音声テキスト変換コンテナー イメージと Text Analytics `v3.0` を使用します。

音声テキスト変換コンテナーの v2.2.0 から、出力に対して[センチメント分析 v3 API](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) を呼び出すことができるようになりました。 センチメント分析を呼び出すには、Text Analytics API リソース エンドポイントが必要です。 次に例を示します。 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

クラウドでテキスト分析エンドポイントにアクセスしている場合は、キーが必要になります。 Text Analytics をローカルで実行している場合は、これを指定する必要はありません。

キーとエンドポイントは、次の例のように、引数として Speech コンテナーに渡されます。

```bash
docker run -it --rm -p 5000:5000 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

このコマンドは、次の操作を行います。

* 上のコマンドと同じ手順を実行します。
* センチメント分析の要求を送信するための Text Analytics API エンドポイントとキーが格納されています。 

#### <a name="phraselist-v2-on-the-speech-to-text-output"></a>音声テキスト変換の出力における Phraselist v2 

v2.6.0 以降の音声テキスト変換コンテナーでは、自分独自のフレーズ (文全体または中間の語句) を使用して出力することができます。 たとえば、次の文の *the tall man* です。

* "This is a sentence **the tall man** this is another sentence."

語句の一覧は、呼び出しを行うときに自分独自のフレーズを追加して構成できます。 次に例を示します。

```python
    phrase="the tall man"
    recognizer = speechsdk.SpeechRecognizer(
        speech_config=dict_speech_config,
        audio_config=audio_config)
    phrase_list_grammer = speechsdk.PhraseListGrammar.from_recognizer(recognizer)
    phrase_list_grammer.addPhrase(phrase)
    
    dict_speech_config.set_service_property(
        name='setflight',
        value='xonlineinterp',
        channel=speechsdk.ServicePropertyChannel.UriQueryParameter
    )
```

追加する語句が複数ある場合は、句ごとに `.addPhrase()` を呼び出して、語句の一覧に追加します。 


# <a name="custom-speech-to-text"></a>[カスタム音声変換](#tab/cstt)

*カスタム音声変換* コンテナーは Custom Speech モデルに依存します。 カスタム モデルは、[Custom Speech ポータル](https://speech.microsoft.com/customspeech)を利用して[トレーニング](how-to-custom-speech-train-model.md)されている必要があります。

コンテナーを実行するには、Custom **Speech モデル ID** が必須です。 Custom Speech ポータルの **[トレーニング]** ページにあります。 Custom Speech ポータルから、 **[トレーニング]** ページに移動し、モデルを選択します。
<br>

![[Custom Speech トレーニング] ページ](media/custom-speech/custom-speech-model-training.png)

`docker run` コマンドの `ModelId` パラメーターの引数として使用する **モデル ID** を取得します。
<br>

![Custom Speech モデルの詳細](media/custom-speech/custom-speech-model-details.png)

次の表は、さまざまな `docker run` パラメーターとその説明をまとめたものです。

| パラメーター | 説明 |
|---------|---------|
| `{VOLUME_MOUNT}` | ホスト コンピューターの[ボリューム マウント](https://docs.docker.com/storage/volumes/)。docker では、これを利用し、カスタム モデルを保持します。 たとえば、*C:\CustomSpeech* では、*C ドライブ* がホスト マシンに置かれます。 |
| `{MODEL_ID}` | Custom Speech ポータルの **[トレーニング]** ページの Custom Speech **モデル ID**。 |
| `{ENDPOINT_URI}` | 測定と課金にはエンドポイントが必須です。 詳細については、「[必須パラメーターの収集](#gathering-required-parameters)」を参照してください。 |
| `{API_KEY}` | API キーは必須です。 詳細については、「[必須パラメーターの収集](#gathering-required-parameters)」を参照してください。 |

*カスタム音声変換* コンテナーを実行するには、次の `docker run` コマンドを実行します。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから *カスタム音声変換* コンテナーを実行します
* 4 つの CPU コアと 4 ギガバイト (GB) のメモリを割り当てます。
* *カスタム Speech-to-Text* モデルをボリューム入力マウント (*C:\CustomSpeech* など) から読み込みます。
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます。
* `ModelId` が指定されたモデルをダウンロードします (ボリューム マウントで見つからない場合)。
* カスタム モデルが以前にダウンロードされた場合、`ModelId` は無視されます。
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。


#### <a name="base-model-download-on-the-custom-speech-to-text-container"></a>カスタム音声テキスト変換コンテナーにダウンロードされた基本モデル  
v2.6.0 以降のカスタム音声テキスト変換コンテナーでは、`BaseModelLocale=<locale>`オプションを使用して、利用可能な基本モデルの情報を取得できます。 このオプションを選択すると、自分の課金アカウントのそのロケールで使用可能な基本モデルの一覧が表示されます。 次に例を示します。

```bash
docker run --rm -it \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
BaseModelLocale={LOCALE} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから *カスタム音声変換* コンテナーを実行します
* ターゲット ロケールで使用可能な基本モデルを確認して返します。

この出力には、基本モデルの一覧が、情報ロケール、モデル ID、作成日時と共に表示されます。 モデル ID を使用すると、希望する特定の基本モデルをダウンロードして使用できます。 次に例を示します。
```
Checking available base model for en-us
2020/10/30 21:54:20 [Info] Searching available base models for en-us
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T08:23:42Z, Id: a3d8aab9-6f36-44cd-9904-b37389ce2bfa
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T12:01:02Z, Id: cc7826ac-5355-471d-9bc6-a54673d06e45
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2017-08-17T12:00:00Z, Id: a1f8db59-40ff-4f0e-b011-37629c3a1a53
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-04-16T11:55:00Z, Id: c7a69da3-27de-4a4b-ab75-b6716f6321e5
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-09-21T15:18:43Z, Id: da494a53-0dad-4158-b15f-8f9daca7a412
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-10-19T11:28:54Z, Id: 84ec130b-d047-44bf-a46d-58c1ac292ca7
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T07:59:09Z, Id: ee5c100f-152f-4ae5-9e9d-014af3c01c56
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T09:21:55Z, Id: d04959a6-71da-4913-9997-836793e3c115
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-01-11T10:04:19Z, Id: 488e5f23-8bc5-46f8-9ad8-ea9a49a8efda
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-02-18T14:37:57Z, Id: 0207b3e6-92a8-4363-8c0e-361114cdd719
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-03-03T17:34:10Z, Id: 198d9b79-2950-4609-b6ec-f52254074a05
2020/10/30 21:54:21 [Fatal] Please run this tool again and assign --modelId '<one above base model id>'. If no model id listed above, it means currently there is no available base model for en-us
```

#### <a name="custom-pronunciation-on-the-custom-speech-to-text-container"></a>カスタム音声変換コンテナーにおけるカスタム発音 
v2.5.0 以降のカスタム音声テキスト変換コンテナーでは、カスタムの発音結果を出力できます。 必要なのは、自分のカスタム モデルに、自分独自のカスタム発音ルールを設定し、そのモデルをカスタム音声テキスト変換コンテナーにマウントすることだけです。


# <a name="text-to-speech"></a>[テキスト読み上げ](#tab/tts)

標準 "*テキスト読み上げ*" コンテナーを実行するには、次の `docker run` コマンドを実行します。

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから標準 "*テキスト読み上げ*" コンテナーを実行します。
* 1 つの CPU コアと 2 ギガバイト (GB) のメモリを割り当てます。
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます。
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。

# <a name="neural-text-to-speech"></a>[Neural Text-to-speech](#tab/ntts)

*Neural Text-to-speech* コンテナーを実行するには、次の `docker run` コマンドを実行します。

```bash
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから *Neural Text-to-speech* コンテナーを実行します。
* 6 つの CPU コアと 12 ギガバイト (GB) のメモリを割り当てます。
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます。
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。

# <a name="custom-text-to-speech"></a>[カスタム テキスト読み上げ](#tab/ctts)

*カスタム テキスト読み上げ* コンテナーはカスタム音声モデルに依存します。 カスタム モデルは、[カスタム音声ポータル](https://aka.ms/custom-voice-portal)を利用して[トレーニング](how-to-custom-voice-create-voice.md)されている必要があります。 コンテナーを実行するには、カスタム音声 **モデル ID** が必須です。 カスタム音声ポータルの **[トレーニング]** ページにあります。 カスタム音声ポータルから、 **[トレーニング]** ページに移動し、モデルを選択します。
<br>

![[カスタム音声トレーニング] ページ](media/custom-voice/custom-voice-model-training.png)

docker run コマンドの `ModelId` パラメーターの引数として使用する **モデル ID** を取得します。
<br>

![カスタム音声モデルの詳細](media/custom-voice/custom-voice-model-details.png)

次の表は、さまざまな `docker run` パラメーターとその説明をまとめたものです。

| パラメーター | 説明 |
|---------|---------|
| `{VOLUME_MOUNT}` | ホスト コンピューターの[ボリューム マウント](https://docs.docker.com/storage/volumes/)。docker では、これを利用し、カスタム モデルを保持します。 たとえば、*C:\CustomSpeech* では、*C ドライブ* がホスト マシンに置かれます。 |
| `{MODEL_ID}` | カスタム音声ポータルの **[トレーニング]** ページの Custom Speech **モデル ID**。 |
| `{ENDPOINT_URI}` | 測定と課金にはエンドポイントが必須です。 詳細については、「[必須パラメーターの収集](#gathering-required-parameters)」を参照してください。 |
| `{API_KEY}` | API キーは必須です。 詳細については、「[必須パラメーターの収集](#gathering-required-parameters)」を参照してください。 |

*カスタム テキスト読み上げ* コンテナーを実行するには、次の `docker run` コマンドを実行します。

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから *カスタム テキスト読み上げ* コンテナーを実行します
* 1 つの CPU コアと 2 ギガバイト (GB) のメモリを割り当てます。
* *カスタム テキスト読み上げ* モデルをボリューム入力マウント (*C:\CustomVoice* など) から読み込みます。
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます。
* `ModelId` が指定されたモデルをダウンロードします (ボリューム マウントで見つからない場合)。
* カスタム モデルが以前にダウンロードされた場合、`ModelId` は無視されます。
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。

# <a name="speech-language-detection"></a>[音声言語検出](#tab/lid)

"*音声言語検出*" コンテナーを実行するには、次の `docker run` コマンドを実行します。

```bash
docker run --rm -it -p 5003:5003 --memory 1g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。 

* コンテナー イメージから音声言語検出コンテナーを実行します。 現在のところ、このイメージを実行しても課金されません。
* 1 つの CPU コアと 1 ギガバイト (GB) のメモリを割り当てます。
* TCP ポート 5003 を公開し、コンテナーに pseudo-TTY を割り当てます。
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。

音声言語検出要求のみを送信する場合は、音声クライアントの `phraseDetection` 値を `None` に設定する必要があります。  

```python
speech_config.set_service_property(
      name='speechcontext-phraseDetection.Mode',
      value='None',
      channel=speechsdk.ServicePropertyChannel.UriQueryParameter
   )
```

このコンテナーを音声テキスト変換コンテナーで実行する場合は、この [Docker イメージ](https://hub.docker.com/r/antsu/on-prem-client)を使用できます。 両方のコンテナーが起動したら、この Docker Run コマンドを使用して `speech-to-text-with-languagedetection-client` を実行します。

```Docker
docker run --rm -v ${HOME}:/root -ti antsu/on-prem-client:latest ./speech-to-text-with-languagedetection-client ./audio/LanguageDetection_en-us.wav --host localhost --lport 5003 --sport 5000
```

> [!NOTE]
> 同時呼び出しの数を増やすと、信頼性と待機時間に影響を与える可能性があります。 言語検出では、1 つの CPU と 1 GB のメモリを使用した最大 4 個の同時呼び出しをお勧めします。 2 つの CPU と 2 GB のメモリを搭載したホストでは、最大 6 個の同時呼び出しをお勧めします。

***

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing)」を参照してください。

## <a name="query-the-containers-prediction-endpoint"></a>コンテナーの予測エンドポイントに対するクエリの実行

> [!NOTE]
> 複数のコンテナーを実行している場合は、一意のポート番号を使用します。

| Containers | SDK ホスト URL | Protocol |
|--|--|--|
| 標準音声テキスト変換とカスタム音声テキスト変換 | `ws://localhost:5000` | WS |
| テキスト読み上げ (標準、カスタム、Neural を含む)、音声言語検出 | `http://localhost:5000` | HTTP |

WSS プロトコルと HTTPS プロトコルを使用する方法については、[コンテナー セキュリティ](../cognitive-services-container-support.md#azure-cognitive-services-container-security)に関するセクションを参照してください。

### <a name="speech-to-text-standard-and-custom"></a>音声テキスト変換 (標準およびカスタム)

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>感情を分析する

Text Analytics API 資格情報[をコンテナーに指定した](#analyze-sentiment-on-the-speech-to-text-output)場合は、Speech SDK を使用して、センチメント分析で音声認識要求を送信できます。 *シンプル* な形式または *詳細* な形式のいずれかを使用するよう、API 応答を構成できます。
> [!NOTE]
> Speech サービス Python SDK の v1.13 では、感情分析の問題が特定されています。 Speech サービス Python SDK で感情分析を使用している場合は、v1.12 以前のバージョンを使用してください。

# <a name="simple-format"></a>[シンプルな形式](#tab/simple-format)

音声クライアントでシンプルな形式を使用するように構成するには、`Simple.Extensions` の値として `"Sentiment"` を追加します。 特定の Text Analytics モデルのバージョンを選択する場合は、`speechcontext-phraseDetection.sentimentAnalysis.modelversion` プロパティの構成で `'latest'` を置き換えます。

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Simple.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Simple.Extensions` は、応答のルート レイヤーでセンチメントの結果を返します。

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6098574b79434bd4849fee7e0a50f22e",
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

# <a name="detailed-format"></a>[詳細な形式](#tab/detailed-format)

Speech クライアントで詳細な形式を使用するように構成するには、`Detailed.Extensions`、`Detailed.Options`、またはその両方の値として `"Sentiment"` を追加します。 特定の Text Analytics モデルのバージョンを選択する場合は、`speechcontext-phraseDetection.sentimentAnalysis.modelversion` プロパティの構成で `'latest'` を置き換えます。

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Options',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Detailed.Extensions` は、応答のルート レイヤーでセンチメントの結果を渡します。 `Detailed.Options` は、応答の `NBest` レイヤーで結果を渡します。 これらは、個別、または組み合わせて使用できます。

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6a2aac009b9743d8a47794f3e81f7963",
   "NBest":[
      {
         "Confidence":0.973695,
         "Display":"What's the weather like?",
         "ITN":"what's the weather like",
         "Lexical":"what's the weather like",
         "MaskedITN":"What's the weather like",
         "Sentiment":{
            "Negative":0.03,
            "Neutral":0.79,
            "Positive":0.18
         }
      },
      {
         "Confidence":0.9164971,
         "Display":"What is the weather like?",
         "ITN":"what is the weather like",
         "Lexical":"what is the weather like",
         "MaskedITN":"What is the weather like",
         "Sentiment":{
            "Negative":0.02,
            "Neutral":0.88,
            "Positive":0.1
         }
      }
   ],
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

---

センチメント分析を完全に無効にする場合は、`sentimentanalysis.enabled` に `false` 値を追加します。

```python
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentanalysis.enabled',
    value='false',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

### <a name="text-to-speech-standard-neural-and-custom"></a>テキスト読み上げ (標準、Neural、カスタム)

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>同じホスト上で複数のコンテナーを実行する

公開されているポートを使って複数のコンテナーを実行する予定の場合、必ず各コンテナーを別の公開されているポートで実行してください。 たとえば、最初のコンテナーをポート 5000 上で、2 番目のコンテナーを 5001 上で実行します。

このコンテナーと、別の Azure Cognitive Services コンテナーを HOST 上で同時に実行することができます。 同じ Cognitive Services コンテナーの複数のコンテナーを実行することもできます。

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>コンテナーの停止

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>トラブルシューティング

コンテナーを起動または実行するとき、問題が発生することがあります。 出力[マウント](speech-container-configuration.md#mount-settings)を使用し、ログ記録を有効にします。 これにより、問題の解決時に役立つログ ファイルをコンテナーで生成できます。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>課金

Speech コンテナーは、Azure アカウントの *Speech* リソースを使用して、Azure に課金情報を送信します。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

これらのオプションの詳細については、「[コンテナーの構成](speech-container-configuration.md)」を参照してください。

## <a name="summary"></a>まとめ

この記事では、Speech コンテナーの概念とそのダウンロード、インストール、および実行のワークフローについて学習しました。 要約すると:

* Speech は、Docker 用に 4 つの Linux コンテナーを提供し、さまざまな機能をカプセル化します。
  * *音声テキスト変換*
  * *カスタム音声変換*
  * *テキスト読み上げ*
  * *カスタム テキスト読み上げ*
  * *Neural Text-to-speech*
  * *音声言語検出*
* コンテナー イメージは、Azure のコンテナー レジストリからダウンロードされます。
* コンテナー イメージを Docker で実行します。
* REST API (テキスト読み上げのみ) を使用するか、SDK (音声変換またはテキスト読み上げ) を使用するかにかかわらず、コンテナーのホスト URI を指定します。 
* コンテナーをインスタンス化するときは、課金情報を指定するように要求されます。

> [!IMPORTANT]
>  Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 Cognitive Services コンテナーが、顧客データ (解析対象の画像やテキストなど) を Microsoft に送信することはありません。

## <a name="next-steps"></a>次のステップ

* 構成設定について、[コンテナーの構成](speech-container-configuration.md)を確認します。
* [Kubernetes と Helm と共に Speech サービス コンテナーを使用する](speech-container-howto-on-premises.md)方法を学習します。
* さらに [Cognitive Services コンテナー](../cognitive-services-container-support.md)を使用する
