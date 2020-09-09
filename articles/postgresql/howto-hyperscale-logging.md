---
title: ログ - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL - Hyperscale (Citus) のデータベース ログにアクセスする方法
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: 4ca64a7793d229e7910fb122fb33996b1ac0c4fe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87094970"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) のログ

PostgreSQL のログは、Hyperscale (Citus) サーバー グループのすべてのノードで使用できます。 ストレージ サーバーまたは分析サービスにログを送信できます。 ログは、構成エラーと十分に最適化されていないパフォーマンスの特定、トラブルシューティング、修復に使用できます。

## <a name="accessing-logs"></a>ログへのアクセス

Hyperscale (Citus) コーディネーターまたはワーカー ノードの PostgreSQL ログにアクセスするには、Azure portal でノードを開きます。

![ノードのリスト](media/howto-hyperscale-logging/choose-node.png)

選択したノードで **[診断設定]** を開き、 **[+ 診断設定の追加]** をクリックします。

![[診断設定の追加] ボタン](media/howto-hyperscale-logging/diagnostic-settings.png)

新しい診断設定の名前を選択し、 **[PostgreSQLLogs]** チェック ボックスをオンにします。  ログの受信先を選択します。

![PostgreSQL のログを選択する](media/howto-hyperscale-logging/diagnostic-create-setting.png)

## <a name="next-steps"></a>次のステップ

- [Log Analytics クエリの使用方法](/azure/azure-monitor/log-query/get-started-portal)
- [Azure Event Hubs](/azure/event-hubs/event-hubs-about) について
