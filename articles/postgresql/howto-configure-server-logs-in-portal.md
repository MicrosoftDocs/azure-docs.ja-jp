---
title: ログを管理する - Azure portal - Azure Database for PostgreSQL - Single Server
description: この記事では、Azure Portal for PostgreSQL - Single Server の Azure Database でサーバーログ（.logファイル）を構成してアクセスする方法について説明します。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 3b52cea1d440506caf5b8244c9643719edd8755c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999248"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Azure portal からの Azure Database for PostgreSQL - Single Server ログの構成とアクセス

Azure portal から [Azure Database for PostgreSQL のログ](concepts-server-logs.md)の構成、一覧表示、およびダウンロードができます。

## <a name="prerequisites"></a>前提条件
この記事の手順では、[Azure Database for PostgreSQL サーバー](quickstart-create-server-database-portal.md)が必要です。

## <a name="configure-logging"></a>ログの構成
クエリ ログとエラー ログへのアクセスを構成します。 

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. Azure Database for PostgreSQL サーバーを選択します。

3. サイドバーの **[監視]** セクションの下で、 **[サーバー ログ]** を選択します。 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="[サーバー ログ] オプションのスクリーンショット":::

4. サーバー パラメーターを表示するには、 **[ログを有効にし、ログ パラメーターを構成するには、ここをクリックしてください]** を選択します。

5. 調整する必要のあるパラメーターを変更します。 このセッションで行ったすべての変更が紫色で強調表示されます。

   パラメーターを変更した後、**[保存]** を選択します。 または、変更を破棄することができます。 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="サーバー パラメーターのオプションのスクリーンショット":::

**[サーバー パラメーター]** ページを閉じると、ログの一覧に戻ることができます。

## <a name="view-list-and-download-logs"></a>リストの表示とログのダウンロード
ログ記録が開始された後、使用可能なログの一覧を表示したり、個々のログ ファイルをダウンロードしたりすることができます。 

1. Azure portal を開きます。

2. Azure Database for PostgreSQL サーバーを選択します。

3. サイドバーの **[監視]** セクションの下で、 **[サーバー ログ]** を選択します。 ページに、ログ ファイルの一覧が表示されます。

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="ログの一覧が強調して示されている [サーバーログ] ページのスクリーンショット":::

   > [!TIP]
   > ログの名前付け規則は、**postgresql-yyyy-mm-dd_hh0000.log** です。 ファイル名に使用されている日時は、ログが発行された日時です。 ログ ファイルのローテーションは、1 時間ごとか 100 MB ごとのどちらか早い方のタイミングで行われます。

4. 必要に応じて、検索ボックスを使用して、日付と時刻に基づいて特定のログをすばやく絞り込みます。 検索はログの名前に対して行われます。

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-search.png" alt-text="検索ボックスと結果が強調表示されている [サーバーログ] ページのスクリーンショット":::

5. 個々のログ ファイルをダウンロードするには、表の行で各ログ ファイルの横にある下向き矢印アイコンを選択します。

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/6-download.png" alt-text="下矢印アイコンが強調して示されている [サーバーログ] ページのスクリーンショット":::

## <a name="next-steps"></a>次のステップ
- [CLI のアクセス サーバー ログ](howto-configure-server-logs-using-cli.md)に関するページを参照して、プログラムでログをダウンロードする方法について学習します。
- Azure Database for PostgreSQL の[サーバー ログ](concepts-server-logs.md)の詳細について学習します。 
- パラメーターの定義と PostgreSQL のログ記録の詳細については、「[Error Reporting and Logging](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)」(エラー レポートとログ記録) の PostgreSQL のドキュメントを参照してください。

