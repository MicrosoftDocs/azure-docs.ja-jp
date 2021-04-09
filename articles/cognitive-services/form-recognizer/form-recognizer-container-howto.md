---
title: Form Recognizer 向けコンテナーのインストールと実行
titleSuffix: Azure Cognitive Services
description: この記事では、Azure Cognitive Services Form Recognizer コンテナーを使用し、フォームおよびテーブル データを解析する方法について説明します。
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 1be8afb58b22435f4f43b2d6884332a38b7f1e11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467478"
---
# <a name="install-and-run-form-recognizer-containers-retiring"></a>Form Recognizer コンテナーのインストールと実行 (廃止)

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Azure Form Recognizer では、機械学習テクノロジを適用して、フォームにあるキーと値のペアおよびテーブルを識別して抽出します。 値とテーブル エントリをキー値のペアに関連付けてから、元のファイル内の関係を含む構造化データを出力します。 

複雑さを軽減し、ワークフロー自動化プロセスまたは他のアプリケーションにカスタム Form Recognizer モデルを簡単に統合するために、単純な REST API を使用してモデルを呼び出すことができます。 必要なフォーム ドキュメントは 5 つだけなので、特定のコンテンツに合わせてすばやく正確に結果を得ることができます。 手動での大掛かりな介入や広範なデータ サイエンスの専門知識は必要ありません。 また、データのラベル付けやデータの注釈付けは必要ありません。

| 機能 | 特徴 |
|----------|----------|
| Form Recognizer | <li>PDF、PNG、JPG ファイルを処理する<li>同じレイアウトのフォームを少なくとも 5 つ使用してカスタム モデルをトレーニングする <li>キーと値のペアおよびテーブル情報を抽出する <li>Azure Cognitive Service の Computer Vision API のテキスト認識機能を使用して、フォーム内の画像から印刷されたテキストを検出し、抽出する<li>注釈またはラベル付けを必要としない |

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。

## <a name="prerequisites"></a>前提条件

Form Recognizer コンテナーを使用する前に、次の前提条件を満たす必要があります。

| 必須 | 目的 |
|----------|---------|
| Docker エンジン | [ホスト コンピューター](#the-host-computer)に Docker エンジンをインストールしておく必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上で Docker 環境の構成を行うパッケージが用意されています。 Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。<br><br> コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 <br><br> Windows では、Linux コンテナーをサポートするように Docker を構成することも必要です。<br><br> |
| Docker に関する知識 | レジストリ、リポジトリ、コンテナー、コンテナー イメージなど、Docker の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) をホストにインストールします。 |
| Computer Vision API リソース | スキャンしたドキュメントおよび画像を処理するには、Computer Vision リソースが必要です。 Azure リソース (REST API または SDK) または *cognitive-services-recognize-text* [コンテナー](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)のいずれかとして、テキスト認識機能にアクセスできます。 通常の課金の料金が適用されます。 <br><br>Computer Vision リソース (Azure クラウドまたは Cognitive Services コンテナー) の API キーとエンドポイントの両方を渡します。 この API キーとエンドポイントを **{COMPUTER_VISION_API_KEY}** と **{COMPUTER_VISION_ENDPOINT_URI}** として使用します。<br><br> *cognitive-services-recognize-text* コンテナーを使用する場合は、以下のことを確認します。<br><br>Form Recognizer コンテナーの Computer Vision キーは、*cognitive-services-recognize-text* コンテナーの Computer Vision `docker run` コマンドで指定されたキーです。<br>課金エンドポイントは、コンテナーのエンドポイントです (`http://localhost:5000` など)。 同じホスト上で Computer Vision コンテナーと Form Recognizer コンテナーの両方を一緒に使用する場合、既定のポート *5000* を使用してその両方を起動することはできません。 |
| Form Recognizer リソース | これらのコンテナーを使用するためには、以下が必要です。<br><br>関連付けられている API キーとエンドポイント URI を取得するための Azure **Form Recognizer** リソース。 どちらの値も、Azure portal の **Form Recognizer** の [概要] ページと [キー] ページで入手でき、コンテナーを起動するには両方の値が必要です。<br><br>**{FORM_RECOGNIZER_API_KEY}** :[キー] ページにある 2 つのリソース キーのうちのどちらか<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}** :[概要] ページで提供されるエンドポイント |

