---
title: ODBC を使って Azure Data Explorer に接続する
description: このハウツーでは、Azure Data Explorer への ODBC 接続を設定する方法を学習します。この接続を使用して、Tableau でデータを視覚化します。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: d01c825e50e30e3545a0d47e432835c658d677af
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59043883"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>ODBC を使って Azure Data Explorer に接続する

Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) は、データベース アクセスのために広く受け入れられているアプリケーション プログラミング インターフェイス (API) です。 専用のコネクタがないアプリケーションから Azure Data Explorer に接続する場合に、ODBC を使用します。

バックグラウンドでは、アプリケーションで ODBC インターフェイスの関数が呼び出され、*ドライバー* というデータベース固有のモジュールで実装されます。 Azure Data Explorer では SQL Server 通信プロトコル ([MS-TDS](/azure/kusto/api/tds/)) のサブセットがサポートされるため、SQL Server 用の ODBC ドライバーを使用することができます。

この記事では、SQL Server ODBC ドライバーを使用して、ODBC をサポートするアプリケーションから Azure Data Explorer に接続できるようにする方法を学習します。 その後、必要に応じて Tableau から Azure Data Explorer に接続し、サンプル クラスターからデータを取り込むことができます。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、以下が必要です。

* ご利用のオペレーティング システム用の [Microsoft ODBC Driver for SQL Server バージョン 17.2.0.1 以降](/sql/connect/odbc/download-odbc-driver-for-sql-server)。

* Tableau の例に従う場合は、以下のものも必要です。

  * Tableau Desktop (完全または[評価](https://www.tableau.com/products/desktop/download)版)。

  * StormEvents サンプル データを含むクラスター。 詳細については、「[クイック スタート: Azure Data Explorer クラスターとデータベースを作成する](create-cluster-database-portal.md)」、および「[Azure のデータ エクスプローラーにサンプル データを取り込む](ingest-sample-data.md)」を参照してください。

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-odbc-data-source"></a>ODBC データ ソースを構成する

ODBC Driver for SQL Server を使用して ODBC データ ソースを構成する場合は、以下の手順に従います。

1. Windows で、*ODBC データ ソース* を検索し、ODBC データ ソース デスクトップ アプリを開きます。

1. **[追加]** を選択します。

    ![データ ソースを追加する](media/connect-odbc/add-data-source.png)

1. **[ODBC Driver 17 for SQL Server]**、**[完了]** の順に選択します。

    ![ドライバーを選択する](media/connect-odbc/select-driver.png)

1. 接続の名前と説明、および接続先のクラスターを入力してから、**[次へ]** を選択します。 クラスターの URL は、*\<クラスター名\>.\<リージョン\>.kusto.windows.net* という形式にする必要があります。

    ![サーバーを選択する](media/connect-odbc/select-server.png)

1. **[Azure Active Directory 統合]**、**[次へ]** の順に選択します。

    ![Active Directory 統合](media/connect-odbc/active-directory-integrated.png)

1. サンプル データを含むデータベース、**[次へ]** の順に選択します。

    ![既定のデータベースを変更する](media/connect-odbc/change-default-database.png)

1. 次の画面では、すべてのオプションを既定値のままにし、**[完了]** を選択します。

1. **[データ ソースのテスト]** を選択します。

    ![データ ソースをテストする](media/connect-odbc/test-data-source.png)

1. テストに成功したことを確認してから、**[OK]** を選択します。 テストに成功しなかった場合は、前の手順で指定した値を調べ、クラスターに接続するための十分なアクセス許可があることを確認します。

    ![テストに成功しました](media/connect-odbc/test-succeeded.png)

## <a name="visualize-data-in-tableau-optional"></a>Tableau でデータを視覚化する (省略可能)

これで ODBC の構成が完了したので、サンプル データを Tableau に取り込むことができます。

1. Tableau Desktop の左側のメニューで、**[Other Databases (ODBC)]\(その他のデータベース (ODBC)\)** を選択します。

    ![ODBC に接続する](media/connect-odbc/connect-odbc.png)

1. **[DSN]** では、ODBC に対して作成したデータ ソースを選び、**[サインイン]** を選択します。

    ![ODBC のサインイン](media/connect-odbc/odbc-sign-in.png)

1. **[データベース]** では、*TestDatabase* など、サンプル クラスターのデータベースを選択します。 **[スキーマ]** では、*[dbo]*、**[テーブル]** では、*[StormEvents]* サンプル テーブルを選びます。

    ![データベースとテーブルを選択する](media/connect-odbc/select-database-table.png)

1. これで、Tableau にサンプル データのスキーマが表示されるようになりました。 **[今すぐ更新]**  を選択して、Tableau にデータを取り込みます。

    ![データの更新](media/connect-odbc/update-data.png)

    データがインポートされると、Tableau に次の図のようなデータの行が表示されます。

    ![結果セット](media/connect-odbc/result-set.png)

1. これで、Azure Data Explorer から取り込んだデータに基づいて、Tableau で視覚化することができました。 詳細については、[Tableau の学習](https://www.tableau.com/learn)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

[Azure Data Explorer のクエリを記述する](write-queries.md)

[チュートリアル: Power BI で Azure Data Explorer のデータを視覚化する](visualize-power-bi.md)