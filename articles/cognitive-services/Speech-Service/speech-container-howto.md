---
title: Speech コンテナーをインストールする
titleSuffix: Azure Cognitive Services
description: Speech コンテナーをインストールして実行します。 音声テキスト変換では、オーディオ ストリームからテキストへの文字起こしがリアルタイムで行われます。結果のテキストを、アプリケーション、ツール、またはデバイスで使用したり表示したりできます。 テキスト読み上げでは、入力テキストが人間のような合成音声に変換されます。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 8f395788d4dd3c845155a52bd6b4666998838fcd
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490232"
---
# <a name="install-and-run-speech-service-containers"></a>Speech サービス コンテナーをインストールして実行する

Speech コンテナーでは、堅牢なクラウド機能とエッジの局所性の両方を活用するように最適化された 1 つの音声アプリケーション アーキテクチャを構築できます。 

2 つの Speech コンテナーは、"**音声テキスト変換**" と "**テキスト読み上げ**" です。 

|Function|機能|最新|
|-|-|--|
|音声テキスト変換| <li>連続するリアルタイムの音声またはバッチ音声録音を、中間結果を含むテキストに文字起こしします。|1.1.3|
|テキスト読み上げ| <li>テキストを自然な音声に変換します。 プレーンテキストの入力または音声合成マークアップ言語 (SSML) に対応しています。 |1.1.0|

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

Speech コンテナーを使用する前に、次の前提条件を満たす必要があります。

