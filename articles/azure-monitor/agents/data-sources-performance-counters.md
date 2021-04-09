---
title: Azure Monitor で Log Analytics エージェントを使用して Windows と Linux のパフォーマンス データ ソースを収集する
description: Azure Monitor では、Windows および Linux のエージェントのパフォーマンスを分析するためにパフォーマンス カウンターが収集されます。  この記事では、Windows および Linux の両方のエージェントでのパフォーマンス カウンターの収集の構成方法、ワークスペースに格納されたそれらの詳細、および Azure Portal でのそれらの分析方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: f4bddc1666d1165d6a1e4c749fdbc96ede37747a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036779"
---
# <a name="collect-windows-and-linux-performance-data-sources-with-log-analytics-agent"></a>Log Analytics エージェントを使用して Windows と Linux のパフォーマンス データ ソースを収集する
Windows および Linux のパフォーマンス カウンターから、ハードウェア コンポーネント、オペレーティング システム、およびアプリケーションのパフォーマンスに関する情報が得られます。  Azure Monitor では、長期的な分析とレポートのためにパフォーマンス データを集計することに加えて、ほぼリアルタイム (NRT) 分析のために Log Analytics エージェントから頻繁な間隔でパフォーマンス カウンターを収集できます。

> [!IMPORTANT]
> この記事では、Azure Monitor で使用されるエージェントの 1 つである [Log Analytics エージェント](./log-analytics-agent.md)を使用してパフォーマンス データを収集する方法について説明します。 他のエージェントは異なるデータを収集し、異なる方法で構成されます。 使用可能なエージェントとそれらが収集できるデータの一覧については、「[Azure Monitor エージェントの概要](../agents/agents-overview.md)」を参照してください。

