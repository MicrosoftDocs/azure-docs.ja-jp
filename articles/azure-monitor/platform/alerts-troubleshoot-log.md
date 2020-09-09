---
title: Azure Monitor のログ アラートのトラブルシューティング | Microsoft Docs
description: Azure のログ アラート ルールに関する一般的な問題、エラー、解決方法。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: d61e052b10b7255cac37531f889324075d596f3c
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87828457"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Azure Monitor のログ アラートのトラブルシューティング  

この記事では、Azure Monitor 内のログ アラートに関する一般的な問題の解決方法について説明します。 また、ログ アラートの機能や構成でよく発生する問題の解決策も紹介しています。

"*ログ アラート*" という用語は、[Azure Log Analytics ワークスペース](../log-query/get-started-portal.md)または [Azure Application Insights](../log-query/log-query-overview.md) のログ クエリで作動するルールを指します。 機能、用語、種類については、「[Azure Monitor でのログ アラート](./alerts-unified-log.md)」を参照してください。

> [!NOTE]
> この記事では、トリガーされ Azure portal に表示されたアラート ルールの通知が、関連付けられているアクション グループで対処されなかった場合については考慮していません。 このようなケースについては、「[Azure portal でのアクション グループの作成および管理](./action-groups.md)」を参照してください。

## <a name="log-alert-didnt-fire"></a>ログ アラートが作動しない

ここでは、構成された [Azure Monitor のログ アラート ルール](./alerts-log.md)の状態が、[期待どおりに*起動済み*](./alerts-managing-alert-states.md)とならない一般的な理由をいくつか示します。

### <a name="data-ingestion-time-for-logs"></a>ログのデータ インジェストの時間

ログ アラートは、[Log Analytics](../log-query/get-started-portal.md) または [Application Insights](../log-query/log-query-overview.md) に基づいてクエリを定期的に実行します。 Azure Monitor は、世界中の多様なソースの顧客が送信する何テラバイトものデータを処理するため、さまざまな時間遅延の影響を受けやすくなっています。 詳細については、「[Azure Monitor でのログ データ インジェスト時間](./data-ingestion-time.md)」を参照してください。

システムで、必要なデータが取り込まれていないことが認識されると、待機してアラート クエリを何度も再試行され、遅延が緩和されます。 システムによって、指数関数的に増加する待機時間が設定されます。 ログ アラートはデータを入手しないとトリガーされないので、ログ データの取り込みが遅いことが遅延の原因である場合があります。

### <a name="incorrect-time-period-configured"></a>構成されている期間が正しくない

