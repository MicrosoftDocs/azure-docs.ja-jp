---
title: Azure SQL Database で Java と JDBC を使用する
description: Azure SQL Database で Java と JDBC を使用する方法について説明します。
services: sql-database
author: jdubois
ms.author: judubois
ms.service: sql-database
ms.subservice: development
ms.topic: quickstart
ms.devlang: java
ms.date: 06/26/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: badf6b8887c356c2a7fc7308f6aa15f551e4bb67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95972676"
---
# <a name="use-java-and-jdbc-with--azure-sql-database"></a>Azure SQL Database で Java と JDBC を使用する

このトピックでは、Java と [JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) を使って [Azure SQL Database](/azure/sql-database/) で情報を格納および取得するサンプル アプリケーションを作成する方法を説明します。

JDBC は、従来のリレーショナル データベースに接続するための標準の Java API です。

## <a name="prerequisites"></a>前提条件

- Azure アカウント。 所有していない場合は、[無料試用版を入手](https://azure.microsoft.com/free/)してください。
- [Azure Cloud Shell](../../cloud-shell/quickstart.md) または [Azure CLI](/cli/azure/install-azure-cli)。 Azure Cloud Shell をお勧めします。これにより、自動的にログインし、必要なすべてのツールにアクセスできるようになります。
- サポートされている [Java 開発キット](/azure/developer/java/fundamentals/java-jdk-long-term-support)、バージョン 8 (Azure Cloud Shell に含まれます)。
- [Apache Maven](https://maven.apache.org/) ビルド ツール。

## <a name="prepare-the-working-environment"></a>作業環境を準備する

入力ミスを少なくし、特定のニーズに応じて以下の構成をカスタマイズしやすくするために、ここでは環境変数を使用します。

次のコマンドを使用して環境変数を設定します。

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_SQL_SERVER_USERNAME=demo
AZ_SQL_SERVER_PASSWORD=<YOUR_AZURE_SQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

プレースホルダーは、この記事全体で使用される次の値に置き換えてください。

- `<YOUR_DATABASE_NAME>`:Azure SQL Database サーバーの名前。 Azure 全体で一意である必要があります。
- `<YOUR_AZURE_REGION>`:使用する Azure リージョン。 既定で `eastus` を使用できますが、居住地に近いリージョンを構成することをお勧めします。 「`az account list-locations`」を入力すると、使用可能なリージョンの完全な一覧を表示できます。
- `<AZ_SQL_SERVER_PASSWORD>`:Azure SQL Database サーバーのパスワード。 パスワードは 8 文字以上にする必要があります。 これには、英大文字、英小文字、数字 (0 から 9)、英数字以外の文字 (!、$、#、% など) のうち、3 つのカテゴリの文字が含まれている必要があります。
- `<YOUR_LOCAL_IP_ADDRESS>`:ローカル コンピューターの IP アドレス。そこから、Java アプリケーションを実行します。 これを確認する簡単な方法は、ブラウザーで [whatismyip.akamai.com](http://whatismyip.akamai.com/)にアクセスすることです。

次に、リソース グループを作成するには、次のコマンドを使用します。

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
  	| jq
```

> [!NOTE]
> `jq` ユーティリティを使用し、JSON データを表示し、より読みやすくします。 このユーティリティは既定で [Azure Cloud Shell](https://shell.azure.com/) にインストールされます。 このツールを使用しない場合は、使用するすべてのコマンドの `| jq` の部分を削除しても問題ありません。

## <a name="create-an-azure-sql-database-instance"></a>Azure SQL Database インスタンスを作成する

最初に作成するのは、マネージド Azure SQL Database サーバーです。

> [!NOTE]
> Azure SQL Database サーバーの作成の詳細については、「[クイックスタート:Azure SQL Database の単一データベースを作成する](./single-database-create-quickstart.md)」を参照してください。

[Azure Cloud Shell](https://shell.azure.com/) で次のコマンドを実行します。

```azurecli
az sql server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --admin-user $AZ_SQL_SERVER_USERNAME \
    --admin-password $AZ_SQL_SERVER_PASSWORD \
  	| jq
```

このコマンドにより、Azure SQL Database サーバーが作成されます。

### <a name="configure-a-firewall-rule-for-your-azure-sql-database-server"></a>Azure SQL Database サーバーのファイアウォール規則を構成する

Azure SQL Database インスタンスは、既定でセキュリティ保護されています。 受信接続を一切許可しないファイアウォールがあります。 データベースを使用できるようにするには、データベース サーバーにアクセスするためのローカル IP アドレスを許可するファイアウォール規則を追加する必要があります。

この記事の冒頭でローカル IP アドレスを構成したので、次のコマンドを実行してサーバーのファイアウォールを開くことができます。

```azurecli
az sql server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
  	| jq
```

### <a name="configure-a-azure-sql-database"></a>Azure SQL Database を構成する

先ほど作成した Azure SQL Database サーバーは空です。 Java アプリケーションで使用できるデータベースはありません。 次のコマンドを実行し、`demo` という名前の新しいデータベースを作成します。

```azurecli
az sql db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server $AZ_DATABASE_NAME \
  	| jq
```

### <a name="create-a-new-java-project"></a>新しい Java プロジェクトを作成する

任意の IDE を使用して新しい Java プロジェクトを作成し、そのルート ディレクトリに `pom.xml` ファイルを追加します。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>

    <properties>
        <java.version>1.8</java.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.sqlserver</groupId>
            <artifactId>mssql-jdbc</artifactId>
            <version>7.4.1.jre8</version>
        </dependency>
    </dependencies>
</project>
```

このファイルは、以下を使用するようにプロジェクトを構成する [Apache Maven](https://maven.apache.org/) です。

- Java 8
- 最近の Java 用 SQL Server ドライバー

### <a name="prepare-a-configuration-file-to-connect-to-azure-sql-database"></a>Azure SQL Database に接続するための構成ファイルを準備する

*src/main/resources/application.properties* ファイルを作成して、以下を追加します。

```properties
url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
user=demo@$AZ_DATABASE_NAME
password=$AZ_SQL_SERVER_PASSWORD
```

- 2 つの `$AZ_DATABASE_NAME` 変数を、この記事の冒頭で構成した値に置き換えます。
- `$AZ_SQL_SERVER_PASSWORD` 変数を、この記事の冒頭で構成した値で置き換えます。

### <a name="create-an-sql-file-to-generate-the-database-schema"></a>データベース スキーマを生成するための SQL ファイルを作成する

データベース スキーマを作成するためには、*src/main/resources/`schema.sql`* ファイルを使用します。 このファイルを次の内容で作成します。

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id INT PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BIT);
```

## <a name="code-the-application"></a>アプリケーションをコーディングする

### <a name="connect-to-the-database"></a>データベースに接続する

次に、JDBC を使用して Azure SQL Database のデータを格納および取得する Java コードを追加します。

次のコードを含んだ *src/main/java/DemoApplication.java* ファイルを作成します。

```java
package com.example.demo;

import java.sql.*;
import java.util.*;
import java.util.logging.Logger;

public class DemoApplication {

    private static final Logger log;

    static {
        System.setProperty("java.util.logging.SimpleFormatter.format", "[%4$-7s] %5$s %n");
        log =Logger.getLogger(DemoApplication.class.getName());
    }

    public static void main(String[] args) throws Exception {
        log.info("Loading application properties");
        Properties properties = new Properties();
        properties.load(DemoApplication.class.getClassLoader().getResourceAsStream("application.properties"));

        log.info("Connecting to the database");
        Connection connection = DriverManager.getConnection(properties.getProperty("url"), properties);
        log.info("Database connection test: " + connection.getCatalog());

        log.info("Create database schema");
        Scanner scanner = new Scanner(DemoApplication.class.getClassLoader().getResourceAsStream("schema.sql"));
        Statement statement = connection.createStatement();
        while (scanner.hasNextLine()) {
            statement.execute(scanner.nextLine());
        }

        /*
        Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
        insertData(todo, connection);
        todo = readData(connection);
        todo.setDetails("congratulations, you have updated data!");
        updateData(todo, connection);
        deleteData(todo, connection);
        */

        log.info("Closing database connection");
        connection.close();
    }
}
```

この Java コードは、SQL Server データベースに接続するため、またデータを格納するスキーマを作成するために、先ほど作成した *application.properties* ファイルと *schema.sql* ファイルを使用します。

このファイルを見るとわかるように、データの挿入、読み取り、更新、削除のためのメソッドがコメント化されています。これらのメソッドのコードは、この記事の中で後から作成します。それぞれのメソッドが完成したら都度、コメント解除することができます。

> [!NOTE]
> データベースの資格情報は、*application.properties* ファイルの *user* プロパティと *password* プロパティに格納されます。 プロパティ ファイルは引数として渡されるため、これらの資格情報は `DriverManager.getConnection(properties.getProperty("url"), properties);` を実行するときに使用されます。

以後、このメイン クラスは、次の任意のツールを使用して実行することができます。

- IDE を使用する場合: *DemoApplication* クラスを右クリックして実行します。
- Maven を使用する場合: `mvn exec:java -Dexec.mainClass="com.example.demo.DemoApplication"` を実行することによってアプリケーションを実行できます。

次のコンソール ログが示すように、このアプリケーションは、Azure SQL Database に接続してデータベース スキーマを作成した後、接続を閉じます。

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Closing database connection 
```

### <a name="create-a-domain-class"></a>ドメイン クラスを作成する

`DemoApplication` クラスの横に新しい `Todo` Java クラスを作成し、以下のコードを追加します。

```java
package com.example.demo;

public class Todo {

    private Long id;
    private String description;
    private String details;
    private boolean done;

    public Todo() {
    }

    public Todo(Long id, String description, String details, boolean done) {
        this.id = id;
        this.description = description;
        this.details = details;
        this.done = done;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }

    @Override
    public String toString() {
        return "Todo{" +
                "id=" + id +
                ", description='" + description + '\'' +
                ", details='" + details + '\'' +
                ", done=" + done +
                '}';
    }
}
```

このクラスは、*schema.sql* スクリプトを実行する際に作成した `todo` テーブルにマップされるドメイン モデルです。

### <a name="insert-data-into-azure-sql-database"></a>Azure SQL Database にデータを挿入する

*src/main/java/DemoApplication.java* ファイルの main メソッドの後に、データベースにデータを挿入するための次のメソッドを追加します。

```java
private static void insertData(Todo todo, Connection connection) throws SQLException {
    log.info("Insert data");
    PreparedStatement insertStatement = connection
            .prepareStatement("INSERT INTO todo (id, description, details, done) VALUES (?, ?, ?, ?);");

    insertStatement.setLong(1, todo.getId());
    insertStatement.setString(2, todo.getDescription());
    insertStatement.setString(3, todo.getDetails());
    insertStatement.setBoolean(4, todo.isDone());
    insertStatement.executeUpdate();
}
```

これで、`main` メソッドの次の 2 つの行のコメントを解除できます。

```java
Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
insertData(todo, connection);
```

メイン クラスを実行すると、次の出力が生成されるはずです。

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Closing database connection
```

### <a name="reading-data-from-azure-sql-database"></a>Azure SQL Database からデータを読み取る

先ほど挿入したデータを読み取って、コードが正しく動作することを確認しましょう。

*src/main/java/DemoApplication.java* ファイルの `insertData` メソッドの後に、データベースからデータを読み取るための次のメソッドを追加します。

```java
private static Todo readData(Connection connection) throws SQLException {
    log.info("Read data");
    PreparedStatement readStatement = connection.prepareStatement("SELECT * FROM todo;");
    ResultSet resultSet = readStatement.executeQuery();
    if (!resultSet.next()) {
        log.info("There is no data in the database!");
        return null;
    }
    Todo todo = new Todo();
    todo.setId(resultSet.getLong("id"));
    todo.setDescription(resultSet.getString("description"));
    todo.setDetails(resultSet.getString("details"));
    todo.setDone(resultSet.getBoolean("done"));
    log.info("Data read from the database: " + todo.toString());
    return todo;
}
```

これで、`main` メソッドの次の行のコメントを解除できます。

```java
todo = readData(connection);
```

メイン クラスを実行すると、次の出力が生成されるはずです。

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Closing database connection 
```

### <a name="updating-data-in-azure-sql-database"></a>Azure SQL Database のデータを更新する

先ほど挿入したデータを更新しましょう。

引き続き *src/main/java/DemoApplication.java* ファイルの `readData` メソッドの後に、データベース内のデータを更新するための次のメソッドを追加します。

```java
private static void updateData(Todo todo, Connection connection) throws SQLException {
    log.info("Update data");
    PreparedStatement updateStatement = connection
            .prepareStatement("UPDATE todo SET description = ?, details = ?, done = ? WHERE id = ?;");

    updateStatement.setString(1, todo.getDescription());
    updateStatement.setString(2, todo.getDetails());
    updateStatement.setBoolean(3, todo.isDone());
    updateStatement.setLong(4, todo.getId());
    updateStatement.executeUpdate();
    readData(connection);
}
```

これで、`main` メソッドの次の 2 つの行のコメントを解除できます。

```java
todo.setDetails("congratulations, you have updated data!");
updateData(todo, connection);
```

メイン クラスを実行すると、次の出力が生成されるはずです。

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Update data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true} 
[INFO   ] Closing database connection 
```

### <a name="deleting-data-in-azure-sql-database"></a>Azure SQL Database からデータを削除する

最後に、先ほど挿入したデータを削除しましょう。

引き続き *src/main/java/DemoApplication.java* ファイルの `updateData` メソッドの後に、データベース内のデータを削除するための次のメソッドを追加します。

```java
private static void deleteData(Todo todo, Connection connection) throws SQLException {
    log.info("Delete data");
    PreparedStatement deleteStatement = connection.prepareStatement("DELETE FROM todo WHERE id = ?;");
    deleteStatement.setLong(1, todo.getId());
    deleteStatement.executeUpdate();
    readData(connection);
}
```

これで、`main` メソッドの次の行のコメントを解除できます。

```java
deleteData(todo, connection);
```

メイン クラスを実行すると、次の出力が生成されるはずです。

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Update data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true} 
[INFO   ] Delete data 
[INFO   ] Read data 
[INFO   ] There is no data in the database! 
[INFO   ] Closing database connection 
```

## <a name="conclusion-and-resources-clean-up"></a>結論とリソースのクリーンアップ

お疲れさまでした。 JDBC を使用して、Azure SQL Database でデータを格納および取得する Java アプリケーションを作成しました。

このクイックスタートで使用したすべてのリソースをクリーンアップするには、次のコマンドを使用してリソース グループを削除します。

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>次のステップ

- [Azure SQL Database で最初のデータベースを設計する](design-first-database-tutorial.md)  
- [SQL Server 用 Microsoft JDBC ドライバー](https://github.com/microsoft/mssql-jdbc)  
- [問題の報告/質問](https://github.com/microsoft/mssql-jdbc/issues)
