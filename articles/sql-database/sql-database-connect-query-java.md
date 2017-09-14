---
title: "Java を使用して Azure SQL Database に照会する | Microsoft Docs"
description: "このトピックでは、Azure SQL Database に接続して Transact-SQL ステートメントでデータベースに照会するプログラムを Java で作成する方法について説明します。"
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 07/10/2017
ms.author: andrela
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 264a9e8f109ff77d8fbd81f01ba40d21aebea538
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="use-java-to-query-an-azure-sql-database"></a>Java を使用して Azure SQL Database に照会する

このクイック スタートでは、[Java](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) を使って Azure SQL データベースに接続した後、Transact-SQL ステートメントを使ってデータを照会する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタート チュートリアルを完了するには、次の前提条件を満たしておく必要があります。

- Azure SQL Database。 このクイック スタートでは、次のいずれかのクイック スタートで作成したリソースを使用します。 

   - [DB の作成 - ポータル](sql-database-get-started-portal.md)
   - [DB の作成 - CLI](sql-database-get-started-cli.md)
   - [DB の作成 - PowerShell](sql-database-get-started-powershell.md)

- このクイック スタート チュートリアルに使用するコンピューターのパブリック IP アドレスに対する[サーバー レベルのファイアウォール規則](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。

- ご使用のオペレーティング システムに対応した Java とそれに関連するソフトウェアをインストール済みであること。

    - **MacOS**: Homebrew と Java をインストールした後、Maven をインストールします。 [手順 1.2. と手順 1.3.](https://www.microsoft.com/sql-server/developer-get-started/java/mac/) を参照してください。
    - **Ubuntu**: Java Development Kit をインストールし、Maven をインストールします。 [手順 1.2.、手順 1.3.、手順 1.4.](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/) を参照してください。
    - **Windows**: Java Development Kit をインストールした後、Maven をインストールします。 [手順 1.2. と手順 1.3.](https://www.microsoft.com/sql-server/developer-get-started/java/windows/) を参照してください。    

## <a name="sql-server-connection-information"></a>SQL Server の接続情報

Azure SQL データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名、データベース名、ログイン情報が必要になります。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. 左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。 
3. データベースの **[概要]** ページで、次の図に示すように、完全修飾サーバー名を確認します。サーバー名をポイントすると、**[コピーするにはクリックします]** オプションが表示されます。  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. サーバーのログイン情報を忘れた場合は、[SQL データベース サーバー] ページに移動して、サーバー管理者名を表示します。  必要に応じて、パスワードをリセットします。     

## <a name="create-maven-project-and-dependencies"></a>**Maven プロジェクトと依存関係の作成**
1. ターミナルから **sqltest** という新しい Maven プロジェクトを作成します。 

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

2. 確認を求められたら「**Y**」と入力します。
3. **sqltest** ディレクトリに移動し、任意のテキスト エディターで ***pom.xml*** を開きます。  次のコードを使用して、プロジェクトの依存関係に **Microsoft JDBC Driver for SQL Server** を追加します。

   ```xml
   <dependency>
       <groupId>com.microsoft.sqlserver</groupId>
       <artifactId>mssql-jdbc</artifactId>
       <version>6.2.1.jre8</version>
   </dependency>
   ```

4. さらに ***pom.xml*** で、次のプロパティをプロジェクトに追加します。  properties セクションがない場合は、dependencies の後に追加してください。

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

5. ***pom.xml*** を保存して閉じます。

## <a name="insert-code-to-query-sql-database"></a>SQL Database に照会するコードの挿入

1. Maven プロジェクトに ***App.java*** というファイルがあらかじめ存在している必要があります (..\sqltest\src\main\java\com\sqlsamples\App.java)。

2. このファイルを開き、その内容を次のコードで置き換えます。サーバー、データベース、ユーザー、パスワードには、実際の値を追加してください。

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.DriverManager;

   public class App {

    public static void main(String[] args) {
    
        // Connect to database
           String hostName = "your_server.database.windows.net";
           String dbName = "your_database";
           String user = "your_username";
           String password = "your_password";
           String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
           Connection connection = null;

           try {
                   connection = DriverManager.getConnection(url);
                   String schema = connection.getSchema();
                   System.out.println("Successful connection - Schema: " + schema);

                   System.out.println("Query data example:");
                   System.out.println("=========================================");

                   // Create and execute a SELECT SQL statement.
                   String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName " 
                       + "FROM [SalesLT].[ProductCategory] pc "  
                       + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";
                
                   try (Statement statement = connection.createStatement();
                       ResultSet resultSet = statement.executeQuery(selectSql)) {

                           // Print results from select statement
                           System.out.println("Top 20 categories:");
                           while (resultSet.next())
                           {
                               System.out.println(resultSet.getString(1) + " "
                                   + resultSet.getString(2));
                           }
                    connection.close();
                   }                   
           }
           catch (Exception e) {
                   e.printStackTrace();
           }
       }
   }
   ```

## <a name="run-the-code"></a>コードの実行

1. コマンド プロンプトで、次のコマンドを実行します。

   ```bash
   mvn package
   mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

2. 先頭から 20 行が返されることを確認して、アプリケーション ウィンドウを閉じます。


## <a name="next-steps"></a>次のステップ
- [最初の Azure SQL Database の設計](sql-database-design-first-database.md)
- [SQL Server 用 Microsoft JDBC ドライバー](https://github.com/microsoft/mssql-jdbc)
- [問題の報告/質問](https://github.com/microsoft/mssql-jdbc/issues)


