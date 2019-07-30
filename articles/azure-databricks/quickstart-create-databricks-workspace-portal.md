---
title: クイック スタート:Azure portal を使用して Azure Databricks で Spark ジョブを実行する
description: このクイックスタートでは、Azure Portal を使用して Azure Databricks ワークスペースと Apache Spark クラスターを作成し、Spark ジョブを実行する方法を示します。
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 3da0e5bb9ecb8557ac8b51b3aa6faf60a52dddf9
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68312107"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>クイック スタート: Azure portal を使用して Azure Databricks 上で Spark ジョブを実行する

このクイック スタートでは、Azure portal を使って Azure Databricks ワークスペースと Apache Spark クラスターを作成します。 クラスターでジョブを実行し、カスタム グラフを使用して、ボストンの安全データからリアルタイムのレポートを作成します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインします

[Azure Portal](https://portal.azure.com) にサインインします。 Azure 無料試用版サブスクリプションを使用してこのチュートリアルを実行することはできません。 無料アカウントを使用して Azure Databricks クラスターを作成するには、クラスターを作成する前に、プロファイルにアクセスし、サブスクリプションを**従量課金制**に変更します。 詳細については、[Azure 無料アカウント](https://azure.microsoft.com/free/)に関するページをご覧ください。

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks ワークスペースを作成する

このセクションでは、Azure Portal を使って Azure Databricks ワークスペースを作成します。

1. Azure portal で、 **[リソースの作成]**  >  **[分析]**  >  **[Azure Databricks]** の順に選択します。

    ![Azure Portal の Databricks](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Azure Portal の Databricks")

2. **[Azure Databricks サービス]** で値を指定して、Databricks ワークスペースを作成します。

    ![Azure Databricks ワークスペースを作成する](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Azure Databricks ワークスペースを作成する")

    次の値を指定します。
    
    |プロパティ  |説明  |
    |---------|---------|
    |**ワークスペース名**     | Databricks ワークスペースの名前を指定します        |
    |**サブスクリプション**     | ドロップダウンから Azure サブスクリプションを選択します。        |
    |**リソース グループ**     | 新しいリソース グループを作成するか、既存のリソース グループを使用するかを指定します。 リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 詳しくは、[Azure リソース グループの概要](../azure-resource-manager/resource-group-overview.md)に関するページをご覧ください。 |
    |**Location**     | **[米国西部 2]** を選択します。 使用可能な他のリージョンについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」をご覧ください。        |
    |**価格レベル**     |  **Standard**、**Premium**、**Trial** のいずれかを選択します。 これらのレベルの詳細については、[Databricks の価格に関するページ](https://azure.microsoft.com/pricing/details/databricks/)を参照してください。       |

    **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、 **[作成]** をクリックします。

4. ワークスペースの作成には数分かかります。 ワークスペースの作成中に、 **[通知]** でデプロイの状態を表示できます。

    ![Databricks のデプロイのタイル](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Databricks のデプロイのタイル")

## <a name="create-a-spark-cluster-in-databricks"></a>Databricks に Spark クラスターを作成する

> [!NOTE]
> 無料アカウントを使用して Azure Databricks クラスターを作成するには、クラスターを作成する前に、プロファイルにアクセスし、サブスクリプションを**従量課金制**に変更します。 詳細については、[Azure 無料アカウント](https://azure.microsoft.com/free/)に関するページをご覧ください。

1. Azure Portal で、作成した Databricks ワークスペースに移動して、 **[Launch Workspace]\(ワークスペースの起動\)** をクリックします。

2. Azure Databricks ポータルにリダイレクトされます。 ポータルから、 **[新しいクラスター]** をクリックします。

    ![Azure の Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Azure の Databricks")

3. **[New cluster]\(新しいクラスター\)** ページで、クラスターを作成するための値を指定します。

    ![Azure で Databricks Spark クラスターを作成する](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Azure で Databricks Spark クラスターを作成する")

    以下を除くすべての値は、既定値のままにします。

   * クラスターの名前を入力します。
   * この記事では、**5.2** ランタイムを使用してクラスターを作成します。
   * **[Terminate after \_\_ minutes of inactivity]** \(アクティビティが \_\_ 分ない場合は終了する\) チェック ボックスをオンにします。 クラスターが使われていない場合にクラスターを終了するまでの時間 (分単位) を指定します。
    
     **[クラスターの作成]** を選択します。 クラスターが実行されたら、ノートブックをクラスターにアタッチして、Spark ジョブを実行できます。

クラスターの作成について詳しくは、[Azure Databricks での Spark クラスターの作成に関するページ](https://docs.azuredatabricks.net/user-guide/clusters/create.html)をご覧ください。

## <a name="run-a-spark-sql-job"></a>Spark SQL ジョブを実行する

次のタスクを実行して Databricks でノートブックを作成し、Azure オープン データセットからデータを読み取るようにノートブックを構成してから、そのデータに対して Spark SQL ジョブを実行します。

1. 左側のウィンドウで、 **[Azure Databricks]** を選択します。 **[Common Tasks] (一般的なタスク)** から、 **[New Notebook] (新しいノートブック)** を選択します。

    ![Databricks でノートブックを作成する](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Databricks でノートブックを作成する")

2. **[Create Notebook] (ノートブックの作成)** ダイアログ ボックスで、名前を入力し、言語として **[Python]** を選択してから、前に作成した Spark クラスターを選択します。

    ![Databricks でノートブックを作成する](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Databricks でノートブックを作成する")

    **作成** を選択します。

3. この手順では、[Azure オープン データセット](https://azure.microsoft.com/services/open-datasets/catalog/boston-safety-data/#AzureDatabricks)からボストンの安全に関するデータを含む Spark データフレームを作成し、SQL を使用してそのデータにクエリを実行します。

   次のコマンドは、Azure Storage のアクセス情報を設定します。 この PySpark コードを最初のセルに貼り付け、**Shift + Enter** キーを使用してコードを実行します。

   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "citydatacontainer"
   blob_relative_path = "Safety/Release/city=Boston"
   blob_sas_token = r"?st=2019-02-26T02%3A34%3A32Z&se=2119-02-27T02%3A34%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=XlJVWA7fMXCSxCKqJm8psMOh0W4h7cSYO28coRqF2fs%3D"
   ```

   次のコマンドは、Spark がリモートで BLOB ストレージから読み取ることを許可します。 この PySpark コードを次のセルに貼り付け、**Shift + Enter** キーを使用してコードを実行します。

   ```python
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
   print('Remote blob path: ' + wasbs_path)
   ```

   次のコマンドは、データフレームを作成します。 この PySpark コードを次のセルに貼り付け、**Shift + Enter** キーを使用してコードを実行します。

   ```python
   df = spark.read.parquet(wasbs_path)
   print('Register the DataFrame as a SQL temporary view: source')
   df.createOrReplaceTempView('source')
   ```

4. **source** という名前の一時ビューから上位 10 行のデータを返す SQL ステートメントを実行します。 この PySpark コードを次のセルに貼り付け、**Shift + Enter** キーを使用してコードを実行します。

   ```python
   print('Displaying top 10 rows: ')
   display(spark.sql('SELECT * FROM source LIMIT 10'))
   ```

5. 次のスクリーンショットのような表形式の出力が表示されます (一部の列のみ示してあります)。

    ![サンプル データ](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "サンプルの JSON データ")

6. 次に、他のソースではなく Citizens Connect App や City Worker App を使用して安全に関するいくつのイベントが報告されているかを表示するために、このデータの視覚的な表現を作成します。 表形式の出力の一番下から **[横棒グラフ]** アイコンを選択し、 **[Plot Options] (プロット オプション)** をクリックします。

    ![棒グラフを作成する](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "棒グラフを作成する")

8. **[Customize Plot]\(プロットのカスタマイズ\)** で、スクリーンショットに示すように値をドラッグ アンド ドロップします。

    ![円グラフをカスタマイズする](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "横棒グラフをカスタマイズする")

   * **[キー]** を **[source]** に設定します。
   * **[値]** を **[<\id>]** に設定します。
   * **[Aggregation]\(集計\)** を **[COUNT]\(個数\)** に設定します。
   * **[Display type] (表示の種類)** を **[円グラフ]** に設定します。

     **[Apply]** をクリックします。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

記事を完了したら、クラスターを終了できます。 そのためには、Azure Databricks ワークスペースの左側のウィンドウで、 **[クラスター]** を選択します。 終了するクラスターで、 **[アクション]** 列の下にある省略記号をポイントし、 **[終了]** アイコンを選択します。

![Databricks クラスターを停止する](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Databricks クラスターを停止する")

クラスター作成時に **[Terminate after \_\_ minutes of inactivity]** \(アクティビティが \_\_ 分ない場合は終了する\) チェック ボックスをオンにしていた場合、手動で終了しなくともクラスターは自動で停止します。 このような場合、クラスターは、一定の時間だけ非アクティブな状態が続くと自動的に停止します。

## <a name="next-steps"></a>次の手順

この記事では、Azure Databricks で Spark クラスターを作成し、Azure オープン データセットからのデータを使用して Spark ジョブを実行しました。 [Spark のデータ ソース](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html)を見て、他のデータ ソースから Azure Databricks にデータをインポートする方法を学習することもできます。 次の記事に進んで、Azure Databricks を使った ETL (データの抽出、変換、読み込み) 操作の実行方法について学びましょう。

> [!div class="nextstepaction"]
>[Azure Databricks を使ったデータの抽出、変換、読み込み](databricks-extract-load-sql-data-warehouse.md)
