---
title: コンテナーを構成する - Text Analytics
titleSuffix: Azure Cognitive Services
description: Text Analytics ではコンテナーごとに一般的な構成フレームワークが提供されているので、コンテナーのストレージ、ログとテレメトリ、セキュリティの設定を簡単に構成して、管理できます。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: f1c42002343de1dd3b3ef6b9c9e35f458db925f4
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051125"
---
# <a name="configure-text-analytics-docker-containers"></a>Text Analytics の docker コンテナーの構成

Text Analytics ではコンテナーごとに一般的な構成フレームワークが提供されているので、コンテナーのストレージ、ログとテレメトリ、セキュリティの設定を簡単に構成して、管理できます。

## <a name="configuration-settings"></a>構成設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting)、[`Eula`](#eula-setting) の各設定は一緒に使用されるため、それらの 3 つすべてに有効な値を指定する必要があります。そうしないと、お客様のコンテナーは起動しません。 これらの構成設定を使用してコンテナーをインスタンス化する方法の詳細については、「[課金](how-tos/text-analytics-how-to-install-containers.md#billing)」を参照してください。

## <a name="apikey-configuration-setting"></a>ApiKey 構成設定

`ApiKey` 設定では、コンテナーの課金情報を追跡するために使用される Azure リソース キーを指定します。 ApiKey の値を指定する必要があります。また、その値は、[`Billing`](#billing-configuration-setting) 構成設定に指定された _Text Analytics_ リソースの有効なキーであることが必要です。

この設定は次の場所で確認できます。

* Azure portal:**Text Analytics** リソース管理の **[キー]** の下

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 構成設定

`Billing` 設定は、コンテナーの課金情報を測定するために使用される Azure の _Text Analytics_ リソースのエンドポイント URI を指定します。 この構成設定の値を指定する必要があり、値は Azure の __Text Analytics_ リソースの有効なエンドポイント URI である必要があります。 コンテナーから、約 10 ～ 15 分ごとに使用状況が報告されます。

この設定は次の場所で確認できます。

* Azure portal:`Endpoint` というラベルの付いた **Text Analytics** の概要

|必須| 名前 | データ型 | 説明 |
|--|------|-----------|-------------|
|はい| `Billing` | string | 必要な課金エンドポイント URI |

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

|省略可能| 名前 | データ型 | 説明 |
|-------|------|-----------|-------------|
|禁止| `Input` | string | Text Analytics コンテナーでは、これは使用されません。|
|省略可能| `Output` | string | 出力マウントのターゲット。 既定値は `/output` です。 これはログの保存先です。 これには、コンテナーのログが含まれます。 <br><br>例:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>docker run コマンドの例 

以下の例では、`docker run` コマンドの記述方法と使用方法を示す構成設定が使用されています。  コンテナーは一度実行すると、お客様が[停止](how-tos/text-analytics-how-to-install-containers.md#stop-the-container)するまで動作し続けます。

* **行連結文字**: 以降のセクションの Docker コマンドには、行連結文字としてバック スラッシュ (`\`) が使用されています。 お客様のホスト オペレーティング システムの要件に応じて、置換または削除してください。 
* **引数の順序**: Docker コンテナーについて高度な知識がある場合を除き、引数の順序は変更しないでください。

{ _<引数名>_ } はお客様独自の値に置き換えてください。

| プレースホルダー | 値 | 形式または例 |
|-------------|-------|---|
| **{API_KEY}** | Azure `Text Analytics` の [キー] ページで使用可能な `Text Analytics` リソースのエンドポイント キー。 |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | 課金エンドポイントの値は、Azure `Text Analytics` の [概要] ページで確認できます。| 明示的な例が必要であれば、[必須パラメーターの収集](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters)に関するページを参照してください。 |

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](how-tos/text-analytics-how-to-install-containers.md#billing)」を参照してください。
> ApiKey の値は、Azure `Text Analytics` リソース キー ページにある**キー**です。 

#### <a name="key-phrase-extractiontabkeyphrase"></a>[キー フレーズ抽出](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detectiontablanguage"></a>[言語検出](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysistabsentiment"></a>[感情分析](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>次の手順

* [コンテナーのインストール方法と実行方法](how-tos/text-analytics-how-to-install-containers.md)を確認する。
* さらに [Azure Cognitive Services コンテナー](../cognitive-services-container-support.md)を使用する
