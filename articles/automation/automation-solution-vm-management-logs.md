---
title: Azure Automation の Start/Stop VMs during off-hours からログを照会する
description: この記事では、Azure Monitor を使用して、Start/Stop VMs during off-hours によって生成されたログ データのクエリを実行する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: de013b6ccd924f50ffe12fcba1285b121eece5f7
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83827558"
---
# <a name="query-logs-from-startstop-vms-during-off-hours"></a>Start/Stop VMs during off-hours からログを照会する

Azure Automation により、ジョブ ログとジョブ ストリームの 2 種類のレコードが Log Analytics のリンクされたワークスペースに転送されます。 この記事では、Azure Monitor 内で[クエリ](../azure-monitor/log-query/log-query-overview.md)に使用できるデータを確認します。

## <a name="job-logs"></a>ジョブ ログ

|プロパティ | 説明|
|----------|----------|
|Caller |  操作を開始したユーザー。 スケジュールされたジョブのシステムまたは電子メール アドレスが記録されます。|
|カテゴリ | データの種類の分類。 Automation の場合、値は JobLogs です。|
|CorrelationId | GUID。Runbook ジョブの関連付け ID です。|
|JobId | GUID。Runbook ジョブの ID です。|
|operationName | Azure で実行された操作の種類を指定します。 Automation の場合、値は Job です。|
|resourceId | Azure のリソースの種類を指定します。 Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
|ResourceGroup | Runbook ジョブのリソース グループ名を指定します。|
|ResourceProvider | デプロイと管理の対象となるリソースのプロバイダー (Azure サービス) を指定します。 Automation の場合、その値は Azure Automation になります。|
|ResourceType | Azure のリソースの種類を指定します。 Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
|resultType | Runbook ジョブの状態。 次のいずれかの値になります。<br>- 開始済み<br>- 停止済み<br>- 中断<br>- 失敗<br>- 成功|
|resultDescription | Runbook ジョブの結果の状態について説明します。 次のいずれかの値になります。<br>- ジョブが開始されました<br>- ジョブが失敗しました<br>- ジョブが完了しました|
|RunbookName | Runbook の名前を指定します。|
|SourceSystem | 送信されたデータのソース システムを指定します。 Automation の場合、値は OpsManager です|
|StreamType | イベントの種類を指定します。 次のいずれかの値になります。<br>- 詳細<br>- 出力<br>- エラー<br>- 警告|
|SubscriptionId | ジョブのサブスクリプション ID を指定します。
|Time | Runbook ジョブが実行された日付と時刻。|

## <a name="job-streams"></a>ジョブ ストリーム

|プロパティ | 説明|
|----------|----------|
|Caller |  操作を開始したユーザー。 スケジュールされたジョブのシステムまたは電子メール アドレスが記録されます。|
|カテゴリ | データの種類の分類。 Automation の場合、値は JobStreams です。|
|JobId | GUID。Runbook ジョブの ID です。|
|operationName | Azure で実行された操作の種類を指定します。 Automation の場合、値は Job です。|
|ResourceGroup | Runbook ジョブのリソース グループ名を指定します。|
|resourceId | Azure のリソース ID を指定します。 Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
|ResourceProvider | デプロイと管理の対象となるリソースのプロバイダー (Azure サービス) を指定します。 Automation の場合、その値は Azure Automation になります。|
|ResourceType | Azure のリソースの種類を指定します。 Automation の場合、値は Runbook に関連付けられている Automation アカウントです。|
|resultType | イベントが生成された時点における Runbook ジョブの結果。 次の値になります。<br>- 処理中|
|resultDescription | Runbook からの出力ストリームが含まれます。|
|RunbookName | Runbook の名前。|
|SourceSystem | 送信されたデータのソース システムを指定します。 Automation の場合、値は OpsManager です。|
|StreamType | ジョブ ストリームの種類。 次のいずれかの値になります。<br>- 処理中<br>- 出力<br>- 警告<br>- エラー<br>- デバッグ<br>- 詳細|
|Time | Runbook ジョブが実行された日付と時刻。|

**JobLogs** または **JobStreams** のカテゴリ レコードを取得する何らかのログ検索を実行するときは、**JobLogs** ビューまたは **JobStreams** ビューを選択すると、検索から返された最新情報が一連のタイルに集約されて表示されます。

## <a name="sample-log-searches"></a>サンプル ログ検索

以下の表は、Start/Stop VMs during off-hours によって収集されたジョブ レコードを探すログ検索の例です。

|クエリ | 説明|
|----------|----------|
|正常に終了した Runbook ScheduledStartStop_Parent のジョブを検索する | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|正常に完了していない Runbook ScheduledStartStop_Parent のジョブを検索する | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|正常に終了した Runbook SequencedStartStop_Parent のジョブを検索する | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|正常に完了していない Runbook SequencedStartStop_Parent のジョブを検索する | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>次のステップ

* 機能を設定するには、「[Start/Stop VMs during off-hours を構成する](automation-solution-vm-management-config.md)」を参照してください。
* 機能のデプロイ中のログ アラートについては、[Azure Monitor を使用したログ アラートの作成](../azure-monitor/platform/alerts-log.md)に関する記事を参照してください。
* 機能のエラーを解決するには、「[Start/Stop VMs during off-hours の問題のトラブルシューティング](troubleshoot/start-stop-vm.md)」を参照してください。