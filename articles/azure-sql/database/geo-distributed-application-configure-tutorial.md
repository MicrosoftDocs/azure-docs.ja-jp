---
title: geo 分散型ソリューションを実装する
description: Azure SQL Database 内のデータベースおよびクライアント アプリケーションをレプリケートされたデータベースにフェールオーバーするための構成方法と、フェールオーバーをテストする方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 03/12/2019
ms.openlocfilehash: 89d285a56553f5c521d1edbc92786debd4a92e32
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101659292"
---
# <a name="tutorial-implement-a-geo-distributed-database-azure-sql-database"></a>チュートリアル:geo 分散型データベースを実装する (Azure SQL Database)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

SQL Database 内のデータベースおよびクライアント アプリケーションをリモート リージョンにフェールオーバーするよう構成し、フェールオーバー計画をテストします。 学習内容は次のとおりです。

> [!div class="checklist"]
>
> - [フェールオーバー グループ](auto-failover-group-overview.md)を作成します。
> - SQL Database 内のデータベースを照会する Java アプリケーションを実行する
> - [テスト フェールオーバー]

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

このチュートリアルに取り組む前に、次のものがインストールされていることを確認してください。

- [Azure PowerShell](/powershell/azure/)
- Azure SQL Database 内の単一データベース。 次の方法で作成します。
  - [Azure Portal](single-database-create-quickstart.md)
  - [Azure CLI](az-cli-script-samples-content-guide.md)
  - [PowerShell](powershell-script-content-guide.md)

  > [!NOTE]
  > このチュートリアルでは、*AdventureWorksLT* サンプル データベースを使用します。

- Java および Maven。「[Build an app using SQL Server (SQL Server を使用したアプリの作成)](https://www.microsoft.com/sql-server/developer-get-started/)」にアクセスして **[Java]** を選択し、ご使用の環境を選択して、表示される手順に従ってください。

> [!IMPORTANT]
> このチュートリアルの手順を実行しているコンピューターのパブリック IP アドレスを使用するようにファイアウォール規則を確実に設定してください。 データベース レベルのファイアウォール ルールは、セカンダリ サーバーに自動的にレプリケートされます。
>
> 詳細については、[データベース レベルのファイアウォール規則の作成](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)に関するページを参照してください。ご利用のコンピューターのサーバー レベルのファイアウォール規則に使用する IP アドレスを調べる場合は、[サーバーレベルのファイアウォールの作成](firewall-create-server-level-portal-quickstart.md)に関するページを参照してください。  

## <a name="create-a-failover-group"></a>フェールオーバー グループを作成する

既存のサーバーと別のリージョンにある新しいサーバーとの間で、Azure PowerShell を使用して[フェールオーバー グループ](auto-failover-group-overview.md)を作成します。 その後、そのフェールオーバー グループにサンプル データベースを追加します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

フェールオーバー グループを作成するには、次のスクリプトを実行します。

```powershell
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>"
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
New-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $drServer `
    -Location $drLocation -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
    -ArgumentList $admin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# create a failover group between the servers
New-AzSqlDatabaseFailoverGroup –ResourceGroupName $resourceGroup -ServerName $server `
    -PartnerServerName $drServer –FailoverGroupName $failoverGroup –FailoverPolicy Automatic -GracePeriodWithDataLossHours 2

# add the database to the failover group
Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $server -DatabaseName $database | `
    Add-AzSqlDatabaseToFailoverGroup -ResourceGroupName $resourceGroup -ServerName $server -FailoverGroupName $failoverGroup
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!IMPORTANT]
> `az login` を実行して Azure にサインインします。

```azurecli
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>" # must be different then $location
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
az sql server create --admin-password $password --admin-user $admin `
    --name $drServer --resource-group $resourceGroup --location $drLocation

# create a failover group between the servers
az sql failover-group create --name $failoverGroup --partner-server $drServer `
    --resource-group $resourceGroup --server $server --add-db $database `
    --failover-policy Automatic --grace-period 2
```

* * *

geo レプリケーションの設定は、Azure portal でデータベースを選択し、 **[設定]**  >  **[geo レプリケーション]** の順に選択することで変更することもできます。

![geo レプリケーションの設定](./media/geo-distributed-application-configure-tutorial/geo-replication.png)

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
                   (insertData((highWaterMark + i)) ? "successful" : "failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i)) ? "successful" : "failed") + "\n");
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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

次のコマンドを使用して、テスト中にディザスター リカバリー サーバーのロールを確認することができます。

```powershell
(Get-AzSqlDatabaseFailoverGroup -FailoverGroupName $failoverGroup `
    -ResourceGroupName $resourceGroup -ServerName $drServer).ReplicationRole
```

フェールオーバーをテストするには、次の手順に従います。

1. フェールオーバー グループの手動フェールオーバーを開始します。

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $drServer -FailoverGroupName $failoverGroup
   ```

1. フェールオーバー グループを再びプライマリ サーバーに戻します。

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $server -FailoverGroupName $failoverGroup
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

次のコマンドを使用して、テスト中にディザスター リカバリー サーバーのロールを確認することができます。

```azurecli
az sql failover-group show --name $failoverGroup --resource-group $resourceGroup --server $drServer
```

フェールオーバーをテストするには、次の手順に従います。

1. フェールオーバー グループの手動フェールオーバーを開始します。

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $drServer
   ```

1. フェールオーバー グループを再びプライマリ サーバーに戻します。

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   ```

* * *

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure SQL Database 内のデータベースとアプリケーションをリモート リージョンにフェールオーバーするように構成し、フェールオーバー計画をテストしました。 以下の方法を学習しました。

> [!div class="checklist"]
>
> - geo レプリケーション フェールオーバー グループを作成する
> - SQL Database 内のデータベースを照会する Java アプリケーションを実行する
> - [テスト フェールオーバー]

Azure SQL Managed Instance のインスタンスをフェールオーバー グループに追加する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure SQL Managed Instance のインスタンスをフェールオーバー グループに追加する](../managed-instance/failover-group-add-instance-tutorial.md)
