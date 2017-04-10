---
title: "非対話型認証 .NET HDInsight アプリケーションを作成する | Microsoft Docs"
description: "非対話型認証 .NET HDInsight アプリケーションを作成する方法について説明します。"
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 8e32430f-6404-498a-9fcd-f20338d964af
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: a423975e8a091183154217678706817694f3e346
ms.openlocfilehash: d5256250d6d3a6d7df3a90ae4a0801af131b830e
ms.lasthandoff: 12/21/2016


---
# <a name="create-non-interactive-authentication-net-hdinsight-applications"></a>非対話型認証 .NET HDInsight アプリケーションを作成する
アプリケーション独自の ID (非対話型) またはアプリケーションのサインイン ユーザーの ID (対話型) のいずれかで、.NET Azure HDInsight アプリケーションを実行できます。 対話型アプリケーションのサンプルについては、「[Azure HDInsight への接続](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight)」を参照してください。 この記事では、Azure に接続して HDInsight を管理する非対話型認証 .NET アプリケーションを作成する方法について説明します。

非対話型 .NET アプリケーションでは、以下が必要です。

* Azure サブスクリプションのテナント ID (別名: ディレクトリ ID)。 「[Get tenant ID](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id)」 (テナント ID を取得する) を参照してください。
* Azure Directory アプリケーションのクライアント ID。 「[Active Directory アプリケーションを作成する](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-active-directory-application)」と「[Get an application ID](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)」 (アプリケーション ID を取得する) を参照してください。
* Azure Directory アプリケーションのシークレット キー。 「[Get application authentication key](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)」 (アプリケーションの認証キーを取得する) を参照してください。

## <a name="prerequisites"></a>前提条件
* HDInsight クラスター。 [使用に関するチュートリアル](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster)を参照してください。



## <a name="assign-ad-application-to-role"></a>AD アプリケーションをロールに割り当てる
アクションを実行するアクセス許可を付与するには、 [ロール](../active-directory/role-based-access-built-in-roles.md) にアプリケーションを割り当てる必要があります。 スコープは、サブスクリプション、リソース グループ、またはリソースのレベルで設定できます。 アクセス許可は、スコープの下位レベルに継承されます (たとえば、アプリケーションをリソース グループの閲覧者ロールに追加すると、アプリケーションではリソース グループとそれに含まれているすべてのリソースを読み取ることができます)。 このチュートリアルでは、リソース グループ レベルでスコープを設定します。 詳細については、「[Azure サブスクリプション リソースへのアクセスをロールの割り当てによって管理する](../active-directory/role-based-access-control-configure.md)」を参照してください。

**AD アプリケーションに所有者ロールを追加するには**

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 左側のウィンドウの **[リソース グループ]** をクリックします。
3. HDInsight クラスター (このチュートリアルの後半で Hive クエリを実行するクラスター) を含むリソース グループをクリックします。 リソース グループ数が多すぎる場合は、フィルターを使用することができます。
4. リソース グループ メニューから **[アクセス制御 (IAM)]**を選択します。
5. **[ユーザー]** ブレードから **[追加]** をクリックします。
6. 指示に従って、前の手順で作成した AD アプリケーションに **所有者** ロールを追加します。 正常に完了し、所有者ロールで [ユーザー] ブレードを見ると、そのアプリケーションが表示されます。

## <a name="develop-hdinsight-client-application"></a>HDInsight クライアント アプリケーションを開発する

1. C# コンソール アプリケーションを作成します。
2. 次の Nuget パッケージを追加します。

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. 次のサンプル コードを使用します。

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Factories;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.HDInsight;
        
        namespace CreateHDICluster
        {
            internal class Program
            {
                private static HDInsightManagementClient _hdiManagementClient;
        
                private static Guid SubscriptionId = new Guid("<Enter Your Azure Subscription ID>");
                private static string tenantID = "<Enter Your Tenant ID (A.K.A. Directory ID)>";
                private static string applicationID = "<Enter Your Application ID>";
                private static string secretKey = "<Enter the Application Secret Key>";
        
                private static void Main(string[] args)
                {
                    var key = new SecureString();
                    foreach (char c in secretKey) { key.AppendChar(c); }

                    var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                    var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
        
                    var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                    resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        
                    _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
        
                    var results = _hdiManagementClient.Clusters.List();
                    foreach (var name in results.Clusters)
                    {
                        Console.WriteLine("Cluster Name: " + name.Name);
                        Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                        Console.WriteLine("\t Cluster location: " + name.Location);
                        Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                    }
                    Console.WriteLine("Press Enter to continue");
                    Console.ReadLine();
                }

                /// Get the access token for a service principal and provided key                
                public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
                {
                    var authFactory = new AuthenticationFactory();
                    var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                    var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                    var accessToken =
                        authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
        
                    return new TokenCloudCredentials(accessToken);
                }
        
                public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
                }
            }
        }

## <a name="next-steps"></a>次のステップ
* [ポータルを利用し、Active Directory のアプリケーションとサービス プリンシパルを作成する](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [Azure Resource Manager でサービス プリンシパルを認証する](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Azure のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)

