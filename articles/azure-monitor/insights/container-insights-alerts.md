---
title: コンテナーに対する Azure Monitor を使用してパフォーマンス アラートを作成する | Microsoft Docs
description: この記事では、コンテナーに対する Azure Monitor を使用して、メモリおよび CPU の使用率に対するログ クエリに基づいてカスタム アラートを作成する方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: magoedte
ms.openlocfilehash: ebe2c2b488e3d71597dd24f5504a14dd7ce6671e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59282289"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>コンテナー用 Azure Monitor でパフォーマンスの問題に関するアラートを設定する方法
コンテナーに対する Azure Monitor は、Azure Container Instances にデプロイされているか、Azure Kubernetes Service (AKS) でホストされているマネージド Kubernetes クラスターにデプロイされている、コンテナー ワークロードのパフォーマンスを監視します。

この記事では、以下の状況のアラートを有効にする方法について説明します。

* クラスター ノードで CPU またはメモリの使用率が定義済みのしきい値を超えたとき
* 対応するリソースに設定されている上限と比較して、コントローラー内のいずれかのコンテナーで CPU またはメモリの使用率が定義済みのしきい値を超えたとき
* *NotReady* 状態のノード数
*  *Failed*、*Pending*、*Unknown*、*Running*、*Succeeded* の各ポッドフェーズ数

クラスター ノードで CPU またはメモリの使用率が高いことを警告するには、提供されているクエリを使用して、メトリック アラートまたはメトリック測定アラートを作成します。 メトリック アラートは、ログ アラートよりも待ち時間が短くなります。 しかし、ログ アラートでは、高度なクエリと、より洗練された機能が利用できます。 ログ アラートのクエリは、*now* 演算子を使用して日時を現在と比較し、1 時間戻ります  (コンテナーに対する Azure Monitor では、すべての日付が協定世界時 (UTC) 形式で格納されます)。

Azure Monitor のアラートに詳しくない場合は、事前に「[Microsoft Azure のアラートの概要](../platform/alerts-overview.md)」を参照してください。 ログ クエリを使用したアラートの詳細については、「[Azure Monitor でのログ アラート](../platform/alerts-unified-log.md)」を参照してください。 メトリック アラートの詳細については、[Azure Monitor でのメトリック アラート](../platform/alerts-metric-overview.md)に関するページを参照してください。

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
        | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus
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

## <a name="create-an-alert-rule"></a>アラート ルールを作成する
前述のログ検索ルールのいずれかを使用して、Azure Monitor でログ アラートを作成するには、次の手順に従います。  

>[!NOTE]
>コンテナー リソースの使用率に関するアラート ルールを作成する次の手順では、「[ログ アラートの API の基本設定を切り替える](../platform/alerts-log-api-switch.md)」の説明に従って、新しいログ アラート API に切り替える必要があります。
>

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のウィンドウで、**[モニター]** を選択します。 **[分析情報]** で **[コンテナー]** を選択します。
3. **[監視対象クラスター]** タブで、一覧からクラスターを選択します。
4. 左側のウィンドウの **[監視]** で **[ログ]** を選択して、Azure Monitor のログ ページを開きます。 このページを使用して、Azure Log Analytics クエリを記述し、実行することができます。
5. **[ログ]** ページで **[+ 新しいアラート ルール]** を選択します。
6. **[条件]** セクションで、事前定義済みのカスタム ログ条件の **[Whenever the Custom log search is \<logic undefined>]\(カスタム ログ検索が <ロジックが定義されていません> であるときは常に\)** を選択します。 シグナルの種類として **[custom log search]\(カスタム ログ検索\)** が自動的に選択されます。これは、Azure Monitor のログ ページで直接アラート ルールを作成しているためです。  
7. 前述の[クエリ](#resource-utilization-log-search-queries)のいずれかを **[検索クエリ]** フィールドに貼り付けます。
8. 次のようにアラートを構成します。

    1. **[基準]** ドロップダウン リストで **[メトリック測定]** を選択します。 メトリック測定では、クエリの対象となったオブジェクトのうち、指定したしきい値を上回っている値の各オブジェクトについて、アラートが生成されます。
    1. **[条件]** で **[より大きい]** を選択し、最初の基準となる **[しきい値]** として「**75**」を入力します。 または、独自の条件を満たす別の値を入力します。
    1. **[アラートをトリガーする基準]** セクションで、**[連続する違反]** を選択します。 ドロップダウン リストで **[より大きい]** を選択し、「**2**」と入力します。
    1. コンテナーの CPU またはメモリの使用率に関するアラートを構成するには、**[集計]** で **[ContainerName]** を選択します。 
    1. **[評価基準]** セクションで、**[期間]** の値を **[60 分]** に設定します。 ルールは 5 分ごとに実行され、現在の時刻から過去 1 時間以内に作成されたレコードが返されます。 期間の枠を広く設定すると、データ待ち時間が発生する原因になります。 また、クエリが必ずデータを返すため、検知漏れによってアラートが発生しない事態を回避することができます。

9. **[完了]** を選択して、アラート ルールを完成させます。
10. **[アラート ルール名]** フィールドに名前を入力します。 アラートの詳細情報を提供する **[説明]** を指定します。 提供されるオプションの中から、適切な重大度レベルを選択します。
11. アラート ルールをすぐにアクティブにするには、**[ルールの作成時に有効にする]** の既定値をそのまま使用します。
12. 既存の**アクション グループ**を選択するか、新しいグループを作成します。 この手順により、アラートがトリガーされるたびに同じアクションが実行されます。 お客様の IT または DevOps オペレーション チームでのインシデントの管理方法に基づいて構成してください。
13. **[アラート ルールの作成]** を選択してアラート ルールを完成させます。 すぐに実行が開始されます。

## <a name="next-steps"></a>次の手順

* [ログ クエリの例](container-insights-analyze.md#search-logs-to-analyze-data)を確認し、事前定義されたクエリや例を評価したり、他のアラート シナリオ向けにカスタマイズしたりする方法を学習します。
* Azure Monitor の詳細と、AKS クラスターの他の側面を監視する方法については、[Azure Kubernetes Service の正常性の表示](container-insights-analyze.md)に関するページを参照してください。
