---
title: Azure Monitor ログ レコード内の標準列 | Microsoft Docs
description: Azure Monitor ログ内の複数のデータ型に共通する列について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/25/2021
ms.openlocfilehash: 5b906bdbd07d59d2acc88f6b30f0db6b6cbc961a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103562248"
---
# <a name="standard-columns-in-azure-monitor-logs"></a>Azure Monitor ログ内の標準列
Azure Monitor ログ内のデータは、[Log Analytics ワークスペースまたは Application Insights アプリケーションのどちらかに一連のレコードとして格納され](../logs/data-platform-logs.md)、各レコードが固有の列のセットを備えた特定のデータ型を持っています。 多くのデータ型には、複数の型にわたって共通の標準列があります。 この記事では、これらの列について説明し、それらの列をクエリで使用する方法の例を示します。

Application Insights のワークスペース ベースのアプリケーションではデータを Log Analytics ワークスペースに格納し、ワークスペース内の他のテーブルと同じ標準列を使用します。 クラシック アプリケーションではデータを個別に格納し、この記事で指定されている別の標準列を使用します。

> [!NOTE]
> 標準列のいくつかは Log Analytics のスキーマ ビューや IntelliSense には表示されず、出力で列を明示的に指定しない限り、クエリの結果にも表示されません。
> 

## <a name="tenantid"></a>TenantId
**TenantId** 列には、Log Analytics ワークスペースのワークスペース ID が保持されます。

## <a name="timegenerated-and-timestamp"></a>TimeGenerated と timestamp
**TimeGenerated** (Log Analytics ワークスペース) および **timestamp** (Application Insights アプリケーション) 列には、そのレコードがデータ ソースによって作成された日付と時刻が含まれています。 詳細については、「[Azure Monitor でのログ データ インジェスト時間](../logs/data-ingestion-time.md)」を参照してください。

**TimeGenerated** と **timestamp** は、時間でフィルター処理または集計するために使用する共通の列を提供します。 Azure portal でビューまたはダッシュボードの時間範囲を選択した場合は、TimeGenerated または timestamp を使用して結果がフィルター処理されます。 

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
**\_TimeReceived** 列には、そのレコードが Azure クラウド内の Azure Monitor インジェスト ポイントによって受信された日付と時刻が含まれています。 データ ソースとクラウドとの間における待ち時間の問題の特定に役立てることができます。 たとえば、エージェントから送信されているデータの遅延を引き起こしているネットワークの問題が考えられます。 詳細については、「[Azure Monitor でのログ データ インジェスト時間](../logs/data-ingestion-time.md)」を参照してください。

> [!NOTE]
> **\_TimeReceived** 列は、使用されるたびに計算されます。 このプロセスは、リソースを集中的に消費します。 それを使用して大量のレコードをフィルター処理しないでください。 この関数を繰り返し使用すると、クエリの実行時間が長くなる可能性があります。


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
**Type** (Log Analytics ワークスペース) および **itemType** (Application Insights アプリケーション) 列には、そのレコードの取得元のテーブルの名前が保持されます。これは、レコードの種類と考えることもできます。 この列は、複数のテーブルからのレコードを結合するクエリ (`search` 演算子を使用するクエリなど) で、異なる種類のレコードを区別するために役立ちます。 場所によっては、**Type** の代わりに **$table** を使用できます。

### <a name="examples"></a>例
次のクエリは、過去 1 時間に収集された種類ごとのレコード数を返します。

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
**\_ItemId** 列には、そのレコードの一意識別子が保持されます。


## <a name="_resourceid"></a>\_ResourceId
**\_ResourceId** 列には、そのレコードが関連付けられているリソースの一意識別子が保持されます。 これにより、クエリのスコープを特定のリソースからのレコードのみに設定したり、複数のテーブルにまたがる関連データを結合したりために使用する標準列が提供されます。

Azure リソースの場合、 **_ResourceId** の値は [Azure リソース ID の URL](../../azure-resource-manager/templates/template-functions-resource.md) です。 この列は、[Azure Arc](../../azure-arc/overview.md) リソースを含む Azure リソース、またはインジェスト時にリソース ID が示されたカスタム ログに限定されます。

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

次のクエリは、 **_ResourceId** を解析し、Azure リソース グループごとに課金対象のデータ ボリュームを集計します。

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by resourceGroup | sort by Bytes nulls last 
```

複数の種類のデータにわたるスキャンは、実行コストが高いため、これらの `union withsource = tt *` クエリは多用しないようにします。

\_ResourceId 列を解析して抽出するのではなく、\_SubscriptionId 列を使用する方が効率的です。

## <a name="_subscriptionid"></a>\_SubscriptionId
**\_SubscriptionId** 列には、そのレコードが関連付けられているリソースのサブスクリプション ID が保持されます。 これにより、クエリのスコープを特定のサブスクリプションからのレコードのみに設定したり、異なるサブスクリプションを比較するために使用する標準列が提供されます。

Azure リソースの場合、 **__SubscriptionId** の値は [Azure リソース ID の URL](../../azure-resource-manager/templates/template-functions-resource.md) のサブスクリプションの部分です。 この列は、[Azure Arc](../../azure-arc/overview.md) リソースを含む Azure リソース、またはインジェスト時にリソース ID が示されたカスタム ログに限定されます。

> [!NOTE]
> 一部のデータ型には、Azure サブスクリプション ID を含むフィールドが既に割り当てられています。 これらのフィールドは下位互換性のために保持されていますが、\_SubscriptionId 列を使用して相互相関を実行する方が一貫性があるため、この方法をお勧めします。
### <a name="examples"></a>例
次のクエリを使用すると、特定のサブスクリプションのコンピューターのパフォーマンス データを調べます。 

```Kusto
Perf 
| where TimeGenerated > ago(24h) and CounterName == "memoryAllocatableBytes"
| where _SubscriptionId == "57366bcb3-7fde-4caf-8629-41dc15e3b352"
| summarize avgMemoryAllocatableBytes = avg(CounterValue) by Computer
```

次のクエリは、 **_ResourceId** を解析し、Azure サブスクリプションごとに課金対象のデータ ボリュームを集計します。

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _SubscriptionId | sort by Bytes nulls last 
```

複数の種類のデータにわたるスキャンは、実行コストが高いため、これらの `union withsource = tt *` クエリは多用しないようにします。


## <a name="_isbillable"></a>\_IsBillable
**\_IsBillable** 列では、取り込まれたデータが課金対象かどうかを指定します。 **\_IsBillable** が `false` のデータは無料で収集され、Azure アカウントには課金されません。

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
**\_BilledSize** 列では、 **\_IsBillable** が true である場合に Azure アカウントに課金されるデータのサイズ (バイト単位) を指定します。


### <a name="examples"></a>使用例
取り込まれた課金対象イベントのサイズをコンピューターごとに表示するには、サイズ (バイト単位) を指定する `_BilledSize` 列を使用します。

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

取り込まれた課金対象イベントのサイズをサブスクリプションごとに表示するには、次のクエリを使用します。

```Kusto
union withsource=table * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId | sort by Bytes nulls last 
```

取り込まれた課金対象イベントのサイズをリソース グループごとに表示するには、次のクエリを使用します。

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

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

- [Azure Monitor ログ データの格納](./log-query-overview.md)方法の詳細を確認する。
- [ログ クエリの作成](./get-started-queries.md)に関するレッスンを参照する。
- [ログ クエリでのテーブルの結合](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins)に関するレッスンを参照する。
