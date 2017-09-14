---
title: "地理的に分散した Azure SQL Database ソリューションを実装する | Microsoft Docs"
description: "Azure SQL Database とアプリケーションをレプリケートされたデータベースにフェールオーバーするための構成方法と、フェールオーバーをテストする方法について説明します。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,business continuity
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/26/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 9f53f318e20dac9248906bdbe898ba4dacb286ac
ms.contentlocale: ja-jp
ms.lasthandoff: 06/30/2017

---

# <a name="implement-a-geo-distributed-database"></a>地理的に分散したデータベースの実装

このチュートリアルでは、Azure SQL Database とアプリケーションをリモート リージョンのフェールオーバーのために構成し、その後フェールオーバー計画をテストします。 学習内容は次のとおりです。 

> [!div class="checklist"]
> * データベース ユーザーを作成し、アクセス許可を付与する
> * データベースレベルのファイアウォール規則を設定する
> * [geo レプリケーション フェールオーバー グループ](sql-database-geo-replication-overview.md)を作成する
> * Java アプリケーションを作成してコンパイルし、Azure SQL Database に対してクエリを実行する
> * ディザスター リカバリー訓練を実施する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

- 最新の [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs) がインストールされていること。 
- Azure SQL Database がインストールされていること。 このチュートリアルは、次のクイック スタートのいずれかから **mySampleDatabase** という名前の AdventureWorksLT サンプル データベースを使用します。

   - [DB の作成 - ポータル](sql-database-get-started-portal.md)
   - [DB の作成 - CLI](sql-database-get-started-cli.md)
   - [DB の作成 - PowerShell](sql-database-get-started-powershell.md)

