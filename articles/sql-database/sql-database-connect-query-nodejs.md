---
title: "Node.js を使用して Azure SQL Database に照会する | Microsoft Docs"
description: "このトピックでは、Azure SQL Database に接続して Transact-SQL ステートメントでデータベースに照会するプログラムを Node.js で作成する方法について説明します。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 07/05/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 1907a95df9132c059d7985b6d5cd913536bf3403
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

---
# <a name="use-nodejs-to-query-an-azure-sql-database"></a>Node.js を使用して Azure SQL Database に照会する

このクイック スタート チュートリアルでは、Azure SQL Database に接続して Transact-SQL ステートメントでデータを照会するプログラムを [Node.js](https://nodejs.org/en/) で作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタート チュートリアルを完了するには、以下のものが必要です。

- Azure SQL Database。 このクイック スタートでは、次のいずれかのクイック スタートで作成したリソースを使用します。 

   - [DB の作成 - ポータル](sql-database-get-started-portal.md)
   - [DB の作成 - CLI](sql-database-get-started-cli.md)
   - [DB の作成 - PowerShell](sql-database-get-started-powershell.md)

- このクイック スタート チュートリアルに使用するコンピューターのパブリック IP アドレスに対する[サーバー レベルのファイアウォール規則](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。
- ご使用のオペレーティング システムに対応した Node.js とそれに関連するソフトウェアをインストール済みであること。
    - **MacOS**: Homebrew と Node.js をインストールした後、ODBC ドライバーと SQLCMD をインストールします。 [手順 1.2. と手順 1.3.](https://www.microsoft.com/sql-server/developer-get-started/node/mac/) を参照してください。
    - **Ubuntu**: Node.js をインストールした後、ODBC ドライバーと SQLCMD をインストールします。 [手順 1.2. と手順 1.3.](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/) を参照してください。
    - **Windows**: Chocolatey と Node.js をインストールした後、ODBC ドライバーと SQL CMD をインストールします。 [手順 1.2. と手順 1.3.](https://www.microsoft.com/sql-server/developer-get-started/node/windows/) を参照してください。

## <a name="sql-server-connection-information"></a>SQL Server の接続情報

Azure SQL データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名、データベース名、ログイン情報が必要になります。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. 左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。 
3. データベースの **[概要]** ページで、次の図に示すように、完全修飾サーバー名を確認します。 サーバー名をポイントすると、**[コピーするにはクリックします]** オプションが表示されます。 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Azure SQL Database サーバーのログイン情報を忘れた場合は、[SQL データベース サーバー] ページに移動して、サーバー管理者名を表示し、必要に応じて、パスワードをリセットします。

> [!IMPORTANT]
> このチュートリアルを実行するコンピューターのパブリック IP アドレスに対してファイアウォール規則を設定しておく必要があります。 別のコンピューターから実行する場合または別のパブリック IP アドレスがある場合は、[Azure Portal でサーバー レベルのファイアウォール規則](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)を作成してください。 

## <a name="create-a-nodejs-project"></a>Node.js プロジェクトの作成

コマンド プロンプトを開き、*sqltest* という名前のフォルダーを作成します。 作成したフォルダに移動し、次のコマンドを実行します。

    
    npm init -y
    npm install tedious
    npm install async
    

## <a name="insert-code-to-query-sql-database"></a>SQL Database に照会するコードの挿入

1. ご使用の開発環境または任意のテキスト エディターで新しいファイル (**sqltest.js**) を作成します。

2. その内容を次のコードで置き換えます。サーバー、データベース、ユーザー、パスワードには、実際の値を追加してください。

   ```js
   var Connection = require('tedious').Connection;
   var Request = require('tedious').Request;

   // Create connection to database
   var config = 
      {
        userName: 'someuser', // update me
        password: 'somepassword', // update me
        server: 'edmacasqlserver.database.windows.net', // update me
        options: 
           {
              database: 'somedb' //update me
              , encrypt: true
           }
      }
   var connection = new Connection(config);

   // Attempt to connect and execute queries if connection goes through
   connection.on('connect', function(err) 
      {
        if (err) 
          {
             console.log(err)
          }
       else
          {
              queryDatabase()
          }
      }
    );

   function queryDatabase()
      { console.log('Reading rows from the Table...');

          // Read all rows from table
        request = new Request(
             "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
                function(err, rowCount, rows) 
                   {
                       console.log(rowCount + ' row(s) returned');
                       process.exit();
                   }
               );
    
        request.on('row', function(columns) {
           columns.forEach(function(column) {
               console.log("%s\t%s", column.metadata.colName, column.value);
            });
                });
        connection.execSql(request);
      }
```

## <a name="run-the-code"></a>コードの実行

1. コマンド プロンプトで、次のコマンドを実行します。

   ```js
   node sqltest.js
   ```

2. 先頭から 20 行が返されることを確認して、アプリケーション ウィンドウを閉じます。

## <a name="next-steps"></a>次のステップ

- [SQL Server 用 Microsoft Node.js ドライバー](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)についての情報を入手します。
- Windows/Linux/macOS で [.NET Core を使い Azure SQL Database に接続してデータベースに照会](sql-database-connect-query-dotnet-core.md)する方法についての情報を入手します。  
- [Windows/Linux/macOS の .NET Core でのコマンド ラインの使用に関する概要](/dotnet/core/tutorials/using-with-xplat-cli)を把握します。
- [SSMS で初めての Azure SQL Database を設計](sql-database-design-first-database.md)する方法や [.NET で初めての Azure SQL Database を設計](sql-database-design-first-database-csharp.md)する方法についての情報を入手します。
- [SSMS を使用した接続とクエリ](sql-database-connect-query-ssms.md)の方法についての情報を入手します。
- [Visual Studio Code を使用した接続とクエリ](sql-database-connect-query-vscode.md)の方法についての情報を入手します。



