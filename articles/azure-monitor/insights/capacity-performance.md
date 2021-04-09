---
title: Azure Monitor の Capacity and Performance ソリューション | Microsoft Docs
description: Monitor の Capacity and Performance ソリューションは、Hyper-V サーバーの容量の把握に役立ちます。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: aa08b9f5db5e6371bcd20b473a0c755db3199e7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101704295"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>Capacity and Performance ソリューション (非推奨) を使って Hyper-V 仮想マシンの容量を計画する

![キャパシティとパフォーマンスのシンボル](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> Capacity and Performance ソリューションは非推奨になりました。  このソリューションを既にインストール済みのユーザーは引き続き使用できますが、新しいワークスペースに Capacity and Performance を追加することはできません。

Monitor の Capacity and Performance ソリューションを使用すると、Hyper-V サーバーの容量の把握に役立ちます。 このソリューションでは、Hyper-V ホストとそのホストで実行中の VM に関して (CPU、メモリ、およびディスクの) 全体としての使用状況が表示されるため、Hyper-V 環境の状況を把握できます。 すべてのホストとそのホストで実行している VM について、CPU、メモリ、ディスクに関するメトリックが収集されます。

このソリューションでは次のことが可能です。

-   CPU とメモリの使用率が上位のホストと下位のホストをそれぞれ表示する
-   CPU とメモリの使用率が上位の VM と下位の VM をそれぞれ表示する
-   IOPS とスループットの使用率が上位の VM と下位の VM をそれぞれ表示する
-   VM を実行しているホストを表示する
-   クラスター共有ボリュームに含まれるディスクをスループット、IOPS、待機時間の観点から評価し、上位のものを表示する
- グループに基づくカスタマイズとフィルタリング

> [!NOTE]
> 容量とパフォーマンス ソリューションは、以前のバージョンでは "容量管理" と呼ばれていました。このソリューションではこれまで、System Center Operations Manager と System Center Virtual Machine Manager が必要でしたが、 新しい容量とパフォーマンス ソリューションでは、どちらも不要になっています。


## <a name="connected-sources"></a>接続先ソース

次の表は、このソリューションの接続先としてサポートされているソースとその説明です。

| 接続先ソース | サポート | 説明 |
|---|---|---|
| [Windows エージェント](../agents/agent-windows.md) | はい | このソリューションでは、Windows エージェントから容量とパフォーマンスに関するデータ情報を収集します。 |
| [Linux エージェント](../vm/quick-collect-linux-computer.md) | いいえ    | このソリューションでは、直接の Linux エージェントから容量とパフォーマンスに関するデータ情報を収集することはありません。|
| [SCOM 管理グループ](../agents/om-agents.md) | はい |このソリューションでは、接続された SCOM 管理グループ内のエージェントから容量とパフォーマンスに関するデータを収集します。 SCOM エージェントから Log Analytics への直接接続は必要ありません。|
| [Azure Storage アカウント](../essentials/resource-logs.md#send-to-log-analytics-workspace) | いいえ | Azure ストレージには、容量とパフォーマンスのデータは存在しません。|

## <a name="prerequisites"></a>前提条件

- 仮想マシンではなく Windows Server 2012 以降の Hyper-V ホストに、Windows エージェントまたは Operations Manager エージェントをインストールする必要があります。


## <a name="configuration"></a>構成

容量とパフォーマンス ソリューションをワークスペースに追加する手順は、以下のとおりです。

- [ソリューション ギャラリーから Log Analytics ソリューションを追加する](./solutions.md)方法に関するページの手順に従って、容量とパフォーマンス ソリューションを Log Analytics ワークスペースに追加します。

## <a name="management-packs"></a>管理パック

SCOM 管理グループが Log Analytics ワークスペースに接続されている場合、このソリューションを追加したときに次の管理パックが SCOM にインストールされます。 これらの管理パックに伴う構成や保守は不要です。

- Microsoft.IntelligencePacks.CapacityPerformance

1201 イベントは、次のようになります。


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

容量とパフォーマンス ソリューションが更新された場合には、バージョン番号が変わります。

ソリューション管理パックの更新方法の詳細については、「 [Operations Manager を Log Analytics に接続する](../agents/om-agents.md)」を参照してください。

## <a name="using-the-solution"></a>ソリューションの使用

容量とパフォーマンス ソリューションをワークスペースに追加すると、[概要] ダッシュボードに [キャパシティとパフォーマンス] が追加されます。 このタイルには、現時点でアクティブになっている Hyper-V ホストと仮想マシンのうち、選択した期間に監視対象となっていたものの数が表示されます。

![[キャパシティとパフォーマンス] タイル](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>使用率の確認

[キャパシティとパフォーマンス] タイルをクリックして、容量とパフォーマンスのダッシュボードを開きます。 ダッシュボードには、次の表に示した列が存在します。 それぞれの列には、特定のスコープと時間範囲について、その列の基準に該当する項目が最大 10 個表示されます。 すべてのレコードを返すログ検索を実行するには、列の一番下にある **[すべて表示]** をクリックするか、列ヘッダーをクリックします。

- **ホスト**
    - **[ホストの CPU 使用率]** 選択した期間について、ホスト コンピューターの CPU 使用率の傾向を示したグラフと、ホストの一覧が表示されます。 折れ線グラフにカーソルを合わせると、特定の時点の詳細を確認できます。 ログ検索でさらに詳しい情報を確認するには、グラフをクリックします。 ホスト名をクリックするとログ検索が開き、ホストされている VM について CPU カウンターの詳細を確認できます。
    - **[ホストのメモリ使用率]** 選択した期間について、ホスト コンピューターのメモリ使用率の傾向を示したグラフと、ホストの一覧が表示されます。 折れ線グラフにカーソルを合わせると、特定の時点の詳細を確認できます。 ログ検索でさらに詳しい情報を確認するには、グラフをクリックします。 ホスト名をクリックするとログ検索が開き、ホストされている VM についてメモリ カウンターの詳細を確認できます。
- **Virtual Machines**
    - **[VM の CPU 使用率]** 選択した期間について、仮想マシンの CPU 使用率の傾向を示したグラフと、仮想マシンの一覧が表示されます。 折れ線グラフにカーソルを合わせると、上位 3 位までの VM について、特定の時点の詳細を確認できます。 ログ検索でさらに詳しい情報を確認するには、グラフをクリックします。 VM 名をクリックするとログ検索が開き、その VM について集計された CPU カウンターの詳細を確認できます。
    - **[VM のメモリ使用率]** 選択した期間について、仮想マシンのメモリ使用率の傾向を示したグラフと、仮想マシンの一覧が表示されます。 折れ線グラフにカーソルを合わせると、上位 3 位までの VM について、特定の時点の詳細を確認できます。 ログ検索でさらに詳しい情報を確認するには、グラフをクリックします。 VM 名をクリックするとログ検索が開き、その VM について集計されたメモリ カウンターの詳細を確認できます。
    - **[VM の合計ディスク IOPS]** 選択した期間について、仮想マシンの合計ディスク IOPS の傾向を示したグラフと、それぞれの仮想マシンの IOPS を示した一覧が表示されます。 折れ線グラフにカーソルを合わせると、上位 3 位までの VM について、特定の時点の詳細を確認できます。 ログ検索でさらに詳しい情報を確認するには、グラフをクリックします。 VM 名をクリックするとログ検索が開き、その VM について集計されたディスク IOPS カウンターの詳細を確認できます。
    - **[VM の合計ディスク スループット]** 選択した期間について、仮想マシンの合計ディスク スループットの傾向を示したグラフと、それぞれの仮想マシンの合計ディスク スループットを示した一覧が表示されます。 折れ線グラフにカーソルを合わせると、上位 3 位までの VM について、特定の時点の詳細を確認できます。 ログ検索でさらに詳しい情報を確認するには、グラフをクリックします。 VM 名をクリックするとログ検索が開き、その VM について集計された合計ディスク スループット カウンターの詳細を確認できます。
- **クラスター化共有ボリューム**
    - **[スループットの合計]** クラスター化共有ボリュームに対する読み取りと書き込みの両方の合計が表示されます。
    - **[IOPS の合計]** クラスター化共有ボリュームに対する 1 秒あたりの入出力操作の合計が表示されます。
    - **[合計待機時間]** クラスター化共有ボリュームでの待ち時間の合計が表示されます。
- **[ホストの密度]** 最上部にあるタイルで、このソリューションで利用可能なホストと仮想マシンの合計数が表示されます。 ログ検索で追加の詳細を確認するには、最上部のタイルをクリックします。 このタイルにはほかにも、全ホストの一覧と、それぞれにホストされている仮想マシンの数が表示されます。 VM に関するログ検索の結果を確認するには、ホストをクリックします。


![ダッシュボードの [ホスト] ブレード](./media/capacity-performance/dashboard-hosts.png)

![ダッシュボードの [仮想マシン] ブレード](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>パフォーマンスの評価

コンピューティングの運用環境には、組織によって大きな差があります。 さらに、VM の実行方法や各自が "普通" であると考える状態の内容に応じて、容量とパフォーマンスのワークロードも変わります。 自分がパフォーマンスを測定するときに役立つ手順が何か具体的にあったとしても、それが環境に適用できるという保証はありません。 このため、もう少し一般化したガイダンスの方が、実用に適していると言えるでしょう。 Microsoft では、パフォーマンスの測定の規範として役立つガイダンス記事を多数公開しています。

このソリューションはつまるところ、パフォーマンス カウンターなどの多種多様な情報源から容量とパフォーマンスのデータを収集するものです。 収集した容量とパフォーマンスに関するデータは、このソリューションでさまざまな形で提示されます。このデータを、「[Measuring Performance on Hyper-V (Hyper-V のパフォーマンスを測定する)](https://www.microsoft.com/en-us/download/details.aspx?id=56495)」で紹介しているデータと比較してみてください。 同記事は少し前に書かれたものではありますが、メトリック、考慮事項、ガイドラインは依然として有効です。 また、この記事にはほかにも便利なリソースへのリンクが記載されています。


## <a name="sample-log-searches"></a>サンプル ログ検索

以下の表は、容量とパフォーマンスに関してこのソリューションで収集および計算されるデータを取得するためのログ検索のサンプルを示したものです。


| クエリ | 説明 |
|:--- |:--- |
| ホストのメモリ構成の一括表示 | Perf &#124; where ObjectName == "Capacity and Performance" and CounterName == "Host Assigned Memory MB" &#124; summarize MB = avg(CounterValue) by InstanceName |
| VM のメモリ構成の一括表示 | Perf &#124; where ObjectName == "Capacity and Performance" and CounterName == "VM Assigned Memory MB" &#124; summarize MB = avg(CounterValue) by InstanceName |
| 全 VM の合計ディスク IOPS の内訳 | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "VHD Reads/s" or CounterName == "VHD Writes/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| 全 VM の合計ディスク スループットの内訳 | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "VHD Read MB/s" or CounterName == "VHD Write MB/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| 全 CSV の合計 IOPS の内訳 | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "CSV Reads/s" or CounterName == "CSV Writes/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| 全 CSV の合計スループットの内訳 | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "CSV Reads/s" or CounterName == "CSV Writes/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| 全 CSV の合計待機時間の内訳 | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "CSV Read Latency" or CounterName == "CSV Write Latency") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |


## <a name="next-steps"></a>次のステップ
* [Log Analytics のログ検索機能](../logs/log-query-overview.md)を使用して、容量とパフォーマンスに関する詳細なデータを確認します。

