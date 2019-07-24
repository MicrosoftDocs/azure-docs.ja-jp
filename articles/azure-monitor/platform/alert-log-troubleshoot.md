---
title: Azure Monitor におけるログ アラートのトラブルシューティング | Microsoft Docs
description: Azure におけるログ アラート ルールに関する一般的な問題、エラー、解決方法。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 0c7189f1d43a114532b30b0c1aabe6f7cd4402d8
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578715"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Azure Monitor におけるログ アラートのトラブルシューティング  

## <a name="overview"></a>概要

この記事では、Azure Monitor 内でログ アラートを設定しているときに遭遇する一般的な問題の解決方法について説明します。 また、ログ アラートの機能や構成に関してよく寄せられる質問の解決策を紹介しています。 

**ログ アラート**という用語は、[Log Analytics ワークスペース](../learn/tutorial-viewdata.md)または [Application Insights](../../azure-monitor/app/analytics.md) において、ログ クエリに基づいて作動するアラートを指します。 機能、用語、および種類については、「[ログ アラート - 概要](../platform/alerts-unified-log.md)」をご覧ください。

> [!NOTE]
> この記事では、トリガーされたアラート ルールが Azure portal に表示されるケースや、関連するアクション グループによって実行された通知が Azure portal に表示されるケースについては考慮していません。 そのようなケースについて詳しくは、[アクション グループ](../platform/action-groups.md)に関する記事を参照してください。

## <a name="log-alert-didnt-fire"></a>ログ アラートが作動しない

ここでは、構成された [Azure Monitor のログ アラート ルール](../platform/alerts-log.md)の状態が、[予期された場合に "*起動済み*"](../platform/alerts-managing-alert-states.md) にならない共通の理由をいくつか示します。 

### <a name="data-ingestion-time-for-logs"></a>ログのデータ インジェストの時間

ログ アラートは、[Log Analytics](../learn/tutorial-viewdata.md) または [Application Insights](../../azure-monitor/app/analytics.md) に基づいてクエリを定期的に実行します。 Azure Monitor は、世界中の多様なソースから数千のお客様によって送られた何テラバイトものデータを処理するため、さまざまな時間的遅延の影響を受けやすいサービスとなっています。 詳細については、「[Azure Monitor でのログ データ インジェスト時間](../platform/data-ingestion-time.md)」を参照してください

システムは、データ インジェストの遅延を軽減するため、必要なデータが取り込まれていないことがわかると、待機してアラート クエリを何度も再試行します。 システムによって、指数関数的に増加する待機時間が設定されます。 ログ アラートはデータが使用可能にならないとトリガーされません。したがって、遅延の原因はログ データ インジェストが遅いことによります。 

### <a name="incorrect-time-period-configured"></a>構成されている期間が正しくない

[ログ アラートの用語](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)に関する記事で説明されているように、クエリの時間範囲は、構成に記述された期間によって指定されます。 クエリから返されるのは、この時間範囲内に作成されたレコードだけです。 期間では、ログ クエリ用にフェッチされるデータを制限して不正使用を防いだり、ログ クエリで使用されている時間コマンド (*ago* など) を回避することができます。 たとえば、期間が 60 分に設定されていて、クエリが午後 1 時 15 分に実行された場合は、午後 12 時 15 分から午後 1 時 15 分までの間に作成されたレコードだけが、ログ クエリで使用されます。 ログ クエリが *ago (1d)* のような時間コマンドを使用する場合でも、クエリは午後 12 時 15 分から午後 1 時 15 分までのデータしか使用しません。期間がその間隔に設定されているためです。*

したがって、構成内の期間がクエリと一致しているかを確認してください。 前出の例で、緑色のマーカーで示したようにログ クエリに *ago (1d)* を使用した場合、クエリが意図どおりに実行されるようにするには、期間が 24 時間または 1,440 分 (赤色で示した箇所) に設定されている必要があります。

