---
title: Azure Monitor ログ レコードの標準プロパティ | Microsoft Docs
description: Azure Monitor ログの複数のデータ型に共通するプロパティについて説明します。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2020
ms.openlocfilehash: 9c7e6733a9149008f6ed98ae7a7e0103f92f79f5
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325272"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Azure Monitor ログ レコードの標準プロパティ
Azure Monitor ログ内のデータは、[Log Analytics ワークスペースまたは Application Insights アプリケーションのいずれかにレコード セットとして格納され](../log-query/logs-structure.md)、それぞれが独自のプロパティ セットを備えた特定のデータ型を持っています。 多くのデータ型には、複数の型にわたって共通の標準プロパティがあります。 この記事では、これらのプロパティについて説明し、プロパティをクエリで使用する方法の例を示します。

> [!IMPORTANT]
> APM 2.1 を使用している場合、Application Insights アプリケーションは他のすべてのログ データと共に Log Analytics ワークスペースに格納されます。 テーブルは名前が変更されて再構成されていますが、テーブル内の情報は Application Insights アプリケーションのテーブルと同じです。 これらの新しいテーブルの標準プロパティは、Log Analytics ワークスペース内の他のテーブルと同じです。

> [!NOTE]
> 標準プロパティのいくつかは、Log Analytics のスキーマ ビューや IntelliSense に表示されず、また、出力でプロパティを明示的に指定しない限り、クエリ結果にも表示されません。

## <a name="timegenerated-and-timestamp"></a>TimeGenerated と timestamp
**TimeGenerated** プロパティ (Log Analytics ワークスペース) と **timestamp** プロパティ (Application Insights アプリケーション) には、データ ソースによってレコードが作成された日時が含まれています。 詳細については、「[Azure Monitor でのログ データ インジェスト時間](data-ingestion-time.md)」を参照してください。

**TimeGenerated** と **timestamp** は、時間でフィルター処理または集計するために使用する一般的なプロパティを提供します。 Azure portal でビューまたはダッシュボードの時間範囲を選択した場合は、TimeGenerated または timestamp を使用して結果がフィルター処理されます。 

### <a name="examples"></a>例

次のクエリは、前の週の各日に作成されたエラー イベントの数を返します。

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

次のクエリでは、前の週の各日に作成された例外の数が返されます。

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TimeReceived
**\_TimeReceived** プロパティには、Azure クラウドにある Azure Monitor のインジェスト ポイントでレコードが受信された日時が格納されます。 データ ソースとクラウドとの間における待ち時間の問題の特定に役立てることができます。 たとえば、エージェントから送信されているデータの遅延を引き起こしているネットワークの問題が考えられます。 詳細については、「[Azure Monitor でのログ データ インジェスト時間](data-ingestion-time.md)」を参照してください。

次のクエリは、エージェントからのイベント レコードについて、1 時間ごとの平均待ち時間を返します。 これには、エージェントからクラウドまでの時間と、レコードがログ クエリで利用できるようになるまでの合計時間が含まれます。

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Type と itemType
**Type** プロパティ (Log Analytics ワークスペース) と **itemType** プロパティ (Application Insights アプリケーション) では、レコードの取得元であり、レコード型と考えることもできるテーブルの名前が保持されます。 このプロパティは、複数のテーブルからのレコードを結合するクエリ (`search` オペレーターを使用するクエリなど) で、異なる種類のレコードを区別するために役立ちます。 場所によっては、**Type** の代わりに **$table** を使用できます。

### <a name="examples"></a>例
次のクエリは、過去 1 時間に収集された種類ごとのレコード数を返します。

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
**\_ItemId** プロパティは、レコードの一意識別子を保持します。


## <a name="_resourceid"></a>\_ResourceId
**\_ResourceId** プロパティは、レコードが関連付けられているリソースの一意識別子を保持します。 これにより、ご自分のクエリを特定のリソースからのレコードのみに範囲を絞り込んだり、複数のテーブルにわたって関連するデータを結合したりするために使用する標準プロパティが提供されます。

Azure リソースの場合、 **_ResourceId** の値は [Azure リソース ID の URL](../../azure-resource-manager/templates/template-functions-resource.md) です。 現在、プロパティは Azure リソースに制限されていますが、オンプレミスのコンピューターなど、Azure 外のリソースに拡張される予定です。

> [!NOTE]
> 一部のデータ型には、Azure リソース ID か、または少なくともその一部 (サブスクリプション ID など) を含むフィールドが既に割り当てられています。 これらのフィールドは下位互換性のために保持されていますが、_ResourceId を使用して相互相関を実行する方が一貫性があるため、この方法をお勧めします。

### <a name="examples"></a>例
次のクエリは、各コンピューターのパフォーマンスとイベント データを結合します。 ID が _101_ でプロセッサ使用率が 50% を超えるすべてのイベントが表示されます。

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

次のクエリは、_AzureActivity_ レコードを _SecurityEvent_ レコードに結合します。 これらのコンピューターにログインしたユーザーを含むアクティビティ操作がすべて表示されます。

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

次のクエリは、 **_ResourceId** を解析し、Azure サブスクリプションごとに課金対象のデータ ボリュームを集計します。

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

複数の種類のデータにわたるスキャンは、実行コストが高いため、これらの `union withsource = tt *` クエリは多用しないようにします。

## <a name="_isbillable"></a>\_IsBillable
**\_IsBillable** プロパティでは、取り込まれたデータが課金対象かどうかを指定します。 **\_IsBillable** が `false` のデータは無料で収集され、Azure アカウントには課金されません。

### <a name="examples"></a>例
課金対象のデータ型を送信しているコンピューターの一覧を取得するには、次のクエリを使用します。

> [!NOTE]
> 複数の種類のデータにわたるスキャンは実行コストが高いため、`union withsource = tt *` を含むクエリの使用は控えめにしてください。 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

これを拡張して、課金対象のデータ型を送信している 1 時間あたりのコンピューターの数を返すことができます。

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
**\_BilledSize** プロパティでは、 **\_IsBillable** が true の場合に Azure アカウントに課金されるデータのサイズをバイト単位で指定します。


### <a name="examples"></a>例
コンピューターごとに、取り込まれた課金可能イベントのサイズを表示するには `_BilledSize` プロパティを使用します。サイズはバイト単位で示されます。

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

取り込まれた課金対象イベントのサイズをサブスクリプションごとに表示するには、次のクエリを使用します。

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

取り込まれた課金対象イベントのサイズをリソース グループごとに表示するには、次のクエリを使用します。

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


コンピューターごとに、取り込まれたイベントの数を表示するには、次のクエリを使用します

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

コンピューターごとに、取り込まれた課金対象イベントの数を表示するには、次のクエリを使用します 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

特定のコンピューターからの課金対象データ型の数を表示するには、次のクエリを使用します。

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>次のステップ

- [Azure Monitor ログ データの格納](../log-query/log-query-overview.md)方法の詳細を確認する。
- [ログ クエリの作成](../log-query/get-started-queries.md)に関するレッスンを参照する。
- [ログ クエリでのテーブルの結合](../log-query/joins.md)に関するレッスンを参照する。

