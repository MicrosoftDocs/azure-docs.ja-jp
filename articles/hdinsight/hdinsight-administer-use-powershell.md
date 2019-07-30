---
title: PowerShell を使用して HDInsight の Apache Hadoop クラスターを管理する - Azure
description: Azure PowerShell を使用して HDInsight の Apache Hadoop クラスターに対する管理タスクを実行する方法について説明します。
ms.reviewer: tyfox
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: tyfox
ms.openlocfilehash: 2035f5c7666d93068fd8007555bfe1aadae58ff1
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67873934"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Azure PowerShell を使用して HDInsight の Apache Hadoop クラスターを管理する
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell を使用して、Azure のワークロードのデプロイと管理を制御し自動化することができます。 この記事では、Azure PowerShell Az モジュールを使用して Azure HDInsight の [Apache Hadoop](https://hadoop.apache.org/) クラスターを管理する方法について説明します。 HDInsight PowerShell コマンドレットの一覧については、[Az.HDInsight リファレンス](https://docs.microsoft.com/powershell/module/az.hdinsight)を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

* インストール済みの PowerShell [Az モジュール](https://docs.microsoft.com/powershell/azure/overview)。

## <a name="create-clusters"></a>クラスターの作成
[Azure PowerShell を使用した HDInsight の Linux ベースのクラスターの作成](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>クラスターの一覧表示
現在のサブスクリプションにあるクラスターすべてを一覧表示するには次のコマンドを使用します。

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>クラスターの表示
現在のサブスクリプションにある特定のクラスターの詳細を表示するには次のコマンドを使用します。

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>クラスターの削除
クラスターを削除するには、次のコマンドを使用します。

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

クラスターが含まれるリソース グループを削除して、クラスターを削除することもできます。 リソース グループを削除すると、既定のストレージ アカウントを含め、グループ内のすべてのリソースが削除されます。

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>クラスターのスケール
クラスターのスケール設定機能を使用すると、Azure HDInsight で実行しているクラスターによって使用される worker ノードの数を、クラスターを再作成することなく、変更できます。

HDInsight でサポートされているクラスターの種類ごとに、データ ノード数を変更した場合の影響:

* Apache Hadoop

    保留中または実行中のジョブに影響を与えることなく、実行中の Hadoop クラスター内の worker ノードの数をシームレスに増加できます。 処理の進行中に新しいジョブを送信することもできます。 スケール設定処理の失敗は正常に処理され、クラスターは常に機能状態になります。

    データ ノードの数を減らして Hadoop クラスターのスケールを小さくした場合、クラスター内の一部のサービスが再起動されます。 サービスを再起動すると、スケール設定処理の完了時に、実行中および保留中のすべてのジョブが失敗します。 ただし、処理が完了した後にジョブを再送信できます。
* Apache HBase

    実行中の HBase クラスターに対して、ノードの追加または削除をシームレスに実行できます。 地域サーバーは、スケール設定処理の完了の数分以内に自動的に分散されます。 ただし、クラスターのヘッドノードにログインし、コマンド プロンプト ウィンドウから次のコマンドを実行して、地域サーバーを手動で分散することもできます。

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

* Apache Storm

    実行中の Storm クラスターに対して、データ ノードの追加または削除をシームレスに実行できます。 ただし、スケール設定処理が正常に完了した後、トポロジのバランス再調整が必要になります。

    バランス再調整は、次の 2 つの方法で実行できます。

  * Storm Web UI
  * コマンド ライン インターフェイス (CLI) ツール

    詳細については、[Apache Storm に関するドキュメント](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)を参照してください。

    Storm Web UI は、HDInsight クラスターで使用できます。

    ![HDInsight Storm の規模のバランス調整](./media/hdinsight-administer-use-powershell/hdinsight.portal.scale.cluster.png)

    CLI コマンドを使用して Storm トポロジのバランスを再調整する方法を次の例で示します。

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Azure PowerShell を使用して Hadoop クラスターのサイズを変更するには、クライアント コンピューターから次のコマンドを実行します。

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


## <a name="grantrevoke-access"></a>アクセス権の付与/取り消し
HDInsight クラスターには、以下の HTTP Web サービスがあります (これらすべてのサービスには、REST ベースのエンドポイントがあります)。

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

既定では、これらのサービスへのアクセス許可が付与されます。 アクセス許可を取り消す/付与することができます。 取り消するには、次を実行します。

```powershell
Revoke-AzHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

許可するには、次を実行します。

```powershell
$clusterName = "<HDInsight Cluster Name>"

# Credential option 1
$hadoopUserName = "admin"
$hadoopUserPassword = "<Enter the Password>"
$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

# Credential option 2
#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Grant-AzHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]  
> アクセス許可を付与するか、取り消すことで、クラスターのユーザー名とパスワードがリセットされます。

アクセス許可の付与および取り消しは、ポータルを使用して行うこともできます。 「[Azure portal を使用して HDInsight の Apache Hadoop クラスターを管理する](hdinsight-administer-use-portal-linux.md)」を参照してください。

## <a name="update-http-user-credentials"></a>HTTP ユーザーの資格情報の更新
HTTP アクセスの付与/取り消しと同じ手順です。 クラスターに HTTP アクセスが許可されている場合は、まずそれを取り消す必要があります。  次に、新しい HTTP ユーザーの資格情報を使用してアクセス許可を付与してください。

## <a name="find-the-default-storage-account"></a>既定のストレージ アカウントの検索
次の PowerShell スクリプトでは、既定のストレージ アカウント名と関連情報を取得する方法を示します。

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```


## <a name="find-the-resource-group"></a>リソース グループの検索
Resource Manager モードでは、各 HDInsight クラスターは Azure リソース グループに属しています。  リソース グループを検索するには:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```


## <a name="submit-jobs"></a>ジョブの送信
**MapReduce ジョブを送信するには**

「[HDInsight に含まれる MapReduce サンプルを実行する](hadoop/apache-hadoop-run-samples-linux.md)」を参照してください。

**Apache Hive ジョブを送信するには**

「[PowerShell を使用して Apache Hive クエリを実行する](hadoop/apache-hadoop-use-hive-powershell.md)」を参照してください。

**Apache Sqoop ジョブを送信するには**

[HDInsight での Apache Sqoop の使用](hadoop/hdinsight-use-sqoop.md)に関するページを参照してください。

**Apache Oozie ジョブを送信するには**

「[HDInsight での Oozie と Hadoop を使用したワークフローの定義と実行](hdinsight-use-oozie-linux-mac.md)」を参照してください。

## <a name="upload-data-to-azure-blob-storage"></a>Azure BLOB ストレージにデータをアップロードする

[HDInsight へのデータのアップロード](hdinsight-upload-data.md)に関するページを参照してください。

## <a name="see-also"></a>関連項目

* [HDInsight コマンドレット リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn479228.aspx)
* [Azure portal を使用して HDInsight の Apache Hadoop クラスターを管理する](hdinsight-administer-use-portal-linux.md)
* [コマンド ライン インターフェイスを使用した HDInsight の管理](hdinsight-administer-use-command-line.md)
* [HDInsight クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)
* [プログラムによる Apache Hadoop ジョブの送信](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Azure HDInsight の概要](hadoop/apache-hadoop-linux-tutorial-get-started.md)
