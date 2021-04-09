---
title: 監査ログの構成 - Azure portal - Azure Database for MySQL - フレキシブル サーバー
description: この記事では、Azure portal から Azure Database for MySQL フレキシブル サーバーの監査ログを構成し、それにアクセスする方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: ebb980aa257fc09c3d6a407febbf60f2d1a26a4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94536474"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Azure portal を使用した Azure Database for MySQL - フレキシブル サーバーの監査ログの構成とアクセス

> [!IMPORTANT]
> Azure Database for MySQL - フレキシブル サーバーは現在、パブリック プレビュー段階にあります。

Azure Database for MySQL フレキシブル サーバーの[監査ログ](concepts-audit-logs.md)と診断設定を Azure portal から構成することができます。

## <a name="prerequisites"></a>前提条件
この記事の手順では、[フレキシブル サーバー](quickstart-create-server-portal.md)が必要です。

## <a name="configure-audit-logging"></a>監査ログを構成する

>[!IMPORTANT]
> サーバーのパフォーマンスに大きな影響を与えないように、監査のために必要なイベントの種類とユーザーのみをログに記録することをお勧めします。

監査ログを有効化および構成します。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. フレキシブル サーバーを選択します。

1. サイドバーの **[設定]** セクションで、 **[サーバー パラメーター]** を選択します。
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/server-parameters.png" alt-text="サーバー パラメーター":::

1. **audit_log_enabled** パラメーターを ON に更新します。
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-enabled.png" alt-text="監査ログの有効化":::

1. **audit_log_events** パラメーターを更新して、ログに記録する [イベントの種類](concepts-audit-logs.md#configure-audit-logging)を選択します。
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-events.png" alt-text="監査ログのイベント":::

1. **audit_log_exclude_users** および **audit_log_include_users** パラメーターを更新して、ログに含めたりログから除外したりする MySQL ユーザーを追加します。 ユーザーは MySQL ユーザー名で指定します。
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="監査ログの除外ユーザー":::

1. パラメーターを変更すると、 **[保存]** をクリックできます。 または変更を **[破棄]** することができます。
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/save-parameters.png" alt-text="および":::

## <a name="set-up-diagnostics"></a>診断を設定する

監査ログは Azure Monitor 診断設定と統合されており、ログを Azure Monitor ログ、Event Hubs、または Azure Storage にパイプすることができます。

1. サイドバーの **[監視]** セクションの下で、 **[診断設定]** を選択します。

1. [+ 診断設定を追加する] をクリックします :::image type="content" source="./media/how-to-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="診断設定を追加する":::

1. 診断設定の名前を指定します。

1. どの送信先 (ストレージ アカウント、イベント ハブ、Log Analytics ワークスペース) に監査ログを送信するか指定します。

1. ログの種類として **MySqlAuditLogs** を選択します。
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="診断設定の構成":::

1. 監査ログをパイプするようにデータ シンクを設定したら、 **[保存]** をクリックすることができます。
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="診断設定の保存":::

1. 構成したデータ シンクを調べて監査ログにアクセスします。 ログが表示されるまでに最大で 10 分かかる可能性があります。

監査ログを Azure Monitor ログ (Log Analytics) にパイプした場合は、分析に使用できるいくつかの[サンプル クエリ](concepts-audit-logs.md#analyze-logs-in-azure-monitor-logs)を参照してください。  

## <a name="next-steps"></a>次のステップ

- [監査ログ](concepts-audit-logs.md)の詳細を確認する
- [低速クエリ ログ](concepts-slow-query-logs.md)を確認する
<!-- - Learn how to configure audit logs in the [Azure CLI](howto-configure-audit-logs-cli.md)-->