---
title: クラスター管理のベスト プラクティス - Azure HDInsight
description: HDInsight クラスターを管理するためのベストプラクティスを学習する。
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 40222b6a108976de9c82ffccee119b1c1c55f334
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505751"
---
# <a name="hdinsight-cluster-management-best-practices"></a>HDInsight クラスター管理のベスト プラクティス

HDInsight クラスターを管理するためのベストプラクティスを学習する。

## <a name="how-do-i-create-hdinsight-clusters"></a>HDInsight クラスターの作成方法？

| オプション | Documents |
|---|---|
| Azure Data Factory | [Azure Data Factory を使用して HDInsight でオンデマンドの Apache Hadoop クラスターを作成する](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| カスタム Resource Manager テンプレート | [Resource Manager テンプレートを使用して、HDInsight で Apache Hadoop クラスターを作成する](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| クイック スタート テンプレート | [HDInsight クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| Azure サンプル | [HDInsight Azure のサンプル](/samples/browse/?products=azure-hdinsight) |
| Azure portal | [Azure portal を使用して、HDInsight で Linux ベースのクラスターを作成する](./spark/apache-spark-intellij-tool-plugin.md) |
| Azure CLI | [Azure CLI を使用した HDInsight クラスターを作成する](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Azure PowerShell を使用した HDInsight の Linux ベースのクラスターを作成する](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Azure REST API を使用して Apache Hadoop クラスターを作成する](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| SDKs (.NET, Python, Java) | [.NET](/dotnet/api/overview/azure/hdinsight), [Python](/python/api/overview/azure/hdinsight), [Java](/java/api/overview/azure/hdinsight), [Go](./hdinsight-go-sdk-overview.md) |

> [!Note]
> クラスターを作成し、以前に作成したクラスターからクラスター名を再利用する場合は、以前のクラスターの削除が完了するまで待ってからクラスターを作成してください。

## <a name="how-do-i-customize-hdinsight-clusters"></a>HDInsight クラスターのカスタマイズ方法は?

| オプション | Documents |
|---|---|
| スクリプト操作 | [スクリプト アクションを使用して Azure HDInsight クラスターをカスタマイズする](./hdinsight-hadoop-customize-cluster-linux.md) |
| ブートストラップ | [ブートストラップを使って HDInsight クラスターをカスタマイズする](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| 外部メタストア | [Azure HDInsight で外部メタデータ ストアを使用する](./hdinsight-use-external-metadata-stores.md) |
| カスタム Ambari DB | [カスタム Ambari DB を使用して HDInsight クラスターを設定する](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>クラスターを作成時に遭遇する可能性のあるエラーは何ですか？

| エラー | 詳細情報 |
|---|---|
| クォータなし | 各リージョンのサブスクリプションで作成できるコア数にはクォータがあります。 詳細については、「[容量計画：割り当て](./hdinsight-capacity-planning.md)」を参照してください。 |
| 使用可能な IP アドレスがこれ以上ありません | 各 VNet には、IP アドレスの数の制限があります。 HDInsightクラスターを作成すると、各ノード（zookeeper およびゲートウェイノードを含む）は、これらの割り当てられた IP アドレスの一部を使用します。 すべての IP アドレスが使用されている場合は、次のエラーが発生します。  |
| ネットワークセキュリティグループ (NSG) ルールにより、HDInsight リソースプロバイダーとの通信が許可されません | NSG またはユーザー定義ルート (UDR) を使用して、HDInsight クラスターへの受信トラフィックを制御する場合、クラスターが Azure の重要な正常性サービスと管理サービスと通信できるようにする必要があります。 詳細については、「[Azure HDInsight のネットワークセキュリティグループ（NSG）サービスタグ](./hdinsight-service-tags.md)」を参照してください |
| クラスター名の再利用 | 以前に使用したクラスター名を使用する場合は、クラスターを再作成する前に X 分間待機する必要があります。 そうしないと、リソースが既に存在するというメッセージが表示されます。 |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>実行中の HDInsight クラスターを管理する方法は?

| オプション | Documents |
|---|---|
| 自動スケール | [Azure HDInsight クラスターを自動的にスケーリングする](./hdinsight-autoscale-clusters.md) |
| 手動スケーリング | [Azure HDInsight クラスターのスケーリング](./hdinsight-scaling-best-practices.md) |
| Ambari を使用した監視| [Azure HDInsight でクラスター パフォーマンスを監視する](./hdinsight-key-scenarios-to-monitor.md) |
| Azure Monitor ログでの監視 | [Azure Monitor ログを使用して Azure HDInsight クラスターを監視する](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |
| サービスの問題、計画メンテナンス、正常性およびセキュリティに関する勧告 | [サブスクリプション固有のサービス正常性アラートをサブスクライブする](../service-health/alerts-activity-log-service-notifications-portal.md) |


## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>削除された HDInsight クラスターを確認する方法ha?

### <a name="azure-monitor-logs"></a>Azure Monitor ログ

Azure Monitor ログで、次のクエリを使用して、削除されたクラスターを監視できます。

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>次の手順

* [HDInsight クラスターの容量計画](./hdinsight-capacity-planning.md)
* [Azure HDInsight の既定および推奨のノード構成](./hdinsight-supported-node-configuration.md)
