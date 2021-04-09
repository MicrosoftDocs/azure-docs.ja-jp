---
title: Container insights からのログ アラート | Microsoft Docs
description: この記事では、Container insights からのメモリおよび CPU の使用率に対するカスタム ログ アラートを作成する方法について説明します。
ms.topic: conceptual
ms.date: 01/05/2021
ms.openlocfilehash: 64d499d69194ac338d367ae094e42f4c8af23bef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711197"
---
# <a name="how-to-create-log-alerts-from-container-insights"></a>Container insights からログ アラートを作成する方法

Container insights により、マネージド Kubernetes クラスターまたは自己管理型 Kubernetes クラスターにデプロイされているコンテナー ワークロードのパフォーマンスが監視されます。 重要なことについてアラートを生成するため、この記事では、AKS クラスターを使用して次の状況でログベースのアラートを作成する方法について説明します。

- クラスター ノードで CPU またはメモリの使用率がしきい値を超えたとき
- 対応するリソースに設定されている上限と比較して、コントローラー内のいずれかのコンテナーで CPU またはメモリの使用率がしきい値を超えたとき
- *NotReady* 状態のノード数
- *Failed*、*Pending*、*Unknown*、*Running*、*Succeeded* の各ポッドフェーズ数
- クラスター ノードで空きディスク領域がしきい値を超えたとき

クラスター ノードで CPU またはメモリの使用率が高いこと、またはクラスター ノードで空きディスク領域が少ないことを警告するには、メトリック アラートまたはメトリック測定アラートを作成するために提供されているクエリを使用します。 メトリック アラートはログ アラートよりも待ち時間が少ない一方で、ログ アラートは高度なクエリと、より洗練された機能に対応しています。 ログ アラートのクエリは、*now* 演算子を使用して 1 時間戻ることで、日時を現在と比較します。 (Container insights により、すべての日付は協定世界時 (UTC) 形式で保存されます)。

Azure Monitor のアラートに詳しくない場合は、事前に「[Microsoft Azure のアラートの概要](../alerts/alerts-overview.md)」を参照してください。 ログ クエリを使用したアラートの詳細については、「[Azure Monitor でのログ アラート](../alerts/alerts-unified-log.md)」を参照してください。 メトリック アラートの詳細については、[Azure Monitor でのメトリック アラート](../alerts/alerts-metric-overview.md)に関するページを参照してください。

## <a name="resource-utilization-log-search-queries"></a>リソース使用率のログ検索クエリ

