---
title: Log Analytics から Azure Government クラウドにアラートを拡張する
description: この記事では、アラートを Log Analytics から Azure アラートに拡張するためのツールと API について説明します。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 9d734f74c4e12b369e46c15dcb9d01a8185dddd6
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103379"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>アラートを Log Analytics から Azure アラートに拡張する
OMS ポータルのアラート機能は、Azure Government クラウドの Azure アラートに置き換わりつつあります。 この移行の一環として、Log Analytics で最初に構成したアラートは Azure に拡張されます。 アラートが自動的に Azure に移動されるのを待ちたくない場合は、次の方法でプロセスを開始できます。

- Operations Management Suite ポータルから手動で開始する。 
- AlertsVersion API を使ってプログラムによって開始する。  

> [!NOTE]
> Microsoft は 2019 年 3 月 1 日より、体系的な方法で、Log Analytics の Azure Government OMS ポータル インスタンスで作成されたアラートを自動的に Azure アラートに拡張します。 [アクション グループ](../../azure-monitor/platform/action-groups.md)の作成でなんらかの問題がある場合は、[これらの修復手順](alerts-extend-tool.md#troubleshooting)を使用して、アクション グループを自動作成させます。 2019 年 3 月 15 日までは、Azure Government OMS ポータルでこれらの手順を使用できます。

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>オプション 1:Operations Management Suite ポータルから開始する
次の手順では、Azure Government クラウドの Operations Management Suite ポータルからワークスペースのアラートを拡張する方法について説明します。  

1. Azure Portal で **[すべてのサービス]** を選択します。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。
2. Log Analytics サブスクリプション ウィンドウで、ワークスペースを選択して **[OMS ポータル]** タイルを選択します。
![[OMS ポータル] タイルが強調表示されている Log Analytics サブスクリプション ウィンドウのスクリーンショット](media/alerts-extend-tool/azure-portal-01.png) 
3. Operations Management Suite ポータルにリダイレクトされたら、**[設定]** アイコンを選択します。
![[設定] アイコンが強調表示されている Operations Management Suite ポータルのスクリーンショット](media/alerts-extend-tool/oms-portal-settings-option.png) 
4. **[設定]** ページで **[アラート]** を選択します。  
5. **[Azure に拡張]** を選択します。
![[Azure に拡張] が強調表示されている Operations Management Suite ポータルの [アラートの設定] ページのスクリーンショット](media/alerts-extend-tool/ExtendInto.png)
6. **[アラート]** ウィンドウに 3 つのステップで構成されるウィザードが表示されます。 概要を読み、**[次へ]** を選択します。
![ウィザードのステップ 1 のスクリーンショット](media/alerts-extend-tool/ExtendStep1.png)  
7. 2 番目のステップでは、アラートに対して提案される変更の概要が、適切な[アクション グループ](../../azure-monitor/platform/action-groups.md)の一覧により示されます。 複数のアラートに同様のアクションがある場合は、それらのすべてに 1 つのアクション グループを関連付けることが提案されます。  名前付け規則は次のとおりです。*WorkspaceName_AG_#Number* という形式を使用します。 続行するには、**[次へ]** を選択します。
![ウィザードのステップ 2 のスクリーンショット](media/alerts-extend-tool/ExtendStep2.png)  
8. ウィザードの最後のステップで、**[完了]** を選択し、プロセスを開始するよう求められたら確認します。 必要に応じて、メールアドレスを指定できます。これにより、プロセスが完了し、すべてのアラートが適切に Azure アラートに移動された時点で通知を受け取ることができます。
![ウィザードのステップ 3 のスクリーンショット](media/alerts-extend-tool/ExtendStep3.png)

ウィザードが終了すると、**[アラートの設定]** ページから、Azure にアラートを拡張するためのオプションが削除されます。 対象のアラートはバックグラウンドで Azure に移動されますが、これには少し時間がかかることがあります。 処理中、Operations Management Suite ポータルからアラートを変更することはできません。 現在の状態は、ポータル上部のバナーで確認できます。 前にメールアドレスを指定した場合は、プロセスが適切に完了した時点でメールが届きます。  


アラートは、Azure に適切に移動された後も、引き続き Operation Management Suite ポータルに表示されます。
![Operations Management Suite ポータルの [アラートの設定] ページのスクリーンショット](media/alerts-extend-tool/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>選択肢 2: AlertsVersion API を使用する
Log Analytics AlertsVersion API を使用して、REST API を呼び出すことができる任意のクライアントから、アラートを Log Analytics から Azure アラートに拡張できます。 PowerShell から API にアクセスするには、オープンソースのコマンドライン ツール [ARMClient](https://github.com/projectkudu/ARMClient) を使用します。 結果は JSON に出力できます。  

API を使用するには、最初に GET 要求を作成します。 これにより、POST 要求を使用して実際に拡張を試みる前に、評価が実施され、変更提案の概要が返されます。 この結果には、対象のアラートと、提案された[アクション グループ](../../azure-monitor/platform/action-groups.md)の一覧が JSON 形式で表示されます。 複数のアラートに同様のアクションがある場合は、それらのすべてに 1 つのアクション グループを関連付けることが提案されます。 名前付け規則は次のとおりです。*WorkspaceName_AG_#Number* という形式を使用します。

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

GET 要求が成功すると、HTTP 状態コード 200 と、アラートおよび提案されたアクション グループの一覧が JSON データで返されます。 応答の例を次に示します。

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "https://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
指定したワークスペースに警告ルールが定義されていない場合は、JSON データにより次が返されます。

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

指定したワークスペース内のすべての警告ルールが Azure に既に拡張されている場合は、GET 要求に対する応答は次のようになります。

```json
{
    "version": 2
}
```

Azure へのアラートの移行を開始するには、POST 応答を開始します。 この POST 応答によって、アラートを Log Analytics から Azure アラートに拡張することについて、ユーザーの意図と同意が確認されます。 アクティビティのスケジュール設定とアラートの処理は、前に GET 応答を実行したときの結果に基づいて指定の通りに行われます。 必要に応じて、スケジュールされたアラート移行のバックグラウンド プロセスが正常に完了したときに Log Analytics によるレポート送信先となるメールアドレスを指定できます。 次のような要求の例を使用できます。

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Azure アラートへのアラートの移行の結果は、GET 応答で提供された概要によって異なる場合があります。 スケジュールされている場合、Log Analytics のアラートは、一時的に Operation Management Suite ポータルで変更できなくなります。 ただし、新しいアラートを作成することはできます。 

POST 要求が成功すると、HTTP 200 OK 状態と次の応答が返されます。

```json
{
    "version": 2
}
```

この応答は、アラートが Azure アラートに適切に拡張されたことを示します。 version プロパティは、アラートが Azure に拡張されたかどうかを確認するためだけのもので、[Log Analytics Search API](../../azure-monitor/platform/api-alerts.md) とは無関係です。 アラートが Azure に正常に拡張されると、POST 要求で指定したすべてのメール アドレスにレポートが送信されます。 指定したワークスペースのすべてのアラートの拡張が既にスケジュールされている場合、POST 要求に対して "禁止" の応答が返されます (403 状態コード)。 エラー メッセージを表示したり、プロセスが停止しているかどうかを確認したりするために、GET 要求を送信できます。 エラー メッセージがある場合は、そのメッセージが概要情報と共に返されます。

```json
{
    "version": 1,
    "message": "OMS was unable to extend your alerts into Azure, Error: The subscription is not registered to use the namespace 'microsoft.insights'. OMS will schedule extending your alerts, once remediation steps illustrated in the troubleshooting guide are done.",
    "recipients": [
       "john.doe@email.com",
       "jane.doe@email.com"
     ],
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "https://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}              

```

## <a name="troubleshooting"></a>トラブルシューティング 
アラートを拡張するプロセスの途中で問題が発生し、必要な[アクション グループ](../../azure-monitor/platform/action-groups.md)を作成できないことがあります。 このような場合は、エラー メッセージが Operation Management Suite ポータルの **[アラート]** セクションのバナーに表示されます。または、API に対する GET 呼び出しの完了時に表示されます。

> [!IMPORTANT]
> Azure Government クラウド ベースの OMS ポータル ユーザーが 2019 年 3 月 15 日までに次の修復手順を実行しない場合、アラートは Azure で実行されますが、アクションや通知は実行されません。 アラートの通知を受け取るには、Azure でアラート ルールを手動で編集して[アクション グループ](../../azure-monitor/platform/action-groups.md)を追加する必要があります。

各エラーの修復手順を次に示します。
- **エラー: Scope Lock is present at subscription/resource group level for write operations (サブスクリプション/リソース グループ レベルに書き込み操作のスコープ ロックが存在します)**:  ![スコープ ロックのエラー メッセージが強調表示されている Operations Management Suite ポータルの [アラートの設定] ページのスクリーンショット](media/alerts-extend-tool/ErrorScopeLock.png)

    スコープ ロックが有効になっている場合、Log Analytics (Operation Management Suite) ワークスペースを含むサブスクリプションまたはリソース グループ内での新しい変更がすべて制限されます。 システムによる Azure へのアラートの拡張は行われず、必要なアクション グループが作成されません。
    
    解決するには、ワークスペースを含むサブスクリプションまたはリソース グループの *ReadOnly* ロックを削除します。 この操作は Azure portal、PowerShell、Azure CLI、または API から実行できます。 詳細については、[リソース ロックの使用](../../azure-resource-manager/resource-group-lock-resources.md)に関するページをご覧ください。 
    
    この記事で説明されている手順を使用して問題を解決すると、スケジュールされている翌日の実行が行われている間に、Operation Management Suite によって対象のアラートが Azure に拡張されます。 他の操作を行ったり、何かを開始したりする必要はありません。

- **エラー: Policy is present at subscription/resource group level (サブスクリプション/リソース グループ レベルにポリシーが存在します)**:  ![ポリシーのエラー メッセージが強調表示されている Operations Management Suite ポータルの [アラートの設定] ページのスクリーンショット](media/alerts-extend-tool/ErrorPolicy.png)

    [Azure Policy](../../governance/policy/overview.md) が適用されている場合、Log Analytics (Operation Management Suite) ワークスペースを含むサブスクリプションまたはリソース グループの新しいリソースがすべて制限されます。 システムによる Azure へのアラートの拡張は行われず、必要なアクション グループが作成されません。
    
    これを解決するには、ワークスペースを含むサブスクリプションまたはリソース グループでの新しいリソースの作成を妨げている *[RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* エラーの原因であるポリシーを編集します。 この操作は Azure portal、PowerShell、Azure CLI、または API から実行できます。 アクションを監査して、障害の原因になっている適切なポリシーを見つけることができます。 詳細については、[アクションを監査するアクティビティ ログの表示](../../azure-resource-manager/resource-group-audit.md)に関するページをご覧ください。 
    
    この記事で説明されている手順を使用して問題を解決すると、スケジュールされている翌日の実行が行われている間に、Operation Management Suite によって対象のアラートが Azure に拡張されます。 他の操作を行ったり、何かを開始したりする必要はありません。


## <a name="next-steps"></a>次の手順

* 新しい [Azure アラートのエクスペリエンス](../../azure-monitor/platform/alerts-overview.md)の詳細について学習する。
* [Azure Alerts のログ アラート](alerts-unified-log.md)について学習する。

