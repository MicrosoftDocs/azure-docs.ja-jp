---
title: Azure Portal での PostgreSQL のサーバー ログの構成とアクセス
description: この記事では、Azure Portal から Azure Database for PostgreSQL のサーバー ログの構成方法とアクセス方法について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 509c3af66e8228f142126dae6938ad74daf1d7ad
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53544891"
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Azure Portal でのサーバー ログの構成とアクセス

Azure Portal から [Azure Database for PostgreSQL のサーバー ログ](concepts-server-logs.md)の構成、一覧表示、およびダウンロードができます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for PostgreSQL サーバー](quickstart-create-server-database-portal.md)

## <a name="configure-logging"></a>ログの構成
クエリ ログとエラー ログへのアクセスを構成します。 

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. Azure Database for PostgreSQL サーバーを選択します。

3. サイドバーの **[監視]** セクションの下で、**[サーバー ログ]** を選択します。 

   ![[サーバー ログ] を選択し、[... ここをクリックしてください] を選択](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. サーバー パラメーターを表示するには、見出しの **[ログを有効にし、ログ パラメーターを構成するには、ここをクリックしてください]** を選択します。

5. 調整する必要のあるパラメーターを変更します。 このセッションで行ったすべての変更が紫色で強調表示されます。

   パラメーターを変更すると、**[保存]** をクリックできます。 または変更を **[破棄]** することができます。 

   ![変更を保存または破棄するパラメーターの長いリスト](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. ログのリストに戻るには、**[サーバー パラメーター]** ページの**閉じるボタン** (X のアイコン) をクリックします。

## <a name="view-list-and-download-logs"></a>リストの表示とログのダウンロード
ログ記録が開始されると、使用可能なログのリストを表示したり、[サーバー ログ] ウィンドウで個々のログ ファイルをダウンロードすることができます。 

1. Azure Portal を開きます。

2. Azure Database for PostgreSQL サーバーを選択します。

3. サイドバーの **[監視]** セクションの下で、**[サーバー ログ]** を選択します。 ページには、次のようにログ ファイルのリストが表示されます。

   ![サーバー ログ リスト](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > ログの名前付け規則は、**postgresql-yyyy-mm-dd_hh0000.log** です。 ファイル名に使用される日時は、ログが発行された時間です。 ログ ファイルのローテーションは、1 時間ごとか 100 MB ごとのどちらか早い方のタイミングで行われます。

4. 必要に応じて、**検索ボックス**を使用して、日付/時刻に基づいて特定のログをすばやく絞り込みます。 検索はログの名前に対して行われます。

   ![ログ名の検索例](./media/howto-configure-server-logs-in-portal/5-search.png)

5. 図に示されているように、表の行内の各ログ ファイルの横にある**ダウンロード** ボタン (下向き矢印のアイコン) を使用して、個々のログ ファイルをダウンロードします。

   ![ダウンロード アイコンをクリック](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>次の手順
- [CLI のアクセス サーバー ログ](howto-configure-server-logs-using-cli.md)に関するページを参照して、プログラムでログをダウンロードする方法について学習します。
- Azure DB for PostgreSQL の[サーバー ログ](concepts-server-logs.md)の詳細について学習します。 
- パラメーターの定義と PostgreSQL のログ記録の詳細については、「[Error Reporting and Logging](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)」(エラー レポートとログ記録) の PostgreSQL のドキュメントを参照してください。