[ログ アラートの用語](./alerts-unified-log.md#log-search-alert-rule---definition-and-types)の記事の説明のとおり、構成に記述されている期間がクエリの時間範囲になります。 クエリは、この時間範囲に作成されたレコードのみを返します。

この期間は、ログ クエリ用にフェッチされるデータを制限して不正使用を防いだり、ログ クエリで使用されている (**ago** などの) すべての時間コマンドを回避したりします。 たとえば、期間が 60 分に設定されていて、クエリが午後 1 時 15 分に実行された場合は、午後 12 時 15 分から午後 1 時 15 分までの間に作成されたレコードだけが、ログ クエリで使用されます。 ログ クエリが **ago (1d)** のような時間コマンドを使用する場合でも、クエリでは期間がその間隔に設定されているため、午後 12 時 15 分から午後 1 時 15 分までのデータしか使用しません。

構成内の期間がクエリと一致していることを確認してください。 前出の例で、ログ クエリが緑色のマーカーの **ago (1d)** を使用する場合、期間は 24 時間または 1,440 分 (赤色で示した箇所) に設定される必要があります。 この設定によって、クエリは意図したとおりに実行されます。

![期間](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>[アラートを表示しない] オプションが設定されている

[Azure portal でのログ アラート ルールの作成](./alerts-log.md#create-a-log-alert-rule-with-the-azure-portal)に関する記事の手順 8 で説明しているとおり、ログ アラートには、構成された期間、トリガーや通知アクションを抑止する **[アラートを表示しない]** オプションがあります。 この結果、警告が作動しなかったと思うことがあります。 実際には起動されたのですが、抑止されたのです。  

![Suppress alerts (アラートの抑制)](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>メトリック測定のアラート ルールが正しくない

*メトリック測定ログ アラート*は、特別な機能を備えたログ アラートのサブタイプです。このアラートのクエリ構文には制限があります。 メトリック測定のログ アラートのルールでは、クエリからの出力がメトリックの時系列である必要があります。 つまり、出力は、対応する集計値と、同じサイズの個別の期間を持つテーブルです。

そのテーブルの **AggregatedValue** と一緒に変数を追加で含めることもできます。 これらの変数はテーブルの並べ替えに使用できます。

たとえば、メトリック測定のログ アラート ルールが次のように構成されていたとします。

- `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)` のクエリ  
- 期間: 6 時間
- しきい値: 50
- アラート ロジック: 連続 3 回の違反
- **集計基準**: **$table** を選択

コマンドには **summarize ... by** が含まれ、2 つの変数 (**timestamp** と **$table**) が指定されているため、システムは**集計基準**として **$table** を選択します。 システムにより、次のスクリーンショットのとおり、結果テーブルが **$table** フィールドで並べ替えられます。 その後、テーブルの種類 (**availabilityResults** など) ごとに複数の **AggregatedValue** が確認され、連続 3 回以上の違反があったかどうかが確認されます。

![複数の値を使用したメトリック測定クエリの実行](media/alert-log-troubleshoot/LogMMQuery.png)

**集計基準**は **$table** に定義されるので、データは **$table** 列に格納されます (赤色の箇所)。 それから**集計基準**フィールドの種類をグループ化して探します。

たとえば、 **$table** の **availabilityResults** の値は、1 つのプロットまたはエンティティと見なされます (オレンジ色の箇所)。 アラート サービスは、この値のプロット/エンティティから、3 つの連続した違反がないかをチェックします (緑色の箇所)。 違反により、テーブル値 **availabilityResults** に対し警告がトリガーされます。

同様に、 **$table** のその他の任意の値で違反が 3 回連続している場合、同じものに対して別のアラート通知がトリガーされます。 アラート サービスは、1 つのプロット/エンティティの値 (オレンジ色の箇所) を時間別に自動的に並べ替えます。

ここで、メトリック測定ログ アラート ルールが変更され、クエリが `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` になったとしましょう。 残りの構成は、連続 3 回の違反のアラート ロジックも含め、前回と同じままです。 この場合の**集計基準**オプションは既定で **timestamp** です。 **summarize ... by** のクエリに指定された値は 1 つ (**timestamp**) だけでした。 前述の例と同様、実行終了時点の出力は以下のようになります。

   ![単一の値を使用したメトリック測定クエリの実行](media/alert-log-troubleshoot/LogMMtimestamp.png)

**集計基準**は **timestamp** で定義されているため、データは **timestamp** 列で並べ替えられます (赤色の箇所)。 そのため **timestamp** でグループ化します。 たとえば、`2018-10-17T06:00:00Z` の値は、1 つのプロットまたはエンティティと見なされます (オレンジ色の箇所)。 この値のプロットまたはエンティティでは、アラート サービスによって連続する違反が検出されません (それぞれの **timestamp** 値にはエントリが 1 つしかないため)。 したがって、アラートはトリガーされません。 このような場合、ユーザーは以下のいずれかを実行する必要があります。

- ダミー変数または既存の変数 ( **$table** のような) を追加して、**集計基準**フィールドを使用して正しい並べ替えを行います。
- または、**違反の合計数**に基づいたアラート ロジックを使用するようアラート ルールを再構成します。

## <a name="log-alert-fired-unnecessarily"></a>ログ アラートが不必要に作動する

[Azure アラート](./alerts-managing-alert-states.md)で表示したとき、構成した [Azure Monitor のログ アラート ルール](./alerts-log.md)が予期せずトリガーされる場合があります。 以下のセクションでは、よくある原因をいくつか説明します。

### <a name="alert-triggered-by-partial-data"></a>一部のデータによってアラートがトリガーされる

Log Analytics と Application Insights では、インジェストや処理が遅くなる場合があります。 ログ アラート クエリを実行すると、データが全くないか、一部しかないことがわかる場合があります。 詳細については、「[Azure Monitor でのログ データ インジェスト時間](./data-ingestion-time.md)」を参照してください

アラート ルールの構成方法によっては、アラートの実行時点でログにデータが存在しない場合や一部のデータしかない場合に、誤作動が生じる可能性があります。 このようなときは、アラート クエリまたは構成を変更することをお勧めします。

たとえば、分析クエリから返される結果の数が 5 件未満のときにトリガーするようログ アラート ルールが構成されている場合、データが存在しないとき (0 レコード) または一部の結果がある (1 レコード) ときに、アラートがトリガーされます。 ただし、データ インジェストの遅延が解消されると、同じクエリで完全なデータが使用され、10 個のレコードが返される可能性があります。

### <a name="alert-query-output-is-misunderstood"></a>アラート クエリの出力が誤って解釈される

分析クエリでログ アラートのロジックを提供するのはユーザーです。 分析クエリでは、さまざまなビッグ データや数学関数を使用できます。 アラート サービスでは、指定した期間のデータを使用して、指定した間隔でクエリを実行します。 アラート サービスでは、アラートの種類に基づいて、指定したクエリに微妙な変更を加えます。 この変更は、次の **[シグナル ロジックの構成]** 画面の **[実行するクエリ]** セクションで確認できます。

![実行するクエリ](media/alert-log-troubleshoot/LogAlertPreview.png)

**[実行するクエリ]** ボックスには、ログ アラート サービスによって実行される内容があります。 アラートを作成する前にアラート クエリの出力がどのようになるかを把握したい場合、[Analytics ポータル](../log-query/log-query-overview.md)または [Analytics API](/rest/api/loganalytics/) を使用し、ここに表示されているクエリと期間を実行できます。

## <a name="log-alert-was-disabled"></a>ログ アラートが無効になった

次のセクションでは、Azure Monitor によって[ログ アラート ルール](./alerts-log.md)が無効にされた可能性がある理由を挙げます。

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>アラートを作成したリソースがもうない

Azure Monitor で作成されるログ アラート ルールは、Azure Log Analytics ワークスペース、Azure Application Insights アプリ、Azure リソースなどの特定のリソースをターゲットとします。 ログ アラート サービスは、指定されたターゲットに対してルールで指定されている分析クエリを実行します。 しかしルールの作成後、ユーザーはしばしばログ アラート ルールのターゲットを Azure から削除したり Azure 内に移動したりします。 アラート ルールのターゲットが無効になるので、ルールの実行は失敗します。

そのような場合、ルールがかなりの期間 (1 週間など) 継続して実行できないとき、Azure Monitor はログ アラートを無効にし、お客様が不必要に課金されないようにします。 Azure Monitor がログ アラートを無効にした正確な時間は、[Azure アクティビティ ログ](../../azure-resource-manager/management/view-activity-logs.md)で調べることができます。 Azure Monitor がログ アラート ルールを無効にすると、Azure アクティビティ ログにイベントが追加されます。

Azure アクティビティ ログの次のサンプル イベントは、連続して失敗したために無効になったアラート ルールのものです。

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>ログ アラートで使用されているクエリが無効になっている

構成の一部として Azure Monitor に作成された各ログ アラート ルールでは、アラート サービスが定期的に実行する分析クエリが指定される必要があります。 分析クエリの構文は、ルールの作成時または更新時には正しく指定されているはずです。 しかし、しばらく時間が経過すると、ログ アラート ルールで指定したクエリで構文の問題が発生し、ルールの実行が失敗することがあります。 ログ アラート ルールで指定した分析クエリでエラーが発生する可能性がある一般的な理由をいくつか以下に示します。

- クエリが[複数のリソースにわたって実行される](../log-query/cross-workspace-query.md)よう記述されています。 そして指定した 1 つ以上のリソースが存在しなくなりました。
- アラート クエリを持つように構成された[メトリック測定タイプのログ アラート](./alerts-unified-log.md#metric-measurement-alert-rules)が構文規範に準拠していません
- 分析プラットフォームへのデータ フローがありません。 指定したクエリに対するデータがないので[クエリを実行するとエラーが発生します](https://dev.loganalytics.io/documentation/Using-the-API/Errors)。
- [クエリ言語](/azure/kusto/query/)が変更され、コマンドと関数の形式が変更されています。 したがって、アラート ルールで以前指定したクエリが無効になっています。

この動作は、[Azure Advisor](../../advisor/advisor-overview.md) で警告されています。 Azure Advisor の高可用性の影響が中程度のカテゴリに、"確実に監視するためログ アラート ルールを修復します" という説明で、特定のログ アラート ルールでの推奨事項が追加されています。

> [!NOTE]
> Azure Advisor により推奨事項が 7 日間表示された後も、ログ アラート ルールのアラート クエリが修正されない場合、Azure Monitor では、ルールが相当な期間 (7 日間) 継続して実行できない場合に、ログ アラートが無効にされ、お客様が不必要に課金されないようにします。 Azure Monitor がログ アラート ルールを無効にした正確な時間は、[Azure アクティビティ ログ](../../azure-resource-manager/management/view-activity-logs.md)でイベントを検索すると見つけることができます。

## <a name="alert-rule-quota-was-reached"></a>アラート ルール クォータへの到達

サブスクリプションおよびリソースあたりのログ検索アラート ルールの数には、[こちら](../service-limits.md)に記載されているクォータ制限が適用されます。

### <a name="recommended-steps"></a>推奨される手順
    
クォータ制限に達した場合は、次の手順が問題の解決に役立つ場合があります。

1. 今後使用しないログ検索アラート ルールを削除または無効にします。
2. クォータ制限を引き上げる必要がある場合は、サポート リクエストを開き、次の情報を提供してください。

    - クォータ制限を引き上げる必要があるサブスクリプション ID
    - クォータの引き上げの理由
    - クォータを引き上げるリソースの種類:**Log Analytics**、**Application Insights** など
    - 要求されるクォータ制限


### <a name="to-check-the-current-usage-of-new-log-alert-rules"></a>新しいログ アラート ルールの現在の使用状況を確認するには
    
#### <a name="from-the-azure-portal"></a>Azure portal から

1. *[アラート]* 画面を開き、 *[アラート ルールの管理]* をクリックします
2. *[サブスクリプション]* ドロップダウン コントロールを使用して、関連するサブスクリプションをフィルター処理します
3. 特定のリソース グループ、リソースの種類、またはリソースをフィルター処理しないようにしてください
4. *[シグナルの種類]* ドロップダウン コントロールで、[ログ検索] を選択します。
5. *[状態]* ドロップダウン コントロールが [有効] に設定されていることを確認します
6. ログ検索アラート ルールの合計数がルールの一覧の上に表示されます

#### <a name="from-api"></a>API から

- PowerShell - [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule?view=azps-3.7.0)
- REST API - [サブスクリプションごとの一覧](/rest/api/monitor/scheduledqueryrules/listbysubscription)

## <a name="next-steps"></a>次のステップ

- [Azure でのログ アラート](./alerts-unified-log.md)について学習します。
- [Application Insights](../log-query/log-query-overview.md) についてさらに学習します。
- [ログ クエリ](../log-query/log-query-overview.md)についてさらに学習します
