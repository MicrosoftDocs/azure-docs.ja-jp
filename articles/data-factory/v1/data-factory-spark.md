---
title: "Azure Data Factory から Spark プログラムを呼び出す | Microsoft Docs"
description: "MapReduce アクティビティを使用して Azure Data Factory から Spark プログラムを呼び出す方法について説明します。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 3690b5f62d8384d255d420946f6ac1cfd47b9317
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Azure Data Factory のパイプラインから Spark プログラムを呼び出す

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive アクティビティ](data-factory-hive-activity.md)
> * [Pig アクティビティ](data-factory-pig-activity.md)
> * [MapReduce アクティビティ](data-factory-map-reduce.md)
> * [Hadoop ストリーミング アクティビティ](data-factory-hadoop-streaming-activity.md)
> * [Spark アクティビティ](data-factory-spark.md)
> * [Machine Learning バッチ実行アクティビティ](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning 更新リソース アクティビティ](data-factory-azure-ml-update-resource-activity.md)
> * [ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL アクティビティ](data-factory-usql-activity.md)
> * [.NET カスタム アクティビティ](data-factory-use-custom-activities.md)

## <a name="introduction"></a>はじめに
Spark アクティビティは、Azure Data Factory でサポートされる[データ変換アクティビティ](data-factory-data-transformation-activities.md)の 1 つです。 このアクティビティでは、指定された Spark プログラムが Azure HDInsight の Apache Spark クラスターで実行されます。    

> [!IMPORTANT]
> - Spark アクティビティでは、Azure Data Lake Store をプライマリ ストレージとして使用する HDInsight Spark クラスターはサポートされません。
> - Spark アクティビティは、既存の (自分の) HDInsight Spark クラスターのみをサポートします。 オンデマンド HDInsight のリンクされたサービスはサポートされません。

## <a name="walkthrough-create-a-pipeline-with-spark-activity"></a>チュートリアル: Spark アクティビティを使用してパイプラインを作成する
Spark アクティビティで Data Factory パイプラインを作成する一般的な手順を次に示します。  

1. データ ファクトリを作成する
2. HDInsight Spark クラスターに関連付けられている Azure Storage をデータ ファクトリにリンクする、Azure Storage のリンクされたサービスを作成します。     
2. Azure HDInsight の Apache Spark クラスターをデータ ファクトリにリンクする、Azure HDInsight のリンクされたサービスを作成します。
3. Azure Storage のリンクされたサービスを参照するデータセットを作成します。 現時点では、出力が生成されていなくても、アクティビティに対する出力データセットを指定する必要があります。  
4. 手順 2 で作成した HDInsight のリンクされたサービスを参照する、Spark アクティビティを使用したパイプラインを作成します。 アクティビティは、前の手順で出力データセットとして作成したデータセットで構成されます。 出力データセットは、スケジュール (1 時間に 1 回、毎日など) を開始するため、 アクティビティによって出力が実際に生成されていなくても、指定する必要があります。

