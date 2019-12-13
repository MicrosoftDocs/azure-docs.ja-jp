---
title: SQL Server ストアド プロシージャ アクティビティ
description: SQL Server ストアド プロシージャ アクティビティを使用して、Data Factory パイプラインから Azure SQL Database または Azure SQL Data Warehouse でストアド プロシージャを呼び出す方法について説明します。
services: data-factory
documentationcenter: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: 45aa49de51f42b26c653b15e79c865e3f5647c39
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931640"
---
# <a name="sql-server-stored-procedure-activity"></a>SQL Server ストアド プロシージャ アクティビティ
> [!div class="op_single_selector" title1="変換アクティビティ"]
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

> [!NOTE]
> この記事は、Azure Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[Data Factory でのストアド プロシージャ アクティビティを使用したデータ変換](../transform-data-using-stored-procedure.md)に関するページを参照してください。

## <a name="overview"></a>概要
Data Factory [パイプライン](data-factory-create-pipelines.md)のデータ変換アクティビティを使用して、生データを予測や洞察に変換および処理します。 ストアド プロシージャ アクティビティは、Data Factory でサポートされる変換アクティビティの 1 つです。 この記事は、データ変換と Data Factory でサポートされる変換アクティビティの概要を説明する、[データ変換アクティビティ](data-factory-data-transformation-activities.md)に関する記事に基づいています。

ストアド プロシージャ アクティビティを使用して、社内または Azure 仮想マシン (VM) 上の次のいずれかのデータ ストアでストアド プロシージャを呼び出すことができます。

- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server データベース SQL Server を使用している場合、データベースをホストするコンピューターと同じコンピューターまたはデータベースにアクセスできる別のコンピューター上にデータ管理ゲートウェイをインストールします。 データ管理ゲートウェイは、安全かつ管理された方法でオンプレミスまたは Azure VM 上のデータ ソースをクラウド サービスに接続するコンポーネントです。 詳細については、[データ管理ゲートウェイ](data-factory-data-management-gateway.md)に関する記事をご覧ください。

> [!IMPORTANT]
> Azure SQL Database または SQL Server にデータをコピーする場合、**sqlWriterStoredProcedureName** プロパティを使用してストアド プロシージャを呼び出すように、コピー アクティビティで **SqlSink** を構成できます。 詳細については、[コピー アクティビティからのストアド プロシージャの呼び出し](data-factory-invoke-stored-procedure-from-copy-activity.md)に関する記事をご覧ください。 プロパティの詳細については、[Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties)、[SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties) の各コネクタに関する記事をご覧ください。 コピー アクティビティを使用して Azure SQL Data Warehouse にデータをコピー中に、ストアド プロシージャを呼び出すことはできません。 しかし、SQL Data Warehouse のストアド プロシージャを呼び出すためにストアド プロシージャのアクティビティを使用することは可能です。
>
> Azure SQL Database、SQL Server、または Azure SQL Data Warehouse からデータをコピーする場合、**sqlReaderStoredProcedureName** プロパティを使用して、ソース データベースからデータを読み取るストアド プロシージャを呼び出すように、コピー アクティビティで **SqlSource** を構成できます。 詳細については、[Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties)、[SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties)、[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) の各コネクタに関する記事をご覧ください。

次のチュートリアルでは、ストアド プロシージャ アクティビティをパイプラインで使用して、Azure SQL データベースでストアド プロシージャを呼び出します。

## <a name="walkthrough"></a>チュートリアル
### <a name="sample-table-and-stored-procedure"></a>サンプル テーブルとストアド プロシージャ
1. SQL Server Management Studio などのツールを使って Azure SQL Database に以下の **テーブル** を作成します。 datetimestamp 列は、対応する ID が生成された日付と時刻です。

    ```SQL
    CREATE TABLE dbo.sampletable
    (
        Id uniqueidentifier,
        datetimestamp nvarchar(127)
    )
    GO

    CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
    GO
    ```
    Id は一意の識別子で、datetimestamp 列は、対応する ID が生成された日時です。
    
    ![サンプル データ](./media/data-factory-stored-proc-activity/sample-data.png)

    このサンプルでは、ストアド プロシージャは、Azure SQL Database にあります。 ストアド プロシージャが Azure SQL Data Warehouse と SQL Server Database にある場合の方法は似ています。 SQL Server データベースの場合は、[データ管理ゲートウェイ](data-factory-data-management-gateway.md)をインストールする必要があります。
