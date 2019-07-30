---
title: ODBC を使って Azure Data Explorer に接続する
description: この記事では、Azure Data Explorer への Open Database Connectivity (ODBC) 接続をセットアップする方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 65795b5b4dea8d2cdeecf5f78f9de751f275dac0
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537584"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>ODBC を使って Azure Data Explorer に接続する

Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) は、データベース アクセスのために広く受け入れられているアプリケーション プログラミング インターフェイス (API) です。 専用のコネクタがないアプリケーションから Azure Data Explorer に接続する場合に、ODBC を使用します。

バックグラウンドでは、アプリケーションで ODBC インターフェイスの関数が呼び出され、*ドライバー* というデータベース固有のモジュールで実装されます。 Azure Data Explorer では SQL Server 通信プロトコル ([MS-TDS](/azure/kusto/api/tds/)) のサブセットがサポートされるため、SQL Server 用の ODBC ドライバーを使用することができます。

次のビデオを使用して、ODBC 接続を作成する方法を確認できます。 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

また、後述するように、[ODBC データ ソースを構成](#configure-the-odbc-data-source)することもできます。 

この記事では、SQL Server ODBC ドライバーを使用して、ODBC をサポートするアプリケーションから Azure Data Explorer に接続できるようにする方法を説明します。 

## <a name="prerequisites"></a>前提条件

以下のものが必要になります。

* ご利用のオペレーティング システム用の [Microsoft ODBC Driver for SQL Server バージョン 17.2.0.1 以降](/sql/connect/odbc/download-odbc-driver-for-sql-server)。

## <a name="configure-the-odbc-data-source"></a>ODBC データ ソースを構成する

ODBC Driver for SQL Server を使用して ODBC データ ソースを構成する場合は、以下の手順に従います。

1. Windows で、*ODBC データ ソース* を検索し、ODBC データ ソース デスクトップ アプリを開きます。

1. **[追加]** を選択します。

    ![データ ソースを追加する](media/connect-odbc/add-data-source.png)

1. **[ODBC Driver 17 for SQL Server]** 、 **[完了]** の順に選択します。

    ![ドライバーを選択する](media/connect-odbc/select-driver.png)

1. 接続の名前と説明、および接続先のクラスターを入力してから、 **[次へ]** を選択します。 クラスターの URL は、 *\<クラスター名\>.\<リージョン\>.kusto.windows.net* という形式にする必要があります。

    ![サーバーを選択する](media/connect-odbc/select-server.png)

1. **[Azure Active Directory 統合]** 、 **[次へ]** の順に選択します。

    ![Active Directory 統合](media/connect-odbc/active-directory-integrated.png)

1. サンプル データを含むデータベース、 **[次へ]** の順に選択します。

    ![既定のデータベースを変更する](media/connect-odbc/change-default-database.png)

1. 次の画面では、すべてのオプションを既定値のままにし、 **[完了]** を選択します。

1. **[データ ソースのテスト]** を選択します。

    ![データ ソースをテストする](media/connect-odbc/test-data-source.png)

1. テストに成功したことを確認してから、 **[OK]** を選択します。 テストに成功しなかった場合は、前の手順で指定した値を調べ、クラスターに接続するための十分なアクセス許可があることを確認します。

    ![テストに成功しました](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>次の手順

* [Tableau から Azure Data Explorer に接続する](tableau.md)