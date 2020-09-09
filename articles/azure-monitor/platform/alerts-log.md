---
title: Azure Monitor を使用してログ アラートを作成、表示、管理する | Microsoft Docs
description: Azure Monitor を使用して、Azure で、ログ アラート ルールを作成、表示、管理します。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 25604bde3afbbef0d541bc21996b59e98b3090f4
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327499"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Azure Monitor を使用してログ アラートを作成、表示、管理する

## <a name="overview"></a>概要
この記事では、Azure portal 内のアラート インターフェイスを使用して、ログ アラートを作成して管理する方法を示します。 アラート ルールは、次の 3 つのコンポーネントによって定義されます。
- ターゲット:監視する特定の Azure リソース
- 条件:真偽を評価する条件またはロジック。 true の場合、アラートが発生します。  
- アクション:電子メール、SMS、Webhook などの通知の受信者に送信される特定の呼び出し。

**ログ アラート**という用語は、[Log Analytics ワークスペース](../log-query/get-started-portal.md)または [Application Insights](../log-query/log-query-overview.md) のログ クエリが評価されるアラートと、結果が true の場合に発生するアラートを示しています。 「[ログ アラート - 概要](alerts-unified-log.md)」からの機能、用語、および型について説明します。

> [!NOTE]
> [Log Analytics ワークスペース](../log-query/get-started-portal.md)からのログ データを、Azure Monitor メトリック データベースにルーティングすることもできます。 各メトリック アラートの[動作](alerts-metric-overview.md)は異なります。これは、操作するデータによっては、より望ましい場合があります。   メトリックにルーティングできるログとその方法については、[ログのメトリック アラート](alerts-metric-logs.md)に関するページを参照してください。

## <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Microsoft Azure portal でログ アラート ルールを作成する

