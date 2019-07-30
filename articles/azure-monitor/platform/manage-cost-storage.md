---
title: Azure Monitor ログの使用量とコストを管理する | Microsoft Docs
description: Azure Monitor で Log Analytics ワークスペースの料金プランを変更し、データ ボリュームとアイテム保持ポリシーを管理する方法について説明します。
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: bcfefc9698f7f251e99531750e19e7c06395e064
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655699"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Azure Monitor ログで使用量とコストを管理する

> [!NOTE]
> この記事では、ご利用の Log Analytics ワークスペースのデータ保有期間を設定して Azure Monitor でコストを管理する方法について説明します。  関連する情報については、次の記事を参照してください。
> - 「[使用量と推定コストの監視](usage-estimated-costs.md)」では、Azure の異なる価格モデルの複数の監視機能全体の使用量と推定コストを表示する方法について説明します。 価格モデルを変更する方法についても説明します。

Azure Monitor ログは、企業内のソースまたは Azure に展開されたソースから毎日大量のデータを収集し、インデックスを付けて、保存する処理をスケーリングおよびサポートするように設計されています。  これは組織の主要な原動力になる場合がありますが、最終的に基になる原動力はコスト効率です。 そのためには、Log Analytics ワークスペースのコストは、収集されるデータのボリュームだけでなく、選択されているプラン、および接続されたソースから生成されたデータの保持期間にも依存することを、理解しておくことが重要です。  

この記事では、データ ボリュームとストレージの拡大を事前に監視し、制限を定義して関連コストを制御する方法を説明します。 

データのコストは、次の要因に大きく依存する可能性があります。 

- 生成されてワークスペースに取り込まれるデータのボリューム 
    - 有効化された管理ソリューションの数
    - 監視対象システムの数
    - 各監視対象リソースから収集されたデータの種類 
- データを保持する時間の長さ 

## <a name="understand-your-workspaces-usage-and-estimated-cost"></a>ワークスペースの使用料と推定コストを理解する

Azure Monitor ログでは、最近の使用パターンに基づいてコストを簡単に理解できます。 これには、データ使用状況を確認して分析するために **Log Analytics の [使用量と推定コスト]** を使用します。 これには、各ソリューションによって収集されるデータの量、保持されるデータの量、および取り込まれたデータ量と無料使用量を超える追加保有期間に基づいたコストの推定値が示されます。

