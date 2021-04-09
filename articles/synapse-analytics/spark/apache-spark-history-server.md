---
title: 拡張された Spark History Server を使用してアプリをデバッグする
description: 拡張された Spark History Server を使用して、Azure Synapse Analytics で Spark アプリケーションのデバッグと診断を行います。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4471fed7b423fe05147db30afe57f6c845fe640e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670689"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>拡張された Apache Spark History Server を使用して Apache Spark アプリケーションのデバッグと診断を行う

この記事では、拡張された Apache Spark History Server を使用して、完成して実行されている Spark アプリケーションのデバッグおよび診断を行う方法に関するガイダンスを提供します。

拡張機能には、[データ] タブ、[グラフ] タブ、[診断] タブが含まれています。 **[データ]** タブを使用して、Spark ジョブの入力データと出力データをチェックします。 **[グラフ]** タブでは、ジョブ グラフのデータ フローと再生が示されます。 **[診断]** タブには、 **[データ スキュー]** 、 **[Time Skew]\(時間のずれ\)** 、および **[Executor Usage Analysis]\(実行プログラムの使用状況の分析\)** が含まれます。

## <a name="access-the-apache-spark-history-server"></a>Apache Spark History Server へのアクセス

Apache Spark History Server は、完了および実行中の Spark アプリケーションの Web ユーザー インターフェイスです。 Apache Spark History Server Web インターフェイスは、Azure Synapse Analytics から開くことができます。

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>Spark History Server Web UI を Apache Spark アプリケーション ノードから開く

