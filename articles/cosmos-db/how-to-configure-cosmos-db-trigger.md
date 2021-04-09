---
title: Cosmos DB 用 Azure Functions トリガーの詳細構成
description: Cosmos DB 用 Azure Functions トリガーで使用されるログと接続ポリシーを構成する方法について学習します
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/19/2020
ms.author: maquaran
ms.openlocfilehash: 30328db465e0d9bf8c1ce67d92e48c688c51e043
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574610"
---
# <a name="how-to-configure-logging-and-connectivity-with-the-azure-functions-trigger-for-cosmos-db"></a>Cosmos DB 用 Azure Functions トリガーによるログと接続を構成する方法
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、Cosmos DB 用 Azure Functions トリガーを使用するときに設定できる詳細な構成オプションについて説明します。

## <a name="enabling-trigger-specific-logs"></a>トリガー固有のログを有効にする

Cosmos DB 用 Azure Functions トリガーでは [Change Feed プロセッサ ライブラリ](./change-feed-processor.md)が内部的に使用されます。このライブラリでは、[トラブルシューティングの目的](./troubleshoot-changefeed-functions.md)で、内部操作を監視するために使用できる正常性ログのセットが生成されます。

正常性ログには、負荷分散シナリオまたは初期化の間に操作を試みたときに Cosmos DB 用 Azure Functions トリガーがどのように動作したかが記述されます。

### <a name="enabling-logging"></a>ログの記録を有効にする

Cosmos DB 用 Azure Functions トリガーの使用時にログ記録を有効にするには、対象の Azure Functions プロジェクトまたは Azure Function アプリ内で `host.json` ファイルを探し、[必要なログ記録のレベルを構成](../azure-functions/functions-monitoring.md#log-levels-and-categories)します。 次のサンプルで示すように、`Host.Triggers.CosmosDB` のトレースを有効にします。

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

更新した構成で Azure Function をデプロイした後は、トレースの一部として Cosmos DB 用 Azure Functions トリガーのログが表示されるようになります。 構成済みログ プロバイダーでのログは、"*カテゴリ"* `Host.Triggers.CosmosDB` で見ることができます。

### <a name="query-the-logs"></a>ログのクエリを実行する

[Azure Application Insights の Analytics](../azure-monitor/logs/log-query-overview.md) で Cosmos DB 用 Azure Functions トリガーによって生成されたログを照会するには、次のクエリを実行します。

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="configuring-the-connection-policy"></a>接続ポリシーの構成

接続モードには、直接モードとゲートウェイ モードの 2 つがあります。 これらの接続モードの詳細については、[接続モード](sql-sdk-connection-modes.md)に関する記事を参照してください。 既定では、Cosmos DB 用 Azure Functions トリガーにおけるすべての接続の確立に、**ゲートウェイ** が使用されます。 ただしこれは、パフォーマンス重視のシナリオでは最適な選択肢ではない可能性があります。

### <a name="changing-the-connection-mode-and-protocol"></a>接続のモードとプロトコルの変更

クライアントの接続ポリシーを構成するために使用できる 2 つの主要な構成設定として、**接続モード** と **接続プロトコル** があります。 Cosmos DB 用 Azure Functions トリガーとすべての [Azure Cosmos DB バインディング](../azure-functions/functions-bindings-cosmosdb-v2-output.md)で使用される既定の接続モードおよびプロトコルは、変更することができます。 既定の設定を変更するには、Azure Functions プロジェクトまたは Azure Functions アプリ内で `host.json` ファイルを見つけて、次の[追加設定](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)を行います。

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

`connectionMode` には目的の接続モード (Direct または Gateway)、`protocol` には目的の接続プロトコル (Tcp または Https) が設定されている必要があります。 

自分の Azure Functions プロジェクトで Azure Functions V1 ランタイムを使用している場合、構成の名前はわずかに異なり、`cosmosDB` ではなく `documentDB` を使用する必要があります。

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> 従量課金プランで関数アプリをホストしている場合は、各インスタンスで保持できるソケット接続の量に制限があります。 直接または TCP モードで操作している場合は、設計上、より多くの接続が作成されるため、[従量課金プランの制限](../azure-functions/manage-connections.md#connection-limit)に達する可能性があります。この場合は、ゲートウェイ モードを使用するか、または代わりに [Premium プラン](../azure-functions/functions-premium-plan.md)または[専用 (App Service) プラン](../azure-functions/dedicated-plan.md)のどちらかで関数アプリをホストできます。

## <a name="next-steps"></a>次の手順

* [Azure Functions での接続の制限](../azure-functions/manage-connections.md#connection-limit)
* Azure Functions アプリケーションで[監視を有効にします](../azure-functions/functions-monitoring.md)。
* Cosmos DB 用 Azure Functions トリガーの使用時に[一般的な問題の診断とトラブルシューティングを行う](./troubleshoot-changefeed-functions.md)方法を学習します。