### <a name="prerequisites"></a>前提条件
1. [ストレージ アカウントの作成](../../storage/common/storage-create-storage-account.md#create-a-storage-account)チュートリアルの手順に従って、**汎用の Azure ストレージ アカウント**を作成します。  
2. **Azure HDInsight での Apache Spark クラスターの作成**チュートリアルの説明に従って、[Azure HDInsight で Apache Spark クラスター](../../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md)を作成します。 手順 1. で作成した Azure ストレージ アカウントをこのクラスターに関連付けます。  
3. [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py) にある python スクリプト ファイル **test.py** をダウンロードして確認します。  
3.  Azure Blob Storage の **adfspark** コンテナーにある**pyFiles** フォルダーに **test.py** をアップロードします。 コンテナーとフォルダーが存在しない場合は作成します。

### <a name="create-data-factory"></a>データ ファクトリの作成
それでは、この手順でデータ ファクトリの作成から始めましょう。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. 左側のメニューで **[新規]** をクリックし、**[データ + 分析]**、**[Data Factory]** の順にクリックします。
3. **[新しいデータ ファクトリ]** ブレードで、[名前] フィールドに「**SparkDF**」と入力します。

   > [!IMPORTANT]
   > Azure データ ファクトリの名前は **グローバルに一意**にする必要があります。 "**データ ファクトリ名 "SparkDF" は利用できません**" というエラーが発生した場合は、 データ ファクトリの名前を変更して (たとえば、yournameSparkDFdate)、もう一度作成してみてください。 Data Factory アーティファクトの名前付け規則については、「 [Azure Data Factory - 名前付け規則](data-factory-naming-rules.md) 」を参照してください。   
4. データ ファクトリを作成する **Azure サブスクリプション** を選択します。
5. 既存の**リソース グループ**を選択するか、新しい Azure リソース グループを作成します。
6. **[ダッシュボードにピン留めする]** オプションをオンにします。  
6. **[新しい Data Factory]** ブレードで **[作成]** をクリックします。

   > [!IMPORTANT]
   > Data Factory インスタンスを作成するには、サブスクリプション/リソース グループ レベルで [Data Factory の共同作業者](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) ロールのメンバーである必要があります。
7. 次のように、Azure Portal の**ダッシュボード**にデータ ファクトリを作成中であることが示されます。   
8. データ ファクトリが正常に作成されると、データ ファクトリの内容を表示するデータ ファクトリ ページが表示されます。 データ ファクトリ ページが表示されない場合は、ダッシュボードでデータ ファクトリのタイルをクリックします。

    ![[Data Factory] ブレード](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>リンクされたサービスの作成
この手順では、リンクされたサービスを 2 つ作成します。1 つは Spark クラスターをデータ ファクトリにリンクし、もう 1 つは Azure Storage をデータ ファクトリにリンクします。  

#### <a name="create-azure-storage-linked-service"></a>Azure Storage のリンクされたサービスを作成する
この手順では、Azure ストレージ アカウントをデータ ファクトリにリンクします。 このチュートリアルの後半の手順で作成するデータセットは、このリンクされたサービスを参照します。 次の手順で定義する HDInsight のリンクされたサービスは、このリンクされたサービスも参照します。  

1. 自分のデータ ファクトリの **[データ ファクトリ]** ブレードで **[作成およびデプロイ]** をクリックします。 Data Factory エディターが起動します。
2. **[新しいデータ ストア]** をクリックし、**[Azure Storage]** を選択します。

   ![New data store - Azure Storage - menu](./media/data-factory-spark/new-data-store-azure-storage-menu.png)
3. Azure Storage のリンクされたサービスを作成するための **JSON スクリプト**がエディターに表示されます。

   ![Azure Storage のリンクされたサービス](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)
4. **accountname** と **accountkey** を Azure ストレージ アカウントの名前とアクセス キーで置き換えます。 ストレージ アクセス キーを取得する方法については、「[ストレージ アカウントの管理](../../storage/common/storage-create-storage-account.md#manage-your-storage-account)」のストレージ アクセス キーを表示、コピー、再生成する方法に関する情報を参照してください。
5. リンク サービスをデプロイするには、コマンド バーの **[デプロイ]** をクリックします。 リンクされたサービスが正常にデプロイされると、**[Draft-1]** ウィンドウが消え、**AzureStorageLinkedService** が左側のツリー ビューに表示されます。

#### <a name="create-hdinsight-linked-service"></a>HDInsight のリンクされたサービスを作成する
この手順では、HDInsight Spark クラスターをデータ ファクトリにリンクする、Azure HDInsight のリンクされたサービスを作成します。 HDInsight のクラスターは、このサンプルのパイプラインの Spark アクティビティに指定された Spark プログラムを実行するために使用されます。  

1. **[...詳細]** ツールバーの **[新規計算]** をクリックし、次に **[HDInsight クラスター]** をクリックします。

    ![HDInsight のリンクされたサービスを作成する](media/data-factory-spark/new-hdinsight-linked-service.png)
2. 次のスニペットをコピーして、 **[Draft-1]** ウィンドウに貼り付けます。 JSON エディターで次の操作を実行します。
    1. HDInsight Spark クラスターの **URI** を指定します。 たとえば、「 `https://<sparkclustername>.azurehdinsight.net/`」のように入力します。
    2. Spark クラスターにアクセスする**ユーザー**の名前を指定します。
    3. ユーザーの**パスワード**を指定します。
    4. HDInsight Spark クラスターに関連付けられる **Azure Storage のリンクされたサービス**を指定します。 この例では、**AzureStorageLinkedService** です。

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - Spark アクティビティでは、Azure Data Lake Store をプライマリ ストレージとして使用する HDInsight Spark クラスターはサポートされません。
    > - Spark アクティビティは、既存の (自分の) HDInsight Spark クラスターのみをサポートします。 オンデマンド HDInsight のリンクされたサービスはサポートされません。

    HDInsight のリンクされたサービスの詳細については、[HDInsight のリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)に関するページを参照してください。
3.  リンク サービスをデプロイするには、コマンド バーの **[デプロイ]** をクリックします。  

### <a name="create-output-dataset"></a>出力データセットの作成
出力データセットは、スケジュール (1 時間に 1 回、毎日など) を開始するため、 アクティビティによって出力が実際に生成されなくても、パイプラインの Spark アクティビティの出力データセットを指定する必要があります。 アクティビティの入力データセットの指定は省略可能です。

1. **Data Factory エディター**のコマンド バーで **[...]\(その他)** をクリックし、**[新しいデータセット]** をクリックして、**[Azure BLOB ストレージ]** を選択します。  
2. 次のスニペットをコピーして、[Draft-1] ウィンドウに貼り付けます。 JSON スニペットで、**OutputDataset** という名前のデータセットを定義します。 さらに、**adfspark** という BLOB コンテナーと **pyFiles/output** というフォルダーに結果が保存されるように指定します。 前述のように、このデータセットはダミー データセットです。 この例の Spark プログラムでは出力は生成されません。 **availability** セクションでは、出力データセットが毎日生成されることを指定します。  

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
3. データセットをデプロイするには、コマンド バーの **[デプロイ]** をクリックします。


### <a name="create-pipeline"></a>パイプラインの作成
この手順で、**HDInsightSpark** アクティビティのあるパイプラインを作成します。 現在、スケジュールは出力データセットによって開始されるため、アクティビティが出力を生成しない場合でも、出力データセットを作成する必要があります。 アクティビティが入力を受け取らない場合は、入力データセットの作成を省略できます。 そのため、この例では入力データセットを指定しません。

1. **Data Factory エディター**で、コマンド バーの **[…詳細]** をクリックし、**[新しいパイプライン]** をクリックします。
2. [Draft-1] ウィンドウのスクリプトを次のスクリプトで置き換えます。

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```
    以下の点に注意してください。
    - **type** プロパティは **HDInsightSpark** に設定されます。
    - **rootPath** は **adfspark\\pyFiles** に設定されます。ここで、adfspark は Azure BLOB コンテナー、pyFiles はそのコンテナーのファイル フォルダーです。 この例では、Azure Blob Storage は、Spark クラスターに関連付けられています。 ファイルは、別の Azure Storage にアップロードできます。 これを行う場合は、ストレージ アカウントをデータ ファクトリにリンクする Azure Storage のリンクされたサービスを作成します。 次に、リンクされたサービスの名前を、**sparkJobLinkedService** プロパティの値として指定します。 このプロパティと、Spark アクティビティでサポートされている他のプロパティの詳細については、「[Spark アクティビティのプロパティ](#spark-activity-properties)」を参照してください。  
    - **entryFilePath** は、python ファイルである **test.py** に設定されます。
    - **getDebugInfo** プロパティは **Always** に設定されます。つまり、ログ ファイルが常に生成されます (成功または失敗)。

        > [!IMPORTANT]
        > 問題のトラブルシューティングを行う場合を除き、運用環境でこのプロパティを `Always` に設定しないことをお勧めします。
    - **outputs** セクションには、1 つの出力データセットがあります。 出力データセットは、Spark プログラムによって出力が生成されなくても指定する必要があります。 出力データセットは、パイプラインのスケジュール (1 時間に 1 回、毎日など) を開始します。  

        Spark アクティビティでサポートされているプロパティの詳細については、「[Spark アクティビティのプロパティ](#spark-activity-properties)」を参照してください。
3. パイプラインをデプロイするには、コマンド バーの **[デプロイ]** をクリックします。

### <a name="monitor-pipeline"></a>パイプラインを監視する
1. **[X]** をクリックして Data Factory エディターのブレードを閉じ、Data Factory ホーム ページに戻ります。 **[監視と管理]** をクリックして、別のタブで監視用のアプリケーションを起動します。

    ![[監視と管理] タイル](media/data-factory-spark/monitor-and-manage-tile.png)
2. 上部にある**[開始時刻]** フィルターを **[2/1/2017]** に変更して、**[適用]** をクリックします。
3. パイプラインの開始時刻 (2017-02-01) と終了時刻 (2017-02-02) の間は 1 日のみのため、表示されるアクティビティ ウィンドウは 1 つのみです。 データ スライスの状態が **[準備完了]** であることを確認します。

    ![パイプラインの監視](media/data-factory-spark/monitor-and-manage-app.png)    
4. **[アクティビティ ウィンドウ]** を選択して、アクティビティの実行に関する詳細を参照します。 エラーが発生している場合は、右側のウィンドウでその詳細を確認してください。

### <a name="verify-the-results"></a>結果を確認する

1. https://CLUSTERNAME.azurehdinsight.net/jupyter に移動して、HDInsight Spark クラスターの **Jupyter Notebook** を起動します。 また、HDInsight Spark クラスターのクラスター ダッシュボードを起動して、**Jupyter Notebook** を起動することもできます。
2. **[新規]** -> **[PySpark]** の順にクリックして、新しい Notebook を開始します。

    ![Jupyter の新しい Notebook](media/data-factory-spark/jupyter-new-book.png)
3. 2 番目のステートメントの最後に次のテキストをコピーして貼り付け、次のコマンドを実行して、**Shift キーを押しながら Enter キー**を押します。  

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
4. hvac テーブルのデータが表示されることを確認します。  

    ![Jupyter クエリの結果](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
詳細な手順については、「[Spark SQL クエリの実行](../../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md)」を参照してください。 

### <a name="troubleshooting"></a>トラブルシューティング
**getDebugInfo** を **Always** に設定しているので、Azure BLOB コンテナー内の **pyFiles** フォルダーに **log** サブフォルダーが表示されます。 ログ フォルダーのログ ファイルで、追加の詳細情報を取得できます。 このログ ファイルは、エラーが発生している場合に特に便利です。 運用環境では、これを **Failure** に設定してみてください。

詳細なトラブルシューティングを行う場合は、次の手順を実行します。


1. `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster` に移動します。

    ![YARN UI アプリケーション](media/data-factory-spark/yarnui-application.png)  
2. 実行の 1 つについて、**[ログ]** をクリックします。

    ![[アプリケーション] ページ](media/data-factory-spark/yarn-applications.png)
3. ログ ページに別途エラー情報が表示されます。

    ![ログ エラー](media/data-factory-spark/yarnui-application-error.png)

次のセクションでは、データ ファクトリで Apache Spark クラスターと Spark アクティビティを使用するための Data Factory エンティティに関する情報を提供します。

## <a name="spark-activity-properties"></a>Spark アクティビティのプロパティ
Spark アクティビティを使用するパイプラインのサンプル JSON 定義を次に示します。    

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    }
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

次の表で、JSON 定義で使用される JSON プロパティについて説明します。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| name | パイプラインのアクティビティの名前。 | はい |
| 説明 | アクティビティの動作を説明するテキスト。 | いいえ |
| type | このプロパティは HDInsightSpark に設定する必要があります。 | はい |
| 既定のコンテナー | Spark プログラムが実行されている HDInsight のリンクされたサービスの名前。 | はい |
| rootPath | Azure BLOB コンテナーと Spark ファイルを含むフォルダー。 ファイル名は大文字と小文字が区別されます。 | はい |
| entryFilePath | Spark コード/パッケージのルート フォルダーへの相対パス。 | はい |
| className | アプリケーションの Java/Spark のメイン クラス | なし |
| arguments | Spark プログラムのコマンドライン引数の一覧です。 | いいえ |
| proxyUser | Spark プログラムの実行を偽装する借用すユーザー アカウント | いいえ |
| sparkConfig | 「[Spark Configuration - Application properties (Spark 構成 - アプリケーションのプロパティ)](https://spark.apache.org/docs/latest/configuration.html#available-properties)」と題するトピックに示されている Spark 構成プロパティの値を指定します。 | いいえ |
| getDebugInfo | HDInsight クラスターで使用されている Azure Storage または sparkJobLinkedService で指定された Azure Storage に Spark ログ ファイルがコピーされるタイミングを指定します。 使用できる値: None、Always、または Failure。 既定値: None。 | なし |
| sparkJobLinkedService | Spark ジョブ ファイル、依存関係、およびログが含まれる Azure Storage のリンクされたサービス。  指定しない場合は、HDInsight クラスターに関連付けられているストレージが使用されます。 | なし |

## <a name="folder-structure"></a>フォルダー構造
Spark アクティビティでは、Pig および Hive アクティビティが実行するインライン スクリプトがサポートされません。 また、Spark ジョブは、Pig/Hive ジョブよりも拡張できます。 Spark ジョブの場合、jar パッケージ (java CLASSPATH に配置)、python ファイル (PYTHONPATH に配置) など、複数の依存関係を利用できます。

HDInsight のリンクされたサービスによって参照される Azure Blob Storage に、次のフォルダー構造を作成します。 その後、依存ファイルを、**entryFilePath** で表されるルート フォルダー内の適切なサブフォルダーにアップロードします。 たとえば、python ファイルはルート フォルダーの pyFiles サブフォルダーに、jar ファイルはルート フォルダーの jar サブフォルダーにアップロードします。 実行時、Data Factory サービスに必要な Azure Blob Storage のフォルダー構造を次に示します。     

| パス | Description | 必須 | 型 |
| ---- | ----------- | -------- | ---- |
| をクリックします。 | ストレージのリンクされたサービスにおける Spark ジョブのルート パス  | はい | フォルダー |
| &lt;user defined &gt; | Spark ジョブの入力ファイルを指定するパス | はい | ファイル |
| ./jars | このフォルダーのすべてのファイルがアップロードされ、クラスターの java classpath に配置されます | なし | フォルダー |
| ./pyFiles | このフォルダーのすべてのファイルがアップロードされ、クラスターの PYTHONPATH に配置されます | いいえ | フォルダー |
| ./files | このフォルダーのすべてのファイルがアップロードされ、Executor 作業ディレクトリに配置されます | いいえ | フォルダー |
| ./archives | このフォルダーのファイルは圧縮されていません | なし | フォルダー |
| ./logs | Spark クラスターのログが格納されているフォルダー。| なし | フォルダー |

次の例のストレージには、HDInsight のリンクされたサービスによって参照される Azure Blob Storage に 2 つの Spark ジョブ ファイルが含まれています。

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
