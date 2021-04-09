---
title: 低速クエリ ログにアクセスする - Azure portal - Azure Database for MySQL
description: この記事では、Azure portal から Azure Database for MySQL の低速クエリ ログを構成し、それにアクセスする方法について説明します。
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 3/15/2021
ms.openlocfilehash: 91569780aa71861e07c7e96bec5eac879642760d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496220"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Azure portal で低速クエリ ログを構成してアクセスする

Azure portal から [Azure Database for MySQL の低速クエリ ログ](concepts-server-logs.md)の構成、一覧表示、ダウンロードを行うことができます。

## <a name="prerequisites"></a>前提条件
この記事の手順では、[Azure Database for MySQL サーバー](quickstart-create-mysql-server-database-using-azure-portal.md)が必要です。

## <a name="configure-logging"></a>ログの構成
MySQL の低速クエリ ログへのアクセスを構成します。 

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. Azure Database for MySQL サーバーを選択します。

3. サイドバーの **[監視]** セクションの下で、 **[サーバー ログ]** を選択します。 
   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="[サーバー ログ] オプションのスクリーンショット":::

4. サーバー パラメーターを表示するには、 **[ログを有効にし、ログ パラメーターを構成するには、ここをクリックしてください]** を選択します。

5. **slow_query_log** を **[オン]** にします。

6. **log_output** を使用して、ログの出力先を選択します。 ログをローカル ストレージと Azure Monitor 診断ログの両方に送信するには、 **[ファイル]** を選択します。

7. 低速クエリ ログ ファイルで収集されるクエリには、クエリ時間しきい値を表す "long_query_time" を設定することを検討してください。long_query_time の最小値と既定値は、それぞれ 0 と 10 です。

8. 管理ステートメントをログに記録する場合、log_slow_admin_statements など、その他のパラメーターを調整します。 既定では、管理ステートメントはログに記録されません。参照にインデックスを使用しないクエリも同様です。 

9. **[保存]** を選択します。 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="低速クエリ ログのパラメーターと保存のスクリーンショット。":::

**[サーバー パラメーター]** ページを閉じると、ログの一覧に戻ることができます。

## <a name="view-list-and-download-logs"></a>リストの表示とログのダウンロード
ログ記録が開始された後、使用可能な低速クエリ ログの一覧を表示したり、個々のログ ファイルをダウンロードしたりすることができます。

1. Azure portal を開きます。

2. Azure Database for MySQL サーバーを選択します。

3. サイドバーの **[監視]** セクションの下で、 **[サーバー ログ]** を選択します。 ページに、ログ ファイルの一覧が表示されます。

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="ログの一覧が強調して示されている [サーバーログ] ページのスクリーンショット":::

   > [!TIP]
   > ログの名前付け規則は、 **-< your server name>-yyyymmddhh.log** です。 ファイル名に使用されている日時は、ログが発行された日時です。 ログ ファイルのローテーションは、24 時間ごとか 7.5 GB ごとのどちらか早い方のタイミングで行われます。 

4. 必要に応じて、検索ボックスを使用して、日付と時刻に基づいて特定のログをすばやく絞り込みます。 検索はログの名前に対して行われます。

5. 個々のログ ファイルをダウンロードするには、表の行で各ログ ファイルの横にある下向き矢印アイコンを選択します。

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-download.png" alt-text="下矢印アイコンが強調して示されている [サーバーログ] ページのスクリーンショット":::

## <a name="set-up-diagnostic-logs"></a>診断ログの設定

1. サイドバーの **[監視]** セクションの下で、 **[診断設定]**  >  **[診断設定を追加する]** を選択します。

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png" alt-text="[診断設定] のオプションのスクリーンショット":::

2. 診断設定の名前を指定します。

3. どのデータ シンク (ストレージ アカウント、イベント ハブ、Log Analytics ワークスペース) に低速クエリ ログを送信するか指定します。

4. ログの種類として **[MySqlSlowLogs]** を選択します。
:::image type="content" source="./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png" alt-text="診断設定構成オプションのスクリーンショット":::

5. 低速クエリ ログをパイプするようにデータ シンクを設定した後、 **[保存]** を選択します。
:::image type="content" source="./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png" alt-text="[保存] が強調して示されている診断設定構成オプションのスクリーンショット":::

6. 構成したデータ シンクを調べて低速クエリ ログにアクセスします。 ログが表示されるまでに最大で 10 分かかる可能性があります。

## <a name="next-steps"></a>次のステップ
- [CLI での低速クエリ ログへのアクセス](howto-configure-server-logs-in-cli.md)に関する記事を参照して、プログラムで低速クエリ ログをダウンロードする方法について学習します。
- Azure Database for MySQL の[低速クエリ ログ](concepts-server-logs.md)の詳細について学習します。
- パラメーターの定義と MySQL のログ記録の詳細については、[ログ](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)に関する MySQL のドキュメントを参照してください。