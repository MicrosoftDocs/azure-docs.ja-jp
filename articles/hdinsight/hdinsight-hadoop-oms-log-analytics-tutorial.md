---
title: Azure Monitor ログを使用して Azure HDInsight クラスターを監視する
description: Azure Monitor ログを使って HDInsight クラスターで実行中のジョブを監視する方法を説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli, devx-track-azurepowershell
ms.date: 05/13/2020
ms.openlocfilehash: 0dfa89f50dedad41394cb77f1cca9b2dd3a65308
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865538"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure Monitor ログを使用して HDInsight クラスターを監視する

Azure Monitor ログを有効にして、HDInsight で Hadoop クラスターの操作を監視する方法を説明します。 また、HDInsight 監視ソリューションを追加する方法について説明します。

[Azure Monitor ログ](../azure-monitor/logs/log-query-overview.md)は、クラウド環境とオンプレミス環境を監視する Azure Monitor サービスです。 この監視では、可用性とパフォーマンスを維持します。 クラウド、オンプレミス環境内にあるリソースによって生成されたデータと他の監視ツールのデータを収集します。 データは、複数のソースにわたる分析を提供するために使用されます。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

* Log Analytics ワークスペース。 このワークスペースは、独自のデータ リポジトリ、データ ソース、およびソリューションを備えた一意の Azure Monitor ログ環境と考えることができます。 手順については、[Log Analytics ワークスペースの作成](../azure-monitor/vm/quick-collect-azurevm.md#create-a-workspace)に関するページをご覧ください。

* Azure HDInsight クラスター。 現在、Azure Monitor ログは次の HDInsight クラスター タイプで使用できます。

  * Hadoop
  * hbase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  HDInsight クラスターの作成手順については、[Azure HDInsight の概要](hadoop/apache-hadoop-linux-tutorial-get-started.md)に関するページをご覧ください。  

* PowerShell を使用している場合は、[AZ モジュール](/powershell/azure/)が必要になります。 最新バージョンを使用していることを確認します。 必要であれば、`Update-Module -Name Az` を実行します。

* Azure CLI を使用したいが、まだインストールしていない場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

> [!NOTE]  
> パフォーマンス向上のため、HDInsight クラスターと Log Analytics ワークスペースの両方を同じリージョンに配置することをお勧めします。 Azure Monitor ログは、すべての Azure リージョンで使用できるわけではありません。

## <a name="enable-azure-monitor-using-the-portal"></a>ポータルを使用して Azure Monitor を有効にする

ここでは、ジョブやデバッグ ログなどを監視するために、Azure Log Analytics ワークスペースを使用するよう既存の HDInsight Hadoop クラスターを構成します。

1. [Azure portal](https://portal.azure.com/) でご自身のクラスターを選択します。 このクラスターは、新しいポータル ページで開かれます。

1. 左側の **[監視]** で **[Azure Monitor]** を選択します。

1. メイン ビューの **[Azure Monitor の統合]** で、 **[有効化]** を選択します。

1. **[ワークスペースを選択]** ドロップダウン リストから、既存の Log Analytics ワークスペースを選択します。

1. **[保存]** を選択します。  設定を保存するまでしばらく時間がかかります。

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png" alt-text="HDInsight クラスターの監視を有効にする":::

## <a name="enable-azure-monitor-using-azure-powershell"></a>Azure PowerShell を使用して Azure Monitor を有効にする

Azure PowerShell Az モジュールの [Enable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) コマンドレットを使用して Azure Monitor ログを有効にすることができます。

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

無効にする場合は、[Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) コマンドレットを使用します。

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Azure CLI を使用して Azure Monitor を有効にする

Azure CLI `[az hdinsight monitor enable`](/cli/azure/hdinsight/monitor#az-hdinsight-monitor-enable) コマンドを使用して、Azure Monitor ログを有効にすることができます。

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor show --name $cluster --resource-group $resourceGroup
```

無効にするには、[`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az-hdinsight-monitor-disable) コマンドを使用します。

```azurecli
az hdinsight monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="install-hdinsight-cluster-management-solutions"></a>HDInsight クラスター管理ソリューションをインストールする

HDInsight では、Azure Monitor ログに追加できるクラスター固有の管理ソリューションが提供されます。 [管理ソリューション](../azure-monitor/insights/solutions.md)を使用すると、Azure Monitor ログに機能を追加して、追加のデータおよび分析ツールを提供できます。 これらのソリューションでは、HDInsight クラスターから重要なパフォーマンス メトリックが収集されます。 メトリックを検索するためのツールが提供されます。 また、これらのソリューションは、HDInsight でサポートされるほとんどのクラスターの種類に対する視覚化とダッシュボードも提供します。 このソリューションで収集したメトリックを使用して、独自の監視ルールおよびアラートを作成できます。

利用可能な HDInsight ソリューションは次のとおりです。

* HDInsight Hadoop Monitoring
* HDInsight HBase の監視
* HDInsight Interactive Query Monitoring
* HDInsight Kafka Monitoring
* HDInsight Spark Monitoring
* HDInsight Storm Monitoring

管理ソリューションをインストールする手順については、[Azure の管理ソリューション](../azure-monitor/insights/solutions.md#install-a-monitoring-solution)に関する記事をご覧ください。 テスト用に HDInsight Hadoop Monitoring ソリューションをインストールします。 インストールが完了すると、 **[概要]** の下に **HDInsightHadoop** タイルが表示されます。 **HDInsightHadoop** タイルを選択します。 HDInsightHadoop ソリューションは次のようになります。

:::image type="content" source="media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png" alt-text="HDInsight 監視ソリューションのビュー":::

クラスターは新規のクラスターであるため、レポートにはアクティビティが表示されません。

## <a name="configuring-performance-counters"></a>パフォーマンス カウンターの構成

Azure Monitor では、クラスター内のノードのパフォーマンス メトリックの収集と分析がサポートされています。 詳細については、[Azure Monitor の Linux パフォーマンス データ ソース](../azure-monitor/agents/data-sources-performance-counters.md#linux-performance-counters)に関する記事をご覧ください。

## <a name="cluster-auditing"></a>クラスターの監査

HDInsight では、次の種類のログをインポートすることによって、Azure Monitor ログを使用したクラスターの監査をサポートしています。

* `log_gateway_audit_CL` - このテーブルは、成功/失敗したログイン試行を示すクラスター ゲートウェイ ノードの監査ログを提供します。
* `log_auth_CL` - このテーブルは、成功/失敗したログイン試行が含まれる SSH のログを提供します。
* `log_ambari_audit_CL` - このテーブルは、Ambari からの監査ログを提供します。
* `log_ranger_audti_CL` - このテーブルは、ESP クラスター上の Apache Ranger からの監査ログを提供します。

## <a name="next-steps"></a>次のステップ

* [Azure Monitor ログでクエリを実行して HDInsight クラスターを監視する](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [Apache Ambari と Azure Monitor ログを使用してクラスターの可用性を監視する方法](./hdinsight-cluster-availability.md)