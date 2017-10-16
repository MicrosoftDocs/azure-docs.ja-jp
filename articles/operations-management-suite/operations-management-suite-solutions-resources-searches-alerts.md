---
title: "OMS ソリューションでの保存された検索条件とアラート | Microsoft Docs"
description: "通常、OMS のソリューションには、ソリューションによって収集されたデータを分析するため、Log Analytics の保存された検索条件が含まれます。  また、重大な問題が発生したときにユーザーに通知するか、自動的に対処するための、アラートも定義できます。  この記事では、管理ソリューションに含めることができるように、Log Analytics の保存された検索条件とアラートを ARM テンプレートで定義する方法について説明します。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 21c42a747a08c5386c65d10190baf0054a7adef8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-oms-management-solution-preview"></a>Log Analytics の保存された検索条件とアラートを OMS 管理ソリューションに追加する (プレビュー)

> [!NOTE]
> 本記事は、現在プレビュー段階である OMS の管理ソリューションの作成手順に関する暫定版ドキュメントです。 本記事で説明するスキーマは、変更されることがあります。   


通常、[OMS の管理ソリューション](operations-management-suite-solutions.md)には、ソリューションによって収集されたデータを分析するため、Log Analytics の[保存された検索条件](../log-analytics/log-analytics-log-searches.md)が含まれます。  また、重大な問題が発生したときにユーザーに通知するか、自動的に対処するための、[アラート](../log-analytics/log-analytics-alerts.md)も定義できます。  この記事では、[管理ソリューション](operations-management-suite-solutions-creating.md)に含めることができるように、Log Analytics の保存された検索条件とアラートを[リソース管理テンプレート](../resource-manager-template-walkthrough.md)で定義する方法について説明します。

> [!NOTE]
> この記事のサンプルでは、管理ソリューションに必須であるかまたは一般的に用いられるパラメーターと変数を使用します。これらについては、「[Operations Management Suite (OMS) での管理ソリューションの作成](operations-management-suite-solutions-creating.md)」で説明しています。  

## <a name="prerequisites"></a>前提条件
この記事では、[管理ソリューションの作成方法](operations-management-suite-solutions-creating.md)および [ARM テンプレート](../resource-group-authoring-templates.md)とソリューション ファイルの構造を理解していることを前提としています。


## <a name="log-analytics-workspace"></a>Log Analytics ワークスペース
Log Analytics のすべてのリソースは、[ワークスペース](../log-analytics/log-analytics-manage-access.md)に含まれています。  「[OMS ワークスペースと Automation アカウント](operations-management-suite-solutions.md#oms-workspace-and-automation-account)」で説明されているように、ワークスペースは管理ソリューションに含まれていませんが、ソリューションのインストール前に追加する必要があります。  このアカウントが含まれていない場合、ソリューションのインストールは失敗します。

ワークスペースの名前は、各 Log Analytics リソースの名前に含まれます。  これは、次の savedsearch リソースの例で示すように **workspace** パラメーターをソリューションで指定することにより行います。

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"


## <a name="saved-searches"></a>保存された検索条件
ソリューションによって収集されたデータをユーザーが照会できるように、[保存された検索条件](../log-analytics/log-analytics-log-searches.md)をソリューションに含めます。  保存された検索条件は、OMS ポータルの **[お気に入り]** と、Azure Portal の **[保存された検索条件]** に表示されます。  保存された検索条件は、各アラートにも必要です。   

[Log Analytics の保存された検索条件](../log-analytics/log-analytics-log-searches.md)リソースは、`Microsoft.OperationalInsights/workspaces/savedSearches` 型であり、次のような構造をしています。  ソリューション ファイルにコード スニペットをコピーして貼り付け、パラメータ名を変更できるように、一般的な変数やパラメータが使用されています。 

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }



次の表では、保存された検索条件の各プロパティについて説明します。 

| プロパティ | Description |
|:--- |:--- |
| カテゴリ | 保存された検索条件のカテゴリです。  同じソリューション内の保存された検索条件は、1 つのカテゴリを共有することが多いため、コンソールではグループ化されています。 |
| displayname | ポータルでの保存された検索条件の表示名です。 |
| query | 実行するクエリ。 |

> [!NOTE]
> JSON として解釈される可能性のある文字が含まれる場合、クエリではエスケープ文字を使うことが必要になる場合があります。  たとえば、**Type:AzureActivity OperationName:"Microsoft.Compute/virtualMachines/write"** というクエリの場合、ソリューション ファイルには **Type:AzureActivity OperationName:\"Microsoft.Compute/virtualMachines/write\"** と書き込まれる必要があります。

