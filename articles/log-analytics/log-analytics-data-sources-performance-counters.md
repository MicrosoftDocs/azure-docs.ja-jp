---
title: "Azure Log Analytics でのパフォーマンス カウンターの収集と分析 | Microsoft Docs"
description: "Log Analytics では、Windows および Linux のエージェントのパフォーマンスを分析するためにパフォーマンス カウンターが収集されます。  この記事では、Windows および Linux の両方のエージェントでのパフォーマンス カウンターの収集の構成方法、OMS リポジトリに格納されたそれらの詳細、および OMS ポータルでのそれらの分析方法について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 20e145e4-2ace-4cd9-b252-71fb4f94099e
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 853177202e4724546242d4e8d1b41b456a444eeb
ms.openlocfilehash: 2381b7e9ab4514d6668569c2a58d108af5008a7e
ms.lasthandoff: 02/27/2017


---
# <a name="windows-and-linux-performance-data-sources-in-log-analytics"></a>Log Analytics での Windows および Linux のパフォーマンス データ ソース
Windows および Linux のパフォーマンス カウンターから、ハードウェア コンポーネント、オペレーティング システム、およびアプリケーションのパフォーマンスに関する情報が得られます。  Log Analytics は、長期的な分析とレポートのためにパフォーマンス データを集計することに加えて、ほぼリアルタイム (NRT) 分析のために頻繁な間隔でパフォーマンス カウンターを収集することができます。

