---
title: 音声コンテナーを構成する
titleSuffix: Azure Cognitive Services
description: 音声コンテナー
services: cognitive-services
author: ievangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: dapine
ms.openlocfilehash: 2dd1769d2d0a940176fb51954f44859cb42f30d9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072436"
---
# <a name="configure-speech-service-containers"></a>Speech Service コンテナーを構成する

音声コンテナーでは、堅牢なクラウド機能とエッジの局所性の両方を活用するように最適化された 1 つの音声アプリケーション アーキテクチャを構築できます。 Microsoft が現在サポートしている 2 つの音声コンテナーは、**音声テキスト変換**と**テキスト読み上げ**です。 

**音声**コンテナーのランタイム環境は、`docker run` コマンドの引数を使用して構成されます。 このコンテナーには、いくつかの必須の設定と省略可能な設定があります。 いくつかのコマンドの[例](#example-docker-run-commands)をご覧ください。 このコンテナーに固有の設定は、課金設定です。 

# <a name="configuration-settings"></a>構成設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting)、[`Eula`](#eula-setting) の各設定は一緒に使用されるため、それらの 3 つすべてに有効な値を指定する必要があります。そうしないと、お客様のコンテナーは起動しません。 これらの構成設定を使用してコンテナーをインスタンス化する方法の詳細については、「[課金](speech-container-howto.md#billing)」を参照してください。

## <a name="apikey-configuration-setting"></a>ApiKey 構成設定

`ApiKey` 設定では、コンテナーの課金情報を追跡するために使用される Azure リソース キーを指定します。 ApiKey の値を指定する必要があります。また、その値は、[`Billing`](#billing-configuration-setting) 構成設定に指定された "_音声_" リソースの有効なキーであることが必要です。

この設定は次の場所で確認できます。

* Azure portal:**音声の** [リソース管理] の **[キー]** の下

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 構成設定

`Billing` 設定では、コンテナーの課金情報を測定するために使用される Azure の "_音声_" リソースのエンドポイント URI を指定します。 この構成設定の値を指定する必要があり、値は Azure の "_音声_" リソースの有効なエンドポイント URI である必要があります。 コンテナーから、約 10 ～ 15 分ごとに使用状況が報告されます。

この設定は次の場所で確認できます。

* Azure portal:**音声の** [概要]、ラベル: `Endpoint`

|必須| Name | データ型 | 説明 |
|--|------|-----------|-------------|
|はい| `Billing` | string | 課金エンドポイント URI<br><br>例:<br>`Billing=https://westus.api.cognitive.microsoft.com/sts/v1.0` |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd の設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http プロキシ資格情報設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Logging の設定
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>マウントの設定

コンテナーとの間でデータを読み書きするには、バインド マウントを使用します。 入力マウントまたは出力マウントは、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドで `--mount` オプションを指定することによって指定できます。

音声コンテナーでは、トレーニングやサービスのデータを格納するために入力マウントまたは出力マウントが使用されることはありません。 

ホストのマウント場所の厳密な構文は、ホスト オペレーティング システムによって異なります。 また、Docker サービス アカウントによって使用されるアクセス許可とホストのマウント場所のアクセス許可とが競合するために、[ホスト コンピューター](speech-container-howto.md#the-host-computer)のマウント場所にアクセスできないこともあります。 

|省略可能| Name | データ型 | 説明 |
|-------|------|-----------|-------------|
|禁止| `Input` | string | 音声コンテナーでは、これは使用されません。|
|省略可能| `Output` | string | 出力マウントのターゲット。 既定値は `/output` です。 これはログの保存先です。 これには、コンテナーのログが含まれます。 <br><br>例:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>docker run コマンドの例 

以下の例では、`docker run` コマンドの記述方法と使用方法を示す構成設定が使用されています。  コンテナーは一度実行すると、お客様が[停止](speech-container-howto.md#stop-the-container)するまで動作し続けます。

* **行連結文字**: 以降のセクションの Docker コマンドには、行連結文字としてバック スラッシュ (`\`) が使用されています。 お客様のホスト オペレーティング システムの要件に応じて、置換または削除してください。 
* **引数の順序**: Docker コンテナーについて高度な知識がある場合を除き、引数の順序は変更しないでください。

{ _<引数名>_ } はお客様独自の値に置き換えてください。

| プレースホルダー | 値 | 形式または例 |
|-------------|-------|---|
|{BILLING_KEY} | 音声リソースのエンドポイント キー。 |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | リージョンを含む課金エンドポイントの値。|`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing-configuration-setting)」を参照してください。
> ApiKey の値は、Azure 音声リソース キー ページにある **[キー]** です。 

## <a name="speech-container-docker-examples"></a>音声コンテナーの Docker の例

次の Docker の例は、音声コンテナーに関するものです。 

### <a name="basic-example-for-speech-to-text"></a>音声テキスト変換の基本的な例

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}   
```

### <a name="basic-example-for-text-to-speech"></a>テキスト読み上げの基本的な例

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}  
```

### <a name="logging-example-for-speech-to-text"></a>音声テキスト変換のログ記録の例

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}   
  Logging:Console:LogLevel:Default=Information
```

### <a name="logging-example-for-text-to-speech"></a>テキスト読み上げのログ記録の例

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}  
  Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>次の手順

* [コンテナーのインストール方法と実行方法](speech-container-howto.md)を確認する。