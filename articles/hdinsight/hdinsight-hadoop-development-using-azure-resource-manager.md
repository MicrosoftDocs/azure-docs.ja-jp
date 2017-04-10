---
title: "HDInsight 用の Azure Resource Manager ツールに移行する | Microsoft Docs"
description: "HDInsight クラスター用の Azure Resource Manager 開発ツールに移行する方法"
services: hdinsight
editor: cgronlun
manager: jhubbard
author: nitinme
documentationcenter: 
ms.assetid: 05efedb5-6456-4552-87ff-156d77fbe2e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: 3606df64b619b62f8b9e5aec2abc4efc994c37e3
ms.lasthandoff: 01/24/2017


---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>HDInsight クラスター用の Azure Resource Manager ベースの開発ツールに移行する

HDInsight は、HDInsight 用の Azure Service Manager (ASM) ベースのツールを推奨していません。 HDInsight クラスターの操作に Azure PowerShell、Azure CLI、HDInsight .NET SDK を使用している場合、今後、Azure Resource Manager (ARM) ベース バージョンの PowerShell、CLI、.NET SDK を使用することをお勧めします。 この記事では、新しい ARM ベースのアプローチに移行する方法についての参照先を紹介します。 また、HDInsight に対する ASM と ARM のアプローチの違いについても適宜説明します。

> [!IMPORTANT]
> ASM ベースの PowerShell、CLI、.NET SDK のサポートは、 **2017 年 1 月 1 日**に中止される予定です。
> 
> 

## <a name="migrating-azure-cli-to-azure-resource-manager"></a>Azure CLI を Azure Resource Manager に移行する
前回のインストールからアップグレードしていない場合、Azure CLI の既定は Azure Resource Manager (ARM) モードになりました。アップグレードした場合は、`azure config mode arm` コマンドを使用して ARM モードに切り替える必要があります。

Azure Service Manager (ASM) を使用して HDInsight を操作するために Azure CLI に用意されていた基本的なコマンドは、ARM を使用する場合と同じですが、一部のパラメーターとスイッチの名前は変わりました。また、ARM を使用する場合、使用できるパラメーターが増えました。 たとえば、`azure hdinsight cluster create` を使用して、クラスターを作成する Azure Virtual Network、Hive や Oozie のメタストア情報を指定できるようになりました。

Azure Resource Manager には、HDInsight を操作する次の基本的なコマンドがあります。

* `azure hdinsight cluster create` - 新しい HDInsight クラスターを作成します
* `azure hdinsight cluster delete` - 既存の HDInsight クラスターを削除します
* `azure hdinsight cluster show` - 既存のクラスターに関する情報を表示します
* `azure hdinsight cluster list` - Azure サブスクリプションの HDInsight クラスターを一覧表示します

各コマンドの使用できるパラメーターとスイッチを調べるには、 `-h` スイッチを使用します。

### <a name="new-commands"></a>新しいコマンド
Azure Resource Manager では、次の新しいコマンドを使用できます。

* `azure hdinsight cluster resize` - クラスター内のワーカー ノード数を動的に変更します
* `azure hdinsight cluster enable-http-access` - クラスターに対する HTTP アクセスを有効にします (既定は有効)
* `azure hdinsight cluster disable-http-access` - クラスターに対する HTTP アクセスを無効にします
* `azure hdinsight script-action` - クラスターに対するスクリプト アクションを作成または管理するコマンドを表示します
* `azure hdinsight config` - `hdinsight cluster create` コマンドに使用して構成情報を提供することができる構成ファイルを作成するためのコマンドを表示します

### <a name="deprecated-commands"></a>非推奨コマンド
`azure hdinsight job` コマンドを使用してジョブを HDInsight クラスターに送信する場合、ARM コマンドでは使用できません。 プログラムでスクリプトから HDInsight にジョブを送信する必要がある場合は、代わりに HDInsight に用意されている REST API を使用してください。 REST API を使用したジョブの送信については、次のドキュメントを参照してください。