![期間](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>[アラートを表示しない] オプションが設定されている

[Azure portal でのログ アラート ルールの作成](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)に関する記事の手順 8 で説明しているように、ログ アラートでは、構成された期間にトリガーや通知アクションを抑止するために **[アラートを表示しない]** オプションが提供されます。 この結果、アラートが実際に発生して抑止されたときに、アラートが発生しなかったと受け取る場合があります。  

![アラートを表示しない](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>メトリック測定のアラート ルールが正しくない

**メトリック測定ログ アラート**は、ログ アラートのサブタイプです。特別な機能を備え、アラートのクエリ構文が制限されています。 メトリック測定のログ アラート ルールでは、クエリ出力がメトリック時系列、すなわち同じ長さの個々の期間とそれに対応する集計値を含むテーブルであることが必要です。 また、ユーザーは、そのテーブルに AggregatedValue と一緒に追加の変数を含めることができます。 これらの変数はテーブルを並べ替えるために使用できます。 

たとえば、メトリック測定のログ アラート ルールが次のように構成されていたとします。

- クエリ: `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 期間: 6 時間
- しきい値: 50
- アラート ロジック: 連続 3 回の違反
- 集計基準: $table を選択

コマンドには *summarize ... by* が含まれ、2 つの変数 (timestamp & $table) が指定されているため、システムは*集計基準*として $table を選択します。 基本的には結果テーブルが *$table* フィールドで並べ替えられます。その後、テーブルの種類 (availabilityResults など) ごとに複数の AggregatedValue に注目し、連続 3 回以上の違反が存在するかどうかを確認します。

![複数の値を使用したメトリック測定クエリの実行](media/alert-log-troubleshoot/LogMMQuery.png)

*集計基準*は *$table* で定義され、データが $table 列で並べ替えられた (赤色で示した箇所) とき、*集計基準*フィールド ($table) の種類をグループ化して探します。たとえば、availabilityResults の値は、1 つのプロットまたはエンティティとして見なされます (オレンジ色で強調表示された箇所)。 アラート サービスは、この値プロットまたはエンティティから、テーブル値 "availabilityResults" に対してアラートがトリガーされる連続 3 回の違反 (緑色で示した箇所) がないかを確認します。 同様に、$table のその他すべての値に関して、連続 3 回の違反が見つかった場合、同じものに対して別のアラート通知がトリガーされます。アラート サービスは、1 つのプロット/エンティティに含まれる各値 (オレンジ色で示した箇所) を時刻別に自動的に並べ替えます。

ここで、メトリック測定ログ アラート ルールが変更され、クエリが `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` になったとしましょう。残りの構成は、連続 3 回の違反に対するアラート ロジックも含め、前回と同じままになります。 この場合の "集計基準" オプションは、既定で timestamp になります。 *summarize ... by* のクエリに指定された値が 1 つ (*timestamp*) しかないため、前述の例と同様、実行終了時点の出力は以下のようになります。

   ![単一の値を使用したメトリック測定クエリの実行](media/alert-log-troubleshoot/LogMMtimestamp.png)

*集計基準*は timestamp で定義され、データが *timestamp* 列で並べ替えられた (赤色で示した箇所) とき、timestamp でグループ化します。たとえば、`2018-10-17T06:00:00Z` の値は、1 つのプロットまたはエンティティとして見なされます (オレンジ色で強調表示された箇所)。 この値プロットまたはエンティティでは、連続する違反がアラート サービスによって検出されることはなく (それぞれのタイムスタンプ値にはエントリが 1 つしかないため)、アラートは決してトリガーされることはありません。 このような場合、ユーザーは次のいずれかを実行する必要があります。

- ダミー変数または ($table のような) 既存の変数を追加して、構成した "集計基準" フィールドを使用して並べ替えが正しく行われるようにします。
- (または) 適宜 "*違反の合計数*" に基づくアラート ロジックを使用するようアラート ルールを再構成します。

## <a name="log-alert-fired-unnecessarily"></a>ログ アラートが不必要に作動する

構成されている [Azure Monitor のログ アラート ルール](../platform/alerts-log.md)が、[Azure アラート](../platform/alerts-managing-alert-states.md)で確認すると、発生すべきでないタイミングでトリガーされていることがあります。以降、その一般的な理由について詳しく取り上げます。

### <a name="alert-triggered-by-partial-data"></a>一部のデータによってアラートがトリガーされる

Log Analytics と Application Insights を支えている Analytics は、インジェストの遅延や処理の影響を受けます。このため、指定されたログ アラート クエリが実行されるタイミングで、利用できるデータがまったく存在しない場合や一部のデータしか利用できない場合があります。 詳細については、「[Azure Monitor でのログ データ インジェスト時間](../platform/data-ingestion-time.md)」を参照してください

アラート ルールの構成方法によっては、アラートの実行時点でログのデータが存在しない場合や不十分な場合に誤作動が生じる可能性があります。 そのような場合は、アラート クエリまたは構成を変更することをお勧めします。 

たとえば、分析クエリから返される結果の数が 5 件未満のときにトリガーするようログ アラート ルールが構成されている場合、データが存在しないとき (0 レコード) または一部の結果がある (1 レコード) ときに、アラートがトリガーされます。 ただし、データ インジェストの遅延後に、完全なデータを使用した同じクエリによって、10 個のレコードについての結果が提供される可能性があります。

### <a name="alert-query-output-misunderstood"></a>アラート クエリの出力が誤って解釈される

分析クエリでログ アラートのロジックを提供するのはユーザーです。 分析クエリでは、さまざまなビッグ データや数学関数を使用できます。  アラート サービスは、指定した期間のデータを使用して、指定した間隔でクエリを実行します。 アラート サービスでは、選択したアラートの種類に基づいて、指定したクエリに微妙な変更が加えられます。 この変更は、次に示すように、*[シグナル ロジックの構成]* 画面の "実行するクエリ" セクションで確認できます。![実行するクエリ](media/alert-log-troubleshoot/LogAlertPreview.png)

**[実行するクエリ]** セクションには、ログ アラート サービスによって実行される内容が表示されます。 アラートを実際に作成する前にアラート クエリの出力がどのようになるかを把握したい場合、ユーザーは、ここに表示されているクエリ (期間を含む) を [Analytics ポータル](../log-query/portals.md)または [Analytics API](https://docs.microsoft.com/rest/api/loganalytics/) で実行することができます。

## <a name="log-alert-was-disabled"></a>ログ アラートが無効になった

[Azure Monitor のログ アラート ルール](../platform/alerts-log.md)が Azure Monitor によって無効にされる可能性があるいくつかの理由を以下に示します。

### <a name="resource-on-which-alert-was-created-no-longer-exists"></a>アラートが作成されたリソースが既に存在しない

Azure Monitor で作成されるログ アラート ルールは、Azure Log Analytics ワークスペース、Azure Application Insights アプリ、Azure リソースなどの特定のリソースをターゲットとします。 そして、ログ アラート サービスは、指定されたターゲットに対してルールで指定されている分析クエリを実行します。 しかしユーザーは、ルールの作成後、アラート ルールのターゲットを Azure から削除したり Azure 内に移動したりすることがよくあります。 ログ アラート ルールのターゲットが無効になると、ルールの実行は失敗します。

そのような場合、ルール自体がかなりの期間 (1 週間など) 継続して実行できないときは、Azure Monitor はログ アラートを無効にし、お客様が不必要に課金されないようにします。 ユーザーは、Azure Monitor によってログ アラート ルールが無効にされた正確な時刻を [Azure アクティビティ ログ](../../azure-resource-manager/resource-group-audit.md)を介して知ることができます。 Azure アクティビティ ログで、Azure によってログ アラート ルールが無効になると、イベントが Azure アクティビティ ログに追加されます。

継続的な失敗によりアラート ルールが無効になっている Azure アクティビティ ログのサンプル イベントを以下に示します。

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

### <a name="query-used-in-log-alert-is-not-valid"></a>ログ アラートで使用されているクエリが無効になっている

構成の一部として Azure Monitor で作成された各ログ アラート ルールでは、アラート サービスによって定期的に実行される分析クエリを指定する必要があります。 分析クエリは、ルールの作成時または更新時には正しい構文が指定されている可能性があります。 時間の経過とともに、ログ アラート ルールで指定されたクエリによって構文上の問題が発生し、ルールの実行が失敗し始めることがあります。 ログ アラート ルールで指定される分析クエリでエラーが発生する可能性がある一般的な理由のいくつかを以下に示します。

- クエリが、指定された 1 つ以上のリソースおよび[複数のリソースにまたがって実行される](../log-query/cross-workspace-query.md)ように記述されているが、既に存在しない。
- 指定されたクエリのデータがないことが原因で[クエリの実行でエラーが発生する](https://dev.loganalytics.io/documentation/Using-the-API/Errors)ため、分析プラットフォームへのデータ フローがない。
- [クエリ言語](https://docs.microsoft.com/azure/kusto/query/)に変更を行ったことで、コマンドと関数の形式が変更されている。 そのため、アラート ルールで以前に指定されたクエリが無効になっている。

ユーザーは、最初に [Azure Advisor](../../advisor/advisor-overview.md) を介してこの動作について警告されます。 Azure Advisor で、特定のログ アラート ルールについて、影響が中程度の、"確実に監視するためログ アラート ルールを修復します" という説明が含まれた推奨事項が高可用性のカテゴリの下に追加されます。 Azure Advisor で推奨事項を 7 日間提供しても、指定されたログ アラート ルールのアラート クエリが修正されない場合があります。 ルール自体がかなりの期間 (1 週間など) 継続して実行できないときは、Azure Monitor はログ アラートを無効にし、お客様が不必要に課金されないようにします。

ユーザーは、Azure Monitor によってログ アラート ルールが無効にされた正確な時刻を [Azure アクティビティ ログ](../../azure-resource-manager/resource-group-audit.md)を介して知ることができます。 Azure アクティビティ ログで、Azure によってログ アラート ルールが無効になると、イベントが Azure アクティビティ ログに追加されます。

## <a name="next-steps"></a>次の手順

- [Azure アラートのログ アラート](../platform/alerts-unified-log.md)について学習します。
- [Application Insights](../../azure-monitor/app/analytics.md) についてさらに学習します。
- [ログ クエリ](../log-query/log-query-overview.md)についてさらに学習します
