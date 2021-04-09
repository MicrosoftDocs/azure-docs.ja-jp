---
title: Azure Monitor のログ アラートのトラブルシューティング | Microsoft Docs
description: Azure のログ アラート ルールに関する一般的な問題、エラー、解決方法。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 6b495e8e700f146f5e6c117413456311ac0cdd5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037882"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Azure Monitor のログ アラートのトラブルシューティング  

この記事では、Azure Monitor 内のログ アラートに関する一般的な問題の解決方法について説明します。 また、ログ アラートの機能や構成でよく発生する問題の解決策も紹介しています。

ログ アラートを使用すると、ユーザーは [Log Analytics](../logs/log-analytics-tutorial.md) クエリを使用して、設定した頻度で毎回リソース ログを評価し、結果に基づいてアラートを発生させることができます。 [アクション グループ](./action-groups.md)を使用することで、ルールによって 1 つ以上のアクションをトリガーできます。 [ログ アラートの機能と用語の詳細について参照してください](alerts-unified-log.md)。

> [!NOTE]
> この記事では、トリガーされ Azure portal に表示されたアラート ルールの通知が、関連付けられているアクション グループで対処されなかった場合については考慮していません。 そのような場合は、[ここ](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)でトラブルシューティングに関する詳細を参照してください。

## <a name="log-alert-didnt-fire"></a>ログ アラートが作動しない

### <a name="data-ingestion-time-for-logs"></a>ログのデータ インジェストの時間

Azure Monitor では、世界中にわたる顧客のテラバイト単位のログを処理します。それにより、[ログ インジェストの待機時間](../logs/data-ingestion-time.md)が発生する可能性があります。

ログは半構造化されたデータであり、本質的にメトリックよりも潜在的な状態で存在します。 発せられたアラートの遅延が 4 分以上発生している場合は、[メトリック アラート](alerts-metric-overview.md)の使用を検討してください。 [ログのメトリック アラート](alerts-metric-logs.md)を使用して、ログからメトリック ストアにデータを送信できます。

システムでは、待機時間を軽減するため、アラートの評価が複数回再試行されます。 データが到着するとアラートが発生します。ほとんどの場合、ログ レコードの時刻とは等しくありません。

### <a name="incorrect-query-time-range-configured"></a>クエリの時間の範囲が正しく構成されない

クエリの時間の範囲は、ルール条件の定義で設定されます。 このフィールドは、ワークスペースと Application Insights の場合は **[期間]** という名前で、他の種類のリソースの場合はすべて、 **[クエリの時間の範囲のオーバーライド]** という名前です。 Log Analytics の場合と同様に、時間の範囲によって、クエリ データは指定された期間に制限されます。 クエリで **ago** コマンドが使用されていても、時間の範囲が適用されます。 

たとえば、時間範囲が 60 分のときは、テキストに **ago(1d)** が含まれていたとしても、クエリでスキャンされるのは 60 分です。 時間範囲とクエリ時間のフィルター処理は、一致している必要があります。 この例の場合、 **[期間]**  /  **[クエリの時間の範囲のオーバーライド]** を 1 日に変更すると、予期した通り機能します。時間の範囲

![期間](media/alerts-troubleshoot-log/LogAlertTimePeriod.png)

### <a name="actions-are-muted-in-the-alert-rule"></a>アラート ルールのアクションがミュートされる

ログ アラートには、発生したアラート アクションを一定時間ミュートするオプションがあります。 このフィールドは、ワークスペースと Application Insights では **[アラートを表示しない]** という名前です。 他のすべてのリソースの種類では、 **[アクションのミュート]** という名前です。 

サービスの問題が原因でアラートによってアクションが起きなかったと思うことが、よくある問題です。 実際には、ルールの構成によってミュートされたのです。