1. [Azure Synapse Analytics](https://web.azuresynapse.net/) を開きます。

2. **[モニター]** を選択し、次に **[Apache Spark アプリケーション]** を選択します。

    ![[モニター] を選択し、[Spark アプリケーション] を選択します。](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. アプリケーションを選択し、 **[ログ クエリ]** を選択して開きます。

    ![ログ クエリ ウィンドウが開きます。](./media/apache-spark-history-server/open-application-window.png)

4. **Spark History Server** を選択すると、Spark History Server Web UI が表示されます。

    ![Spark History Server を開きます。](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>Spark History Server Web UI をデータ ノードから開く

1. Azure Synapse Studio ノートブックで、ジョブ実行出力セルから、または ノートブック ドキュメントの下部にある状態パネルから、 **[Spark History Server]** を選択します。 **[セッション詳細]** を選択します。

   ![Spark History Server の起動 1](./media/apache-spark-history-server/launch-history-server2.png "Spark History Server の起動")

2. スライドアウト パネルから **[Spark History Server]** を選択します。

   ![Spark History Server の起動 2](./media/apache-spark-history-server/launch-history-server.png "Spark History Server の起動")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Spark History Server の [データ] タブを探索する

表示するジョブのジョブ ID を選択します。 次に、[ツール] メニューの **[データ]** を選択すると、データ ビューが表示されます。 このセクションでは、[データ] タブでさまざまなタスクを実行する方法について説明します。

* 各タブを選択して **[Inputs]\(入力\)** 、 **[Outputs]\(出力\)** 、 **[Table Operations]\(テーブルの操作\)** を確認します。

    ![Spark アプリケーションのデータのタブ](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* **[コピー]** を選択して、すべての行をコピーします。

    ![Spark アプリケーションのデータ、コピー](./media/apache-spark-history-server/apache-spark-data-copy.png)

* **[csv]** を選択して、すべてのデータを CSV ファイルとして保存します。

    ![Spark アプリケーションのデータ、保存](./media/apache-spark-history-server/apache-spark-data-save.png)

* **[検索]** フィールドにキーワードを入力して、検索します。 検索結果はすぐに表示されます。

    ![Spark アプリケーションのデータ、検索](./media/apache-spark-history-server/apache-spark-data-search.png)

* テーブルを並べ替えるには、列ヘッダーを選択します。行を展開して行の詳細を表示するには、プラス記号を選択します。行を折りたたむには、マイナス記号を選択します。

    ![Spark アプリケーションのデータ、テーブル](./media/apache-spark-history-server/apache-spark-data-table.png)

* **[Partial Download]\(一部ダウンロード\)** を選択して、1 つのファイルをダウンロードします。 選択したファイルはローカルにダウンロードされます。 ファイルが存在しない場合は、新しいタブがエラー メッセージと共に表示されます。

    ![Spark アプリケーションのデータ、行のダウンロード](./media/apache-spark-history-server/sparkui-data-download-row.png)

* 完全パスまたは相対パスをコピーするには、ドロップ ダウン メニューから展開する **[完全なパスのコピー]** または **[相対パスのコピー]** オプションを選択します。 Azure Data Lake Storage ファイルの場合、 **[Azure Storage Explorer で開く]** によって、Azure Storage Explorer が起動し、サインインしているフォルダーに移動します。

    ![Spark アプリケーションのデータ、パスのコピー](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* 1 ページに表示できる行数を超える行がある場合は、テーブルの下のページ番号を選択して、ページに移動します。

    ![Spark アプリケーションのデータ、ページ](./media/apache-spark-history-server/apache-spark-data-page.png)

* **[データ]** の横の疑問符をポイントして、ツールチップを表示するか、または疑問符を選択して、詳細を取得します。

    ![Spark アプリケーションのデータ、詳細情報](./media/apache-spark-history-server/sparkui-data-more-info.png)

* **[フィードバックをお寄せください]** を選択することで、問題のフィードバックを送信できます。

    ![Spark、グラフ、フィードバック提供再掲](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Apache Spark History Server の [グラフ] タブ

表示するジョブのジョブ ID を選択します。 ツール メニューの **[グラフ]** を選択して、ジョブ グラフ ビューを取得します。

### <a name="overview"></a>概要

生成されたジョブ グラフで、ジョブの概要を表示できます。 既定で、グラフにはすべてのジョブが表示されます。 **ジョブ ID** で、このビューをフィルター処理できます。

![Spark アプリケーションとジョブ グラフ、ジョブ ID](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>表示

既定で、 **[進行状況]** 表示が選択されています。 **[表示]** ドロップダウン リストで **[読み取り]** または **[書き込み]** を選択して、データ フローを確認できます。

![Spark アプリケーションとジョブ グラフ、表示](./media/apache-spark-history-server/sparkui-graph-display.png)

グラフ ノードに、ヒートマップの凡例に表示される色が表示されます。

![Spark アプリケーションとジョブ グラフ、ヒートマップ](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>再生

ジョブを再生するには、 **[再生]** を選択します。 いつでも **[停止]** を選択して停止できます。 タスクの色は、再生時にさまざまな状態を示します。

|Color|意味|
|-|-|
|[緑]|Succeeded (成功):ジョブは正常に完了しました。|
|オレンジ|再試行:失敗しましたが、ジョブの最終結果には影響しないタスクのインスタンス。 このようなタスクには、後で成功する可能性がある重複するインスタンスまたは再試行インスタンスがあります。|
|青|Running (実行中):タスクは実行中です。|
|White|待機中またはスキップ:タスクは実行を待機しているか、ステージがスキップされました。|
|[赤]|失敗:タスクは失敗しました。|

次の図に、緑、オレンジ色、青の状態の色を示しています。

![Spark アプリケーションとジョブ グラフ、色サンプル、実行](./media/apache-spark-history-server/sparkui-graph-color-running.png)

次の図に、緑と白の状態の色を示しています。

![Spark アプリケーションとジョブ グラフの色のサンプル、スキップ](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

次の図に、赤と緑の状態の色を示しています。

![Spark アプリケーションとジョブ グラフ、色サンプル、失敗](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> 各ジョブを再生できます。 不完全なジョブでは、再生はサポートされていません。

### <a name="zoom"></a>Zoom

ジョブ グラフを拡大または縮小するために、マウス スクロールを使用するか、画面に合わせるために、 **[ウィンドウのサイズに合わせて大きさを変更]** を選択します。

![Spark アプリケーションとジョブ グラフ、画面に合わせてサイズを変更](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>ツールヒント

失敗したタスクがある場合にツールヒントを表示するには、グラフ ノードをポイントし、ステージを選択して、そのステージ ページを開きます。

![Spark アプリケーションとジョブ グラフ、ヒント](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

ジョブ グラフ タブで、次の条件を満たすタスクがある場合、ステージにはツールヒントと小さいアイコンが表示されます。

|条件|説明|
|-|-|
|データ スキュー|データの読み取りサイズ > このステージ内のすべてのタスクの平均データ読み取りサイズ * 2 かつデータ読み取りサイズ > 10 MB|
|時間のずれ|実行時間 > このステージ内のすべてのタスクの平均実行時間 * 2 かつ実行時間 > 2 分|
   
![Spark アプリケーションとジョブ グラフ、スキュー アイコン](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>グラフ ノードの説明

ジョブ グラフ ノードには、各ステージの次の情報が表示されます。

  * ID。
  * 名前または説明。
  * 合計タスク数。
  * データの読み取り: 入力サイズとシャッフル読み取りサイズの合計。
  * データ書き込み: 出力サイズとシャッフル書き込みサイズの合計。
  * 実行時間: 最初の試行の開始時刻と最後の試行の完了時刻の間の時間。
  * 行数: 入力レコード、出力レコード、シャッフル読み取りレコード、シャッフル書き込みレコードの合計。
  * 進行状況。

    > [!NOTE]  
    > 既定で、ジョブ グラフ ノードには各ステージの最後の試行の情報が表示されます (ステージの実行時間を除く)。 ただし、再生中は、グラフ ノードに各試行の情報が表示されます。
    >  
    > 読み取りと書き込みのデータ サイズは、1 MB = 1000 KB = 1000 * 1000 バイトです。

### <a name="provide-feedback"></a>フィードバックの提供

**[フィードバックをお寄せください]** を選択することで、問題のフィードバックを送信できます。

![Spark アプリケーションとジョブ グラフ、フィードバック](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>Apache Spark History Server の [診断] タブの探索

[診断] タブにアクセスするには、ジョブ ID を選択します。 次に、[ツール] メニューの **[診断]** を選択すると、ジョブの [診断] ビューが表示されます。 [Diagnosis]\(診断\) タブには、 **[Data Skew]\(データ スキュー\)** 、 **[Time Skew]\(時間のずれ\)** 、および **[Executor Usage Analysis]\(実行プログラムの使用状況の分析\)** が含まれます。

**[Data Skew]\(データ スキュー\)** 、 **[Time Skew]\(時間のずれ\)** 、および **[Executor Usage Analysis]\(実行プログラムの使用状況の分析\)** を確認するには、各タブを選択します。

![SparkUI、診断、データ スキュー タブ再掲](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>[Data Skew]\(データ スキュー\)

**[データ スキュー]** タブを選択すると、指定されたパラメーターに基づいて、対応する偏りのあるタスクが表示されます。

* **[パラメーターの指定]** - 最初のセクションには、データ スキューの検出に使用するパラメーターが表示されます。 既定の規則は次のとおりです。読み取られたタスク データが、読み取られたタスク データの平均量の 3 倍を超えており、なおかつ 10 MB を超えていることが指定されています。 傾斜したタスクに独自の規則を定義する場合は、パラメーターを選択できます。 **[傾斜したステージ]** と **[スキュー グラフ]** セクションは、それに応じて更新されます。

* **[傾斜したステージ]** - 2 番目のセクションには、上記で指定した条件を満たす偏りのあるタスクがあるステージが表示されます。 ステージに偏りのあるタスクが複数ある場合、偏りのあるステージ テーブルには、最も偏りの大きなタスク (たとえば、データ スキューの最大データ) のみが表示されます。

    ![SparkUI、診断、データ スキュー タブ](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **[Skew Chart]\(スキュー グラフ\)** - スキュー ステージ テーブルの行を選択すると、データの読み取りと実行時間に基づいて、スキュー グラフにさらに多くのタスク分布の詳細が表示されます。 偏りのあるタスクは赤でマークされ、通常のタスクは青でマークされます。 グラフには最大 100 個のサンプル タスクが表示され、タスクの詳細が右下のパネルに表示されます。

    ![SparkUI、ステージ 10 のスキュー グラフ](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>[Time Skew]\(時間のずれ\)

**[Time Skew]\(時間のずれ\)** タブには、タスクの実行時間に基づいて偏りのあるタスクが表示されます。

* **[パラメーターの指定]** - 最初のセクションには、時間のずれの検出に使用するパラメーターが表示されます。 時間のずれを検出する既定の条件は、タスクの実行時間が平均実行時間の 3 倍を超えており、なおかつ 30 秒を超えていることです。 必要に応じてパラメーターを変更できます。 **[Skewed Stage]\(偏りのあるステージ\)** と **[Skew Chart]\(スキュー グラフ\)** には、上の **[Data Skew]\(データ スキュー\)** タブと同様に対応するステージとタスクの情報が表示されます。

* **[時間のずれ]** を選択すると、 **[パラメーターの指定]** セクションで設定されたパラメーターに従って、フィルター処理された結果が **[傾斜したステージ]** セクションに表示されます。 **[傾斜したステージ]** セクションで項目を選択すると、対応するグラフの下書きがセクション 3 に表示され、タスクの詳細が右下のパネルに表示されます。

    ![SparkUI、診断、時間のずれセクション](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>実行プログラムの使用状況の分析

[Executor Usage Graph]\(実行プログラムの使用状況グラフ\) には、Spark ジョブの実行プログラムの割り当てと実行状態が視覚化されます。  

1. **[Executor Usage Analysis]\(Executor 利用状況分析\)** を選択すると、Executor の利用状況に関する 4 種類の曲線 ( **[Allocated Executors]\(割り当て済みの Executor 数\)** 、 **[Running Executors]\(実行中の Executor 数\)** 、 **[idle Executors]\(アイドル状態の Executor 数\)** 、 **[Max Executor Instances]\(Executor インスタンスの最大数\)** ) が表示されます。 割り当て済みの実行プログラムについては、"Executor added" (実行プログラムの追加) イベントまたは "Executor removed" (実行プログラムの削除) イベントごとに割り当て済み実行プログラムが増減します。 詳細な比較については、[Jobs]\(ジョブ\) タブの [Event Timeline]\(イベントのタイムライン\) を確認します。

   ![SparkUI、診断、エグゼキュータ タブ](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. 色のアイコンを選択すると、すべての下書きの対応する内容が選択または選択解除されます。

    ![SparkUI、診断、グラフの選択](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>既知の問題

Resilient Distributed Datasets (RDD) を使用した入力/出力データは、[データ] タブに表示されません。

## <a name="next-steps"></a>次のステップ

- [Azure Synapse Analytics](../overview-what-is.md)
- [.NET for Apache Spark ドキュメント](/dotnet/spark)

