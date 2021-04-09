---
title: Log Analytics のアラート REST API の使用
description: Log Analytics のアラート REST API は、Log Analytics の一部である Log Analytics でアラートを作成し、管理するために使用できます。  この記事では、API の詳細と、さまざまな操作を実行するいくつかの例について説明します。
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 33f5ce86a21a9aad110db162849ff3e3c7bbb2b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037491"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>REST API を使用して Log Analytics でアラートのルールを作成および管理する 

> [!IMPORTANT]
> [お知らせ](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/)のとおり、"*2019 年 6 月 1 日*" の後に作成されたログ分析ワークスペースでは、現行の [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules/) を利用してアラート ルールが管理されます。 Azure Monitor scheduledQueryRules [特典](./alerts-log-api-switch.md#benefits)を活用するため、以前のワークスペースでは、[現行の API に切り替える](./alerts-log-api-switch.md)ことをお客様に推奨しています。 この記事では、レガシ API を利用するアラートの管理について説明します。

Log Analytics のアラート REST API は、Log Analytics でアラートを作成し、管理するために使用できます。  この記事では、API の詳細と、さまざまな操作を実行するいくつかの例について説明します。

Log Analytics の検索 REST API は RESTful であり、Azure Resource Manager REST API を使用してアクセスできます。 このドキュメントでは、Azure Resource Manager API の呼び出しを簡略化するオープン ソースのコマンドライン ツールである [ARMClient](https://github.com/projectkudu/ARMClient) を使用して PowerShell コマンド ラインから API にアクセスする例を示します。 Log Analytics 検索 API には、ARMClient や PowerShell を使用する以外にもさまざまな方法でアクセスできます。 これらのツールを使用すると、RESTful Azure Resource Manager API を使用して Log Analytics のワークスペースにアクセスし、その中で検索コマンドを実行できます。 API の検索結果は JSON 形式で出力されるため、検索結果をプログラムによりさまざまな方法で使用できます。

## <a name="prerequisites"></a>前提条件
現時点では、アラートは Log Analytics の保存した検索条件でのみ作成できます。  詳細については、「 [Log Search REST API (ログ検索 REST API)](../logs/log-query-overview.md) 」を参照してください。

## <a name="schedules"></a>スケジュール
保存した検索条件には、1 つ以上のスケジュールを設定できます。 スケジュールは、検索を実行する頻度と、条件を識別する時間間隔を定義します。
スケジュールには、次の表に示したプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| Interval |検索を実行する頻度。 分単位で指定します。 |
| QueryTimeSpan |条件を評価する時間間隔。 [Interval] の値以上にする必要があります。 分単位で指定します。 |
| Version |使用している API バージョン。  現時点では、常に 1 に設定する必要があります。 |

たとえば、[Interval] を 15 分、[QueryTimeSpan] を 30 分に設定したイベント クエリを考えます。 この場合、クエリは 15 分ごとに実行され、条件が 30 分間にわたって true に評価されるとアラートがトリガーされます。

### <a name="retrieving-schedules"></a>スケジュールの取得
Get メソッドを使用して、保存した検索条件のすべてのスケジュールを取得します。

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20
```

保存した検索条件に関して特定のスケジュールを取得するには、Get メソッドにスケジュール ID を渡します。

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20
```

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
新しいスケジュールを作成するには、Put メソッドに一意のスケジュール ID を渡します。  2 つのスケジュールが同じ ID を持つことは、たとえ異なる保存された検索に関連付けられている場合であっても、できません。  Log Analytics コンソールでスケジュールを作成すると、スケジュール ID に対して GUID が作成されます。

> [!NOTE]
> Log Analytics API で作成する、すべての保存した検索条件、スケジュール、およびアクションは、小文字にする必要があります。

```powershell
$scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson
```

### <a name="editing-a-schedule"></a>スケジュールの編集
スケジュールを編集するには、Put メソッドを使用して、同じ保存済み検索条件の既存のスケジュール ID を指定します。下記の例では、スケジュールが無効化されています。 要求の本体には、スケジュールの *etag* が含まれている必要があります。

```powershell
$scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson
```

### <a name="deleting-schedules"></a>スケジュールの削除
スケジュールを削除するには、スケジュールの ID と共に Delete メソッドを使用します。

```powershell
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20
```

## <a name="actions"></a>Actions
スケジュールでは複数のアクションを使用できます。 アクションでは、メールの送信や Runbook の開始など、実行する 1 つ以上のプロセスを定義するか、または検索結果が条件に一致するためのしきい値を定義できます。  一部のアクションはそれらの両方を定義し、しきい値に達したときにプロセスが実行されます。

すべてのアクションには、次の表に示したプロパティがあります。  後で説明するように、アラートの種類によって異なる追加のプロパティもあります。

| プロパティ | 説明 |
|:--- |:--- |
| `Type` |アクションの種類。  現在使用可能な値は、[Alert] と [Webhook] です。 |
| `Name` |アラートの表示名。 |
| `Version` |使用している API バージョン。  現時点では、常に 1 に設定する必要があります。 |

### <a name="retrieving-actions"></a>アクションの取得

Get メソッドを使用して、スケジュールのすべてのアクションを取得します。

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20
```

Get メソッドと共にアクション ID を使用して、スケジュールの特定のアクションを取得します。

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20
```

### <a name="creating-or-editing-actions"></a>アクションの作成または編集
新しいアクションを作成するには、スケジュールに固有のアクション ID を Put メソッドに渡します。  Log Analytics コンソールでアクションを作成するときの GUID は、そのアクション ID を定義するためのものです。

> [!NOTE]
> Log Analytics API で作成する、すべての保存した検索条件、スケジュール、およびアクションは、小文字にする必要があります。

同じ保存した検索条件のアクションを編集するには、既存のアクション ID を Put メソッドに渡します。  要求の本体には、スケジュールの etag が含まれている必要があります。

新しいアクションを作成するための要求の形式は、アクションの種類によって異なるため、以下のセクションに例を示しています。

### <a name="deleting-actions"></a>アクションの削除

アクション ID と共に Delete メソッドを使用すると、アクションを削除できます。

```powershell
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20
```

### <a name="alert-actions"></a>アラート アクション
スケジュールには、アラート アクションを 1 つだけ指定する必要があります。  アラート アクションには、次の表に示したセクションが 1 つ以上含まれています。  それぞれについて、以下で詳しく説明します。

| Section | 説明 | 使用法 |
|:--- |:--- |:--- |
| Threshold |アクションがいつ実行されるかの条件。| Azure に拡張される前と後の両方の、すべてのアラートで必要です。 |
| 重大度 |アラートがトリガーされるときに分類に使用されるラベル。| Azure に拡張される前と後の両方の、すべてのアラートで必要です。 |
| Suppress |アラートからの通知を停止するオプション。 | Azure に拡張される前と後の両方の、すべてのアラートで省略可能です。 |
| Action Groups |Azure ActionGroup の ID。電子メール、SMS、音声通話、Webhook、Automation Runbook、ITSM Connector など、必要なアクションが指定されています。| アラートが Azure に拡張されると必要|
| Customize Actions|ActionGroup の選択したアクションの標準出力を変更します| すべてのアラートで省略可能で、アラートが Azure に拡張された後に使用できます。 |

### <a name="thresholds"></a>しきい値
アラート アクションには、しきい値を 1 つだけ指定する必要があります。  保存した検索条件の結果が、その検索に関連付けられているアクションのしきい値に一致すると、そのアクションの他のすべてのプロセスが実行されます。  また、アクションにしきい値だけを含めて、しきい値を含まない他の種類のアクションと共に使用することもできます。

しきい値には、次の表に示したプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| `Operator` |しきい値の比較演算子。 <br> gt = より大きい <br> lt = より小さい |
| `Value` |しきい値の値。 |

たとえば、[Interval] を 15 分、[QueryTimeSpan] を 30 分に設定し、しきい値を 10 より大きく設定したイベント クエリを考えます。 この場合、クエリは 15 分ごとに実行され、30 分間にわたって作成された 10 個のイベントが返されたときに、アラートがトリガーされます。

しきい値のみを含むアクションに対する応答の例を次に示します。  

```json
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
```

スケジュールの新しいしきい値アクションを作成するには、Put メソッドに一意のアクション ID を渡します。  

```powershell
$thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson
```

スケジュールのしきい値アクションを編集するには、既存のアクション ID を Put メソッドに渡します。  要求の本体には、アクションの etag が含まれている必要があります。

```powershell
$thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson
```

#### <a name="severity"></a>重大度
Log Analytics を使用するとアラートをカテゴリに分類し、簡単に管理およびトリアージできます。 定義されているアラートの重大度は、情報、警告、重大です。 これらは次のように Azure Alerts の正規化された重大度スケールにマッピングされています。

|Log Analytics の重大度レベル  |Azure Alerts の重大度レベル  |
|---------|---------|
|`critical` |重大度 0|
|`warning` |重大度 1|
|`informational` | 重大度 2|

しきい値と重大度のみを含むアクションに対する応答の例を次に示します。 

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Severity": "critical",
   "Version": 1
}
```

スケジュールの新しいアクションを重大度で作成するには、Put メソッドに一意のアクション ID を渡します。  

```powershell
$thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson
```

スケジュールの重大度アクションを編集するには、既存のアクション ID を Put メソッドに渡します。  要求の本体には、アクションの etag が含まれている必要があります。

```powershell
$thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson
```

#### <a name="suppress"></a>Suppress
Log Analytics ベースのクエリ アラートは、しきい値に達するか超過するたびに発生します。 クエリに示されたロジックに基づいて、連続する間隔でアラートが生成され、その結果の通知が常に送信される可能性があります。 このようなシナリオを防ぐため、ユーザーは、指定した時間待機してからアラート ルールの 2 回目の通知が発生するように Log Analytics に指示する [抑制] オプションを設定することができます。 抑制が 30 分に設定したとします。まず最初のアラートが生成され、構成された通知が送信されます。 この場合、アラート ルールの通知が再び使用されるまで、30 分待機されます。 その間、アラート ルールは継続して実行されます。この期間にアラート ルールが何回生成されたかにかかわらず、指定した期間は Log Analytics によって通知のみが抑制されます。

Log Analytics アラート ルールの抑制プロパティは、 *[スロットリング]* 値と、*DurationInMinutes* 値を使用した抑制期間を使用して指定されます。

しきい値、重大度、および抑制プロパティのみを含むアクションに対する応答の例を次に示します

```json
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
   "Version": 1
}
```

スケジュールの新しいアクションを重大度で作成するには、Put メソッドに一意のアクション ID を渡します。  

```powershell
$AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson
```

スケジュールの重大度アクションを編集するには、既存のアクション ID を Put メソッドに渡します。  要求の本体には、アクションの etag が含まれている必要があります。

```powershell
$AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson
```

#### <a name="action-groups"></a>Action Groups
Azure のすべてのアラートは、アクションを管理する既定のメカニズムとして、アクション グループを使用します。 アクション グループを使用すると、一度に複数のアクションを指定し、そのアクション グループを Azure 全体にわたって複数のアラートに関連付けることができます。 繰り返し宣言することなく、同じアクションを繰り返し実行できます。 アクション グループは、電子メール、SMS、音声通話、ITSM Connection、Automation Runbook、Webhook URI など、複数のアクションに対応しています。 

アラートを Azure に拡張しているユーザーの場合、スケジュールにアクション グループの詳細がしきい値とともに渡され、アラートを作成できるようになっています。 アラートを作成する前に、電子メールの詳細、Webhook の URL、Runbook Automation の詳細、およびその他のアクションをアクション グループ内に定義する必要があります。Portal の [Azure Monitor からアクション グループ](./action-groups.md)を作成するか、[アクション グループ API](/rest/api/monitor/actiongroups) を使用できます。

アラートにアクション グループの関連付けを追加するには、アラートの定義にそのアクション グループの一意の Azure Resource Manager ID を指定します。 以下にサンプル図を示します。

```json
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
}
```

Put メソッドを一意のアクション ID とともに使用して、スケジュールの既存のアクション グループを関連付けます。  次に使用状況のサンプル図を示します。

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Put メソッドを既存のアクション ID とともに使用して、スケジュールに関連付けられているアクション グループを編集します。  要求の本体には、アクションの etag が含まれている必要があります。

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': { 'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'] } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

#### <a name="customize-actions"></a>Customize Actions
既定のアクションで、通知の標準のテンプレートとフォーマットに従います。 ただし、アクション グループによって制御されている場合でも、ユーザーは一部のアクションをカスタマイズできます。 現時点では、電子メールの件名と Webhook のペイロードをカスタマイズできます。

##### <a name="customize-e-mail-subject-for-action-group"></a>アクション グループの電子メールの件名をカスタマイズする
既定では、アラートの電子メールの件名は、`<WorkspaceName>` に関するアラートの通知 `<AlertName>` です。 ただしこれはカスタマイズできるため、言葉やタグを指定できます。これにより、受信トレイにフィルター規則を簡単に適用できます。 電子メールの件名の詳細のカスタマイズは、以下のサンプルに示すように、ActionGroup の詳細とともに送信する必要があります。

```json
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
}
```

Put メソッドを一意のアクション ID とともに使用して、スケジュールの既存のアクション グループをカスタマイズとともに関連付けます。  次に使用状況のサンプル図を示します。

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Put メソッドを既存のアクション ID とともに使用して、スケジュールに関連付けられているアクション グループを編集します。  要求の本体には、アクションの etag が含まれている必要があります。

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

##### <a name="customize-webhook-payload-for-action-group"></a>アクション グループの Webhook のペイロードをカスタマイズする
既定では、ログ分析のためにアクション グループを介して送信される Webhook の構造は固定されています。 ただし、サポートされている特定の変数を使用して、Webhook のエンドポイントの条件を満たすように、JSON のペイロードをカスタマイズできます。 詳細については、「[ログ アラート ルールの webhook アクション](./alerts-log-webhook.md)」を参照してください 

Webhook の詳細のカスタマイズは、ActionGroup の詳細とともに送信する必要があり、以下のサンプルに示すように、そのアクション グループ内に指定されたすべての Webhook URI に適用されます。

```json
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
```

Put メソッドを一意のアクション ID とともに使用して、スケジュールの既存のアクション グループをカスタマイズとともに関連付けます。  次に使用状況のサンプル図を示します。

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Put メソッドを既存のアクション ID とともに使用して、スケジュールに関連付けられているアクション グループを編集します。  要求の本体には、アクションの etag が含まれている必要があります。

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

## <a name="next-steps"></a>次のステップ

* Log Analytics で [ログ検索を実行するための REST API](../logs/log-query-overview.md) を使用します。
* [Azure Monitor でのログ アラート](./alerts-unified-log.md)について学習します
* [Azure Monitor でログ アラート ルールを作成、編集、または管理する](./alerts-log.md)方法