|必須|目的|
|--|--|
|Docker エンジン| [ホスト コンピューター](#the-host-computer)に Docker エンジンをインストールしておく必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上で Docker 環境の構成を行うパッケージが用意されています。 Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。<br><br> コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 <br><br> **Windows では**、Linux コンテナーをサポートするように Docker を構成することも必要です。<br><br>|
|Docker に関する知識 | レジストリ、リポジトリ、コンテナー、コンテナー イメージなど、Docker の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。| 
|Speech リソース |これらのコンテナーを使用するためには、以下が必要です。<br><br>関連付けられている課金キーと課金エンドポイント URI を取得するための _Speech_ Azure リソース。 これらの値は、どちらも Azure portal の **Speech** の [概要] ページと [キー] ページで入手でき、コンテナーを起動するために必要です。<br><br>**{BILLING_KEY}** : リソース キー<br><br>**{BILLING_ENDPOINT_URI}** : エンドポイントURI の例: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="request-access-to-the-container-registry"></a>コンテナー レジストリへのアクセスの要求

コンテナーへのアクセスを要求するには、最初に [Cognitive Services Speech コンテナー要求フォーム](https://aka.ms/speechcontainerspreview/)に記入して送信する必要があります。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>ホスト コンピューター

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Advanced Vector Extension のサポート

**ホスト**とは、Docker コンテナーを実行するコンピューターのことです。 ホストは、[Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2) をサポートしている必要があります。 次のコマンドを使用して、Linux ホストでのこのサポートを確認できます。 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

次の表に、各 Speech コンテナーに割り当てる CPU コアとメモリの最小値と推奨値を示します。

| コンテナー | 最小値 | 推奨 |
|-----------|---------|-------------|
|cognitive-services-speech-to-text | 2 コア<br>2 GB メモリ  | 4 コア<br>4 GB メモリ  |
|cognitive-services-text-to-speech | 1 コア、0.5 GB メモリ| 2 コア、1 GB メモリ |

* 各コアは少なくとも 2.6 ギガヘルツ (GHz) 以上にする必要があります。

コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

**注**: 最小および推奨値は、Docker の制限に基づくもので、ホスト マシンのリソースに基づくものでは "*ありません*"。 たとえば、音声テキスト変換コンテナーは、大規模な言語モデルの一部をメモリ マップするため、ファイル全体がメモリに収まるようにすることを "_お勧めします_"。これには、追加で 4 から 6 GB が必要です。 また、モデルがメモリにページングされているため、どちらかのコンテナーの最初の実行に時間がかかる場合があります。

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull` によるコンテナー イメージの取得

Speech のコンテナー イメージを入手できます。

| コンテナー | リポジトリ |
|-----------|------------|
| cognitive-services-speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| cognitive-services-text-to-speech | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>コンテナー タグ内の言語ロケール

`latest` タグにより、`en-us` ロケールと `jessarus` 音声がプルされます。

#### <a name="speech-to-text-locales"></a>音声テキスト変換のロケール

`latest` を除くすべてのタグは次の形式になっています。ここで、`<culture>` はロケール コンテナーを示します。

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

次のタグは、この形式の例です。

```
1.1.3-amd64-en-us-preview
```

次の表に、1.1.3 バージョンのコンテナーの**音声テキスト変換**でサポートされているロケールを示します。

|言語ロケール|Tags|
|--|--|
|中国語|`zh-cn`|
|英語 |`en-us`<br>`en-gb`<br>`en-au`<br>`en-in`|
|フランス語 |`fr-ca`<br>`fr-fr`|
|ドイツ語|`de-de`|
|イタリア語|`it-it`|
|日本語|`ja-jp`|
|韓国語|`ko-kr`|
|ポルトガル語|`pt-br`|
|スペイン語|`es-es`<br>`es-mx`|

#### <a name="text-to-speech-locales"></a>テキスト読み上げのロケール

`latest` を除くすべてのタグは次の形式になっています。ここで、`<culture>` はロケールを示し、`<voice>` はコンテナーの音声を示します。

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

次のタグは、この形式の例です。

```
1.1.0-amd64-en-us-jessarus-preview
```

次の表に、1.1.0 バージョンのコンテナーの "**テキスト読み上げ**" でサポートされているロケールを示します。

|言語ロケール|Tags|サポートされている音声|
|--|--|--|
|中国語|`zh-cn`|huihuirus<br>kangkang-apollo<br>yaoyao-apollo|
|英語 |`en-au`|catherine<br>hayleyrus|
|英語 |`en-gb`|george-apollo<br>hazelrus<br>susan-apollo|
|英語 |`en-in`|heera-apollo<br>priyarus<br>ravi-apollo<br>|
|英語 |`en-us`|jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus|
|フランス語|`fr-ca`|caroline<br>harmonierus|
|フランス語|`fr-fr`|hortenserus<br>julie-apollo<br>paul-apollo|
|ドイツ語|`de-de`|hedda<br>heddarus<br>stefan-apollo|
|イタリア語|`it-it`|cosimo-apollo<br>luciarus|
|日本語|`ja-jp`|ayumi-apollo<br>harukarus<br>ichiro-apollo|
|韓国語|`ko-kr`|heamirus|
|ポルトガル語|`pt-br`|daniel-apollo<br>heloisarus|
|スペイン語|`es-es`|elenarus<br>laura-apollo<br>pablo-apollo<br>|
|スペイン語|`es-mx`|hildarus<br>raul-apollo|

### <a name="docker-pull-for-the-speech-containers"></a>Speech コンテナー用の docker pull

#### <a name="speech-to-text"></a>音声テキスト変換

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

#### <a name="text-to-speech"></a>テキスト読み上げ

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

## <a name="how-to-use-the-container"></a>コンテナーを使用する方法

コンテナーを[ホスト コンピューター](#the-host-computer)上に用意できたら、次の手順を使用してコンテナーを操作します。

1. 必要でありながら使用されていない課金設定を使用して[コンテナーを実行](#run-the-container-with-docker-run)します。 `docker run` コマンドの他の[例](speech-container-configuration.md#example-docker-run-commands)もご覧いただけます。
1. [コンテナーの予測エンドポイントに対するクエリを実行します](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>`docker run` によるコンテナーの実行

3 つのコンテナーのいずれかを実行するには、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用します。 このコマンドには、次のパラメーターが使用されます。

**プレビュー期間中**は、コンテナーを起動するために課金設定が有効になっている必要がありますが、使用量は課金されません。

| プレースホルダー | 値 |
|-------------|-------|
|{BILLING_KEY} | このキーは、コンテナーを起動するために使用され、Azure portal の Speech の [キー] ページで入手できます。  |
|{BILLING_ENDPOINT_URI} | 課金エンドポイント URI の値は、Azure portal の Speech の [概要] ページで入手できます。|

次の例の `docker run` コマンドでは、これらのパラメーターをお客様独自の値に置き換えてください。

### <a name="text-to-speech"></a>テキスト読み上げ

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

### <a name="speech-to-text"></a>音声テキスト変換

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから Speech コンテナーを実行します
* 2 つの CPU コアと 2 ギガバイト (GB) のメモリを割り当てます
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing)」を参照してください。

## <a name="query-the-containers-prediction-endpoint"></a>コンテナーの予測エンドポイントに対するクエリの実行

|コンテナー|エンドポイント|
|--|--|
|音声テキスト変換|ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1|
|テキスト読み上げ|http://localhost:5000/speech/synthesize/cognitiveservices/v1|

### <a name="speech-to-text"></a>音声テキスト変換

コンテナーは、websocket ベースのクエリ エンドポイント API シリーズを提供します。これには、[Speech SDK](index.yml) を介してアクセスします。

既定では、Speech SDK は、オンラインの音声サービスを使用します。 コンテナーを使用するには、初期化方法を変更する必要があります。 次の例を参照してください。

#### <a name="for-c"></a>C# の場合

この Azure クラウド初期化呼び出しを使用する方法を

```C#
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

次のようにコンテナー エンドポイントを使用する呼び出しに変更します。

```C#
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"),
    "YourSubscriptionKey");
```

#### <a name="for-python"></a>Python の場合

この Azure クラウド初期化呼び出しを使用する方法を

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

次のようにコンテナー エンドポイントを使用する呼び出しに変更します。

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

### <a name="text-to-speech"></a>テキスト読み上げ

コンテナーは、[こちら](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)にある REST エンドポイント API シリーズを提供します。サンプルについては、[こちら](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/)を参照してください。

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>コンテナーの停止

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>トラブルシューティング

コンテナーを実行するとき、コンテナーでは **stdout** と **stderr** を使用して、コンテナーの起動時または実行時に発生した問題のトラブルシューティングに役立つ情報を出力します。

## <a name="billing"></a>課金

Speech コンテナーは、Azure アカウントの _Speech_ リソースを使用して、Azure に課金情報を送信します。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

これらのオプションの詳細については、「[コンテナーの構成](speech-container-configuration.md)」を参照してください。

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>まとめ

この記事では、Speech コンテナーの概念とそのダウンロード、インストール、および実行のワークフローについて学習しました。 要約すると:

* Speech は、音声テキスト変換とテキスト読み上げをカプセル化した 2 つの Linux コンテナーを Docker 用に提供します。
* コンテナー イメージは、Azure のプライベート コンテナー レジストリからダウンロードされます。
* コンテナー イメージを Docker で実行します。
* REST API または SDK を使用して、コンテナーのホスト URI を指定することによって、Speech コンテナーの操作を呼び出すことができます。
* コンテナーをインスタンス化するときは、課金情報を指定する必要があります。

> [!IMPORTANT]
>  Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 Cognitive Services コンテナーが、顧客データ (解析対象の画像やテキストなど) を Microsoft に送信することはありません。

## <a name="next-steps"></a>次の手順

* 構成設定について、[コンテナーの構成](speech-container-configuration.md)を確認する
* さらに [Cognitive Services コンテナー](../cognitive-services-container-support.md)を使用する
