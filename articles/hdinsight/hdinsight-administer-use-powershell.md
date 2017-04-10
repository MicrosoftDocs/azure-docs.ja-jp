---
title: "PowerShell を使用した HDInsight での Hadoop クラスターの管理 | Microsoft Docs"
description: "HDInsight の Hadoop クラスターを Azure PowerShell を使用して管理する方法について説明します。"
services: hdinsight
editor: cgronlun
manager: jhubbard
tags: azure-portal
author: mumian
documentationcenter: 
ms.assetid: bfdfa754-18e5-4ef9-b0d6-2dbdcebc0283
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 0587dfcd6079fc8df91bad5a5f902391d3657a6b
ms.openlocfilehash: af9e0b7c0f7077b8d4e20ccafdc1fb4e1eb58505
ms.lasthandoff: 12/08/2016


---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Azure PowerShell を使用した HDInsight での Hadoop クラスターの管理
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell は、Azure のワークロードのデプロイと管理を制御し自動化するために使用できる強力なスクリプティング環境です。 この記事では、ローカルの Azure PowerShell コンソールを使って Azure HDInsight で Hadoop クラスターを管理する方法を紹介します。例として、Windows PowerShell を使用します。 HDInsight PowerShell コマンドレットの一覧については、[HDInsight コマンドレット リファレンス][hdinsight-powershell-reference]を参照してください。

**前提条件**

この記事を読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

