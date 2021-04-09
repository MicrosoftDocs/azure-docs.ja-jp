---
title: Container insights で PV の監視を構成する | Microsoft Docs
description: この記事では、Container insights で永続ボリュームを使用している Kubernetes クラスターの監視を構成する方法について説明します。
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 578cfe128b7445f8b09771999d1e653e92c4befa
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200701"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>Container insights で PV の監視を構成する

エージェントのバージョン *ciprod10052020* 以降、Azure Monitor for containers に統合されているエージェントで、PV (永続ボリューム) の使用状況の監視がサポートされるようになりました。 エージェントのバージョン *ciprod01112021* では、状態、ストレージ クラス、種類、アクセス モードなどの詳細情報を含む、PV インベントリの監視がサポートされます。
## <a name="pv-metrics"></a>PV メトリック

Container insights により、次のメトリックが 60 秒間隔で収集され **InsightMetrics** テーブルに格納されることで、PV 使用率の監視が自動的に開始されます。

|メトリック名 |メトリック ディメンション (タグ) |メトリックの説明 | | `pvUsedBytes`| podUID、podName、pvcName、pvcNamespace、capacityBytes、clusterId、clusterName |特定のポッドによって使用される要求が含まれる特定の永続ボリュームのために使用される領域 (バイト単位)。 `capacityBytes` は、データ インジェスト コストを削減し、クエリを簡略化するために、ディメンションとして [タグ] フィールドに組み込まれています。|

収集される PV メトリックを構成する方法に関する詳細については、[こちら](https://aka.ms/ci/pvconfig)を参照してください。

## <a name="pv-inventory"></a>PV インベントリ

Azure Monitor for containers では、次の情報が 60 秒間隔で収集されて **KubePVInventory** テーブルに格納されることで、PV の監視が自動的に開始されます。

|Data |Data Source| データの種類| フィールド|
|-----|-----------|----------|-------|
|Kubernetes クラスター内の永続ボリュームのインベントリ |Kube API |`KubePVInventory` | PVName、PVCapacityBytes、PVCName、PVCNamespace、PVStatus、PVAccessModes、PVType、PVTypeInfo、PVStorageClassName、PVCreationTimestamp、TimeGenerated、ClusterId、ClusterName、_ResourceId |

## <a name="monitor-persistent-volumes"></a>永続ボリュームを監視する

Azure Monitor for containers には、すべてのクラスター用のブック テンプレートの中に、この使用状況メトリックとインベントリ情報の事前構成済みのグラフが含まれています。 また、PV の使用状況に関して推奨されるアラートを有効にし、Log Analytics でこれらのメトリックのクエリを実行できます。  

### <a name="workload-details-workbook"></a>ワークロードの詳細ブック

**[ワークロードの詳細]** ブックの [永続ボリューム] タブに表示される特定のワークロードの使用状況グラフは、左側のペイン、[分析情報] ペインの **[ブックの表示]** ドロップダウン、または [分析情報] ペインの **[レポート (プレビュー)] タブ** からブックを選択することで、AKS クラスターから直接見つけることができます。


:::image type="content" source="./media/container-insights-persistent-volumes/pv-workload-example.PNG" alt-text="Azure Monitor での PV ワークロード ブックの例":::

### <a name="persistent-volume-details-workbook"></a>永続ボリュームの詳細ブック

**[永続ボリュームの詳細]** ブックに表示される永続ボリューム インベントリの概要は、左側のペイン、[分析情報] ペインの **[ブックの表示]** ドロップダウン、または [分析情報] ペインの **[レポート (プレビュー)]** タブからブックを選択することで、AKS クラスターから直接見つけることができます。


:::image type="content" source="./media/container-insights-persistent-volumes/pv-details-workbook-example.PNG" alt-text="Azure Monitor での PV の詳細ブックの例":::

### <a name="persistent-volume-usage-recommended-alert"></a>永続ボリュームの使用率に関する推奨アラート
ポッドの平均 PV 使用率が 80% を超えた場合にアラートを通知する、推奨アラートを有効にすることができます。 アラートについては[こちら](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-metric-alerts)、および規定のしきい値を上書きする方法については[こちら](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-metric-alerts#configure-alertable-metrics-in-configmaps)を参照してください。
## <a name="next-steps"></a>次のステップ

- 収集される PV メトリックの詳細を[こちら](./container-insights-agent-config.md)で確認します。
