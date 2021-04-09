---
title: Azure Monitor を使用してログ アラートを作成、表示、管理する | Microsoft Docs
description: Azure Monitor を使用してログ アラート ルールを作成、表示、管理する
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 8759a539005a38892498c9450d31f97884547db7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033709"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Azure Monitor を使用してログ アラートを作成、表示、管理する

## <a name="overview"></a>概要

ログ アラートによって、ユーザーは [Log Analytics](../logs/log-analytics-tutorial.md) クエリを使用して、設定された頻度でリソース ログを評価し、その結果に基づいてアラートを発行することができます。 [アクション グループ](./action-groups.md)を使用することで、ルールによって 1 つ以上のアクションをトリガーできます。 [ログ アラートの機能と用語の詳細について参照してください](./alerts-unified-log.md)。

この記事では、Azure Monitor を使用してログ アラートを作成および管理する方法について説明します。 アラート ルールは、次の 3 つのコンポーネントによって定義されます。
- ターゲット:監視する特定の Azure リソース。
- 条件:評価するロジック。 一致すると、アラートが発生します。  
- アクション:通知または自動化 - 電子メール、SMS、Webhook など。

Azure Resource Manager テンプレートを使用してログ アラート ルールを作成することもできます。これについては、[別の記事](../alerts/alerts-log-create-templates.md)で説明されています。

> [!NOTE]
> [Log Analytics ワークスペース](../logs/log-analytics-tutorial.md)からのログ データを、Azure Monitor メトリック ストアに送信することもできます。 各メトリック アラートの[動作](./alerts-metric-overview.md)は異なります。これは、操作するデータによっては、より望ましい場合があります。 メトリックにルーティングできるログとその方法については、[ログのメトリック アラート](./alerts-metric-logs.md)に関するページを参照してください。

## <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Microsoft Azure portal でログ アラート ルールを作成する

アラートのクエリの記述を開始するための手順は次のとおりです。

1. アラートを作成するリソースにアクセスします。 可能な場合は、サブスクリプションまたはリソース グループの範囲を選択して、複数のリソースにアラート ルールを設定することを検討してください。 複数のリソースに対してアラートを生成すると、コストが削減され、複数のアラート ルールを管理する必要がなくなります。
1. **[モニター]** で、 **[ログ]** を選択します。
1. 問題を示している可能性のあるログ データのクエリを実行します。 検出できることや、[独自のクエリを記述する方法の概要](../logs/log-analytics-tutorial.md)を理解するには、[アラートのクエリ例に関するトピック](../logs/example-queries.md)を使用してください。 また、[最適化されたアラート クエリを作成する方法について確認してください](alerts-log-query.md)。
1. [+ 新しいアラート ルール] ボタンを押して、アラート作成フローを開始します。

    ![Log Analytics - アラートの設定](media/alerts-log/AlertsAnalyticsCreate.png)

