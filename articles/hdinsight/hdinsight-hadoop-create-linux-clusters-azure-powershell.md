---
title: "PowerShell を使用して Azure HDInsight (Hadoop) を作成する | Microsoft Docs"
description: "Azure PowerShell を使用して、HDInsight 用の Linux ベースの Hadoop、HBase、Storm、または Spark クラスターを作成する方法について説明します。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4208deca-d64a-45e1-8948-2673d5d7678c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 44e418e52fc18dd22820331f7d921e789da62832
ms.lasthandoff: 03/25/2017


---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Azure PowerShell を使用した HDInsight の Linux ベースのクラスターの作成
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell は、Microsoft Azure のワークロードのデプロイと管理を制御し、自動化するために使用できる強力なスクリプト作成環境です。 このドキュメントでは、Azure PowerShell を使用して Linux ベースの HDInsight クラスターを作成する方法について説明します。 また、スクリプトの例も含まれています。

> [!NOTE]
> Azure PowerShell は、Windows クライアントだけで利用できます。 Linux、Unix、または Mac OS X クライアントを使用している場合、Azure CLI を使用したクラスターの作成方法については、 [Azure CLI を使用した Linux ベースの HDInsight クラスターの作成](hdinsight-hadoop-create-linux-clusters-azure-cli.md) に関する記事をご覧ください。
> 
> 

## <a name="prerequisites"></a>前提条件
この手順を開始するには、以下が必要です。

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* Azure PowerShell。
    HDInsight での Azure PowerShell の使用方法の詳細については、 [PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)に関する記事をご覧ください。 HDInsight Windows PowerShell コマンドレットの一覧については、 [HDInsight コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/dn858087.aspx)をご覧ください。
  
    > [!IMPORTANT]
    > Azure Service Manager を使用した HDInsight リソースの管理に関する Azure PowerShell のサポートは**廃止**され、2017 年 1 月 1 日に削除されました。 このドキュメントの手順では、Azure Resource Manager で機能する新しい HDInsight コマンドレットを使用します。
    > 
    > [Azure PowerShell のインストールと構成](/powershell/azureps-cmdlets-docs) に関するページの手順に従い、Azure PowerShell の最新バージョンをインストールしてください。 Azure Resource Manager で機能する新しいコマンドレットを使用するようにスクリプトを変更する必要がある場合、詳細については、「 [Migrating to Azure Resource Manager-based development tools for HDInsight clusters (HDInsight クラスターの Azure Resource Manager ベースの開発ツールへの移行)](hdinsight-hadoop-development-using-azure-resource-manager.md) 」をご覧ください。
    > 
    > 

### <a name="access-control-requirements"></a>アクセス制御の要件
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>クラスターの作成
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure PowerShell を使用して HDInsight クラスターを作成するには、次の手順を完了する必要があります。

* Azure リソース グループの作成
* Azure Storage アカウントの作成
* Azure BLOB コンテナーの作成
* HDInsight クラスターの作成

Linux クラスターを作成するために設定する必要がある最も重要な 2 つのパラメーターは、OS の種類と SSH ユーザーの詳細を指定するパラメーターです。

* **-OSType** パラメーターを **Linux** として指定していることを確認します。
* クラスターのリモート セッションで SSH を使用するには、SSH ユーザー パスワードまたは SSH 公開キーを指定します。 SSH ユーザー パスワードと SSH 公開キーを両方とも指定すると、キーが無視されます。 リモート セッションで SSH キーを使用する場合は、入力を求められたら、空の SSH パスワードを指定する必要があります。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

次のスクリプトでは、新しいクラスターを作成する方法を示します。

    $token ="<SpecifyAnUniqueString>"
    $subscriptionID = "<SubscriptionName>"        # Provide your Subscription Name

    $resourceGroupName = $token + "rg"      # Provide a Resource Group name
    $clusterName = $token
    $defaultStorageAccountName = $token + "store"   # Provide a Storage account name
    $defaultStorageContainerName = $token + "container"
    $location = "East US 2"     # Change the location if needed
    $clusterNodes = 1           # The number of nodes in the HDInsight cluster

    # Sign in to Azure
    Login-AzureRmAccount

    # Select the subscription to use if you have multiple subscriptions
    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    # Create an Azure Resource Group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account and container used as the default storage
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $defaultStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultStorageContainerName -Context $destContext

    # Create an HDInsight cluster
    $credentials = Get-Credential -Message "Enter Cluster user credentials" -UserName "admin"
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials"

    # The location of the HDInsight cluster must be in the same data center as the Storage account.
    $location = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $defaultStorageAccountName | %{$_.Location}

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials

**$clusterCredentials** に指定する値は、クラスターの Hadoop ユーザー アカウントの作成に使用されます。 このアカウントを使用してクラスターに接続します。

**$sshCredentials** に指定する値は、クラスターの SSH ユーザーの作成に使用されます。 このアカウントを使用してクラスターでリモート SSH セッションを開始し、ジョブを実行します。

> [!IMPORTANT]
> このスクリプトで、クラスターに存在する worker ノードの数を指定する必要があります。 (クラスターの作成時または作成後のスケーリングで) 32 個を超える worker ノードを使用することを計画している場合は、コア数が 8 個以上、RAM が 14 GB 以上のヘッド ノード サイズも指定する必要があります。
> 
> ノードのサイズと関連コストに関する詳細については、「 [HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。
> 
> 

クラスターを作成するには最大 20 分かかることがあります。

次の例では、追加のストレージ アカウントを追加する方法を示します。

    # Create another storage account used as additional storage account
    $additionalStorageAccountName = $token + "store2"
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $additionalStorageAccountName -Location $location -Type Standard_LRS
    $additionalStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $additionalStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value

    $config = New-AzureRmHDInsightClusterConfig
    Add-AzureRmHDInsightStorage -Config $config -StorageAccountName "$additionalStorageAccountName.blob.core.windows.net" -StorageAccountKey $additionalStorageAccountKey

    # Create a new HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials `
        -Config $config

## <a name="customize-clusters"></a>クラスターのカスタマイズ
* 「 [ブートストラップを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell)」を参照してください。
* 「[スクリプト アクションを使用して HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。

## <a name="delete-the-cluster"></a>クラスターを削除する
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>次のステップ
HDInsight クラスターが正常に作成されました。次に、クラスターの使用方法について、以下のリソースを参照してください。

### <a name="hadoop-clusters"></a>Hadoop クラスター
* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase クラスター
* [HDInsight での HBase の使用](hdinsight-hbase-tutorial-get-started-linux.md)
* [HDInsight での HBase の Java アプリケーションの開発](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm クラスター
* [HDInsight での Storm の Java トポロジの開発](hdinsight-storm-develop-java-topology.md)
* [HDInsight の Storm での Python コンポーネントの使用](hdinsight-storm-develop-python-topology.md)
* [HDInsight の Storm を使用したトポロジのデプロイと監視](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Spark クラスター
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark と BI: HDInsight で BI ツールと Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)


