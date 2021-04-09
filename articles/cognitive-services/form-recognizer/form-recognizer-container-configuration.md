---
title: Form Recognizer 向けコンテナーの構成方法
titleSuffix: Azure Cognitive Services
description: Form Recognizer コンテナーを構成してフォームとテーブルのデータを解析する方法を学習します。
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: 324b70fc810acc4faba4f488f821049f7eb0875e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "86538005"
---
# <a name="configure-form-recognizer-containers"></a>Form Recognizer コンテナーの構成

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Azure Form Recognizer コンテナーを使用することで、堅牢なクラウド機能とエッジのローカル性という両方のメリットを活用できるように最適化されたアプリケーション アーキテクチャを構築できます。

Form Recognizer コンテナーのランタイム環境は、`docker run` コマンドの引数を使用することによって構成します。 このコンテナーには、いくつかの必須の設定と省略可能な設定があります。 いくつかの例については、「[docker run コマンドの例](#example-docker-run-commands)」セクションを参照してください。 このコンテナーに固有の設定は、課金設定です。

## <a name="configuration-settings"></a>構成設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting)、および [`Eula`](#eula-setting) の各設定は一緒に使用されます。 3 つの設定すべてに有効な値を指定する必要があります。そうしないと、コンテナーは起動しません。 これらの構成設定を使用してコンテナーをインスタンス化する方法の詳細については、「[課金](form-recognizer-container-howto.md#billing)」を参照してください。

## <a name="apikey-configuration-setting"></a>ApiKey 構成設定

`ApiKey` 設定では、コンテナーの課金情報を追跡するために使用される Azure リソース キーを指定します。 ApiKey の値は、「Billing 構成設定」セクションで `Billing` に対して指定された _Form Recognizer_ リソースの有効なキーであることが必要です。

この設定は、Azure portal で、**Form Recognizer の [リソース管理]** の **[キー]** で確認できます。

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 構成設定

`Billing` 設定では、コンテナーの課金情報を測定するために使用される Azure の _Form Recognizer_ リソースのエンドポイント URI を指定します。 この構成設定の値は、Azure の _Form Recognizer_ リソースの有効なエンドポイント URI である必要があります。 コンテナーから、約 10 ～ 15 分ごとに使用状況が報告されます。

この設定は、Azure portal で、**Form Recognizer の [概要]** の **[エンドポイント]** で確認できます。

|必須| 名前 | データ型 | 説明 |
|--|------|-----------|-------------|
|はい| `Billing` | String | 課金エンドポイント URI。 課金 URI の取得の詳細については、「[必須パラメーターの収集](form-recognizer-container-howto.md#gathering-required-parameters)」を参照してください。 リージョンのエンドポイントの詳細および全一覧については、「[Cognitive Services のカスタム サブドメイン名](../cognitive-services-custom-subdomains.md)」を参照してください。 |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd の設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP プロキシ資格情報設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Logging の設定

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>マウントの設定

コンテナーとの間でデータを読み書きするには、バインド マウントを使用します。 入力マウントまたは出力マウントは、[`docker run` コマンド](https://docs.docker.com/engine/reference/commandline/run/)で `--mount` オプションを指定することによって指定できます。

Form Recognizer コンテナーには、入力マウントと出力マウントが必要です。 入力マウントは読み取り専用にすることができ、トレーニングおよびスコアリングに使用されるデータへのアクセスに必要です。 出力マウントは書き込み可能である必要があり、モデルと一時データの保管に使用します。

ホストのマウント場所の厳密な構文は、ホスト オペレーティング システムによって異なります。 また、Docker サービス アカウントのアクセス許可とホストのマウント場所のアクセス許可が競合するために、[ホスト コンピューター](form-recognizer-container-howto.md#the-host-computer)のマウント場所にアクセスできない場合があります。

|省略可能| 名前 | データ型 | 説明 |
|-------|------|-----------|-------------|
|必須| `Input` | String | 入力マウントのターゲット。 既定値は `/input` です。    <br><br>例:<br>`--mount type=bind,src=c:\input,target=/input`|
|必須| `Output` | String | 出力マウントのターゲット。 既定値は `/output` です。  <br><br>例:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>docker run コマンドの例

以下の例では、`docker run` コマンドの記述方法と使用方法を示す構成設定が使用されています。 コンテナーを実行すると、[停止](form-recognizer-container-howto.md#stop-the-container)されるまで動作し続けます。

* **行連結文字**: 以降のセクションの Docker コマンドには、行連結文字としてバック スラッシュ (\\) が使用されています。 ホスト オペレーティング システムの要件に応じて、この文字を置換または削除してください。
* **引数の順序**: Docker コンテナーについて知識がある場合を除き、引数の順序は変更しないでください。

次の表の {_argument_name_} を、独自の値に置き換えます。

| プレースホルダー | 値 |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | コンテナーを起動するために使用されるキー。 Azure portal の Form Recognizer の [キー] ページで入手できます。 |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | 課金エンドポイント URI の値は、Azure portal の Form Recognizer の [概要] ページで入手できます。|
| **{COMPUTER_VISION_API_KEY}** | このキーは、Azure portal の Computer Vision API の [キー] ページで入手できます。|
| **{COMPUTER_VISION_ENDPOINT_URI}** | 課金エンドポイント。 クラウドベースの Computer Vision リソースを使用している場合、URI 値は Azure portal の Computer Vision API の [概要] ページで入手できます。 *cognitive-services-recognize-text* コンテナーを使用している場合は、`docker run` コマンドでコンテナーに渡される課金エンドポイント URL を使用します。 |

これらの値を取得する方法の詳細については、[必要なパラメーターの収集](form-recognizer-container-howto.md#gathering-required-parameters)に関する記事を参照してください。

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定します。そうしないと、コンテナーが起動しません。 詳細については、「[課金](#billing-configuration-setting)」を参照してください。

## <a name="form-recognizer-container-docker-examples"></a>Form Recognizer コンテナーの Docker の例

次の Docker の例は、Form Recognizer コンテナーに関するものです。

### <a name="basic-example-for-form-recognizer"></a>Form Recognizer の基本的な例

```Docker
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

### <a name="logging-example-for-form-recognizer"></a>Form Recognizer のログ記録の例

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>次のステップ

* [コンテナーのインストールと実行](form-recognizer-container-howto.md)について確認します。
