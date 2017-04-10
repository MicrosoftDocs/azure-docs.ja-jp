---
title: "Log Analytics データを Power BI にエクスポートする | Microsoft Docs"
description: "Power BI は、さまざまなデータのセットの分析のために豊富な視覚エフェクトとレポートを提供するマイクロソフトのクラウド ベースのビジネス分析サービスです。  Log Analytics は、その視覚エフェクトと分析ツールを活用できるように、OMS リポジトリから Power BI へのデータを継続的にエクスポートできます。  この記事では、一定の間隔で Power BI に自動的にエクスポートする Log Analytics でのクエリの構成方法について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7870e2aebf6c142b0f3103a500ca73d691e6d3e6


---
# <a name="export-log-analytics-data-to-power-bi"></a>Log Analytics データを Power BI にエクスポートする
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) は、さまざまなデータのセットの分析のために豊富な視覚エフェクトとレポートを提供するマイクロソフトのクラウド ベースのビジネス分析サービスです。  Log Analytics は、その視覚エフェクトと分析ツールを活用できるように、OMS リポジトリから Power BI へのデータを自動的にエクスポートできます。

Log Analytics で Power BI を構成する場合は、Power BI の対応するデータセットに結果をエクスポートするログ クエリを作成します。  クエリとエクスポートは、Log Analytics によって収集された最新データでデータセットを保持することを定義するスケジュール上で自動的に実行を続けます。

![Power BI への Log Analytics](media/log-analytics-powerbi/overview.png)

## <a name="power-bi-schedules"></a>Power BI スケジュール
*Power BI スケジュール* には、OMS リポジトリから Power BI の対応するデータセットにデータのセットをエクスポートするログ検索と、この検索を実行してデータセットを最新のまま保持する頻度を定義するスケジュールが含まれます。

データセットのフィールドは、ログ検索によって返されるレコードのプロパティと一致します。  検索によってさまざまな種類のレコードが返される場合、データセットには、含まれているレコードの各種類のプロパティがすべて含まれます。  

