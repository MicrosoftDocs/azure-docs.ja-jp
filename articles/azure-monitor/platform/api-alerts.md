---
title: Log Analytics のアラート REST API の使用
description: Log Analytics のアラート REST API は、Log Analytics の一部である Log Analytics でアラートを作成し、管理するために使用できます。  この記事では、API の詳細と、さまざまな操作を実行するいくつかの例について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2018
ms.author: bwren
ms.openlocfilehash: bee64909c7f3b295691ef1cb1840424aa7e3fe49
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549714"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>REST API を使用して Log Analytics でアラートのルールを作成および管理する
Log Analytics のアラート REST API は、Log Analytics でアラートを作成し、管理するために使用できます。  この記事では、API の詳細と、さまざまな操作を実行するいくつかの例について説明します。

Log Analytics の検索 REST API は RESTful であり、Azure Resource Manager REST API を使用してアクセスできます。 このドキュメントでは、Azure Resource Manager API の呼び出しを簡略化するオープン ソースのコマンドライン ツールである [ARMClient](https://github.com/projectkudu/ARMClient) を使用して PowerShell コマンド ラインから API にアクセスする例を示します。 Log Analytics 検索 API には、ARMClient や PowerShell を使用する以外にもさまざまな方法でアクセスできます。 これらのツールを使用すると、RESTful Azure Resource Manager API を使用して Log Analytics のワークスペースにアクセスし、その中で検索コマンドを実行できます。 API の検索結果は JSON 形式で出力されるため、検索結果をプログラムによりさまざまな方法で使用できます。

## <a name="prerequisites"></a>前提条件
現時点では、アラートは Log Analytics の保存した検索条件でのみ作成できます。  詳細については、「 [Log Search REST API (ログ検索 REST API)](../../azure-monitor/log-query/log-query-overview.md) 」を参照してください。

## <a name="schedules"></a>スケジュール
保存した検索条件には、1 つ以上のスケジュールを設定できます。 スケジュールは、検索を実行する頻度と、条件を識別する時間間隔を定義します。
スケジュールには、次の表に示したプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| Interval |検索を実行する頻度。 分単位で指定します。 |
| QueryTimeSpan |条件を評価する時間間隔。 [Interval] の値以上にする必要があります。 分単位で指定します。 |
| バージョン |使用している API バージョン。  現時点では、常に 1 に設定する必要があります。 |

たとえば、[Interval] を 15 分、[QueryTimeSpan] を 30 分に設定したイベント クエリを考えます。 この場合、クエリは 15 分ごとに実行され、条件が 30 分間にわたって true に評価されるとアラートがトリガーされます。

### <a name="retrieving-schedules"></a>スケジュールの取得
Get メソッドを使用して、保存した検索条件のすべてのスケジュールを取得します。

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

保存した検索条件に関して特定のスケジュールを取得するには、Get メソッドにスケジュール ID を渡します。

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

スケジュールに対する応答の例を次に示します。

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/sampleRG/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15,
            "Enabled": true,
        }
    }]
}
```

### <a name="creating-a-schedule"></a>スケジュールを作成する
新しいスケジュールを作成するには、Put メソッドに一意のスケジュール ID を渡します。  2 つのスケジュールが同じ ID を持つことはできないことに注意してください。スケジュールに関連付けられている、保存した検索条件がそれぞれ異なるとしても同様です。  Log Analytics コンソールでスケジュールを作成すると、スケジュール ID に対して GUID が作成されます。

> [!NOTE]
> Log Analytics API で作成する、すべての保存した検索条件、スケジュール、およびアクションは、小文字にする必要があります。

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>スケジュールの編集
スケジュールを編集するには、Put メソッドを使用して、同じ保存済み検索条件の既存のスケジュール ID を指定します。下記の例では、スケジュールが無効化されています。 要求の本体には、スケジュールの *etag* が含まれている必要があります。

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>スケジュールの削除
スケジュールを削除するには、スケジュールの ID と共に Delete メソッドを使用します。

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Actions
スケジュールでは複数のアクションを使用できます。 アクションでは、メールの送信や Runbook の開始など、実行する 1 つ以上のプロセスを定義するか、または検索結果が条件に一致するためのしきい値を定義できます。  一部のアクションはそれらの両方を定義し、しきい値に達したときにプロセスが実行されます。

すべてのアクションには、次の表に示したプロパティがあります。  後で説明するように、アラートの種類によって異なる追加のプロパティもあります。

| プロパティ | 説明 |
|:--- |:--- |
| `Type` |アクションの種類。  現在使用可能な値は、[Alert] と [Webhook] です。 |
| `Name` |アラートの表示名。 |
| `Version` |使用している API バージョン。  現時点では、常に 1 に設定する必要があります。 |

### <a name="retrieving-actions"></a>アクションの取得

> [!NOTE]
> 2018 年 5 月 14 日より、Log Analytics ワークスペースの Azure パブリック クラウド インスタンス内のすべてのアラートが Azure に自動的に拡張されます。 ユーザーは 2018 年 5 月 14 日より前に、アラートの Azure への拡張を自主的に開始できます。 詳細については、[Log Analytics から Azure へのアラートの拡張](../../azure-monitor/platform/alerts-extend.md)に関するページを参照してください。 Azure にアラートを拡張すると、アクションを Azure のアクション グループで管理できるようになります。 ワークスペースとそのアラートを Azure に拡張すると、[アクション グループの API](https://docs.microsoft.com/rest/api/monitor/actiongroups) を使用してアクションを取得または追加できます。

Get メソッドを使用して、スケジュールのすべてのアクションを取得します。

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Get メソッドと共にアクション ID を使用して、スケジュールの特定のアクションを取得します。

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>アクションの作成または編集
新しいアクションを作成するには、スケジュールに固有のアクション ID を Put メソッドに渡します。  Log Analytics コンソールでアクションを作成するときの GUID は、そのアクション ID を定義するためのものです。

> [!NOTE]
> Log Analytics API で作成する、すべての保存した検索条件、スケジュール、およびアクションは、小文字にする必要があります。

同じ保存した検索条件のアクションを編集するには、既存のアクション ID を Put メソッドに渡します。  要求の本体には、スケジュールの etag が含まれている必要があります。

新しいアクションを作成するための要求の形式は、アクションの種類によって異なるため、以下のセクションに例を示しています。

### <a name="deleting-actions"></a>アクションの削除

> [!NOTE]
> 2018 年 5 月 14 日より、Log Analytics ワークスペースの Azure パブリック クラウド インスタンス内のすべてのアラートが Azure に自動的に拡張されます。 ユーザーは 2018 年 5 月 14 日より前に、アラートの Azure への拡張を自主的に開始できます。 詳細については、[Log Analytics から Azure へのアラートの拡張](../../azure-monitor/platform/alerts-extend.md)に関するページを参照してください。 Azure にアラートを拡張すると、アクションを Azure のアクション グループで管理できるようになります。 ワークスペースとそのアラートを Azure に拡張すると、[アクション グループの API](https://docs.microsoft.com/rest/api/monitor/actiongroups) を使用してアクションを取得または追加できます。

アクション ID と共に Delete メソッドを使用すると、アクションを削除できます。

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>アラート アクション
スケジュールには、アラート アクションを 1 つだけ指定する必要があります。  アラート アクションには、次の表に示したセクションが 1 つ以上含まれています。  それぞれについて、以下で詳しく説明します。

| セクション | 説明 | 使用法 |
|:--- |:--- |:--- |
| Threshold |アクションがいつ実行されるかの条件。| Azure に拡張される前と後の両方の、すべてのアラートで必要です。 |
| Severity |アラートがトリガーされるときに分類に使用されるラベル。| Azure に拡張される前と後の両方の、すべてのアラートで必要です。 |
| Suppress |アラートからの通知を停止するオプション。 | Azure に拡張される前と後の両方の、すべてのアラートで省略可能です。 |
| Action Groups |Azure ActionGroup の ID。電子メール、SMS、音声通話、Webhook、Automation Runbook、ITSM Connector など、必要なアクションが指定されています。| アラートが Azure に拡張されると必要|
| Customize Actions|ActionGroup の選択したアクションの標準出力を変更します| すべてのアラートで省略可能で、アラートが Azure に拡張された後に使用できます。 |
| EmailNotification |複数の受信者にメールを送信します。 | アラートが Azure に拡張された後は不要|
| Remediation |識別された問題を解決するための Runbook を Azure Automation で開始します。 |アラートが Azure に拡張された後は不要|
| Webhook アクション | アラートから JSON として必要なサービスにデータをプッシュします |アラートが Azure に拡張された後は不要|

> [!NOTE]
> 2018 年 5 月 14 日より、Log Analytics ワークスペースの Azure パブリック クラウド インスタンス内のすべてのアラートが Azure に自動的に拡張されます。 ユーザーは 2018 年 5 月 14 日より前に、アラートの Azure への拡張を自主的に開始できます。 詳細については、[Log Analytics から Azure へのアラートの拡張](../../azure-monitor/platform/alerts-extend.md)に関するページを参照してください。

#### <a name="thresholds"></a>しきい値
アラート アクションには、しきい値を 1 つだけ指定する必要があります。  保存した検索条件の結果が、その検索に関連付けられているアクションのしきい値に一致すると、そのアクションの他のすべてのプロセスが実行されます。  また、アクションにしきい値だけを含めて、しきい値を含まない他の種類のアクションと共に使用することもできます。

しきい値には、次の表に示したプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| `Operator` |しきい値の比較演算子。 <br> gt = より大きい <br> lt = より小さい |
| `Value` |しきい値の値。 |

たとえば、[Interval] を 15 分、[QueryTimeSpan] を 30 分に設定し、しきい値を 10 より大きく設定したイベント クエリを考えます。 この場合、クエリは 15 分ごとに実行され、30 分間にわたって作成された 10 個のイベントが返されたときに、アラートがトリガーされます。

しきい値のみを含むアクションに対する応答の例を次に示します。  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

スケジュールの新しいしきい値アクションを作成するには、Put メソッドに一意のアクション ID を渡します。  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

スケジュールのしきい値アクションを編集するには、既存のアクション ID を Put メソッドに渡します。  要求の本体には、アクションの etag が含まれている必要があります。

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Severity
Log Analytics を使用するとアラートをカテゴリに分類し、簡単に管理およびトリアージできます。 定義されているアラートの重大度は、情報、警告、重大です。 これらは次のように Azure Alerts の正規化された重大度スケールにマッピングされています。

|Log Analytics の重大度レベル  |Azure Alerts の重大度レベル  |
|---------|---------|
|`critical` |重大度 0|
|`warning` |重大度 1|
|`informational` | 重大度 2|

しきい値と重大度のみを含むアクションに対する応答の例を次に示します。 

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Severity": "critical",
        "Version": 1    }

スケジュールの新しいアクションを重大度で作成するには、Put メソッドに一意のアクション ID を渡します。  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

スケジュールの重大度アクションを編集するには、既存のアクション ID を Put メソッドに渡します。  要求の本体には、アクションの etag が含まれている必要があります。

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>Suppress
Log Analytics ベースのクエリ アラートは、しきい値に達するか超過するたびに発生します。 クエリに示されたロジックに基づいて、連続する間隔でアラートが生成され、その結果の通知が常に送信される可能性があります。 このようなシナリオを防ぐため、ユーザーは、指定した時間待機してからアラート ルールの 2 回目の通知が発生するように Log Analytics に指示する [抑制] オプションを設定することができます。 抑制が 30 分に設定したとします。まず最初のアラートが生成され、構成された通知が送信されます。 この場合、アラート ルールの通知が再び使用されるまで、30 分待機されます。 その間、アラート ルールは継続して実行されます。この期間にアラート ルールが何回生成されたかにかかわらず、指定した期間は Log Analytics によって通知のみが抑制されます。

Log Analytics アラート ルールの抑制プロパティは、*[スロットリング]* 値と、*DurationInMinutes* 値を使用した抑制期間を使用して指定されます。

しきい値、重大度、および抑制プロパティのみを含むアクションに対する応答の例を次に示します

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Throttling": {
          "DurationInMinutes": 30
        },
        "Severity": "critical",
        "Version": 1    }

スケジュールの新しいアクションを重大度で作成するには、Put メソッドに一意のアクション ID を渡します。  

    $AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

スケジュールの重大度アクションを編集するには、既存のアクション ID を Put メソッドに渡します。  要求の本体には、アクションの etag が含まれている必要があります。

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>Action Groups
Azure のすべてのアラートは、アクションを管理する既定のメカニズムとして、アクション グループを使用します。 アクション グループを使用すると、一度に複数のアクションを指定し、そのアクション グループを Azure 全体にわたって複数のアラートに関連付けることができます。 繰り返し宣言することなく、同じアクションを繰り返し実行できます。 アクション グループは、電子メール、SMS、音声通話、ITSM Connection、Automation Runbook、Webhook URI など、複数のアクションに対応しています。 

アラートを Azure に拡張しているユーザーの場合、スケジュールにアクション グループの詳細がしきい値とともに渡され、アラートを作成できるようになっています。 アラートを作成する前に、電子メールの詳細、Webhook の URL、Runbook Automation の詳細、およびその他のアクションをアクション グループ内に定義する必要があります。Portal の [Azure Monitor からアクション グループ](../../azure-monitor/platform/action-groups.md)を作成するか、[アクション グループ API](https://docs.microsoft.com/rest/api/monitor/actiongroups) を使用できます。

アラートにアクション グループの関連付けを追加するには、アラートの定義にそのアクション グループの一意の Azure Resource Manager ID を指定します。 以下にサンプル図を示します。

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
        },
        "Severity": "critical",
        "Version": 1
      },

Put メソッドを一意のアクション ID とともに使用して、スケジュールの既存のアクション グループを関連付けます。  次に使用状況のサンプル図を示します。

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Put メソッドを既存のアクション ID とともに使用して、スケジュールに関連付けられているアクション グループを編集します。  要求の本体には、アクションの etag が含まれている必要があります。

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Customize Actions
既定のアクションで、通知の標準のテンプレートとフォーマットに従います。 ただし、アクション グループによって制御されている場合でも、ユーザーは一部のアクションをカスタマイズできます。 現時点では、電子メールの件名と Webhook のペイロードをカスタマイズできます。

##### <a name="customize-e-mail-subject-for-action-group"></a>アクション グループの電子メールの件名をカスタマイズする
既定では、アラートの電子メールの件名は、`<WorkspaceName>` に関するアラートの通知 `<AlertName>` です。 ただしこれはカスタマイズできるため、言葉やタグを指定できます。これにより、受信トレイにフィルター規則を簡単に適用できます。 電子メールの件名の詳細のカスタマイズは、以下のサンプルに示すように、ActionGroup の詳細とともに送信する必要があります。

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Put メソッドを一意のアクション ID とともに使用して、スケジュールの既存のアクション グループをカスタマイズとともに関連付けます。  次に使用状況のサンプル図を示します。

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Put メソッドを既存のアクション ID とともに使用して、スケジュールに関連付けられているアクション グループを編集します。  要求の本体には、アクションの etag が含まれている必要があります。

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>アクション グループの Webhook のペイロードをカスタマイズする
既定では、ログ分析のためにアクション グループを介して送信される Webhook の構造は固定されています。 ただし、サポートされている特定の変数を使用して、Webhook のエンドポイントの条件を満たすように、JSON のペイロードをカスタマイズできます。 詳細については、「[ログ アラート ルールの webhook アクション](../../azure-monitor/platform/alerts-log-webhook.md)」を参照してください 

Webhook の詳細のカスタマイズは、ActionGroup の詳細とともに送信する必要があり、以下のサンプルに示すように、そのアクション グループ内に指定されたすべての Webhook URI に適用されます。

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Put メソッドを一意のアクション ID とともに使用して、スケジュールの既存のアクション グループをカスタマイズとともに関連付けます。  次に使用状況のサンプル図を示します。

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Put メソッドを既存のアクション ID とともに使用して、スケジュールに関連付けられているアクション グループを編集します。  要求の本体には、アクションの etag が含まれている必要があります。

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="email-notification"></a>電子メール通知
電子メール通知は、1 人以上の受信者にメールを送信します。  電子メール通知には、次の表に示したプロパティがあります。

> [!NOTE]
> 2018 年 5 月 14 日より、Log Analytics ワークスペースの Azure パブリック クラウド インスタンス内のすべてのアラートが Azure に自動的に拡張されます。 ユーザーは 2018 年 5 月 14 日より前に、アラートの Azure への拡張を自主的に開始できます。 詳細については、[Log Analytics から Azure へのアラートの拡張](../../azure-monitor/platform/alerts-extend.md)に関するページを参照してください。 Azure にアラートを拡張すると、電子メール通知などのアクションを Azure のアクション グループで管理できるようになります。 ワークスペースとそのアラートを Azure に拡張すると、[アクション グループの API](https://docs.microsoft.com/rest/api/monitor/actiongroups) を使用してアクションを取得または追加できます。
   

| プロパティ | 説明 |
|:--- |:--- |
| Recipients |メール アドレスの一覧。 |
| サブジェクト |メールの件名。 |
| Attachment |添付ファイルは現在サポートされていないため、値は常に "None" になります。 |

しきい値を含む電子メール通知に対する応答の例を次に示します。  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

スケジュールの新しい電子メール アクションを作成するには、Put メソッドに一意のアクション ID を渡します。  次の例では、しきい値を含む電子メール通知を作成し、保存した検索条件の結果がしきい値を超えた場合にメールが送信されます。

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

スケジュールの電子メール アクションを編集するには、既存のアクション ID を Put メソッドに渡します。  要求の本体には、アクションの etag が含まれている必要があります。

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

#### <a name="remediation-actions"></a>修復アクション
修復では、アラートで識別された問題を修正するための Runbook が Azure Automation で開始されます。  修復アクションで使用される Runbook の Webhook を作成し、WebhookUri プロパティに URI を指定する必要があります。  Azure portal を使用してこのアクションを作成すると、Runbook に対して新しい Webhook が自動的に作成されます。

> [!NOTE]
> 2018 年 5 月 14 日より、Log Analytics ワークスペースの Azure パブリック クラウド インスタンス内のすべてのアラートが Azure に自動的に拡張されます。 ユーザーは 2018 年 5 月 14 日より前に、アラートの Azure への拡張を自主的に開始できます。 詳細については、[Log Analytics から Azure へのアラートの拡張](../../azure-monitor/platform/alerts-extend.md)に関するページを参照してください。 Azure にアラートを拡張すると、Runbook を使用した修復などのアクションを Azure のアクション グループで管理できるようになります。 ワークスペースとそのアラートを Azure に拡張すると、[アクション グループの API](https://docs.microsoft.com/rest/api/monitor/actiongroups) を使用してアクションを取得または追加できます。

修復には、次の表に示したプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| RunbookName |Runbook の名前。 これは、Log Analytics ワークスペースの Automation ソリューションで構成されたオートメーション アカウントの発行済み Runbook と一致する必要があります。 |
| WebhookUri |Webhook の URI。 |
| Expiry |Webhook の有効期限の日付と時刻。  Webhook に有効期限がない場合は、任意の有効な将来の日付を設定できます。 |

しきい値を含む修復アクションに対する応答の例を次に示します。

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

スケジュールの新しい修復アクションを作成するには、Put メソッドに一意のアクション ID を渡します。  次の例では、しきい値を含む修復を作成し、保存した検索条件の結果がしきい値を超えた場合に Runbook が開始されます。

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

スケジュールの修復アクションを編集するには、既存のアクション ID を Put メソッドに渡します。  要求の本体には、アクションの etag が含まれている必要があります。

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>例
新しい電子メール アラートを作成する完全な例を次に示します。  この例では、しきい値と電子メールを含むアクションと共に新しいスケジュールを作成します。

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $resourceGroup  = "MyResourceGroup"    
    $workspaceName    = "MyWorkspace"
    $searchId       = "MySearch"
    $scheduleId     = "MySchedule"
    $thresholdId    = "MyThreshold"
    $actionId       = "MyEmailAction"
    
    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/?api-version=2015-03-20 $scheduleJson
    
    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Severity':'Warning', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/actions/$actionId/?api-version=2015-03-20 $emailJson

#### <a name="webhook-actions"></a>Webhook アクション
Webhook アクションは、URL を呼び出し、送信されるペイロードをオプションで指定することにより、プロセスを開始します。  これは修復アクションに似ていますが、Azure Automation の Runbook 以外のプロセスを呼び出す可能性のある Webhook に対して使用することを意図しています。  また、リモート プロセスに配信されるペイロードを指定する追加のオプションも用意されています。

> [!NOTE]
> 2018 年 5 月 14 日より、Log Analytics ワークスペースの Azure パブリック クラウド インスタンス内のすべてのアラートが Azure に自動的に拡張されます。 ユーザーは 2018 年 5 月 14 日より前に、アラートの Azure への拡張を自主的に開始できます。 詳細については、[Log Analytics から Azure へのアラートの拡張](../../azure-monitor/platform/alerts-extend.md)に関するページを参照してください。 Azure にアラートを拡張すると、Webhook などのアクションを Azure のアクション グループで管理できるようになります。 ワークスペースとそのアラートを Azure に拡張すると、[アクション グループの API](https://docs.microsoft.com/rest/api/monitor/actiongroups) を使用してアクションを取得または追加できます。


Webhook アクションにはしきい値はありませんが、その代わりに、しきい値のあるアラート アクションを含むスケジュールに追加する必要があります。  

Webhook アクションとしきい値を含む関連アラート アクションに対する応答の例を次に示します。

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

##### <a name="create-or-edit-a-webhook-action"></a>Webhook アクションの作成または編集
スケジュールの新しい Webhook アクションを作成するには、Put メソッドに一意のアクション ID を渡します。  次の例では、Webhook アクションとしきい値を含むアラート アクションとを作成して、保存した検索条件の結果がしきい値を超えたときに Webhook がトリガーされるようにします。

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

スケジュールの Webhook アクションを編集するには、既存のアクション ID を Put メソッドに渡します。  要求の本体には、アクションの etag が含まれている必要があります。

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction


## <a name="next-steps"></a>次の手順
* Log Analytics で [ログ検索を実行するための REST API](../../azure-monitor/log-query/log-query-overview.md) を使用します。
* [Azure Alerts のログ アラート](../../azure-monitor/platform/alerts-unified-log.md)について確認します。

