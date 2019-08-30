---
title: Azure Monitor ログ (Log Analytics) を使用して Azure Site Recovery を監視する
description: Azure Monitor ログ (Log Analytics) を使用して Azure Site Recovery を監視する方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: 4eb88658437d3b29cc55d24bb83f73b660daea43
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718230"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Azure Monitor ログを使用した Site Recovery の監視

この記事では、Azure [Site Recovery](site-recovery-overview.md) によってレプリケートされたマシンを [Azure Monitor ログ](../azure-monitor/platform/data-platform-logs.md)と [Log Analytics](../azure-monitor/log-query/log-query-overview.md) を使って監視する方法を説明します。

Azure Monitor ログは、アクティビティと診断のログを他の監視データと共に収集するログ データ プラットフォームです。 Azure Monitor ログ内で Log Analytics を使用し、ログ クエリを記述してテストしたり、ログ データを対話形式で分析したりすることができます。 ログの結果を可視化してクエリを実行したり、監視対象データに基づいてアクションを実行するようにアラートを構成したりすることが可能です。

Site Recovery では、Azure Monitor ログを次の目的に使用できます。

- **Site Recovery の正常性と状態を監視する**。 たとえば、レプリケーションの正常性、テスト フェールオーバーの状態、Site Recovery のイベント、保護対象マシンの RPO (目標復旧ポイント)、ディスク (またはデータ) の変更量を監視することができます。
- **Site Recovery のアラートを設定する**。 たとえば、マシンの正常性、テスト フェールオーバーの状態、Site Recovery ジョブの状態に対するアラートを構成することができます。

Site Recovery での Azure Monitor ログの使用は、Azure から Azure へのレプリケーションと VMware VM (または物理サーバー) から Azure へのレプリケーションでサポートされます。
## <a name="before-you-start"></a>開始する前に

次のものが必要です。

- Recovery Services コンテナー内で保護された少なくとも 1 つのマシン。
- Site Recovery のログを格納するための Log Analytics ワークスペース。 ワークスペースの設定に関する[説明](../azure-monitor/learn/quick-create-workspace.md)を参照してください。
- Log Analytics におけるログ クエリの記述、実行、分析の方法に関する基本的な理解。 [詳細情報](../azure-monitor/log-query/get-started-portal.md)。

最初に、[監視についての一般的な質問](monitoring-common-questions.md)を確認しておくことをお勧めします。

## <a name="configure-site-recovery-to-send-logs"></a>ログを送信するように Site Recovery を構成する

1. コンテナーで、 **[診断設定]**  >  **[診断設定を追加する]** の順にクリックします。

    ![診断ログを選択する](./media/monitoring-log-analytics/add-diagnostic.png)

2. **[診断設定]** で、ログ アクションの名前を指定し、 **[Log Analytics への送信]** を選択します。
3. Azure Monitor ログのサブスクリプションと Log Analytics ワークスペースを選択します。
4. ログの一覧から、**AzureSiteRecovery** で始まるログをすべて選択します。 次に、 **[OK]** をクリックします

    ![ワークスペースを選択](./media/monitoring-log-analytics/select-workspace.png)

以後、選択したワークスペース内のテーブル (**AzureDiagnostics**) に Site Recovery のログが取り込まれます。


## <a name="query-the-logs---examples"></a>ログのクエリを実行する - 例

ログからデータを取得するには、[Kusto 照会言語](../azure-monitor/log-query/get-started-queries.md)で記述されたログ クエリを使用します。 このセクションでは、Site Recovery の監視で一般的に使用されるクエリの例をいくつか紹介します。

> [!NOTE]
> 一部の例では、**replicationProviderName_s** を **A2A** に設定しています。 この場合、Site Recovery を使用してセカンダリ Azure リージョンにレプリケートされた Azure VM が取得されます。 それらの例で、Site Recovery を使用して Azure にレプリケートされたオンプレミスの VMware VM または物理サーバーを取得したい場合は、**A2A** を **InMageAzureV2** に置き換えてください。


### <a name="query-replication-health"></a>レプリケーションの正常性を照会する

このクエリは、保護対象のすべての Azure VM について、最新のレプリケーションの正常性をノーマル、警告、クリティカルの 3 つの状態に分けて円グラフにプロットします。

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>Mobility Service のバージョンを照会する

このクエリは、Site Recovery を使ってレプリケートされた Azure VM を、実行されている Mobility エージェントのバージョンごとに分けて円グラフにプロットします。

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>RPO 時間を照会する

このクエリは、Site Recovery を使ってレプリケートされた Azure VM を、目標復旧ポイント (RPO) (15 分未満、15 から 30 分、30 分超) ごとに分けて棒グラフにプロットします。

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| extend RPO = case(rpoInSeconds_d <= 900, "<15Min",   
rpoInSeconds_d <= 1800, "15-30Min", ">30Min")  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , RPO  
| summarize Count = count() by RPO  
| render barchart 
```

![RPO を照会する](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Site Recovery ジョブを照会する

このクエリは、(ディザスター リカバリーのあらゆるシナリオを対象に) 直近 72 時間以内にトリガーされたすべての Site Recovery ジョブとその完了状態を取得します。

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Site Recovery イベントを照会する

このクエリは、(ディザスター リカバリーのあらゆるシナリオを対象に) 直近 72 時間以内に発生したすべての Site Recovery イベントとその重大度を取得します。 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>テスト フェールオーバーの状態を照会する (円グラフ)

このクエリは、Site Recovery を使ってレプリケートされた Azure VM のテスト フェールオーバーの状態を円グラフにプロットします。

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , Resource, failoverHealth_s  
| summarize count() by failoverHealth_s  
| render piechart 
```