> [!NOTE]
> Computer Vision リソース名は、ハイフン `-` または他の特殊文字を含まない 1 つの単語にする必要があります。 この制限は、Form Recognizer とテキスト認識コンテナーの互換性を保証するために設けられています。

## <a name="gathering-required-parameters"></a>必須パラメーターの収集

すべての Cognitive Services のコンテナーに対して必須である、3 つの主要なパラメーターがあります。 エンドユーザー使用許諾契約書 (EULA) は、`accept` の値と共に提示される必要があります。 さらに、エンドポイント URL と API キーの両方が必要です。

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>エンドポイント URI `{COMPUTER_VISION_ENDPOINT_URI}` と `{FORM_RECOGNIZER_ENDPOINT_URI}`

**エンドポイント** URI の値は、Azure portal で、対応する Cognitive Service リソースの *[概要]* ページで入手できます。 *[概要]* ページに移動して、エンドポイントの上にマウス ポインターを移動すると、`Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> アイコンが表示されます。 必要に応じてコピーして使用します。

![後で使用するためにエンドポイント URI を収集する](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>キー `{COMPUTER_VISION_API_KEY}` と `{FORM_RECOGNIZER_API_KEY}`

このキーはコンテナーを起動するために使用され、Azure portal で、対応する Cognitive Service リソースの [キー] ページで入手できます。 *[キー]* ページに移動し、`Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> アイコンをクリックします。