## <a name="install-azure-powershell"></a>Azure PowerShell をインストールするには
[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Azure PowerShell バージョン 0.9x をインストールしている場合は、新しいバージョンをインストールする前に、アンインストールする必要があります。

インストールされている PowerShell のバージョンを確認するには:

    Get-Module *azure*

以前のバージョンをアンインストールするには、コントロール パネルで [プログラムと機能] を実行します。 

## <a name="create-clusters"></a>クラスターの作成
[Azure PowerShell を使用した HDInsight の Linux ベースのクラスターの作成](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>クラスターの一覧表示
現在のサブスクリプションにあるクラスターすべてを一覧表示するには次のコマンドを使用します。

    Get-AzureRmHDInsightCluster

## <a name="show-cluster"></a>クラスターの表示
現在のサブスクリプションにある特定のクラスターの詳細を表示するには次のコマンドを使用します。

    Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>

## <a name="delete-clusters"></a>クラスターの削除
クラスターを削除するには、次のコマンドを使用します。

    Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>

クラスターが含まれるリソース グループを削除して、クラスターを削除することもできます。 グループを削除すると、既定のストレージ アカウントを含め、グループ内のすべてのリソースが削除されることに注意してください。

    Remove-AzureRmResourceGroup -Name <Resource Group Name>

## <a name="scale-clusters"></a>クラスターのスケール
クラスターのスケール設定機能を使用すると、Azure HDInsight で実行しているクラスターによって使用される worker ノードの数を、クラスターを再作成することなく、変更できます。

> [!NOTE]
> HDInsight バージョン 3.1.3 以降を使用しているクラスターのみがサポートされます。 クラスターのバージョンがわからない場合、[プロパティ] ページを確認できます。  「[クラスターの一覧と表示](hdinsight-administer-use-portal-linux.md#list-and-show-clusters)」を参照してください。
> 
> 

HDInsight でサポートされているクラスターの種類ごとに、データ ノード数を変更した場合の影響:

* Hadoop は、
  
    保留中または実行中のジョブに影響を与えることなく、実行中の Hadoop クラスター内の worker ノードの数をシームレスに増加できます。 処理の進行中に新しいジョブを送信することもできます。 スケール設定処理の失敗は正常に処理され、クラスターは常に機能状態になります。
  
    データ ノードの数を減らして Hadoop クラスターのスケールを小さくした場合、クラスター内の一部のサービスが再起動されます。 これにより、スケール設定処理の完了時に、実行中および保留中のすべてのジョブが失敗します。 ただし、処理が完了した後にジョブを再送信できます。
* HBase
  
    実行中の HBase クラスターに対して、ノードの追加または削除をシームレスに実行できます。 地域サーバーは、スケール設定処理の完了の数分以内に自動的に分散されます。 ただし、クラスターのヘッドノードにログインし、コマンド プロンプト ウィンドウから次のコマンドを実行して、地域サーバーを手動で分散することもできます。
  
        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer
* Storm
  
    実行中の Storm クラスターに対して、データ ノードの追加または削除をシームレスに実行できます。 ただし、スケール設定処理が正常に完了した後、トポロジのバランス再調整が必要になります。
  
    バランス再調整は、次の 2 つの方法で実行できます。
  
  * Storm Web UI
  * コマンド ライン インターフェイス (CLI) ツール
    
    詳細については、 [Apache Storm に関するドキュメント](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) をご覧ください。
    
    Storm Web UI は、HDInsight クラスターで使用できます。
    
    ![HDInsight Storm の規模のバランス調整](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)
    
    CLI コマンドを使用して Storm トポロジのバランスを再調整する方法を次の例で示します。
    
        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors
        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

Azure PowerShell を使用して Hadoop クラスターのサイズを変更するには、クライアント コンピューターから次のコマンドを実行します。

    Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>


## <a name="grantrevoke-access"></a>アクセス権の付与/取り消し
HDInsight クラスターには、以下の HTTP Web サービスがあります (これらすべてのサービスには、REST ベースのエンドポイントがあります)。

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

既定では、これらのサービスへのアクセス許可が付与されます。 アクセス許可を取り消す/付与することができます。 取り消するには、次を実行します。

    Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>

許可するには、次を実行します。

    $clusterName = "<HDInsight Cluster Name>"

    # Credential option 1
    $hadoopUserName = "admin"
    $hadoopUserPassword = "<Enter the Password>"
    $hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

    # Credential option 2
    #$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

    Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential

> [!NOTE]
> アクセス許可を付与するか、取り消すことで、クラスターのユーザー名とパスワードがリセットされます。
> 
> 

これは、ポータルを使用して行うこともできます。 [Azure Portal ポータルを使用した HDInsight の管理][hdinsight-admin-portal]に関するページをご覧ください。

## <a name="update-http-user-credentials"></a>HTTP ユーザーの資格情報の更新
[HTTP アクセス許可の付与/取り消し](#grant/revoke-access)と同じ手順です。HTTP アクセスがクラスターで許可されている場合、最初に取り消す必要があります。  次に、新しい HTTP ユーザーの資格情報を使用してアクセス許可を付与してください。

## <a name="find-the-default-storage-account"></a>既定のストレージ アカウントの検索
次の Powershell スクリプトでは、クラスターの既定のストレージ アカウント名と既定のストレージ アカウント キーを取得する方法を示します。

    $clusterName = "<HDInsight Cluster Name>"

    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup
    $defaultStorageAccountName = ($cluster.DefaultStorageAccount).Replace(".blob.core.windows.net", "")
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 

## <a name="find-the-resource-group"></a>リソース グループの検索
Resource Manager モードでは、各 HDInsight クラスターは Azure リソース グループに属しています。  リソース グループを検索するには:

    $clusterName = "<HDInsight Cluster Name>"

    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup


## <a name="submit-jobs"></a>ジョブの送信
**MapReduce ジョブを送信するには**

「 [Windows ベースの HDInsight での Hadoop MapReduce サンプルの実行](hdinsight-run-samples.md)」を参照してください。

**Hive ジョブを送信するには** 

「 [PowerShell を使用して Hive クエリを実行](hdinsight-hadoop-use-hive-powershell.md)」を参照してください。

**Pig ジョブを送信するには**

「 [PowerShell を使用した Pig ジョブの実行](hdinsight-hadoop-use-pig-powershell.md)」を参照してください。

**Sqoop ジョブを送信するには**

「 [Hadoop .NET SDK と HDInsight の使用](hdinsight-use-sqoop.md)」を参照してください。

**Oozie ジョブを送信するには**

「 [HDInsight での Oozie と Hadoop を使用したワークフローの定義と実行](hdinsight-use-oozie.md)」を参照してください。

## <a name="upload-data-to-azure-blob-storage"></a>Azure BLOB ストレージにデータをアップロードする
[HDInsight へのデータのアップロード][hdinsight-upload-data]に関するページを参照してください。

## <a name="see-also"></a>関連項目
* [HDInsight コマンドレット リファレンス ドキュメント][hdinsight-powershell-reference]
* [Azure Portal を使用した HDInsight の管理][hdinsight-admin-portal]
* [コマンド ライン インターフェイスを使用した HDInsight の管理][hdinsight-admin-cli]
* [HDInsight クラスターの作成][hdinsight-provision]
* [HDInsight へのデータのアップロード][hdinsight-upload-data]
* [プログラムによる Hadoop ジョブの送信][hdinsight-submit-jobs]
* [Azure HDInsight の概要][hdinsight-get-started]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png

