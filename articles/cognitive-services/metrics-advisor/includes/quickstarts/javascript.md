---
title: Metrics Advisor JavaScript クイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 668255486b1c53c062907aba9a679cf7a84bc3ca
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948303"
---
[リファレンスのドキュメント](/java/api/overview/azure/ai-metricsadvisor-readme) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/metricsadvisor/ai-metrics-advisor/README.md) | [パッケージ (npm)](https://www.npmjs.com/package/@azure/ai-metrics-advisor) | [サンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* 最新バージョンの [Node.js](https://nodejs.org/)
* Azure サブスクリプションを作成したら、Azure portal で <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Metrics Advisor リソースを作成"  target="_blank">Metrics Advisor リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>して、Metrics Advisor インスタンスをデプロイします。  
* 時系列データを含む独自の SQL データベース。
  
> [!TIP]
> * JavaScript Metrics Advisor のサンプルは、[GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples) にあります。
> * Metrics Advisor リソースによってサービス インスタンスがデプロイされ、使用できるようになるまでには、10 分から 30 分ほどかかる可能性があります。 デプロイに成功したら、 **[リソースに移動]** をクリックします。 デプロイ後、Web ポータルと REST API の両方で Metrics Advisor インスタンスの使用を開始できます。 
> * REST API の URL は Azure portal で確認できます (自分のリソースの **[概要]** セクション)。 次のようになります。
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 

```console
mkdir myapp && cd myapp
```

`npm init` コマンドを実行し、`package.json` ファイルを使用して node アプリケーションを作成します。 

```console
npm init
```

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

`@azure/ai-metrics-advisor` NPM パッケージをインストールします。

```console
npm install @azure/ai-metrics-advisor@1.0.0-beta.2
```

アプリの `package.json` ファイルが依存関係によって更新されます。

`index.js` という名前のファイルを作成して次のライブラリをインポートします。

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples/javascript) にあり、このクイックスタートのコード例が含まれています。

自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。 

