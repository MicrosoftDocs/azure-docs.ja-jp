---
title: 低速クエリ ログの構成 - Azure portal - Azure Database for MySQL - フレキシブル サーバー
description: この記事では、Azure portal から Azure Database for MySQL フレキシブル サーバーの低速クエリ ログを構成し、それにアクセスする方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: a61c8e3451d661dae2e5ad56a0d4a947252ec873
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94540065"
---
# <a name="configure-and-access-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Azure portal を使用した Azure Database for MySQL フレキシブル サーバーの低速クエリ ログの構成とアクセス

> [!IMPORTANT]
> Azure Database for MySQL - フレキシブル サーバーは現在、パブリック プレビュー段階にあります。

Azure portal から Azure Database for MySQL フレキシブル サーバーの[低速クエリ ログ](concepts-slow-query-logs.md)の構成、一覧表示、ダウンロードを行うことができます。

## <a name="prerequisites"></a>前提条件
この記事の手順では、[フレキシブル サーバー](quickstart-create-server-portal.md)が必要です。

## <a name="configure-logging"></a>ログの構成
MySQL の低速クエリ ログへのアクセスを構成します。 

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. フレキシブル サーバーを選択します。

1. サイドバーの **[設定]** セクションで、 **[サーバー パラメーター]** を選択します。
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/server-parameters.png" alt-text="[サーバー パラメーター] ページ。":::

1. **slow_query_log** パラメーターを **[ON]** に更新します。
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/slow-query-log-enable.png" alt-text="低速クエリ ログを有効にします。":::

1. 必要なその他のすべてのパラメーターを変更します (例: `long_query_time`, `log_slow_admin_statements`). その他のパラメーターについては、[低速クエリ ログ](./concepts-slow-query-logs.md#configure-slow-query-logging)のドキュメントを参照してください。  
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/long-query-time.png" alt-text="低速クエリ ログに関連するパラメーターを更新します。":::

1. **[保存]** を選択します。 
   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-parameters.png" alt-text="低速クエリ ログ パラメーターを保存します。":::

**[サーバー パラメーター]** ページを閉じると、ログの一覧に戻ることができます。

## <a name="set-up-diagnostics"></a>診断を設定する

低速クエリ ログは Azure Monitor 診断設定と統合されており、ログを Azure Monitor ログ、Event Hubs、または Azure Storage にパイプ処理することができます。

1. サイドバーの **[監視]** セクションの下で、 **[診断設定]**  >  **[診断設定を追加する]** を選択します。

   :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/add-diagnostic-setting.png" alt-text="[診断設定] のオプションのスクリーンショット":::

1. 診断設定の名前を指定します。

1. どの送信先 (ストレージ アカウント、イベント ハブ、Log Analytics ワークスペース) に低速クエリ ログを送信するか指定します。

1. ログの種類として **[MySqlSlowLogs]** を選択します。
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/configure-diagnostic-setting.png" alt-text="診断設定構成オプションのスクリーンショット":::

1. 低速クエリ ログをパイプするようにデータ シンクを設定した後、 **[保存]** を選択します。
    :::image type="content" source="./media/how-to-configure-slow-query-logs-portal/save-diagnostic-setting.png" alt-text="[保存] が強調して示されている診断設定構成オプションのスクリーンショット":::

1. 構成したデータ シンクを調べて低速クエリ ログにアクセスします。 ログが表示されるまでに最大で 10 分かかる可能性があります。

ログを Azure Monitor ログ (Log Analytics) にパイプ処理した場合は、分析に使用できるいくつかの[サンプル クエリ](concepts-slow-query-logs.md#analyze-logs-in-azure-monitor-logs)を参照してください。 

## <a name="next-steps"></a>次のステップ
<!-- - See [Access slow query Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download slow query logs programmatically.-->
- [低速クエリ ログ](concepts-slow-query-logs.md)の詳細を確認する
- パラメーターの定義と MySQL のログ記録の詳細については、[ログ](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)に関する MySQL のドキュメントを参照してください。
- [監査ログ](concepts-audit-logs.md)について確認する
