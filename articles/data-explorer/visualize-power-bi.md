---
title: チュートリアル:Power BI で Azure Data Explorer のデータを視覚化する
description: このチュートリアルでは、Power BI で Azure データ エクスプローラーに接続して、データを視覚化する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: 70a06b75db9ff49222c2de4aa78519b32d863478
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59049882"
---
# <a name="tutorial-visualize-data-from-azure-data-explorer-in-power-bi"></a>チュートリアル:Power BI で Azure Data Explorer のデータを視覚化する

Azure Data Explorer は、ログと利用統計情報データのための高速で拡張性に優れたデータ探索サービスです。 Power BI はビジネス分析ソリューションであり、データを視覚化して、組織全体で結果を共有することができます。 このチュートリアルでは、最初に Azure データ エクスプローラーでビジュアルをレンダリングする方法を説明します。 次に、Azure データ エクスプローラーを Power BI と接続し、サンプル データに基づいてレポートを作成して、レポートを Power BI サービスに発行します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。 Power BI Pro にサインアップしていない場合は、[無料試用版にサインアップ](https://app.powerbi.com/signupredirect?pbi_source=web)してから始めてください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure データ エクスプローラーでビジュアルをレンダリングする
> * Power BI Desktop で Azure データ エクスプローラーに接続する
> * Power BI Desktop でデータを操作する
> * ビジュアルでレポートを作成する
> * レポートを発行して共有する

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、Azure と Power BI のサブスクリプションに加えて、以下のものが必要です。

* [テスト用のクラスターとデータベース](create-cluster-database-portal.md)

* [StormEvents サンプル データ](ingest-sample-data.md)。 [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (**[無料ダウンロード]** を選択)

## <a name="render-visuals-in-azure-data-explorer"></a>Azure データ エクスプローラーでビジュアルをレンダリングする

Power BI に進む前に、Azure データ エクスプローラーでビジュアルをレンダリングする方法を見ておきましょう。 これは、簡単な分析に適しています。

1. [https://dataexplorer.azure.com](https://dataexplorer.azure.com) にサインインします。

1. 左側のウィンドウで、StormEvents サンプル データを含むテスト データベースを選択します。

1. 右側のウィンドウに次のクエリを貼り付けて、**[Run]\(実行\)** を選択します。

    ```Kusto
    StormEvents
    | summarize event_count=count() by State
    | where event_count > 1800
    | project State, event_count
    | sort by event_count
    | render columnchart
    ```

    このクエリでは、州別に気象イベントがカウントされます。 次に、1,800 以上の気象イベントがあるすべての州の縦棒グラフがレンダリングされます。

    ![イベントの縦棒グラフ](media/visualize-power-bi/events-column-chart.png)

1. 右側のウィンドウに次のクエリを貼り付けて、**[Run]\(実行\)** を選択します。

    ```Kusto
    StormEvents
    | where State == "WASHINGTON" and StartTime >= datetime(2007-07-01) and StartTime <= datetime(2007-07-31)
    | summarize StormCount = count() by EventType
    | render piechart
    ```

    このクエリでは、ワシントン州の 7 月の気象イベントが種類別にカウントされます。 その後、各イベント種類の割合を示す円グラフがレンダリングされます。

    ![イベントの円グラフ](media/visualize-power-bi/events-pie-chart.png)

次に Power BI について説明しますが、Azure データ エクスプローラーではビジュアルに関してさらに多くのことを行えます。

## <a name="connect-to-azure-data-explorer"></a>Azure データ エクスプローラーに接続する

Power BI Desktop で Azure データ エクスプローラーに接続します。

1. Power BI Desktop の **[ホーム]** タブで、**[データの取得]**、**[詳細]** の順に選択します。

    ![データを取得する](media/visualize-power-bi/get-data-more.png)

1. *Azure Data Explorer* を検索し、**[Azure Data Explorer (Beta)]\(Azure Data Explorer (ベータ)\)** を選択して、**[接続]** を選択します。

    ![データを検索して取得する](media/visualize-power-bi/search-get-data.png)

1. **[コネクタのプレビュー]** ページで、**[続行]** を選択します。

1. 次の画面で、テスト クラスターとデータベースの名前を入力します。 クラスターは、`https://<ClusterName>.<Region>.kusto.windows.net` の形式にする必要があります。 テーブルの名前には「*StormEvents*」と入力します。 他のオプションはすべて既定値のままにして、**[OK]** を選択します。

    ![クラスター、データベース、テーブルのオプション](media/visualize-power-bi/cluster-database-table.png)

1. データ プレビュー画面で、**[編集]** を選択します。

    テーブルが Power Query エディターで開き、データをインポートする前に行と列を編集できます。

## <a name="work-with-data-in-power-bi-desktop"></a>Power BI Desktop でデータを操作する

Azure データ エクスプローラーに接続したので、Power Query エディターでデータを編集します。 **BeginLat** 列が null 値である行を削除し、**StormSummary** JSON 列全体を削除します。 これらは簡単な操作ですが、データをインポートするときに複雑な変換を実行することもできます。

1. **BeginLat** 列の矢印を選択して、**null** のチェック ボックスをオフにし、**[OK]** を選択します。

    ![列をフィルター処理する](media/visualize-power-bi/filter-column.png)

1. **[StormSummary]** 列見出しを右クリックし、**[削除]** を選択します。

    ![列を削除する](media/visualize-power-bi/remove-column.png)

1. **[クエリの設定]** ウィンドウで、名前を *Query1* から *StormEvents* に変更します。

    ![クエリの名前を変更する](media/visualize-power-bi/query-name.png)

1. リボンの **[ホーム]** タブで、**[閉じて適用]** を選択します。

    ![閉じて適用する](media/visualize-power-bi/close-apply.png)

    変更が適用されて、サンプル データが "*データ モデル*" にインポートされます。 次の数ステップでは、そのモデルを充実させる方法を説明します。 やはり、これは何ができるかを把握するための簡単な例です。

1. メイン ウィンドウの左側で、データ ビューを選択します。

    ![データ ビュー](media/visualize-power-bi/data-view.png)

1. リボンの **[モデリング]** タブで、**[新しい列]** を選択します。

    ![新しい列](media/visualize-power-bi/new-column.png)

1. 次の Data Analysis Expressions (DAX) 式を数式バーに入力して、Enter キーを押します。

    ```DAX
    DurationHours = DATEDIFF(StormEvents[StartTime], StormEvents[EndTime], hour)
    ```

    ![数式バー](media/visualize-power-bi/formula-bar.png)

    この式は、各気象イベントが継続した時間数を計算する *DurationHours* 列を作成します。 次のセクションのビジュアルでは、この列を使用します。

1. テーブルを右側にスクロールして、列を表示するます。

## <a name="create-a-report-with-visuals"></a>ビジュアルでレポートを作成する

データをインポートして、データ モデルを改善したので、いよいよビジュアルでレポートを作成します。 イベントの継続時間に基づく縦棒グラフと、作物の被害を示すマップを追加します。

1. ウィンドウの左側で、レポート ビューを選択します。

    ![レポート ビュー](media/visualize-power-bi/report-view.png)

1. **[視覚化]** ウィンドウで、集合縦棒グラフを選択します。

    ![縦棒グラフを追加する](media/visualize-power-bi/add-column-chart.png)

    空のグラフがキャンバスに追加されます。

    ![空のグラフ](media/visualize-power-bi/blank-chart.png)

1. **[フィールド]** の一覧で、**DurationHours** と **State** を選択します。

    ![フィールドを選択する](media/visualize-power-bi/select-fields.png)

    1 年間の気象イベントの合計時間数を州別に示すグラフになります。

    ![継続時間の縦棒グラフ](media/visualize-power-bi/duration-column-chart.png)

1. 縦棒グラフの外側のキャンバス上の任意の場所をクリックします。

1. **[視覚化]** ウィンドウで、マップを選択します。

    ![マップを追加する](media/visualize-power-bi/add-map.png)

1. **[フィールド]** の一覧で、**CropDamage** と **State** を選択します。 米国の州がはっきりわかるようにマップのサイズを変更します。

    ![作物被害のマップ](media/visualize-power-bi/crop-damage-map.png)

    バブルのサイズは、作物被害額 (ドル単位) を表します。 バブルをポイントすると詳細が表示されます。

1. レポートが次の図のような見た目になるように、ビジュアルを移動してサイズを変更します。

    ![完成したレポート](media/visualize-power-bi/finished-report.png)

1. レポートを *storm-events.pbix* という名前で保存します。

## <a name="publish-and-share-the-report"></a>レポートを発行して共有する

これまで Power BI で行った作業はすべて、Power BI Desktop を使用したローカルなものです。 次に、レポートを Power BI サービスに発行して、他のユーザーと共有できるようにします。

1. Power BI Desktop のリボンの **[ホーム]** タブで、**[発行]** を選択します。

    ![[発行] ボタン](media/visualize-power-bi/publish-button.png)

1. まだ Power BI にサインインしていない場合は、サインイン プロセスを実行します。

1. **[マイ ワークスペース]**、**[選択]** の順に選択します。

    ![ワークスペースを選択](media/visualize-power-bi/select-workspace.png)

1. 発行が完了したら、**[Power BI で storm-events.pbix を開く]** を選択します。

    ![発行は成功しました](media/visualize-power-bi/publishing-succeeded.png)

    Power BI Desktop で定義したものと同じビジュアルおよびレイアウトのレポートが、サービスで開きます。

1. レポートの右上隅にある **[共有]** を選択します。

    ![[共有] ボタン](media/visualize-power-bi/share-button.png)

1. **[レポートの共有]** 画面で、組織内の同僚を追加し、メモを追加して、**[共有]** を選択します。

    ![レポートを共有する](media/visualize-power-bi/share-report.png)

    同僚が適切なアクセス許可を持っている場合は、共有を受けたレポートにアクセスできます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

作成したレポートを残しておきたくない場合は、単に *storm-events.pbix* ファイルを削除します。 発行したレポートを削除する場合は、以下の手順のようにします。

1. **[マイ ワークスペース]** で **[レポート]** まで下にスクロールし、**storm-events** を探します。

1. **storm-events** の隣の省略記号 **[...]** を選択して、**[削除]** を選択します。

    ![レポートを削除する](media/visualize-power-bi/remove-report.png)

1. 削除を確認します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [クエリを作成する](write-queries.md)
