---
title: PHP を使用してクエリを実行する
description: PHP を使用して、Azure SQL Database または Azure SQL Managed Instance 内のデータベースに接続し、T-SQL ステートメントを使用してクエリを実行するプログラムを作成する方法。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: sqldbrb=2
ms.openlocfilehash: e0ad8e9e71a0ee2c23412d535fe79955edb39ba8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576652"
---
# <a name="quickstart-use-php-to-query-a-database-in-azure-sql-database"></a>クイック スタート:PHP を使用して Azure SQL Database 内のデータベースに対してクエリを実行する
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

この記事では、[PHP](https://php.net/manual/en/intro-whatis.php) を使用して Azure SQL Database または Azure SQL Managed Instance 内のデータベースに接続する方法について説明します。 その後、T-SQL ステートメントを使用してデータを照会することができます。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Azure SQL Database または Azure SQL Managed Instance 内のデータベース。 次のいずれかのクイックスタートを使用して、データベースを作成し、構成できます。

  | アクション | SQL Database | SQL Managed Instance | Azure VM 上の SQL Server |
  |:--- |:--- |:---|:---|
  | 作成| [ポータル](single-database-create-quickstart.md) | [ポータル](../managed-instance/instance-create-quickstart.md) | [ポータル](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | 構成 | [サーバーレベルの IP ファイアウォール規則](firewall-create-server-level-portal-quickstart.md)| [VM からの接続](../managed-instance/connect-vm-instance-configure.md)|
  |||[オンプレミスからの接続](../managed-instance/point-to-site-p2s-configure.md) | [SQL Server インスタンスに接続する](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |データの読み込み|クイック スタートごとに読み込まれる Adventure Works|[Wide World Importers を復元する](../managed-instance/restore-sample-database-quickstart.md) | [Wide World Importers を復元する](../managed-instance/restore-sample-database-quickstart.md) |
  |||[GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) の [BACPAC](database-import.md) ファイルから Adventure Works を復元またはインポートする| [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) の [BACPAC](database-import.md) ファイルから Adventure Works を復元またはインポートする|
  |||




  > [!IMPORTANT]
  > この記事のスクリプトは、Adventure Works データベースを使用するように記述されています。 SQL Managed Instance では、Adventure Works データベースをインスタンス データベースにインポートするか、Wide World Importers データベースを使用するようにこの記事のスクリプトを修正する必要があります。

- ご使用のオペレーティング システムに対応した、以下の PHP 関連のソフトウェアをインストール済みであること。

  - **macOS**: PHP と ODBC ドライバーをインストールした後、SQL Server 用の PHP ドライバーをインストールします。 [手順 1、2、および 3](/sql/connect/php/installation-tutorial-linux-mac) を参照してください。

  - **Linux**: PHP と ODBC ドライバーをインストールした後、PHP Driver for SQL Server をインストールします。 [手順 1、2、および 3](/sql/connect/php/installation-tutorial-linux-mac) を参照してください。

  - **Windows**: PHP と PHP ドライバーをインストールした後、ODBC ドライバーと SQLCMD をインストールします。 [手順 1.2. と手順 1.3.](https://www.microsoft.com/sql-server/developer-get-started/php/windows/) を参照してください。

## <a name="get-server-connection-information"></a>サーバーの接続情報を取得する

Azure SQL Database のデータベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名またはホスト名、データベース名、およびログイン情報が必要になります。

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. **[SQL Database]** または **[SQL Managed Instance]** ページに移動します。

3. **[概要]** ページで、Azure SQL Database 内のデータベースの **[サーバー名]** の横にある完全修飾サーバー名、または Azure SQL Managed Instance または Azure VM 内の SQL Server の **[ホスト]** の横にある完全修飾サーバー名 (または IP アドレス) を確認します。 サーバー名またはホスト名をコピーするには、名前をポイントして **[コピー]** アイコンを選択します。

> [!NOTE]
> Azure VM 上の SQL Server の接続情報については、[SQL Server インスタンスへの接続](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)に関するページをご覧ください。

## <a name="add-code-to-query-the-database"></a>データベースに対してクエリを実行するコードを追加する

1. 任意のテキスト エディターで新しいファイル (*sqltest.php*) を作成します。  

1. その内容を次のコードに置き換えます。 そのうえで、サーバー、データベース、ユーザー、パスワードの適切な値を入力してください。

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
       );
       //Establishes the connection
       $conn = sqlsrv_connect($serverName, $connectionOptions);
       $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
            FROM [SalesLT].[ProductCategory] pc
            JOIN [SalesLT].[Product] p
            ON pc.productcategoryid = p.productcategoryid";
       $getResults= sqlsrv_query($conn, $tsql);
       echo ("Reading data from table" . PHP_EOL);
       if ($getResults == FALSE)
           echo (sqlsrv_errors());
       while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
        echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
       }
       sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>コードの実行

1. コマンド プロンプトでアプリを実行します。

   ```bash
   php sqltest.php
   ```

1. 先頭から 20 行が返されることを確認して、アプリ ウィンドウを閉じます。

## <a name="next-steps"></a>次のステップ

- [Azure SQL Database で最初のデータベースを設計する](design-first-database-tutorial.md)
- [SQL Server 用 Microsoft PHP ドライバー](https://github.com/Microsoft/msphpsql/)
- [問題の報告と質問](https://github.com/Microsoft/msphpsql/issues)
- [再試行ロジックの例:PHP を使用して Azure SQL に弾性的に接続する](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
