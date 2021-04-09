---
title: 読み取り OCR コンテナーを構成する - Computer Vision
titleSuffix: Azure Cognitive Services
description: この記事では、Computer Vision の読み取り OCR コンテナーに必須の設定とオプションの設定を構成する方法を説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: ee2e4fca697c086b95e83feb9d40ce8e07dc344c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102611897"
---
# <a name="configure-read-ocr-docker-containers"></a>読み取り OCR Docker コンテナーを構成する

`docker run` コマンドの引数を使用することによって、Computer Vision の読み取り OCR コンテナーのランタイム環境を構成します。 このコンテナーには、いくつかの必須の設定と省略可能な設定があります。 いくつかのコマンドの[例](#example-docker-run-commands)をご覧ください。 このコンテナーに固有の設定は、課金設定です。 

## <a name="configuration-settings"></a>構成設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting)、[`Eula`](#eula-setting) の各設定は一緒に使用されるため、それらの 3 つすべてに有効な値を指定する必要があります。そうしないと、お客様のコンテナーは起動しません。 これらの構成設定を使用してコンテナーをインスタンス化する方法の詳細については、「[課金](computer-vision-how-to-install-containers.md)」を参照してください。

このコンテナーには、次のコンテナー固有の構成設定もあります。

|必須|設定|目的|
|--|--|--|
|いいえ|ReadEngineConfig:ResultExpirationPeriod| v2.0 コンテナーのみ。 結果の有効期限 (時間)。 既定値は 48 時間です。 この設定によって、システムが認識結果をクリアするタイミングが指定されます。 たとえば、`resultExpirationPeriod=1` の場合、プロセスの 1 時間後に、システムによって認識結果がクリアされます。 `resultExpirationPeriod=0` の場合、結果が取得された後に、システムによって認識結果がクリアされます。|
|いいえ|Cache:Redis| v2.0 コンテナーのみ。 結果を格納するための Redis ストレージを有効にします。 ロード バランサーの背後に複数の読み取りコンテナーが配置されている場合は、キャッシュが "*必要*" です。|
|いいえ|Queue:RabbitMQ|v2.0 コンテナーのみ。 タスクをディスパッチするための RabbitMQ を有効にします。 この設定は、ロード バランサーの背後に複数の読み取りコンテナーが配置されている場合に便利です。|
|いいえ|Queue:Azure:QueueVisibilityTimeoutInMilliseconds | v3.x コンテナーのみ。 別のワーカーが処理しているときにメッセージが非表示になる時間。 |
|いいえ|Storage::DocumentStore::MongoDB|v2.0 コンテナーのみ。 永続的な結果ストレージ用に MongoDB を有効にします。 |
|いいえ|Storage:ObjectStore:AzureBlob:ConnectionString| v3.x コンテナーのみ。 Azure BLOB ストレージの接続文字列。 |
|いいえ|Storage:TimeToLiveInDays| v3.x コンテナーのみ。 結果の有効期限 (日数)。 この設定によって、システムが認識結果をクリアするタイミングが指定されます。 既定値は 2 日 (48 時間) です。これは、その期間より長く存続するすべての結果は、正常に取得されるとは限らないことを意味します。 |
|いいえ|Task:MaxRunningTimeSpanInMinutes| v3.x コンテナーのみ。 1 つの要求の最大実行時間。 既定値は 60 分です。 |

## <a name="apikey-configuration-setting"></a>ApiKey 構成設定

`ApiKey` 設定では、コンテナーの課金情報を追跡するために使用される Azure `Cognitive Services` リソース キーを指定します。 ApiKey の値を指定する必要があります。また、その値は、[`Billing`](#billing-configuration-setting) 構成設定に指定された _Cognitive Services_ リソースの有効なキーであることが必要です。

この設定は次の場所で確認できます。

* Azure portal:**Cognitive Services** の [リソース管理] の **[キー]** の下

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 構成設定

`Billing` 設定は、コンテナーの課金情報を測定するために使用される Azure の _Cognitive Services_ リソースのエンドポイント URI を指定します。 この構成設定の値を指定する必要があり、値は Azure の _Cognitive Services_ リソースの有効なエンドポイント URI である必要があります。 コンテナーから、約 10 ～ 15 分ごとに使用状況が報告されます。

この設定は次の場所で確認できます。

* Azure portal:**Cognitive Services** の [概要]。`Endpoint` として表示されます。

次の表に示したように、エンドポイント URI には、忘れずに `vision/v1.0` ルーティングを追加してください。 

|必須| 名前 | データ型 | 説明 |
|--|------|-----------|-------------|
|はい| `Billing` | String | 課金エンドポイント URI<br><br>例:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd の設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP プロキシ資格情報設定

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Logging の設定
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>マウントの設定

コンテナーとの間でデータを読み書きするには、バインド マウントを使用します。 入力マウントまたは出力マウントは、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドで `--mount` オプションを指定することによって指定できます。

Computer Vision コンテナーでは、トレーニングやサービスのデータを格納するために入力マウントまたは出力マウントが使用されることはありません。 

ホストのマウント場所の厳密な構文は、ホスト オペレーティング システムによって異なります。 また、Docker サービス アカウントによって使用されるアクセス許可とホストのマウント場所のアクセス許可とが競合するために、[ホスト コンピューター](computer-vision-how-to-install-containers.md#the-host-computer)のマウント場所にアクセスできないこともあります。 

|省略可能| 名前 | データ型 | 説明 |
|-------|------|-----------|-------------|
|禁止| `Input` | String | Computer Vision コンテナーでは、これは使用されません。|
|オプション| `Output` | String | 出力マウントのターゲット。 既定値は `/output` です。 これはログの保存先です。 これには、コンテナーのログが含まれます。 <br><br>例:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>docker run コマンドの例

以下の例では、`docker run` コマンドの記述方法と使用方法を示す構成設定が使用されています。  コンテナーは一度実行すると、お客様が[停止](computer-vision-how-to-install-containers.md#stop-the-container)するまで動作し続けます。

* **行連結文字**: 以降のセクションの Docker コマンドには、行連結文字としてバック スラッシュ (`\`) が使用されています。 お客様のホスト オペレーティング システムの要件に応じて、置換または削除してください。 
* **引数の順序**: Docker コンテナーについて高度な知識がある場合を除き、引数の順序は変更しないでください。

{ _<引数名>_ } はお客様独自の値に置き換えてください。

| プレースホルダー | 値 | 形式または例 |
|-------------|-------|---|
| **{API_KEY}** | Azure `Computer Vision` の [キー] ページの `Computer Vision` リソースのエンドポイント キー。 | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | 課金エンドポイントの値は、Azure `Computer Vision` の [概要] ページで確認できます。| 明示的な例が必要であれば、[必須パラメーターの収集](computer-vision-how-to-install-containers.md#gathering-required-parameters)に関するページを参照してください。 |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](computer-vision-how-to-install-containers.md#billing)」を参照してください。
> ApiKey の値は、Azure `Cognitive Services` リソース キー ページにある **キー** です。

## <a name="container-docker-examples"></a>コンテナーの Docker の例

次の Docker の例は、読み取りコンテナーに関するものです。


# <a name="version-32-preview"></a>[Version 3.2-preview](#tab/version-3-2)

### <a name="basic-example"></a>基本的な例

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.1 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>ログの例 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2-preview.1 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

# <a name="version-20-preview"></a>[Version 2.0-preview](#tab/version-2)

### <a name="basic-example"></a>基本的な例

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

### <a name="logging-example"></a>ログの例 

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>次のステップ

* [コンテナーのインストール方法と実行方法](computer-vision-how-to-install-containers.md)を確認します。
