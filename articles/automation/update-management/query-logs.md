---
title: Azure Automation の Update Management ログを照会する
description: この記事では、Log Analytics ワークスペースで Update Management のログに対してクエリを実行する方法について説明します。
services: automation
ms.subservice: update-management
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: 5eb0c7d72896cc9a27907743b1b9c3d5a40614dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100592861"
---
# <a name="query-update-management-logs"></a>Update Management ログにクエリを実行する

Update Management のデプロイ中に提供される詳細に加えて、Log Analytics ワークスペースに格納されているログを検索することができます。 Automation アカウントからログを検索するには、 **[Update Management]** を選択し、該当するデプロイに関連付けられている Log Analytics ワークスペースを開きます。

ログのクエリをカスタマイズしたり、それらのクエリをさまざまなクライアントから使用することもできます。 [Log Analytics の検索 API のドキュメント](https://dev.loganalytics.io/)を参照してください。

## <a name="query-update-records"></a>Update レコードを照会する

Update Management では、Windows および Linux VM のレコードと、ログ検索結果に表示されるデータ型が収集されます。 以下のセクションで、これらのレコードについて説明します。

### <a name="query-required-updates"></a>必須の更新を照会する

コンピューターで必要な更新プログラムを表す `RequiredUpdate` の種類のレコードが作成されます。 これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | 説明 |
|----------|-------------|
| Computer | レポート コンピューターの完全修飾ドメイン名。 |
| KBID | Windows Update のサポート技術情報の記事 ID。 |
| ManagementGroupName | Operations Manager 管理グループまたは Log Analytics ワークスペースの名前。 |
| Product | 更新プログラムが適用される製品。 |
| PublishDate | Windows Update から更新プログラムをダウンロードしてインストールする準備ができた日付。 |
| サーバー | | 
| SourceHealthServiceId | Log Analytics Windows エージェント ID を表す一意識別子。 |
| SourceSystem | *OperationsManager* |
| TenantId | Azure Active Directory の組織のインスタンスを表す一意識別子。 |
| TimeGenerated | レコードが作成された日付と時刻。 |
| Type | *アップデート* |
| UpdateClassification | 適用できる更新プログラムの種類を示します。 Windows の場合:<br> *緊急更新プログラム*<br> *セキュリティ更新プログラム*<br> *更新プログラムのロールアップ*<br> *Feature Pack*<br> *Service Pack*<br> *定義ファイルの更新*<br> *ツール*<br> *[Updates]* (更新)。 Linux の場合:<br> *重要な更新プログラムとセキュリティ更新プログラム*<br> *その他* |
| UpdateSeverity | 脆弱性の重大度の評価。 値は次のとおりです。<br> *重大*<br> *重要*<br> *中*<br> *低* |
| UpdateTitle | 更新プログラムのタイトル。|

### <a name="query-update-record"></a>Update レコードを照会する

`Update` という種類のレコードが作成されます。これは、使用可能な更新プログラムと、コンピューターでのそのインストール状態を表します。 これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | 説明 |
|----------|-------------|
| ApprovalSource | Windows オペレーティング システムにのみ適用されます。 レコードの承認のソース。 値は [Microsoft Update] です。 |
| Approved | レコードが承認されている場合は True、それ以外の場合は False。 |
| 分類 | 承認の分類。 値は [更新プログラム] です。 |
| Computer | レポート コンピューターの完全修飾ドメイン名。 |
| ComputerEnvironment | 環境。 可能な値は、[Azure] または [Azure 以外] です。 |
| MSRCBulletinID | セキュリティ情報 ID 番号。 |
| MSRCSeverity | 脆弱性の重大度の評価。 値は次のとおりです。<br> Critical<br> 重要<br> 中<br> 低 |  
| KBID | Windows Update のサポート技術情報の記事 ID。 |
| ManagementGroupName | Operations Manager 管理グループまたは Log Analytics ワークスペースの名前。 |
| UpdateID | ソフトウェア更新プログラムの一意識別子。 |
| RevisionNumber | 更新プログラムの特定のリビジョンのリビジョン番号。 |
| 省略可能 | レコードが省略可能な場合は True、それ以外の場合は False。 |
| RebootBehavior | 更新プログラムをインストールまたはアンインストールした後の再起動動作。 |
| _ResourceId | レコードに関連付けられているリソースの一意識別子。 |
| Type | レコードの種類。 値は [更新] です。 |
| VMUUID | 仮想マシンの一意識別子。 |
| MG | 管理グループまたは Log Analytics ワークスペースの一意識別子。 |
| TenantId | Azure Active Directory の組織のインスタンスを表す一意識別子。 |
| SourceSystem | レコードのソース システム。 値は `OperationsManager`です。 |
| TimeGenerated | レコードの作成日時。 |
| SourceComputerId | ソース コンピューターを表す一意識別子。 |
| タイトル | 更新プログラムのタイトル。 |
| PublishedDate (UTC) | Windows Update から更新プログラムをダウンロードしてインストールする準備ができた日付。  |
| UpdateState | 更新プログラムの現在の状態。 |
| Product | 更新プログラムが適用される製品。 |
| SubscriptionId | Azure サブスクリプションの一意識別子。 |
| ResourceGroup | リソースが属しているリソース グループの名前。 |
| ResourceProvider | リソース プロバイダー。 |
| リソース | リソースの名前。 |
| ResourceType | リソースの種類。 |

### <a name="query-update-agent-record"></a>Update Agent レコードを照会する

コンピューター上の更新エージェントの詳細を提供する `UpdateAgent` の種類のレコードが作成されます。 これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | 説明 |
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | |
| AutomaticUpdateEnabled | |
| Computer | レポート コンピューターの完全修飾ドメイン名。 |
| DaySinceLastUpdateBucket | |
| ManagementGroupName | Operations Manager 管理グループまたは Log Analytics ワークスペースの名前。 |
| OSVersion | オペレーティング システムのバージョン。 |
| サーバー | |
| SourceHealthServiceId | Log Analytics Windows エージェント ID を表す一意識別子。 |
| SourceSystem | レコードのソース システム。 値は `OperationsManager`です。 |
| TenantId | Azure Active Directory の組織のインスタンスを表す一意識別子。 |
| TimeGenerated | レコードの作成日時。 |
| Type | レコードの種類。 値は [更新] です。 |
| WindowsUpdateAgentVersion | Windows Update エージェントのバージョン。 |
| WSUSServer | Windows Update エージェントで問題が発生した場合のエラー。トラブルシューティングに役立ちます。 |

### <a name="query-update-deployment-status-record"></a>Update Deployment Status レコードを照会する

コンピューターごとにスケジュールされたデプロイの更新プログラムのデプロイの状態を提供する、`UpdateRunProgress` の種類のレコードが作成されます。 これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | 説明 |
|----------|-------------|
| Computer | レポート コンピューターの完全修飾ドメイン名。 |
| ComputerEnvironment | 環境。 値は [Azure] または [Azure 以外] です。 |
| CorrelationId | 更新プログラムに対して実行される Runbook ジョブの一意識別子。 |
| EndTime | 同期プロセスが終了した時刻。 "*このプロパティは現在使用されていません。TimeGenerated を参照してください。* " |
| ErrorResult | 更新プログラムのインストールに失敗した場合に生成される Windows Update のエラー コード。 |
| InstallationStatus | クライアント コンピューター上の更新プログラムの考えられるインストール状態。<br> `NotStarted` - ジョブはまだトリガーされていません。<br> `FailedToStart` - マシンでジョブを開始できません。<br> `Failed` - ジョブは開始されましたが、例外が発生して失敗しました。<br> `InProgress` - ジョブは進行中です。<br> `MaintenanceWindowExceeded` - 実行が残っているが、メンテナンス期間に達した場合。<br> `Succeeded` - ジョブが成功しました。<br> `InstallFailed` - 更新を正常にインストールできませんでした。<br> `NotIncluded`<br> `Excluded` |
| KBID | Windows Update のサポート技術情報の記事 ID。 |
| ManagementGroupName | Operations Manager 管理グループまたは Log Analytics ワークスペースの名前。 |
| OSType | オペレーティング システムの種類。 値は [Windows] または [Linux] です。 |
| Product | 更新プログラムが適用される製品。 |
| リソース | リソースの名前。 |
| ResourceId | レコードに関連付けられているリソースの一意識別子。 |
| ResourceProvider | リソース プロバイダー。 |
| ResourceType | リソースの種類。 |
| SourceComputerId | ソース コンピューターを表す一意識別子。 |
| SourceSystem | レコードのソース システム。 値は `OperationsManager`です。 |
| StartTime | 更新プログラムのインストールがスケジュールされている時刻。 "*このプロパティは現在使用されていません。TimeGenerated を参照してください。* " |
| SubscriptionId | Azure サブスクリプションの一意識別子。 |
| SucceededOnRetry | 更新プログラムの実行が最初の試行で失敗したかどうかと、現在の操作が再試行であるかどうかを示す値。 |
| TimeGenerated | レコードの作成日時。 |
| タイトル | 更新プログラムのタイトル。 |
| Type | 更新の種類。 値は `UpdateRunProgress`です。 |
| UpdateId | ソフトウェア更新プログラムの一意識別子。 |
| VMUUID | 仮想マシンの一意識別子。 |
| ResourceId | レコードに関連付けられているリソースの一意識別子。 |

### <a name="query-update-summary-record"></a>Update Summary レコードを照会する

コンピューターごとの更新の概要を提供する `UpdateSummary` の種類のレコードが作成されます。 これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | 説明 |
|----------|-------------|
| Computer | レポート コンピューターの完全修飾ドメイン名。 |
| ComputerEnvironment | 環境。 値は [Azure] または [Azure 以外] です。 |
| CriticalUpdatesMissing | 適用可能だが、インストールされていない重要な更新プログラムの数。 |
| ManagementGroupName | Operations Manager 管理グループまたは Log Analytics ワークスペースの名前。 |
| NETRuntimeVersion | Windows コンピューターにインストールされている .NET Framework のバージョン。 |
| OldestMissingSecurityUpdateBucket | 不足している最も古いセキュリティ バケットの指定子。 値は次のとおりです。<br> 最近 (値が 30 日未満の場合)<br> 30 日前<br> 60 日前<br> 90 日前<br> 120 日前<br> 150 日前<br> 180 日前<br> それより以前 (値が 180 日を超える場合)。 |
| OldestMissingSecurityUpdateInDays | 適用可能として検出された、インストールされていない最も古い更新プログラムの合計日数。 |
| OsVersion | オペレーティング システムのバージョン。 |
| OtherUpdatesMissing | インストールされていない更新プログラムの検出数。 |
| リソース | レコードのリソースの名前。 |
| ResourceGroup | リソースを含むリソース グループの名前。 |
| ResourceId | レコードに関連付けられているリソースの一意識別子。 |
| ResourceProvider | リソース プロバイダー。 |
| ResourceType | リソースの種類。 |
| RestartPending | 再起動が保留中の場合は True、それ以外の場合は False。 |
| SecurityUpdatesMissing | 適用可能だが、インストールされていないセキュリティ更新プログラムの数。|
| SourceComputerId | 仮想マシンの一意識別子。 |
| SourceSystem | レコードのソース システム。 値は `OpsManager`です。 |
| SubscriptionId | Azure サブスクリプションの一意識別子。 |
| TimeGenerated | レコードの作成日時。 |
| TotalUpdatesMissing | 適用可能だが、インストールされていない更新プログラムの合計数。 |
| Type | レコードの種類。 値は `UpdateSummary`です。 |
| VMUUID | 仮想マシンの一意識別子。 |
| WindowsUpdateAgentVersion | Windows Update エージェントのバージョン。 |
| WindowsUpdateSetting | Windows Update エージェントの状態。 次のいずれかの値になります。<br> `Scheduled installation`<br> `Notify before installation`<br> `Error returned from unhealthy WUA agent` |
| WSUSServer | Windows Update エージェントで問題が発生した場合のエラー。トラブルシューティングに役立ちます。 |
| _ResourceId | レコードに関連付けられているリソースの一意識別子。 |

## <a name="sample-queries"></a>サンプル クエリ

以下のセクションは、Update Management に関して収集された更新レコードのログ クエリの例です。

### <a name="confirm-that-non-azure-machines-are-enabled-for-update-management"></a>Update Management の対象として Azure 以外のマシンが有効になっていることを確認する

直接接続されたマシンが Azure Monitor ログと通信していることを確認するには、次のいずれかのログ検索を実行します。

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Windows コンピューターでは、次の情報を調べて、Azure Monitor ログとエージェントの接続を確認できます。

1. [コントロール パネル] から **[Microsoft Monitoring Agent]** を開きます。 **[Azure Log Analytics]** タブで、エージェントに"**Microsoft Monitoring Agent は Log Analytics に正常に接続しました**" というメッセージが表示されます。

1. Windows イベント ログを開きます。 **アプリケーションとサービス ログ\Operations Manager** に移動して、ソースの **[サービス コネクタ]** でイベント ID 3000 および 5002 を検索します。 これらのイベントは、コンピューターが Log Analytics ワークスペースに登録され、構成を受信していることを示しています。

エージェントが Azure Monitor ログと通信できず、ファイアウォールまたはプロキシ サーバーを介してインターネットと通信するよう構成されている場合は、ファイアウォールまたはプロキシ サーバーが正しく構成されていることを確認します。 ファイアウォールまたはプロキシ サーバーが正しく構成されていることを確認する方法については、[Windows エージェントのネットワーク構成](../../azure-monitor/agents/agent-windows.md)または [Linux エージェントのネットワーク構成](../../azure-monitor/vm/quick-collect-linux-computer.md)に関する記事を参照してください。

> [!NOTE]
> Linux システムがプロキシまたは Log Analytics ゲートウェイと通信するよう構成されている場合で、かつ Update Management を有効にしようとしている場合は、次のコマンドを実行し、ファイルに対する読み取り権限を omiuser グループに付与するよう、`proxy.conf` のアクセス許可を更新します。
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

新しく追加された Linux エージェントは、評価が完了した後、状態が "**更新済み**" と表示されます。 このプロセスには最大で 6 時間かかります。

Operations Manager 管理グループが Azure Monitor ログと通信していることを確認する方法については、[Operations Manager と Azure Monitor ログの統合の検証](../../azure-monitor/agents/om-agents.md#validate-operations-manager-integration-with-azure-monitor)に関するページを参照してください。

### <a name="single-azure-vm-assessment-queries-windows"></a>単一の Azure VM 評価クエリ (Windows)

VMUUID 値を、クエリの対象である仮想マシンの VM GUID に置き換えます。 Azure Monitor ログで次のクエリを実行することで、使用する必要がある VMUUID を確認できます。`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>不足している更新プログラムの概要

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>不足している更新プログラム一覧

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

### <a name="single-azure-vm-assessment-queries-linux"></a>単一の Azure VM 評価クエリ (Linux)

Linux のディストリビューションによっては、Azure Resource Manager に由来する VMUUID 値と、Azure Monitor ログに格納されている値との間で[エンディアン](https://en.wikipedia.org/wiki/Endianness)が一致しない場合があります。 次のクエリは、いずれかのエンディアンでの一致をチェックします。 結果を適切に返すために、VMUUID 値を GUID のビッグエンディアン形式とリトルエンディアン形式に置き換えます。 Azure Monitor ログで次のクエリを実行することで、使用する必要がある VMUUID を確認できます。`Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>不足している更新プログラムの概要

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>不足している更新プログラム一覧

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

### <a name="multi-vm-assessment-queries"></a>マルチ VM の評価クエリ

#### <a name="computers-summary"></a>コンピューターの概要

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount
```

#### <a name="missing-updates-summary"></a>不足している更新プログラムの概要

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="computers-list"></a>コンピューター一覧

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

#### <a name="missing-updates-list"></a>不足している更新プログラム一覧

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="next-steps"></a>次のステップ

* Azure Monitor ログの詳細については、[Azure Monitor ログ](../../azure-monitor/logs/log-query-overview.md)に関するページを参照してください。
* アラートの設定については、「[アラートを構成する](configure-alerts.md)」を参照してください。
