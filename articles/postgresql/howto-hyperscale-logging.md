---
title: ログ - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL - Hyperscale (Citus) のデータベース ログにアクセスする方法
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: ca3cc2873fbc6db72b10c80daecddf1471e30ff4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577061"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) のログ

PostgreSQL のログは、Hyperscale (Citus) サーバー グループのすべてのノードで使用できます。 ストレージ サーバーまたは分析サービスにログを送信できます。 ログは、構成エラーと十分に最適化されていないパフォーマンスの特定、トラブルシューティング、修復に使用できます。

## <a name="accessing-logs"></a>ログへのアクセス

Hyperscale (Citus) コーディネーターまたはワーカー ノードの PostgreSQL ログにアクセスするには、Azure portal でノードを開きます。

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="ノードのリスト":::

選択したノードで **[診断設定]** を開き、 **[+ 診断設定の追加]** をクリックします。

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="[診断設定の追加] ボタン":::

新しい診断設定の名前を選択し、 **[PostgreSQLLogs]** チェック ボックスをオンにします。  ログの受信先を選択します。

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="PostgreSQL のログを選択する":::

## <a name="next-steps"></a>次のステップ

- [Log Analytics クエリの使用方法](../azure-monitor/logs/log-analytics-tutorial.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md) について