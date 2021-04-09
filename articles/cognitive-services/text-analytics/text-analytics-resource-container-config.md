---
title: コンテナーを構成する - Text Analytics
titleSuffix: Azure Cognitive Services
description: Text Analytics ではコンテナーごとに一般的な構成フレームワークが提供されているので、コンテナーのストレージ、ログとテレメトリ、セキュリティの設定を簡単に構成して、管理できます。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f6a1bc652125990a7daf3414895f34b95c544912
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "83590556"
---
# <a name="configure-text-analytics-docker-containers"></a>Text Analytics の docker コンテナーの構成

Text Analytics ではコンテナーごとに一般的な構成フレームワークが提供されているので、コンテナーのストレージ、ログとテレメトリ、セキュリティの設定を簡単に構成して、管理できます。 いくつかの [Docker 実行コマンドの例](how-tos/text-analytics-how-to-install-containers.md#run-the-container-with-docker-run)も利用できます。

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

`Billing` 設定は、コンテナーの課金情報を測定するために使用される Azure の _Text Analytics_ リソースのエンドポイント URI を指定します。 この構成設定の値を指定する必要があり、値は Azure の _ _Text Analytics_ リソースの有効なエンドポイント URI である必要があります。 コンテナーから、約 10 ～ 15 分ごとに使用状況が報告されます。

この設定は次の場所で確認できます。

* Azure portal:`Endpoint` というラベルの付いた **Text Analytics** の概要

|必須| 名前 | データ型 | 説明 |
|--|------|-----------|-------------|
|はい| `Billing` | String | 課金エンドポイント URI。 課金 URI の取得の詳細については、「[必須パラメーターの収集](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters)」を参照してください。 リージョンのエンドポイントの詳細および全一覧については、「[Cognitive Services のカスタム サブドメイン名](../cognitive-services-custom-subdomains.md)」を参照してください。 |

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
|禁止| `Input` | String | Text Analytics コンテナーでは、これは使用されません。|
|省略可能| `Output` | String | 出力マウントのターゲット。 既定値は `/output` です。 これはログの保存先です。 これには、コンテナーのログが含まれます。 <br><br>例:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="next-steps"></a>次のステップ

* [コンテナーのインストール方法と実行方法](how-tos/text-analytics-how-to-install-containers.md)を確認する。
* さらに [Cognitive Services コンテナー](../cognitive-services-container-support.md)を使用する