- データベースに対して SQL スクリプトを実行する方法を指定したので、次のクエリ ツールのいずれかを使用することができます。
   - [Azure Portal](https://portal.azure.com) のクエリ エディター。 Azure Portal でクエリ エディターを使用する方法の詳細については、[クエリ エディターを使用した接続とクエリ実行](sql-database-get-started-portal.md#query-the-sql-database)に関するドキュメントを参照してください。
   - 最新バージョンの [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。SQL Server から SQL Database まで、Microsoft Windows を対象としたあらゆる SQL インフラストラクチャを管理するための統合環境です。
   - 最新バージョンの [Visual Studio Code](https://code.visualstudio.com/docs)。Linux、macOS、Windows で使用できるグラフィカル コード エディターです。Microsoft SQL Server、Azure SQL Database、SQL Data Warehouse のデータを照会するために、[mssql 拡張機能](https://aka.ms/mssql-marketplace)など、各種の拡張機能をサポートしています。 Azure SQL Database でこのツールを使用する方法の詳細については、[VS Code を使用した接続とクエリ実行](sql-database-connect-query-vscode.md)に関するドキュメントを参照してください。 

## <a name="create-database-users-and-grant-permissions"></a>データベース ユーザーを作成し、アクセス許可を付与する

次のクエリ ツールのいずれかを使用して、データベースに接続し、ユーザー アカウントを作成します。

- Azure Portal のクエリ エディター
- SQL Server Management Studio
- Visual Studio Code

これらのユーザー アカウントはセカンダリ サーバーに自動的にレプリケートされます (同期が維持されます)。 SQL Server Management Studio または Visual Studio Code を使用するには、まだファイアウォールを構成していない IP アドレスのクライアントから接続している場合、ファイアウォール規則を作成する必要がある場合があります。 詳細な手順については、「[サーバーレベルのファイアウォール規則を作成する](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)」を参照してください。

- クエリ ウィンドウで次のクエリを実行し、データベース内に 2 つのユーザー アカウントを作成します。 このスクリプトによって **app_admin** アカウントに **db_owner** のアクセス許可が付与され、**app_user** アカウントに **SELECT** および **UPDATE** のアクセス許可が付与されます。 

   ```sql
   CREATE USER app_admin WITH PASSWORD = 'ChangeYourPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'ChangeYourPassword1';
   --grant permission to SalesLT schema
   GRANT SELECT, INSERT, DELETE, UPDATE ON SalesLT.Product TO app_user;
   ```

## <a name="create-database-level-firewall"></a>データベース レベルのファイアウォールを作成する

SQL Database に[データベースレベルのファイアウォール規則](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)を作成します。 このデータベース レベルのファイアウォール規則は、このチュートリアルで作成するセカンダリ サーバーに自動的にレプリケートされます。 (このチュートリアルでは) 簡略化のために、このチュートリアルの手順を実行しているコンピューターのパブリック IP アドレスを使用します。 現在のコンピューターのファイアウォール規則に使用する IP アドレスを確認するには、「[サーバーレベルのファイアウォール規則を作成する](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)」を参照してください。  

- 開かれたクエリ ウィンドウで、前のクエリを次のクエリと置き換えます。IP アドレスは自分の環境の適切な IP アドレスに置き換えます。  

   ```sql
   -- Create database-level firewall setting for your public IP address
   EXECUTE sp_set_database_firewall_rule @name = N'myGeoReplicationFirewallRule',@start_ip_address = '0.0.0.0', @end_ip_address = '0.0.0.0';
   ```

## <a name="create-an-active-geo-replication-auto-failover-group"></a>アクティブ geo レプリケーション自動フェールオーバー グループを作成する 

Azure PowerShell を使用して、Azure リージョンの既存の Azure SQL Server および新しい空の Azure SQL Server 間に[アクティブ geo レプリケーション自動フェールオーバー グループ](sql-database-geo-replication-overview.md)を作成してから、フェイルオーバー グループにサンプル データベースを追加します。

> [!IMPORTANT]
> これらのコマンドレットでは、Azure PowerShell 4.0 が必要です。 [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]
>

1. 既存のサーバーおよびサンプル データベースの値を使用して PowerShell スクリプトの変数を設定し、フェールオーバー グループ名にグローバルに一意の値を指定します。

   ```powershell
   $adminlogin = "ServerAdmin"
   $password = "ChangeYourAdminPassword1"
   $myresourcegroupname = "<your resource group name>"
   $mylocation = "<your resource group location>"
   $myservername = "<your existing server name>"
   $mydatabasename = "mySampleDatabase"
   $mydrlocation = "<your disaster recovery location>"
   $mydrservername = "<your disaster recovery server name>"
   $myfailovergroupname = "<your unique failover group name>"
   ```

2. フェールオーバー リージョンに空のバックアップ サーバーを作成します。

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -Location $mydrlocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $mydrserver   
   ```

3. 2 つのサーバー間にフェールオーバー グループを作成します。

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup `
      –ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -PartnerServerName $mydrservername  `
      –FailoverGroupName $myfailovergroupname `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $myfailovergroup   
   ```

4. データベースをフェールオーバー グループに追加します。

   ```powershell
   $myfailovergroup = Get-AzureRmSqlDatabase `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -DatabaseName $mydatabasename | `
    Add-AzureRmSqlDatabaseToFailoverGroup `
      -ResourceGroupName $myresourcegroupname ` `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   $myfailovergroup   
   ```

## <a name="install-java-software"></a>Java ソフトウェアのインストール

このセクションの手順では、Java による開発には慣れていて、Azure SQL Database は初めて使用するユーザーを想定しています。 

### <a name="mac-os"></a>**Mac OS**
ターミナルを開き、Java プロジェクトの作成先となるディレクトリに移動します。 次のコマンドを入力して、**brew** と **Maven** をインストールします。 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

Java および Maven 環境をインストールして構成する方法に関する詳細なガイダンスについては、「[Build an app using SQL Server (SQL Server を使用してアプリを構築する)](https://www.microsoft.com/sql-server/developer-get-started/)」にアクセスし、**[Java]**、**[MacOS]** の順に選択してから、手順 1.2 と 1.3 の Java および Maven を構成する詳細手順に従います。

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
ターミナルを開き、Java プロジェクトの作成先となるディレクトリに移動します。 次のコマンドを入力して、**Maven** をインストールします。

```bash
sudo apt-get install maven
```

Java および Maven 環境をインストールして構成する方法に関する詳細なガイダンスについては、「[Build an app using SQL Server (SQL Server を使用してアプリを構築する)](https://www.microsoft.com/sql-server/developer-get-started/)」にアクセスし、**[Java]**、**[Ubuntu]** の順に選択してから、手順 1.2、1.3、1.4 の Java および Maven を構成する詳細手順に従います。

### <a name="windows"></a>**Windows**
公式インストーラーを使用して [Maven](https://maven.apache.org/download.cgi) をインストールします。 Maven を使用して、Java プロジェクトの依存関係の管理、ビルド、テスト、および実行を行うことができます。 Java および Maven 環境をインストールして構成する方法に関する詳細なガイダンスについては、「[Build an app using SQL Server (SQL Server を使用してアプリを構築する)](https://www.microsoft.com/sql-server/developer-get-started/)」にアクセスし、**[Java]**、[Windows] の順に選択してから、手順 1.2 と 1.3 の Java および Maven を構成する詳細手順に従います。

## <a name="create-sqldbsample-project"></a>SqlDbSample プロジェクトを作成する

1. コマンド コンソール (Bash など) で、Maven プロジェクトを作成します。 
   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```
2. 「**Y**」と入力して **Enter** キーを押します。
3. 新しく作成されたプロジェクトにディレクトリを変更します。

   ```bash
   cd SqlDbSamples
   ```

4. お好みのエディターを使用して、プロジェクト フォルダーの pom.xml ファイルを開きます。 

5. 好みのテキスト エディターを開いて次の行を pom.xml ファイルにコピーして貼り付け、Microsoft JDBC Driver for SQL Server の依存関係を Maven プロジェクトに追加します。 ファイル内であらかじめ設定されている既存の値を上書きしないでください。 JDBC の依存関係は、より大きな "dependency" セクションに張り付ける必要があります。   

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

6. pom.xml ファイルの "dependency" セクションの後に次の "properties" セクションを追加して、プロジェクトをコンパイルする Java のバージョンを指定します。 

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```
7. pom.xml ファイルの "properties" セクションの後に次の "build" セクションを追加して、jar のマニフェスト ファイルをサポートします。       

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
8. pom.xml ファイルを保存して閉じます。
9. App.java ファイル (C:\apache-maven-3.5.0\SqlDbSample\src\main\java\com\sqldbsamples\App.java) を開き、その内容を次の内容で置き換えます。 フェールオーバー グループ名をお使いのフェールオーバー グループの名前で置き換えます。 データベース名、ユーザー、またはパスワードの値を変更した場合は、それらの値も変更します。

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

      private static final String FAILOVER_GROUP_NAME = "myfailovergroupname";
  
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "app_user";
      private static final String PASSWORD = "ChangeYourPassword1";

      private static final String READ_WRITE_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println(""); 

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " + (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " + (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name that we can use to find the product again later
      String sql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

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
      // Query the data that was previously inserted into the primary database from the geo replicated database
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
      // Query the high water mark id that is stored in the table to be able to make unique inserts 
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
6. App.java ファイルを保存して閉じます。

## <a name="compile-and-run-the-sqldbsample-project"></a>SqlDbSample プロジェクトをコンパイルして実行する

1. コマンド コンソールで、次のコマンドを実行します。

   ```bash
   mvn package
   ```
2. 終了したら、次のコマンドを実行してアプリケーションを実行します (手動で停止しない限り、約 1 時間実行されます)。

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ```

## <a name="perform-disaster-recovery-drill"></a>ディザスター リカバリー訓練を実施する

1. フェールオーバー グループの手動フェールオーバーを呼び出します。 

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $mydrservername `
   -FailoverGroupName $myfailovergroupname
   ```

2. フェールオーバー時にアプリケーションの結果を確認します。 DNS キャッシュの更新中に、一部の挿入が失敗します。     

3. ディザスター リカバリー サーバーが実行している役割を確認します。

   ```powershell
   $mydrserver.ReplicationRole
   ```

4. フェールバックします。

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $myservername `
   -FailoverGroupName $myfailovergroupname
   ```

5. フェールバック時にアプリケーションの結果を確認します。 DNS キャッシュの更新中に、一部の挿入が失敗します。     

6. ディザスター リカバリー サーバーが実行している役割を確認します。

   ```powershell
   $fileovergroup = Get-AzureRMSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername
   $fileovergroup.ReplicationRole
   ```

## <a name="next-steps"></a>次のステップ

詳細については、[フェールオーバー グループとアクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関するページをご覧ください。

