---
title: Azure Data Factory から Spark プログラムを呼び出す
description: MapReduce アクティビティを使用して Azure Data Factory から Spark プログラムを呼び出す方法について説明します。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 644a0d645abb186731ddb1f408cd36e5b9ba3c3f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780023"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Azure Data Factory のパイプラインから Spark プログラムを呼び出す

> [!div class="op_single_selector" title1="変換アクティビティ"]
> * [Hive アクティビティ](data-factory-hive-activity.md)
> * [Pig アクティビティ](data-factory-pig-activity.md)
> * [MapReduce アクティビティ](data-factory-map-reduce.md)
> * [Hadoop Streaming アクティビティ](data-factory-hadoop-streaming-activity.md)
> * [Spark アクティビティ](data-factory-spark.md)
> * [Azure Machine Learning スタジオ (クラシック) のバッチ実行アクティビティ](data-factory-azure-ml-batch-execution-activity.md)
> * [Azure Machine Learning スタジオ (クラシック) の更新リソース アクティビティ](data-factory-azure-ml-update-resource-activity.md)
> * [ストアド プロシージャ アクティビティ](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL アクティビティ](data-factory-usql-activity.md)
> * [.NET カスタム アクティビティ](data-factory-use-custom-activities.md)

> [!NOTE]
> この記事は、一般公開されている Azure Data Factory のバージョン 1 に適用されます。 最新バージョンの Data Factory サービスを使用している場合は、[Data Factory での Apache Spark アクティビティを使用したデータ変換](../transform-data-using-spark.md)に関するページをご覧ください。

## <a name="introduction"></a>はじめに
Spark アクティビティは、Data Factory でサポートされる[データ変換アクティビティ](data-factory-data-transformation-activities.md)の 1 つです。 このアクティビティでは、指定された Spark プログラムが Azure HDInsight の Spark クラスターで実行されます。

> [!IMPORTANT]
> - Spark アクティビティでは、Azure Data Lake Store をプライマリ ストレージとして使用する HDInsight Spark クラスターはサポートされません。
> - Spark アクティビティは、既存の (自分の) HDInsight Spark クラスターのみをサポートします。 オンデマンド HDInsight のリンクされたサービスはサポートされません。

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>チュートリアル:Spark アクティビティを含むパイプラインを作成する
Spark アクティビティで Data Factory パイプラインを作成する一般的な手順を次に示します。

* データ ファクトリを作成します。
* HDInsight Spark クラスターに関連付けられているストレージをデータ ファクトリにリンクする、Azure Storage のリンクされたサービスを作成します。
* HDInsight の Spark クラスターをデータ ファクトリにリンクする、HDInsight のリンクされたサービスを作成します。
* Storage のリンクされたサービスを参照するデータセットを作成します。 現時点では、出力が生成されていなくても、アクティビティに対する出力データセットを指定する必要があります。
* 作成した HDInsight のリンクされたサービスを参照する、Spark アクティビティを使用したパイプラインを作成します。 アクティビティは、前の手順で出力データセットとして作成したデータセットで構成されます。 出力データセットは、スケジュール (1 時間に 1 回、毎日) を開始します。 このため、この出力データセットは、アクティビティによって出力が実際に生成されなくても、指定する必要があります。

### <a name="prerequisites"></a>前提条件
1. [ストレージ アカウントの作成](../../storage/common/storage-account-create.md)に関するトピックの手順に従って、汎用ストレージ アカウントを作成します。

1. [HDInsight での Spark クラスターの作成](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)のチュートリアルの説明に従って、HDInsight で Spark クラスターを作成します。 手順 1. で作成したストレージ アカウントをこのクラスターに関連付けます。

1. [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py)にある Python スクリプト ファイル **test.py** をダウンロードして確認してください。

1. Blob Storage の **adfspark** コンテナーにある **pyFiles** フォルダーに **test.py** をアップロードします。 コンテナーとフォルダーを作成します (存在しない場合)。

### <a name="create-a-data-factory"></a>Data Factory の作成
データ ファクトリを作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **[新規]**  >  **[データ + 分析]**  >  **[データ ファクトリ]** を選択します。

1. **[新しいデータ ファクトリ]** ブレードで、 **[名前]** フィールドに「**SparkDF**」と入力します。

   > [!IMPORTANT]
   > Azure Data Factory の名前はグローバルに一意にする必要があります。 "データ ファクトリ名 SparkDF は利用できません" というエラーが発生した場合は、データ ファクトリの名前を変更します。 たとえば、yournameSparkDFdate を使用し、もう一度データ ファクトリを作成します。 名前付け規則の詳細については、[Data Factory の名前付け規則](data-factory-naming-rules.md)に関するページを参照してください。

