---
title: 音声コンテナーを構成する
titleSuffix: Azure Cognitive Services
description: Speech Service では、コンテナーごとに一般的な構成フレームワークが提供されているので、コンテナーのストレージ、ログとテレメトリ、セキュリティの設定を簡単に構成して管理できます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: e65bb7c7d8fc04baec6b50a53519e689e748fbe1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96012171"
---
# <a name="configure-speech-service-containers"></a>Speech Service コンテナーを構成する

Speech コンテナーでは、堅牢なクラウド機能とエッジの局所性の両方を活用するように最適化された 1 つの音声アプリケーション アーキテクチャを構築できます。 現在サポートされている 5 つの音声コンテナーは、**音声テキスト変換**、**カスタム音声テキスト変換**、**テキスト読み上げ**、**ニューラル テキスト読み上げ**、および **カスタム テキスト読み上げ** です。

**音声** コンテナーのランタイム環境は、`docker run` コマンドの引数を使用して構成されます。 このコンテナーには、いくつかの必須の設定と省略可能な設定があります。 いくつかのコマンドの[例](#example-docker-run-commands)をご覧ください。 このコンテナーに固有の設定は、課金設定です。

## <a name="configuration-settings"></a>構成設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting)、[`Eula`](#eula-setting) の各設定は一緒に使用されるため、それらの 3 つすべてに有効な値を指定する必要があります。そうしないと、お客様のコンテナーは起動しません。 これらの構成設定を使用してコンテナーをインスタンス化する方法の詳細については、「[課金](speech-container-howto.md#billing)」を参照してください。

## <a name="apikey-configuration-setting"></a>ApiKey 構成設定

`ApiKey` 設定では、コンテナーの課金情報を追跡するために使用される Azure リソース キーを指定します。 ApiKey の値を指定する必要があります。また、その値は、[`Billing`](#billing-configuration-setting) 構成設定に指定された "_音声_" リソースの有効なキーであることが必要です。

この設定は次の場所で確認できます。

- Azure portal:**音声の** [リソース管理] の **[キー]** の下

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 構成設定

`Billing` 設定では、コンテナーの課金情報を測定するために使用される Azure の "_音声_" リソースのエンドポイント URI を指定します。 この構成設定の値を指定する必要があり、値は Azure の "_音声_" リソースの有効なエンドポイント URI である必要があります。 コンテナーから、約 10 ～ 15 分ごとに使用状況が報告されます。

この設定は次の場所で確認できます。

- Azure portal:**音声の** [概要]、ラベル: `Endpoint`

| 必須 | 名前 | データ型 | 説明 |
| -------- | ---- | --------- | ----------- |
| はい | `Billing` | String | 課金エンドポイント URI。 課金 URI の取得の詳細については、「[必須パラメーターの収集](speech-container-howto.md#gathering-required-parameters)」を参照してください。 リージョンのエンドポイントの詳細および全一覧については、「[Cognitive Services のカスタム サブドメイン名](../cognitive-services-custom-subdomains.md)」を参照してください。 |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd の設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP プロキシ資格情報設定

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Logging の設定

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>マウントの設定

コンテナーとの間でデータを読み書きするには、バインド マウントを使用します。 入力マウントまたは出力マウントは、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドで `--mount` オプションを指定することによって指定できます。

標準的な音声コンテナーでは、トレーニングやサービスのデータを格納するために入力マウントまたは出力マウントが使用されることはありません。 ただし、カスタム音声コンテナーはボリュームのマウントに依存します。

ホストのマウント場所の厳密な構文は、ホスト オペレーティング システムによって異なります。 また、Docker サービス アカウントによって使用されるアクセス許可とホストのマウント場所のアクセス許可とが競合するために、[ホスト コンピューター](speech-container-howto.md#the-host-computer)のマウント場所にアクセスできないこともあります。

| 省略可能 | 名前 | データ型 | 説明 |
| -------- | ---- | --------- | ----------- |
| 禁止 | `Input` | String | 標準的な音声コンテナーでは、これは使用されません。 カスタム音声コンテナーでは、[ボリュームのマウント](#volume-mount-settings)が使用されます。                                                                                    |
| 省略可能 | `Output` | String | 出力マウントのターゲット。 既定値は `/output` です。 これはログの保存先です。 これには、コンテナーのログが含まれます。 <br><br>例:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>ボリュームのマウントの設定

カスタム音声コンテナーでは、[ボリュームのマウント](https://docs.docker.com/storage/volumes/)を使用して、カスタム モデルが保持されます。 `-v` (または `--volume`) オプションを [docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドに追加することで、ボリュームのマウントを指定できます。

カスタム モデルは、カスタム音声コンテナーの docker run コマンドの一部として新しいモデルが初めて取り込まれるときにダウンロードされます。 カスタム音声コンテナーに対する同じ `ModelId` の連続実行では、ダウンロード済みのモデルが使用されます。 ボリュームのマウントが指定されていない場合、カスタム モデルを永続化することはできません。

ボリュームのマウントの設定は、3 つの色付きの `:` で区切られたフィールドで構成されます。

1. 最初のフィールドは、ホスト コンピューター上のボリュームの名前です (たとえば _C:\input_)。
2. 2 番目のフィールドは、コンテナー内のディレクトリです (たとえば _/usr/local/models_)。
3. 3 番目のフィールド (省略可能) は、オプションのコンマ区切りの一覧です。詳細については、[ボリュームの使用](https://docs.docker.com/storage/volumes/)に関する記事を参照してください。

### <a name="volume-mount-example"></a>ボリュームのマウントの例

```bash
-v C:\input:/usr/local/models
```

このコマンドでは、ホスト コンピューターの _C:\input_ ディレクトリが、コンテナーの _/usr/local/models_ にマウントされます。

> [!IMPORTANT]
> ボリュームのマウント設定が適用されるのは、**カスタム音声テキスト変換** と **カスタム テキスト読み上げ** コンテナーだけです。 **音声テキスト変換**、**ニューラル テキスト読み上げ**、および **テキスト読み上げ** コンテナーでは、ボリュームのマウントは使用されません。

## <a name="example-docker-run-commands"></a>docker run コマンドの例

以下の例では、`docker run` コマンドの記述方法と使用方法を示す構成設定が使用されています。 コンテナーは一度実行すると、お客様が[停止](speech-container-howto.md#stop-the-container)するまで動作し続けます。

- **行連結文字**: 以降のセクションの Docker コマンドには、行連結文字としてバック スラッシュ (`\`) が使用されています。 お客様のホスト オペレーティング システムの要件に応じて、置換または削除してください。
- **引数の順序**: Docker コンテナーについて高度な知識がある場合を除き、引数の順序は変更しないでください。

{ _<引数名>_ } はお客様独自の値に置き換えてください。

| プレースホルダー | 値 | 形式または例 |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | Azure `Speech` の [キー] ページの `Speech` リソースのエンドポイント キー。   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | 課金エンドポイントの値は、Azure `Speech` の [概要] ページで確認できます。 | 明示的な例が必要であれば、[必須パラメーターの収集](speech-container-howto.md#gathering-required-parameters)に関するページを参照してください。 |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。 詳細については、「[課金](#billing-configuration-setting)」を参照してください。
> ApiKey の値は、Azure 音声リソース キー ページにある **[キー]** です。

## <a name="speech-container-docker-examples"></a>音声コンテナーの Docker の例

次の Docker の例は、音声コンテナーに関するものです。

## <a name="speech-to-text"></a>[音声テキスト変換](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>音声テキスト変換の基本的な例

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>音声テキスト変換のログ記録の例

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-text"></a>[カスタム音声変換](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>カスタム音声テキスト変換の基本的な例

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>カスタム音声テキスト変換のログ記録の例

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="text-to-speech"></a>[テキスト読み上げ](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>テキスト読み上げの基本的な例

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>テキスト読み上げのログ記録の例

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speech"></a>[カスタム テキスト読み上げ](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>カスタム テキスト読み上げの基本的な例

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>カスタム テキスト読み上げのログ記録の例

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="neural-text-to-speech"></a>[ニューラル テキスト読み上げ](#tab/ntts)

### <a name="basic-example-for-neural-text-to-speech"></a>ニューラル テキスト読み上げの基本的な例

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-neural-text-to-speech"></a>ニューラル テキスト読み上げのログ記録の例

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="speech-language-detection"></a>[音声言語検出](#tab/lid)

### <a name="basic-example-for-speech-language-detection"></a>音声言語検出の基本的な例

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-language-detection"></a>音声言語検出のログの例

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>次のステップ

- [コンテナーのインストール方法と実行方法](speech-container-howto.md)を確認する。