![Suppress alerts (アラートの抑制)](media/alerts-troubleshoot-log/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-with-splitting-using-the-legacy-log-analytics-api"></a>レガシ Log Analytics API を使用したメトリック測定アラート ルールの分割

[メトリック測定](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)は、まとめられた時系列の結果に基づく種類のログ アラートです。 これらのルールを使用すると、列単位でグループ化して[アラートを分割](alerts-unified-log.md#split-by-alert-dimensions)できます。 レガシ Log Analytics API を使用している場合は、予期したとおりに分割が機能しません。 レガシ API でグループ化を選択することはサポートされていません。

現在の ScheduledQueryRules API では、[[メトリック測定]](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) ルールに **[集約]** を設定できます。これは、予期したとおりに機能します。 [現在の ScheduledQueryRules API への切り替えの詳細について確認してください](../alerts/alerts-log-api-switch.md)。

## <a name="log-alert-fired-unnecessarily"></a>ログ アラートが不必要に作動する

構成した [Azure Monitor のログ アラート ルール](./alerts-log.md)が予期せずトリガーされる場合があります。 以下のセクションでは、よくある原因をいくつか説明します。

### <a name="alert-triggered-by-partial-data"></a>一部のデータによってアラートがトリガーされる

Azure Monitor では、世界中にわたる顧客のテラバイト単位のログを処理します。それにより、[ログ インジェストの待機時間](../logs/data-ingestion-time.md)が発生する可能性があります。

ログは半構造化されたデータであり、本質的にメトリックよりも潜在的な状態で存在します。 発せられたアラートで、誤発生が多数起きている場合は、[メトリック アラート](alerts-metric-overview.md)の使用を検討してください。 [ログのメトリック アラート](alerts-metric-logs.md)を使用して、ログからメトリック ストアにデータを送信できます。

ログ アラートが最適に機能するのは、ログ内でデータの検出を試みるときです。 ログ内にデータがないことを検出しようとするときの効果は低下します。 たとえば、仮想マシンのハートビートに関するアラートです。 

誤ったアラートを防止する機能は組み込まれていますが、それでも、非常に潜在性の高いデータ (約 30 分以上) や待機時間が急増することがあるデータで発生する可能性があります。

### <a name="query-optimization-issues"></a>クエリ最適化に関する問題

クエリは、負荷の軽減とアラート待機時間の短縮のため、アラート サービスによって変更されます。 アラート フローは、問題を示す結果をアラートに変換するために構築されました。 たとえば、次のようなクエリの場合を考えてみましょう。

``` Kusto
SecurityEvent
| where EventID == 4624
```

ユーザーの意図がアラートを発することの場合は、この種類のイベントが発生すると、アラート ロジックによってクエリに `count` が追加されます。 実行されるクエリは次のようになります。

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

クエリにアラート ロジックを追加する必要はなく、それを行うと問題を引き起こす可能性さえあります。 上の例では、クエリに `count` を含めると、アラート サービスによって `count` の `count` が行われるため、常に値 1 が返されます。

最適化されたクエリは、ログ アラート サービスによって実行されるものです。 変更されたクエリは、Log Analytics の[ポータル](../logs/log-query-overview.md)または [API](/rest/api/loganalytics/) で実行できます。

ワークスペースと Application Insights の場合は **[実行するクエリ]** という名前で、条件ペインにあります。 他のリソースの種類ではすべて、条件タブで **[最終的なアラート クエリを表示]** を選択します。

![実行するクエリ](media/alerts-troubleshoot-log/LogAlertPreview.png)

## <a name="log-alert-was-disabled"></a>ログ アラートが無効になった

以降のセクションで、Azure Monitor によってログ アラート ルールが無効にされた可能性があるいくつかの理由を挙げます。 [ルールが無効にされたときに送信されるアクティビティ ログの例](#activity-log-example-when-rule-is-disabled)も含めてあります。

### <a name="alert-scope-no-longer-exists-or-was-moved"></a>アラート スコープが存在しなくなったか移動された

アラート ルールのスコープ リソースが有効でなくなると、ルールの実行は失敗します。 この場合は課金も停止します。

Azure Monitor では、連続して失敗する場合は 1 週間後にログ アラートが無効にされます。

### <a name="query-used-in-a-log-alert-isnt-valid"></a>ログ アラートで使用されているクエリが有効でない

ログ アラート ルールが作成されるときには、クエリの構文が正しいか検証されます。 ただし場合によっては、ログ アラート ルールで指定されたクエリが失敗し始めることがあります。 一般的な理由を以下にいくつか示します。

- ルールが API によって作成され、検証がユーザーによってスキップされました。
- クエリが[複数のリソースに対して実行](../logs/cross-workspace-query.md)され、1 つ以上のリソースが削除または移動されました。
- [クエリが失敗する](https://dev.loganalytics.io/documentation/Using-the-API/Errors)のは以下のためです。
    - ログ記録ソリューションが[ワークスペースにデプロイ](../insights/solutions.md#install-a-monitoring-solution)されなかったために、テーブルが作成されていません。
    - クエリのテーブルへのデータ フローが 30 日より長く停止しました。
    - データ フローが開始されていないため、[カスタム ログ テーブル](../agents/data-sources-custom-logs.md)がまだ作成されていません。
- [クエリ言語](/azure/kusto/query/)が変更され、コマンドと関数の形式が変更されています。 そのため以前指定したクエリが有効ではなくなっています。

この動作は、[Azure Advisor](../../advisor/advisor-overview.md) で警告されています。 影響を受けるログ アラート ルールに関する推奨事項が追加されます。 使用されるカテゴリは "高可用性" で、影響は中程度です。また、"確実に監視するためログ アラート ルールを修復します" という説明が含まれています。

## <a name="alert-rule-quota-was-reached"></a>アラート ルール クォータへの到達

サブスクリプションおよびリソースあたりのログ検索アラート ルールの数には、[こちら](../service-limits.md)に記載されているクォータ制限が適用されます。

### <a name="recommended-steps"></a>推奨される手順
    
クォータ制限に達した場合は、次の手順が問題の解決に役立つ場合があります。

1. 今後使用しないログ検索アラート ルールを削除または無効にします。
1. ルール数を減らすため、[ディメンション別のアラートの分割](alerts-unified-log.md#split-by-alert-dimensions)を使用してみます。 これらのルールでは、多くのリソースと検出ケースを監視できます。
1. クォータ制限を増やす必要がある場合は、引き続きサポート リクエストを開き、以下の情報を提供します。

    - クォータ制限を増やす必要があるサブスクリプション ID とリソース ID。
    - クォータの引き上げの理由。
    - クォータを引き上げるリソースの種類:**Log Analytics**、**Application Insights** など。
    - 要求されるクォータ制限。


### <a name="to-check-the-current-usage-of-new-log-alert-rules"></a>新しいログ アラート ルールの現在の使用状況を確認するには
    
#### <a name="from-the-azure-portal"></a>Azure portal から

1. *[アラート]* 画面を開き、 *[アラート ルールの管理]* を選択します
2. *[サブスクリプション]* ドロップダウン コントロールを使用して、関連するサブスクリプションをフィルター処理します
3. 特定のリソース グループ、リソースの種類、またはリソースをフィルター処理しないようにしてください
4. *[シグナルの種類]* ドロップダウン コントロールで、[ログ検索] を選択します。
5. *[状態]* ドロップダウン コントロールが [有効] に設定されていることを確認します
6. ログ検索アラート ルールの合計数がルールの一覧の上に表示されます

#### <a name="from-api"></a>API から

- PowerShell - [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule)
- REST API - [サブスクリプションごとの一覧](/rest/api/monitor/scheduledqueryrules/listbysubscription)

## <a name="activity-log-example-when-rule-is-disabled"></a>ルールが無効になっているときのアクティビティ ログの例

クエリが 7 日間続けて失敗した場合、Azure Monitor によってログ アラートが無効にされ、ルールの課金が停止されます。 Azure Monitor がログ アラートを無効にした正確な時間は、[Azure アクティビティ ログ](../../azure-resource-manager/management/view-activity-logs.md)で調べることができます。 こちらの例を参照してください。

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

## <a name="next-steps"></a>次のステップ

- [Azure でのログ アラート](./alerts-unified-log.md)について学習します。
- [ログ アラートの構成](../logs/log-query-overview.md)に関する詳細を確認します。
- [ログ クエリ](../logs/log-query-overview.md)についてさらに学習します