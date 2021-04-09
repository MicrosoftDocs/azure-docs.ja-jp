---
title: .NET SDK を使用して HDInsight で Apache Hadoop クラスターを管理する - Azure
description: HDInsight .NET SDK を使用して、HDInsight で Apache Hadoop クラスターの管理タスクを実行する方法について説明します。
ms.service: hdinsight
ms.custom: hdinsightactive, devx-track-csharp
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: e5f328d6be52732c30478eb49360efb13919a23e
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104872219"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>.NET SDK を使用して HDInsight で Apache Hadoop クラスターを管理する

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

[HDInsight.NET SDK](/dotnet/api/overview/azure/hdinsight)を使用して、HDInsight クラスターを管理する方法について説明します。

**前提条件**

この記事を読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

## <a name="connect-to-azure-hdinsight"></a>Azure HDInsight への接続

次の NuGet パッケージが必要です。

```powershell
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

次のコード サンプルは、Azure サブスクリプションで HDInsight クラスターを管理する前に Azure に接続する方法を示しています。

```csharp
using System;
using Microsoft.Azure;
using Microsoft.Azure.Management.HDInsight;
using Microsoft.Azure.Management.HDInsight.Models;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace HDInsightManagement
{
    class Program
    {
        private static HDInsightManagementClient _hdiManagementClient;
        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

        static void Main(string[] args)
        {
            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            // insert code here

            System.Console.WriteLine("Press ENTER to continue");
            System.Console.ReadLine();
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }
    }
}
```

このプログラムを実行すると、プロンプトが表示されます。  プロンプトを表示しない場合は、「 [非対話型認証 .NET HDInsight アプリケーションを作成する](hdinsight-create-non-interactive-authentication-dotnet-applications.md)」をご覧ください。


## <a name="list-clusters"></a>クラスターを一覧表示する

次のコード スニペットは、クラスターと一部のプロパティを一覧表示します。

```csharp
var results = _hdiManagementClient.Clusters.List();
foreach (var name in results.Clusters) {
    Console.WriteLine("Cluster Name: " + name.Name);
    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
    Console.WriteLine("\t Cluster location: " + name.Location);
    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
}
```

## <a name="delete-clusters"></a>クラスターの削除

クラスターを同期または非同期で削除するには、次のコード スニペットを使用します。 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>クラスターのスケール

クラスターのスケール設定機能を使用すると、Azure HDInsight で実行しているクラスターによって使用される worker ノードの数を、クラスターを再作成することなく、変更できます。

> [!NOTE]  
> HDInsight バージョン 3.1.3 以降を使用しているクラスターのみがサポートされます。 クラスターのバージョンがわからない場合、[プロパティ] ページを確認できます。  「[クラスターの一覧と表示](hdinsight-administer-use-portal-linux.md#showClusters)」を参照してください。

HDInsight でサポートされているクラスターの種類ごとに、データ ノード数を変更した場合の影響:

* Apache Hadoop
  
    保留中または実行中のジョブに影響を与えることなく、実行中の Hadoop クラスター内の worker ノードの数をシームレスに増加できます。 処理の進行中に新しいジョブを送信することもできます。 スケール設定処理の失敗は正常に処理され、クラスターは常に機能状態になります。
  
    データ ノードの数を減らして Hadoop クラスターのスケールを小さくした場合、クラスター内の一部のサービスが再起動されます。 これにより、スケール設定処理の完了時に、実行中および保留中のすべてのジョブが失敗します。 ただし、処理が完了した後にジョブを再送信できます。
* Apache HBase
  
    実行中の HBase クラスターに対して、ノードの追加または削除をシームレスに実行できます。 地域サーバーは、スケール設定処理の完了の数分以内に自動的に分散されます。 ただし、クラスターのヘッドノードにログインし、コマンド プロンプト ウィンドウから次のコマンドを実行して、地域サーバーを手動で分散することもできます。
  

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Apache Storm
  
    実行中の Storm クラスターに対して、データ ノードの追加または削除をシームレスに実行できます。 ただし、スケール設定処理が正常に完了した後、トポロジのバランス再調整が必要になります。
  
    バランス再調整は、次の 2 つの方法で実行できます。
  
  * Storm Web UI
  * コマンド ライン インターフェイス (CLI) ツール
    
    詳細については、 [Apache Storm に関するドキュメント](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) をご覧ください。
    
    Storm Web UI は、HDInsight クラスターで使用できます。
    
    :::image type="content" source="./media/hdinsight-administer-use-powershell/hdinsight-portal-scale-cluster-storm-rebalance.png" alt-text="HDInsight Storm のスケールのバランス調整":::
    
    CLI コマンドを使用して Storm トポロジのバランスを再調整する方法を次の例で示します。
    

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

次のコード スニペットは、同期または非同期でクラスターのサイズを変更する方法を示しています。

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>アクセス権の付与/取り消し

HDInsight クラスターには、以下の HTTP Web サービスがあります (これらすべてのサービスには、REST ベースのエンドポイントがあります)。

* ODBC
* JDBC
* Apache Ambari
* Apache Oozie
* Apache Templeton

既定では、これらのサービスへのアクセス許可が付与されます。 アクセス許可を取り消す/付与することができます。 取り消するには、次を実行します。

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = false,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

許可するには、次を実行します。

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = enable,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

> [!NOTE]  
> アクセス許可を付与するか、取り消すことで、クラスターのユーザー名とパスワードがリセットされます。

これは、ポータルを使用して行うこともできます。 「[Azure portal を使用して HDInsight の Apache Hadoop クラスターを管理する](hdinsight-administer-use-portal-linux.md)」を参照してください。

## <a name="update-http-user-credentials"></a>HTTP ユーザーの資格情報の更新

HTTP アクセスの付与/取り消しと同じ手順です。  クラスターに HTTP アクセスが許可されている場合は、まずそれを取り消す必要があります。  次に、新しい HTTP ユーザーの資格情報を使用してアクセス許可を付与してください。

## <a name="find-the-default-storage-account"></a>既定のストレージ アカウントの検索

次のコード スニペットは、クラスターの既定のストレージ アカウント名と既定のストレージ アカウント キーを取得する方法を示しています。

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>ジョブの送信

**MapReduce ジョブを送信するには**

[HDInsight での MapReduce サンプルの実行](hadoop/apache-hadoop-run-samples-linux.md)に関するページを参照してください。

**Apache Hive ジョブを送信するには** 

[.NET SDK を使用した Apache Hive クエリの実行](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)に関するページを参照してください。

**Apache Sqoop ジョブを送信するには**

[HDInsight での Apache Sqoop の使用](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md)に関するページを参照してください。

**Apache Oozie ジョブを送信するには**

[HDInsight での Apache Oozie と Hadoop を使用したワークフローの定義と実行](hdinsight-use-oozie-linux-mac.md)に関するページを参照してください。

## <a name="upload-data-to-azure-blob-storage"></a>Azure BLOB ストレージにデータをアップロードする

[HDInsight へのデータのアップロード][hdinsight-upload-data]に関するページを参照してください。

## <a name="see-also"></a>参照

* [HDInsight .NET SDK リファレンス ドキュメント](/dotnet/api/overview/azure/hdinsight)
* [Azure portal を使用して HDInsight の Apache Hadoop クラスターを管理する](hdinsight-administer-use-portal-linux.md)
* [コマンド ライン インターフェイスを使用した HDInsight の管理][hdinsight-admin-cli]
* [HDInsight クラスターの作成][hdinsight-provision]
* [HDInsight へのデータのアップロード][hdinsight-upload-data]
* [Azure HDInsight の概要][hdinsight-get-started]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
