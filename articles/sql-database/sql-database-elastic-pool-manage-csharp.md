---
title: "C#: Azure SQL Database のエラスティック プールの作成と管理 | Microsoft Docs"
description: "C# データベース開発技術を使用して Azure SQL Database エラスティック プールを管理します。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: f6e6ff3b-6b60-43c1-afe9-575991e38237
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: csharp
ms.workload: data-management
ms.date: 10/04/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: c83a5aacca6669c4259365fba9f444ecb1b2a6c9
ms.openlocfilehash: 1e7393537f6c9e4d6af522dd05f149403a85559e
ms.lasthandoff: 03/01/2017


---
# <a name="create-and-manage-an-elastic-pool-with-cx23"></a>C&#x23; を使用したエラスティック プールの作成と管理

このトピックでは、C＃ を使ってスケーラブルな[エラスティック プール](sql-database-elastic-pool.md)を作成および管理する方法について説明します。 Azure エラスティック プールは、[Azure Portal](https://portal.azure.com/)、[PowerShell](sql-database-elastic-pool-manage-powershell.md)、または REST API を使用して作成および管理することもできます。 また、[Transact-SQL](sql-database-elastic-pool-manage-tsql.md) を使用して、データベースを作成したり、エラスティック プールに出し入れしたりすることもできます。

> [!NOTE]
> SQL Database の新機能の多くは、[Azure Resource Manager デプロイメント モデル](../azure-resource-manager/resource-group-overview.md)を使用している場合にのみサポートされます。そのため、常に最新の **Azure SQL Database Management Library for .NET ([ドキュメント](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))** を使用する必要があります。 以前の[クラシック デプロイメント モデル ベースのライブラリ](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql)は互換性のためだけにサポートされているため、より新しい Resource Manager ベースのライブラリを使用することをお勧めします。
> 

このトピックでは、C# と [Azure SQL Database Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql) を使用して Azure SQL エラスティック プールを作成および管理する方法を説明します。 単一の SQL データベースを作成する場合は、「[C# を使用して SQL Database Library for .NET で SQL Database を作成する](sql-database-get-started-csharp.md)」を参照してください。

Azure SQL Database Library for .NET は、[Resource Manager ベースの SQL Database REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx) をラップする [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) ベースの API を提供します。

> [!NOTE]
> SQL Database の新機能の多くは、[Azure Resource Manager デプロイメント モデル](../azure-resource-manager/resource-group-overview.md)を使用している場合にのみサポートされます。そのため、常に最新の **Azure SQL Database Management Library for .NET ([ドキュメント](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))** を使用する必要があります。 以前の[クラシック デプロイメント モデルのライブラリ](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql)は互換性のためだけにサポートされているため、より新しい Resource Manager ベースのライブラリを使用することをお勧めします。
> 

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、このページの上部にある **無料アカウント** をクリックしてサブスクリプションを作成してから、この記事に戻って最後まで完了してください。
* 見ることができます。 Visual Studio の無償版については、 [Visual Studio のダウンロード](https://www.visualstudio.com/downloads/download-visual-studio-vs) に関するページを参照してください。

## <a name="create-a-console-app-and-install-the-required-libraries"></a>コンソール アプリケーションの作成と必要なライブラリのインストール
1. Visual Studio を起動します。
2. **[ファイル]** > **[新規]** > **[プロジェクト]** の順にクリックします。
3. C# **コンソール アプリケーション** を作成し、 *SqlElasticPoolConsoleApp*

## <a name="create-a-sql-database"></a>SQL Database の作成

C# を使用して SQL データベースを作成するために、( [パッケージ マネージャー コンソール](http://docs.nuget.org/Consume/Package-Manager-Console)を使用して) 必要な管理ライブラリを読み込みます。

1. **[ツール]** > **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順にクリックします。
2. 「 `Install-Package Microsoft.Azure.Management.Sql -Pre` 」と入力して [Microsoft Azure SQL 管理ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)をインストールします。
3. 「 `Install-Package Microsoft.Azure.Management.ResourceManager -Pre` 」と入力して [Microsoft Azure Resource Manager ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)をインストールします。
4. 「 `Install-Package Microsoft.Azure.Common.Authentication -Pre` 」と入力して [Microsoft Azure 一般認証ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication)をインストールします。 

> [!NOTE]
> この記事の例では、各 API 要求の同期フォームを使用し、基になるサービスでの REST 呼び出しが完了するまでブロックします。 非同期の手法も利用できます。
>  

## <a name="create-a-sql-elastic-pool---c-example"></a>SQL エラスティック プールの作成 - C# の例
次のサンプルでは、リソース グループ、サーバー、ファイアウォール規則、エラスティック プールを作成した後、そのプールに SQL データベースを作成します。 「[リソースにアクセスするためのサービス プリンシパルの作成](#create-a-service-principal-to-access-resources)」を参照し、変数 `_subscriptionId, _tenantId, _applicationId, and _applicationSecret` を取得してください。

**Program.cs** の内容を次のとおりに書き換え、実際のアプリの値で `{variables}` を更新します (`{}` は含めません)。

```
using Microsoft.Azure;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.Azure.Management.ResourceManager.Models;
using Microsoft.Azure.Management.Sql;
using Microsoft.Azure.Management.Sql.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace SqlElasticPoolConsoleApp
{
    class Program
        {

        // For details about these four (4) values, see
        // https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/
        static string _subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _tenantId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationSecret = "{your-password}";

        // Create management clients for the Azure resources your app needs to work with.
        // This app works with Resource Groups, and Azure SQL Database.
        static ResourceManagementClient _resourceMgmtClient;
        static SqlManagementClient _sqlMgmtClient;

        // Authentication token
        static AuthenticationResult _token;

        // Azure resource variables
        static string _resourceGroupName = "{resource-group-name}";
        static string _resourceGrouplocation = "{Azure-region}";

        static string _serverlocation = _resourceGrouplocation;
        static string _serverName = "{server-name}";
        static string _serverAdmin = "{server-admin-login}";
        static string _serverAdminPassword = "{server-admin-password}";

        static string _firewallRuleName = "{firewall-rule-name}";
        static string _startIpAddress = "{0.0.0.0}";
        static string _endIpAddress = "{255.255.255.255}";

        static string _poolName = "{pool-name}";
        static string _poolEdition = "{Standard}";
        static int _poolDtus = {100};
        static int _databaseMinDtus = {10};
        static int _databaseMaxDtus = {100};

        static string _databaseName = "{elasticdbfromcsarticle}";



        static void Main(string[] args)
        {
            // Authenticate:
            _token = GetToken(_tenantId, _applicationId, _applicationSecret);
            Console.WriteLine("Token acquired. Expires on:" + _token.ExpiresOn);

            // Instantiate management clients:
            _resourceMgmtClient = new ResourceManagementClient(new Microsoft.Rest.TokenCredentials(_token.AccessToken));
            _sqlMgmtClient = new SqlManagementClient(new TokenCloudCredentials(_subscriptionId, _token.AccessToken));


            Console.WriteLine("Resource group...");
            ResourceGroup rg = CreateOrUpdateResourceGroup(_resourceMgmtClient, _subscriptionId, _resourceGroupName, _resourceGrouplocation);
            Console.WriteLine("Resource group: " + rg.Id);


            Console.WriteLine("Server...");
            ServerGetResponse sgr = CreateOrUpdateServer(_sqlMgmtClient, _resourceGroupName, _serverlocation, _serverName, _serverAdmin, _serverAdminPassword);
            Console.WriteLine("Server: " + sgr.Server.Id);

            Console.WriteLine("Server firewall...");
            FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);
            Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);

            Console.WriteLine("Elastic pool...");
            ElasticPoolCreateOrUpdateResponse epr = CreateOrUpdateElasticDatabasePool(_sqlMgmtClient, _resourceGroupName, _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, _databaseMaxDtus);
            Console.WriteLine("Elastic pool: " + epr.ElasticPool.Id);

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName);
            Console.WriteLine("Database: " + dbr.Database.Id);


            Console.WriteLine("Press any key to continue...");
            Console.ReadKey();
        }

        static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupName, string resourceGroupLocation)
        {
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = resourceGroupLocation,
            };
            resourceMgmtClient.SubscriptionId = subscriptionId;
            ResourceGroup resourceGroupResult = resourceMgmtClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }

        static ServerGetResponse CreateOrUpdateServer(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverLocation, string serverName, string serverAdmin, string serverAdminPassword)
        {
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = "12.0"
                }
            };
            ServerGetResponse serverResult = sqlMgmtClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;
        }


        static FirewallRuleGetResponse CreateOrUpdateFirewallRule(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string firewallRuleName, string startIpAddress, string endIpAddress)
        {
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = startIpAddress,
                    EndIpAddress = endIpAddress
                }
            };
            FirewallRuleGetResponse firewallResult = sqlMgmtClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
            return firewallResult;
        }



        static ElasticPoolCreateOrUpdateResponse CreateOrUpdateElasticDatabasePool(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string poolName, string poolEdition, int poolDtus, int databaseMinDtus, int databaseMaxDtus)
        {
            // Retrieve the server that will host this elastic pool
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = poolEdition,
                    Dtu = poolDtus,
                    DatabaseDtuMin = databaseMinDtus,
                    DatabaseDtuMax = databaseMaxDtus
                }
            };

            // Create the pool
            var newPoolResponse = sqlMgmtClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, poolName, newPoolParameters);
            return newPoolResponse;
        }




        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string poolName)
        {
            // Retrieve the server that will host this database
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    CreateMode = DatabaseCreateMode.Default,
                    ElasticPoolName = poolName
                }
            };
            DatabaseCreateOrUpdateResponse dbResponse = sqlMgmtClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }



        private static AuthenticationResult GetToken(string tenantId, string applicationId, string applicationSecret)
        {
            AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/" + tenantId);
            _token = authContext.AcquireToken("https://management.core.windows.net/", new ClientCredential(applicationId, applicationSecret));
            return _token;
        }
    }
}
```

## <a name="create-a-service-principal-to-access-resources"></a>リソースにアクセスするためのサービス プリンシパルの作成
次の PowerShell スクリプトを実行すると、Active Directory (AD) アプリケーションのほか、C# アプリの認証に必要なサービス プリンシパルが作成されます。 このスクリプトによって、上記の C# のサンプルに必要な値が出力されます。 詳細については、「 [リソースにアクセスするためのサービス プリンシパルを Azure PowerShell で作成する](../azure-resource-manager/resource-group-authenticate-service-principal.md)」を参照してください。

    # Sign in to Azure.
    Add-AzureRmAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




この記事の手順を完了するには、次のものが必要です。

* エラスティック プール。 エラスティック プールを作成するには、「[C# を使用したエラスティック プールの作成](sql-database-elastic-pool-manage-csharp.md)」をご覧ください。
* 見ることができます。 Visual Studio の無償版については、 [Visual Studio のダウンロード](https://www.visualstudio.com/downloads/download-visual-studio-vs) に関するページを参照してください。

## <a name="move-a-database-into-an-elastic-pool"></a>エラスティック プールへのデータベースの移動
エラスティック プールに、またはエラスティック プールから、スタンドアロンのデータベースを移動できます。  

    // Retrieve current database properties.

    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database.
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

## <a name="list-databases-in-an-elastic-pool"></a>エラスティック プール内のデータベースの一覧表示
エラスティック プール内のすべてのデータベースを取得するには、[ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases) メソッドを呼び出します。

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## <a name="change-performance-settings-of-an-elastic-pool"></a>エラスティック プールのパフォーマンス設定の変更
既存のプールのプロパティを取得します。 値を変更して、CreateOrUpdate メソッドを実行します。

    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

## <a name="latency-of-elastic-pool-operations"></a>エラスティック プール操作の待機時間
* 通常、データベースあたりの最小 eDTU またはデータベースあたりの最大 eDTU の変更は、5 分以内で完了します。
* プール サイズ (eDTU) の変更時間は、プール内のすべてのデータベースを結合したサイズに依存します。 変更の平均時間は、100 GB あたり 90 分以下です。 たとえば、プール内のすべてのデータベースの合計領域が 200 GB の場合、プールあたりの eDTU の変更にかかる想定待機時間は、3 時間以下になります。

## <a name="additional-resources"></a>その他のリソース
*  SQL Database クライアント アプリケーションの SQL エラー コード、データベース接続エラー、およびその他の問題については、[エラー メッセージ](sql-database-develop-error-messages.md)に関するページを参照してください。
* [Azure リソース管理 API](https://msdn.microsoft.com/library/azure/dn948464.aspx)
* エラスティック プールのガイダンスについては、「[エラスティック プールの使用に適した状況](sql-database-elastic-pool-guidance.md)」を参照してください。