1. **[サブスクリプション]** で、データ ファクトリを作成する Azure サブスクリプションを選択します。

1. 既存のリソース グループを選択するか、新しい Azure リソース グループを作成します。

1. **[ダッシュボードにピン留めする]** チェック ボックスをオンにします。

1. **［作成］** を選択します

   > [!IMPORTANT]
   > Data Factory インスタンスを作成するには、サブスクリプション/リソース グループ レベルで [Data Factory の共同作業者](../../role-based-access-control/built-in-roles.md#data-factory-contributor) ロールのメンバーである必要があります。

1. 作成されたデータ ファクトリは、Azure Portal のダッシュボードに表示されます。

1. データ ファクトリが作成されると、データ ファクトリの内容を表示する **[データ ファクトリ]** ページが表示されます。 **[データ ファクトリ]** ページが表示されない場合は、ダッシュボードでデータ ファクトリのタイルを選択します。

    ![[Data Factory] ブレード](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>リンクされたサービスを作成します
この手順では、リンクされたサービスを 2 つ作成します。 1 つは Spark クラスターをデータ ファクトリにリンクするサービスで、もう 1 つはストレージをデータ ファクトリにリンクするサービスです。

#### <a name="create-a-storage-linked-service"></a>ストレージのリンクされたサービスを作成するには
この手順では、ストレージ アカウントをデータ ファクトリにリンクします。 このチュートリアルの後半の手順で作成するデータセットは、このリンクされたサービスを参照します。 次の手順で定義する HDInsight のリンクされたサービスは、このリンクされたサービスも参照します。

1. **[データ ファクトリ]** ブレードで **[作成およびデプロイ]** を選択します。 Data Factory エディターが表示されます。

1. **[新しいデータ ストア]** 、 **[Azure Storage]** の順に選択します。

   ![新しいデータ ストア](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. Storage のリンクされたサービスを作成するときに使用する JSON スクリプトがエディターに表示されます。

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. **accountname** と **accountkey** をストレージ アカウントの名前とアクセス キーで置き換えます。 ストレージ アクセス キーを取得する方法については、「[Manage storage account access keys (ストレージ アカウントのアクセス キーの管理)](../../storage/common/storage-account-keys-manage.md)」をご覧ください。

1. リンクされたサービスをデプロイするには、コマンド バーの **[デプロイ]** を選択します。 リンクされたサービスが正常にデプロイされると、[Draft-1] ウィンドウが消えます。 **AzureStorageLinkedService** が左側のツリー ビューに表示されます。

#### <a name="create-an-hdinsight-linked-service"></a>HDInsight のリンクされたサービスを作成する
この手順では、HDInsight Spark クラスターをデータ ファクトリにリンクする、HDInsight のリンクされたサービスを作成します。 HDInsight のクラスターは、このサンプルのパイプラインの Spark アクティビティに指定された Spark プログラムを実行するために使用されます。

1. Data Factory エディターで、 **[詳細]**  >  **[新規計算]**  >  **[HDInsight クラスター]** を選択します。

    ![HDInsight のリンクされたサービスを作成する](media/data-factory-spark/new-hdinsight-linked-service.png)

1. 次のスニペットをコピーして、[Draft-1] ウィンドウに貼り付けます。 JSON エディターで、次の手順を実行します。

    1. HDInsight Spark クラスターの URI を指定します。 (例: `https://<sparkclustername>.azurehdinsight.net/`)。

    1. Spark クラスターにアクセスするユーザーの名前を指定します。

    1. ユーザーのパスワードを指定します。

    1. HDInsight Spark クラスターに関連付けられている Storage のリンクされたサービスを指定します。 この例では、AzureStorageLinkedService です。

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

1. リンクされたサービスをデプロイするには、コマンド バーの **[デプロイ]** を選択します。

### <a name="create-the-output-dataset"></a>出力データセットを作成する
出力データセットは、スケジュール (1 時間に 1 回、毎日) を開始します。 このため、Spark アクティビティによって出力が生成されなくても、パイプラインでアクティビティの出力データセットを指定する必要があります。 アクティビティの入力データセットの指定は省略可能です。

1. Data Factory エディターで、 **[詳細]**  >  **[新しいデータセット]**  >  **[Azure Blob Storage]** を選択します。

1. 次のスニペットをコピーして、[Draft-1] ウィンドウに貼り付けます。 JSON スニペットで、**OutputDataset** という名前のデータセットを定義します。 さらに、**adfspark** という BLOB コンテナーと **pyFiles/output** というフォルダーに結果が保存されるように指定します。 前述のように、このデータセットはダミー データセットです。 この例の Spark プログラムでは出力は生成されません。 **availability** セクションでは、出力データセットが毎日生成されることを指定します。

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
1. データセットをデプロイするには、コマンド バーの **[デプロイ]** を選択します。


### <a name="create-a-pipeline"></a>パイプラインを作成する
この手順で、HDInsightSpark アクティビティのあるパイプラインを作成します。 現在、スケジュールは出力データセットによって開始されるため、アクティビティが出力を生成しない場合でも、出力データセットを作成する必要があります。 アクティビティが入力を受け取らない場合は、入力データセットの作成を省略できます。 そのため、この例では入力データセットを指定しません。

1. Data Factory エディターで、 **[詳細]**  >  **[新しいパイプライン]** を選択します。

1. [Draft-1] ウィンドウのスクリプトを次のスクリプトで置き換えます。

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

    1. **type** プロパティは **HDInsightSpark** に設定されます。

    1. **rootPath** プロパティは **adfspark\\pyFiles** に設定されます。ここで、adfspark は BLOB コンテナー、pyFiles はそのコンテナーのファイル フォルダーです。 この例では、Blob Storage は、Spark クラスターに関連付けられています。 ファイルは、別のストレージ アカウントにアップロードできます。 これを行う場合は、ストレージ アカウントをデータ ファクトリにリンクする Storage のリンクされたサービスを作成します。 次に、リンクされたサービスの名前を、**sparkJobLinkedService** プロパティの値として指定します。 このプロパティと、Spark アクティビティでサポートされている他のプロパティの詳細については、「[Spark アクティビティのプロパティ](#spark-activity-properties)」を参照してください。

    1. **entryFilePath** プロパティは、Python ファイルである **test.py** に設定されます。

    1. **getDebugInfo** プロパティは **Always** に設定されます。つまり、ログ ファイルが常に生成されます (成功または失敗)。

       > [!IMPORTANT]
       > 問題のトラブルシューティングを行う場合を除き、運用環境ではこのプロパティを `Always` に設定しないことをお勧めします。

    1. **outputs** セクションには、1 つの出力データセットがあります。 出力データセットは、Spark プログラムによって出力が生成されない場合でも指定する必要があります。 この出力データセットは、パイプラインのスケジュール (1 時間に 1 回、毎日) を開始します。

    Spark アクティビティでサポートされているプロパティの詳細については、「[Spark アクティビティのプロパティ](#spark-activity-properties)」を参照してください。

1. パイプラインをデプロイするには、コマンド バーの **[デプロイ]** を選択します。

### <a name="monitor-a-pipeline"></a>パイプラインを監視する
1. **[データ ファクトリ]** ブレードで、 **[監視と管理]** を選択して、別のタブで監視アプリケーションを開始します。

    ![Monitor & Manage tile](media/data-factory-spark/monitor-and-manage-tile.png)

1. 上部にある **[開始時刻]** フィルターを **[2/1/2017]** に変更して、 **[適用]** を選択します。

1. パイプラインの開始時刻 (2017-02-01) と終了時刻 (2017-02-02) の間は 1 日のみのため、1 つのアクティビティ ウィンドウのみが表示されます。 データ スライスの状態が **[準備完了]** であることを確認します。

    ![パイプラインの監視](media/data-factory-spark/monitor-and-manage-app.png)

1. **[アクティビティ ウィンドウ]** の一覧でアクティビティの実行を選択して、その詳細を確認します。 エラーが発生している場合は、右側のウィンドウでその詳細を確認してください。

### <a name="verify-the-results"></a>結果を確認する

1. `https://CLUSTERNAME.azurehdinsight.net/jupyter` に移動して、HDInsight Spark クラスターの Jupyter Notebook を開始します。 HDInsight Spark クラスターのクラスター ダッシュボードを開いて、Jupyter Notebook を起動することもできます。

1. **[新規]**  >  **[PySpark]** を選択して、新しい Notebook を開始します。

    ![Jupyter の新しい Notebook](media/data-factory-spark/jupyter-new-book.png)

1. 2 番目のステートメントの最後に次のテキストをコピーして貼り付け、Shift キーを押しながら Enter キーを押して、次のコマンドを実行します。

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. hvac テーブルのデータが表示されることを確認します。

    ![Jupyter クエリの結果](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
詳細な手順については、[Spark SQL クエリの実行](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)に関するページをご覧ください。

### <a name="troubleshooting"></a>トラブルシューティング
getDebugInfo を **Always** に設定しているため、BLOB コンテナー内の pyFiles フォルダーに log サブフォルダーが表示されます。 ログ フォルダーのログ ファイルで、追加情報を確認できます。 このログ ファイルは、エラーが発生している場合に特に便利です。 運用環境では、これを **Failure** に設定してください。

詳細なトラブルシューティングを行う場合は、次の手順を実行します。


1. `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster` にアクセスします。

    ![YARN UI アプリケーション](media/data-factory-spark/yarnui-application.png)

1. 実行の試行の 1 つについて、 **[ログ]** を選択します。

    ![[アプリケーション] ページ](media/data-factory-spark/yarn-applications.png)

1. ログ ページに次の追加のエラー情報が表示されます。

    ![ログ エラー](media/data-factory-spark/yarnui-application-error.png)

次のセクションでは、データ ファクトリで Spark クラスターと Spark アクティビティを使用するための、データ ファクトリ エンティティに関する情報を提供します。

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
                    },
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
| description | アクティビティの動作を説明するテキスト。 | いいえ |
| type | このプロパティは HDInsightSpark に設定する必要があります。 | はい |
| linkedServiceName | Spark プログラムが実行されている HDInsight のリンクされたサービスの名前。 | はい |
| rootPath | BLOB コンテナーと Spark ファイルを含むフォルダー。 ファイル名の大文字と小文字は区別されます。 | はい |
| entryFilePath | Spark コード/パッケージのルート フォルダーへの相対パス。 | はい |
| className | アプリケーションの Java/Spark のメイン クラス。 | いいえ |
| 引数 | Spark プログラムのコマンドライン引数の一覧です。 | いいえ |
| proxyUser | Spark プログラムの実行を偽装するユーザー アカウント。 | いいえ |
| sparkConfig | [Spark 構成のアプリケーション プロパティ](https://spark.apache.org/docs/latest/configuration.html#available-properties)に関するトピックに示されている Spark 構成プロパティの値を指定します。 | いいえ |
| getDebugInfo | HDInsight クラスターで使用されているストレージまたは sparkJobLinkedService で指定されたストレージに Spark ログ ファイルがコピーされるタイミングを指定します。 使用できる値は None、Always、または Failure です。 既定値は None です。 | いいえ |
| sparkJobLinkedService | Spark ジョブ ファイル、依存関係、およびログが含まれる Storage のリンクされたサービス。 指定しない場合は、HDInsight クラスターに関連付けられているストレージが使用されます。 | いいえ |

## <a name="folder-structure"></a>フォルダー構造
Spark アクティビティでは、Pig および Hive アクティビティが実行するインライン スクリプトがサポートされません。 また、Spark ジョブは、Pig/Hive ジョブよりも拡張できます。 Spark ジョブの場合、jar パッケージ (java CLASSPATH に配置)、Python ファイル (PYTHONPATH に配置) など、複数の依存関係を利用できます。

HDInsight のリンクされたサービスによって参照される Blob Storage に、次のフォルダー構造を作成します。 その後、依存ファイルを、**entryFilePath** で表されるルート フォルダー内の適切なサブフォルダーにアップロードします。 たとえば、Python ファイルはルート フォルダーの pyFiles サブフォルダーに、jar ファイルはルート フォルダーの jar サブフォルダーにアップロードします。 実行時、Data Factory サービスに必要な Blob Storage のフォルダー構造を次に示します。

| Path | 説明 | 必須 | Type |
| ---- | ----------- | -------- | ---- |
| . | ストレージのリンクされたサービスにおける Spark ジョブのルート パス。 | はい | Folder |
| &lt;user defined &gt; | Spark ジョブの入力ファイルを指定するパス。 | はい | ファイル |
| ./jars | このフォルダーのすべてのファイルがアップロードされ、クラスターの Java classpath に配置されます。 | いいえ | Folder |
| ./pyFiles | このフォルダーのすべてのファイルがアップロードされ、クラスターの PYTHONPATH に配置されます。 | いいえ | Folder |
| ./files | このフォルダーのすべてのファイルがアップロードされ、Executor 作業ディレクトリに配置されます。 | いいえ | Folder |
| ./archives | このフォルダーのファイルは圧縮されていません。 | いいえ | Folder |
| ./logs | Spark クラスターのログが格納されているフォルダー。| いいえ | Folder |

次の例のストレージには、HDInsight のリンクされたサービスによって参照される Blob Storage に 2 つの Spark ジョブ ファイルが含まれています。

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