> [!NOTE]
> リソース グループまたはサブスクリプション スコープを使用して複数のリソース上で実行されるログのリソース アクセス モードを使用する場合は、大規模なアラートを作成することをお勧めします。 大規模にアラートを行うと、ルール管理のオーバーヘッドを軽減できます。 リソースをターゲットにできるようにするには、リソース ID 列を結果に含めてください。 [ディメンション別にアラートを分割する方法の詳細について参照してください](./alerts-unified-log.md#split-by-alert-dimensions)。

### <a name="log-alert-for-log-analytics-and-application-insights"></a>Log Analytics と Application Insights のログ アラート

1. クエリ構文が正しい場合は、直近の 6 時間から最後の週までさまざまなグラフの期間を微調整するオプションと共に、クエリの履歴データがグラフとして表示されます。
 
    クエリ結果に要約されたデータ、または時間列のない[プロジェクト](/azure/kusto/query/projectoperator)固有の列が含まれている場合、グラフには単一の値が表示されます。

    ![アラート ルールの構成](media/alerts-log/AlertsPreviewAlertLog.png)

1. [ **[期間]**](./alerts-unified-log.md#query-time-range) オプションを使用して、指定された条件を評価する時間の範囲を選びます。

1. ログ アラートは、次の 2 種類の [**測定**](./alerts-unified-log.md#measure)を基に作成できます。
    1. **結果の数** - クエリによって返されるレコードの数。
    1. **メトリック測定** - 選択された式と、[bin()](/azure/kusto/query/binfunction) の選択でグループ化された summarize を使用して計算された "*集計値*"。 次に例を示します。

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

1. メトリック測定のアラート ロジックでは、必要に応じて、 **[集計]** オプションを使用して、アラートを [ディメンション](./alerts-unified-log.md#split-by-alert-dimensions)別に分割する方法を指定できます。 行グループ式は一意かつ並べ替えられている必要があります。

    > [!NOTE]
    > [bin()](/azure/kusto/query/binfunction) では不均一な期間が返される可能性があるため、実行時、アラート サービスでは適切な時間を使用して [bin()](/azure/kusto/query/binfunction) 関数が [bin_at()](/azure/kusto/query/binatfunction) に変換され、固定小数点を持つ結果が得られるようにします。

    > [!NOTE]
    > アラート ディメンションによる分割は、現在の scheduledQueryRules API でのみ使用できます。 従来の [Log Analytics Alert API](./api-alerts.md) を使用する場合は、切り替える必要があります。 [切り替えの詳細について参照してください](./alerts-log-api-switch.md)。 大規模なリソース中心のアラートは、`2020-05-01-preview` 以上の API バージョンでのみサポートされています。

    ![[集計] オプション](media/alerts-log/aggregate-on.png)

1. 次に、プレビュー データに基づいて、[ **[演算子]** 、 **[しきい値]**](./alerts-unified-log.md#threshold-and-operator)、[ **[頻度]**](./alerts-unified-log.md#frequency) を設定します。

1. 必要に応じて、 **[合計] または [連続する違反]** を使用して、[アラートをトリガーする違反の数](./alerts-unified-log.md#number-of-violations-to-trigger-alert)を設定することもできます。

1. **[Done]** を選択します。 

1. **[アラート ルール名]** 、 **[説明]** を定義し、アラートの **[重大度]** を選択します。 これらの詳細は、すべてのアラート アクションで使用されます。 さらに、 **[ルールの作成時に有効にする]** を選択して、作成に関するアラート ルールをアクティブにしないようにできます。

1. アラートが発生した後に、ルールのアクションを非表示にする場合は、[ **[アラートを表示しない]**](./alerts-unified-log.md#state-and-resolving-alerts) オプションを使用します。 ルールは引き続き実行され、アラートも作成されますが、ノイズを防ぐためにアクションはトリガーされません。 有効にするには、ミュート アクションの値がアラートの頻度よりも大きい必要があります。

    ![ログ アラートのアラートを表示しない](media/alerts-log/AlertsPreviewSuppress.png)

1. アラートの条件が満たされた場合に、アラート ルールで 1 つまたは複数の [**アクション グループ**](./action-groups.md#webhook)をトリガーするかどうかを指定します。

    > [!NOTE]
    > 実行できるアクションの制限については、[Azure サブスクリプション サービスの制限](../../azure-resource-manager/management/azure-subscription-service-limits.md)に関するページを参照してください。  

1. ログ アラート ルールのアクションは、必要に応じてカスタマイズすることができます。

    - **電子メールの件名のカスタマイズ**:電子メール アクションの "*電子メールの件名*" をオーバーライドします。 メールの本文は変更できず、このフィールドは **電子メール アドレス用ではありません**。
    - **カスタム JSON ペイロードを含める**: アクション グループに Webhook アクションが含まれていることを前提として、アクション グループによって使用される Webhook JSON をオーバーライドします。 詳細については、[ログ アラートの Webhook アクション](./alerts-log-webhook.md)に関するページを参照してください。

    ![ログ アラートのアクションのオーバーライド](media/alerts-log/AlertsPreviewOverrideLog.png)

1. すべてのフィールドが正しく設定されている場合、 **[アラート ルールの作成]** ボタンをクリックすると、アラートが作成されます。

    数分後にアラートがアクティブになり、前述のようにトリガーされます。

    ![ルールの作成](media/alerts-log/AlertsPreviewCreate.png)

#### <a name="creating-log-alert-for-log-analytics-and-application-insights-from-the-alerts-management"></a>アラートの管理から Log Analytics および Application Insights のログ アラートを作成する

> [!NOTE]
> 現在、アラートの管理からの作成は、リソース中心のログではサポートされていません

1. [ポータル](https://portal.azure.com/)で、 **[モニター]** を選択してから **[アラート]** を選択します。

    ![監視](media/alerts-log/AlertsPreviewMenu.png)

1. **[新しいアラート ルール]** を選択します。 

    ![[アラートの追加]](media/alerts-log/AlertsPreviewOption.png)

1. **[アラートの作成]** ペインが表示されます。 これには、次の 4 つの部分があります。 
    - アラートが適用されるリソース。
    - 確認する条件。
    - 条件が true の場合に実行するアクション。
    - アラートの名前とその説明の詳細。 

    ![ルールを作成する](media/alerts-log/AlertsPreviewAdd.png)

1. **[リソースの選択]** ボタンを選択します。 "*サブスクリプション*"、"*リソースの種類*" を選択し、リソースを選択してフィルターを適用します。 リソースに使用可能なログがあることを確認します。

   ![リソースの選択](media/alerts-log/Alert-SelectResourceLog.png)

1. 次に、 **[条件]** の追加ボタンを使用して、そのリソースで利用できるシグナル オプションの一覧を表示します。 **[カスタム ログ検索]** オプションを選択します。

   ![リソースの選択 - カスタム ログ検索](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > アラート ポータルには Log Analytics と Application Insights から保存されたクエリが一覧表示されており、これらはテンプレート アラート クエリとして使用できます。

1. 選択したら、 **[検索クエリ]** フィールドでアラート クエリを書き込む、貼り付ける、または編集します。

1. [前のセクション](#log-alert-for-log-analytics-and-application-insights)に記載されている次のステップに進みます。

### <a name="log-alert-for-all-other-resource-types"></a>その他の種類のリソースのログ アラート

> [!NOTE]
> 現時点では、API バージョン `2020-05-01-preview` とリソース中心のログ アラートに対して追加料金は発生しません。  プレビュー段階にある機能の価格は、後で発表され、課金が始まる前に通知されます。 通知期間後も新しい API バージョンとリソース中心のログ アラートを引き続き使用することを選択した場合は、該当する料金が適用されます。

1. **[条件]** タブから開始します。

    1. [ **[測定]**](./alerts-unified-log.md#measure)、[ **[集計の種類]**](./alerts-unified-log.md#aggregation-type)、および [ **[集計の粒度]**](./alerts-unified-log.md#aggregation-granularity) が正しいことを確認します。 
        1. 既定のルールでは、過去 5 分間の結果の数がカウントされます。
        1. 集計されたクエリ結果が検出されると、それを取得するため、ルールは数秒以内に自動的に更新されます。

    1. 必要に応じて、[ディメンション別にアラートを分割する](./alerts-unified-log.md#split-by-alert-dimensions)ことを選択します。 
       - **リソース ID 列** が検出されると、自動的に選択され、発生したアラートのコンテキストがレコードのリソースに変更されます。 
       - **リソース ID 列** を選択解除して、サブスクリプションまたはリソース グループでアラートを発生させることができます。 クエリ結果が複数のリソースに基づいている場合は、選択解除すると役立ちます。 たとえば、リソース グループの仮想マシンの 80% で CPU 使用率が高くなっているかどうかを確認するクエリなどが該当します。
       - ディメンション テーブルを使用することで、任意の数値またはテキストの列の型に対して、追加で最大 6 つの分割を選択することもできます。
       - アラートは、一意の組み合わせに基づく分割によって個別に発生し、アラート ペイロードにはこの情報が含まれます。
    
        ![集計パラメーターと分割の選択](media/alerts-log/select-aggregation-parameters-and-splitting.png)

    1. **プレビュー** のグラフには、時間の経過に伴うクエリ評価の結果が表示されています。 グラフの期間を変更したり、ディメンションごとに一意のアラートを分割した結果として生成された別の時系列を選択したりできます。

        ![プレビューのグラフ](media/alerts-log/preview-chart.png)

    1. 次に、プレビュー データに基づいて、 **[アラート ロジック]** 、[ **[演算子]** 、 **[しきい値]**](./alerts-unified-log.md#threshold-and-operator)、[ **[頻度]**](./alerts-unified-log.md#frequency) を設定します。

        ![しきい値とアラート ロジックが含まれるプレビューのグラフ](media/alerts-log/chart-and-alert-logic.png)

    1. 必要に応じて、 **[詳細オプション]** セクションにある [ **[アラートをトリガーする違反の数]**](./alerts-unified-log.md#number-of-violations-to-trigger-alert) を設定することもできます。
    
        ![[詳細オプション]](media/alerts-log/advanced-options.png)

1. **[アクション]** タブで、必要な [[アクション グループ]](./action-groups.md) を選択または作成します。

    ![[アクション] タブ](media/alerts-log/actions-tab.png)

1. **[詳細]** タブで、 **[アラート ルールの詳細]** と **[プロジェクトの詳細]** を定義します。 必要に応じて、 **[今すぐ実行を開始]** しないか、アラート ルールが発生した後の期間に [ **[アクションのミュート]**](./alerts-unified-log.md#state-and-resolving-alerts) を行うかどうかを設定できます。

    > [!NOTE]
    > ログ アラート ルールは現在ステートレスであり、ミュートが定義されていない限り、アラートが作成されるたびにアクションが発生します。

    ![[詳細] タブ](media/alerts-log/details-tab.png)

1. **[タグ]** タブで、アラート ルール リソースに必要なタグを設定します。

    ![[タグ] タブ](media/alerts-log/tags-tab.png)

1. **[確認と作成]** タブで検証が実行され、問題がある場合は通知されます。 ルールの定義を確認して承認します。
1. すべてのフィールドが正しい場合は、 **[作成]** ボタンを選択し、アラート ルールの作成を完了します。 すべてのアラートは、アラートの管理で表示できます。
 
    ![[確認と作成] タブ](media/alerts-log/review-and-create-tab.png)

## <a name="view--manage-log-alerts-in-azure-portal"></a>Microsoft Azure portal でログ アラートを表示および管理する

1. [ポータル](https://portal.azure.com/)で、関連するリソースまたは **[モニター]** サービスを選択します。 次に、[モニター] セクションで **[アラート]** を選択します。

1. アラートの管理には、発生したすべてのアラートが表示されます。 [アラートの管理の詳細について参照してください。](alerts-managing-alert-instances.md)

    > [!NOTE]
    > 現在、ログ アラート ルールは[ステートレスで、解決しません。](./alerts-unified-log.md#state-and-resolving-alerts)

1. ルールを編集するには、上部のバーにある **[アラート ルールの管理]** ボタンを選択します。

    ![ manage alert rules](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-powershell"></a>PowerShell を使用したログ アラートの管理

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> 現在、PowerShell は API バージョン `2020-05-01-preview` ではサポートされていません

下に一覧表示されている PowerShell コマンドレットは、[Scheduled Query Rules API](/rest/api/monitor/scheduledqueryrules/) を利用してルールを管理するために用意されています。

- [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) :新しいログ アラート ルールを作成するための PowerShell コマンドレット。
- [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) :既存のログ アラート ルールを更新するための PowerShell コマンドレット。
- [New-AzScheduledQueryRuleSource](/powershell/module/az.monitor/new-azscheduledqueryrulesource) :ログ アラートのソース パラメーターを指定するオブジェクトを作成または更新するための PowerShell コマンドレット。 [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) および [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) コマンドレットによって入力として使用されます。
- [New-AzScheduledQueryRuleSchedule](/powershell/module/az.monitor/new-azscheduledqueryruleschedule):ログ アラートのスケジュール パラメーターを指定するオブジェクトを作成または更新するための PowerShell コマンドレット。 [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) および [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) コマンドレットによって入力として使用されます。
- [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) :ログ アラートのアクション パラメーターを指定するオブジェクトを作成または更新するための PowerShell コマンドレット。 [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) および [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) コマンドレットによって入力として使用されます。
- [New-AzScheduledQueryRuleAznsActionGroup](/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) :ログ アラートのアクション グループ パラメーターを指定するオブジェクトを作成または更新するための PowerShell コマンドレット。 [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) コマンドレットによって入力として使用されます。
- [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) :ログ アラートのトリガー条件パラメーターを指定するオブジェクトを作成または更新するための PowerShell コマンドレット。 [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) コマンドレットによって入力として使用されます。
- [New-AzScheduledQueryRuleLogMetricTrigger](/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) :[メトリック測定タイプのログ アラート](./alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)のメトリック トリガー条件パラメーターを指定するオブジェクトを作成または更新するための PowerShell コマンドレット。 [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) コマンドレットによって入力として使用されます。
- [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule) :既存のログ アラート ルールまたは特定のログ アラート ルールを一覧表示するための PowerShell コマンドレット
- [Update-AzScheduledQueryRule](/powershell/module/az.monitor/update-azscheduledqueryrule) :ログ アラート ルールを有効または無効にするための PowerShell コマンドレット
- [Remove-AzScheduledQueryRule](/powershell/module/az.monitor/remove-azscheduledqueryrule):既存のログ アラート ルールを削除するための PowerShell コマンドレット

> [!NOTE]
> ScheduledQueryRules PowerShell コマンドレットで管理できるのは、現在の [Scheduled Query Rules API](/rest/api/monitor/scheduledqueryrules/) を使用して作成されたルールのみです。 従来の [Log Analytics Alert API](./api-alerts.md) を使用して作成されたログ アラート ルールは、[Scheduled Query Rules API に切り替えた後にのみ](../alerts/alerts-log-api-switch.md)、PowerShell を使用してのみ管理できます。

PowerShell を使用してログ アラート ルールを作成する手順の例を次に示します。

```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

PowerShell とリソース間のクエリを使用してログ アラート ルールを作成する手順の例を次に示します。

```powershell
$authorized = @ ("/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicewsCrossExample", "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceAppInsights")

$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews" -AuthorizedResource $authorized

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition
New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name" 
```

PowerShell を使用して、[テンプレートとパラメーター](./alerts-log-create-templates.md) ファイルを使用してログ アラートを作成することもできます。

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile mylogalerttemplate.json -TemplateParameterFile mylogalerttemplate.parameters.json
```

## <a name="managing-log-alerts-using-cli"></a>CLI を使用したログ アラートの管理

> [!NOTE]
> Azure CLI サポートは、scheduledQueryRules API バージョン `2020-05-01-preview` 以降でのみ使用できます。 以前の API バージョンでは、次に示すように、テンプレートと共に Azure Resource Manager CLI を使用できます。 従来の [Log Analytics Alert API](./api-alerts.md) を使用する場合は、CLI を使用するよう切り替える必要があります。 [切り替えの詳細について参照してください](./alerts-log-api-switch.md)。

前のセクションでは、Azure portal を使用してログ アラート ルールを作成、表示、および管理する方法について説明しました。 このセクションでは、クロスプラットフォームの [Azure CLI](/cli/azure/get-started-with-azure-cli) を使用して同じ操作を行う方法について説明します。 Azure CLI の使用を開始する最も簡単な方法は、[Azure Cloud Shell](../../cloud-shell/overview.md) を使用することです。 この記事では、Cloud Shell を使用します。

1. Azure portal に移動して、 **[Cloud Shell]** を選択します。

1. プロンプトでは、``--help`` オプションを指定してコマンドを実行することで、コマンドとその使用方法の詳細を確認できます。 たとえば、次のコマンドでは、ログ アラートの作成、表示、管理に使用できるコマンドの一覧が表示されます。

    ```azurecli
    az monitor scheduled-query --help
    ```

1. システム イベント エラーの数を監視するログ アラート ルールを作成できます。

    ```azurecli
    az monitor scheduled-query create -g {ResourceGroup} -n {nameofthealert} --scopes {vm_id} --condition "count \'union Event, Syslog | where TimeGenerated > ago(1h) | where EventLevelName == \"Error\" or SeverityLevel== \"err\"\' > 2" --description {descriptionofthealert}
    ```

1. 次のコマンドを使用して、リソース グループ内のすべてのログ アラートを表示できます。

    ```azurecli
    az monitor scheduled-query list -g {ResourceGroup}
    ```

1. ルールの名前またはリソース ID を使用して、特定のログ アラート ルールの詳細を確認できます。

    ```azurecli
    az monitor scheduled-query show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor scheduled-query show --ids {RuleResourceId}
    ```

1. 次のコマンドを使用して、ログ アラート ルールを無効にできます。

    ```azurecli
    az monitor scheduled-query update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

1. 次のコマンドを使用して、ログ アラート ルールを削除できます。

    ```azurecli
    az monitor scheduled-query delete -g {ResourceGroup} -n {AlertRuleName}
    ```

また、[テンプレート](./alerts-log-create-templates.md) ファイルに対して Azure Resource Manager CLI を使用することもできます。

```azurecli
az login

az deployment group create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file mylogalerttemplate.json \
    --parameters @mylogalerttemplate.parameters.json
```

作成に成功すると、201 が返されます。 更新に成功すると、200 が返されます。

## <a name="next-steps"></a>次の手順

* [ログ アラート](./alerts-unified-log.md)について確認します。
* [Azure Resource Manager テンプレート](./alerts-log-create-templates.md)を使用してログ アラートを作成します。
* [ログ アラートの Webhook アクション](./alerts-log-webhook.md)を理解します。
* [ログ クエリ](../logs/log-query-overview.md)についてさらに学習します
