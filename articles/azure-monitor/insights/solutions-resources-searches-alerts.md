---
title: 管理ソリューションでの保存された検索条件 | Microsoft Docs
description: 通常、管理ソリューションには、ソリューションによって収集されたデータを分析するため、Log Analytics の保存された検索条件が含まれます。 また、重大な問題が発生したときにユーザーに通知するか、自動的に対処するための、アラートも定義できます。 この記事では、管理ソリューションに含めることができるように、Log Analytics の保存された検索条件を Resource Manager テンプレートで定義する方法について説明します。
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0975b23a8f96da6fc2dfcc8bd9ad046847a68aa9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104832"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Log Analytics の保存された検索条件とアラートを管理ソリューションに追加する (プレビュー)

> [!IMPORTANT]
> [Log Analytics のアラートが Azure Monitor に拡張された](../platform/alerts-extend.md)ため、ここに記載されていた、Resource Manager テンプレートを使用したアラートの作成の詳細は古くなりました。 Resource Manager テンプレートを使用したログ アラートの作成の詳細については、「[Azure リソース テンプレートを使用したログ アラートの管理](../platform/alerts-log.md#managing-log-alerts-using-azure-resource-template)」を参照してください。

> [!NOTE]
> 本記事は、現在プレビュー段階である管理ソリューションの作成手順に関する暫定版ドキュメントです。 本記事で説明するスキーマは、変更されることがあります。

通常、[管理ソリューション](solutions.md)には、ソリューションによって収集されたデータを分析するため、Log Analytics の[保存された検索条件](../../azure-monitor/log-query/log-query-overview.md)が含まれます。 また、重大な問題が発生したときにユーザーに通知するか、自動的に対処するための、[アラート](../../azure-monitor/platform/alerts-overview.md)も定義できます。 この記事では、[管理ソリューション](solutions-creating.md)に含めることができるように、Log Analytics の保存された検索条件とアラートを[リソース管理テンプレート](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)で定義する方法について説明します。

> [!NOTE]
> この記事のサンプルでは、「[Azure での管理ソリューションの設計とビルド](solutions-creating.md)」で説明されている、管理ソリューションにとって必須または一般的なパラメーターと変数を使用します。

## <a name="prerequisites"></a>前提条件
この記事では、[管理ソリューションの作成方法](solutions-creating.md)および [Resource Manager テンプレート](../../azure-resource-manager/resource-group-authoring-templates.md)とソリューション ファイルの構造を理解していることを前提としています。


## <a name="log-analytics-workspace"></a>Log Analytics ワークスペース
Log Analytics のすべてのリソースは、[ワークスペース](../../azure-monitor/platform/manage-access.md)に含まれています。 「[Log Analytics ワークスペースと Automation アカウント](solutions.md#log-analytics-workspace-and-automation-account)」で説明されているように、ワークスペースは管理ソリューションに含まれていませんが、ソリューションのインストール前に追加する必要があります。 このアカウントが含まれていない場合、ソリューションのインストールは失敗します。

ワークスペースの名前は、各 Log Analytics リソースの名前に含まれます。 これは、次の SavedSearch リソースの例で示すように **workspace** パラメーターをソリューションで指定することにより行います。

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Log Analytics API バージョン
Resource Manager テンプレートで定義された Log Analytics リソースはすべて、そのリソースで使用する API のバージョンを定義するプロパティ **apiVersion** を保持しています。

次の表は、この例で使用されているリソースの API バージョンの一覧です。

| リソースの種類 | API バージョン | Query |
|:---|:---|:---|
| savedSearches | 2017-03-15-preview | Event &#124; where EventLevelName == "Error"  |


## <a name="saved-searches"></a>保存された検索条件
ソリューションによって収集されたデータをユーザーが照会できるように、[保存された検索条件](../../azure-monitor/log-query/log-query-overview.md)をソリューションに含めます。 保存された検索条件は、Azure portal の **[保存された検索条件]** に表示されます。 保存された検索条件は、各アラートにも必要です。

[Log Analytics の保存された検索条件](../../azure-monitor/log-query/log-query-overview.md)リソースは、`Microsoft.OperationalInsights/workspaces/savedSearches` 型であり、次のような構造をしています。 ソリューション ファイルにコード スニペットをコピーして貼り付け、パラメータ名を変更できるように、一般的な変数やパラメータが使用されています。

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

次の表は、保存された検索条件の各プロパティについて説明しています。

| プロパティ | Description |
|:--- |:--- |
| category | 保存された検索条件のカテゴリです。  同じソリューション内の保存された検索条件は、1 つのカテゴリを共有することが多いため、コンソールではグループ化されています。 |
| displayName | ポータルでの保存された検索条件の表示名です。 |
| query | 実行するクエリ。 |

> [!NOTE]
> JSON として解釈される可能性のある文字が含まれる場合、クエリではエスケープ文字を使うことが必要になる場合があります。 たとえば、**AzureActivity | OperationName:"Microsoft.Compute/virtualMachines/write"** というクエリの場合、ソリューション ファイルには **AzureActivity | OperationName:/\"Microsoft.Compute/virtualMachines/write\"** と書き込まれる必要があります。

## <a name="alerts"></a>アラート
[Azure Log のアラート](../../azure-monitor/platform/alerts-unified-log.md)は、指定されたログ クエリを一定の間隔で実行する Azure Alert ルールによって作成されます。 クエリの結果が指定されている条件と一致する場合、アラート レコードが作成されて、[アクション グループ](../../azure-monitor/platform/action-groups.md)を使用して 1 つまたは複数のアクションが実行されます。

> [!NOTE]
> 2018 年 5 月 14 日から、Log Analytics ワークスペースの Azure パブリック クラウド インスタンス内のすべてのアラートは Azure に拡張されます。 詳細については、[Azure へのアラートの拡張](../../azure-monitor/platform/alerts-extend.md)に関するページを参照してください。 Azure にアラートを拡張すると、アクションを Azure のアクション グループで管理できるようになります。 ワークスペースとそのアラートを Azure に拡張すると、[アクション グループの Azure Resource Manager テンプレート](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)を使用してアクションを取得または追加できます。
管理ソリューションのアラート ルールは、次の 3 つの異なるリソースで構成されます。

- **保存された検索条件**。 実行されるログ検索を定義します。 複数のアラート ルールで、1 つの保存された検索条件を共有できます。
- **スケジュール**。 ログ検索の実行頻度を定義します。 各アラート ルールには、スケジュールが 1 つだけあります。
- **アラート アクション**。 各アラート ルールには **Alert** 型のアクション グループ リソースまたはアクション リソース (レガシ) が 1 つあり、アラート レコードが作成される条件やアラートの重大度などのアラートの詳細が定義されています。 [アクション グループ](../../azure-monitor/platform/action-groups.md) リソースには、音声通話、SMS、メール、webhook、ITSM ツール、Automation Runbook、ロジック アプリなど、アラート発生時に実行する構成済みアクションのリストを設定できます。

アクション リソース (レガシ) では、必要に応じて、メールと Runbook の応答が定義されます。
- **webhook アクション (レガシ)** アラート ルールが webhook を呼び出すときは、**Webhook** 型の追加のアクション リソースが必要です。

保存された検索条件リソースについては、上で説明してあります。 他のリソースについては以下で説明します。

### <a name="schedule-resource"></a>スケジュール リソース

保存された検索条件は 1 つ以上のスケジュールを持つことができ、各スケジュールは異なるアラート ルールを表します。 スケジュールでは、検索を実行する頻度と、データを取得する期間が定義されています。 スケジュール リソースは `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` 型であり、次のような構造をしています。 ソリューション ファイルにコード スニペットをコピーして貼り付け、パラメータ名を変更できるように、一般的な変数やパラメータが使用されています。

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
| enabled       | はい | 作成時点でアラートが有効かどうかを指定します。 |
| interval      | はい | クエリを実行する間隔です (分単位)。 |
| queryTimeSpan | はい | 結果を評価する期間です (分単位)。 |

スケジュール リソースは保存された検索条件に依存するので、スケジュールの前に作成する必要があります。
> [!NOTE]
> スケジュール名は、特定のワークスペース内では一意である必要があります。2 つのスケジュールが同じ ID を持つことはできません。スケジュールに関連付けられている、保存した検索条件がそれぞれ異なるとしても同様です。 また、Log Analytics API で作成する、すべての保存した検索条件、スケジュール、およびアクションは、小文字にする必要があります。

### <a name="actions"></a>Actions
スケジュールでは複数のアクションを使用できます。 アクションでは、メールの送信や Runbook の開始など、実行する 1 つ以上のプロセスを定義するか、または検索結果が条件に一致するためのしきい値を定義できます。 一部のアクションはそれらの両方を定義し、しきい値に達したときにプロセスが実行されます。
アクションは、アクション グループ リソースまたはアクション リソースを使って定義できます。
> [!NOTE]
> 2018 年 5 月 14 日から、Log Analytics ワークスペースの Azure パブリック クラウド インスタンス内のすべてのアラートは自動的に Azure に拡張されます。 詳細については、[Azure へのアラートの拡張](../../azure-monitor/platform/alerts-extend.md)に関するページを参照してください。 Azure にアラートを拡張すると、アクションを Azure のアクション グループで管理できるようになります。 ワークスペースとそのアラートを Azure に拡張すると、[アクション グループの Azure Resource Manager テンプレート](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)を使用してアクションを取得または追加できます。
**Type** プロパティによって指定される 2 種類のアクション リソースがあります。 スケジュールには、アラート ルールの詳細と、アラート作成時に実行するアクションが定義されている、1 つの **Alert** アクションが必要です。 アクション リソースは `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions` 型です。

アラート アクションの構造は次のとおりです。 ソリューション ファイルにコード スニペットをコピーして貼り付け、パラメータ名を変更できるように、一般的な変数やパラメータが使用されています。

```json
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
        "AzNsNotification": {
            "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
            "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]",
            "CustomWebhookPayload": "[variables('MyAlert').AzNsNotification.CustomWebhookPayload]"
        }
    }
}
```

次の表では、アラート アクション リソースのプロパティについて説明します。

| 要素名 | 必須 | Description |
|:--|:--|:--|
| Type | はい | アクションの種類。  これは、アラート アクションの**アラート**です。 |
| Name | はい | アラートの表示名。  これは、コンソールに表示されるアラート ルールの名前です。 |
| Description | いいえ | アラートに関する省略可能な説明です。 |
| Severity | はい | アラート レコードの重大度であり、次のいずれかの値です。<br><br> **critical**<br>**warning**<br>**informational**


#### <a name="threshold"></a>Threshold
このセクションは必須です。 アラートのしきい値のプロパティを定義します。

| 要素名 | 必須 | Description |
|:--|:--|:--|
| Operator | はい | 比較のための演算子であり、次のいずれかの値です。<br><br>**gt = より大きい<br>lt = より小さい** |
| Value | はい | 結果を比較する値です。 |

##### <a name="metricstrigger"></a>MetricsTrigger
このセクションは省略可能です。 メトリック測定アラートの場合に指定します。

> [!NOTE]
> メトリック測定アラートは現在パブリック プレビュー中です。

| 要素名 | 必須 | Description |
|:--|:--|:--|
| TriggerCondition | はい | しきい値が、違反の合計数に対するものか、または連続する違反の数に対するものかを、次の値で指定します。<br><br>**Total<br>Consecutive** |
| Operator | はい | 比較のための演算子であり、次のいずれかの値です。<br><br>**gt = より大きい<br>lt = より小さい** |
| Value | はい | アラートをトリガーするために必要な、条件が満たされた回数です。 |


#### <a name="throttling"></a>Throttling
このセクションは省略可能です。 同じルールからのアラートを、アラート作成後の一定期間にわたって抑制する場合に、このセクションを指定します。

| 要素名 | 必須 | Description |
|:--|:--|:--|
| DurationInMinutes | Throttling 要素が含まれる場合は Yes です。 | アラートが作成された後、それと同じアラート ルールからにアラートを抑制する分数です。 |

#### <a name="azure-action-group"></a>Azure のアクション グループ
Azure のすべてのアラートは、アクションを管理する既定のメカニズムとして、アクション グループを使用します。 アクション グループを使用すると、一度に複数のアクションを指定し、そのアクション グループを Azure 全体にわたって複数のアラートに関連付けることができます。 繰り返し宣言することなく、同じアクションを繰り返し実行できます。 アクション グループは、電子メール、SMS、音声通話、ITSM Connection、Automation Runbook、Webhook URI など、複数のアクションに対応しています。

アラートを Azure に拡張しているユーザーの場合、スケジュールにアクション グループの詳細がしきい値とともに渡され、アラートを作成できるようになっています。 アラートを作成する前に、電子メールの詳細、Webhook の URL、Runbook Automation の詳細、およびその他のアクションをアクション グループ内に定義する必要があります。Portal の [Azure Monitor からアクション グループ](../../azure-monitor/platform/action-groups.md)を作成するか、[アクション グループ リソース テンプレート](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)を使用できます。

| 要素名 | 必須 | Description |
|:--|:--|:--|
| AzNsNotification | はい | アラート条件が満たされたときに必要なアクションを実行するためにアラートに関連付ける Azure アクション グループのリソース ID です。 |
| CustomEmailSubject | いいえ | 関連付けられたアクション グループで指定されているすべてのアドレスに送信されるメールのカスタム件名行です。 |
| CustomWebhookPayload | いいえ | 関連付けられたアクション グループで定義されているすべての webhook エンドポイントに送信するカスタマイズされたペイロードです。 形式は、webhook で想定されていることに依存し、有効なシリアル化された JSON である必要があります。 |

#### <a name="actions-for-oms-legacy"></a>OMS のアクション (レガシ)

どのスケジュールも 1 つの**アラート** アクションを保持しています。 アラート アクションでは、アラートの詳細と、必要に応じて通知と修復のアクションが定義されています。 通知は、1 つ以上のアドレスにメールを送信します。 修復は、Azure Automation で Runbook を開始し、検出された問題の修復を試みます。

> [!NOTE]
> 2018 年 5 月 14 日から、Log Analytics ワークスペースの Azure パブリック クラウド インスタンス内のすべてのアラートは自動的に Azure に拡張されます。 詳細については、[Azure へのアラートの拡張](../../azure-monitor/platform/alerts-extend.md)に関するページを参照してください。 Azure にアラートを拡張すると、アクションを Azure のアクション グループで管理できるようになります。 ワークスペースとそのアラートを Azure に拡張すると、[アクション グループの Azure Resource Manager テンプレート](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)を使用してアクションを取得または追加できます。

##### <a name="emailnotification"></a>EmailNotification
 このセクションは省略可能です。アラートによって 1 人以上の受信者にメールを送信する場合に指定します。

| 要素名 | 必須 | Description |
|:--|:--|:--|
| Recipients | はい | アラートが作成されたときに通知を送信するメール アドレスのコンマ区切りのリストです。次に例を示します。<br><br>**[ "recipient1\@contoso.com", "recipient2\@contoso.com" ]** |
| サブジェクト | はい | メールの件名です。 |
| Attachment | いいえ | 添付ファイルは現在はサポートされていません。 この要素を指定する場合は、**None** にする必要があります。 |

##### <a name="remediation"></a>Remediation
このセクションは省略可能です。アラートに応答して Runbook を開始する場合に指定します。 

| 要素名 | 必須 | Description |
|:--|:--|:--|
| RunbookName | はい | 開始する Runbook の名前です。 |
| WebhookUri | はい | Runbook に対する webhook の URI です。 |
| Expiry | いいえ | 修復が期限切れになる日付と時刻です。 |

##### <a name="webhook-actions"></a>Webhook アクション

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

| 要素名 | 必須 | Description |
|:--|:--|:--|
| type | はい | アクションの種類。 これは、webhook アクションの **Webhook** です。 |
| name | はい | アクションの表示名です。 コンソールには表示されません。 |
| webhookUri | はい | webhook の URI です。 |
| customPayload | いいえ | Webhook に送信するカスタム ペイロード。 形式は、Webhook で想定される内容によって異なります。 |

## <a name="sample"></a>サンプル

以下のリソースを含むソリューションのサンプルを次に示します。

- 保存された検索条件
- スケジュール
- アクション グループ

このサンプルでは、リソースの定義に値をハードコーディングするのではなく、ソリューションでよく使われる[標準ソリューション パラメーター]( solutions-solution-file.md#parameters)の変数を使っています。

```json
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
        "workspaceregionId": {
            "type": "string",
            "metadata": {
                "Description": "Region of Log Analytics workspace"
            }
        },
        "actiongroup": {
            "type": "string",
            "metadata": {
                "Description": "List of action groups for alert actions separated by semicolon"
            }
        }
    },
    "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
        "LogAnalyticsApiVersion-Search": "2017-03-15-preview",
        "LogAnalyticsApiVersion-Solution": "2015-11-01-preview",
        "MySearch": {
            "displayName": "Error records by hour",
            "query": "MyRecord_CL | summarize AggregatedValue = avg(Rating_d) by Instance_s, bin(TimeGenerated, 60m)",
            "category": "Samples",
            "name": "Samples-Count of data"
        },
        "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert. Fires when 3 error records found over hour interval.",
            "Severity": "critical",
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
            "AzNsNotification": {
                "GroupIds": [
                    "[parameters('actiongroup')]"
                ],
                "CustomEmailSubject": "Sample alert"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Solution')]",
            "dependsOn": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
            ],
            "properties": {
                "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
                "referencedResources": [
                ],
                "containedResources": [
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
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
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
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
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
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
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
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
                "AzNsNotification": {
                    "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
                    "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]"
                }
            }
        }
    ]
}
```

次のパラメーター ファイルで、このソリューションのサンプル値を提供します。

```json
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
        "actiongroup": {
            "value": "/subscriptions/3b540246-808d-4331-99aa-917b808a9166/resourcegroups/myTestGroup/providers/microsoft.insights/actiongroups/sample"
        }
    }
}
```

## <a name="next-steps"></a>次の手順
* 管理ソリューションに[ビューを追加する](solutions-resources-views.md)。
* 管理ソリューションに [Automation Runbook とその他のリソースを追加する](solutions-resources-automation.md)。
