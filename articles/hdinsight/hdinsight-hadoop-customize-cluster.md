---
title: "スクリプト アクションを使った HDInsight クラスターのカスタマイズ | Microsoft Docs"
description: "スクリプト アクションを使って HDInsight クラスターをカスタマイズする方法について説明します。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3a63e216-4163-40c1-aa04-6b42fd0162ad
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: nitinme
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: b5fafb9603957a93a0ca8fbc6dd53798070641a4
ms.openlocfilehash: da013207a2c804898d1a93dfd7875ed2a2deed22
ms.lasthandoff: 02/16/2017


---
# <a name="customize-windows-based-hdinsight-clusters-using-script-action"></a>Script Action を使用して Windows ベースの HDInsight クラスターをカスタマイズする
**Script Action** を使用して、クラスターに追加のソフトウェアをインストールするクラスター作成プロセス中に [カスタム スクリプト](hdinsight-hadoop-script-actions.md) を起動できます。

この記事の情報は、Windows ベースの HDInsight クラスターに固有のものです。 Linux ベースのクラスターについては、「 [Script Action を使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。

> [!IMPORTANT]
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

HDInsight クラスターは、その他さまざまな方法でカスタマイズできます。たとえば、Azure ストレージ アカウントの追加、Hadoop 構成ファイルの変更 (core-site.xml、hive-site.xml など)、クラスター内の一般的な場所への共有ライブラリの追加 (Hive、Oozie など) といった方法があります。 これらのカスタマイズは、Azure PowerShell、Azure HDInsight .NET SDK、Azure ポータルから実行できます。 詳細については、[HDInsight での Hadoop クラスターの作成][hdinsight-provision-cluster]に関する記事をご覧ください。

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

## <a name="script-action-in-the-cluster-creation-process"></a>クラスターの作成処理でのスクリプト アクション
Script Action は、クラスターが作成中にのみ使用されます。 次の図は、作成処理中にスクリプト アクションが実行された場合を示しています。

![クラスター作成時の HDInsight クラスターのカスタマイズと段階][img-hdi-cluster-states]

スクリプトの実行中、クラスターは **ClusterCustomization** 段階に入ります。 この段階でスクリプトは、システム管理者アカウントで、ノードで完全な管理者権限を持ち、クラスター内のすべての指定したノードで並列的に実行されます。

> [!NOTE]
> **ClusterCustomization** 段階ではクラスター ノードで管理者権限を持っているため、スクリプトを使用して、Hadoop 関連のサービスを含め、サービスの停止や開始などの操作を行うことができます。 そのため、スクリプトの一部として、スクリプトの完了前に Ambari サービスや他の Hadoop 関連のサービスが動作していることを確認する必要があります。 これらのサービスでは、クラスターの作成時にクラスターが正常に稼動しているか確認する必要があります。 これらのサービスに影響するクラスター上の構成を変更する場合は、用意されているヘルパー関数を使用する必要があります。 ヘルパー関数の詳細については、「[HDInsight 用のスクリプト アクションのスクリプトを開発する][hdinsight-write-script]」を参照してください。
>
>

スクリプトの出力とエラー ログは、クラスター用に指定した既定のストレージ アカウントに格納されます。 ログは、**u<\cluster-name-fragment><\time-stamp>setuplog** という名前のテーブルに格納されます。 これらは、クラスター内のすべてのノード (ヘッドノードとワーカー ノード) で実行されるスクリプトから取得される集計ログです。

各クラスターは、指定された順序で呼び出される複数のスクリプト アクションを受け取ることができます。 スクリプトはヘッド ノード、ワーカー ノード、またはその両方で実行できます。

HDInsight は、HDInsight クラスターで、次のコンポーネントをインストールするためのいくつかのスクリプトを提供します。

| 名前 | スクリプト |
| --- | --- |
| **Spark のインストール** |https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1。 「[HDInsight クラスターで Spark をインストールして使用する][hdinsight-install-spark]」を参照してください。 |
| **R のインストール** |https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1。 [HDInsight クラスターでの R のインストールと使用][hdinsight-install-r]に関する記事を参照してください。 |
| **Solr のインストール** |https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1。 「 [HDInsight クラスターに Solr をインストールして使用する](hdinsight-hadoop-solr-install.md)」をご覧ください。 |
| - **Giraph のインストール** |https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1。 「 [HDInsight クラスターに Giraph をインストールして使用する](hdinsight-hadoop-giraph-install.md)」をご覧ください。 |
| **Hive ライブラリの事前読み込み** |https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1 「[HDInsight クラスター作成時の Hive ライブラリの追加](hdinsight-hadoop-add-hive-libraries.md)」を参照してください。 |

## <a name="call-scripts-using-the-azure-portal"></a>Azure ポータルを使用してスクリプトを呼び出す
**Azure ポータル**

1. [HDInsight での Hadoop クラスターの作成](hdinsight-provision-clusters.md)に関する記事の説明に従って、クラスターの作成を開始します。
2. [オプションの構成] の **[スクリプト アクション]** ブレードで、**[スクリプト アクションの追加]** をクリックし、次に示すように、スクリプト アクションの詳細を指定します。

    ![スクリプト アクションを使ってクラスターをカスタマイズする](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "スクリプト アクションを使ってクラスターをカスタマイズする")

    <table border='1'>
        <tr><th>プロパティ</th><th>値</th></tr>
        <tr><td>名前</td>
            <td>スクリプト アクションの名前を指定します。</td></tr>
        <tr><td>スクリプト URI</td>
            <td>クラスターのカスタマイズのために呼び出されるスクリプトへの URI を指定します。 s</td></tr>
        <tr><td>ヘッド/ワーカー</td>
            <td>カスタマイズ スクリプトが実行されるノード (**[ヘッド]** または **[ワーカー]**) を指定します。</b>
        <tr><td>parameters</td>
            <td>スクリプトで必要な場合は、パラメーターを指定します。</td></tr>
    </table>

    クラスターに複数のコンポーネントをインストールするには、Enter キーを押して複数のスクリプト アクションを追加します。
3. **[選択]** をクリックしてスクリプト アクションの構成を保存し、クラスターの作成を続行します。

## <a name="call-scripts-using-azure-powershell"></a>Azure PowerShell を使用してスクリプトを呼び出す
次の PowerShell スクリプトでは、Windows ベースの HDInsight クラスターに Spark をインストールする方法を示します。  

    # Provide values for these variables
    $subscriptionID = "<Azure Suscription ID>" # After "Login-AzureRmAccount", use "Get-AzureRmSubscription" to list IDs.

    $nameToken = "<Enter A Name Token>"  # The token is use to create Azure service names.
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2" # used for creating resource group, storage account, and HDInsight cluster.

    $hdinsightClusterName = $namePrefix + "spark"
    $httpUserName = "admin"
    $httpPassword = "<Enter a Password>"

    $defaultStorageAccountName = "$namePrefix" + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    #############################################################
    # Connect to Azure
    #############################################################

    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Login-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    #############################################################
    # Prepare the dependent components
    #############################################################

    # Create resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext

    #############################################################
    # Create cluster with ApacheSpark
    #############################################################

    # Specify the configuration options
    $config = New-AzureRmHDInsightClusterConfig `
                -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $defaultStorageAccountKey


    # Add a script action to the cluster configuration
    $config = Add-AzureRmHDInsightScriptAction `
                -Config $config `
                -Name "Install Spark" `
                -NodeType HeadNode `
                -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1 `

    # Start creating a cluster with Spark installed
    New-AzureRmHDInsightCluster `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -Location $location `
            -ClusterSizeInNodes 2 `
            -ClusterType Hadoop `
            -OSType Windows `
            -DefaultStorageContainer $defaultBlobContainerName `
            -Config $config


その他のソフトウェアをインストールするには、スクリプトのスクリプト ファイルを置換する必要があります。

入力を求められたら、クラスターの資格情報を入力します。 クラスターが作成されるまでに数分かかる場合があります。

## <a name="call-scripts-using-net-sdk"></a>.NET SDK を使用してスクリプトを呼び出す
次のサンプルでは、Windows ベースの HDInsight クラスターに Spark をインストールする方法を示します。 その他のソフトウェアをインストールするには、コードのスクリプト ファイルを置換する必要があります。

**Spark で HDInsight クラスターを作成するには**

1. Visual Studio で、C# コンソール アプリケーションを作成します。
2. NuGet パッケージ マネージャー コンソールから、次のコマンドを実行します。

        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package Microsoft.Azure.Management.ResourceManager -Pre
        Install-Package Microsoft.Azure.Management.HDInsight
3. Program.cs ファイルで次の using ステートメントを使用します。

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
4. クラスのコードを次のコードに置き換えます。

        private static HDInsightManagementClient _hdiManagementClient;

        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId;
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumWorkerNodes = 2;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const string NewClusterType = "Hadoop";
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            CreateCluster();
        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumWorkerNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        /// <param name="TenantId">The AAD tenant ID</param>
        /// <param name="ClientId">The AAD client ID</param>
        /// <param name="SubscriptionId">The Azure subscription ID</param>
        /// <returns></returns>
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
5. **F5** キーを押してアプリケーションを実行します。

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>HDInsight クラスターで使用するオープン ソース ソフトウェアのサポート
Microsoft Azure HDInsight サービスは柔軟性に優れたプラットフォームであり、Hadoop を中心に形成されたオープン ソース テクノロジのエコシステムを利用し、クラウド内でビッグ データ アプリケーションを構築できます。 **Azure サポート FAQ Web サイト** の <a href="http://azure.microsoft.com/support/faq/" target="_blank">サポート範囲</a>のセクションでも説明しているように、Microsoft Azure では、オープン ソース テクノロジについて一般的なレベルのサポートを提供しています。 HDInsight サービスでは、次に説明するいくつかのコンポーネントについてさらに高いレベルのサポートを受けることができます。

HDInsight サービスで利用できるオープン ソース コンポーネントには、2 つの種類があります。

* **組み込みコンポーネント** - これらのコンポーネントは、HDInsight クラスターにプレインストールされており、クラスターの主要な機能を提供します。 たとえば、YARN リソース マネージャー、Hive クエリ言語 (HiveQL)、Mahout ライブラリなどがこのカテゴリに属します。 クラスター コンポーネントの完全な一覧は、「[HDInsight で提供される Hadoop クラスター バージョンの新機能](hdinsight-component-versioning.md)</a>」から入手できます。
* **カスタム コンポーネント** - クラスターのユーザーは、コミュニティで入手できるコンポーネントや自作のコンポーネントを、インストールするか、ワークロード内で使用することができます。

組み込みコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。

> [!WARNING]
> HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
>
> カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。 これにより問題が解決する場合もあれば、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。 たとえば、[HDInsight についての MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight)や [http://stackoverflow.com](http://stackoverflow.com) などの数多くのコミュニティ サイトを利用できます。 また、Apache プロジェクトには、[http://apache.org](http://apache.org) に [Hadoop](http://hadoop.apache.org/) や [Spark](http://spark.apache.org/) などのプロジェクト サイトがあります。
>
>

HDInsight サービスでは、カスタム コンポーネントを使用する方法をいくつか用意しています。 コンポーネントの用途やクラスターへのインストール方法にかかわらず、同じレベルのサポートが適用されます。 以下は、HDInsight クラスターでのカスタム コンポーネントの用途として、最も一般的な方法の一覧です。

1. ジョブの送信 - Hadoop や他の種類のジョブを、カスタム コンポーネントを実行または使用するクラスターに送信できます。
2. クラスターのカスタマイズ - クラスター作成時に、追加設定や、クラスター ノードにインストールするカスタム コンポーネントを指定できます。
3. サンプル - よく利用されるカスタム コンポーネントに対しては、それらを HDInsight クラスターで使用する方法について Microsoft やその他の提供者がサンプルを用意している場合があります。 これらのサンプルはサポートなしで提供されます。

## <a name="develop-script-action-scripts"></a>スクリプト アクションのスクリプトを開発する
「[HDInsight 用のスクリプト アクションのスクリプトを開発する][hdinsight-write-script]」を参照してください。

## <a name="see-also"></a>関連項目
* [HDInsight での Hadoop クラスターの作成][hdinsight-provision-cluster]に関する記事では、その他のカスタム オプションを使用して HDInsight クラスターを作成する方法について説明しています。
* [HDInsight 用のスクリプト アクションのスクリプトを開発する][hdinsight-write-script]
* [HDInsight クラスターで Spark をインストールして使用する][hdinsight-install-spark]
* [HDInsight クラスターに R をインストールして使用する][hdinsight-install-r]
* [HDInsight クラスターに Solr をインストールして使用する](hdinsight-hadoop-solr-install.md)
* [HDInsight クラスターに Giraph をインストールして使用する](hdinsight-hadoop-giraph-install.md)

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "クラスター作成時の段階"

