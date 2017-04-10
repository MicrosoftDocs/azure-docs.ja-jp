---
title: "Java を使用して Azure SQL Database に接続する | Microsoft Docs"
description: "Azure SQL Database への接続に使用できる Java コード サンプルについて説明します。"
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/27/2017
ms.author: andrela;carlrab;sstein
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: a047d4cdf869fff0d2afaf11f124370c0eae98e4
ms.lasthandoff: 03/30/2017


---
# <a name="azure-sql-database-use-java-to-connect-and-query-data"></a>Azure SQL Database: Java を使って接続とデータの照会を行う

[Java](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) を使用して Azure SQL Database に接続し、クエリを実行します。 このガイドでは、Java を使用して Azure SQL Database に接続し、照会、挿入、更新、削除の各ステートメントを実行する方法について詳しく説明します。

このクイック スタートでは、次のクイック スタートで作成されたリソースが出発点として使用されます。

- [DB の作成 - ポータル](sql-database-get-started-portal.md)
- [DB の作成 - CLI](sql-database-get-started-cli.md)

## <a name="configure-development-environment"></a>開発環境の設定

次のセクションでは、既存の Mac OS、Linux (Ubuntu)、および Windows 開発環境で Azure SQL Database を使用するように設定する方法について説明します。

### <a name="mac-os"></a>**Mac OS**
ターミナルを開き、Java プロジェクトの作成先となるディレクトリに移動します。 次のコマンドを入力して、**brew** と **Maven** をインストールします。 

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
ターミナルを開き、Java プロジェクトの作成先となるディレクトリに移動します。 次のコマンドを入力して、**Maven** をインストールします。 

```
sudo apt-get install maven
```

### <a name="windows"></a>**Windows**
公式インストーラーを使用して [Maven](https://maven.apache.org/download.cgi) をインストールします。  

## <a name="get-connection-information"></a>接続情報の取得

Azure Portal で接続文字列を取得します。 その接続文字列は Azure SQL データベースに接続するために使用します。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. 左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。 
3. データベースの **[要点]** ウィンドウで、完全修飾サーバー名を確認します。 

    <img src="./media/sql-database-connect-query-dotnet/server-name.png" alt="server name" style="width: 780px;" />

4. **[データベース接続文字列の表示]** をクリックします。

5. 完全な **JDBC** 接続文字列を確認します。

    <img src="./media/sql-database-connect-query-jdbc/jdbc-connection-string.png" alt="JDBC connection string" style="width: 780px;" />

### <a name="create-maven-project"></a>**Maven プロジェクトの作成**
ターミナルから新しい Maven プロジェクトを作成します。 
```
mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
```

**Microsoft JDBC Driver for SQL Server** を ***pom.xml*** 内の依存関係に追加します。 

```xml
<dependency>
    <groupId>com.microsoft.sqlserver</groupId>
    <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
</dependency>
```

## <a name="select-data"></a>データの選択

[接続](https://docs.microsoft.com/sql/connect/jdbc/working-with-a-connection)と [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL ステートメントを使用し、Java を使って Azure SQL Database のデータを照会します。

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
        String hostName = "yourserver";
        String dbName = "yourdatabase";
        String user = "yourusername";
        String password = "yourpassword";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
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
                Statement statement = connection.createStatement();
                ResultSet resultSet = statement.executeQuery(selectSql);

                // Print results from select statement
                System.out.println("Top 20 categories:");
                while (resultSet.next())
                {
                    System.out.println(resultSet.getString(1) + " "
                        + resultSet.getString(2));
                }
            }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

## <a name="insert-data"></a>データを挿入する

[準備されたステートメント](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql)と [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL ステートメントを使用して、Azure SQL Database にデータを挿入します。

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "yourserver";
        String dbName = "yourdatabase";
        String user = "yourusername";
        String password = "yourpassword";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Insert data example:");
                System.out.println("=========================================");

                // Prepared statement to insert data
                String insertSql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, )" 
                    + " StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

                java.util.Date date = new java.util.Date();
                java.sql.Timestamp sqlTimeStamp = new java.sql.Timestamp(date.getTime());

                PreparedStatement prep = connection.prepareStatement(insertSql);
                prep.setString(1, "BrandNewProduct");
                prep.setInt(2, 200989);
                prep.setString(3, "Blue");
                prep.setDouble(4, 75);
                prep.setDouble(5, 80);
                prep.setTimestamp(6, sqlTimeStamp);

                int count = prep.executeUpdate();
                System.out.println("Inserted: " + count + " row(s)");
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```
## <a name="update-data"></a>データの更新

[準備されたステートメント](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql)と [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL ステートメントを使用して、Azure SQL Database のデータを更新します。

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "yourserver";
        String dbName = "yourdatabase";
        String user = "yourusername";
        String password = "yourpassword";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Update data example:");
                System.out.println("=========================================");

                // Prepared statement to update data
                String updateSql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?";

                PreparedStatement prep = connection.prepareStatement(updateSql);
                prep.setString(1, "500");
                prep.setString(2, "BrandNewProduct");

                int count = prep.executeUpdate();
                System.out.println("Updated: " + count + " row(s)")
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```



## <a name="delete-data"></a>データの削除

[準備されたステートメント](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql)と [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL ステートメントを使用して、Azure SQL Database のデータを削除します。

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "yourserver";
        String dbName = "yourdatabase";
        String user = "yourusername";
        String password = "yourpassword";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Delete data example:");
                System.out.println("=========================================");

                // Prepared statement to delete data
                String deleteSql = "DELETE SalesLT.Product WHERE Name = ?";

                PreparedStatement prep = connection.prepareStatement(deleteSql);
                prep.setString(1, "BrandNewProduct");

                int count = prep.executeUpdate();
                System.out.println("Deleted: " + count + " row(s)");
            }        
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

## <a name="next-steps"></a>次のステップ
* [SQL Database の開発の概要](sql-database-develop-overview.md)の確認。
* GitHub リポジトリの [SQL Server 用 Microsoft JDBC ドライバー](https://github.com/microsoft/mssql-jdbc)。
* [問題/質問を登録します](https://github.com/microsoft/mssql-jdbc/issues)。
* [SQL Database の機能](https://azure.microsoft.com/services/sql-database/)すべてを確認します。


