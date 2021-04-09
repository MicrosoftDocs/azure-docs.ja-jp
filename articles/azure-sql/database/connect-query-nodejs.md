---
title: Node.js を使用してデータベースを照会する
description: Node.js を使用して、Azure SQL Database または Azure SQL Managed Instance 内のデータベースに接続し、T-SQL ステートメントを使用してクエリを実行するプログラムを作成する方法。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: seo-javascript-september2019, seo-javascript-october2019, sqldbrb=2, devx-track-js
ms.openlocfilehash: 44530577972839aacb803d1722fa97716088fa0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91325438"
---
# <a name="quickstart-use-nodejs-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>クイック スタート:Node.js を使用して Azure SQL Database または Azure SQL Managed Instance 内のデータベースに対してクエリを実行する
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

このクイックスタートでは、Node.js を使用してデータベースに接続し、データに対してクエリを実行します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

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


- [Node.js](https://nodejs.org) 関連のソフトウェア

  # <a name="macos"></a>[macOS](#tab/macos)

  「[macOS で SQL Server を使用して Node.js アプリを作成する](https://www.microsoft.com/sql-server/developer-get-started/node/mac/)」の手順 **1.2** と **1.3** を使用して、Homebrew と Node.js をインストールした後、ODBC ドライバーと SQLCMD をインストールします。

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  「[Ubuntu で SQL Server を使用して Node.js アプリを作成する](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/)」の手順 **1.2** と **1.3** を使用して、Node.js をインストールした後、ODBC ドライバーと SQLCMD をインストールします。

  # <a name="windows"></a>[Windows](#tab/windows)

  「[Windows で SQL Server を使用して Node.js アプリを作成する](https://www.microsoft.com/sql-server/developer-get-started/node/windows/)」の手順 **1.2** と **1.3** を使用して、Chocolatey と Node.js をインストールした後、ODBC ドライバーと SQLCMD をインストールします。

  ---

> [!IMPORTANT]
> この記事のスクリプトは、**Adventure Works** データベースを使用するように記述されています。

> [!NOTE]
> 必要に応じて、Azure SQL Managed Instance を使用することを選択できます。
>
> 作成して構成するには、[Azure portal](../managed-instance/instance-create-quickstart.md)、[PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md)、または [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) を使用してから、[オンプレミス](../managed-instance/point-to-site-p2s-configure.md)または [VM](../managed-instance/connect-vm-instance-configure.md) 接続を設定します。
>
> データを読み込む方法については、[Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) ファイルの [BACPAC を使用した復元](database-import.md)に関するページを参照するか、[Wide World Importers データベースの復元](../managed-instance/restore-sample-database-quickstart.md)に関するページを参照してください。

## <a name="get-server-connection-information"></a>サーバーの接続情報を取得する

Azure SQL Database のデータベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名またはホスト名、データベース名、およびログイン情報が必要になります。

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. **[SQL Database]** または **[SQL Managed Instance]** ページに移動します。

3. **[概要]** ページで、Azure SQL Database 内のデータベースの **[サーバー名]** の横にある完全修飾サーバー名、または Azure SQL Managed Instance または Azure VM 上の SQL Server の **[ホスト]** の横にある完全修飾サーバー名 (または IP アドレス) を確認します。 サーバー名またはホスト名をコピーするには、名前をポイントして **[コピー]** アイコンを選択します。

> [!NOTE]
> Azure VM 上の SQL Server の接続情報については、[SQL Server への接続](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)に関するページをご覧ください。

## <a name="create-the-project"></a>プロジェクトを作成する

コマンド プロンプトを開き、*sqltest* という名前のフォルダーを作成します。 作成したフォルダーを開き、次のコマンドを実行します。

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-the-database"></a>データベースに対してクエリを実行するコードを追加する

1. 任意のテキスト エディターで新しいファイル (*sqltest.js*) を作成します。

1. その内容を次のコードに置き換えます。 そのうえで、サーバー、データベース、ユーザー、パスワードの適切な値を入力してください。

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
          console.log("%s\t%s", column.metadata.colName, column.value);
        });
      });

      connection.execSql(request);
    }
    ```

> [!NOTE]
> このコード例では、Azure SQL Database で **AdventureWorksLT** サンプル データベースを使用します。

## <a name="run-the-code"></a>コードの実行

1. コマンド プロンプトでプログラムを実行します。

    ```bash
    node sqltest.js
    ```

1. 先頭から 20 行が返されることを確認して、アプリケーション ウィンドウを閉じます。

## <a name="next-steps"></a>次のステップ

- [SQL Server 用 Microsoft Node.js ドライバー](/sql/connect/node-js/node-js-driver-for-sql-server)

- Windows/Linux/macOS における [.Net Core](connect-query-dotnet-core.md)、[Visual Studio Code](connect-query-vscode.md)、[SSMS](connect-query-ssms.md) (Windows のみ) を使用した接続と照会

- [Windows/Linux/macOS の .NET Core でのコマンド ラインの使用に関する概要](/dotnet/core/tutorials/using-with-xplat-cli)

- [.NET](design-first-database-csharp-tutorial.md) または [SSMS](design-first-database-tutorial.md) を使用して Azure SQL Database で最初のデータベースを設計する
