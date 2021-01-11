---
title: Speech コンテナーをインストールする - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech コンテナーをインストールして実行します。 音声テキスト変換では、オーディオ ストリームからテキストへの文字起こしがリアルタイムで行われます。結果のテキストを、アプリケーション、ツール、またはデバイスで使用したり表示したりできます。 テキスト読み上げでは、入力テキストが人間のような合成音声に変換されます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: aahi
ms.openlocfilehash: dc17c25a84c3d0af39bfa7a8902bdc1d93f201e8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88518324"
---
# <a name="install-and-run-speech-service-containers-preview"></a>Speech サービス コンテナーをインストールして実行する (プレビュー)

コンテナーを使用すると、独自の環境で一部の Speech サービス API を実行できます。 コンテナーは、特定のセキュリティ要件とデータ ガバナンス要件に適しています。 この記事では、Speech コンテナーをダウンロード、インストール、実行する方法について説明します。

Speech コンテナーでは、堅牢なクラウド機能とエッジの局所性の両方のために最適化された音声アプリケーション アーキテクチャを構築できます。 4 つの異なるコンテナーを利用できます。 2 つの標準コンテナーは、**音声テキスト変換**と**テキスト読み上げ**です。 2 つのカスタム コンテナーは、**カスタム音声変換**と**カスタム テキスト読み上げ**です。 Speech コンテナーの[価格](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)は、クラウド ベースの Azure Speech サービスと同じです。

