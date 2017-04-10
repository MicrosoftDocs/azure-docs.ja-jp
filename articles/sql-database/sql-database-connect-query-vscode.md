---
title: "VS Code: Azure SQL Database に接続してデータを照会する | Microsoft Docs"
description: "Visual Studio Code を使用して Azure SQL Database に接続する方法について説明します。 また、Transact-SQL (T-SQL) ステートメントを実行して、データの照会と編集を行います。"
metacanonical: 
keywords: "SQL Database への接続"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 676bd799-a571-4bb8-848b-fb1720007866
ms.service: sql-database
ms.custom: manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 86471fe29bbc9076624d96b83f7001d8755363bc
ms.lasthandoff: 03/25/2017


---
# <a name="azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>Azure SQL Database: Visual Studio Code を使って接続とデータの照会を行う

[Visual Studio Code](https://code.visualstudio.com/docs) は、拡張機能をサポートするグラフィカル コード エディターであり、Linux、macOS、Windows で使用できます。 [mssql 拡張機能](https://aka.ms/mssql-marketplace)がインストールされた Visual Studio Code を使用し、Azure SQL データベースに接続してデータを照会します。 このガイドでは、Visual Studio Code を使用して Azure SQL データベースに接続し、照会、挿入、更新、削除の各ステートメントを実行する方法について詳しく説明します。

このクイック スタートでは、次のクイック スタートで作成されたリソースが出発点として使用されます。

- [DB の作成 - ポータル](sql-database-get-started-portal.md)
- [DB の作成 - CLI](sql-database-get-started-cli.md)

開始する前に、必ず最新バージョンの [Visual Studio Code](https://code.visualstudio.com/Download) をインストールして [mssql 拡張機能](https://aka.ms/mssql-marketplace)を読み込んでおいてください。 mssql 拡張機能のインストール ガイダンスについては、「[Install VS Code (VS Code のインストール)](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code)」を参照してください。 

## <a name="get-connection-information"></a>接続情報の取得

Azure Portal で、Azure SQL Database サーバーの完全修飾サーバー名を取得します。 その完全修飾サーバー名は、Visual Studio Code でのサーバーへの接続に使用します。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. 左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。 
3. そのデータベースの Azure Portal ページの **[要点]** ウィンドウで、後でこのクイック スタートで使用する**サーバー名**を見つけてコピーします。

    <img src="./media/sql-database-connect-query-ssms/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="set-language-mode-to-sql"></a>言語モードを SQL に設定

Visual Studio Code で言語モードを **[SQL]** に設定し、mssql コマンドと T-SQL IntelliSense を有効にします。

1. 新しい Visual Studio Code ウィンドウを開きます。 

2. **Ctrl + K キーを押して M キー**を押します。次に、「**SQL**」と入力して **Enter** キーを押し、言語モードを SQL に設定します。 

<img src="./media/sql-database-connect-query-vscode/vscode-language-mode.png" alt="SQL language mode" style="width: 780px;" />

## <a name="connect-to-the-server"></a>サーバーへの接続

Visual Studio Code を使用して、Azure SQL Database サーバーに対する接続を確立します。

1. VS Code で、**Ctrl + Shift + P** キー (または **F1** キー) を押してコマンド パレットを開きます。

2. 「**sqlcon**」と入力して **Enter** キーを押します。

3. **[はい]** をクリックして言語を **[SQL]** に設定します。

4. **Enter** キーを押して **[接続プロファイルの作成]** を選択します。 これで、SQL Server インスタンスの接続プロファイルが作成されます。

5. プロンプトに従って、新しい接続プロファイルの接続プロパティを指定します。 それぞれの値を指定したら、**Enter** キーを押して続行します。 

   次の表で、接続プロファイルのプロパティについて説明します。

   | Setting | Description |
   |-----|-----|
   | **[サーバー名]** | 完全修飾サーバー名を入力します (**mynewserver20170313.database.windows.net** など) |
   | **[データベース名]** | データベース名を入力します (**mySampleDatabase** など) |
   | **認証** | [SQL ログイン] を選択します |
   | **ユーザー名** | サーバー管理者アカウントを入力します |
   | **[パスワード (SQL ログイン)]** | サーバー管理者アカウントのパスワードを入力します | 
   | **[パスワードを保存しますか?]** | **[はい]** または **[いいえ]** を選択します |
   | **(省略可能) [このプロファイルの名前を入力してください]** | 接続プロファイルの名前を入力します (**mySampleDatabase**) 

6. **Esc** キーを押して、プロファイルが作成され、接続が確立されたことを示す情報メッセージを閉じます。

7. ステータス バーで接続を確認します。

   <img src="./media/sql-database-connect-query-vscode/vscode-connection-status.png" alt="Connection status" style="width: 780px;" />

## <a name="query-data"></a>データのクエリを実行する

[SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL ステートメントを使用して、Azure SQL データベースのデータを照会します。

1. **エディター** ウィンドウで、空のクエリ ウィンドウに次のクエリを入力します。

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. **Ctrl + Shift + E** キーを押して、Product と ProductCategory のテーブルからデータを取得します。

    <img src="./media/sql-database-connect-query-vscode/query.png" alt="Query" style="width: 780px;" />

## <a name="insert-data"></a>データを挿入する

[INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL ステートメントを使用して、Azure SQL データベースにデータを挿入します。

1. **エディター** ウィンドウで、前のクエリを削除して次のクエリを入力します。

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

3. **Ctrl + Shift + E** キーを押して、新しい行を Product テーブルに挿入します。

## <a name="update-data"></a>データの更新

[UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL ステートメントを使用して、Azure SQL データベースのデータを更新します。

1.  **エディター** ウィンドウで、前のクエリを削除して次のクエリを入力します。

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

3. **Ctrl + Shift + E** キーを押して、Product テーブルの指定した行を更新します。

## <a name="delete-data"></a>データの削除

[DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL ステートメントを使用して、Azure SQL データベースのデータを削除します。

1. **エディター** ウィンドウで、前のクエリを削除して次のクエリを入力します。

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

3. **Ctrl + Shift + E** キーを押して、Product テーブルの指定した行を削除します。

## <a name="next-steps"></a>次のステップ

- Visual Studio Code については、[Visual Studio Code](https://code.visualstudio.com/docs) に関するページを参照してください。
- SQL Server Management Studio を使用したデータの照会と編集については、[SSMS](https://msdn.microsoft.com/library/ms174173.aspx) に関するページを参照してください。

