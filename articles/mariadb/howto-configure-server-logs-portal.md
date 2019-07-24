---
title: Azure portal での Azure Database for MariaDB のサーバー ログの構成とアクセス
description: この記事では、Azure portal から Azure Database for MariaDB のサーバー ログの構成方法とアクセス方法について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 4ff2fbd5976a8e203bbc43a87b31ddb1bed63402
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548818"
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Azure Portal でのサーバー ログの構成とアクセス

Azure portal から [Azure Database for MariaDB のサーバー ログ](concepts-server-logs.md)の構成、一覧表示、ダウンロードを行うことができます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for MariaDB サーバー](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>ログの構成
低速クエリ ログへのアクセスを構成します。 

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. Azure Database for MariaDB サーバーを選択します。

3. サイドバーの **[監視]** セクションの下で、**[サーバー ログ]** を選択します。 
   ![サーバー ログを選択し、クリックして構成](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. サーバー パラメーターを表示するには、見出しの **[ログを有効にし、ログ パラメーターを構成するには、ここをクリックしてください]** を選択します。

5. 調整する必要のあるパラメーターを変更します ("slow_query_log" を "ON" にする、など)。 このセッションで行ったすべての変更が紫色で強調表示されます。 

   パラメーターを変更すると、**[保存]** をクリックできます。 または変更を **[破棄]** することができます。

   ![[保存] または [破棄] をクリック](./media/howto-configure-server-logs-portal/3-save-discard.png)

6. ログのリストに戻るには、**[サーバー パラメーター]** ページの**閉じるボタン** (X のアイコン) をクリックします。

## <a name="view-list-and-download-logs"></a>リストの表示とログのダウンロード
ログ記録が開始されると、使用可能なログのリストを表示したり、[サーバー ログ] ウィンドウで個々のログ ファイルをダウンロードすることができます。 

1. Azure Portal を開きます。

2. Azure Database for MariaDB サーバーを選択します。

3. サイドバーの **[監視]** セクションの下で、**[サーバー ログ]** を選択します。 ページには、次のようにログ ファイルのリストが表示されます。

   ![ログのリスト](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > ログの名前付け規則は、**-< your server name>-yyyymmddhh.log** です。 ファイル名に使用される日時は、ログが発行された時間です。 ログ ファイルのローテーションは、24 時間ごとか 7.5 GB ごとのどちらか早い方のタイミングで行われます。

4. 必要に応じて、**検索ボックス**を使用して、日付/時刻に基づいて特定のログをすばやく絞り込みます。 検索はログの名前に対して行われます。

5. 図に示されているように、表の行内の各ログ ファイルの横にある**ダウンロード** ボタン (下向き矢印のアイコン) を使用して、個々のログ ファイルをダウンロードします。

   ![ダウンロード アイコンをクリック](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="next-steps"></a>次の手順
- Azure Database for MariaDB の[サーバー ログ](concepts-server-logs.md)の詳細について学習します。
- パラメーターの定義とログ記録の詳細については、[ログ](https://mariadb.com/kb/en/library/slow-query-log-overview/) に関するページの MariaDB のドキュメントを参照してください。

<!-- - See [Access Server Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download logs programmatically. -->
