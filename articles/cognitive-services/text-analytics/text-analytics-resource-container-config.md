---
title: コンテナーを構成する
titlesuffix: Text Analytics - Azure Cognitive Services
description: Text Analytics ではコンテナーごとに一般的な構成フレームワークが提供されているので、コンテナーのストレージ、ログとテレメトリ、セキュリティの設定を簡単に構成して、管理できます。
services: cognitive-services
author: ievangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: dapine
ms.openlocfilehash: 604b17bc3d4696ff0bf2be1759a53927a531fe13
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296017"
---
# <a name="configure-text-analytics-docker-containers"></a>Text Analytics の docker コンテナーの構成

Text Analytics ではコンテナーごとに一般的な構成フレームワークが提供されているので、コンテナーのストレージ、ログとテレメトリ、セキュリティの設定を簡単に構成して、管理できます。

## <a name="configuration-settings"></a>構成設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting)、[`Eula`](#eula-setting) の各設定は一緒に使用されるため、それらの 3 つすべてに有効な値を指定する必要があります。そうしないと、お客様のコンテナーは起動しません。 これらの構成設定を使用してコンテナーをインスタンス化する方法の詳細については、「[課金](how-tos/text-analytics-how-to-install-containers.md#billing)」を参照してください。

## <a name="apikey-configuration-setting"></a>ApiKey 構成設定

`ApiKey` 設定では、コンテナーの課金情報を追跡するために使用される Azure リソース キーを指定します。 ApiKey の値を指定する必要があります。また、その値は、[`Billing`](#billing-configuration-setting) 構成設定に指定された _Cognitive Services_ リソースの有効なキーであることが必要です。

この設定は次の場所で確認できます。

* Azure portal:**Cognitive Services** の [リソース管理] の **[キー]** の下

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 構成設定

`Billing` 設定は、コンテナーの課金情報を測定するために使用される Azure の _Cognitive Services_ リソースのエンドポイント URI を指定します。 この構成設定の値を指定する必要があり、値は Azure の _Cognitive Services_ リソースの有効なエンドポイント URI である必要があります。 コンテナーから、約 10 ～ 15 分ごとに使用状況が報告されます。

この設定は次の場所で確認できます。

* Azure portal:**Cognitive Services** の [概要]。`Endpoint` として表示されます。

`text/analytics/v2.0` ルーティングをエンドポイント URI に追加する必要があります。次の BILLING_ENDPOINT_URI の例を参照してください。

|必須| Name | データ型 | 説明 |
|--|------|-----------|-------------|
|はい| `Billing` | string | 課金エンドポイント URI<br><br>例:<br>`Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.1` |

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

Text Analytics コンテナーでは、トレーニングやサービスのデータを格納するために入力マウントまたは出力マウントが使用されることはありません。 

ホストのマウント場所の厳密な構文は、ホスト オペレーティング システムによって異なります。 また、Docker サービス アカウントによって使用されるアクセス許可とホストのマウント場所のアクセス許可とが競合するために、[ホスト コンピューター](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)のマウント場所にアクセスできないこともあります。 

|省略可能| Name | データ型 | 説明 |
|-------|------|-----------|-------------|
|禁止| `Input` | string | Text Analytics コンテナーでは、これは使用されません。|
|省略可能| `Output` | string | 出力マウントのターゲット。 既定値は `/output` です。 これはログの保存先です。 これには、コンテナーのログが含まれます。 <br><br>例:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>docker run コマンドの例 

以下の例では、`docker run` コマンドの記述方法と使用方法を示す構成設定が使用されています。  コンテナーは一度実行すると、お客様が[停止](how-tos/text-analytics-how-to-install-containers.md#stop-the-container)するまで動作し続けます。

* **行連結文字**: 以降のセクションの Docker コマンドには、行連結文字としてバック スラッシュ (`\`) が使用されています。 お客様のホスト オペレーティング システムの要件に応じて、置換または削除してください。 
* **引数の順序**: Docker コンテナーについて高度な知識がある場合を除き、引数の順序は変更しないでください。

`text/analytics/v2.0` ルーティングをエンドポイント URI に追加する必要があります。次の BILLING_ENDPOINT_URI の例を参照してください。

{ _<引数名>_ } はお客様独自の値に置き換えてください。

| プレースホルダー | 値 | 形式または例 |
|-------------|-------|---|
|{BILLING_KEY} | Azure `Cognitive Services` の [キー] ページで使用可能な `Cognitive Services` リソースのエンドポイント キー。 |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | 課金エンドポイントの値は、Azure `Cognitive Services` の [概要] ページで確認できます。|`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](how-tos/text-analytics-how-to-install-containers.md#billing)」を参照してください。
> ApiKey の値は、Azure `Cognitive Services` リソース キー ページにある**キー**です。 

## <a name="key-phrase-extraction-container-docker-examples"></a>キー フレーズ抽出コンテナー docker の例

次の docker 例はキー フレーズ抽出コンテナーに対するものです。 

### <a name="basic-example"></a>基本的な例 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>ログの例 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel:Default=Information
  ```

## <a name="language-detection-container-docker-examples"></a>言語検出コンテナー docker の例

次の docker 例は言語検出コンテナーに対するものです。 

### <a name="basic-example"></a>基本的な例

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>ログの例

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel:Default=Information
  ```
 
## <a name="sentiment-analysis-container-docker-examples"></a>感情分析コンテナー docker の例

次の docker 例は感情分析コンテナーに対するものです。 

### <a name="basic-example"></a>基本的な例

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>ログの例

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>次の手順

* [コンテナーのインストール方法と実行方法](how-tos/text-analytics-how-to-install-containers.md)を確認する。
* さらに [Azure Cognitive Services コンテナー](../cognitive-services-container-support.md)を使用する
