---
title: Durable Functions のプレビュー機能 - Azure Functions
description: Durable Functions のプレビュー機能について説明します。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: article
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 8f2560141eac4e7d9fed267d347990e65bfe9c26
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933239"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2.0 プレビュー (Azure Functions)

*Durable Functions* は、サーバーレス環境でステートフル関数を記述できる、[Azure Functions](../functions-overview.md) と [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) の拡張機能です。 この拡張機能は状態、チェックポイント、再起動を管理します。 Durable Functions にまだ慣れていない場合は、[概要ドキュメント](durable-functions-overview.md)をご覧ください。

Durable Functions 1.x は Azure Functions の GA (一般提供) 機能ですが、現在パブリック プレビュー段階のサブ機能もいくつか含まれています。 この記事では、新しくリリースされたプレビュー機能について説明し、そのしくみと使い始める方法を詳しく見ていきます。

> [!NOTE]
> これらのプレビュー機能は Durable Functions 2.0 リリースの一部であり、現在それはいくつかの破壊的変更を含む**プレビュー品質リリース**です。 Azure Functions Durable 拡張機能パッケージ ビルドは、nuget.org 上に **2.0.0-betaX** という形式のバージョンで存在します。 これらのビルドは運用環境のワークロードを対象としておらず、今後のリリースで破壊的変更が追加される可能性があります。

## <a name="breaking-changes"></a>重大な変更

Durable Functions 2.0 では、破壊的変更がいくつか導入されています。 既存のアプリケーションは、コードを変更しないと Durable Functions 2.0 と互換性がないものと思われます。 このセクションでは、変更の一部を示します。

### <a name="hostjson-schema"></a>Host.json スキーマ

次のスニペットでは、host.json の新しいスキーマを示します。 注意すべき主な変更点は、新しいサブセクションです。

* ストレージ固有の構成用の `"storageProvider"` (および `"azureStorage"` サブセクション)
* 追跡とログ記録の構成用の `"tracking"`
* イベント グリッド通知構成用の `"notifications"` (および `"eventGrid"` サブセクション)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

Durable Functions 2.0 は安定化が続けられており、host.json の `durableTask` セクションはさらに変更されます。 これらの変更について詳しくは、[こちらの GitHub の問題](https://github.com/Azure/azure-functions-durable-extension/issues/641)をご覧ください。

### <a name="public-interface-changes"></a>パブリック インターフェイスの変更

Durable Functions によってサポートされるさまざまな "コンテキスト" オブジェクトには、単体テストでの使用が意図される抽象基本クラスが含まれました。 Durable Functions 2.0 の一部として、これらの抽象基本クラスがインターフェイスに置き換えられました。 具象型を直接使用する関数のコードには影響しません。

次の表では、主な変更を示します。

| 古い型 | 新しい型 |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |
| OrchestrationClientAttribute | DurableClientAttribute |

抽象基本クラスに仮想メソッドが含まれていた場合、これらの仮想メソッドは `DurableContextExtensions` で定義される拡張メソッドに置き換えられています。

## <a name="entity-functions"></a>エンティティ関数

Durable Functions v2.0.0-alpha から、新しい[エンティティ関数](durable-functions-entities.md)の概念が導入されました。

エンティティ関数では、"*持続エンティティ*" と呼ばれる小さい状態の読み取りと更新のための操作が定義されています。 オーケストレーター関数と同様、エンティティ関数は特殊なトリガー型である "*エンティティ トリガー*" を含む関数です。 オーケストレーター関数とは異なり、エンティティ関数には特定のコードの制約はありません。 また、エンティティ関数では、制御フローを介して状態を表す暗黙的ではなく、明示的に状態が管理されます。

最初のユーザーからのフィードバックに基づいて、Durable Functions v2.0.0-beta1 のエンティティに対するクラスベースのプログラミング モデルのサポートが後から追加されています。

詳細については、[エンティティ関数](durable-functions-entities.md)に関する記事を参照してください。

## <a name="durable-http"></a>持続的 HTTP

Durable Functions v2.0.0-beta2 から新しい[持続的 HTTP](durable-functions-http-features.md) 機能が導入され、次のことが可能になりました。

* オーケストレーション関数から HTTP API シリーズを直接呼び出す (ドキュメントに記載されているいくつかの制限事項があります)
* 自動クライアント側 HTTP 202 状態ポーリングを実装する
* [Azure マネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) の組み込みサポート

詳細については、[HTTP 機能](durable-functions-http-features.md#consuming-http-apis)に関する記事を参照してください。

## <a name="alternate-storage-providers"></a>代替ストレージ プロバイダー

現在、Durable Task Framework では、[Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage)、[Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus)、[インメモリ エミュレーター](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)、実験的な [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) プロバイダーなど、複数のストレージ プロバイダーがサポートされています。 ただし、これまで、Azure Functions の Durable Task 拡張機能では Azure Storage プロバイダーのみがサポートされていました。 Durable Functions 2.0 以降では、Redis プロバイダーから始めて、代替ストレージ プロバイダーのサポートが追加されています。

> [!NOTE]
> Durable Functions 2.0 では、.NET Standard 2.0 と互換性のあるプロバイダーのみがサポートされます。

### <a name="emulator"></a>エミュレーター

[DurableTask.Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) プロバイダーは、ローカル テスト シナリオに適している、ローカル メモリの非持続ストレージ プロバイダーです。 次の最小限の **host.json** スキーマを使用して構成できます。

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { "enabled": true }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (実験段階)

[DurableTask.Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) プロバイダーでは、すべてのオーケストレーション状態が構成されている Redis クラスターに保持されます。

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

`connectionStringName` では、アプリ設定または環境変数の名前を参照する必要があります。 そのアプリ設定または環境変数には、Redis 接続文字列の値が "*サーバー:ポート*" の形式で含まれる必要があります。 たとえば、ローカル Redis クラスターに接続する場合は `localhost:6379` です。

> [!NOTE]
> 現在、Redis プロバイダーは実験段階であり、1 つのノードで実行されている関数アプリのみをサポートします。 Redis プロバイダーが一般公開されることは保証されず、将来のリリースでは削除される可能性があります。
