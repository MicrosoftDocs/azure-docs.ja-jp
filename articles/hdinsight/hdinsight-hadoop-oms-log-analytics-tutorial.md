---
title: Azure Monitor ログを使用して Azure HDInsight クラスターを監視する
description: Azure Monitor ログを使って HDInsight クラスターで実行中のジョブを監視する方法を説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: hrasheed
ms.openlocfilehash: 399a6bcb6e0bfd0edaddca471ba2c8e0802d3394
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58904782"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure Monitor ログを使用して HDInsight クラスターを監視する

Azure Monitor ログを有効にして HDInsight で Hadoop クラスターの操作を監視する方法と、HDInsight 監視ソリューションを追加する方法を説明します。

[Azure Monitor ログ](../log-analytics/log-analytics-overview.md)は、可用性やパフォーマンスの維持を目的としてクラウド環境とオンプレミス環境を監視する Azure Monitor のサービスです。 Log Analytics を使用すると、クラウドおよびオンプレミスの環境内にあるリソースによって生成されたデータや、他の監視ツールのデータを収集し、複数のソースにわたる分析を行えます。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

* **Log Analytics ワークスペース**。 このワークスペースは、独自のデータ リポジトリ、データ ソース、およびソリューションを備えた一意の Azure Monitor ログ環境と考えることができます。 手順については、[Log Analytics ワークスペースの作成](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace)に関するページをご覧ください。

* **Azure HDInsight クラスター**。 現在、Azure Monitor ログは次の HDInsight クラスター タイプで使用できます。

  * Hadoop
  * hbase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  HDInsight クラスターの作成手順については、[Azure HDInsight の概要](hadoop/apache-hadoop-linux-tutorial-get-started.md)に関するページをご覧ください。  

* **Azure PowerShell Az モジュール**。  「[新しい Azure PowerShell Az モジュールの概要](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)」を参照してください。

> [!NOTE]  
> パフォーマンス向上のため、HDInsight クラスターと Log Analytics ワークスペースの両方を同じリージョンに配置することをお勧めします。 Azure Monitor ログは、すべての Azure リージョンで使用できるわけではありません。

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>ポータルを使用した Azure Monitor ログの有効化

ここでは、ジョブやデバッグ ログなどを監視するために、Azure Log Analytics ワークスペースを使用するよう既存の HDInsight Hadoop クラスターを構成します。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. 左側のメニューから、**[すべてのサービス]** を選択します。

3. **[ANALYTICS]** で **[HDInsight クラスター]** を選択します。

4. 一覧からクラスターを選択します。

5. 左側から、**[監視]** の下にある **[Operations Management Suite]** を選択します。

6. メイン ビューから、**[OMS 監視]** の下にある **[有効化]** を選択します。

7. **[ワークスペースを選択]** ドロップダウン リストから、既存の Log Analytics ワークスペースを選択します。

8. **[保存]** を選択します。  設定を保存するまでしばらく時間がかかります。

    ![HDInsight クラスターの監視の有効化](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "HDInsight クラスターの監視の有効化")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Azure PowerShell を使用した Azure Monitor ログの有効化

Azure PowerShell Az モジュールの [Enable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) コマンドレットを使用して Azure Monitor ログを有効にすることができます

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables Operations Management Suite
Enable-AzHDInsightOperationsManagementSuite -ResourceGroupName $resourceGroup -Name $cluster -WorkspaceId $WorkspaceId -PrimaryKey $PrimaryKey
```

無効にする場合は、[Disable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightoperationsmanagementsuite) コマンドレットを使用します。

```powershell
Disable-AzHDInsightOperationsManagementSuite -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>HDInsight クラスター管理ソリューションをインストールする

HDInsight では、Azure Monitor ログに追加できるクラスター固有の管理ソリューションが提供されます。 [管理ソリューション](../log-analytics/log-analytics-add-solutions.md)を使用すると、Azure Monitor ログに機能を追加して、追加のデータおよび分析ツールを提供できます。 これらのソリューションは、HDInsight クラスターから重要なパフォーマンス メトリックを収集し、メトリックを検索するツールを提供します。 また、これらのソリューションは、HDInsight でサポートされるほとんどのクラスターの種類に対する視覚化とダッシュボードも提供します。 このソリューションで収集したメトリックを使用して、独自の監視ルールおよびアラートを作成できます。

利用可能な HDInsight ソリューションは次のとおりです。

* HDInsight Hadoop Monitoring
* HDInsight HBase の監視
* HDInsight Interactive Query Monitoring
* HDInsight Kafka Monitoring
* HDInsight Spark Monitoring
* HDInsight Storm Monitoring

管理ソリューションをインストールする手順については、「[Azure の管理ソリューション](../azure-monitor/insights/solutions.md#install-a-monitoring-solution)」をご覧ください。 テスト用に HDInsight Hadoop Monitoring ソリューションをインストールします。 インストールが完了すると、**[概要]** の下に **HDInsightHadoop** タイルが表示されます。 **HDInsightHadoop** タイルを選択します。 HDInsightHadoop ソリューションは次のようになります。

![HDInsight 監視ソリューションのビュー](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

クラスターは新規のクラスターであるため、レポートにはアクティビティが表示されません。

## <a name="next-steps"></a>次の手順

* [Azure Monitor ログでクエリを実行して HDInsight クラスターを監視する](hdinsight-hadoop-oms-log-analytics-use-queries.md)
