---
title: Cosmos DB 用 Azure Functions トリガーの使用時のログの構成と読み取り
description: Cosmos DB 用 Azure Functions トリガーの使用時に Azure Functions のログ記録パイプラインにログを公開する方法について説明します
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 17b2d4854e3222e5bdb607c6c80b0ec5ed6177d3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093597"
---
# <a name="how-to-configure-and-read-the-logs-when-using-azure-functions-trigger-for-cosmos-db"></a>Cosmos DB 用 Azure Functions トリガーの使用時のログの構成および読み取り方法

この記事では、Cosmos DB 用 Azure Functions トリガーのログを構成済みの[監視ソリューション](../azure-functions/functions-monitoring.md)に送信するように Azure Functions 環境を構成する方法について説明します。

## <a name="included-logs"></a>含まれるログ

Cosmos DB 用 Azure Functions トリガーでは [Change Feed プロセッサ ライブラリ](./change-feed-processor.md)が内部的に使用されます。このライブラリでは、[トラブルシューティングの目的](./troubleshoot-changefeed-functions.md)で、内部操作を監視するために使用できる正常性ログのセットが生成されます。

正常性ログには、負荷分散シナリオまたは初期化の間に操作を試みたときに Cosmos DB 用 Azure Functions トリガーがどのように動作したかが記述されます。

## <a name="enabling-logging"></a>ログの記録を有効にする

Cosmos DB 用 Azure Functions トリガーの使用時にログ記録を有効にするには、対象の Azure Functions プロジェクトまたは Azure Function アプリ内で `host.json` ファイルを探し、[必要なログ記録のレベルを構成](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson)します。 次のサンプルで示すように、`Host.Triggers.CosmosDB` のトレースを有効にする必要があります。

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

更新した構成で Azure Function をデプロイした後は、トレースの一部として Cosmos DB 用 Azure Functions トリガーのログが表示されるようになります。 構成済みログ プロバイダーでのログは、"*カテゴリ*" `Host.Triggers.CosmosDB` で見ることができます。

## <a name="query-the-logs"></a>ログのクエリを実行する

[Azure Application Insights の Analytics](../azure-monitor/app/analytics.md) で Cosmos DB 用 Azure Functions トリガーによって生成されたログを照会するには、次のクエリを実行します。

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>次の手順

* Azure Functions アプリケーションで[監視を有効にします](../azure-functions/functions-monitoring.md)。
* Cosmos DB 用 Azure Functions トリガーの使用時に[一般的な問題の診断とトラブルシューティングを行う](./troubleshoot-changefeed-functions.md)方法を学習します。