## <a name="alerts"></a>アラート
[Log Analytics のアラート](../log-analytics/log-analytics-alerts.md)は、定期的に保存された検索条件を実行するアラート ルールによって作成されます。  クエリの結果が指定されている条件と一致する場合、アラート レコードが作成されて、1 つまたは複数のアクションが実行されます。  

管理ソリューションのアラート ルールは、次の 3 つの異なるリソースで構成されます。

- **保存された検索条件**。  実行されるログ検索を定義します。  複数のアラート ルールで、1 つの保存された検索条件を共有できます。
- **スケジュール**。  ログ検索の実行頻度を定義します。  各アラート ルールには、スケジュールがただ 1 つだけあります。
- **アラート アクション**。  各アラート ルールには **Alert** 型のアクション リソースが 1 つあり、アラート レコードが作成される条件やアラートの重大度などのアラートの詳細が定義されています。  アクション リソースでは、必要に応じて、メールと Runbook の応答が定義されます。
- **webhook アクション (省略可能)**。  アラート ルールが webhook を呼び出すときは、**Webhook** 型の追加アクション リソースが必要です。    

保存された検索条件リソースについては、上で説明してあります。  他のリソースについては以下で説明します。


### <a name="schedule-resource"></a>スケジュール リソース

保存された検索条件は 1 つ以上のスケジュールを持つことができ、各スケジュールは異なるアラート ルールを表します。 スケジュールでは、検索を実行する頻度と、データを取得する期間が定義されています。  スケジュール リソースは `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` 型であり、次のような構造をしています。 ソリューション ファイルにコード スニペットをコピーして貼り付け、パラメータ名を変更できるように、一般的な変数やパラメータが使用されています。 


    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }



次の表では、スケジュール リソースのプロパティについて説明します。

| 要素名 | 必須 | Description |
|:--|:--|:--|
| 有効       | はい | 作成時点でアラートが有効かどうかを指定します。 |
| interval      | はい | クエリを実行する間隔です (分単位)。 |
| queryTimeSpan | はい | 結果を評価する期間です (分単位)。 |

スケジュール リソースは保存された検索条件に依存するので、スケジュールの前に作成する必要があります。


### <a name="actions"></a>アクション
**Type** プロパティによって指定される 2 種類のアクション リソースがあります。  スケジュールには、アラート ルールの詳細と、アラート作成時に実行するアクションが定義されている、1 つの **Alert** アクションが必要です。  アラートから webhook を呼び出す必要がある場合は、**Webhook** アクションを含むこともできます。  

アクション リソースは `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions` 型です。  

#### <a name="alert-actions"></a>アラート アクション

すべてのスケジュールが 1 つの**アラート** アクションを持ちます。  アラート アクションでは、アラートの詳細と、必要に応じて通知と修復のアクションが定義されています。  通知は、1 つ以上のアドレスにメールを送信します。  修復は、Azure Automation で Runbook を開始し、検出された問題の修復を試みます。

アラート アクションの構造は次のとおりです。  ソリューション ファイルにコード スニペットをコピーして貼り付け、パラメータ名を変更できるように、一般的な変数やパラメータが使用されています。 



    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
        ],
        "properties": {
            "etag": "*",
            "type": "Alert",
            "name": "[variables('Alert').Name]",
            "description": "[variables('Alert').Description]",
            "severity": "[variables('Alert').Severity]",
            "threshold": {
                "operator": "[variables('Alert').Threshold.Operator]",
                "value": "[variables('Alert').Threshold.Value]",
                "metricsTrigger": {
                    "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                    "operator": "[variables('Alert').Trigger.Operator]",
                    "value": "[variables('Alert').Trigger.Value]"
                },
            },
            "emailNotification": {
                "recipients": [
                    "[variables('Alert').Recipients]"
                ],
                "subject": "[variables('Alert').Subject]"
            },
            "remediation": {
                "runbookName": "[variables('Alert').Remedition.RunbookName]",
                "webhookUri": "[variables('Alert').Remedition.WebhookUri]"
            }
        }
    }

次の表では、アラート アクション リソースのプロパティについて説明します。

| 要素名 | 必須 | Description |
|:--|:--|:--|
| 種類 | はい | アクションの種類。  これは、アラート アクションの**アラート**です。 |
| 名前 | はい | アラートの表示名。  これは、コンソールに表示されるアラート ルールの名前です。 |
| Description | なし | アラートに関する省略可能な説明です。 |
| Severity | はい | アラート レコードの重大度であり、次のいずれかの値です。<br><br> **Critical**<br>**Warning**<br>**Informational** |