> [!IMPORTANT]
> 現在のところ、音声コンテナーはすべて、[パブリック "ゲート付き" プレビュー](../cognitive-services-container-support.md#container-availability-in-azure-cognitive-services)の一部として提供されています。 音声コンテナーが一般公開 (GA) になるときは告知があります。

| 機能 | 特徴 | 最新 |
|--|--|--|
| 音声テキスト変換 | 中間結果を使用して、センチメントを分析し、リアルタイムの音声録音またはバッチ音声録音を文字起こしします。  | 2.3.1 |
| カスタム音声変換 | [Custom Speech ポータル](https://speech.microsoft.com/customspeech)のカスタム モデルを利用し、連続するリアルタイムの音声またはバッチ音声録音を、中間結果を含むテキストに文字起こしします。 | 2.3.1 |
| テキスト読み上げ | テキストを、プレーンテキストの入力または音声合成マークアップ言語 (SSML) を含む自然な音声に変換します。 | 1.5.0 |
| カスタム テキスト読み上げ | [Custom Voice ポータル](https://aka.ms/custom-voice-portal)のカスタム モデルを利用し、テキストを、プレーンテキストの入力または音声合成マークアップ言語 (SSML) を含む自然な音声に変換します。 | 1.5.0 |

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。

## <a name="prerequisites"></a>前提条件

Speech コンテナーを使用する前の前提条件は次のとおりです。

| 必須 | 目的 |
|--|--|
| Docker エンジン | [ホスト コンピューター](#the-host-computer)に Docker エンジンをインストールしておく必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上で Docker 環境の構成を行うパッケージが用意されています。 Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。<br><br> コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 <br><br> **Windows では**、Linux コンテナーをサポートするように Docker を構成することも必要です。<br><br> |
| Docker に関する知識 | レジストリ、リポジトリ、コンテナー、コンテナー イメージなど、Docker の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。 |
| Speech リソース | これらのコンテナーを使用するためには、以下が必要です。<br><br>関連付けられている API キーとエンドポイント URI を取得するための Azure _Speech_ リソース。 どちらの値も、Azure portal の **Speech** の [概要] ページと [キー] ページで確認できます。 コンテナーを起動するには、両方が必要です。<br><br>**{API_KEY}** : **[キー]** ページにある 2 つの利用可能なリソース キーのどちらか<br><br>**{ENDPOINT_URI}** : **[概要]** ページに提示されているエンドポイント |

## <a name="request-access-to-the-container-registry"></a>コンテナー レジストリへのアクセスの要求

コンテナーへのアクセスを要求するには、[要求フォーム](https://aka.ms/cognitivegate)に記入して送信します。 


[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>ホスト コンピューター

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Advanced Vector Extension のサポート

**ホスト**とは、Docker コンテナーを実行するコンピューターのことです。 ホストは、[高度なベクター拡張機能](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2) を *サポートしている必要があります*。 次のコマンドを使用して、Linux ホストでの AVX2 サポートを確認できます。

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> AVX2 をサポートするにはホスト コンピューターが*必須*です。 AVX2 サポートがないと、コンテナーは正しく機能*しません*。

### <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

次の表に、各 Speech コンテナーに割り当てるリソースの最小値と推奨値を示します。

# <a name="speech-to-text"></a>[音声テキスト変換](#tab/stt)

| コンテナー | 最小値 | 推奨 |
|-----------|---------|-------------|
| 音声テキスト変換 | 2 コア、2 GB のメモリ | 4 コア、4 GB メモリ |

# <a name="custom-speech-to-text"></a>[カスタム音声変換](#tab/cstt)

| コンテナー | 最小値 | 推奨 |
|-----------|---------|-------------|
| カスタム音声変換 | 2 コア、2 GB のメモリ | 4 コア、4 GB メモリ |

# <a name="text-to-speech"></a>[テキスト読み上げ](#tab/tts)

| コンテナー | 最小値 | 推奨 |
|-----------|---------|-------------|
| テキスト読み上げ | 1 コア、2 GB メモリ | 2 コア、3 GB のメモリ |

# <a name="custom-text-to-speech"></a>[カスタム テキスト読み上げ](#tab/ctts)

| コンテナー | 最小値 | 推奨 |
|-----------|---------|-------------|
| カスタム テキスト読み上げ | 1 コア、2 GB メモリ | 2 コア、3 GB のメモリ |

***

* 各コアは少なくとも 2.6 ギガヘルツ (GHz) 以上にする必要があります。

コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

> [!NOTE]
> 最小および推奨値は、Docker の制限に基づくもので、ホスト マシンのリソースに基づくものでは*ありません*。 たとえば、音声テキスト変換コンテナーは、大規模な言語モデルの一部をメモリ マップするため、ファイル全体がメモリに収まるようにすることを "*お勧めします*"。これには、追加で 4 から 6 GB が必要です。 また、モデルがメモリにページングされているため、どちらかのコンテナーの最初の実行に時間がかかる場合があります。

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull` によるコンテナー イメージの取得

Speech のコンテナー イメージは、次のコンテナー レジストリで入手できます。

# <a name="speech-to-text"></a>[音声テキスト変換](#tab/stt)

| コンテナー | リポジトリ |
|-----------|------------|
| 音声テキスト変換 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[カスタム音声変換](#tab/cstt)

| コンテナー | リポジトリ |
|-----------|------------|
| カスタム音声変換 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[テキスト読み上げ](#tab/tts)

| コンテナー | リポジトリ |
|-----------|------------|
| テキスト読み上げ | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[カスタム テキスト読み上げ](#tab/ctts)

| コンテナー | リポジトリ |
|-----------|------------|
| カスタム テキスト読み上げ | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Speech コンテナー用の docker pull

# <a name="speech-to-text"></a>[音声テキスト変換](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Speech-to-text コンテナー用の docker pull

Container Preview レジストリからコンテナー イメージをダウンロードするには、[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用します。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
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
2.3.1-amd64-en-us-preview
```

**音声テキスト変換**コンテナーのサポートされている全ロケールについては、「[音声テキスト変換イメージ タグ](../containers/container-image-tags.md#speech-to-text)」を参照してください。

# <a name="custom-speech-to-text"></a>[カスタム音声変換](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>カスタム音声変換コンテナー用の docker pull

Container Preview レジストリからコンテナー イメージをダウンロードするには、[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用します。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> Custom Speech コンテナーの `locale` と `voice` は、コンテナーによって取り込まれるカスタム モデルによって決定されます。

# <a name="text-to-speech"></a>[テキスト読み上げ](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Text-to-speech コンテナー用の docker pull

Container Preview レジストリからコンテナー イメージをダウンロードするには、[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用します。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
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
1.5.0-amd64-en-us-ariarus-preview
```

サポートされているロケールと**テキスト読み上げ**コンテナーの対応音声についてはすべて、「[テキスト読み上げイメージ タグ](../containers/container-image-tags.md#text-to-speech)」を参照してください。

> [!IMPORTANT]
> *標準 Text-to-speech* HTTP POST を構築するとき、[Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md) メッセージには、`name` 属性を含む `voice` 要素が必要になります。 値はそれに対応するコンテナーのロケールと音声であり、["短い名前"](language-support.md#standard-voices) とも呼ばれています。 たとえば、`latest` タグには `en-US-AriaRUS` という音声名が与えられます。

# <a name="custom-text-to-speech"></a>[カスタム テキスト読み上げ](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>カスタム テキスト読み上げコンテナー用の docker pull

Container Preview レジストリからコンテナー イメージをダウンロードするには、[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用します。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> Custom Speech コンテナーの `locale` と `voice` は、コンテナーによって取り込まれるカスタム モデルによって決定されます。

***

## <a name="how-to-use-the-container"></a>コンテナーを使用する方法

コンテナーを[ホスト コンピューター](#the-host-computer)上に用意できたら、次の手順を使用してコンテナーを操作します。

1. 必要な課金設定を使用して[コンテナーを実行](#run-the-container-with-docker-run)します。 `docker run` コマンドの他の[例](speech-container-configuration.md#example-docker-run-commands)もご覧いただけます。
1. [コンテナーの予測エンドポイントに対するクエリを実行します](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>`docker run` によるコンテナーの実行

コンテナーを実行するには、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用します。 `{Endpoint_URI}` と `{API_Key}` の値を取得する方法の詳細については、「[必須パラメーターの収集](#gathering-required-parameters)」を参照してください。 `docker run` コマンドの追加[例](speech-container-configuration.md#example-docker-run-commands)も利用できます。

# <a name="speech-to-text"></a>[音声テキスト変換](#tab/stt)

*Speech-to-text* コンテナーを実行するには、次の `docker run` コマンドを実行します。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから *Speech-to-text* コンテナーを実行します
* 4 つの CPU コアと 4 ギガバイト (GB) のメモリを割り当てます。
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます。
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>音声テキスト変換の出力のセンチメントを分析する 

音声テキスト変換コンテナーの v2.2.0 から、出力に対して[センチメント分析 v3 API](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) を呼び出すことができるようになりました。 センチメント分析を呼び出すには、Text Analytics API リソース エンドポイントが必要です。 次に例を示します。 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

クラウドでテキスト分析エンドポイントにアクセスしている場合は、キーが必要になります。 Text Analytics をローカルで実行している場合は、これを指定する必要はありません。

キーとエンドポイントは、次の例のように、引数として Speech コンテナーに渡されます。

```bash
docker run -it --rm -p 5000:5000 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

このコマンドは、次の操作を行います。

* 上のコマンドと同じ手順を実行します。
* センチメント分析の要求を送信するための Text Analytics API エンドポイントとキーが格納されています。 


# <a name="custom-speech-to-text"></a>[カスタム音声変換](#tab/cstt)

*カスタム音声変換* コンテナーは Custom Speech モデルに依存します。 カスタム モデルは、[Custom Speech ポータル](https://speech.microsoft.com/customspeech)を利用して[トレーニング](how-to-custom-speech-train-model.md)されている必要があります。

> [!IMPORTANT]
> Custom Speech モデルは、次のいずれかのモデルバージョンでトレーニングが必要になります。
> * **20181201 (v3.3 統合)**
> * **20190520 (v4.14 統合)**
> * **20190701 (v4.17 統合)**<br>
> ![Custom Speech トレーニング コンテナー モデル](media/custom-speech/custom-speech-train-model-container-scoped.png)

コンテナーを実行するには、Custom **Speech モデル ID** が必須です。 Custom Speech ポータルの **[トレーニング]** ページにあります。 Custom Speech ポータルから、 **[トレーニング]** ページに移動し、モデルを選択します。
<br>

![[Custom Speech トレーニング] ページ](media/custom-speech/custom-speech-model-training.png)

`docker run` コマンドの `ModelId` パラメーターの引数として使用する**モデル ID** を取得します。
<br>

![Custom Speech モデルの詳細](media/custom-speech/custom-speech-model-details.png)

次の表は、さまざまな `docker run` パラメーターとその説明をまとめたものです。

| パラメーター | 説明 |
|---------|---------|
| `{VOLUME_MOUNT}` | ホスト コンピューターの[ボリューム マウント](https://docs.docker.com/storage/volumes/)。docker では、これを利用し、カスタム モデルを保持します。 たとえば、*C:\CustomSpeech* では、*C ドライブ*がホスト マシンに置かれます。 |
| `{MODEL_ID}` | Custom Speech ポータルの **[トレーニング]** ページの Custom Speech **モデル ID**。 |
| `{ENDPOINT_URI}` | 測定と課金にはエンドポイントが必須です。 詳細については、「[必須パラメーターの収集](#gathering-required-parameters)」を参照してください。 |
| `{API_KEY}` | API キーは必須です。 詳細については、「[必須パラメーターの収集](#gathering-required-parameters)」を参照してください。 |

*カスタム音声変換* コンテナーを実行するには、次の `docker run` コマンドを実行します。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから*カスタム音声変換* コンテナーを実行します
* 4 つの CPU コアと 4 ギガバイト (GB) のメモリを割り当てます。
* *カスタム Speech-to-Text* モデルをボリューム入力マウント (*C:\CustomSpeech* など) から読み込みます。
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます。
* `ModelId` が指定されたモデルをダウンロードします (ボリューム マウントで見つからない場合)。
* カスタム モデルが以前にダウンロードされた場合、`ModelId` は無視されます。
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。

# <a name="text-to-speech"></a>[テキスト読み上げ](#tab/tts)

*Text-to-speech* コンテナーを実行するには、次の `docker run` コマンドを実行します。

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから *Text-to-speech* コンテナーを実行します
* 1 つの CPU コアと 2 ギガバイト (GB) のメモリを割り当てます。
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます。
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。

# <a name="custom-text-to-speech"></a>[カスタム テキスト読み上げ](#tab/ctts)

*カスタム テキスト読み上げ* コンテナーはカスタム音声モデルに依存します。 カスタム モデルは、[カスタム音声ポータル](https://aka.ms/custom-voice-portal)を利用して[トレーニング](how-to-custom-voice-create-voice.md)されている必要があります。 コンテナーを実行するには、カスタム音声**モデル ID** が必須です。 カスタム音声ポータルの **[トレーニング]** ページにあります。 カスタム音声ポータルから、 **[トレーニング]** ページに移動し、モデルを選択します。
<br>

![[カスタム音声トレーニング] ページ](media/custom-voice/custom-voice-model-training.png)

docker run コマンドの `ModelId` パラメーターの引数として使用する**モデル ID** を取得します。
<br>

![カスタム音声モデルの詳細](media/custom-voice/custom-voice-model-details.png)

次の表は、さまざまな `docker run` パラメーターとその説明をまとめたものです。

| パラメーター | 説明 |
|---------|---------|
| `{VOLUME_MOUNT}` | ホスト コンピューターの[ボリューム マウント](https://docs.docker.com/storage/volumes/)。docker では、これを利用し、カスタム モデルを保持します。 たとえば、*C:\CustomSpeech* では、*C ドライブ*がホスト マシンに置かれます。 |
| `{MODEL_ID}` | カスタム音声ポータルの **[トレーニング]** ページの Custom Speech **モデル ID**。 |
| `{ENDPOINT_URI}` | 測定と課金にはエンドポイントが必須です。 詳細については、「[必須パラメーターの収集](#gathering-required-parameters)」を参照してください。 |
| `{API_KEY}` | API キーは必須です。 詳細については、「[必須パラメーターの収集](#gathering-required-parameters)」を参照してください。 |

*カスタム テキスト読み上げ* コンテナーを実行するには、次の `docker run` コマンドを実行します。

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから*カスタム テキスト読み上げ* コンテナーを実行します
* 1 つの CPU コアと 2 ギガバイト (GB) のメモリを割り当てます。
* *カスタム テキスト読み上げ* モデルをボリューム入力マウント (*C:\CustomVoice* など) から読み込みます。
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます。
* `ModelId` が指定されたモデルをダウンロードします (ボリューム マウントで見つからない場合)。
* カスタム モデルが以前にダウンロードされた場合、`ModelId` は無視されます。
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。

***

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing)」を参照してください。

## <a name="query-the-containers-prediction-endpoint"></a>コンテナーの予測エンドポイントに対するクエリの実行

> [!NOTE]
> 複数のコンテナーを実行している場合は、一意のポート番号を使用します。

| Containers | SDK ホスト URL | Protocol |
|--|--|--|
| 音声変換およびカスタム音声変換 | `ws://localhost:5000` | WS |
| テキスト読み上げおよびカスタム テキスト読み上げ | `http://localhost:5000` | HTTP |

WSS プロトコルと HTTPS プロトコルを使用する方法については、[コンテナー セキュリティ](../cognitive-services-container-support.md#azure-cognitive-services-container-security)に関するセクションを参照してください。

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>感情を分析する

Text Analytics API 資格情報[をコンテナーに指定した](#analyze-sentiment-on-the-speech-to-text-output)場合は、Speech SDK を使用して、センチメント分析で音声認識要求を送信できます。 *シンプル*な形式または*詳細*な形式のいずれかを使用するよう、API 応答を構成できます。

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

### <a name="text-to-speech-or-custom-text-to-speech"></a>テキスト読み上げまたはカスタム テキスト読み上げ

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

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>まとめ

この記事では、Speech コンテナーの概念とそのダウンロード、インストール、および実行のワークフローについて学習しました。 要約すると:

* Speech は、Docker 用に 4 つの Linux コンテナーを提供し、さまざまな機能をカプセル化します。
  * *音声テキスト変換*
  * *カスタム音声変換*
  * *テキスト読み上げ*
  * *カスタム テキスト読み上げ*
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