> [!IMPORTANT]
> Azure Portal にアクセスします。 「**前提条件**」セクションで作成した Metrics Advisor リソースが正常にデプロイされたら、 **[次の手順]** の下にある **[リソースに移動]** ボタンをクリックします。 サブスクリプションのキーとエンドポイントは、リソースの **[key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 <br><br>API キーを取得するには、[https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) にアクセスする必要があります。 リソースに適切な **ディレクトリ**、**サブスクリプション**、**ワークスペース** を選択し、 **[Get started]\(作業の開始\)** を選択します。 その後、[https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) から API キーを取得できるようになります。   
>
> 終わったらコードからキーを削除し、公開しないよう注意してください。 運用環境では、資格情報を安全に格納して利用するための方法を用いることを検討してください。 詳細については、Cognitive Services の[セキュリティ](../../../cognitive-services-security.md)に関するページを参照してください。

```javascript
subscriptionKey = "<paste-your-metrics-advisor-key-here>";
apiKey ="<paste-your-metrics-advisor-api-key-here>";
endpoint = "<paste-your-metrics-advisor-endpoint-here>";
```

## <a name="object-model"></a>オブジェクト モデル

次のクラスとインターフェイスにより、Metrics Advisor JavaScript SDK の主要な機能の一部が処理されます。

|名前|説明|
|---|---|
| MetricsAdvisorClient | **使用目的**: <br> - インシデントの一覧表示 <br> - インシデントの根本原因の一覧表示 <br> - 元の時系列データと、サービスによってエンリッチされた時系列データの取得 <br> - アラートの一覧表示 <br> - モデルを調整するためのフィードバックの追加 |
| MetricsAdvisorAdministrationClient | **次のことを実行できます**: <br> - データ フィードを管理する <br> - 異常アラート構成を作成、設定、取得、一覧表示、削除する <br> - フックを管理する  |
| DataFeed | **Metrics Advisor によってデータソースから取り込まれるもの。`DataFeed` には、次の行が含まれます:** <br> - タイムスタンプ <br> - 0 個以上のディメンション <br> - 1 つ以上のメジャー  |
| DataFeedMetric | `DataFeedMetric` は、特定のビジネス プロセスの状態を監視および評価するために使用される定量化可能なメジャーです。 ディメンションに分割された複数の時系列値を組み合わせることができます。 たとえば、web health のメトリックには、user count と en-us market のディメンションが含まれている場合があります。 |

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、JavaScript 用 Metrics Advisor クライアント ライブラリを使用して次のことを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [データ フィードを追加する](#add-a-data-feed)
* [インジェストの状態を確認する](#check-ingestion-status)
* [異常検出を構成する](#configure-anomaly-detection)
* [フックを作成する](#create-a-hook)
* [アラート構成を作成する](#create-an-alert-configuration)
* [アラートに対してクエリを実行する](#query-the-alert)

## <a name="authenticate-the-client"></a>クライアントを認証する

2 つのキーとエンドポイント アドレスを取得したら、MetricsAdvisorKeyCredential クラスを使用して、次のようにクライアントを認証できます。

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorClient,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
const client = new MetricsAdvisorClient(endpoint, credential);
const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
```

## <a name="add-a-data-feed"></a>データ フィードを追加する

Metrics Advisor では、さまざまな種類のデータ ソースの接続がサポートされています。 ここでは、SQL Server からデータを取り込むサンプルを示します。

`sql_server_connection_string` を、実際の SQL Server 接続文字列に置き換え、`query` を、1 つのタイムスタンプでデータを返すクエリに置き換えます。 また、カスタム データに基づいて `metric` と `dimension` の値を調整する必要もあります。

> [!IMPORTANT]
> クエリからは、各タイムスタンプで、ディメンションの各組み合わせに対して多くても 1 つのレコードが返される必要があります。 また、クエリによって返されるすべてのレコードで、タイムスタンプが同じになっている必要があります。 Metrics Advisor によってタイムスタンプごとにこのクエリが実行され、データが取り込まれます。 詳細と例については、[クエリの FAQ セクション](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data)を参照してください。 

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  subscriptionKey = "<paste-your-metrics-advisor-key-here>";
  apiKey ="<paste-your-metrics-advisor-api-key-here>";
  endpoint = "<paste-your-metrics-advisor-endpoint-here>";
  const sqlServerConnectionString ="<sql_server_connection_string>";
  const sqlServerQuery ="<query>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const created = await createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery);
  console.log(`Data feed created: ${created.id}`);
}

async function createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery) {
  console.log("Creating Datafeed...");
  const dataFeed = {
    name: "test_datafeed_" + new Date().getTime().toString(),
    source: {
      dataSourceType: "SqlServer",
      dataSourceParameter: {
        connectionString: sqlServerConnectionString,
        query: sqlServerQuery
      }
    },
    granularity: {
      granularityType: "Daily"
    },
    schema: {
      metrics: [
        {
          name: "revenue",
          displayName: "revenue",
          description: "Metric1 description"
        },
        {
          name: "cost",
          displayName: "cost",
          description: "Metric2 description"
        }
      ],
      dimensions: [
        { name: "city", displayName: "city display" },
        { name: "category", displayName: "category display" }
      ],
      timestampColumn: null
    },
    ingestionSettings: {
      ingestionStartTime: new Date(Date.UTC(2020, 5, 1)),
      ingestionStartOffsetInSeconds: 0,
      dataSourceRequestConcurrency: -1,
      ingestionRetryDelayInSeconds: -1,
      stopRetryAfterInSeconds: -1
    },
    rollupSettings: {
      rollupType: "AutoRollup",
      rollupMethod: "Sum",
      rollupIdentificationValue: "__CUSTOM_SUM__"
    },
    missingDataPointFillSettings: {
      fillType: "SmartFilling"
    },
    accessMode: "Private",
    adminEmails: ["xyz@example.com"]
  };
  const result = await adminClient.createDataFeed(dataFeed);

  return result;
}
```

## <a name="check-ingestion-status"></a>インジェストの状態を確認する

データ インジェストを開始したら、インジェストの状態を確認できます。

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const dataFeedId = "<data feed id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  await checkIngestionStatus(
    adminClient,
    dataFeedId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );
}

async function checkIngestionStatus(adminClient, datafeedId, startTime, endTime) {
  // This shows how to use for-await-of syntax to list status
  console.log("Checking ingestion status...");
  const iterator = adminClient.listDataFeedIngestionStatus(datafeedId, startTime, endTime);
  for await (const status of iterator) {
    console.log(`  [${status.timestamp}] ${status.status} - ${status.message}`);
  }
}
```

## <a name="configure-anomaly-detection"></a>異常検出を構成する

時系列内のポイントが異常かどうかを判断するために、異常検出構成が必要です。 各メトリックには既定の検出構成が自動的に適用されますが、カスタマイズされた異常検出構成を作成することによって、データで使用される検出モードを調整することができます。

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const metricId =  "<metric id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const detectionConfig = await configureAnomalyDetectionConfiguration(adminClient, metricId);
  console.log(`Detection configuration created: ${detectionConfig.id}`);
}

async function configureAnomalyDetectionConfiguration(adminClient, metricId) {
  console.log(`Creating an anomaly detection configuration on metric '${metricId}'...`);
  const detectionConfig = {
    name: "test_detection_configuration" + new Date().getTime().toString(),
    metricId,
    wholeSeriesDetectionCondition: {
      smartDetectionCondition: {
        sensitivity: 100,
        anomalyDetectorDirection: "Both",
        suppressCondition: {
          minNumber: 1,
          minRatio: 1
        }
      }
    },
    description: "Detection configuration description"
  };
  return await adminClient.createDetectionConfig(detectionConfig);
}
```

### <a name="create-a-hook"></a>フックを作成する

リアルタイム アラートをサブスクライブするために、フックを使用します。 この例では、アラートを POST する Metrics Advisor サービスの Webhook を作成します。

```javascript

const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const hook = await createWebhookHook(adminClient);
  console.log(`Webhook hook created: ${hook.id}`);
}

async function createWebhookHook(adminClient) {
  console.log("Creating a webhook hook");
  const hook = {
    hookType: "Webhook",
    name: "web hook " + new Date().getTime().toFixed(),
    description: "description",
    hookParameter: {
      endpoint: "https://example.com/handleAlerts", // you must enter a valid webhook url to post the alert payload
      username: "username",
      password: "password"
      // certificateKey: "certificate key",
      // certificatePassword: "certificate password"
    }
  };

  return await adminClient.createHook(hook);
}
```

##  <a name="create-an-alert-configuration"></a>アラート構成を作成する

このサンプルでは、アラートをトリガーする必要がある条件と、アラートの送信先として使用するフックを構成する方法について説明します。

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const detectionConfigId = "<detection id>";
  const hookId = "<hook id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const alertConfig = await configureAlertConfiguration(adminClient, detectionConfigId, [hookId]);
  console.log(`Alert configuration created: ${alertConfig.id}`);
}

async function configureAlertConfiguration(adminClient, detectionConfigId, hookIds) {
  console.log("Creating a new alerting configuration...");
  const anomalyAlertConfig = {
    name: "test_alert_config_" + new Date().getTime().toString(),
    crossMetricsOperator: "AND",
    metricAlertConfigurations: [
      {
        detectionConfigurationId: detectionConfigId,
        alertScope: {
          scopeType: "All"
        },
        alertConditions: {
          severityCondition: { minAlertSeverity: "Medium", maxAlertSeverity: "High" }
        },
        snoozeCondition: {
          autoSnooze: 0,
          snoozeScope: "Metric",
          onlyForSuccessive: true
        }
      }
    ],
    hookIds,
    description: "Alerting config description"
  };
  return await adminClient.createAlertConfig(anomalyAlertConfig);
}
```

## <a name="query-the-alert"></a>アラートに対してクエリを実行する

```javascript
const { MetricsAdvisorKeyCredential, MetricsAdvisorClient } = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const alertConfigId = "<alert config id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const client = new MetricsAdvisorClient(endpoint, credential);

  const alertIds = await queryAlerts(
    client,
    alertConfigId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );

  if (alertIds.length > 1) {
    // query anomalies using an alert id.
    await queryAnomaliesByAlert(client, alertConfigId, alertIds[0]);
  } else {
    console.log("No alerts during the time period");
  }
}

async function queryAlerts(client, alertConfigId, startTime, endTime) {
  let alerts = [];
  const iterator = client.listAlerts(alertConfigId, startTime, endTime, "AnomalyTime");
  for await (const alert of iterator) {
    alerts.push(alert);
  }

  return alerts;
}

async function queryAnomaliesByAlert(client, alert) {
  console.log(
    `Listing anomalies for alert configuration '${alert.alertConfigId}' and alert '${alert.id}'`
  );
  const iterator = client.listAnomalies(alert);
  for await (const anomaly of iterator) {
    console.log(
      `  Anomaly ${anomaly.severity} ${anomaly.status} ${anomaly.seriesKey.dimension} ${anomaly.timestamp}`
    );
  }
}
```

### <a name="run-the-application"></a>アプリケーションの実行

クイック スタート ファイルで `node` コマンドを使用して、アプリケーションを実行します。

```console
node index.js
```