##### <a name="threshold"></a>しきい値
このセクションは必須です。  アラートのしきい値のプロパティを定義します。

| 要素名 | 必須 | Description |
|:--|:--|:--|
| 演算子 | はい | 比較のための演算子であり、次のいずれかの値です。<br><br>**gt = より大きい<br>lt = より小さい** |
| 値 | はい | 結果を比較する値です。 |


##### <a name="metricstrigger"></a>MetricsTrigger
このセクションは省略可能です。  メトリック測定アラートの場合に指定します。

> [!NOTE]
> メトリック測定アラートは現在パブリック プレビュー中です。 

| 要素名 | 必須 | Description |
|:--|:--|:--|
| TriggerCondition | はい | しきい値が、違反の合計数に対するものか、または連続する違反の数に対するものかを、次の値で指定します。<br><br>**Total<br>Consecutive** |
| 演算子 | はい | 比較のための演算子であり、次のいずれかの値です。<br><br>**gt = より大きい<br>lt = より小さい** |
| 値 | はい | アラートをトリガーするために必要な、条件が満たされた回数です。 |

##### <a name="throttling"></a>調整
このセクションは省略可能です。  同じルールからのアラートを、アラート作成後の一定期間にわたって抑制する場合に、このセクションを指定します。

| 要素名 | 必須 | Description |
|:--|:--|:--|
| DurationInMinutes | Throttling 要素が含まれる場合は Yes です。 | アラートが作成された後、それと同じアラート ルールからにアラートを抑制する分数です。 |

##### <a name="emailnotification"></a>EmailNotification
 このセクションは省略可能です。アラートで 1 人以上の受信者にメールを送信する場合に指定します。

| 要素名 | 必須 | Description |
|:--|:--|:--|
| Recipients | はい | アラートが作成されたときに通知を送信するメール アドレスのコンマ区切りのリストです。次に例を示します。<br><br>**[ "recipient1@contoso.com", "recipient2@contoso.com" ]** |
| [件名] | はい | メールの件名です。 |
| 添付ファイル | なし | 添付ファイルは現在はサポートされていません。  この要素を指定する場合は、**None** にする必要があります。 |


##### <a name="remediation"></a>修復
このセクションは省略可能です。アラートに対して Runbook を開始する場合に指定します。 |

| 要素名 | 必須 | 説明 |
|:--|:--|:--|
| RunbookName | はい | 開始する Runbook の名前です。 |
| WebhookUri | はい | Runbook に対する webhook の URI です。 |
| Expiry | いいえ | 修復が期限切れになる日付と時刻です。 |

#### <a name="webhook-actions"></a>Webhook アクション

Webhook アクションは、URL を呼び出し、送信されるペイロードをオプションで指定することにより、プロセスを開始します。 これは修復アクションに似ていますが、Azure Automation の Runbook 以外のプロセスを呼び出す可能性のある Webhook に対して使用することを意図しています。 また、リモート プロセスに配信されるペイロードを指定する追加のオプションも用意されています。

アラートが webhook を呼び出す場合は、**アラート** アクション リソースに加えて **Webhook** 型のアクション リソースが必要です。  

    {
      "name": "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Webhook').Name)]",
      "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions/",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
      ],
      "properties": {
        "etag": "*",
        "type": "[variables('Alert').Webhook.Type]",
        "name": "[variables('Alert').Webhook.Name]",
        "webhookUri": "[variables('Alert').Webhook.webhookUri]",
        "customPayload": "[variables('Alert').Webhook.CustomPayLoad]"
      }
    }

次の表では、Webhook アクション リソースのプロパティについて説明します。

| 要素名 | 必須 | 説明 |
|:--|:--|:--|
| type | はい | アクションの種類。  これは、webhook アクションの **Webhook** です。 |
| name | はい | アクションの表示名です。  コンソールには表示されません。 |
| wehookUri | はい | webhook の URI です。 |
| customPayload | いいえ | Webhook に送信するカスタム ペイロード。 形式は、Webhook で想定される内容によって異なります。 |




## <a name="sample"></a>サンプル

以下のリソースを含むソリューションのサンプルを次に示します。

- 保存された検索条件
- スケジュール
- アラート アクション
- webhook アクション

