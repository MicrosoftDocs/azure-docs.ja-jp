---
title: コンテナーの構成 - Face
titleSuffix: Azure Cognitive Services
description: Face コンテナーのランタイム環境は、`docker run` コマンドの引数を使用して構成されます。 必須とオプションの両方の設定があります。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2f608843e27b79d02697df8e2a7f2aba6695e10a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "80878427"
---
# <a name="configure-face-docker-containers"></a>Face Docker コンテナーの構成

**Face** コンテナーのランタイム環境は、`docker run` コマンドの引数を使用して構成されます。 このコンテナーには、いくつかの必須の設定と省略可能な設定があります。 いくつかのコマンドの[例](#example-docker-run-commands)をご覧ください。 このコンテナーに固有の設定は、課金設定です。 

## <a name="configuration-settings"></a>構成設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting)、[`Eula`](#eula-setting) の各設定は一緒に使用されるため、それらの 3 つすべてに有効な値を指定する必要があります。そうしないと、お客様のコンテナーは起動しません。 これらの構成設定を使用してコンテナーをインスタンス化する方法の詳細については、「[課金](face-how-to-install-containers.md#billing)」を参照してください。

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

エンドポイント URI には、忘れずに _Face_ ルーティングを追加してください。その例を次に示します。 

|必須| 名前 | データ型 | 説明 |
|--|------|-----------|-------------|
|はい| `Billing` | String | 課金エンドポイント URI。 課金 URI の取得の詳細については、「[必須パラメーターの収集](face-how-to-install-containers.md#gathering-required-parameters)」を参照してください。 リージョンのエンドポイントの詳細および全一覧については、「[Cognitive Services のカスタム サブドメイン名](../cognitive-services-custom-subdomains.md)」を参照してください。 |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>CloudAI カスタム構成設定

`CloudAI` セクションの構成設定では、ご利用のコンテナーに固有のオプションが提供されます。 `CloudAI` セクションの Face コンテナーでは、次の設定とオブジェクトがサポートされます

| 名前 | データ型 | 説明 |
|------|-----------|-------------|
| `Storage` | Object | Face コンテナーで使用されるストレージ シナリオ。 `Storage` オブジェクトのストレージ シナリオと関連する設定の詳細については、「[ストレージ シナリオの設定](#storage-scenario-settings)」を参照してください |

### <a name="storage-scenario-settings"></a>ストレージ シナリオの設定

Face コンテナーには、格納される内容に応じて、BLOB、キャッシュ、メタデータ、およびキュー データが格納されます。 たとえば、大きな人物グループのトレーニング インデックスと結果は、BLOB データとして格納されます。 Face コンテナーでは、これらの種類のデータを操作および格納する場合に、次の 2 つの異なるストレージ シナリオが提供されます。

* メモリ  
  メモリには 4 つのすべての種類のデータが格納されます。 これらは分散されることはなく、永続的なものでもありません。 Face コンテナーが停止または削除された場合、そのコンテナーのストレージ内のデータはすべて破棄されます。  
  これは、Face コンテナーの既定のストレージ シナリオです。
* Azure  
  Face コンテナーでは Azure Storage と Azure Cosmos DB を使用して、これら 4 つの種類のデータを永続的ストレージに分散します。 BLOB およびキュー データは、Azure Storage で処理されます。 メタデータとキャッシュ データは、Azure Cosmos DB によって処理されます。 Face コンテナーが停止または削除された場合、そのコンテナーのストレージ内のデータはすべて Azure Storage と Azure Cosmos DB に格納されたままとなります。  
  Azure ストレージ シナリオで使用されるリソースには、次の追加の要件があります
  * Azure Storage リソースでは、StorageV2 という種類のアカウントを使用する必要があります
  * Azure Cosmos DB リソースでは、Azure Cosmos DB の MongoDB 用 API を使用する必要があります

ストレージ シナリオと関連する構成設定は、`CloudAI` 構成セクションの下の、`Storage` オブジェクトで管理されます。 `Storage` オブジェクトでは、次の構成設定を使用できます。

| 名前 | データ型 | 説明 |
|------|-----------|-------------|
| `StorageScenario` | String | コンテナーで使用されるストレージ シナリオ。 次の値を使用できます<br/>`Memory` - 既定値。 コンテナーでは、単一ノードの一時的な使用のために、非永続的、非分散およびインメモリ ストレージが使用されます。 コンテナーが停止または削除された場合、そのコンテナーのストレージは破棄されます。<br/>`Azure` - コンテナーでは、ストレージのために Azure リソースが使用されます。 コンテナーが停止または削除された場合、そのコンテナーのストレージは保持されます。|
| `ConnectionStringOfAzureStorage` | String | コンテナーで使用される、Azure Storage リソースの接続文字列。<br/>`Azure` が `StorageScenario` 構成設定に対して指定されている場合にのみ、この設定が適用されます。 |
| `ConnectionStringOfCosmosMongo` | String | コンテナーで使用される、Azure Cosmos DB リソースの MongoDB 接続文字列。<br/>`Azure` が `StorageScenario` 構成設定に対して指定されている場合にのみ、この設定が適用されます。 |

たとえば、次のコマンドでは、Azure ストレージ シナリオを指定し、Face コンテナーのデータを格納するために使用される Azure Storage および Cosmos DB リソースのサンプル接続文字列を提供します。

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

ストレージ シナリオは、入力マウントと出力マウントとは別に処理されます。 1 つのコンテナーに対して、これらの機能の組み合わせを指定できます。 たとえば、次のコマンドでは Docker バインド マウントを、出力マウントとしてホスト コンピューター上の `D:\Output` フォルダーに定義し、さらに Face コンテナー イメージからコンテナーをインスタンス化し、ログ ファイルを JSON 形式で出力マウントに保存しています。 また、このコマンドでは、Azure ストレージ シナリオを指定し、Face コンテナーのデータを格納するために使用される Azure Storage および Cosmos DB リソースのサンプル接続文字列を提供します。

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

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

Face コンテナーでは、トレーニングやサービスのデータを格納するために入力マウントまたは出力マウントが使用されることはありません。 

ホストのマウント場所の厳密な構文は、ホスト オペレーティング システムによって異なります。 また、Docker サービス アカウントによって使用されるアクセス許可とホストのマウント場所のアクセス許可とが競合するために、[ホスト コンピューター](face-how-to-install-containers.md#the-host-computer)のマウント場所にアクセスできないこともあります。 

|省略可能| 名前 | データ型 | 説明 |
|-------|------|-----------|-------------|
|禁止| `Input` | String | Face コンテナーでは、これは使用されません。|
|オプション| `Output` | String | 出力マウントのターゲット。 既定値は `/output` です。 これはログの保存先です。 これには、コンテナーのログが含まれます。 <br><br>例:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>docker run コマンドの例 

以下の例では、`docker run` コマンドの記述方法と使用方法を示す構成設定が使用されています。  コンテナーは一度実行すると、お客様が[停止](face-how-to-install-containers.md#stop-the-container)するまで動作し続けます。

* **行連結文字**: 以降のセクションの Docker コマンドには、行連結文字としてバック スラッシュ (`\`) が使用されています。 お客様のホスト オペレーティング システムの要件に応じて、置換または削除してください。 
* **引数の順序**: Docker コンテナーについて高度な知識がある場合を除き、引数の順序は変更しないでください。

{ _<引数名>_ } はお客様独自の値に置き換えてください。

| プレースホルダー | 値 | 形式または例 |
|-------------|-------|---|
| **{API_KEY}** | Azure `Face` の [キー] ページの `Face` リソースのエンドポイント キー。 | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | 課金エンドポイントの値は、Azure `Face` の [概要] ページで確認できます。| 明示的な例が必要であれば、[必須パラメーターの収集](face-how-to-install-containers.md#gathering-required-parameters)に関するページを参照してください。 |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](face-how-to-install-containers.md#billing)」を参照してください。
> ApiKey の値は、Azure `Cognitive Services` リソース キー ページにある **キー** です。 

## <a name="face-container-docker-examples"></a>Face コンテナーの Docker の例

次の Docker の例は、Face コンテナーに関するものです。 

### <a name="basic-example"></a>基本的な例 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>ログの例 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>次のステップ

* [コンテナーのインストール方法と実行方法](face-how-to-install-containers.md)を確認する。
