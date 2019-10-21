---
title: Docker コンテナーの設定 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS コンテナーのランタイム環境は、`docker run` コマンドの引数を使用して構成されます。 LUIS には、いくつかの必須の設定と省略可能な設定があります。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 9760475886ecb0f20d9f0f3981eab8246643da21
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101977"
---
# <a name="configure-language-understanding-docker-containers"></a>Language Understanding の Docker コンテナーの構成 

**Language Understanding** (LUIS) コンテナーのランタイム環境は、`docker run` コマンドの引数を使用して構成されます。 LUIS には、いくつかの必須の設定と省略可能な設定があります。 いくつかのコマンドの[例](#example-docker-run-commands)をご覧ください。 このコンテナーに固有の設定は、入力[マウント設定](#mount-settings)と課金設定です。 

## <a name="configuration-settings"></a>構成設定

このコンテナーには、次の構成設定があります。

|必須|Setting|目的|
|--|--|--|
|はい|[ApiKey](#apikey-setting)|課金情報の追跡に使用されます。|
|いいえ|[ApplicationInsights](#applicationinsights-setting)|[Azure Application Insights](https://docs.microsoft.com/azure/application-insights) テレメトリ サポートをお客様のコンテナーに追加できます。|
|はい|[Billing](#billing-setting)|Azure 上のサービス リソースのエンドポイント URI を指定します。|
|はい|[Eula](#eula-setting)| コンテナーのライセンスに同意していることを示します。|
|いいえ|[Fluentd](#fluentd-settings)|ログと (必要に応じて) メトリック データを Fluentd サーバーに書き込みます。|
|いいえ|[HTTP プロキシ](#http-proxy-credentials-settings)|送信要求を行うために、HTTP プロキシを構成します。|
|いいえ|[Logging](#logging-settings)|ASP.NET Core のログ サポートをお客様のコンテナーに提供します。 |
|はい|[Mounts](#mount-settings)|ホスト コンピューターからコンテナーに、またコンテナーからホスト コンピューターにデータを読み取ったり書き込んだりします。|

> [!IMPORTANT]
> [`ApiKey`](#apikey-setting)、[`Billing`](#billing-setting)、[`Eula`](#eula-setting) の各設定は一緒に使用されるため、それらの 3 つすべてに有効な値を指定する必要があります。そうしないと、お客様のコンテナーは起動しません。 これらの構成設定を使用してコンテナーをインスタンス化する方法の詳細については、「[課金](luis-container-howto.md#billing)」を参照してください。

## <a name="apikey-setting"></a>ApiKey 設定

`ApiKey` 設定では、コンテナーの課金情報を追跡するために使用される Azure リソース キーを指定します。 ApiKey の値を指定する必要があります。また、その値は、[`Billing`](#billing-setting) 構成設定に指定された _Cognitive Services_ リソースの有効なキーであることが必要です。

この設定は次の場所で確認できます。

* Azure portal:**Cognitive Services** の [リソース管理] の **[キー]** の下
* LUIS ポータル: **[Keys and Endpoint settings]\(キーとエンドポイントの設定\)** ページ。 

スターター キーまたはオーサリング キーは使用しないでください。 

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Billing 設定

`Billing` 設定は、コンテナーの課金情報を測定するために使用される Azure の _Cognitive Services_ リソースのエンドポイント URI を指定します。 この構成設定の値を指定する必要があり、値は Azure の _Cognitive Services_ リソースの有効なエンドポイント URI である必要があります。 コンテナーから、約 10 ～ 15 分ごとに使用状況が報告されます。

この設定は次の場所で確認できます。

* Azure portal:**Cognitive Services** の [概要]。`Endpoint` として表示されます。
* LUIS ポータル: **[Keys and Endpoint settings]\(キーとエンドポイントの設定\)** ページ。エンドポイント URI の一部として表示されます。

次の表に示したように、URL には、忘れずに `luis/v2.0` ルーティングを含めてください。


|必須| 名前 | データ型 | 説明 |
|--|------|-----------|-------------|
|はい| `Billing` | string | 課金エンドポイント URI<br><br>例:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

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

LUIS コンテナーでは、トレーニングやサービスのデータを格納するために入力マウントまたは出力マウントが使用されることはありません。 

ホストのマウント場所の厳密な構文は、ホスト オペレーティング システムによって異なります。 また、Docker サービス アカウントによって使用されるアクセス許可とホストのマウント場所のアクセス許可とが競合するために、[ホスト コンピューター](luis-container-howto.md#the-host-computer)のマウント場所にアクセスできないこともあります。 

次の表で、サポートされる設定について説明します。

|必須| 名前 | データ型 | 説明 |
|-------|------|-----------|-------------|
|はい| `Input` | string | 入力マウントのターゲット。 既定値は `/input` です。 これは LUIS パッケージ ファイルの保存先です。 <br><br>例:<br>`--mount type=bind,src=c:\input,target=/input`|
|いいえ| `Output` | string | 出力マウントのターゲット。 既定値は `/output` です。 これはログの保存先です。 LUIS のクエリ ログやコンテナー ログが含まれます。 <br><br>例:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>docker run コマンドの例

以下の例では、`docker run` コマンドの記述方法と使用方法を示す構成設定が使用されています。  コンテナーは一度実行すると、お客様が[停止](luis-container-howto.md#stop-the-container)するまで動作し続けます。

* これらの例では、Windows 上のアクセス許可の競合を防ぐために、`C:` ドライブのディレクトリを使用しています。 特定のディレクトリを入力ディレクトリとして使用する必要がある場合は、Docker サービスのアクセス許可の付与が必要なことがあります。 
* Docker コンテナーについて高度な知識がある場合を除き、引数の順序は変更しないでください。
* 別のオペレーティング システムを使用している場合は、正しいコンソール/ターミナル、マウント用のフォルダー構文、ご利用のシステムの行連結文字を使用します。 これらの例では、行連結文字 `^` を使用した Windows コンソールを想定しています。 コンテナーは Linux オペレーティング システムであるため、ターゲット マウントでは Linux スタイルのフォルダー構文を使用します。

次の表に示したように、URL には、忘れずに `luis/v2.0` ルーティングを含めてください。

{ _<引数名>_ } はお客様独自の値に置き換えてください。

| プレースホルダー | 値 | 形式または例 |
|-------------|-------|---|
| **{API_KEY}** | Azure `LUIS` の [キー] ページの `LUIS` リソースのエンドポイント キー。 | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | 課金エンドポイントの値は、Azure `LUIS` の [概要] ページで確認できます。| 明示的な例が必要であれば、[必須パラメーターの収集](luis-container-howto.md#gathering-required-parameters)に関するページを参照してください。 |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。 詳細については、「[課金](luis-container-howto.md#billing)」を参照してください。
> ApiKey の値は、LUIS ポータルの [Keys and Endpoints]\(キーとエンドポイント\) ページにある **[キー]** です。また、Azure `Cognitive Services` リソース キー ページで確認することもできます。 

### <a name="basic-example"></a>基本的な例

次の例には、コンテナーを実行できるだけの最低限の引数が含まれています。

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>ApplicationInsights の例

次の例では、コンテナーの実行中にテレメトリを Application Insights に送信するように、ApplicationInsights の引数を設定しています。

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>ログの例 

次のコマンドでは、ログ レベル (`Logging:Console:LogLevel`) を設定することにより、ログ レベルを [`Information`](https://msdn.microsoft.com) に構成しています。 

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>次の手順

* [コンテナーのインストール方法と実行方法](luis-container-howto.md)を確認する。
* [トラブルシューティング](troubleshooting.md)に関するページを参照して、LUIS 機能に関連する問題を解決する。
* さらに [Cognitive Services コンテナー](../cognitive-services-container-support.md)を使用する