1. [ポータル](https://portal.azure.com/)で、 **[モニター]** を選択します。 そのセクションでは、 **[アラート]** を選びます。

    ![監視](media/alerts-log/AlertsPreviewMenu.png)

1. **[新しいアラート ルール]** をクリックします。 

    ![[アラートの追加]](media/alerts-log/AlertsPreviewOption.png)

1. **[アラートの作成]** ペインが表示されます。 これには、次の 4 つの部分があります。 
    - アラートが適用されるリソース
    - 確認する条件
    - 条件が true の場合に実行するアクション
    - アラートの名前とその説明の詳細。 

    ![ルールを作成する](media/alerts-log/AlertsPreviewAdd.png)

1. **[リソースの選択]** のリンクを使用してターゲットを指定し、リソースを選択することで、アラートの条件を定義します。 *サブスクリプション*、*リソースの種類* を選択し、必要な *リソース* を選択して、フィルターを適用します。 

   ![リソースの選択](media/alerts-log/Alert-SelectResourceLog.png)

1. 確実に、**リソースの種類**が *Log Analytics* または *Application Insights* のような分析ソースで、シグナルの種類が *[Log]* になるようにします。 **[Done]** をクリックします。 次に、 **[条件の追加]** ボタンを使用して、そのリソースで利用できるシグナル オプションの一覧を表示します。 ログ アラートのデータが置かれている場所に応じて、*Log Analytics* または *Application Insights* に対する **[カスタム ログ検索]** オプションを見つけて選択します。

   ![リソースの選択 - カスタム ログ検索](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > アラート リストは上の図に示すように、分析クエリをシグナルの種類 - **[Log (Saved Query)]\(ログ (保存されたクエリ)\)** としてインポートできます。 そのため、ユーザーは Analytics でクエリを完成させ、後でアラートに使用するために保存することができます。 保存されたクエリの使用について詳しくは、[Azure Monitor でのログ クエリの使用](../log-query/log-query-overview.md)と [Application Insights 分析での共有クエリ](../app/app-insights-overview.md)に関するページをご覧ください。

1. 選択したら、 **[検索クエリ]** フィールドでアラート クエリを作成します。 クエリ構文が正しくない場合、フィールドにエラーが赤色で表示されます。 

1. クエリ構文が正しい場合は、直近の 6 時間から最後の週までさまざまな時間枠を微調整するオプションと共に、クエリの履歴データがグラフとして表示されます。

    ![アラート ルールの構成](media/alerts-log/AlertsPreviewAlertLog.png)

   履歴データの視覚エフェクトは、時間に関する詳細がクエリ結果にある場合にのみ表示されます。 クエリの結果が概要データまたは特定の列の値である場合、画面には単一のプロットが表示されます。
  
   Application Insights または [Log Analytics API](/rest/api/monitor/scheduledqueryrules) を使用するメトリック測定では、以下に示すように、 **[集計]** オプションを使用して、データをグループ化する特定の変数を指定できます。 
  
   ![[集計] オプション](media/alerts-log/aggregate-on.png)



1. 次に、**アラート ロジック**の条件、集計およびしきい値を選びます。 

1. **[期間]** オプションを使用して、指定された条件を評価する期間を選びます。 

1. **[頻度]** でアラートが実行される頻度を選びます。 

    **ログ アラート**は次のものに基づきます。
    - [レコード数](./alerts-unified-log.md#number-of-results-alert-rules): クエリによって返されるレコード数が、指定した値より大きいまたは小さい場合に、アラートが作成されます。
    - [メトリック測定](./alerts-unified-log.md#metric-measurement-alert-rules): 結果内の各*集計値*が指定したしきい値を超えた場合に、アラートが作成されます。アラートは選択された値で*グループ化*されます。 アラートが作成されるための違反数は、選択した期間のしきい値を超えた回数です。 結果セット全体での違反の任意の組み合わせについて違反総数を指定するか、または、違反が連続したサンプルで発生しなければならない必要な連続する違反を指定できます。


1. **[Done]** をクリックします。 

1. **[アラート ルール名]** フィールドでご利用のアラートの名前を定義し、 **[説明]** でそのアラートについて詳しく説明し、提供されるオプションから **[重大度]** の値を選びます。 これらの詳細は、Azure Monitor で行われるすべてのアラート メールの送信、通知、またはプッシュで再利用されます。 さらに、 **[ルールの作成時に有効にする]** をクリックして、作成に関するアラート ルールをすぐにアクティブにするように選択できます。

1. 一定の期間、**アラートを表示しない**ようにするかどうかを選択します。  アラート ルールの抑制を有効にすると、新しいアラートを作成した後、定義された期間の間、ルールのアクションが無効になります。 ルールは引き続き実行され、条件が満たされれた場合はアラート レコードが作成されます。 この設定により、同じアクションを繰り返し実行することなく、問題を修正できます。

   ![ログ アラートのアラートを表示しない](media/alerts-log/AlertsPreviewSuppress.png)

    > [!TIP]
    > アラート抑制の値をアラートの頻度より大きくし、通知がオーバーラップすることなく確実に停止されるようにします

1. 3 番目および最後の手順として、アラートの条件が満たされた場合に、アラート ルールで 1 つまたは複数の**アクション グループ**をトリガーする必要があるかどうかを指定します。 既存のアクション グループを選択することも、新しいものを作成することもできます。 アクション グループを使用すると、電子メールの送信、SMS の送信、Webhook の呼び出し、Azure Runbook を使用した修復、ITSM ツールへのプッシュなど、さまざまなアクションを実行できます。 [アクション グループ](action-groups.md)の詳細をご覧ください。

    > [!NOTE]
    > 実行できるアクションの制限については、[Azure サブスクリプション サービスの制限](../../azure-resource-manager/management/azure-subscription-service-limits.md)に関するページを参照してください。  

    既定のアクションをオーバーライドするために他のいくつかの機能を使用できます。

    - **電子メール通知**:アクション グループを介して送信される "*電子メールの件名*" をオーバーライドします。 メールの本文は変更できず、このフィールドは電子メール アドレス用では**ありません**。
    - **カスタム JSON ペイロードを含める**: アクション グループに Webhook の種類が含まれていることを前提として、アクション グループによって使用される Webhook JSON をオーバーライドします。 Webhook 形式の詳細については、[ログ アラートの Webhook アクション](./alerts-log-webhook.md)に関するページを参照してください。 サンプルの JSON データを使用して形式を確認する [View Webhook] (Webhook の表示) オプションが提供されています。

        ![ログ アラートのアクションのオーバーライド](media/alerts-log/AlertsPreviewOverrideLog.png)


1. すべてのフィールドが有効で緑色のチェックマークがついている場合、 **[アラート ルールの作成]** をクリックすることができ、アラートが Azure Monitor - アラートで作成されます。 アラート ダッシュボードですべてのアラートを表示できます。

     ![ルールの作成](media/alerts-log/AlertsPreviewCreate.png)

     数分後にアラートがアクティブになり、前述のようにトリガーされます。

ユーザーは、[ログ分析](../log-query/log-query-overview.md)で分析クエリを最終処理し、[アラートの設定] ボタンでアラートを作成するようにプッシュし、上記のチュートリアルの手順 6 以降の指示に従います。

 ![Log Analytics - アラートの設定](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Microsoft Azure portal でログ アラートを表示および管理する

1. [ポータル](https://portal.azure.com/)で **[モニター]** を選び、[モニター] セクションで **[アラート]** を選びます。

1. **アラート ダッシュ ボード**が表示されます - ここには、ログ アラート ルールが発動したときのすべてのインスタンスなど、(ログ アラートを含む) すべての Azure アラートが単一のボードに表示されます。 詳細については、「[Alert Management](https://aka.ms/managealertinstances)」を参照してください。
    > [!NOTE]
    > ログ アラート ルールは、ユーザーが提供するクエリベースのカスタム ロジックから構成され、したがって解決済みの状態は使用しません。 このため、ログ アラート ルールで指定された条件に適合するごとに発動されます。

1. 上部のバーにある **[ルールの管理]** を選択して、ルールを管理するセクションに移動します。ここでは、作成されたすべてのアラート ルール (無効にされたアラートも含む) が一覧表示されます。
    ![アラート ルールの管理](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Azure リソース テンプレートを使用したログ アラートの管理

Azure Monitor のログ アラートはリソースの種類 `Microsoft.Insights/scheduledQueryRules/` に関連付けられています。 このリソースの種類の詳細については、[Azure Monitor - Scheduled Query Rules API の API リファレンス](/rest/api/monitor/scheduledqueryrules/)を参照してください。 Application Insights または Log Analytics のログ アラートは、[Scheduled Query Rules API](/rest/api/monitor/scheduledqueryrules/) を使用して作成できます。

> [!NOTE]
> Log Analytics のログ アラートは、従来の [Log Analytics Alert API](api-alerts.md) や [Log Analytics の保存された検索とアラート](../insights/solutions.md)の従来のテンプレートを使用して管理することもできます。 ここで詳しく説明している新しい ScheduledQueryRules API を既定で使用する方法の詳細については、[Log Analytics アラート用の新しい API に切り替える方法](alerts-log-api-switch.md)に関するページを参照してください。


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>Azure リソース テンプレートを使用したログ アラートの作成例

以下に、[スケジュール済みクエリ ルールの作成](/rest/api/monitor/scheduledqueryrules/createorupdate)のための構造体に基づくリソース テンプレートを示します。標準ログ検索クエリである[結果タイプの数によるログ アラート](alerts-unified-log.md#number-of-results-alert-rules)を使用しています。変数として、サンプル データ セットを指定しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "southcentralus",
        "alertName": "samplelogalert",
        "alertDescription": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"requests",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4"
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescription')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "aznsAction":{
                    "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]"
                }
            }
        }
    } ]
}

```

上記のサンプル JSON は、このチュートリアルの目的で (たとえば) sampleScheduledQueryRule.json として保存でき、[Azure portal で Azure Resource Manager](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) を使用してデプロイすることができます。


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>Azure リソース テンプレートを使用したリソース間クエリでのログ アラート

以下に、[スケジュール済みクエリ ルールの作成](/rest/api/monitor/scheduledqueryrules/createorupdate)のための構造体に基づくリソース テンプレートを示します。[メトリック測定タイプのログ アラート](./alerts-unified-log.md#metric-measurement-alert-rules)の[リソース間ログ検索クエリ](../log-query/cross-workspace-query.md)を使用しています。変数として、サンプル データ セットを指定しています。

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "Region Name for your Application Insights App or Log Analytics Workspace",
        "alertName": "sample log alert",
        "alertDescr": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"union workspace(\"servicews\").Update, app('serviceapp').requests | summarize AggregatedValue = count() by bin(TimeGenerated,1h), Classification",
            "Resource1": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Resource2": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceapp",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4",
            "SuppressTimeinMin": 20
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "metricMeasurement": {
            "thresholdOperator": "Equal",
            "threshold": "1",
            "metricTriggerType": "Consecutive",
            "metricColumn": "Classification"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescr')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "authorizedResources": "[concat(array(variables('alertSource').Resource1), array(variables('alertSource').Resource2))]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "throttlingInMin": "[variables('alertActions').SuppressTimeinMin]",
                "aznsAction":{
                    "actionGroup": "[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]",
                    "metricTrigger":{
                        "thresholdOperator": "[variables('metricMeasurement').thresholdOperator]",
                        "threshold": "[variables('metricMeasurement').threshold]",
                        "metricColumn": "[variables('metricMeasurement').metricColumn]",
                        "metricTriggerType": "[variables('metricMeasurement').metricTriggerType]"
                    }
                }
            }
        }
    } ]
}

```

> [!IMPORTANT]
> ログ アラートでリソース間クエリを使用する場合、[authorizedResources](/rest/api/monitor/scheduledqueryrules/createorupdate#source) の使用は必須であり、ユーザーには記載されているリソースの一覧へのアクセス権が必要です。

上記のサンプル JSON は、このチュートリアルの目的で (たとえば) sampleScheduledQueryRule.json として保存でき、[Azure portal で Azure Resource Manager](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) を使用してデプロイすることができます。

## <a name="managing-log-alerts-using-powershell"></a>PowerShell を使用したログ アラートの管理

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor - [スケジュール済みクエリ ルール API](/rest/api/monitor/scheduledqueryrules/) は REST API であり、Azure Resource Manager REST API と完全な互換性があります。 また、[Scheduled Query Rules API](/rest/api/monitor/scheduledqueryrules/) を利用するために、次の PowerShell コマンドレットが用意されています。

- [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) :新しいログ アラート ルールを作成するための PowerShell コマンドレット。
- [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) :既存のログ アラート ルールを更新するための PowerShell コマンドレット。
- [New-AzScheduledQueryRuleSource](/powershell/module/az.monitor/new-azscheduledqueryrulesource) :ログ アラートのソース パラメーターを指定するオブジェクトを作成または更新するための PowerShell コマンドレット。 [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) および [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) コマンドレットによって入力として使用されます。
- [New-AzScheduledQueryRuleSchedule](/powershell/module/az.monitor/new-azscheduledqueryruleschedule):ログ アラートのスケジュール パラメーターを指定するオブジェクトを作成または更新するための PowerShell コマンドレット。 [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) および [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) コマンドレットによって入力として使用されます。
- [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) :ログ アラートのアクション パラメーターを指定するオブジェクトを作成または更新するための PowerShell コマンドレット。 [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) および [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) コマンドレットによって入力として使用されます。
- [New-AzScheduledQueryRuleAznsActionGroup](/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) :ログ アラートのアクション グループ パラメーターを指定するオブジェクトを作成または更新するための PowerShell コマンドレット。 [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) コマンドレットによって入力として使用されます。
- [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) :ログ アラートのトリガー条件パラメーターを指定するオブジェクトを作成または更新するための PowerShell コマンドレット。 [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) コマンドレットによって入力として使用されます。
- [New-AzScheduledQueryRuleLogMetricTrigger](/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) :[メトリック測定タイプのログ アラート](./alerts-unified-log.md#metric-measurement-alert-rules)のメトリック トリガー条件パラメーターを指定するオブジェクトを作成または更新するための PowerShell コマンドレット。 [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) コマンドレットによって入力として使用されます。
- [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule) :既存のログ アラート ルールまたは特定のログ アラート ルールを一覧表示するための PowerShell コマンドレット
- [Update-AzScheduledQueryRule](/powershell/module/az.monitor/update-azscheduledqueryrule) :ログ アラート ルールを有効または無効にするための PowerShell コマンドレット
- [Remove-AzScheduledQueryRule](/powershell/module/az.monitor/remove-azscheduledqueryrule):既存のログ アラート ルールを削除するための PowerShell コマンドレット

> [!NOTE]
> ScheduledQueryRules PowerShell コマンドレットは、コマンドレット自体によって、または Azure Monitor - [Scheduled Query Rules API](/rest/api/monitor/scheduledqueryrules/) を使用して作成されたルールのみを管理できます。 従来の [Log Analytics Alert API](api-alerts.md) と、[Log Analytics 保存済み検索およびアラート](../insights/solutions.md)の従来のテンプレートを使用して作成されたログ アラート ルールは、ユーザーが [Log Analytics アラート用に API 基本設定を切り替えた](alerts-log-api-switch.md)後に限り、ScheduledQueryRules PowerShell コマンドレットを使用して管理できます。

次に示すのは、scheduledQueryRules PowerShell コマンドレットを使用してサンプル ログ アラート ルールを作成する手順です。

```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

## <a name="managing-log-alerts-using-cli-or-api"></a>CLI または API を使用したログ アラートの管理

Azure Monitor - [スケジュール済みクエリ ルール API](/rest/api/monitor/scheduledqueryrules/) は REST API であり、Azure Resource Manager REST API と完全な互換性があります。 そのためこれは、Azure CLI 用の Resource Manager コマンドを使用して、PowerShell を介して使用できます。


> [!NOTE]
> Log Analytics のログ アラートは、従来の [Log Analytics Alert API](api-alerts.md) や [Log Analytics の保存された検索とアラート](../insights/solutions.md)の従来のテンプレートを使用して管理することもできます。 ここで詳しく説明している新しい ScheduledQueryRules API を既定で使用する方法の詳細については、[Log Analytics アラート用の新しい API に切り替える方法](alerts-log-api-switch.md)に関するページを参照してください。

現在、ログ アラートには専用の CLI コマンドはありません。ただし、リソース テンプレートのセクションで前に示したサンプル リソース テンプレート (sampleScheduledQueryRule.json) に対し、Azure Resource Manager CLI コマンドを介して次のように使用できます。

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

操作が成功すると、新しいアラート ルールの作成を示す 201 が返されます。または、既存のアラート ルールが変更された場合は 200 が返されます。

## <a name="next-steps"></a>次のステップ

* [Azure アラートのログ アラート](./alerts-unified-log.md)について学習します。
* [ログ アラートの Webhook アクション](./alerts-log-webhook.md)を理解します。
* [Application Insights](../log-query/log-query-overview.md) についてさらに学習します。
* [ログ クエリ](../log-query/log-query-overview.md)についてさらに学習します