* [Curl を使用して HDInsight の Hadoop で MapReduce ジョブを実行](hdinsight-hadoop-use-mapreduce-curl.md)
* [Curl を使用した HDInsight の Hadoop での Hive クエリの実行](hdinsight-hadoop-use-hive-curl.md)
* [Curl を使用して HDInsight の Hadoop で Pig ジョブを実行](hdinsight-hadoop-use-pig-curl.md)

MapReduce、Hive、Pig を対話的に実行する他の方法については、「[HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)」、「[HDInsight で Hadoop と共に Hive と HiveQL を使用して Apache log4j サンプル ファイルを分析する](hdinsight-use-hive.md)」、「[HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)」を参照してください。

### <a name="examples"></a>例
**クラスターを作成する**

* 以前のコマンド (ASM) - `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* 新しいコマンド (ARM) - `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**クラスターを削除する**

* 以前のコマンド (ASM) - `azure hdinsight cluster delete myhdicluster`
* 新しいコマンド (ARM) - `azure hdinsight cluster delete mycluster -g myresourcegroup`

**クラスターの一覧表示**

* 以前のコマンド (ASM) - `azure hdinsight cluster list`
* 新しいコマンド (ARM) - `azure hdinsight cluster list`

> [!NOTE]
> list コマンドの場合、`-g` を使用してリソース グループを指定すると、指定したリソース グループのクラスターのみが返されます。
> 
> 

**クラスター情報を表示する**

