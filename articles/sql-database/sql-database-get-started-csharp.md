---
title: "C#: Azure SQL Database の概要 | Microsoft Docs"
description: "SQL Database で SQL および C# アプリケーションを開発し、C# と SQL Database Library for .NET を使用して、Azure SQL Database を作成します。"
keywords: "sql を試す, sql c#"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: cgronlun
ms.assetid: cfff2299-a474-4054-8d99-759af1ae5188
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: csharp
ms.workload: data-management
ms.date: 10/04/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: dbf337a27c43fc6c91f1b061a1938c5471dd36a4
ms.openlocfilehash: bc1a78a2891c73df23bc2a57cec67e6b73414165
ms.lasthandoff: 02/16/2017


---
# <a name="use-c-to-create-a-sql-database-with-the-sql-database-library-for-net"></a>C# を使用して SQL Database Library for .NET で SQL データベースを作成する

C# で [Microsoft Azure SQL Management Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql) を使用して Azure SQL データベースを作成する方法について説明します。 この記事では、SQL と C# を使って単一のデータベースを作成する方法について説明します。 エラスティック プールを作成するには、[エラスティック プールの作成](sql-database-elastic-pool-manage-csharp.md)に関する記事を参照してください。

Azure SQL Database Management Library for .NET は、[Resource Manager ベースの SQL Database REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx) をラップする [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) ベースの API を提供します。

> [!NOTE]
> SQL Database の新機能の多くは、[Azure Resource Manager デプロイメント モデル](../azure-resource-manager/resource-group-overview.md)を使用している場合にのみサポートされます。そのため、常に最新の **Azure SQL Database Management Library for .NET ([ドキュメント](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))** を使用する必要があります。 以前の[クラシック デプロイメント モデル ベースのライブラリ](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql)は互換性のためだけにサポートされているため、より新しい Resource Manager ベースのライブラリを使用することをお勧めします。
> 
> 

この記事の手順を完了するには、次のものが必要です。

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、このページの上部にある **無料アカウント** をクリックしてサブスクリプションを作成してから、この記事に戻って最後まで完了してください。
* 見ることができます。 Visual Studio の無償版については、 [Visual Studio のダウンロード](https://www.visualstudio.com/downloads/download-visual-studio-vs) に関するページを参照してください。

> [!NOTE]
> この記事では、新しい空の SQL データベースを作成します。 次のサンプルの *CreateOrUpdateDatabase(...)* メソッドを変更し、データベースのコピー、データベースのスケール変更、プールでのデータベースの作成などを行います。詳細については、[DatabaseCreateMode](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databasecreatemode.aspx) クラスと [DatabaseProperties](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databaseproperties.aspx) クラスを参照してください。
> 
> 

## <a name="create-a-console-app-and-install-the-required-libraries"></a>コンソール アプリケーションの作成と必要なライブラリのインストール
1. Visual Studio を起動します。
2. **[ファイル]** > **[新規]** > **[プロジェクト]** の順にクリックします。
3. C# **コンソール アプリケーション** を作成し、 *SqlDbConsoleApp*

C# を使用して SQL データベースを作成するために、( [パッケージ マネージャー コンソール](http://docs.nuget.org/Consume/Package-Manager-Console)を使用して) 必要な管理ライブラリを読み込みます。

1. **[ツール]** > **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順にクリックします。
2. 「`Install-Package Microsoft.Azure.Management.Sql -Pre`」と入力して最新の [Microsoft Azure SQL Management Library](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql) をインストールします。
3. 「 `Install-Package Microsoft.Azure.Management.ResourceManager -Pre` 」と入力して [Microsoft Azure Resource Manager ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)をインストールします。
4. 「 `Install-Package Microsoft.Azure.Common.Authentication -Pre` 」と入力して [Microsoft Azure 一般認証ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication)をインストールします。 

> [!NOTE]
> この記事の例では、各 API 要求の同期フォームを使用し、基になるサービスでの REST 呼び出しが完了するまでブロックします。 非同期の手法も利用できます。
> 
> 

## <a name="create-a-sql-database-server-firewall-rule-and-sql-database---c-example"></a>SQL Database サーバー、ファイアウォール規則、SQL データベースの作成 (C# のサンプル)
次のサンプルでは、リソース グループ、サーバー、ファイアウォール規則、SQL データベースを作成します。 「[リソースにアクセスするためのサービス プリンシパルの作成](#create-a-service-principal-to-access-resources)」を参照し、変数 `_subscriptionId, _tenantId, _applicationId, and _applicationSecret` を取得してください。

**Program.cs** の内容を次のとおりに書き換え、実際のアプリの値で `{variables}` を更新します (`{}` は含めません)。

    using Microsoft.Azure;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;

    namespace SqlDbConsoleApp
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

        static string _databaseName = "{dbfromcsarticle}";
        static string _databaseEdition = DatabaseEditions.Basic;
        static string _databasePerfLevel = ""; // "S0", "S1", and so on here for other tiers


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

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _databaseEdition, _databasePerfLevel);
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



        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string databaseEdition, string databasePerfLevel)
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
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel
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



## <a name="next-steps"></a>次のステップ
ここでは、SQL Database を試し、C# でデータベースを設定しました。次は以下の記事を参照してください。

* [SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)

## <a name="additional-resources"></a>その他のリソース
* [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)
* [データベース クラス](https://msdn.microsoft.com/library/azure/microsoft.azure.management.sql.models.database.aspx)

<!--Image references-->
[1]: ./media/sql-database-get-started-csharp/aad.png
[2]: ./media/sql-database-get-started-csharp/permissions.png
[3]: ./media/sql-database-get-started-csharp/getdomain.png
[4]: ./media/sql-database-get-started-csharp/aad2.png
[5]: ./media/sql-database-get-started-csharp/aad-applications.png
[6]: ./media/sql-database-get-started-csharp/add.png
[7]: ./media/sql-database-get-started-csharp/add-application.png
[8]: ./media/sql-database-get-started-csharp/add-application2.png
[9]: ./media/sql-database-get-started-csharp/clientid.png