![パフォーマンス カウンター](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>パフォーマンス カウンターの構成
Log Analytics ワークスペースの [[エージェント構成] メニュー](../agents/agent-data-sources.md#configuring-data-sources)からパフォーマンス カウンターを構成します。

新しいワークスペースの Windows または Linux のパフォーマンス カウンターを初めて構成する場合、いくつかの一般的なカウンターをすばやく作成するためのオプションが表示されます。  それぞれのオプションの横には、チェック ボックスが表示されます。  最初に作成するカウンターがオンになっていることを確認し、 **[Add the selected performance counters (選択されたパフォーマンス カウンターを追加する)]** をクリックします。

Windows のパフォーマンス カウンターの場合、パフォーマンス カウンターごとに特定のインスタンスを選択できます。 Linux のパフォーマンス カウンターの場合、各カウンターに対して選択したインスタンスが、そのすべての子カウンターに適用されます。 次の表は、Linux と Windows の両方のパフォーマンス カウンターで利用できる共通のインスタンスを示しています。

| インスタンス名 | 説明 |
| --- | --- |
| \_Total |すべてのインスタンスの合計 |
| \* |すべてのインスタンス |
| (/&#124;/var) |/ または /var という名前のインスタンスと一致します。 |

### <a name="windows-performance-counters"></a>Windows パフォーマンス カウンター

[![Windows パフォーマンス カウンターの構成](media/data-sources-performance-counters/configure-windows.png)](media/data-sources-performance-counters/configure-windows.png#lightbox)

収集する新しい Windows パフォーマンス カウンターを追加するには、次の手順を実行します。 V2 Windows パフォーマンス カウンターがサポートされていないことにご注意ください。

1. **[パフォーマンス カウンターの追加]** をクリックします。
2. *<オブジェクト (インスタンス)>\<カウンター>* の形式で、テキスト ボックスにカウンターの名前を入力します。  入力を開始すると、入力内容に一致する一般的なカウンターの一覧が表示されます。  一覧からカウンターを選択するか、または独自の名前を入力することができます。  *<オブジェクト>\<カウンター>* を指定して、特定のカウンターのすべてのインスタンスを返すこともできます。  

    名前付きインスタンスから SQL Server パフォーマンス カウンターを収集するとき、すべての名前付きインスタンス カウンターの名前が *MSSQL$* から始まり、その後ろにインスタンスの名前が付きます。  たとえば、名前付き SQL インスタンス INST2 のデータベース パフォーマンス オブジェクトからログ キャッシュ ヒット率カウンターを収集するには、`MSSQL$INST2:Databases(*)\Log Cache Hit Ratio` と指定します。

4. カウンターを追加すると、その **[サンプルの間隔]** には既定値の 10 秒が使用されます。  収集されたパフォーマンス データのストレージ要件を削減する場合は、この値を最大 1800 秒 (30 分) まで高く変更できます。
5. カウンターの追加を完了したら、画面の上部にある **[適用]** ボタンをクリックして、構成を保存します。

### <a name="linux-performance-counters"></a>Linux パフォーマンス カウンター

[![Linux パフォーマンス カウンターの構成](media/data-sources-performance-counters/configure-linux.png)](media/data-sources-performance-counters/configure-linux.png#lightbox)

収集する新しい Linux パフォーマンス カウンターを追加するには、次の手順を実行します。

1. **[パフォーマンス カウンターの追加]** をクリックします。
1. *<オブジェクト (インスタンス)>\<カウンター>* の形式で、テキスト ボックスにカウンターの名前を入力します。  入力を開始すると、入力内容に一致する一般的なカウンターの一覧が表示されます。  一覧からカウンターを選択するか、または独自の名前を入力することができます。  
1. オブジェクトのすべてのカウンターは、同じ **[サンプルの間隔]** を使用します。  既定値は 10 秒です。  収集されたパフォーマンス データのストレージ要件を削減したい場合は、1,800 秒 (30 分) を上限としてこの値を増やしてください。
1. カウンターの追加を完了したら、画面の上部にある **[適用]** ボタンをクリックして、構成を保存します。

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>構成ファイルで Linux のパフォーマンス カウンターを構成する
Azure Portal を使用して Linux のパフォーマンス カウンターを構成する代わりに、Linux エージェントで構成ファイルを編集することもできます。  収集するパフォーマンス メトリックは、 **/etc/opt/microsoft/omsagent/\<workspace id\>/conf/omsagent.conf** の構成によって制御されます。

収集するパフォーマンス メトリックの各オブジェクト (カテゴリ) は、構成ファイルの中で単一の `<source>` 要素として定義する必要があります。 次の構文形式に従って記述してください。

```xml
<source>
    type oms_omi  
    object_name "Processor"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>
```


この要素のパラメーターを次の表に示します。

| パラメーター | 説明 |
|:--|:--|
| object\_name | コレクションのオブジェクト名。 |
| instance\_regex |  収集するインスタンスを定義する *正規表現*。 すべてのインスタンスは、 `.*` という値で指定します。 \_Total インスタンスのみを対象にプロセッサ メトリックを収集するには、`_Total` を指定します。 crond または sshd のインスタンスのみを対象にプロセス メトリックを収集するには、「 `(crond\|sshd)`」と指定します。 |
| counter\_name\_regex | 収集する (オブジェクトの) カウンターを定義する *正規表現*。 オブジェクトのすべてのカウンターを収集するには、「 `.*`」と指定します。 メモリ オブジェクトを対象にスワップ領域カウンターを収集するには、たとえば `.+Swap.+` のように指定できます。 |
| interval | オブジェクトのカウンターを収集する頻度。 |


次の表は、構成ファイルで指定できるオブジェクトとカウンターを一覧表示しています。  「[Collect performance counters for Linux applications in Azure Monitor (Azure Monitor で Linux アプリケーションのパフォーマンス カウンターを収集する)](data-sources-linux-applications.md)」に記載されているとおり、特定のアプリケーションで使用できる追加のカウンターがあります。

| オブジェクト名 | カウンター名 |
|:--|:--|
| 論理ディスク | % Free Inodes |
| 論理ディスク | % Free Space |
| 論理ディスク | % Used Inodes |
| 論理ディスク | % Used Space |
| 論理ディスク | Disk Read Bytes/sec |
| 論理ディスク | Disk Reads/sec |
| 論理ディスク | Disk Transfers/sec |
| 論理ディスク | Disk Write Bytes/sec |
| 論理ディスク | Disk Writes/sec |
| 論理ディスク | Free Megabytes |
| 論理ディスク | Logical Disk Bytes/sec |
| メモリ | % Available Memory |
| メモリ | % Available Swap Space |
| メモリ | % Used Memory |
| メモリ | % Used Swap Space |
| メモリ | Available MBytes Memory |
| メモリ | Available MBytes Swap |
| メモリ | Page Reads/sec |
| メモリ | Page Writes/sec |
| メモリ | Pages/sec |
| メモリ | Used MBytes Swap Space |
| メモリ | Used Memory MBytes |
| ネットワーク | Total Bytes Transmitted |
| ネットワーク | Total Bytes Received |
| ネットワーク | Total Bytes |
| ネットワーク | Total Packets Transmitted |
| ネットワーク | Total Packets Received |
| ネットワーク | Total Rx Errors |
| ネットワーク | Total Tx Errors |
| ネットワーク | Total Collisions |
| 物理ディスク | Avg.Disk sec/Read |
| 物理ディスク | Avg.Disk sec/Transfer |
| 物理ディスク | Avg.Disk sec/Write |
| 物理ディスク | Physical Disk Bytes/sec |
| Process | Pct Privileged Time |
| Process | Pct User Time |
| Process | Used Memory kBytes |
| Process | Virtual Shared Memory |
| プロセッサ | % DPC Time |
| プロセッサ | % Idle Time |
| プロセッサ | % Interrupt Time |
| プロセッサ | % IO Wait Time |
| プロセッサ | % Nice Time |
| プロセッサ | % Privileged Time |
| プロセッサ | % Processor Time |
| プロセッサ | % User Time |
| システム | Free Physical Memory |
| システム | Free Space in Paging Files |
| システム | Free Virtual Memory |
| システム | 処理 |
| システム | Size Stored In Paging Files |
| システム | Uptime |
| システム | ユーザー |


パフォーマンス メトリックの既定の構成を次に示します。

```xml
<source>
    type oms_omi
    object_name "Physical Disk"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 5m
</source>

<source>
    type oms_omi
    object_name "Logical Disk"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 5m
</source>

<source>
    type oms_omi
    object_name "Processor"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>

<source>
    type oms_omi
    object_name "Memory"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>
```

## <a name="data-collection"></a>データ コレクション
Azure Monitor は、カウンターがインストールされているすべてのエージェントについて、指定されたサンプル間隔ですべての指定されたパフォーマンス カウンターを収集します。  データは集計されず、Log Analytics ワークスペースで指定した期間、すべてのログ クエリ ビューで生データを利用できます。

## <a name="performance-record-properties"></a>パフォーマンス レコードのプロパティ
パフォーマンス レコードには、 **Perf** の型と、次の表に示すプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| Computer |イベントが収集されたコンピューター。 |
| CounterName |パフォーマンス カウンターの名前 |
| CounterPath |\\\\\<Computer>\\オブジェクト (インスタンス)\\カウンターの形式に従う、カウンターの完全なパス。 |
| CounterValue |カウンターの数値。 |
| InstanceName |イベント インスタンスの名前。  インスタンスがない場合は空白です。 |
| ObjectName |パフォーマンス オブジェクトの名前 |
| SourceSystem |データが収集されたエージェントの種類。 <br><br>OpsManager – Windows エージェント、直接接続または SCOM <br> Linux – すべての Linux エージェント  <br> AzureStorage – Azure Diagnostics |
| TimeGenerated |データがサンプリングされた日付と時刻。 |

## <a name="sizing-estimates"></a>サイズ見積もり
 10 秒間隔での特定のカウンターの収集量の大まかな見積もり値は、インスタンスごとに 1 日あたり約 1 MB です。  次の式で、特定のカウンターのストレージ要件を見積もることができます。

> 1 MB x (カウンターの数) x (エージェントの数) x (インスタンスの数)

## <a name="log-queries-with-performance-records"></a>パフォーマンス レコードに対するログ クエリ
次の表は、パフォーマンス レコードを取得するログ クエリのさまざまな例をまとめたものです。

| クエリ | 説明 |
|:--- |:--- |
| Perf |すべてのパフォーマンス データ |
| Perf &#124; where Computer == "MyComputer" |特定のコンピューターからのすべてのパフォーマンス データ |
| Perf &#124; where CounterName == "Current Disk Queue Length" |特定のカウンターに関するすべてのパフォーマンス データ |
| Perf &#124; where ObjectName == "Processor" and CounterName == "% Processor Time" and InstanceName == "_Total" &#124; summarize AVGCPU = avg(CounterValue) by Computer |コンピューター全体の平均 CPU 使用率 |
| Perf &#124; where CounterName == "% Processor Time" &#124; summarize AggregatedValue = max(CounterValue) by Computer |コンピューター全体の最大 CPU 使用率 |
| Perf &#124; where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and Computer == "MyComputerName" &#124; summarize AggregatedValue = avg(CounterValue) by InstanceName |特定のコンピューターのインスタンス全体における現在のディスク キューの長さの平均 |
| Perf &#124; where CounterName == "Disk Transfers/sec" &#124; summarize AggregatedValue = percentile(CounterValue, 95) by Computer |コンピューター全体のディスク転送数/秒の 95 パーセンタイル |
| Perf &#124; where CounterName == "% Processor Time" and InstanceName == "_Total" &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), Computer |全コンピューターの CPU 使用率の平均値 (1 時間ごと) |
| Perf &#124; where Computer == "MyComputer" and CounterName startswith_cs "%" and InstanceName == "_Total" &#124; summarize AggregatedValue = percentile(CounterValue, 70) by bin(TimeGenerated, 1h), CounterName | 特定のコンピューターの各パーセント (%) カウンターの 70 パーセンタイル (1 時間ごと) |
| Perf &#124; where CounterName == "% Processor Time" and InstanceName == "_Total" and Computer == "MyComputer" &#124; summarize ["min(CounterValue)"] = min(CounterValue), ["avg(CounterValue)"] = avg(CounterValue), ["percentile75(CounterValue)"] = percentile(CounterValue, 75), ["max(CounterValue)"] = max(CounterValue) by bin(TimeGenerated, 1h), Computer |特定のコンピューターの CPU 使用率の平均、最小、最大、75 パーセンタイル (1 時間ごと) |
| Perf &#124; where ObjectName == "MSSQL$INST2:Databases" and InstanceName == "master" | 名前付き SQL インスタンス INST2 のマスター データベースのデータベース パフォーマンス オブジェクトのすべてのパフォーマンス データ。  




## <a name="next-steps"></a>次のステップ
* MySQL および Apache HTTP Server を含む [Linux アプリケーションからパフォーマンス カウンターを収集します](data-sources-linux-applications.md)。
* [ログ クエリ](../logs/log-query-overview.md)について学習し、データ ソースとソリューションから収集されたデータを分析します。  
* 詳細な視覚化および分析を行うために、収集されたデータを [Power BI](../visualize/powerbi.md) にエクスポートします。