* 以前のコマンド (ASM) - `azure hdinsight cluster show myhdicluster`
* 新しいコマンド (ARM) - `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Azure PowerShell から Azure Resource Manager に移行する
Azure Resource Manager (ARM) モードの Azure PowerShell の全般情報については、「[Azure リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。

Azure PowerShell ARM コマンドレットは、ASM コマンドと同時にインストールできます。 2 つのモードのコマンドレットは、名前で区別できます。  ARM モードのコマンドレット名は *AzureRmHDInsight* であり、ASM モードのコマンドレット名は *AzureHDInsight* です。  たとえば、*New-AzureRmHDInsightCluster* と*New-AzureHDInsightCluster* などです。 ARM では、一部のパラメーター名やスイッチ名が新しくなり、多数のパラメーターが追加されました。  たとえば、一部のコマンドレットでは、 *-ResourceGroupName*という新しいスイッチが必須になりました。 

HDInsight コマンドレットを使用する前に、Azure アカウントに接続し、新しいリソース グループを作成する必要があります。

* Login-AzureRmAccount または [Select-AzureRmProfile](https://msdn.microsoft.com/library/mt619310.aspx)。 「[Azure リソース マネージャーでのサービス プリンシパルの認証](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>名前が変更されたコマンドレット
Windows PowerShell コンソールで HDInsight ASM コマンドレットを一覧表示するには:

    help *azurermhdinsight*

次の表は、ASM コマンドレットと、ARM モードでの対応するコマンドレット名の一覧です。

| ASM コマンドレット | ARM コマンドレット |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzureRmHDInsightConfigValues](https://msdn.microsoft.com/library/mt603530.aspx) |
| Add-AzureHDInsightMetastore |[Add-AzureRmHDInsightMetastore](https://msdn.microsoft.com/library/mt603670.aspx) |
| Add-AzureHDInsightScriptAction |[Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) |
| Add-AzureHDInsightStorage |[Add-AzureRmHDInsightStorage](https://msdn.microsoft.com/library/mt619445.aspx) |
| Get-AzureHDInsightCluster |[Get-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619371.aspx) |
| Get-AzureHDInsightJob |[Get-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603590.aspx) |
| Get-AzureHDInsightJobOutput |[Get-AzureRmHDInsightJobOutput](https://msdn.microsoft.com/library/mt603793.aspx) |
| Get-AzureHDInsightProperties |[Get-AzureRmHDInsightProperties](https://msdn.microsoft.com/library/mt603546.aspx) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619407.aspx) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603717.aspx) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzureRmHDInsightHiveJob](https://msdn.microsoft.com/library/mt603593.aspx) |
| New-AzureHDInsightCluster |[New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) |
| New-AzureHDInsightClusterConfig |[New-AzureRmHDInsightClusterConfig](https://msdn.microsoft.com/library/mt603700.aspx) |
| New-AzureHDInsightHiveJobDefinition |[New-AzureRmHDInsightHiveJobDefinition](https://msdn.microsoft.com/library/mt619448.aspx) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzureRmHDInsightMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| New-AzureHDInsightPigJobDefinition |[New-AzureRmHDInsightPigJobDefinition](https://msdn.microsoft.com/library/mt603671.aspx) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzureRmHDInsightSqoopJobDefinition](https://msdn.microsoft.com/library/mt608551.aspx) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzureRmHDInsightStreamingMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| Remove-AzureHDInsightCluster |[Remove-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619431.aspx) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619375.aspx) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603523.aspx) |
| Set-AzureHDInsightClusterSize |[Set-AzureRmHDInsightClusterSize](https://msdn.microsoft.com/library/mt603513.aspx) |
| Set-AzureHDInsightDefaultStorage |[Set-AzureRmHDInsightDefaultStorage](https://msdn.microsoft.com/library/mt603486.aspx) |
| Start-AzureHDInsightJob |[Start-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603798.aspx) |
| Stop-AzureHDInsightJob |[Stop-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt619424.aspx) |
| Use-AzureHDInsightCluster |[Use-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619442.aspx) |
| Wait-AzureHDInsightJob |[Wait-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603834.aspx) |

### <a name="new-cmdlets"></a>新しいコマンドレット
ARM モードでのみ使用できる新しいコマンドレットを次に示します。 

**スクリプト操作に関連するコマンドレット:**

* **Get-AzureRmHDInsightPersistedScriptAction**: クラスターの保存されたスクリプト操作を取得して古い順に一覧表示します。または、保存されたスクリプト操作のうち、指定した操作の詳細を取得します。 
* **Get-AzureRmHDInsightScriptActionHistory**: クラスターのスクリプト操作履歴を取得し、新しい順に一覧表示します。または、以前に実行されたスクリプト操作の詳細を取得します。 
* **Remove-AzureRmHDInsightPersistedScriptAction**: 保存されたスクリプト操作を HDInsight クラスターから削除します。
* **Set-AzureRmHDInsightPersistedScriptAction**: 以前に実行されたスクリプト操作を、保存されたスクリプト操作に設定します。
* **Submit-AzureRmHDInsightScriptAction**: 新しいスクリプト操作を Azure HDInsight クラスターに送信します。 

その他の使用方法について詳しくは、「[Script Action を使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。

**クラスター ID に関連するコマンドレット:**

* **Add-AzureRmHDInsightClusterIdentity**: クラスター ID をクラスター構成オブジェクトに追加して、HDInsight クラスターが Azure Data Lake Store にアクセスできるようにします。 「[Azure PowerShell を使用して、Data Lake Store を使用する HDInsight クラスターを作成する](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)」を参照してください。

### <a name="examples"></a>例
**クラスターの作成**

以前のコマンド (ASM): 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

新しいコマンド (ARM):

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**クラスターの削除**

以前のコマンド (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

新しいコマンド (ARM):

    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**クラスターの一覧表示**

以前のコマンド (ASM):

    Get-AzureHDInsightCluster

新しいコマンド (ARM):

    Get-AzureRmHDInsightCluster 

**クラスターの表示**

以前のコマンド (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

新しいコマンド (ARM):

    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>その他のサンプル
* [HDInsight クラスターの作成](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Hive ジョブの送信](hdinsight-hadoop-use-hive-powershell.md)
* [Pig ジョブの送信](hdinsight-hadoop-use-pig-powershell.md)
* [Sqoop ジョブの送信](hdinsight-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-arm-based-hdinsight-net-sdk"></a>ARM ベースの HDInsight .NET SDK に移行する
Azure Service Management ベース [(ASM) HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) は非推奨になりました。 Azure Resource Management ベース [(ARM) HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt271028.aspx)を使用することが推奨されます。 次の ASM ベースの HDInsight パッケージは廃止される予定です。

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

ここでは、ARM ベースの SDK を使用して特定のタスクを実行する方法に関する詳細情報の参照先を紹介します。

| ARM ベースの HDInsight SDK を使用して次の操作を実行 | リンク |
| --- | --- |
| .NET SDK を使用して HDInsight クラスターを作成する |「[.NET SDK を使用して HDInsight クラスターを作成する](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| .NET SDK でスクリプト操作を使用してクラスターをカスタマイズする |「[.NET SDK を使用した HDInsight の Linux ベースのクラスターの作成](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Azure Active Directory と .NET SDK を使用して対話型アプリケーションを認証する |「[.NET SDK を使用した Hive クエリの実行](hdinsight-hadoop-use-hive-dotnet-sdk.md)」を参照してください。 この記事のコード スニペットでは、対話型の認証アプローチを使用しています。 |
| Azure Active Directory と .NET SDK を使用して非対話型アプリケーションを認証する |「[非対話型認証 .NET HDInsight アプリケーションを作成する](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| .NET SDK を使用して Hive ジョブを送信する |「[Hive ジョブの送信](hdinsight-hadoop-use-hive-dotnet-sdk.md) |
| .NET SDK を使用して Pig ジョブを送信する |「[Pig ジョブの送信](hdinsight-hadoop-use-pig-dotnet-sdk.md) |
| .NET SDK を使用して Sqoop ジョブを送信する |「[Sqoop ジョブの送信](hdinsight-hadoop-use-sqoop-dotnet-sdk.md) |
| .NET SDK を使用して HDInsight クラスターを一覧表示する |「[List HDInsight clusters (HDInsight クラスターを一覧表示する)](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| .NET SDK を使用して HDInsight クラスターをスケールする |「[Scale HDInsight clusters (HDInsight クラスターをスケールする)](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| .NET SDK を使用して HDInsight クラスターへのアクセス権を付与する、または取り消す |「[Grant/revoke access to HDInsight clusters (HDInsight クラスターへのアクセス権の付与/取り消し)](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| .NET SDK を使用して HDInsight クラスターの HTTP ユーザーの資格情報を更新する |「[Update HTTP user credentials for HDInsight clusters (HDInsight クラスターの HTTP ユーザーの資格情報を更新する)](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| .NET SDK を使用して HDInsight クラスターの既定のストレージ アカウントを検索する |「[Find the default storage account for HDInsight clusters (HDInsight クラスターの既定のストレージ アカウントを検索する)](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| .NET SDK を使用して HDInsight クラスターを削除する |「[Delete HDInsight clusters (HDInsight クラスターを削除する)](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>例
ASM ベースの SDK を使用して操作を実行する方法と、ARM ベースの SDK の対応するコード スニペットの例を次に示します。

**クラスターの CRUD クライアントを作成する**

* 以前のコマンド (ASM):
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager (ARM)
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* 新しいコマンド (ARM) (サービス プリンシパルの承認)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/en-us/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* 新しいコマンド (ARM) (ユーザーの承認)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**クラスターを作成する**

* 以前のコマンド (ASM):
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* 新しいコマンド (ARM):
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**HTTP アクセスを有効にする**

* 以前のコマンド (ASM):
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* 新しいコマンド (ARM):
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**クラスターを削除する**

* 以前のコマンド (ASM):
  
        client.DeleteCluster(dnsName);
* 新しいコマンド (ARM):
  
        client.Clusters.Delete(resourceGroup, dnsname);


