---
title: 地理的に分散した Azure SQL データベース ソリューションを実装する | Microsoft Docs
description: Azure SQL データベースとアプリケーションをレプリケートされたデータベースにフェールオーバーするための構成方法と、フェールオーバーをテストする方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: a5be3efa5544e47f40ab9f0a31f6658b134977e2
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444519"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>チュートリアル:地理的に分散したデータベースの実装

Azure SQL データベースとアプリケーションをリモート リージョンにフェールオーバーするよう構成し、フェールオーバー計画をテストします。 学習内容は次のとおりです。

> [!div class="checklist"]
> - [フェールオーバー グループ](sql-database-auto-failover-group.md)を作成します。
> - Azure SQL データベースに照会する Java アプリケーションを実行する
> - [テスト フェールオーバー]

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

このチュートリアルに取り組む前に、次のものがインストールされていることを確認してください。

- [Azure PowerShell](/powershell/azureps-cmdlets-docs)
- Azure SQL Database 内の単一データベース。 次の方法で作成します。
  - [ポータル](sql-database-single-database-get-started.md)
  - [CLI](sql-database-cli-samples.md)
  - [PowerShell](sql-database-powershell-samples.md)

  > [!NOTE]
  > このチュートリアルでは、*AdventureWorksLT* サンプル データベースを使用します。

- Java および Maven。「[Build an app using SQL Server (SQL Server を使用したアプリの作成)](https://www.microsoft.com/sql-server/developer-get-started/)」にアクセスして **[Java]** を選択し、ご使用の環境を選択して、表示される手順に従ってください。

> [!IMPORTANT]
> このチュートリアルの手順を実行しているコンピューターのパブリック IP アドレスを使用するようにファイアウォール規則を確実に設定してください。 データベース レベルのファイアウォール ルールは、セカンダリ サーバーに自動的にレプリケートされます。
>
> 詳細については、[データベース レベルのファイアウォール規則の作成](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)に関するページを参照してください。ご利用のコンピューターのサーバー レベルのファイアウォール規則に使用する IP アドレスを調べる場合は、[サーバーレベルのファイアウォールの作成](sql-database-server-level-firewall-rule.md)に関するページを参照してください。  

## <a name="create-a-failover-group"></a>フェールオーバー グループを作成する

既存の Azure SQL サーバーと別のリージョンにある新しい Azure SQL サーバーとの間で、Azure PowerShell を使用して[フェールオーバー グループ](sql-database-auto-failover-group.md)を作成します。 その後、そのフェールオーバー グループにサンプル データベースを追加します。

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]

フェールオーバー グループを作成するには、次のスクリプトを実行します。

   ```powershell
    # Set variables for your server and database
    $adminlogin = "<your admin>"
    $password = "<your password>"
    $myresourcegroupname = "<your resource group name>"
    $mylocation = "<your resource group location>"
    $myservername = "<your existing server name>"
    $mydatabasename = "<your database name>"
    $mydrlocation = "<your disaster recovery location>"
    $mydrservername = "<your disaster recovery server name>"
    $myfailovergroupname = "<your globally unique failover group name>"

    # Create a backup server in the failover region
    New-AzSqlServer -ResourceGroupName $myresourcegroupname `
       -ServerName $mydrservername `
       -Location $mydrlocation `
       -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
          -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

    # Create a failover group between the servers
    New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -PartnerServerName $mydrservername  `
       –FailoverGroupName $myfailovergroupname `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2

    # Add the database to the failover group
    Get-AzSqlDatabase `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -DatabaseName $mydatabasename | `
     Add-AzSqlDatabaseToFailoverGroup `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -FailoverGroupName $myfailovergroupname
   ```

geo レプリケーションの設定は、Azure portal でデータベースを選択し、 **[設定]**  >  **[geo レプリケーション]** の順に選択することで変更することもできます。

![geo レプリケーションの設定](./media/sql-database-implement-geo-distributed-database/geo-replication.png)

## <a name="run-the-sample-project"></a>サンプル プロジェクトを実行する

1. コンソールで次のコマンドを実行して Maven プロジェクトを作成します。

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. 「**Y**」と入力して **Enter** キーを押します。

1. 新しいプロジェクトのディレクトリに移動します。

   ```bash
   cd SqlDbSample
   ```

1. お好みのエディターを使用して、プロジェクト フォルダーの *pom.xml* ファイルを開きます。

1. 以下の `dependency` セクションを追加して、Microsoft JDBC Driver for SQL Server 依存関係を追加します。 この依存関係を、上位の `dependencies` セクション内に貼り付けてください。

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. `dependencies` セクションの後に `properties` セクションを追加して Java バージョンを指定します。

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. `properties` セクションの後に `build` セクションを追加してマニフェスト ファイルをサポートします。

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. *pom.xml* ファイルを保存して閉じます。

1. ..\SqlDbSample\src\main\java\com\sqldbsamples にある *App.java* ファイルを開き、その内容を次のコードで置き換えます。

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. *App.java* ファイルを保存して閉じます。

1. コマンド コンソールで、次のコマンドを実行します。

   ```bash
   mvn package
   ```

1. アプリケーションを起動します。手動で停止しない限り約 1 時間実行されるので、その間にフェールオーバー テストを実行できます。

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>[テスト フェールオーバー]

以下に示した各スクリプトを実行してフェールオーバーをシミュレートし、アプリケーションの結果を観察します。 データベースの移行中、いくつかの挿入と選択が失敗します。そのようすに注目してください。

次のコマンドを使用して、テスト中にディザスター リカバリー サーバーのロールを確認することもできます。

   ```powershell
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername).ReplicationRole
   ```

フェールオーバーをテストするには、次の手順に従います。

1. フェールオーバー グループの手動フェールオーバーを開始します。

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -FailoverGroupName $myfailovergroupname
   ```

1. フェールオーバー グループを再びプライマリ サーバーに戻します。

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   ```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure SQL データベースとアプリケーションをリモート リージョンにフェールオーバーするよう構成し、フェールオーバー計画をテストしました。 以下の方法について学習しました。

> [!div class="checklist"]
> - geo レプリケーション フェールオーバー グループを作成する
> - Azure SQL データベースに照会する Java アプリケーションを実行する
> - [テスト フェールオーバー]

DMS を使用した移行方法について取り上げた次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [DMS を使用して SQL Server を Azure SQL Database Managed Instance に移行する](../dms/tutorial-sql-server-to-managed-instance.md)