2. **sampletable** にデータを挿入する、次の**ストアド プロシージャ**を作成します。

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > パラメーター (この例では DateTime) の**名前**と**大文字小文字の区別**は、パイプライン/アクティビティ JSON に指定されたパラメーターのものと一致する必要があります。 ストアド プロシージャ定義で、 **\@** がパラメーターのプレフィックスとして使用されていることを確認します。

### <a name="create-a-data-factory"></a>Data Factory の作成
1. [Azure Portal](https://portal.azure.com/) にログインします。
2. 左側のメニューの **[新規]** をクリックし、 **[インテリジェンス + 分析]** 、 **[Data Factory]** の順にクリックします。

    ![新しいデータ ファクトリ](media/data-factory-stored-proc-activity/new-data-factory.png)
3. **[新しいデータ ファクトリ]** ブレードで、[名前] フィールドに「**SProcDF**」と入力します。 Azure Data Factory の名前は**グローバルで一意**となります。 ファクトリを正常に作成できるようにするには、データ ファクトリの名前の先頭に自分の名前を付ける必要があります。

   ![新しいデータ ファクトリ](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. **Azure サブスクリプション**を選択します。
5. **リソース グループ**について、次の手順のいずれかを行います。
   1. **[新規作成]** をクリックし、リソース グループの名前を入力します。
   2. **[既存のものを使用]** を選択し、既存のリソース グループを選択します。
6. データ ファクトリの **場所** を選択します。
7. 次回ログオンしたときにデータ ファクトリがダッシュボードに表示されるようにするために、 **[ダッシュボードにピン留めする]** を選択します。
8. **[新しい Data Factory]** ブレードで **[作成]** をクリックします。
9. 作成したデータ ファクトリは、Azure Portal の**ダッシュボード**に表示されます。 データ ファクトリが正常に作成されると、データ ファクトリの内容を表示するデータ ファクトリ ページが表示されます。

   ![Data Factory ホーム ページ](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Azure SQL のリンク サービスを作成する
データ ファクトリの作成後、sampletable テーブルと usp_sample ストアド プロシージャが格納された Azure SQL データベースをデータ ファクトリにリンクする、Azure SQL のリンクされたサービスを作成します。

1. **SProcDF** の **[Data Factory]** ブレードで、 **[作成およびデプロイ]** をクリックして Data Factory エディターを起動します。
2. コマンド バーの **[新しいデータ ストア]** をクリックし、 **[Azure SQL Database]** を選択します。 Azure SQL のリンク サービスを作成するための JSON スクリプトがエディターに表示されます。

   ![新しいデータ ストア](media/data-factory-stored-proc-activity/new-data-store.png)
3. JSON スクリプトを次のように変更します。

   1. `<servername>` を、Azure SQL Database サーバーの名前に置き換えます。
   2. `<databasename>` を、テーブルとストアド プロシージャを作成したデータベースの名前に置き換えます。
   3. `<username@servername>` を、データベースへのアクセス権を持つユーザー アカウントに置き換えます。
   4. `<password>` を、ユーザー アカウントのパスワードに置き換えます。

      ![新しいデータ ストア](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. リンク サービスをデプロイするには、コマンド バーの **[デプロイ]** をクリックします。 AzureSqlLinkedService が左側のツリー ビューに表示されることを確認します。

    ![リンクされたサービスを表示しているツリー ビュー](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>出力データセットの作成
ストアド プロシージャがデータを生成しない場合でも、ストアド プロシージャ アクティビティの出力データセットを指定する必要があります。 これは、出力データセットによってアクティビティのスケジュール (時間単位、日単位など、アクティビティの実行頻度) が開始されるためです。 出力データセットでは、ストアド プロシージャを実行する、Azure SQL Database、Azure SQL Data Warehouse、または SQL Server Database を表す**リンクされたサービス**を使用する必要があります。 出力データセットは、パイプラインの別のアクティビティ ([連鎖するアクティビティ](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)) による後続処理のために、ストアド プロシージャの結果を渡す 1 つの方法として使用できます。 ただし、Data Factory では、ストアド プロシージャの出力をこのデータセットに自動的に書き込むわけではありません。 出力データセットが参照する SQL テーブルへの書き込みは、ストアド プロシージャが実行します。 出力データセットに**ダミー データセット** (ストアド プロシージャの出力を実際には保持しないテーブルを参照するデータセット) を指定できる場合もあります。 このダミー データセットは、ストアド プロシージャ アクティビティを実行するスケジュールの指定にのみ使用されます。

1. **[...More (その他)]** (ツール バー上) をクリックし、 **[新しいデータセット]** 、 **[Azure SQL]** の順にクリックします。 コマンド バーの **[新しいデータセット]** をクリックし、 **[Azure SQL]** を選択します。

    ![リンクされたサービスを表示しているツリー ビュー](media/data-factory-stored-proc-activity/new-dataset.png)
2. 次の JSON スクリプトをコピーして、JSON エディターに貼り付けます。

    ```JSON
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "sampletable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. データセットをデプロイするには、コマンド バーの **[デプロイ]** をクリックします。 ツリー ビューにデータセットが表示されることを確認します。

    ![リンクされたサービスを表示しているツリー ビュー](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>SqlServerStoredProcedure アクティビティでパイプラインを作成する
次に、ストアド プロシージャ アクティビティを含むパイプラインを作成しましょう。

次のプロパティに注意してください。

- **type** プロパティを **SqlServerStoredProcedure** に設定します。
- type プロパティの **storedProcedureName** を **usp_sample** (ストアド プロシージャの名前) に設定します。
- **storedProcedureParameters** セクションには、**DateTime** という名前のパラメーターが 1 つ含まれています。 JSON でのパラメーターの名前は、大文字と小文字の区別も含め、ストアド プロシージャの定義でのパラメーターの名前と一致する必要があります。 パラメーターで null を渡す必要がある場合は、構文として `"param1": null` (すべて小文字) を使用します。

1. **[...More (その他)]** (コマンド バー上) をクリックし、 **[新しいパイプライン]** をクリックします。
2. 次の JSON スニペットをコピーして貼り付けます。

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "usp_sample",
                        "storedProcedureParameters": {
                            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                        }
                    },
                    "outputs": [
                        {
                            "name": "sprocsampleout"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "SprocActivitySample"
                }
            ],
            "start": "2017-04-02T00:00:00Z",
            "end": "2017-04-02T05:00:00Z",
            "isPaused": false
        }
    }
    ```
3. パイプラインをデプロイするには、ツール バーの **[デプロイ]** をクリックします。

### <a name="monitor-the-pipeline"></a>パイプラインの監視
1. **[X]** をクリックして Data Factory エディターのブレードを閉じ、[Data Factory] ブレードに戻って **[ダイアグラム]** をクリックします。

    ![[ダイアグラム] タイル](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. **ダイアグラム ビュー**に、パイプラインの概要と、このチュートリアルで使用するデータセットが表示されます。

    ![[ダイアグラム] タイル](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. ダイアグラム ビューで、`sprocsampleout` データセットをダブルクリックします。 スライスが準備完了状態で表示されます。 スライスは、JSON の開始時刻から終了時刻までの間に 1 時間ごとに生成されるため、5 つのスライスがあります。

    ![[ダイアグラム] タイル](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. スライスが**準備完了**状態のときに、Azure SQL データベースに対して `select * from sampletable` クエリを実行して、ストアド プロシージャによってデータがテーブルに挿入されていることを確認します。

   ![出力データ](./media/data-factory-stored-proc-activity/output.png)

   Azure Data Factory パイプラインの監視の詳細については、 [パイプラインの監視](data-factory-monitor-manage-pipelines.md) に関するページを参照してください。

## <a name="specify-an-input-dataset"></a>入力データセットの指定
このチュートリアルでは、ストアド プロシージャ アクティビティに入力データセットはありません。 入力データセットを指定すると、入力データセットのスライスが使用可能 (準備完了状態) になるまでストアド プロシージャ アクティビティは実行されません。 データセットには、(同じパイプラインの別のアクティビティで生成されるものではない) 外部データセットを指定することも、アップストリーム アクティビティ (このアクティビティの前に実行されるアクティビティ) で生成される内部データセットを指定することもできます。 ストアド プロシージャ アクティビティの複数の入力データセットを指定できます。 この場合、ストアド プロシージャ アクティビティは、入力データセットのすべてのスライスが使用可能 (準備完了状態) である場合にのみ実行されます。 ストアド プロシージャで入力データセットをパラメーターとして使用することはできません。 入力データセットは、ストアド プロシージャ アクティビティを開始する前に、依存関係の確認にのみ使用されます。

## <a name="chaining-with-other-activities"></a>他のアクティビティとの連鎖
アップストリーム アクティビティをこのアクティビティと連鎖させる場合は、アップストリーム アクティビティの出力をこのアクティビティの入力として指定します。 この場合、アップストリーム アクティビティが完了し、アップストリーム アクティビティの出力データセットが使用可能 (準備完了状態) になるまでストアド プロシージャ アクティビティは実行されません。 複数のアップストリーム アクティビティの出力データセットを、ストアド プロシージャ アクティビティの入力データセットとして指定できます。 この場合、ストアド プロシージャ アクティビティは、入力データセットのすべてのスライスが使用可能である場合にのみ実行されます。

次の例では、コピー アクティビティの出力は OutputDataset です。これがストアド プロシージャ アクティビティの入力になります。 そのため、コピー アクティビティが完了し、OutputDataset のスライスが使用可能 (準備完了状態) になるまで、ストアド プロシージャ アクティビティは実行されません。 複数の入力データセットを指定した場合、入力データセットのすべてのスライスが使用可能 (準備完了状態) になるまで、ストアド プロシージャ アクティビティは実行されません。 入力データセットは、ストアド プロシージャ アクティビティのパラメーターとして直接使用することはできません。

連鎖するアクティビティの詳細については、「[パイプライン内の複数アクティビティ](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)」をご覧ください。

```json
{
    "name": "ADFTutorialPipeline",
    "properties": {
        "description": "Copy data from a blob to blob",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [ { "name": "InputDataset" } ],
                "outputs": [ { "name": "OutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst"
                },
                "name": "CopyFromBlobToSQL"
            },
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "SPSproc"
                },
                "inputs": [ { "name": "OutputDataset" } ],
                "outputs": [ { "name": "SQLOutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunStoredProcedure"
            }
        ],
        "start": "2017-04-12T00:00:00Z",
        "end": "2017-04-13T00:00:00Z",
        "isPaused": false,
    }
}
```

同様に、ストアド プロシージャ アクティビティを**ダウンストリーム アクティビティ** (ストアド プロシージャ アクティビティの完了後に実行されるアクティビティ) とリンクするには、ストアド プロシージャ アクティビティの出力データセットを、パイプラインのダウンストリーム アクティビティの入力として指定します。

> [!IMPORTANT]
> Azure SQL Database または SQL Server にデータをコピーする場合、**sqlWriterStoredProcedureName** プロパティを使用してストアド プロシージャを呼び出すように、コピー アクティビティで **SqlSink** を構成できます。 詳細については、[コピー アクティビティからのストアド プロシージャの呼び出し](data-factory-invoke-stored-procedure-from-copy-activity.md)に関する記事をご覧ください。 プロパティの詳細については、[Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties)、[SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties) の各コネクタに関する記事をご覧ください。
> 
> Azure SQL Database、SQL Server、または Azure SQL Data Warehouse からデータをコピーする場合、**sqlReaderStoredProcedureName** プロパティを使用して、ソース データベースからデータを読み取るストアド プロシージャを呼び出すように、コピー アクティビティで **SqlSource** を構成できます。 詳細については、[Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties)、[SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties)、[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) の各コネクタに関する記事をご覧ください。

## <a name="json-format"></a>JSON 形式
JSON 形式のストアド プロシージャ アクティビティの定義を次に示します。

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs": [ { "name": "inputtable" } ],
    "outputs": [ { "name": "outputtable" } ],
    "typeProperties":
    {
        "storedProcedureName": "<name of the stored procedure>",
        "storedProcedureParameters":
        {
            "param1": "param1Value"
            …
        }
    }
}
```

次の表に、JSON のプロパティを示します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| 名前 | アクティビティの名前 |はい |
| description |アクティビティの用途を説明するテキストです。 |いいえ |
| type | 次のように設定する必要があります。**SqlServerStoredProcedure** | はい |
| inputs | 省略可能。 入力データセットを指定した場合、ストアド プロシージャ アクティビティの実行に使用できる ("準備完了" 状態である) 必要があります。 ストアド プロシージャで入力データセットをパラメーターとして使用することはできません。 入力データセットは、ストアド プロシージャ アクティビティを開始する前に、依存関係の確認にのみ使用されます。 |いいえ |
| outputs | ストアド プロシージャ アクティビティの出力データセットを指定する必要があります。 出力データセットでは、ストアド プロシージャ アクティビティの**スケジュール** (毎時、毎週、毎月など) を指定します。 <br/><br/>出力データセットでは、ストアド プロシージャを実行する、Azure SQL Database、Azure SQL Data Warehouse、または SQL Server Database を表す**リンクされたサービス**を使用する必要があります。 <br/><br/>出力データセットは、パイプラインの別のアクティビティ ([連鎖するアクティビティ](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)) による後続処理のために、ストアド プロシージャの結果を渡す 1 つの方法として使用できます。 ただし、Data Factory では、ストアド プロシージャの出力をこのデータセットに自動的に書き込むわけではありません。 出力データセットが参照する SQL テーブルへの書き込みは、ストアド プロシージャが実行します。 <br/><br/>出力データセットに**ダミー データセット**を指定できる場合もあります。ダミー データセットは、ストアド プロシージャ アクティビティを実行するスケジュールの指定にのみ使用されます。 |はい |
| storedProcedureName |出力テーブルで使用するリンクされたサービスで表される Azure SQL データベース、Azure SQL Data Warehouse、または SQL Server データベースのストアド プロシージャの名前を指定します。 |はい |
| storedProcedureParameters |ストアド プロシージャのパラメーター値を指定します。 パラメーターで null を渡す必要がある場合は、構文として "param1": null (すべて小文字) を使用します。 このプロパティの使用方法については、次のサンプルをご覧ください。 |いいえ |

## <a name="passing-a-static-value"></a>静的な値を渡す
次に、‘Document sample’ という静的値を含む ‘Scenario’ という別の列をテーブルに追加する例を考えてましょう。

![サンプル データ 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**テーブル:**

```SQL
CREATE TABLE dbo.sampletable2
(
    Id uniqueidentifier,
    datetimestamp nvarchar(127),
    scenario nvarchar(127)
)
GO

CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable2(Id);
```

**ストアド プロシージャ:**

```SQL
CREATE PROCEDURE usp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

ストアド プロシージャ アクティビティから **Scenario** パラメーターとその値を渡します。 前のサンプルの「**typeProperties**」セクションは、次のスニペットのようになります。

```JSON
"typeProperties":
{
    "storedProcedureName": "usp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Data Factory データセット:**

```JSON
{
    "name": "sprocsampleout2",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "sampletable2"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Data Factory パイプライン**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample2",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
                        "Scenario": "Document sample"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout2"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
        "start": "2016-10-02T00:00:00Z",
        "end": "2016-10-02T05:00:00Z"
    }
}
```
