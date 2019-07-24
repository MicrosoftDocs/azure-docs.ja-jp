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
ms.date: 07/23/2018
ms.custom: mvc
ms.openlocfilehash: 3a58a34271562b127735a4682046a7b646d0c085
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60010519"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>クイック スタート: Azure portal を使用して Azure Databricks で Spark ジョブを実行する

このクイックスタートでは、Azure Databricks ワークスペースと、そのワークスペース内の Apache Spark クラスターを作成する方法を示します。 最後に、Databricks クラスターで Spark ジョブを実行する方法を説明します。 Azure Databricks の詳細については、「[Azure Databricks とは](what-is-azure-databricks.md)」を参照してください。

このクイック スタートでは、Spark ジョブの一環として、ラジオ チャンネルのサブスクリプション データを分析し、人口統計学的属性に基づく無料/有料使用についての分析情報を取得します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする

[Azure Portal](https://portal.azure.com) にログインします。

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks ワークスペースを作成する

このセクションでは、Azure Portal を使って Azure Databricks ワークスペースを作成します。

1. Azure Portal で、**[リソースの作成]** > **[データ + 分析]** > **[Azure Databricks]** の順に選択します。

    ![Azure Portal の Databricks](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Azure Portal の Databricks")

2. **[Azure Databricks サービス]** で値を指定して、Databricks ワークスペースを作成します。

    ![Azure Databricks ワークスペースを作成する](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Azure Databricks ワークスペースを作成する")

    次の値を指定します。
    
    |プロパティ  |説明  |
    |---------|---------|
    |**ワークスペース名**     | Databricks ワークスペースの名前を指定します        |
    |**サブスクリプション**     | ドロップダウンから Azure サブスクリプションを選択します。        |
    |**リソース グループ**     | 新しいリソース グループを作成するか、既存のリソース グループを使用するかを指定します。 リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 詳しくは、[Azure リソース グループの概要](../azure-resource-manager/resource-group-overview.md)に関するページをご覧ください。 |
    |**場所**     | **[米国東部 2]** を選択します。 使用可能な他のリージョンについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」をご覧ください。        |
    |**価格レベル**     |  **Standard** と **Premium** のいずれかを選択します。 これらのレベルの詳細については、[Databricks の価格に関するページ](https://azure.microsoft.com/pricing/details/databricks/)を参照してください。       |

    **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** をクリックします。

4. ワークスペースの作成には数分かかります。 ワークスペース作成時に、ポータルの右側に **[Submitting deployment for Azure Databricks]\(Azure Databricks のデプロイを送信しています\)** タイルが表示されます。 このタイルを表示するために、ダッシュボードを右へスクロールしなければならない場合があります。 スクリーンの上部に進行状況バーも表示されます。 いずれかの領域で進行状況を確認できます。

    ![Databricks のデプロイのタイル](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Databricks のデプロイのタイル")

## <a name="create-a-spark-cluster-in-databricks"></a>Databricks に Spark クラスターを作成する

> [!NOTE]
> 無料アカウントを使用して Azure Databricks クラスターを作成するには、クラスターを作成する前に、プロファイルにアクセスし、サブスクリプションを**従量課金制**に変更します。 詳細については、[Azure 無料アカウント](https://azure.microsoft.com/free/)に関するページをご覧ください。

1. Azure Portal で、作成した Databricks ワークスペースに移動して、**[Launch Workspace]\(ワークスペースの起動\)** をクリックします。

2. Azure Databricks ポータルにリダイレクトされます。 ポータルで **[クラスター]** をクリックします。

    ![Azure の Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Azure の Databricks")

3. **[New cluster]\(新しいクラスター\)** ページで、クラスターを作成するための値を指定します。

    ![Azure で Databricks Spark クラスターを作成する](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Azure で Databricks Spark クラスターを作成する")

    以下を除くすべての値は、既定値のままにします。

   * クラスターの名前を入力します。
   * この記事では、**4.0** ランタイムを使用してクラスターを作成します。
   * **[Terminate after \_\_ minutes of inactivity]** \(アクティビティが \_\_ 分ない場合は終了する\) チェック ボックスをオンにします。 クラスターが使われていない場合にクラスターを終了するまでの時間 (分単位) を指定します。
    
     **[クラスターの作成]** を選択します。 クラスターが実行されたら、ノートブックをクラスターにアタッチして、Spark ジョブを実行できます。

クラスターの作成について詳しくは、[Azure Databricks での Spark クラスターの作成に関するページ](https://docs.azuredatabricks.net/user-guide/clusters/create.html)をご覧ください。


## <a name="download-a-sample-data-file"></a>サンプル データ ファイルをダウンロードする
サンプル JSON データ ファイルをダウンロードし、Azure BLOB ストレージに保存します。

1. このサンプル JSON データ ファイルを [GitHub から](https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json)ローカル コンピューターにダウンロードします。 右クリックし、名前を付けて、生ファイルをローカルに保存します。

2. まだストレージ アカウントがない場合は、作成します。
   - Azure Portal で、**[リソースの作成]** を選択します。 **[ストレージ]** カテゴリを選択し、**[ストレージ アカウント]** を選択します
   - ストレージ アカウントに一意の名前を指定してください。
   - **アカウントの種類**を選択します。**Blob Storage**
   - **リソース グループ**名を選択します。 Databricks ワークスペースを作成したのと同じリソース グループを使用します。
    
     詳細については、[Azure Blob ストレージ アカウントの作成](../storage/common/storage-quickstart-create-account.md)に関するページを参照してください。

3. Blob ストレージ アカウントにストレージ コンテナーを作成し、サンプル json ファイルをコンテナーにアップロードします。 ファイルのアップロードには、Azure portal または [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) を使用することができます。

    - Azure portal でストレージ アカウントを開きます。
    - **[BLOB]** を選択します。
    - **[+ コンテナー]** を選択して、新しい空のコンテナーを作成します。
    - コンテナーの**名前**を指定します (`databricks` など)。
    - **[Private (non anonymous access)]\(プライベート (非匿名アクセス)\)** アクセス レベルを選択します。
    - コンテナーが作成されたら、コンテナー名を選択します。
    - **[アップロード]** ボタンを選択します。
    - **[ファイル]** ページで**フォルダー アイコン**を選択し、アップロードするサンプル ファイル `small_radio_json.json` を探して選択します。
    - **[アップロード]** を選択して、ファイルをアップロードします。

## <a name="run-a-spark-sql-job"></a>Spark SQL ジョブを実行する
次のタスクで、Databricks にノートブックを作成し、Azure BLOB ストレージ アカウントからデータを読み取るようにノートブックを構成して、データに対して Spark SQL ジョブを実行します。

1. 左側のウィンドウで、**[ワークスペース]** をクリックします。 **[ワークスペース]** ドロップダウンの **[作成]** をクリックして、**[ノートブック]** をクリックします。

    ![Databricks でノートブックを作成する](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Databricks でノートブックを作成する")

2. **[Create Notebook]\(ノートブックの作成\)** ダイアログ ボックスで、名前を入力し、言語として **[Scala]** を選んで、前に作成した Spark クラスターを選びます。

    ![Databricks でノートブックを作成する](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Databricks でノートブックを作成する")

    **Create** をクリックしてください。

3. この手順では、Azure Storage アカウントを Databricks Spark クラスターに関連付けます。 この関連付けを実行する方法は 2 つあります。 Azure Storage アカウントを Databricks Filesystem (DBFS) にマウントする方法と、作成したアプリケーションから Azure Storage アカウントに直接アクセスする方法です。

    > [!IMPORTANT]
    >この記事では、**DBFS のストレージにマウントする方法**を使用します。 この方法では、マウントされたストレージがクラスター ファイル システム自体に確実に関連付けられます。 そのため、クラスターにアクセスするすべてのアプリケーションが、関連付けられているストレージも使用できます。 直接アクセスする方法は、アクセスの構成を行うアプリケーションに限定されます。
    >
    > マウントする方法を使用するには、Databricks ランタイム バージョン **4.0** で Spark クラスターを作成する必要があります。これは、この記事で選択したバージョンです。

    次のスニペットで、`{YOUR CONTAINER NAME}`、`{YOUR STORAGE ACCOUNT NAME}`、および `{YOUR STORAGE ACCOUNT ACCESS KEY}` を Azure Storage アカウントの適切な値に置き換えてください。 スニペットをノートブックの空のセルに貼り付け、Shift + Enter キーを押してコードのセルを実行します。

   * **DBFS を使用してストレージ アカウントにマウントする (推奨)**。 このスニペットでは、Azure Storage アカウントのパスが `/mnt/mypath` にマウントされています。 そのため、今後 Azure Storage アカウントにアクセスする場合は、完全なパスを指定する必要はありません。 単に `/mnt/mypath` を使用することができます。

         dbutils.fs.mount(
           source = "wasbs://{YOUR CONTAINER NAME}@{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net/",
           mountPoint = "/mnt/mypath",
           extraConfigs = Map("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net" -> "{YOUR STORAGE ACCOUNT ACCESS KEY}"))

   * **ストレージ アカウントに直接アクセスする**

         spark.conf.set("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net", "{YOUR STORAGE ACCOUNT ACCESS KEY}")

     ストレージ アカウント キーを取得する方法については、「[ストレージ アクセス キーの管理](../storage/common/storage-account-manage.md#access-keys)」を参照してください。

     > [!NOTE]
     > Azure Databricks 上の Spark クラスターで Azure Data Lake Store を使うこともできます。 方法については、[Azure Databricks での Data Lake Store の使用に関するページ](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)をご覧ください。

4. SQL ステートメントを実行し、サンプルの JSON データ ファイル **small_radio_json.json** のデータを使って、一時テーブルを作成します。 次のスニペットでは、プレースホルダーの値をコンテナー名およびストレージ アカウント名に置き換えます。 スニペットをノートブックのコード セルに貼り付けて、Shift + Enter キーを押します。 スニペットの `path` は、Azure ストレージ アカウントにアップロードしたサンプルの JSON ファイルの場所を示します。

    ```sql
    %sql
    DROP TABLE IF EXISTS radio_sample_data;
    CREATE TABLE radio_sample_data
    USING json
    OPTIONS (
     path "/mnt/mypath/small_radio_json.json"
    )
    ```

    コマンドが正常に完了すると、JSON ファイルのすべてのデータが Databricks クラスター内のテーブルとして取り込まれます。

    `%sql` 言語のマジック コマンドを使うと、ノートブックから SQL コードを実行できます (ノートブックが別の種類であっても)。 詳しくは、「[Mixing languages in a notebook](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook)」(ノートブックに言語を混在させる) をご覧ください。

5. 実行するクエリについて詳しく理解するため、サンプルの JSON データのスナップショットを見てみます。 次のスニペットをコード セルに貼り付けて、**Shift + Enter** キーを押します。

    ```sql
    %sql
    SELECT * from radio_sample_data
    ```

6. 次のスクリーンショットのような表形式の出力が表示されます (一部の列のみ示してあります)。

    ![サンプルの JSON データ](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "サンプルの JSON データ")

    サンプルには、ラジオ チャンネルの視聴者の性別 (列名: **gender**) および登録が無料か有料か (列名: **level**) に関するデータが含まれます。

7. このデータのビジュアル表現を作成し、各性別について、無料アカウント ユーザーの数と有料登録者の数がわかるようにします。 表形式の出力の下部で、**[Bar chart]\(棒グラフ\)** アイコンをクリックした後、**[Plot Options]\(プロット オプション\)** をクリックします。

    ![棒グラフを作成する](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "棒グラフを作成する")

8. **[Customize Plot]\(プロットのカスタマイズ\)** で、スクリーンショットに示すように値をドラッグ アンド ドロップします。

    ![棒グラフをカスタマイズする](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "棒グラフをカスタマイズする")

   * **[Keys]\(キー\)** を **gender** に設定します。
   * **[Series groupings]\(系列グループ\)** を **level** に設定します。
   * **[Values]\(値\)** を **level** に設定します。
   * **[Aggregation]\(集計\)** を **[COUNT]\(個数\)** に設定します。

     **[Apply]** をクリックします。

9. 出力は、次のスクリーンショットのようなビジュアル表現になります。

    ![棒グラフをカスタマイズする](./media/quickstart-create-databricks-workspace-portal/databricks-sql-query-output-bar-chart.png "棒グラフをカスタマイズする")

## <a name="clean-up-resources"></a>リソースのクリーンアップ

記事を完了したら、クラスターを終了できます。 そのためには、Azure Databricks ワークスペースの左側のウィンドウで、**[クラスター]** を選択します。 終了するクラスターで、**[アクション]** 列の下にある省略記号をポイントし、**[終了]** アイコンを選択します。

![Databricks クラスターを停止する](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Databricks クラスターを停止する")

クラスター作成時に **[Terminate after \_\_ minutes of inactivity]** \(アクティビティが \_\_ 分ない場合は終了する\) チェック ボックスをオンにしていた場合、手動で終了しなくともクラスターは自動で停止します。 このような場合、クラスターは、一定の時間だけ非アクティブな状態が続くと自動的に停止します。

## <a name="next-steps"></a>次の手順

この記事では、Azure Databricks に Spark クラスターを作成し、Azure Storage のデータを使って Spark ジョブを実行しました。 [Spark のデータ ソース](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html)を見て、他のデータ ソースから Azure Databricks にデータをインポートする方法を学習することもできます。 次の記事に進んで、Azure Databricks を使った ETL (データの抽出、変換、読み込み) 操作の実行方法について学びましょう。

> [!div class="nextstepaction"]
>[Azure Databricks を使ったデータの抽出、変換、読み込み](databricks-extract-load-sql-data-warehouse.md)
