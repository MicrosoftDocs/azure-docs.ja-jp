---
title: 'チュートリアル:Azure HDInsight での Power BI を使用した Apache Spark データの分析 '
description: Microsoft Power BI を使用して HDInsight クラスターに格納されている Apache Spark データを視覚化する
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/07/2018
ms.openlocfilehash: c058cfa3bc9fa976726731cedeb80eb76a1f0810
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2019
ms.locfileid: "53999273"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>チュートリアル:HDInsight での Power BI を使用した Apache Spark データの分析 

[Microsoft Power BI](https://powerbi.microsoft.com/) を使用して [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) で [Apache Spark](https://spark.apache.org/) クラスター内のデータを視覚化する方法について説明します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Power BI を使用して Spark データを視覚化する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

* **「[チュートリアル:Azure HDInsight での Apache Spark クラスターへのデータの読み込みとクエリの実行](./apache-spark-load-data-run-query.md)」の記事を完了します**。
* **Power BI**: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) と [Power BI 試用版サブスクリプション](https://app.powerbi.com/signupredirect?pbi_source=web) (省略可能)。


## <a name="verify-the-data"></a>データの検証

[前のチュートリアル](apache-spark-load-data-run-query.md)で作成した [Jupyter Notebook](https://jupyter.org/) には、`hvac` テーブルを作成するためのコードが含まれています。 このテーブルは、**\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv** のすべての HDInsight Spark クラスターで使用可能な CSV ファイルに基づいています。 データの検証に使用する手順は、以下のとおりです。

1. Jupyter Notebook から次のコードを貼り付けて、**Shift + Enter** キーを押します。 このコードによってテーブルの存在が検証されます。

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    出力は次のようになります。

    ![Spark でのテーブルの表示](./media/apache-spark-use-bi-tools/show-tables.png)

    このチュートリアルを開始する前に Notebook を閉じると、`hvactemptable` はクリーンアップされるため、出力に含まれません。  BI ツールからアクセスできるのは、metastore に保存された Hive テーブル (**isTemporary** 列に **False** と表示される) のみです。 このチュートリアルでは、作成した **hvac** テーブルに接続します。

2. 次のコードを空のセルに貼り付け、**Shift + Enter** キーを押します。 このコードによってテーブル内のデータが検証されます。

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    出力は次のようになります。

    ![Spark での hvac テーブルの行の表示](./media/apache-spark-use-bi-tools/select-limit.png)

3. Notebook の **[ファイル]** メニューの **[Close and Halt]\(閉じて停止\)** をクリックします。 Notebook をシャットダウンしてリソースを解放します。 

## <a name="visualize-the-data"></a>データの視覚化

このセクションでは、Power BI を使用して Spark クラスター データから視覚エフェクト、レポート、およびダッシュボードを作成します。 

### <a name="create-a-report-in-power-bi-desktop"></a>Power BI Desktop でレポートを作成する
Spark を操作する最初のステップでは、Power BI Desktop のクラスターに接続し、クラスターからデータを読み込み、そのデータを基に基本的な視覚エフェクトを作成します。

> [!NOTE]  
> この記事で説明するコネクタは、現在プレビューの段階です。 お客様のフィードバックを [Power BI コミュニティ](https://community.powerbi.com/) サイトや [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas) を通じてお寄せください。

1. [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) を開きます。
1. **[ホーム]** タブで、**[データの取得]**、**[詳細]** の順にクリックします。

    ![HDInsight Apache Spark から Power BI Desktop にデータを取得](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Apache Spark BI から Power BI にデータを取得")


2. 検索ボックスに「`Spark`」と入力し、**[Azure HDInsight Spark]** を選択して、**[接続]** をクリックします。

    ![Apache Spark BI から Power BI にデータを取得](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Apache Spark BI から Power BI にデータを取得")

3. クラスターの URL (`mysparkcluster.azurehdinsight.net` の形式) を入力し、**[DirectQuery]** を選択して **[OK]** をクリックします。

    Spark では、いずれかのデータ接続モードをご利用いただけます。 DirectQuery を使用する場合、データセット全体を更新しなくても変更がレポートに反映されます。 データをインポートする場合は、データセットを更新して変更内容を確認する必要があります。 DirectQuery をいつ、どのように使用するかの詳細については、「[Power BI で DirectQuery を使用する](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/)」をご覧ください。 

4. HDInsight のログイン アカウント情報を入力し、**[接続]** をクリックします。 既定のアカウント名は *admin* です。

5. `hvac` テーブルを選択して、データのプレビューが表示されるのを待ち、**[読み込み]** をクリックします。

    ![Spark クラスターのユーザー名とパスワード](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark クラスターのユーザー名とパスワード")

    Spark クラスターに接続し、`hvac` テーブルからデータを読み込むために必要な情報が Power BI Desktop に整いました。 **[フィールド]** ウィンドウにテーブルとその列が表示されます。  次のスクリーンショットをご覧ください。

6. 各ビルの目標温度と実際の温度の差を視覚化します。 

    1. **[視覚エフェクト]** ウィンドウで **[面グラフ]** を選択します。 
    2. **[BuildingID]** フィールドを**軸**にドラッグし、**[ActualTemp]** と **[TargetTemp]** の各フィールドを**値**にドラッグします。

        ![Apache Spark BI を使用して Spark データ視覚化を作成](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Apache Spark BI を使用して Spark データ視覚化を作成")

        図は次のようになります。

        ![Apache Spark BI を使用して Spark データ視覚化を作成](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Apache Spark BI を使用して Spark データ視覚化を作成")

        既定では、**ActualTemp** および **TargetTemp** の合計が表示されます。 [視覚化] ウィンドウで **ActualTemp** と **TragetTemp** の隣にある下矢印をクリックすると、**[合計]** が選択されていることを確認できます。

    3. [視覚化] ウィンドウで **ActualTemp** と **TragetTemp** の隣にある下矢印をクリックし、**[平均]** を選択して、各ビルの実際の温度および目標温度の平均を取得します。

        ![Apache Spark BI を使用して Spark データ視覚化を作成](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Apache Spark BI を使用して Spark データ視覚化を作成")

        次のスクリーンショットのようにデータが視覚化されます。 グラフの上にカーソルを移動すると、関連データを含むツール ヒントが表示されます。

        ![Apache Spark BI を使用して Spark データ視覚化を作成](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Apache Spark BI を使用して Spark データ視覚化を作成")

7. **[ファイル]**、**[保存]** の順にクリックして、ファイル名 `BuildingTemperature.pbix` を入力します。 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Power BI サービスにレポートを発行する (省略可能)

Power BI サービスを使用すると、組織全体でレポートとダッシュボードを共有できます。 このセクションでは、まずデータセットとレポートを発行します。 次に、このレポートをダッシュボードにピン留めします。 通常、ダッシュボードは、レポート内のデータのサブセットにフォーカスするために使用します。レポート内にある視覚エフェクトは 1 つのみですが、それでも手順を実行するのに役立ちます。

1. Power BI Desktop を開きます。
2. **[ホーム]** タブで **[発行]** をクリックします。

    ![Power BI Desktop から発行](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Power BI Desktop から発行")

2. データセットを発行してレポートするワークスペースを選択して、**[選択]** をクリックします。 次の図では、既定値の **[マイ ワークスペース]** が選択されています。

    ![データセットを発行してレポートするワークスペースを選択する](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "データセットを発行してレポートするワークスペースを選択する") 

3. 発行に成功したら、**[Open 'BuildingTemperature.pbix' in Power BI]\('BuildingTemperature.pbix' を Power BI で開く\)** をクリックします。

    ![発行の成功、クリックして資格情報を入力](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "発行の成功、クリックして資格情報を入力") 

4. Power BI サービスで、**[資格情報を入力する]** をクリックします。

    ![Power BI サービスで資格情報を入力する](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Power BI サービスで資格情報を入力する")

5. **[資格情報を編集]** をクリックします。

    ![Power BI サービスで資格情報を編集する](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Power BI サービスで資格情報を編集する")

6. HDInsight のログイン アカウント情報を入力し、**[サインイン]** をクリックします。 既定のアカウント名は *admin* です。

    ![Spark クラスターへのサインイン](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Spark クラスターへのサインイン")

7. 左ウィンドウ枠で **[ワークスペース]** > **[マイ ワークスペース]** > **[レポート]** の順に移動して、**[BuildingTemperature]** をクリックします。

    ![左ウィンドウ枠の [レポート] に表示されるレポート](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "左ウィンドウ枠の [レポート] に表示されるレポート")

    左ウィンドウ枠の **[データセット]** にも **[BuildingTemperature]** が表示されます。

    これで、Power BI Desktop で作成したビジュアルが Power BI サービスで使用できるようになりました。 

8. 視覚エフェクトにカーソルを合わせ、右上隅のピン アイコンをクリックします。

    ![Power BI サービスのレポート](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Power BI サービスのレポート")

9. [新しいダッシュボード] を選択して、名前 `Building temperature` を入力し、**[ピン留め]** をクリックします。

    ![新しいダッシュボードにピン留めする](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "新しいダッシュボードにピン留めする")

10. レポートで、**[ダッシュボードへ移動]** をクリックします。 

ビジュアルはダッシュボードにピン留めされます。他のビジュアルをレポートに追加して、同じダッシュボードにピン留めすることもできます。 レポートとダッシュボードの詳細については、「[Power BI のレポート](https://powerbi.microsoft.com/documentation/powerbi-service-reports/)」のほか、[Power BI のダッシュボード](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)に関する記事を参照してください。

<!--
## <a name="tableau"></a>Use Tableau Desktop 

> [!NOTE]
> This section is applicable only for Spark 1.5.2 clusters created in Azure HDInsight.
>
>

1. Install [Tableau Desktop](https://www.tableau.com/products/desktop) on the computer where you are running this Apache Spark BI tutorial.

2. Make sure that computer also has Microsoft Spark ODBC driver installed. You can install the driver from [here](https://go.microsoft.com/fwlink/?LinkId=616229).

1. Launch Tableau Desktop. In the left pane, from the list of server to connect to, click **Spark SQL**. If Spark SQL is not listed by default in the left pane, you can find it by click **More Servers**.
2. In the Spark SQL connection dialog box, provide the values as shown in the screenshot, and then click **OK**.

    ![Connect to a cluster for Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Connect to a cluster for Apache Spark BI")

    The authentication drop-down lists **Microsoft Azure HDInsight Service** as an option, only if you installed the [Microsoft Spark ODBC Driver](https://go.microsoft.com/fwlink/?LinkId=616229) on the computer.
3. On the next screen, from the **Schema** drop-down, click the **Find** icon, and then click **default**.

    ![Find schema for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Find schema for Apache Spark BI")
4. For the **Table** field, click the **Find** icon again to list all the Hive tables available in the cluster. You should see the **hvac** table you created earlier using the notebook.

    ![Find table for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Find table for Apache Spark BI")
5. Drag and drop the table to the top box on the right. Tableau imports the data and displays the schema as highlighted by the red box.

    ![Add tables to Tableau for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Add tables to Tableau for Apache Spark BI")
6. Click the **Sheet1** tab at the bottom left. Make a visualization that shows the average target and actual temperatures for all buildings for each date. Drag **Date** and **Building ID** to **Columns** and **Actual Temp**/**Target Temp** to **Rows**. Under **Marks**, select **Area** to use an area map for Spark data visualization.

     ![Add fields for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Add fields for Spark data visualization")
7. By default, the temperature fields are shown as aggregate. If you want to show the average temperatures instead, you can do so from the drop-down, as shown in the following screenshot:

    ![Take average of temperature for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Take average of temperature for Spark data visualization")

8. You can also super-impose one temperature map over the other to get a better feel of difference between target and actual temperatures. Move the mouse to the corner of the lower area map until you see the handle shape highlighted in a red circle. Drag the map to the other map on the top and release the mouse when you see the shape highlighted in red rectangle.

    ![Merge maps for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Merge maps for Spark data visualization")

     Your data visualization should change as shown in the screenshot:

    ![Tableau output for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Tableau output for Spark data visualization")
9. Click **Save** to save the worksheet. You can create dashboards and add one or more sheets to it.
-->

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

- Power BI を使用して Azure Spark データを視覚化する。

次の記事に進んで、Spark に登録したデータを Power BI などの BI 分析ツールに取り込む方法を確認してください。 
> [!div class="nextstepaction"]
> [Apache Spark ストリーミング ジョブを実行する](apache-spark-eventhub-streaming.md)