このサンプルでは、リソースの定義に値をハードコーディングするのではなく、ソリューションでよく使われる[標準ソリューション パラメーター](operations-management-suite-solutions-solution-file.md#parameters)の変数を使っています。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0",
        "parameters": {
          "workspaceName": {
            "type": "string",
            "metadata": {
              "Description": "Name of Log Analytics workspace"
            }
          },
          "accountName": {
            "type": "string",
            "metadata": {
              "Description": "Name of Automation account"
            }
          },
          "workspaceregionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Log Analytics workspace"
            }
          },
          "regionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Automation account"
            }
          },
          "pricingTier": {
            "type": "string",
            "metadata": {
              "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
          },
          "recipients": {
            "type": "string",
            "metadata": {
              "Description": "List of recipients for the email alert separated by semicolon"
            }
          }
        },
        "variables": {
          "SolutionName": "MySolution",
          "SolutionVersion": "1.0",
          "SolutionPublisher": "Contoso",
          "ProductName": "SampleSolution",
    
          "LogAnalyticsApiVersion": "2015-11-01-preview",
    
          "MySearch": {
            "displayName": "Error records by hour",
            "query": "Type=MyRecord_CL | measure avg(Rating_d) by Instance_s interval 60minutes",
            "category": "Samples",
            "name": "Samples-Count of data"
          },
          "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert.  Fires when 3 error records found over hour interval.",
            "Severity": "Critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
              "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
              "Interval": 15,
              "TimeSpan": 60
            },
            "MetricsTrigger": {
              "TriggerCondition": "Consecutive",
              "Operator": "gt",
              "Value": 3
            },
            "ThrottleMinutes": 60,
            "Notification": {
              "Recipients": [
                "[parameters('recipients')]"
              ],
              "Subject": "Sample alert"
            },
            "Remediation": {
              "RunbookName": "MyRemediationRunbook",
              "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=TluBFH3GpX4IEAnFoImoAWLTULkjD%2bTS0yscyrr7ogw%3d"
            },
            "Webhook": {
              "Name": "MyWebhook",
              "Uri": "https://MyService.com/webhook",
              "Payload": "{\"field1\":\"value1\",\"field2\":\"value2\"}"
            }
          }
        },
        "resources": [
          {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Webhook.Name)]"
            ],
            "properties": {
              "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
              "referencedResources": [
              ],
              "containedResources": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Webhook.Name)]"
              ]
            },
            "plan": {
              "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
              "Version": "[variables('SolutionVersion')]",
              "product": "[variables('ProductName')]",
              "publisher": "[variables('SolutionPublisher')]",
              "promotionCode": ""
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
              "etag": "*",
              "query": "[variables('MySearch').query]",
              "displayName": "[variables('MySearch').displayName]",
              "category": "[variables('MySearch').category]"
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
              "etag": "*",
              "interval": "[variables('MyAlert').Schedule.Interval]",
              "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
              "enabled": true
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/',  variables('MyAlert').Schedule.Name, '/',  variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/',  variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
              "etag": "*",
              "Type": "Alert",
              "Name": "[variables('MyAlert').DisplayName]",
              "Description": "[variables('MyAlert').Description]",
              "Severity": "[variables('MyAlert').Severity]",
              "Threshold": {
                "Operator": "[variables('MyAlert').ThresholdOperator]",
                "Value": "[variables('MyAlert').ThresholdValue]",
                "MetricsTrigger": {
                  "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                  "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                  "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                }
              },
              "Throttling": {
                "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
              },
              "EmailNotification": {
                "Recipients": "[variables('MyAlert').Notification.Recipients]",
                "Subject": "[variables('MyAlert').Notification.Subject]",
                "Attachment": "None"
              },
              "Remediation": {
                "RunbookName": "[variables('MyAlert').Remediation.RunbookName]",
                "WebhookUri": "[variables('MyAlert').Remediation.WebhookUri]"
              }
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Webhook.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]",
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name, '/actions/',variables('MyAlert').Name)]"
            ],
            "properties": {
              "etag": "*",
              "Type": "Webhook",
              "Name": "[variables('MyAlert').Webhook.Name]",
              "WebhookUri": "[variables('MyAlert').Webhook.Uri]",
              "CustomPayload": "[variables('MyAlert').Webhook.Payload]"
            }
          }
        ]
    }


次のパラメーター ファイルで、このソリューションのサンプル値を提供します。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspacename": {
                "value": "myWorkspace"
            },
            "accountName": {
                "value": "myAccount"
            },
            "workspaceregionId": {
                "value": "East US"
            },
            "regionId": {
                "value": "East US 2"
            },
            "pricingTier": {
                "value": "Free"
            },
            "recipients": {
                "value": "recipient1@contoso.com;recipient2@contoso.com"
            }
        }
    }


## <a name="next-steps"></a>次のステップ
* 管理ソリューションに[ビューを追加する](operations-management-suite-solutions-resources-views.md)。
* 管理ソリューションに [Automation Runbook とその他のリソースを追加する](operations-management-suite-solutions-resources-automation.md)。

