---
title: データ パイプラインを監視および管理する - Azure
description: 監視と管理アプリを使用して Azure のデータ ファクトリとパイプラインを監視および管理する方法について説明します。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 3a9eb73aab381f90ae51300bff1250348916c2ab
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779598"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>監視と管理アプリを使用した Azure Data Factory パイプラインの監視と管理
> [!div class="op_single_selector"]
> * [Azure Portal の使用/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [監視と管理アプリの使用](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[Data Factory パイプラインの監視と管理](../monitor-visually.md)に関するページを参照してください。

この記事では、監視と管理アプリを使用して、Azure Data Factory パイプラインを監視、管理、デバッグする方法について説明します。 次のビデオを見ると、アプリケーションの使用方法の概要を理解できます。

> [!NOTE]
> ビデオに示されるユーザー インターフェイスは、ご使用のポータルに表示されるものと完全には一致しない場合があります。 ビデオのユーザー インターフェイスは若干古いですが、概念は変わりません。 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>監視と管理アプリの起動
監視と管理アプリを起動するには、データ ファクトリの **[Data Factory]** ブレードで **[監視と管理]** タイルをクリックします。

![Data Factory ホーム ページの監視タイル](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

監視と管理アプリが別のウィンドウで開いたことがわかります。  

![監視と管理アプリ](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> 承認中であることを示すメッセージが表示されたまま Web ブラウザーが固まってしまう場合は、**サード パーティの Cookie とサイト データをブロック** するチェック ボックスをオフにします。または、チェック ボックスはオンにしたままにし、**login.microsoftonline.com** に対する例外を作成してから、もう一度アプリを開いてください。


中央のウィンドウの [アクティビティ ウィンドウ] 一覧で、アクティビティの実行ごとにアクティビティ ウィンドウを確認できます。 たとえば、1 時間ごとに 5 時間実行するようにアクティビティをスケジュールした場合、5 つのデータ スライスに関連付けられた 5 つのアクティビティ ウィンドウが表示されます。 下部にある一覧でアクティビティ ウィンドウを確認できない場合は、以下の手順を実行します。
 
- パイプラインの開始時刻および終了時刻に一致するように、上部にある **[開始時刻]** と **[終了時刻]** のフィルターを更新し、 **[適用]** をクリックします。  
- [アクティビティ ウィンドウ] 一覧は、自動的には更新されません。 **[アクティビティ ウィンドウ]** 一覧で、ツール バーの **[更新]** をクリックします。  

これらの手順を試す Data Factory アプリケーションがない場合は、[Data Factory を使用した Blob Storage から SQL Database へのデータのコピー](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)に関するチュートリアルを実行してください。

## <a name="understand-the-monitoring-and-management-app"></a>監視と管理アプリについて
左側に、 **[リソース エクスプローラー]** 、 **[監視ビュー]** 、 **[アラート]** の 3 つのタブがあります。 最初のタブ ( **[リソース エクスプローラー]** ) が既定で選択されています。

### <a name="resource-explorer"></a>Resource Explorer
次のように表示されます。

* 左側のウィンドウにリソース エクスプローラーの **ツリー ビュー**
* 中央のウィンドウの上部に **ダイアグラム ビュー**
* 中央のウィンドウの下部に **[アクティビティ ウィンドウ]** 一覧
* 右側のウィンドウに **[プロパティ]** 、 **[アクティビティ ウィンドウ エクスプローラー]** 、 **[スクリプト]** の各タブ

リソース エクスプローラーでは、データ ファクトリのすべてのリソース (パイプライン、データセット、リンクされたサービス) がツリー ビューに表示されます。 リソース エクスプローラーでオブジェクトを選択するときは、次の点に注意します。

* 関連付けられている Data Factory エンティティはダイアグラム ビューで強調表示されます。
* [関連付けられているアクティビティ ウィンドウ](data-factory-scheduling-and-execution.md)は、下部の [アクティビティ ウィンドウ] 一覧で強調表示されます。  
* 選択したオブジェクトのプロパティは、右側のウィンドウにある [プロパティ] ウィンドウに表示されます。
* 該当する場合は、選択したオブジェクトの JSON 定義が表示されます (リンクされたサービス、データセット、パイプラインなど)。

![Resource Explorer](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

アクティビティ ウィンドウの概念の詳細については、[スケジュール設定と実行](data-factory-scheduling-and-execution.md)に関する記事をご覧ください。

### <a name="diagram-view"></a>Diagram view
データ ファクトリのダイアグラム ビューでは、データ ファクトリとその資産を 1 つのウィンドウで監視および管理できます。 ダイアグラム ビューで Data Factory エンティティ (データセットまたはパイプライン) を選択するときは、次の点に注意します。

* Data Factory エンティティはツリー ビューで選択されます。
* 関連付けられているアクティビティ ウィンドウは、[アクティビティ ウィンドウ] 一覧で強調表示されます。
* 選択したオブジェクトのプロパティは、[プロパティ] ウィンドウに表示されます。

パイプラインは、有効になっている場合 (一時停止状態ではない場合)、緑の線付きで示されます。

![実行中のパイプライン](./media/data-factory-monitor-manage-app/PipelineRunning.png)

ダイアグラム ビューで選択し、コマンド バーのボタンを使用して、パイプラインを一時停止、再開、または終了できます。

![コマンド バーの一時停止/再開ボタン](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
ダイアグラム ビューには、パイプライン用の 3 つのコマンド バー ボタンがあります。 2 番目のボタンを使用すると、パイプラインを一時停止できます。 一時停止しても、現在実行中のアクティビティが終了することはありません。アクティビティは完了するまで続行されます。 3 番目のボタンは、パイプラインを一時停止し、実行中の既存のアクティビティを終了します。 最初のボタンはパイプラインを再開します。 パイプラインを一時停止すると、パイプラインの色が変わります。 たとえば、一時停止したパイプラインは次の図のようになります。 

![一時停止されたパイプライン](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Ctrl キーを使用して、複数のパイプラインを同時に選択できます。 コマンド バー ボタンを使用して、一度に複数のパイプラインを一時停止/再開することができます。

パイプラインを右クリックし、パイプラインを中断、再開、または終了するオプションを選択することもできます。 

![パイプラインのコンテキスト メニュー](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

**[パイプラインを開く]** オプションをクリックすると、そのパイプラインのすべてのアクティビティが表示されます。 

![パイプラインを開くメニュー](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

開かれたパイプラインのビューには、パイプラインのすべてのアクティビティが表示されます。 この例では、コピー アクティビティというアクティビティが 1 つだけあります。 

![開いているパイプライン](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

前のビューに戻るには、上部にある階層リンク メニューのデータ ファクトリ名をクリックします。

パイプライン ビューでは、出力データセットを選択するか、出力データセット上にマウスを移動すると、そのデータセットの [アクティビティ ウィンドウ] ポップアップ ウィンドウが表示されます。

![[アクティビティ ウィンドウ] ポップアップ ウィンドウ](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

アクティビティ ウィンドウをクリックすると、右側のウィンドウにある **[プロパティ]** ウィンドウにその詳細が表示されます。

![アクティビティ ウィンドウのプロパティ](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

右側のウィンドウで、 **[アクティビティ ウィンドウ エクスプローラー]** タブに切り替えると、さらに詳しい情報が表示されます。

![[アクティビティ ウィンドウ エクスプローラー] タブへのアクセス方法を示すスクリーンショット。](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

アクティビティが試行されるたびに、 **[試行]** セクションに **解決された変数** も表示されます。

![解決された変数](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

**[スクリプト]** タブに切り替えて、選択したオブジェクトの JSON スクリプト定義を確認します。   

![[スクリプト] タブ](./media/data-factory-monitor-manage-app/ScriptTab.png)

アクティビティ ウィンドウは、次の 3 か所で確認できます。

* ダイアグラム ビューの [アクティビティ ウィンドウ] ポップアップ (中央のウィンドウ)。
* 右側のウィンドウにあるアクティビティ ウィンドウ エクスプローラー。
* 下部のウィンドウにある [アクティビティ ウィンドウ] 一覧。

[アクティビティ ウィンドウ] ポップアップとアクティビティ ウィンドウ エクスプローラーでは、左矢印と右矢印を使用して前の週と次の週にスクロールすることができます。

![アクティビティ ウィンドウ エクスプローラーの左矢印と右矢印](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

ダイアグラム ビューの下部には、拡大、縮小、画面に合わせたサイズ変更、100 % 表示、レイアウト ロックの各ボタンが表示されます。 **レイアウト ロック** ボタンは、ダイアグラム ビューでテーブルやパイプラインを誤って移動するのを防ぎます。 このボタンは既定でオンになっています。 このボタンをオフにすると、ダイアグラム内でエンティティを移動できます。 オフにするときに、最後のボタンを使用してテーブルやパイプラインを自動的に配置できます。 マウス ホイールを使用して、拡大または縮小することもできます。

![ダイアグラム ビューの拡大/縮小コマンド](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>[アクティビティ ウィンドウ] 一覧
中央のウィンドウの下部にある [アクティビティ ウィンドウ] 一覧には、リソース エクスプローラーまたはダイアグラム ビューで選択したデータセットのすべてのアクティビティ ウィンドウが表示されます。 既定では、この一覧は降順になっています。つまり、最新のアクティビティ ウィンドウが先頭に表示されます。

![[アクティビティ ウィンドウ] 一覧](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

この一覧は自動的に更新されないので、ツール バーの更新ボタンを使用して手動で更新します。  

アクティビティ ウィンドウは、次の状態のいずれかになります。

<table>
<tr>
    <th align="left">Status</th><th align="left">副状態</th><th align="left">説明</th>
</tr>
<tr>
    <td rowspan="8">待機中</td><td>ScheduleTime</td><td>アクティビティ ウィンドウが実行される時刻になっていません。</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>アップストリームの依存関係の準備ができていません。</td>
</tr>
<tr>
<td>ComputeResources</td><td>コンピューティング リソースが使用できません。</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>すべてのアクティビティ インスタンスが、他のアクティビティ ウィンドウを実行しています。</td>
</tr>
<tr>
<td>ActivityResume</td><td>アクティビティは一時停止されており、再開されるまでアクティビティ ウィンドウを実行できません。</td>
</tr>
<tr>
<td>[再試行]</td><td>アクティビティの実行が再試行されています。</td>
</tr>
<tr>
<td>検証</td><td>検証がまだ開始されていません。</td>
</tr>
<tr>
<td>ValidationRetry</td><td>検証は再試行を待機中です。</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>Validating</td><td>検証を実行中です。</td>
</tr>
<td>-</td>
<td>アクティビティ ウィンドウが処理されています。</td>
</tr>
<tr>
<td rowspan="4">失敗</td><td>TimedOut</td><td>アクティビティで許可されている実行時間を超過しました。</td>
</tr>
<tr>
<td>Canceled</td><td>ユーザー操作によってアクティビティ ウィンドウがキャンセルされました。</td>
</tr>
<tr>
<td>検証</td><td>検証が失敗しました。</td>
</tr>
<tr>
<td>-</td><td>アクティビティ ウィンドウの生成または検証に失敗しました。</td>
</tr>
<td>Ready</td><td>-</td><td>アクティビティ ウィンドウは使用可能な状態です。</td>
</tr>
<tr>
<td>スキップ</td><td>-</td><td>アクティビティ ウィンドウが処理されませんでした。</td>
</tr>
<tr>
<td>なし</td><td>-</td><td>アクティビティ ウィンドウは別の状態で存在していましたが、リセットされました。</td>
</tr>
</table>


一覧でアクティビティ ウィンドウをクリックすると、右側にある **[アクティビティ ウィンドウ エクスプローラー]** または **[プロパティ]** ウィンドウに詳細が表示されます。

![アクティビティ ウィンドウの詳細の表示方法を示すスクリーンショット。](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>アクティビティ ウィンドウの更新
詳細は自動的に更新されないので、コマンド バーの 更新ボタン (2 番目のボタン) を使用して、アクティビティ ウィンドウの一覧を手動で更新します。  

### <a name="properties-window"></a>[プロパティ] ウィンドウ
[プロパティ] ウィンドウは、監視と管理アプリの右端のウィンドウにあります。

![[プロパティ] ウィンドウ](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

ここには、リソース エクスプローラー (ツリー ビュー)、ダイアグラム ビュー、または [アクティビティ ウィンドウ] 一覧で選択した項目のプロパティが表示されます。

### <a name="activity-window-explorer"></a>[Activity Window Explorer (アクティビティ ウィンドウ エクスプローラー)]
**[アクティビティ ウィンドウ エクスプローラー]** ウィンドウは、監視と管理アプリの右端のウィンドウにあります。 ここには、[アクティビティ ウィンドウ] ポップアップ ウィンドウまたは [アクティビティ ウィンドウ] 一覧で選択したアクティビティ ウィンドウの詳細が表示されます。

![[Activity Window Explorer (アクティビティ ウィンドウ エクスプローラー)]](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

別のアクティビティ ウィンドウに切り替えるには、上部にあるカレンダー ビューでそのアクティビティ ウィンドウをクリックします。 上部にある左矢印/右矢印ボタンを使用して、前の週または次の週のアクティビティ ウィンドウを表示することもできます。

下部のウィンドウにあるツール バー ボタンを使用して、アクティビティ ウィンドウを再実行したり、ウィンドウ内の詳細情報を更新したりすることができます。

### <a name="script"></a>スクリプト
**[スクリプト]** タブを使用して、選択した Data Factory エンティティ (リンクされたサービス、データセット、またはパイプライン) の JSON 定義を表示できます。

![[スクリプト] タブ](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>システム ビューの使用
監視と管理アプリには、既成のシステム ビュー (**最近のアクティビティ ウィンドウ**、**失敗したアクティビティ ウィンドウ**、**進行中のアクティビティ ウィンドウ**) が用意されています。これらを使用すると、データ ファクトリの最近のアクティビティ ウィンドウ、失敗したアクティビティ ウィンドウ、進行中のアクティビティ ウィンドウを表示できます。

左側の **[監視ビュー]** タブをクリックして切り替えます。

![Monitoring Views tab](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

現在、3 つのシステム ビューがサポートされています。 (中央のウィンドウの下部にある) [アクティビティ ウィンドウ] 一覧に最近のアクティビティ ウィンドウ、失敗したアクティビティ ウィンドウ、または進行中のアクティビティ ウィンドウを表示するオプションを選択します。

**[最近のアクティビティ ウィンドウ]** を選択すると、最近のアクティビティ ウィンドウが **最終試行時刻** の降順ですべて表示されます。

**[失敗したアクティビティ ウィンドウ]** ビューを使用すると、失敗したアクティビティ ウィンドウすべてを一覧に表示できます。 一覧で失敗したアクティビティ ウィンドウを選択すると、その詳細が **[プロパティ]** ウィンドウまたは **[アクティビティ ウィンドウ エクスプローラー]** に表示されます。 また、失敗したアクティビティ ウィンドウのログをダウンロードすることもできます。

## <a name="sort-and-filter-activity-windows"></a>アクティビティ ウィンドウの並べ替えとフィルター処理
アクティビティ ウィンドウをフィルター処理するには、コマンド バーの **[開始時刻]** と **[終了時刻]** の設定を変更します。 開始時刻と終了時刻を変更したら、終了時刻の横にあるボタンをクリックして、[アクティビティ ウィンドウ] 一覧を更新します。

![開始時刻と終了時刻](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> 現時点では、監視と管理アプリの時刻はすべて UTC 形式です。
>
>

**[アクティビティ ウィンドウ]** の一覧で、列の名前 (例: 状態) をクリックします。

![[アクティビティ ウィンドウ] 一覧の列メニュー](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

次の操作を実行できます。

* 昇順で並べ替える。
* 降順で並べ替える。
* 1 つ以上の値 ("準備完了"、"待機中" など) でフィルター処理する。

列にフィルターを指定すると、その列のフィルター ボタンが有効になります。このボタンは、列の値がフィルター処理された値であることを示しています。

![[アクティビティ ウィンドウ] 一覧の列のフィルター処理](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

フィルターをクリアするには、同じポップアップ ウィンドウを使用できます。 [アクティビティ ウィンドウ] 一覧のすべてのフィルターをクリアするには、コマンド バーのフィルターのクリア ボタンをクリックします。

![[アクティビティ ウィンドウ] 一覧のすべてのフィルターのクリア](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>バッチ操作の実行
### <a name="rerun-selected-activity-windows"></a>選択したアクティビティ ウィンドウを再実行する
アクティビティ ウィンドウを選択し、1 つ目のコマンド バー ボタンの下向き矢印をクリックして、 **[再実行]**  /  **[パイプラインのアップストリームによる再実行]** を選択します。 **[パイプラインのアップストリームによる再実行]** を選択すると、アップストリーム アクティビティ ウィンドウもすべて再実行されます。
    ![Rerun a activity window](./media/data-factory-monitor-manage-app/ReRunSlice.png)

また、一覧で複数のアクティビティ ウィンドウを選択して、それらを同時に再実行することもできます。 状態 (例: **失敗**) でアクティビティ ウィンドウをフィルター処理し、アクティビティ ウィンドウが失敗した原因となる問題を解決した後に、失敗したアクティビティ ウィンドウを再実行できます。 一覧にあるアクティビティ ウィンドウのフィルター処理の詳細については、次のセクションを参照してください。  

### <a name="pauseresume-multiple-pipelines"></a>複数のパイプラインを一時停止/再開する
Ctrl キーを使用して、複数のパイプラインを複数選択できます。 (次の図の赤い四角形で強調表示されている) コマンド バー ボタンを使用して、これらのパイプラインを一時停止/再開できます。

![コマンド バーの一時停止/再開ボタン](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
