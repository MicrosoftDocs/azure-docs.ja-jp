---
title: Azure portal で Azure Database for MariaDB のサーバー ログを構成してアクセスする
description: この記事では、Azure portal から Azure Database for MariaDB のサーバー ログの構成方法とアクセス方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: c7e95521e503ad2d92bf13ae7e410b71ed6dfcd3
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71844670"
---
# <a name="configure-and-access-server-logs-from-the-azure-portal"></a>Azure portal からサーバー ログを構成してアクセスする

Azure portal から [Azure Database for MariaDB の低速クエリ ログ](concepts-server-logs.md)の構成、一覧表示、ダウンロードを行うことができます。

## <a name="prerequisites"></a>前提条件
この記事の手順では、[Azure Database for MariaDB サーバー](quickstart-create-mariadb-server-database-using-azure-portal.md)が必要です。

## <a name="configure-logging"></a>ログの構成
低速クエリ ログへのアクセスを構成します。 

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. Azure Database for MariaDB サーバーを選択します。

3. サイドバーの **[監視]** セクションの下で、 **[サーバー ログ]** を選択します。 
   ![[サーバー ログ] オプションのスクリーンショット](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. サーバー パラメーターを表示するには、 **[ログを有効にし、ログ パラメーターを構成するには、ここをクリックしてください]** を選択します。

5. 調整する必要のあるパラメーターを変更します (**slow_query_log** を **ON** にする、など)。 このセッションで行ったすべての変更が紫色で強調表示されます。 

   パラメーターを変更した後、 **[保存]** を選択します。 または、変更を破棄することができます。

   ![サーバー パラメーターのオプションのスクリーンショット](./media/howto-configure-server-logs-portal/3-save-discard.png)

**[サーバー パラメーター]** ページを閉じると、ログの一覧に戻ることができます。

## <a name="view-list-and-download-logs"></a>リストの表示とログのダウンロード
ログ記録が開始された後、使用可能な低速クエリ ログの一覧を表示したり、個々のログ ファイルをダウンロードしたりすることができます。 

1. Azure Portal を開きます。

2. Azure Database for MariaDB サーバーを選択します。

3. サイドバーの **[監視]** セクションの下で、 **[サーバー ログ]** を選択します。 ページに、ログ ファイルの一覧が表示されます。

   ![ログの一覧が強調して示されている [サーバーログ] ページのスクリーンショット](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > ログの名前付け規則は、 **-< your server name>-yyyymmddhh.log** です。 ファイル名に使用されている日時は、ログが発行された日時です。 ログ ファイルのローテーションは、24 時間ごとか 7.5 GB ごとのどちらか早い方のタイミングで行われます。

4. 必要に応じて、検索ボックスを使用して、日付と時刻に基づいて特定のログをすばやく絞り込みます。 検索はログの名前に対して行われます。

5. 個々のログ ファイルをダウンロードするには、表の行で各ログ ファイルの横にある下向き矢印アイコンを選択します。

   ![下矢印アイコンが強調して示されている [サーバーログ] ページのスクリーンショット](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>診断ログの設定

1. サイドバーの **[監視]** セクションの下で、 **[診断設定]**  >  **[診断設定を追加する]** を選択します。

   ![[診断設定] のオプションのスクリーンショット](./media/howto-configure-server-logs-portal/add-diagnostic-setting.png)

1. 診断設定の名前を指定します。

1. どのデータ シンク (ストレージ アカウント、イベント ハブ、Log Analytics ワークスペース) に低速クエリ ログを送信するか指定します。

1. ログの種類として **[MySqlSlowLogs]** を選択します。
![診断設定構成オプションのスクリーンショット](./media/howto-configure-server-logs-portal/configure-diagnostic-setting.png)

1. 低速クエリ ログをパイプするようにデータ シンクを設定した後、 **[保存]** を選択します。
![[保存] が強調して示されている診断設定構成オプションのスクリーンショット](./media/howto-configure-server-logs-portal/save-diagnostic-setting.png)

1. 構成したデータ シンクを調べて低速クエリ ログにアクセスします。 ログが表示されるまでに最大で 10 分かかる可能性があります。

## <a name="next-steps"></a>次の手順
- [CLI での低速クエリ ログへのアクセス](howto-configure-server-logs-cli.md)に関する記事を参照して、プログラムで低速クエリ ログをダウンロードする方法について学習します。
- Azure Database for MariaDB の[低速クエリ ログ](concepts-server-logs.md)の詳細について学習します。
- パラメーターの定義とログ記録の詳細については、[ログ](https://mariadb.com/kb/en/library/slow-query-log-overview/)に関する MariaDB のドキュメントを参照してください。