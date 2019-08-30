---
title: Java を使用して Azure Database for MySQL に接続する
description: このクイック スタートでは、Azure Database for MySQL データベースに接続してデータを照会するために使用できる、Java コード サンプルを紹介します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019
ms.topic: quickstart
ms.devlang: java
ms.date: 08/08/2019
ms.openlocfilehash: b1771d7940a12d0cc818c223c28dcad87acd8a0c
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624721"
---
# <a name="quickstart-use-java-to-connect-to-and-query-data-in-azure-database-for-mysql"></a>クイック スタート:Azure Database for MySQL で Java を使用してデータに接続してクエリを実行する

このクイック スタートでは、Java アプリケーションを使用して Azure Database for MySQL および JDBC ドライバー [MariaDB Connector/J](https://mariadb.com/kb/en/library/mariadb-connector-j/) に接続する方法を紹介します。 ここでは、SQL ステートメントを使用してデータベース内のデータを照会、挿入、更新、削除する方法を説明します。 この記事では、Java を使用した開発には慣れているものの、Azure Database for MySQL の使用は初めてであるユーザーを想定しています。

## <a name="prerequisites"></a>前提条件
1. このクイック スタートでは、次のいずれかのガイドで作成されたリソースを出発点として使用します。
   - [Azure Portal を使用した Azure Database for MySQL サーバーの作成 ](./quickstart-create-mysql-server-database-using-azure-portal.md)
   - [Azure CLI を使用した Azure Database for MySQL サーバーの作成 ](./quickstart-create-mysql-server-database-using-azure-cli.md)

2. アプリケーションが正常に接続できるよう、ファイアウォールが開き、SSL 設定が調整された状態で Azure Database for MySQL 接続のセキュリティが構成されていることを確認する。

3. 次のいずれかの方法を使用して、MariaDB Connector/J コネクタを取得します。
   - Maven パッケージ [mariadb-java-client](https://search.maven.org/search?q=a:mariadb-java-client) を使用して [mariadb-java-client dependency](https://mvnrepository.com/artifact/org.mariadb.jdbc/mariadb-java-client) をプロジェクトの POM ファイルに含めます。
   - JDBC ドライバー [MariaDB Connector/J](https://downloads.mariadb.org/connector-java/) をダウンロードし、JDBC jar ファイル (たとえば mariadb-java-client-2.4.3.jar) をアプリケーションのクラス パスに含めます。 クラス パスで問題が発生した場合は、クラス パスの詳細 ([Apache Tomcat](https://tomcat.apache.org/tomcat-7.0-doc/class-loader-howto.html)、[Java SE](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/classpath.html) など) について環境のドキュメントを参照してください。

## <a name="get-connection-information"></a>接続情報の取得
Azure Database for MySQL に接続するために必要な接続情報を取得します。 完全修飾サーバー名とログイン資格情報が必要です。

1. [Azure Portal](https://portal.azure.com/) にログインします。
2. Azure portal の左側のメニューにある **[すべてのリソース]** を選択し、作成したサーバー (例: **mydemoserver**) を検索します。
3. サーバー名を選択します。
4. サーバーの **[概要]** パネルから、 **[サーバー名]** と **[サーバー管理者ログイン名]** を書き留めます。 パスワードを忘れた場合も、このパネルからパスワードをリセットすることができます。
 ![Azure Database for MySQL サーバー名 ](./media/connect-java/1_server-overview-name-login.png)

## <a name="connect-create-table-and-insert-data"></a>接続、テーブルの作成、データの挿入
接続し、**INSERT** SQL ステートメントが含まれた関数を使用してデータを読み込むには、次のコードを使用します。 [getConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) メソッドは MySQL への接続に使用されます。 [createStatement()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#creating-a-table-on-a-mariadb-or-mysql-server) メソッドと execute() メソッドは、テーブルの削除と作成に使用されます。 prepareStatement オブジェクトは、パラメーター値をバインドする setString() および setInt() と共に、挿入コマンドの作成に使用されます。 executeUpdate() メソッドは、パラメーターの各セットに対してコマンドを実行して、値を挿入します。 

host、database、user、password の各パラメーターは、独自のサーバーとデータベースの作成時に指定した値に置き換えてください。

```java
import java.sql.*;
import java.util.Properties;

public class CreateTableInsertRows {

    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables. 
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        // check that the driver is installed
        try
        {
            Class.forName("org.mariadb.jdbc");
        }
        catch (ClassNotFoundException e)
        {
            throw new ClassNotFoundException("MariaDB JDBC driver NOT detected in library path.", e);
        }

        System.out.println("MariaDB JDBC driver detected in library path.");

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);

            // Set connection properties.
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");

            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database.", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Drop previous table of same name if one exists.
                Statement statement = connection.createStatement();
                statement.execute("DROP TABLE IF EXISTS inventory;");
                System.out.println("Finished dropping table (if existed).");
    
                // Create table.
                statement.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);");
                System.out.println("Created table.");
                
                // Insert some data into table.
                int nRowsInserted = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("INSERT INTO inventory (name, quantity) VALUES (?, ?);");
                preparedStatement.setString(1, "banana");
                preparedStatement.setInt(2, 150);
                nRowsInserted += preparedStatement.executeUpdate();

                preparedStatement.setString(1, "orange");
                preparedStatement.setInt(2, 154);
                nRowsInserted += preparedStatement.executeUpdate();

                preparedStatement.setString(1, "apple");
                preparedStatement.setInt(2, 100);
                nRowsInserted += preparedStatement.executeUpdate();
                System.out.println(String.format("Inserted %d row(s) of data.", nRowsInserted));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
    
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}

```

## <a name="read-data"></a>データの読み取り
**SELECT** SQL ステートメントを使用してデータを読み取るには、次のコードを使用します。 [getConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) メソッドは MySQL への接続に使用されます。 [createStatement()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#creating-a-table-on-a-mariadb-or-mysql-server) メソッドと executeQuery() メソッドは、接続と SELECT ステートメントの実行に使用されます。 結果は、ResultSet オブジェクトを使用して処理されます。 

host、database、user、password の各パラメーターは、独自のサーバーとデータベースの作成時に指定した値に置き換えてください。

```java
import java.sql.*;
import java.util.Properties;

public class ReadTable {

    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables.
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        // check that the driver is installed
        try
        {
            Class.forName("org.mariadb.jdbc");
        }
        catch (ClassNotFoundException e)
        {
            throw new ClassNotFoundException("MariaDB JDBC driver NOT detected in library path.", e);
        }

        System.out.println("MariaDB JDBC driver detected in library path.");

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);

            // Set connection properties.
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");
            
            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
    
                Statement statement = connection.createStatement();
                ResultSet results = statement.executeQuery("SELECT * from inventory;");
                while (results.next())
                {
                    String outputString = 
                        String.format(
                            "Data row = (%s, %s, %s)",
                            results.getString(1),
                            results.getString(2),
                            results.getString(3));
                    System.out.println(outputString);
                }
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database."); 
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="update-data"></a>データの更新
**UPDATE** SQL ステートメントを使用してデータを変更するには、次のコードを使用します。 [getConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) メソッドは MySQL への接続に使用されます。 [prepareStatement()](https://docs.oracle.com/javase/tutorial/jdbc/basics/prepared.html) メソッドと executeUpdate() メソッドは、UPDATE ステートメントの準備と実行に使用されます。 

host、database、user、password の各パラメーターは、独自のサーバーとデータベースの作成時に指定した値に置き換えてください。

```java
import java.sql.*;
import java.util.Properties;

public class UpdateTable {
    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables. 
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        // check that the driver is installed
        try
        {
            Class.forName("org.mariadb.jdbc");
        }
        catch (ClassNotFoundException e)
        {
            throw new ClassNotFoundException("MariaDB JDBC driver NOT detected in library path.", e);
        }

        System.out.println("MariaDB JDBC driver detected in library path.");

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);
            
            // set up the connection properties
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");

            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database.", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Modify some data in table.
                int nRowsUpdated = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("UPDATE inventory SET quantity = ? WHERE name = ?;");
                preparedStatement.setInt(1, 200);
                preparedStatement.setString(2, "banana");
                nRowsUpdated += preparedStatement.executeUpdate();
                System.out.println(String.format("Updated %d row(s) of data.", nRowsUpdated));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="delete-data"></a>データの削除
**DELETE** SQL ステートメントを使用してデータを削除するには、次のコードを使用します。 [getConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) メソッドは MySQL への接続に使用されます。  [prepareStatement()](https://docs.oracle.com/javase/tutorial/jdbc/basics/prepared.html) メソッドと executeUpdate() メソッドは、delete ステートメントの準備と実行に使用されます。 

host、database、user、password の各パラメーターは、独自のサーバーとデータベースの作成時に指定した値に置き換えてください。

```java
import java.sql.*;
import java.util.Properties;

public class DeleteTable {
    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables.
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";
        
        // check that the driver is installed
        try
        {
            Class.forName("org.mariadb.jdbc");
        }
        catch (ClassNotFoundException e)
        {
            throw new ClassNotFoundException("MariaDB JDBC driver NOT detected in library path.", e);
        }

        System.out.println("MariaDB JDBC driver detected in library path.");

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);
            
            // set up the connection properties
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");
            
            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Delete some data from table.
                int nRowsDeleted = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("DELETE FROM inventory WHERE name = ?;");
                preparedStatement.setString(1, "orange");
                nRowsDeleted += preparedStatement.executeUpdate();
                System.out.println(String.format("Deleted %d row(s) of data.", nRowsDeleted));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ ダンプと復元を使用した Azure Database for MySQL への MySQL データベースの移行 ](concepts-migrate-dump-restore.md)