![後で使用するために 2 つのキーのどちらかを入手する](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> これらのサブスクリプション キーは、Cognitive Service API にアクセスするために使用されます。 キーを共有しないでください。 Azure Key Vault を使用するなどして、安全に保管してください。 これらのキーを定期的に再生成することもお勧めします。 API 呼び出しを行うために必要なキーは 1 つだけです。 最初のキーを再生成するときに、2 番目のキーを使用してサービスに継続的にアクセスすることができます。

## <a name="the-host-computer"></a>ホスト コンピューター

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

次の表に、各 Form Recognizer コンテナーに割り当てる CPU コアとメモリの最小値と推奨値を示します。

| コンテナー | 最小値 | 推奨 |
|-----------|---------|-------------|
| Form Recognizer | 2 コア、4 GB メモリ | 4 コア、8 GB メモリ |
| テキスト認識 | 1 コア、8 GB のメモリ | 2 コア、8 GB のメモリ |

* 各コアは少なくとも 2.6 ギガヘルツ (GHz) 以上にする必要があります。
* コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

> [!Note]
> 最小値と推奨値は、Docker の制限に基づくもので、ホスト マシンのリソースに基づくものでは *ありません*。

Form Recognizer とテキスト認識の両方のコンテナーが必要になります。**テキスト認識** コンテナー [の詳細は、この記事の範囲に含まれないことに注意してください。](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>コンテナーを使用する方法

コンテナーを[ホスト コンピューター](#the-host-computer)上に用意できたら、次の手順を使用してコンテナーを操作します。

1. 必要な課金設定を使用して[コンテナーを実行](#run-the-container-by-using-the-docker-run-command)します。 `docker run` コマンドの他の[例](form-recognizer-container-configuration.md#example-docker-run-commands)もご覧いただけます。
1. [コンテナーの予測エンドポイントに対するクエリを実行します](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-by-using-the-docker-run-command"></a>docker run コマンドを使用してコンテナーを実行する

コンテナーを実行するには、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用します。 `{COMPUTER_VISION_ENDPOINT_URI}`、`{COMPUTER_VISION_API_KEY}`、`{FORM_RECOGNIZER_ENDPOINT_URI}`、および `{FORM_RECOGNIZER_API_KEY}` の値を取得する方法の詳細については、[必要なパラメーターの収集](#gathering-required-parameters)に関する記事を参照してください。

`docker run` コマンドの[例](form-recognizer-container-configuration.md#example-docker-run-commands)を利用できます。

### <a name="form-recognizer"></a>Form Recognizer

> [!NOTE]
> これらの例の `--mount` に使用されるディレクトリは、Windows のディレクトリ パスです。 Linux または macOS を使用している場合は、環境に合わせてパラメーターを変更してください。 

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから Form Recognizer コンテナーを実行します。
* 2 つの CPU コアと 8 ギガバイト (GB) のメモリを割り当てます。
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます。
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。
* /input と /output のボリュームをコンテナーにマウントします。

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>別個のコンテナーを別個の docker run コマンドとして実行する

同じホスト上でローカルにホストされている Form Recognizer とテキスト認識エンジンの組み合わせに対して、以下に示す 2 つの Docker CLI コマンドの例を実行します。

ポート 5000 上で最初のコンテナーを実行します。 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

ポート 5001 上で 2 番目のコンテナーを実行します。

```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
後続の各コンテナーは、別のポート上になっている必要があります。 

### <a name="run-separate-containers-with-docker-compose"></a>Docker Compose を使用して別個のコンテナーを実行する

同じホスト上でローカルにホストされている Form Recognizer とテキスト認識エンジンの組み合わせについて、Docker Compose YAML ファイルの次の例を参照してください。 テキスト認識エンジンの `{COMPUTER_VISION_API_KEY}` は、`formrecognizer` と `ocr` の両方のコンテナーで同じでなければなりません。 `formrecognizer` コンテナーでは `ocr` の名前とポートを使用するため、`{COMPUTER_VISION_ENDPOINT_URI}` は `ocr` コンテナーでのみ使用されます。 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: "{COMPUTER_VISION_API_KEY}"

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{FORM_RECOGNIZER_ENDPOINT_URI}"
      apikey: "{FORM_RECOGNIZER_API_KEY}"
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"
```

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` に加えて `FormRecognizer:ComputerVisionApiKey` と `FormRecognizer:ComputerVisionEndpointUri` のオプションを指定する必要があります。そうしないと、コンテナーが起動しません。 詳細については、「[課金](#billing)」を参照してください。

## <a name="query-the-containers-prediction-endpoint"></a>コンテナーの予測エンドポイントに対するクエリの実行

|コンテナー|エンドポイント|
|--|--|
|form-recognizer|http://localhost:5000

### <a name="form-recognizer"></a>Form Recognizer

コンテナーは、websocket ベースのクエリ エンドポイント API シリーズを提供します。これには、[Form Recognizer サービス SDK のドキュメント](./index.yml)を介してアクセスします。

Form Recognizer SDK では、既定でオンライン サービスを使用します。 コンテナーを使用するには、初期化方法を変更する必要があります。 次の例を参照してください。

#### <a name="for-c"></a>C# の場合

この Azure クラウド初期化呼び出しを使用する方法を

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
次のようにコンテナー エンドポイントを使用する呼び出しに変更します。

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>Python の場合

この Azure クラウド初期化呼び出しを使用する方法を

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

次のようにコンテナー エンドポイントを使用する呼び出しに変更します。

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Form Recognizer

コンテナーには、REST エンドポイント API が用意されています。これは、Form Recognizer API リファレンス ページ (https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) ) にあります。


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>コンテナーの停止

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>トラブルシューティング

出力[マウント](form-recognizer-container-configuration.md#mount-settings)とログを有効にした状態でコンテナーを実行すると、コンテナーによってログ ファイルが生成されます。これらはコンテナーの起動時または実行時に発生した問題のトラブルシューティングに役立ちます。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>課金

Form Recognizer コンテナーでは、Azure アカウントの _Form Recognizer_ リソースを使用して、Azure に課金情報を送信します。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

これらのオプションの詳細については、「[コンテナーの構成](form-recognizer-container-configuration.md)」を参照してください。

## <a name="summary"></a>まとめ

この記事では、Form Recognizer コンテナーの概念とそのダウンロード、インストール、および実行のワークフローについて学習しました。 要約すると:

* Form Recognizer は、Docker 用の Linux コンテナーを 1 つ提供します。
* コンテナー イメージは、Azure のプライベート コンテナー レジストリからダウンロードされます。
* コンテナー イメージを Docker で実行します。
* REST API または REST SDK のいずれかを使用して、コンテナーのホスト URI を指定することによって、Form Recognizer コンテナーの操作を呼び出すことができます。
* コンテナーをインスタンス化するときには、課金情報を指定する必要があります。

> [!IMPORTANT]
>  Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 Cognitive Services コンテナーが、顧客データ (解析対象の画像やテキストなど) を Microsoft に送信することはありません。

## <a name="next-steps"></a>次のステップ

* 構成設定について、[コンテナーの構成](form-recognizer-container-configuration.md)を確認します。
* さらに [Cognitive Services コンテナー](../cognitive-services-container-support.md)を使用します。