### <a name="query-test-failover-state-table"></a>テスト フェールオーバーの状態を照会する (表)

このクエリは、Site Recovery を使ってレプリケートされた Azure VM のテスト フェールオーバーの状態を表にプロットします。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>マシンの RPO を照会する

このクエリは、直近 72 時間における特定の Azure VM (ContosoVM123) の RPO を追跡した傾向グラフをプロットします。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![マシンの RPO を照会する](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-for-a-vm"></a>VM のデータの変更量 (変更頻度) を照会する

このクエリは、特定の Azure VM (ContosoVM123) について、データの変更量 (1 秒あたりの書き込みバイト数) とデータのアップロード速度を追跡する傾向グラフをプロットします。 この情報が得られるのは、セカンダリ Azure リージョンにレプリケートされた Azure VM だけです。

```
AzureDiagnostics   
| where Category in ("AzureSiteRecoveryProtectedDiskDataChurn", "AzureSiteRecoveryReplicationDataUploadRate")   
| extend CategoryS = case(Category contains "Churn", "DataChurn",   
Category contains "Upload", "UploadRate", "none")  
| extend InstanceWithType=strcat(CategoryS, "_", InstanceName_s)   
| where TimeGenerated > ago(24h)   
| where InstanceName_s startswith "ContosoVM123"   
| project TimeGenerated , InstanceWithType , Churn_MBps = todouble(Value_s)/1048576   
| render timechart  
```
![データの変更量を照会する](./media/monitoring-log-analytics/example3.png)

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>ディザスター リカバリーのサマリーを照会する (Azure から Azure)

このクエリは、セカンダリ Azure リージョンにレプリケートされた Azure VM のサマリー テーブルをプロットします。  VM 名、レプリケーションと保護の状態、RPO、テスト フェールオーバーの状態、Mobility エージェントのバージョン、アクティブなレプリケーション エラー、ソースの場所が表示されます。

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>ディザスター リカバリーのサマリーを照会する (VMware または物理サーバー)

このクエリは、Azure にレプリケートされた VMware VM と物理サーバーのサマリー テーブルをプロットします。  マシン名、レプリケーションと保護の状態、RPO、テスト フェールオーバーの状態、Mobility エージェントのバージョン、アクティブなレプリケーション エラー、関連するプロセス サーバーが表示されます。

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>アラートを設定する - 例

Azure Monitor のデータに基づいて Site Recovery のアラートを設定できます。 ログ アラートの設定に関する[詳細情報](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)を参照してください。 

> [!NOTE]
> 一部の例では、**replicationProviderName_s** を **A2A** に設定しています。 この場合、セカンダリ Azure リージョンにレプリケートされた Azure VM のアラートが設定されます。 それらの例で、Azure にレプリケートされたオンプレミスの VMware VM または物理サーバーのアラートを設定したい場合は、**A2A** を **InMageAzureV2** に置き換えてください。

### <a name="multiple-machines-in-a-critical-state"></a>複数のマシンがクリティカル状態

レプリケートされた Azure VM のうち、クリティカル状態になった VM が 20 を超えた場合のアラートを設定します。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
このアラートでは、**しきい値**を 20 に設定します。

### <a name="single-machine-in-a-critical-state"></a>1 つのマシンがクリティカル状態

レプリケートされた特定の Azure VM がクリティカル状態になった場合のアラートを設定します。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
このアラートでは、**しきい値**を 1 に設定します。

### <a name="multiple-machines-exceed-rpo"></a>複数のマシンが RPO を超過

20 個を超える Azure VM で RPO が 30 分を超過した場合のアラートを設定します。
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
このアラートでは、**しきい値**を 20 に設定します。

### <a name="single-machine-exceeds-rpo"></a>1 つのマシンが RPO を超過

1 つの Azure VM で RPO が 30 分を超過した場合のアラートを設定します。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
このアラートでは、**しきい値**を 1 に設定します。

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>複数のマシンのテスト フェールオーバーが 90 日を超過

テスト フェールオーバーが最後に成功してからの経過日数が 90 日を超える VM が 20 個を超えた場合のアラートを設定します。 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
このアラートでは、**しきい値**を 20 に設定します。

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>1 つのマシンのテスト フェールオーバーが 90 日を超過

特定の VM でテスト フェールオーバーが最後に成功してからの経過日数が 90 日を超えた場合のアラートを設定します。
```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| where name_s == "ContosoVM123"  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
このアラートでは、**しきい値**を 1 に設定します。

### <a name="site-recovery-job-fails"></a>Site Recovery ジョブの失敗

Site Recovery のすべてのシナリオを対象に、直近 24 時間に Site Recovery ジョブ (このケースでは再保護ジョブ) が失敗した場合のアラートを設定します。 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

このアラートでは、**しきい値**を 1 に、**期間**を 1,440 分に設定して、直近 24 時間の失敗をチェックします。

## <a name="next-steps"></a>次の手順

Site Recovery のビルトインの監視機能に関する[説明](site-recovery-monitor-and-troubleshoot.md)を参照してください。