このセクションのクエリでは、各アラート シナリオがサポートされています。 それらは、この記事の[アラートの作成](#create-an-alert-rule)のセクションの手順 7 で使用します。

次のクエリでは、平均 CPU 使用率を、メンバー ノードの 1 分ごとの CPU 使用率の平均として算出します。  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

次のクエリでは、平均メモリ使用率を、メンバー ノードの 1 分ごとのメモリ使用率の平均として算出します。

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>次のクエリでは、プレースホルダー値 \<your-cluster-name> と \<your-controller-name> を使用して、クラスターとコントローラーを表しています。 アラートを設定するときに、プレースホルダーを環境に固有の値に置き換えてください。

次のクエリでは、コントローラー内のすべてのコンテナーの平均 CPU 使用率を、コントローラー内の全コンテナー インスタンスの 1 分ごとの CPU 使用率の平均として計算します。 測定値は、コンテナーに設定された上限に対する割合です。

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

次のクエリでは、コントローラー内のすべてのコンテナーの平均メモリ使用率を、コントローラー内の全コンテナー インスタンスの 1 分ごとのメモリ使用率の平均として計算します。 測定値は、コンテナーに設定された上限に対する割合です。

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

次のクエリでは、*Ready* および *NotReady* 状態のすべてのノードと数が返されます。

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
次のクエリでは、すべてのフェーズに基づくポッド フェーズ数が返されます:*Failed*、*Pending*、*Unknown*、*Running*、または *Succeeded*。  

```kusto
let endDateTime = now(); 
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | summarize PodStatus=any(PodStatus) by TimeGenerated, PodUid, ClusterName
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>*Pending*、*Failed*、*Unknown* などの特定のポッド フェーズについてのアラートを生成するには、クエリの最後の行を変更します。 たとえば、*FailedCount* についてのアラートを生成するには、次のようにします。 <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

次のクエリは、空き領域の使用が 90% を超えるクラスター ノードのディスクを返します。 クラスター ID を取得するには、まず、次のクエリを実行し、`ClusterId` プロパティの値をコピーします。

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>アラート ルールを作成する

このセクションでは、Container insights からのパフォーマンス データを使用する、メトリック測定アラート ルールの作成について見ていきます。 さまざまなログ クエリでこの基本プロセスを使用して、さまざまなパフォーマンス カウンターに対してアラートを生成することができます。 最初は、前に示したログ検索クエリのいずれかを使用します。 ARM テンプレートを使用して作成するには、[Azure リソース テンプレートを使用したサンプル ログ アラートの作成](../alerts/alerts-log-create-templates.md)に関するページを参照してください。

>[!NOTE]
>コンテナー リソースの使用率に関するアラート ルールを作成する次の手順では、「[ログ アラートの API の基本設定を切り替える](../alerts/alerts-log-api-switch.md)」の説明に従って、新しいログ アラート API に切り替える必要があります。
>

1. [Azure portal](https://portal.azure.com) にサインインします。
2. Azure portal で、 **[Log Analytics ワークスペース]** を検索して選択します。
3. Log Analytics ワークスペースの一覧で、Container insights をサポートしているワークスペースを選択します。 
4. 左側のウィンドウで、 **[ログ]** を選択し、Azure Monitor ログの ページを開きます。 このページを使用して、Azure ログ クエリを記述し、実行することができます。
5. **[ログ]** ページで、前述の [クエリ](#resource-utilization-log-search-queries)の1つを **[検索クエリ]** のフィールドに貼り付け、 **[実行]** を選択して結果を検証します。 この手順を実行しない場合、 **[+ 新しいアラート]** オプションを選択することはできません。
6. **[+ 新しいアラート]** を選択してログアラートを作成します。
7. **[条件]** セクションで、事前定義済みのカスタム ログ条件の **[Whenever the Custom log search is \<logic undefined>]\(カスタム ログ検索が <ロジックが定義されていません> であるときは常に\)** を選択します。 シグナルの種類として **[custom log search]\(カスタム ログ検索\)** が自動的に選択されます。これは、Azure Monitor のログ ページで直接アラート ルールを作成しているためです。  
8. 前述の [クエリ](#resource-utilization-log-search-queries)のいずれかを **[検索クエリ]** フィールドに貼り付けます。
9. 次のようにアラートを構成します。

    1. **[基準]** ドロップダウン リストで **[メトリック測定]** を選択します。 メトリック測定では、クエリの対象となったオブジェクトのうち、指定したしきい値を上回っている値の各オブジェクトについて、アラートが生成されます。
    1. **[条件]** で **[より大きい]** を選択し、CPU およびメモリ使用率のアラートの最初の基準となる **[しきい値]** として「**75**」と入力します。 ディスク領域不足のアラートについては、「**90**」と入力します。 または、独自の条件を満たす別の値を入力します。
    1. **[アラートをトリガーする基準]** セクションで、 **[連続する違反]** を選択します。 ドロップダウン リストで **[より大きい]** を選択し、「**2**」と入力します。
    1. コンテナーの CPU またはメモリの使用率に関するアラートを構成するには、 **[集計]** で **[ContainerName]** を選択します。 クラスター ノードのディスク領域不足のアラートを構成するには、 **[ClusterId]** を選択します。
    1. **[評価基準]** セクションで、 **[期間]** の値を **[60 分]** に設定します。 ルールは 5 分ごとに実行され、現在の時刻から過去 1 時間以内に作成されたレコードが返されます。 期間の枠を広く設定すると、データ待ち時間が発生する原因になります。 また、クエリが必ずデータを返すため、検知漏れによってアラートが発生しない事態を回避することができます。

10. **[完了]** を選択して、アラート ルールを完成させます。
11. **[アラート ルール名]** フィールドに名前を入力します。 アラートの詳細情報を提供する **[説明]** を指定します。 提供されるオプションの中から、適切な重大度レベルを選択します。
12. アラート ルールをすぐにアクティブにするには、 **[ルールの作成時に有効にする]** の既定値をそのまま使用します。
13. 既存の **アクション グループ** を選択するか、新しいグループを作成します。 この手順により、アラートがトリガーされるたびに同じアクションが実行されます。 お客様の IT または DevOps オペレーション チームでのインシデントの管理方法に基づいて構成してください。
14. **[アラート ルールの作成]** を選択してアラート ルールを完成させます。 すぐに実行が開始されます。

## <a name="next-steps"></a>次のステップ

- [ログ クエリの例](container-insights-log-search.md#search-logs-to-analyze-data)を表示して、事前定義されたクエリや例を確認し、クラスターのアラート、視覚化、または分析のために評価やカスタマイズを行います。

- Azure Monitor と、Kubernetes クラスターの他の側面を監視する方法の詳細については、[Kubernetes クラスターのパフォーマンスの表示](container-insights-analyze.md)および [Kubernetes クラスターの正常性の表示](./container-insights-overview.md)に関するページをご覧ください。