![パフォーマンス カウンター](media/log-analytics-data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>パフォーマンス カウンターの構成
パフォーマンス カウンターは、[Log Analytics の [設定] の[データ] メニュー](log-analytics-data-sources.md#configuring-data-sources)から構成します。

新しい OMS ワークスペースの Windows または Linux のパフォーマンス カウンターを初めて構成する場合、いくつかの一般的なカウンターをすばやく作成するためのオプションが表示されます。  それぞれのオプションの横には、チェック ボックスが表示されます。  最初に作成するカウンターがオンになっていることを確認し、 **[Add the selected performance counters (選択されたパフォーマンス カウンターを追加する)]**をクリックします。

![Windows パフォーマンス カウンターの構成](media/log-analytics-data-sources-performance-counters/configure-windows.png)

収集する新しい Windows パフォーマンス カウンターを追加するには、次の手順を実行します。

1. *<オブジェクト (インスタンス)>\<カウンター>* の形式で、テキスト ボックスにカウンターの名前を入力します。  入力を開始すると、入力内容に一致する一般的なカウンターの一覧が表示されます。  一覧からカウンターを選択するか、または独自の名前を入力することができます。  *<オブジェクト>\<カウンター>* を指定して、特定のカウンターのすべてのインスタンスを返すこともできます。
2. **+** をクリックするか、または **Enter** キーを押して、一覧にカウンターを追加します。
3. カウンターを追加すると、その **[サンプルの間隔]** には既定値の 10 秒が使用されます。  収集されたパフォーマンス データのストレージ要件を削減する場合は、この値を最大 1800 秒 (30 分) まで高く変更できます。
4. カウンターの追加を完了したら、画面の上部にある **[保存]** ボタンをクリックして、構成を保存します。

![Linux パフォーマンス カウンターの構成](media/log-analytics-data-sources-performance-counters/configure-linux.png)

収集する新しい Linux パフォーマンス カウンターを追加するには、次の手順を実行します。

1. 既定では、すべての構成変更はすべてのエージェントに自動的にプッシュされます。  Linux エージェントの場合、構成ファイルが Fluentd データ コレクターに送信されます。  各 Linux エージェントでこのファイルを手動で変更する場合、 *[Apply below configuration to my Linux machines (Linux コンピューターに以下の構成を適用する)]*チェック ボックスをオフにします。
2. *<オブジェクト (インスタンス)>\<カウンター>* の形式で、テキスト ボックスにカウンターの名前を入力します。  入力を開始すると、入力内容に一致する一般的なカウンターの一覧が表示されます。  一覧からカウンターを選択するか、または独自の名前を入力することができます。  
3. **+** をクリックするか、または **Enter** キーを押して、オブジェクトのその他のカウンターの一覧にカウンターを追加します。
4. オブジェクトのすべてのカウンターは、同じ **[サンプルの間隔]** を使用します。  既定値は 10 秒です。  収集されたパフォーマンス データのストレージ要件を削減したい場合は、1,800 秒 (30 分) を上限としてこの値を増やしてください。
5. カウンターの追加を完了したら、画面の上部にある **[保存]** ボタンをクリックして、構成を保存します。

## <a name="data-collection"></a>データ収集
Log Analytics は、カウンターがインストールされているすべてのエージェントについて、指定されたサンプル間隔ですべての指定されたパフォーマンス カウンターを収集します。  データは集計されず、生データが、OMS サブスクリプションで指定した期間、すべてのログ検索ビューで利用可能です。

## <a name="performance-record-properties"></a>パフォーマンス レコードのプロパティ
パフォーマンス レコードには、 **Perf** の型と、次の表に示すプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| コンピューター |イベントが収集されたコンピューター。 |
| CounterName |パフォーマンス カウンターの名前 |
| CounterPath |\\\\\<コンピューター>\\オブジェクト(インスタンス)\\カウンターの形式のカウンターの完全パス。 |
| CounterValue |カウンターの数値。 |
| InstanceName |イベント インスタンスの名前。  インスタンスがない場合は空白です。 |
| ObjectName |パフォーマンス オブジェクトの名前 |
| SourceSystem |データが収集されたエージェントの種類。 <br> OpsManager – Windows エージェント、直接接続または SCOM <br> Linux – すべての Linux エージェント  <br> AzureStorage – Azure 診断 |
| TimeGenerated |データがサンプリングされた日付と時刻。 |

## <a name="sizing-estimates"></a>サイズ見積もり
 10 秒間隔での特定のカウンターの収集量の大まかな見積もり値は、インスタンスごとに 1 日あたり約 1 MB です。  次の式で、特定のカウンターのストレージ要件を見積もることができます。

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-searches-with-performance-records"></a>パフォーマンス レコードでのログ検索
次の表は、パフォーマンス レコードを取得するログ検索のさまざまな例をまとめたものです。

| クエリ | 説明 |
|:--- |:--- |
| Type=Perf |すべてのパフォーマンス データ |
| Type=Perf Computer="MyComputer" |特定のコンピューターからのすべてのパフォーマンス データ |
| Type=Perf CounterName="Current Disk Queue Length" |特定のカウンターに関するすべてのパフォーマンス データ |
| Type=Perf (ObjectName=Processor) CounterName="% Processor Time" InstanceName=_Total &#124; measure Avg(Average) as AVGCPU  by Computer |コンピューター全体の平均 CPU 使用率 |
| Type=Perf (CounterName="% Processor Time") &#124;  measure max(Max) by Computer |コンピューター全体の最大 CPU 使用率 |
| Type=Perf ObjectName=LogicalDisk CounterName="Current Disk Queue Length" Computer="MyComputerName" &#124; measure Avg(Average) by InstanceName |特定のコンピューターのインスタンス全体における現在のディスク キューの長さの平均 |
| Type=Perf CounterName="DiskTransfers/sec" &#124; measure percentile95(Average) by Computer |コンピューター全体のディスク転送数/秒の&95; パーセンタイル |
| Type=Perf CounterName="% Processor Time" InstanceName="_Total"  &#124; measure avg(CounterValue) by Computer Interval 1HOUR |全コンピューターの CPU 使用率の平均値 (1 時間ごと) |
| Type=Perf Computer="MyComputer" CounterName=%* InstanceName=_Total &#124; measure percentile70(CounterValue) by CounterName Interval 1HOUR |特定のコンピューターの各パーセント (%) カウンターの 70 パーセンタイル (1 時間ごと) |
| Type=Perf CounterName="% Processor Time" InstanceName="_Total"  (Computer="MyComputer") &#124; measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR |特定のコンピューターの CPU 使用率の平均、最小、最大、75 パーセンタイル (1 時間ごと) |

## <a name="viewing-performance-data"></a>パフォーマンス データの表示
パフォーマンス データのログ検索を実行すると、既定で **[ログ]** ビューが表示されます。  グラフィカルな形式でデータを表示するには、 **[メトリック]**をクリックします。  詳細なグラフィック表示については、カウンターの横にある **+** をクリックします。  

![折りたたまれた [メトリック] ビュー](media/log-analytics-data-sources-performance-counters/metricscollapsed.png)

ログ検索でパフォーマンス データを集計する方法については、「[On-demand metric aggregation and visualization in OMS (OMS におけるオンデマンドのメトリック集計と視覚化)](http://blogs.technet.microsoft.com/msoms/2016/02/26/on-demand-metric-aggregation-and-visualization-in-oms/)」を参照してください。

## <a name="next-steps"></a>次のステップ
* [ログ検索](log-analytics-log-searches.md) について学習し、データ ソースとソリューションから収集されたデータを分析します。  
* 詳細な視覚化および分析を行うために、収集されたデータを [Power BI](log-analytics-powerbi.md) にエクスポートします。