![使用量と推定コスト](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

データを詳細に調査するには、 **[使用量と推定コスト]** ページでグラフの右上にあるアイコンをクリックします。 これで、このクエリを操作して使用状況の詳細を調査できます。  

![ログ ビュー](media/manage-cost-storage/logs.png)

**[使用量と推定コスト]** ページでは、該当の月のデータ ボリュームを確認できます。 これには、受信して Log Analytics ワークスペースに格納されたすべてのデータが含まれます。  ページ上部の **[使用量の詳細]** をクリックすると、使用量ダッシュボードにデータ ボリュームの傾向の情報がソース別、コンピューター別、サービス別に表示されます。 日次上限を表示および設定したり、リテンション期間を変更したりするには、 **[データ ボリュームの管理]** をクリックします。
 
Log Analytics の課金は Azure の課金内容に加えられます。 Azure Portal の [課金] または [Azure Billing Portal](https://account.windowsazure.com/Subscriptions) で、Azure の課金内容の詳細を確認できます。  

## <a name="daily-cap"></a>日次上限

日次上限を構成して、ワークスペースの毎日のインジェストを制限できますが、1 日の上限に達することが目標ではないので注意する必要があります。  そうしないと、その日の残りの時間についてデータが失われ、ワークスペースで最新のデータが利用できることに依存する機能を持つ他の Azure サービスやソリューションに影響を与える可能性があります。  その結果、IT サービスをサポートするリソースの正常性状態を監視してアラートを受け取る機能が影響を受けます。  日次上限は、マネージド リソースからのデータ ボリュームの予期しない増加を管理して制限内に留めるための手段、またはワークスペースの計画外の料金を制限する手段として使うためのものです。  

日次上限に達すると、課金対象のデータの種類の収集は、その日はそれ以上行われません。 選択した Log Analytics ワークスペースのページの上部に警告バナーが表示され、**LogManagement** カテゴリの *Operation* テーブルに操作イベントが送信されます。 [*1 日の上限を次に設定する*] で定義されているリセット時刻が過ぎると、データ収集が再開されます。 この操作イベントに基づいてアラート ルールを定義し、データの日次制限に達したら通知するよう構成することをお勧めします。 

> [!NOTE]
> 日次上限によって Azure Security Center からのデータの収集が停止することはありません。

### <a name="identify-what-daily-data-limit-to-define"></a>定義する日次データ制限を明らかにする

[Log Analytics の使用量と推定コスト](usage-estimated-costs.md)に関する記事を参照し、データ インジェストの傾向および定義する日次データ ボリューム上限について理解してください。 上限に達した後はリソースを監視できなくなるので、慎重に検討してください。 

### <a name="manage-the-maximum-daily-data-volume"></a>最大日次データ ボリュームを管理する

次の手順では、Log Analytics ワークスペースが 1 日に取り込むデータのボリュームを管理する制限を構成する方法について説明します。  

1. ワークスペースの左ウィンドウから **[使用量と推定コスト]** を選びます。
2. 選んだワークスペースの **[使用量と推定コスト]** ページの上部にある **[データ ボリュームの管理]** をクリックします。 
3. 日次上限は既定では **[オフ]** になっています。有効にするには、 **[オン]** をクリックし、GB/日でデータ ボリュームの制限を設定します。

    ![Log Analytics のデータ制限の構成](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>日次上限に到達した際のアラート

データ制限のしきい値に達したら Azure Portal に視覚的な合図が表示されますが、この動作は、早急な措置を必要とする運用上の問題を管理する方法と、必ずしも一致していない場合があります。  アラート通知を受け取るには、Azure Monitor で新しいアラート ルールを作成します。  詳細については、[アラートを作成、表示、管理する方法](alerts-metric.md)に関するページをご覧ください。

最初は、次のようにアラートを設定することをお勧めします。

- ターゲット:お客様の Log Analytics リソースを選択します
- 条件: 
   - シグナル名: カスタム ログ検索
   - 検索クエリ: Operation | where Detail has 'OverQuota'
   - ベース: 結果の数
   - 条件: より大きい
   - しきい値: 0
   - 期間: 5 (分)
   - 頻度: 5 (分)
- アラート ルール名: 1 日のデータ制限に達しました
- 重大度: 警告 (重大度 1)

アラートを定義した後で制限に達すると、アラートがトリガーされ、アクション グループで定義されている応答が実行されます。 メールおよびテキスト メッセージでチームに通知したり、webhook、Automation Runbook、または[外部の ITSM ソリューションとの統合](itsmc-overview.md#create-itsm-work-items-from-azure-alerts)を使ってアクションを自動化したりできます。 

## <a name="change-the-data-retention-period"></a>データ保持期間の変更

次の手順では、ワークスペースにログ データを保持する期間を構成する方法について説明します。
 
1. ワークスペースの左ウィンドウから **[使用量と推定コスト]** を選びます。
2. **[使用量と推定コスト]** ページの上部にある **[データ ボリュームの管理]** をクリックします。
3. ウィンドウで、スライダーを移動して日数を増減し、 **[OK]** をクリックします。  *無料*プランをご利用の場合は、データ保持期間を変更できません。この設定を制御するには、有料プランにアップグレードする必要があります。

    ![ワークスペースのデータ保持設定の変更](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
この保持期間は `dataRetention` パラメーターを使用して [ARM 経由で設定](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace)することもできます。 さらに、データ保持を 30 日間に設定すると、`immediatePurgeDataOn30Days` パラメーターを使用してより古いデータの即時の消去をトリガーできます。これは、コンプライアンス関連のシナリオに役立つ可能性があります。 この機能は ARM 経由でのみ公開されます。 

## <a name="legacy-pricing-tiers"></a>レガシ価格レベル

2018 年 4 月 2 日より前に Log Analytics ワークスペースまたはその中の Application Insights リソースがあったサブスクリプション、あるいは 2019 年 2 月 1 日より前に開始したマイクロソフト エンタープライズ契約にリンクされているサブスクリプションは、引き続きレガシ価格レベルにアクセスできます: **Free**、**スタンドアロン (GB あたり)** 、**ノードごと (OMS)** 。  Free 価格レベルのワークスペースでは、毎日のデータ インジェストの上限が 500 MB になります (Azure Security Center によって収集されるセキュリティ データの種類を除く)。また、データの保持は 7 日に制限されます。 Free 価格レベルでは、評価のみが目的として意図されています。 スタンドアロンまたはノードごとの価格レベルのワークスペースには、最大 2 年のユーザーが構成可能な保持が含まれています。 2016 年 4 月より前に作成されたワークスペースでは、元の **Standard** と **Premium** の価格レベルにもアクセスできます。 価格レベルの制限の詳細は、[ここ](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces)で利用できます。

> [!NOTE]
> OMS E1 Suite、OMS E2 Suite、または OMS Add-On for System Center のいずれかを購入することによって得られる資格を使用するには、OMS Log Analytics の*ノード単位*の価格レベルを選択します。

Log Analytics の最早採用者は、元の価格レベル **Standard** と **Premium** にもアクセスできます。これは、それぞれ 30 日と 365 日のデータ保持に固定されています。 

## <a name="changing-pricing-tier"></a>価格レベルの変更

Log Analytics ワークスペースが従来の価格レベルにアクセスできる場合、従来の価格レベル間で変更するには、以下の手順を実行します。

1. Azure portal の Log Analytics サブスクリプション ウィンドウで、ワークスペースを選択します。

2. ワークスペース ウィンドウで、 **[全般]** の **[価格レベル]** を選択します。  

3. **[価格レベル]** で価格レベルを選択し、 **[選択]** をクリックします。  
    ![選択された料金プラン](media/manage-cost-storage/workspace-pricing-tier-info.png)

`ServiceTier` パラメーターを使用して [ARM 経由で価格レベルを設定](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace)することもできます。 

## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Log Analytics がデータを収集しなくなった場合のトラブルシューティング

レガシの無料の価格レベルを使用しており、1 日に 500 MB を超えるデータを送信した場合、その日の残りはデータ収集が停止します。 1 日の制限に達したことが、Log Analytics がデータの収集を停止したり、データがないように見えたりする一般的な原因です。  データ収集が開始および停止すると、Log Analytics は Operation 型のイベントを作成します。 1 日の制限に達し、データがなくなっているかどうかを確認するには、検索で次のクエリを実行します。 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

データ収集が停止するとき、OperationStatus は **[警告]** です。 データ収集が開始するとき、OperationStatus は **[成功]** です。 次の表は、データ収集が停止する原因と、データ収集を再開するための推奨されるアクションを示しています。  

|収集が停止する原因| 解決策| 
|-----------------------|---------|
|レガシの無料価格レベルの 1 日の制限に到達している |収集が自動的に再開される次の日まで待つか、または有料の価格レベルに変更します。|
|ワークスペースの 1 日あたりの上限に達した|収集が自動的に再開されるまで待つか、「最大日次データ ボリュームを管理する」で説明されているようにして 1 日のデータ ボリューム制限を増やします。 日次上限のリセット時間が **[データ ボリュームの管理]** ページに表示されます。 |
|次のために、Azure サブスクリプションが中断された状態にある<br> 無料試用版が終了した<br> Azure パスの期限が切れた<br> 1 月の使用制限に達した (たとえば、MSDN または Visual Studio サブスクリプションで)|有料のサブスクリプションに変換する<br> 制限を削除するか、または制限がリセットされるまで待つ|

データ収集が停止したときに通知が届くようにするには、"*日次データ上限の作成*" アラートで説明されている手順を使用して、データ収集が停止したときに通知されるようにします。 [アクション グループの追加](action-groups.md)に関する記事に記載されている手順を使用して、アラート ルールに電子メール、webhook、または Runbook アクションを構成します。 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>使用量が予想よりも多い理由のトラブルシューティング

使用量が多くなる原因は、次のいずれかまたは両方です。
- 予想よりも多くのノードが Log Analytics ワークスペースにデータを送信している
- 予想よりも多くのデータが Log Analytics ワークスペースに送信されている

## <a name="understanding-nodes-sending-data"></a>データを送信するノードについて理解する

過去 1 か月間における毎日のコンピューター数のレポート ハートビートを把握するには、次を使用します

```kusto
Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```

ワークスペースが "ノードごと" のレガシ価格レベルである場合に、ノードとして課金対象になるコンピューターの一覧を取得するには、**課金されるデータの種類**を送信しているノードを探します (一部のデータの種類は無料です)。 これを行うには、`_IsBillable` [プロパティ](log-standard-properties.md#_isbillable)を使用し、完全修飾ドメイン名の左端のフィールドを使用します。 これにより、課金対象のデータがあるコンピューターの一覧が返されます。

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

表示される課金対象のノードの数は、次のように推定できます。 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| billableNodes=dcount(computerName)
```

> [!NOTE]
> 複数の種類のデータにわたるスキャンは、実行コストが高いため、これらの `union withsource = tt *` クエリは多用しないようにします。 このクエリは、Usage データ型でコンピューター情報ごとにクエリを実行する従来の方法に取って代わるものです。  

実際に課金される内容をより正確に計算するには、課金されるデータの種類を送信している、時間あたりのコンピューター数を取得します。 ("ノードごと" のレガシ価格レベルのワークスペースでは、Log Analytics によって、時間単位での課金が必要なノードの数が計算されます。) 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>取り込まれたデータ ボリュームについて理解する

**[使用量と推定コスト]** ページの *[ソリューションごとのデータの取り込み]* グラフに、送信されたデータの総量と各ソリューションによって送信されている量が表示されます。 これにより、全体的なデータ使用量 (または、特定のソリューションによる使用量) が、増加しているか、一定しているか、減少しているかといった傾向を確認できます。 これを生成するために使用するクエリは、次のとおりです

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

句 "where IsBillable = true" は、取り込み料金がかからない特定のソリューションからのデータ型を除外することに注意してください。 

さらに詳しく調査して、IIS ログのためにデータを調査したい場合などに、特定のデータ型のデータの傾向を確認することができます。

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

### <a name="data-volume-by-computer"></a>コンピューターごとのデータ ボリューム

コンピューターごとに、取り込まれた課金可能イベントの**サイズ**を表示するには `_BilledSize` [プロパティ](log-standard-properties.md#_billedsize)を使用します。サイズはバイト単位で示されます。

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

`_IsBillable` [プロパティ](log-standard-properties.md#_isbillable)では、取り込まれたデータで課金が発生するかどうかを指定します。

コンピューターごとに取り込まれた**請求対象**のイベントの数を表示するには、次を使用します 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount=count() by computerName  | sort by count_ nulls last
```

特定のコンピューターにデータを送信する、課金対象のデータ型のデータ数を表示する場合は、次のコマンドを使用します。

```kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Azure リソース、リソース グループ、またはサブスクリプションごとのデータ ボリューム

__コンピューター__ごとに取り込まれた課金可能イベントの**サイズ**を取得できる Azure でホストされているノードからのデータについては、次のようにリソースへの完全パスを提供する _ResourceId [プロパティ](log-standard-properties.md#_resourceid)を使用します。

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

__Azure サブスクリプションごとに__取り込まれた課金可能イベントの**サイズ**を取得できる Azure でホストされているノードからのデータについては、`_ResourceId` プロパティを次のように解析します。

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

`subscriptionId` を `resourceGroup` に変更すると、Azure リソース グループごとの課金可能な取り込まれたデータ ボリュームが表示されます。 


> [!NOTE]
> 使用状況データ型の一部のフィールドは、スキーマにはまだありますが非推奨とされていて、その値が設定されなくなります。 これらは、**Computer** と、取り込みに関連するフィールド (**TotalBatches**、**BatchesWithinSla**、**BatchesOutsideSla**、**BatchesCapped**、および **AverageProcessingTimeMs**) です。

### <a name="querying-for-common-data-types"></a>一般的なデータ型のクエリを実行する

特定のデータ型のデータのソースについてさらに詳しく調べるための、いくつかの便利なクエリの例を次に示します。

+ **Security** ソリューション
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **Log Management** ソリューション
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ **Perf** データの種類
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ **Event** データの種類
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ **Syslog** データの種類
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ **AzureDiagnostics** データ型
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>データ量の削減のためのヒント

収集されるログの量を削減するためのいくつかの提案は、次のとおりです。

| データ量の多いソース | データ量を削減する方法 |
| -------------------------- | ------------------------- |
| セキュリティ イベント            | [一般的または最小限のセキュリティ イベント](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)を選択します。 <br> 必要なイベントのみを収集するようにセキュリティ監査ポリシーを変更します。 特に、次のイベントを収集する必要性を検討します。 <br> - [フィルタリング プラットフォームの監査](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [レジストリの監査](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [ファイル システムの監査](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [カーネル オブジェクトの監査](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [ハンドル操作の監査](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - リムーバブル記憶域の監査 |
| パフォーマンス カウンター       | [パフォーマンス カウンターの構成](data-sources-performance-counters.md)を次のように変更します。 <br> - 収集の頻度を減らす <br> - パフォーマンス カウンターの数を減らす |
| イベント ログ                 | [イベント ログの構成](data-sources-windows-events.md)を次のように変更します。 <br> - 収集対象のイベント ログの数を減らす <br> - 必要なイベント レベルのみを収集する たとえば、"*情報*" レベルのイベントを収集しないようにします。 |
| syslog                     | [syslog の構成](data-sources-syslog.md)を次のように変更します。 <br> - 収集対象の施設の数を減らす <br> - 必要なイベント レベルのみを収集する たとえば、"*情報*" と "*デバッグ*" レベルのイベントを収集しないようにします。 |
| AzureDiagnostics           | リソース ログの収集を次のように変更します。 <br> - Log Analytics へのリソース送信ログの数を減らす <br> - 必要なログのみを収集する |
| ソリューションを必要としないコンピューターからのソリューション データ | [ソリューションのターゲット設定](../insights/solution-targeting.md)を使用して、必要なコンピューター グループからのみデータを収集するようにします。 |

### <a name="getting-security-and-automation-node-counts"></a>セキュリティ ノード数と Automation ノード数を取得する

「ノードごと (OMS)」価格レベルを使用している場合は、使用しているノードとソリューションの数と、 **[使用量と推定コスト]** ページの表に表示される課金対象の Insights ノードと Analytics ノードの数に基づいて課金されます。  

個別のセキュリティ ノードの数を表示するには、次のクエリを使用できます。

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

個別の Automation ノードの数を表示するには、次のクエリを使用します。

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="create-an-alert-when-data-collection-is-high"></a>収集したデータの量が多い場合のアラートを作成する

このセクションでは、次の場合のアラートを作成する方法について説明します。
- データ量が指定された量を超えた。
- データ量が指定された量を超えると予測された。

Azure アラートでは、検索クエリを使用する[ログ アラート](alerts-unified-log.md)をサポートしています。 

次のクエリでは、過去 24 時間で 100 GB を超えるデータが収集された場合に結果が返されます。

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type 
| where DataGB > 100
```

次のクエリでは、簡単な数式を使用して、1 日に 100 GB を超えるデータが送信される場合を予測します。 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type 
| where EstimatedGB > 100
```

別のデータ量でアラートを生成するには、クエリの 100 をアラートを生成する GB 数に変更します。

[新しいログ アラートの作成](alerts-metric.md)に関するページで説明されている手順を使用して、収集したデータの量が予測を超えた場合に通知されるようにします。

最初のクエリ (24 時間でデータが 100 GB を超えた場合) のアラートを作成するには、次のように設定します。  

- **[アラートの条件を定義します]** では、リソース ターゲットとして Log Analytics ワークスペースを指定します。
- **[アラートの条件]** では、以下を指定します。
   - **[シグナル名]** では、 **[カスタム ログ検索]** を選択します。
   - **[検索クエリ]** : `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - **[アラート ロジック]** は "*結果の数*" **に基づき、** **[条件]** は**しきい値**の *0* "*より大きい*" です
   - **[期間]** を *1440* 分にします。使用状況データが更新されるのは 1 時間に 1 回のみのため、 **[アラートの頻度]** を *60* 分ごとにします。
- **[アラートの詳細を定義します]** では、以下を指定します。
   - **[名前]** : "*24 時間でデータ量が 100 GB を超えた場合*"
   - **[重大度]** : *[警告]*

ログ アラートが条件に一致するときに通知されるように、既存の[アクション グループ](action-groups.md)を指定するか、アクション グループを新たに作成します。

2 つ目のクエリ (24 時間でデータが 100 GB を超えると予測される場合) のアラートを作成するには、次のように設定します。

- **[アラートの条件を定義します]** では、リソース ターゲットとして Log Analytics ワークスペースを指定します。
- **[アラートの条件]** では、以下を指定します。
   - **[シグナル名]** では、 **[カスタム ログ検索]** を選択します。
   - **[検索クエリ]** : `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - **[アラート ロジック]** は "*結果の数*" **に基づき、** **[条件]** は**しきい値**の *0* "*より大きい*" です
   - **[期間]** を *180* 分にします。使用状況データが更新されるのは 1 時間に 1 回のみのため、 **[アラートの頻度]** を *60* 分ごとにします。
- **[アラートの詳細を定義します]** では、以下を指定します。
   - **[名前]** : "*24 時間でデータ量が 100 GB を超えると予想される場合*"
   - **[重大度]** : *[警告]*

ログ アラートが条件に一致するときに通知されるように、既存の[アクション グループ](action-groups.md)を指定するか、アクション グループを新たに作成します。

アラートを受け取ったら、次のセクションの手順を使用して、使用量が予想よりも多い理由のトラブルシューティングを行います。

## <a name="limits-summary"></a>制限の概要

追加の Log Analytics の制限がいくつかあり、その一部は Log Analytics 価格レベルに依存します。 [ここ](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces)に記載されています。


## <a name="next-steps"></a>次の手順

- 検索言語の使用方法については、[Azure Monitor ログでのログ検索](../log-query/log-query-overview.md)に関する記事を参照してください。 検索クエリを使用して、使用量データをさらに分析できます。
- [新しいログ アラートの作成](alerts-metric.md)に関するページで説明されている手順を使用して、検索条件が満たされた場合に通知されるようにします。
- [ソリューションのターゲット設定](../insights/solution-targeting.md)を使用して、必要なコンピューター グループからのみデータを収集するようにします。
- 効果的なイベント収集ポリシーを構成するには、[Azure Security Center のフィルタリング ポリシー](../../security-center/security-center-enable-data-collection.md)に関するページを参照してください。
- [パフォーマンス カウンターの構成](data-sources-performance-counters.md)を変更します。
- イベントの収集設定を変更する方法については、[イベント ログの構成](data-sources-windows-events.md)に関するページを参照してください。
- Syslog の収集設定を変更する方法については、[Syslog の構成](data-sources-syslog.md)に関するページを参照してください。