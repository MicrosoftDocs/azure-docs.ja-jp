---
title: ブートストラップを使用して Azure HDInsight クラスター構成をカスタマイズする
description: .NET、PowerShell、および Resource Manager テンプレートを使用して、HDInsight クラスター構成をプログラムでカスタマイズする方法を説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/01/2020
ms.openlocfilehash: 55792cc1890702b904db00b7e3cb3ac3a78c63f1
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866524"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>ブートストラップを使って HDInsight クラスターをカスタマイズする

ブートストラップ スクリプトを使用すると、Azure HDInsight のコンポーネントをプログラムでインストールおよび構成できます。

HDInsight クラスターの作成時に構成ファイル設定を指定する方法は、次の 3 つです。

* Azure PowerShell の使用
* .NET SDK の使用
* Azure Resource Manager テンプレートの使用

たとえば、これらのプログラムによる方法を使用して、以下のファイルのオプションを構成できます。

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* server.properties (kafka-broker 構成)

作成時に追加コンポーネントを HDInsight クラスターにインストールする方法の詳細については、[スクリプト アクションを使用した HDInsight クラスターのカスタマイズ (Linux)](hdinsight-hadoop-customize-cluster-linux.md) に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* PowerShell を使用している場合は、[AZ モジュール](/powershell/azure/)が必要になります。

## <a name="use-azure-powershell"></a>Azure PowerShell の使用

次の PowerShell コードでは、[Apache Hive](https://hive.apache.org/) 構成をカスタマイズします。

> [!IMPORTANT]  
> [Add-Add-AzHDInsightConfigValue](/powershell/module/az.hdinsight/add-azhdinsightconfigvalue) と共にパラメーター `Spark2Defaults` を使うことが必要な場合があります。 以下のコード例に示すように、パラメーターに空の値を渡すことができます。

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90s" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config
```

完全な作業 PowerShell スクリプトについては、「[付録](#appendix-powershell-sample)」をご覧ください。

**接続を確認するには:**

1. `https://CLUSTERNAME.azurehdinsight.net/` に移動します。`CLUSTERNAME` はクラスターの名前です。
1. 左側のメニューから **[Hive]**  >  **[Configs]\(構成\)**  >  **[Advanced]\(詳細\)** に移動します。
1. **[Advanced hive-site]\(詳細な Hive サイト\)** を展開します。
1. **hive.metastore.client.socket.timeout** を探し、値が **90s** であることを確認します。

他の構成ファイルのカスタマイズのサンプルを次に示します。

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```

## <a name="use-net-sdk"></a>.NET SDK の使用

「[Azure HDInsight SDK for .NET](/dotnet/api/overview/azure/hdinsight)」を参照してください。

## <a name="use-resource-manager-template"></a>Resource Manager テンプレートの使用

Resource Manager テンプレートでは、ブートストラップを使用できます。

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

:::image type="content" source="./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png" alt-text="Hadoop でクラスター ブートストラップの Azure Resource Manager テンプレートをカスタマイズする":::

定期的にイベント ログをストレージからクリーンアップするように spark2-defaults の構成を切り替える Resource Manager テンプレート スニペットの例を次に示します。  

```json
"configurations": {
    "spark2-defaults": {
        "spark.history.fs.cleaner.enabled": "true",
        "spark.history.fs.cleaner.interval": "7d",
        "spark.history.fs.cleaner.maxAge": "90d"
    }
}
```

## <a name="see-also"></a>関連項目

* [HDInsight での Apache Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)に関する記事では、その他のカスタム オプションを使用して HDInsight クラスターを作成する方法について説明しています。
* [HDInsight 用の Script Action スクリプトの開発](hdinsight-hadoop-script-actions-linux.md)
* [HDInsight クラスターで Apache Spark をインストールして使用する](spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [HDInsight クラスターに Apache Giraph をインストールして使用する](./hdinsight-hadoop-hue-linux.md)。

## <a name="appendix-powershell-sample"></a>付録: PowerShell のサンプル

この PowerShell スクリプトでは、HDInsight クラスターを作成し、Hive の設定をカスタマイズします。 必ず `$nameToken`、`$httpPassword`、および `$sshPassword` の値を入力してください。

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>"
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>'

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>'
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

# Note: Storage account kind BlobStorage cannot be used as primary storage.

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90s"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```