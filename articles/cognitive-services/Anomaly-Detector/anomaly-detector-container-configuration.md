---
title: Anomaly Detector API 用のコンテナーを構成する方法
titleSuffix: Azure Cognitive Services
description: Anomaly Detector API コンテナーのランタイム環境を構成するには、`docker run` コマンドの引数を使用します。 このコンテナーには、いくつかの必須の設定と省略可能な設定があります。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mbullwin
ms.openlocfilehash: c175a52259e9cfe5b4d03ce0279bbe24d16a48ae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94363716"
---
# <a name="configure-anomaly-detector-containers"></a>Anomaly Detector コンテナーを構成する

**Anomaly Detector** コンテナーのランタイム環境は、`docker run` コマンドの引数を使用して構成されます。 このコンテナーには、いくつかの必須の設定と省略可能な設定があります。 いくつかのコマンドの[例](#example-docker-run-commands)をご覧ください。 このコンテナーに固有の設定は、課金設定です。 

## <a name="configuration-settings"></a>構成設定

このコンテナーには、次の構成設定があります。

|必須|設定|目的|
|--|--|--|
|はい|[ApiKey](#apikey-configuration-setting)|課金情報の追跡に使用されます。|
|いいえ|[ApplicationInsights](#applicationinsights-setting)|[Azure Application Insights](/azure/application-insights) テレメトリ サポートをお客様のコンテナーに追加できます。|
|はい|[Billing](#billing-configuration-setting)|Azure 上のサービス リソースのエンドポイント URI を指定します。|
|はい|[Eula](#eula-setting)| コンテナーのライセンスに同意していることを示します。|
|いいえ|[Fluentd](#fluentd-settings)|ログと (必要に応じて) メトリック データを Fluentd サーバーに書き込みます。|
|いいえ|[Http Proxy](#http-proxy-credentials-settings)|送信要求を行うために、HTTP プロキシを構成します。|
|いいえ|[Logging](#logging-settings)|ASP.NET Core のログ サポートをお客様のコンテナーに提供します。 |
|いいえ|[Mounts](#mount-settings)|ホスト コンピューターからコンテナーに、またコンテナーからホスト コンピューターにデータを読み取ったり書き込んだりします。|

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting)、[`Eula`](#eula-setting) の各設定は一緒に使用されるため、それらの 3 つすべてに有効な値を指定する必要があります。そうしないと、お客様のコンテナーは起動しません。 これらの構成設定を使用してコンテナーをインスタンス化する方法の詳細については、「[課金](anomaly-detector-container-howto.md#billing)」を参照してください。

## <a name="apikey-configuration-setting"></a>ApiKey 構成設定

`ApiKey` 設定では、コンテナーの課金情報を追跡するために使用される Azure リソース キーを指定します。 ApiKey の値を指定する必要があります。また、その値は、[`Billing`](#billing-configuration-setting) 構成設定に指定された _Anomaly Detector_ リソースの有効なキーであることが必要です。

この設定は次の場所で確認できます。

* Azure portal: **Anomaly Detector の** [リソース管理] の **[キー]** の下

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 構成設定

`Billing` 設定は、コンテナーの課金情報を測定するために使用される Azure の _Anomaly Detector_ リソースのエンドポイント URI を指定します。 この構成設定の値を指定する必要があり、値は Azure の _Anomaly Detector_ リソースの有効なエンドポイント URI である必要があります。

この設定は次の場所で確認できます。

* Azure portal: **Anomaly Detector の** [概要] (ラベルが `Endpoint`)

|必須| 名前 | データ型 | 説明 |
|--|------|-----------|-------------|
|はい| `Billing` | String | 課金エンドポイント URI。 課金 URI の取得の詳細については、「[必須パラメーターの収集](anomaly-detector-container-howto.md#gathering-required-parameters)」を参照してください。 リージョンのエンドポイントの詳細および全一覧については、「[Cognitive Services のカスタム サブドメイン名](../cognitive-services-custom-subdomains.md)」を参照してください。 |

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

Anomaly Detector コンテナーでは、トレーニングやサービスのデータを格納するために入力マウントまたは出力マウントが使用されることはありません。 

ホストのマウント場所の厳密な構文は、ホスト オペレーティング システムによって異なります。 また、Docker サービス アカウントによって使用されるアクセス許可とホストのマウント場所のアクセス許可とが競合するために、[ホスト コンピューター](anomaly-detector-container-howto.md#the-host-computer)のマウント場所にアクセスできないこともあります。 

|省略可能| 名前 | データ型 | 説明 |
|-------|------|-----------|-------------|
|禁止| `Input` | String | Anomaly Detector コンテナーでは、これは使用されません。|
|オプション| `Output` | String | 出力マウントのターゲット。 既定値は `/output` です。 これはログの保存先です。 これには、コンテナーのログが含まれます。 <br><br>例:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>docker run コマンドの例 

以下の例では、`docker run` コマンドの記述方法と使用方法を示す構成設定が使用されています。  コンテナーは一度実行すると、お客様が[停止](anomaly-detector-container-howto.md#stop-the-container)するまで動作し続けます。

* **行連結文字**: 以降のセクションの Docker コマンドには、Bash シェルの行連結文字としてバック スラッシュ (`\`) が使用されています。 お客様のホスト オペレーティング システムの要件に応じて、置換または削除してください。 たとえば、Windows の行連結文字はキャレット (`^`) です。 バック スラッシュをキャレットで置き換えます。 
* **引数の順序**: Docker コンテナーについて高度な知識がある場合を除き、引数の順序は変更しないでください。

中かっこ `{}` の中の値を独自の値で置き換えます。

| プレースホルダー | 値 | 形式または例 |
|-------------|-------|---|
| **{API_KEY}** | Azure `Anomaly Detector` の [キー] ページの `Anomaly Detector` リソースのエンドポイント キー。 | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | 課金エンドポイントの値は、Azure `Anomaly Detector` の [概要] ページで確認できます。| 明示的な例が必要であれば、[必須パラメーターの収集](anomaly-detector-container-howto.md#gathering-required-parameters)に関するページを参照してください。 |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](anomaly-detector-container-howto.md#billing)」を参照してください。
> ApiKey の値は、Azure Anomaly Detector リソース キー ページにある **キー** です。 

## <a name="anomaly-detector-container-docker-examples"></a>Anomaly Detector コンテナー docker の例

次の docker 例は Anomaly Detector コンテナーに対するものです。 

### <a name="basic-example"></a>基本的な例 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>コマンドライン引数を使用したログの例

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>次のステップ

* [Anomaly Detector コンテナーを Azure Container Instances にデプロイする](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Anomaly Detector API サービスの詳細情報](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)