> [!NOTE]
> [Measure](log-analytics-search-reference.md#measure)などのコマンドで任意の統合を実行するのではなく、生データを返すログ検索クエリを使用することをお勧めします。  生データから Power BI での集計と計算を実行できます。
> 
> 

## <a name="connecting-oms-workspace-to-power-bi"></a>OMS ワークスペースを Power BI に接続する
Log Analytics から Power BI にエクスポートする前に、次の手順に従って、OMS ワークスペースを Power BI アカウントに接続する必要があります。  

1. OMS コンソールで **[設定]** タイルをクリックします。
2. **[アカウント]**を選択します。
3. **[Workspace Information (ワークスペース情報)]** セクションで **[Connect to Power BI Account (Power BI アカウントに接続する)]** をクリックします。
4. Power BI アカウントの資格情報を入力します。

## <a name="create-a-power-bi-schedule"></a>Power BI スケジュールの作成
次の手順に従って、データセットごとに Power BI スケジュールを作成します。

1. OMS コンソールで **[Log Search]** (ログの検索) タイルをクリックします。
2. 新しいクエリで入力するか、または **Power BI**にエクスポートするデータを返す保存された検索を選択します。  
3. ページの上部にある **[Power BI]** ボタンをクリックして、**[Power BI]** ダイアログ ボックスを開きます。
4. 次の表にある情報を入力して、 **[保存]**をクリックします。

| プロパティ | Description |
|:--- |:--- |
| 名前 |Power BI スケジュールの一覧を表示するときにスケジュールを識別する名前。 |
| 保存する検索条件 |実行するログ検索。  現在のクエリを選択するか、またはドロップダウン ボックスから既存の保存された検索を選択します。 |
| スケジュール |保存された検索を実行して Power BI データセットにエクスポートする頻度。  値は 15 分～ 24 時間の範囲で指定する必要があります。 |
| データセット名 |Power BI のデータセットの名前。  存在しない場合は作成し、存在する場合は更新します。 |

## <a name="viewing-and-removing-power-bi-schedules"></a>Power BI スケジュールの表示と削除
次の手順で既存の Power BI スケジュールの一覧を表示します。

1. OMS コンソールで **[設定]** タイルをクリックします。
2. **[Power BI]**を選択します。

スケジュールの詳細に加えて、スケジュールが過去 1 週間に実行した回数と、前回の同期の状態が表示されます。  同期にエラーが発生した場合は、リンクをクリックして、エラーの詳細を含むレコードのログ検索を実行することができます。

スケジュールを削除するには、**[削除]** 列で **[X]** をクリックします。  スケジュールを無効にするには、 **[オフ]**を選択します。  スケジュールを変更するには、それを削除し、新しい設定で再作成する必要があります。

![Power BI スケジュール](media/log-analytics-powerbi/schedules.png)

## <a name="sample-walkthrough"></a>サンプルのチュートリアル
次のセクションでは、Power BI スケジュールを作成し、そのデータセットを使用して、簡単なレポートを作成する例を段階的に説明します。  この例では、コンピューターのセットのすべてのパフォーマンス データを Power BI にエクスポートし、線グラフを作成してプロセッサ使用率を表示します。

### <a name="create-log-search"></a>ログ検索の作成
まず、データセットに送信するデータのログ検索を作成します。  この例では、 *srv*で始まる名前を持つコンピューターのすべてのパフォーマンス データを返すクエリを使用します。  

![Power BI スケジュール](media/log-analytics-powerbi/walkthrough-query.png)

### <a name="create-power-bi-search"></a>Power BI 検索の作成
**[Power BI]** ボタンをクリックして、[Power BI] ダイアログ ボックスを開き、必要な情報を入力します。  この検索を 1 時間あたり 1 回実行するとします。そこで、*Contoso Perf* という名前のデータセットを作成します。  必要なデータを作成する検索は既に開かれているため、**[Saved Search (保存した検索条件)]** は既定の *[Use current search query (現在の検索クエリを使用する)]* のままにしておきます。

![Power BI 検索](media/log-analytics-powerbi/walkthrough-schedule.png)

### <a name="verify-power-bi-search"></a>Power BI 検索の確認
スケジュールが正しく作成されたことを確認するには、OMS のダッシュボードの **[設定]** タイルにある Power BI 検索の一覧を表示します。  数分間待機し、同期が実行されたことを報告するまで、このビューを更新します。

![Power BI 検索](media/log-analytics-powerbi/walkthrough-schedules.png)

### <a name="verify-the-dataset-in-power-bi"></a>Power BI でのデータセットの確認
[powerbi.microsoft.com](http://powerbi.microsoft.com/) でアカウントにログインして、左側のウィンドウの下部にある **[データセット]** までスクロールします。  エクスポートが正常に実行されたことを示す *[Contoso Perf]* データセットが表示されていることを確認できます。

![Power BI データセット](media/log-analytics-powerbi/walkthrough-datasets.png)

### <a name="create-report-based-on-dataset"></a>データセットに基づくレポートの作成
**[Contoso Perf]** データセットを選択し、右側の **[フィールド]** ウィンドウにある **[結果]** をクリックして、このデータセットの一部であるフィールドを表示します。  各コンピューターのプロセッサ使用率を示す線グラフを作成するには、次の操作を実行します。

1. 線グラフの視覚エフェクトを選択します。
2. **[ObjectName]** を **[Report level filter (レポート レベルのフィルター)]** にドラッグして、**[プロセッサ]** チェック ボックスをオンにします。
3. **[CounterName]** を **[Report level filter (レポート レベルのフィルター)]** にドラッグして、**[% Processor Time (% プロセッサ時間)]** チェック ボックスをオンにします。
4. **[CounterValue]** を **[値]** にドラッグします。
5. **[Computer]** を **[凡例]** にドラッグします。
6. **[TimeGenerated]** を **[軸]** にドラッグします。

この例のデータセットからデータを結果として得られる線グラフが表示されることがわかります。

![Power BI 線グラフ](media/log-analytics-powerbi/walkthrough-linegraph.png)

### <a name="save-the-report"></a>レポートの保存
画面の上部にある [保存] ボタンをクリックしてレポートを保存し、左側のウィンドウの [レポート] セクションにレポートが一覧表示されていることを検証します。

![Power BI レポート](media/log-analytics-powerbi/walkthrough-report.png)

## <a name="next-steps"></a>次のステップ
* [ログ検索](log-analytics-log-searches.md) について学習し、Power BI にエクスポートできるクエリを作成します。
* [Power BI](http://powerbi.microsoft.com) について詳しく学習し、Log Analytics のエクスポートに基づく視覚エフェクトを作成します。




<!--HONumber=Nov16_HO3-->


