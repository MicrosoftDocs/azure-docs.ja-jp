---
title: Azure Data Factory を使用して SQL Server データを SQL Azure へ - Team Data Science Process
description: オンプレミスとクラウド内のデータベース間で毎日同時にデータを移動する 2 つのデータ移行アクティビティを構成する ADF パイプラインを設定します。
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 59f8b8b253fc914e5723a9c41475ec78bc3f376e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57888401"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Azure Data Factory を使用してオンプレミスの SQL Server から SQL Azure にデータを移動する

この記事では、Azure Data Factory (ADF) を使用して、オンプレミスの SQL Server データベースから Azure Blob Storage を経由して SQL Azure データベースにデータを移動する方法を説明します。

Azure SQL Database にデータを移動するためのさまざまなオプションをまとめた表については、「[Azure Machine Learning 用にデータを Azure SQL Database に移動する](move-sql-azure.md)」を参照してください。

## <a name="intro"></a>概要:ADF の説明とデータの移行に ADF を使用するべきタイミング
Azure Data Factory は、データの移動や変換を調整し自動化する、フル マネージドのクラウドベースのデータ統合サービスです。 ADF モデルにおける主要な概念は、パイプラインです。 パイプラインとはアクティビティの論理グループであり、各アクティビティによって、データセットに含まれているデータに対して実行するアクションを定義します。 リンクされたサービスは、Data Factory がデータ リソースに接続するために必要な情報を定義するために使用されます。

ADF を使用すると、既存のデータ処理サービスを、可用性が高く、クラウドで管理されるデータ パイプラインに組み込むことができます。 データの取り込み、準備、変換、分析、発行を行うために、これらのデータ パイプラインをスケジュールできます。ADF がデータと処理の複雑な依存関係を管理して調整します。 増加するオンプレミスのデータ ソースとクラウドのデータ ソースを接続するソリューションをクラウド内で迅速に構築してデプロイすることができます。

次の場合に ADF の使用を検討してください。

* オンプレミスとクラウドの両方のリソースにアクセスするハイブリッド シナリオで、データを継続的に移行する必要がある場合
* 移行時にデータを処理する場合や、移行時にデータを変更したり、ビジネス ロジックを追加したりする必要がある場合

ADF では、定期的にデータの移動を管理するシンプルな JSON スクリプトを使用して、ジョブのスケジュールと監視ができます。 ADF には他にも、複雑な操作のサポートなどの機能があります。 詳細については、 [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/)にあるドキュメントを参照してください。

## <a name="scenario"></a>シナリオ
2 つのデータ移行アクティビティを構成する ADF パイプラインを設定します。 これらのアクティビティは、オンプレミスの SQL Database とクラウドの Azure SQL Database 間で毎日データを移動します。 2 つのアクティビティは次のとおりです。

* オンプレミスの SQL Server データベースから Azure BLOB ストレージ アカウントにデータをコピーする
* Azure BLOB ストレージ アカウントから Azure SQL Database にデータをコピーする

> [!NOTE]
> ここで示す手順は、ADF チームが提供するより詳細なチュートリアル「[オンプレミスの SQL Server データベースから Azure Blob Storage にデータをコピーする](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal/)」から抜粋したものです。また、このトピックの関連セクションへの参照が適宜提供されています。
>
>

## <a name="prereqs"></a>前提条件
このチュートリアルでは、以下があることを前提としています。

* **Azure サブスクリプション**。 サブスクリプションがない場合は、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
* **Azure ストレージ アカウント**。 このチュートリアルでは、データの格納に Azure ストレージ アカウントを使用します。 Azure ストレージ アカウントがない場合は、「 [ストレージ アカウントの作成](../../storage/common/storage-quickstart-create-account.md) 」を参照してください。 ストレージ アカウントを作成したら、ストレージへのアクセスに使用するアカウント キーを取得する必要があります。 「[ストレージ アクセス キーの管理](../../storage/common/storage-account-manage.md#access-keys)」を参照してください。
* **Azure SQL Database**へのアクセス権。 Azure SQL Database をセットアップする必要がある場合、Azure SQL Database の新しいインスタンスをプロビジョニングする方法については、[Microsoft Azure SQL Database の概要](../../sql-database/sql-database-get-started.md)に関するページをご覧ください。
* **Azure PowerShell** がローカルにインストールされ構成されていること。 手順については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)」を参照してください。

> [!NOTE]
> この手順では、 [Azure ポータル](https://portal.azure.com/)を使用します。
>
>

## <a name="upload-data"></a> オンプレミスの SQL Server にデータをアップロードする
[NYC タクシー データセット](https://chriswhong.com/open-data/foil_nyc_taxi/) を使用して、移行プロセスを説明します。 NYC タクシー データセットは、記事に記載されているように、Azure BLOB ストレージの [NYC タクシー データ](https://www.andresmh.com/nyctaxitrips/)から入手できます。 データには、乗車の詳細を含む trip_data.csv ファイルと、乗車ごとの料金の詳細を含む trip_far.csv ファイルの 2 つのファイルがあります。 これらのファイルのサンプルと説明は、「 [NYC タクシー乗車データセットの説明](sql-walkthrough.md#dataset)」にあります。

ここに示されている手順は、自身のデータに適用することも、NYC タクシー データセットを使用してこの手順に従って行うこともできます。 NYC タクシー データセットを自身のオンプレミスの SQL Server データベースにアップロードするには、「[SQL Server データベースにデータを一括インポートする](sql-walkthrough.md#dbload)」に記載されている手順に従います。 これらは Azure Virtual Machine 上の SQL Server にアップロードする手順ですが、オンプレミスの SQL Server へのアップロード手順も同じです。

## <a name="create-adf"></a> Azure Data Factory を作成する
[Azure Portal](https://portal.azure.com/) で新しい Azure Data Factory とリソース グループを作成する手順については、「[Azure Data Factory を作成する](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory)」をご覧ください。 新しい ADF インスタンスに *adfdsp* という名前を付け、作成されたリソース グループに *adfdsprg* という名前を付けます。

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Azure Data Factory Integration Runtime をインストールして構成する
Integration Runtime は、異なるネットワーク環境間でデータ統合機能を提供するために Azure Data Factory によって使用される、マネージド データ統合インフラストラクチャです。 このランタイムは、以前は "Data Management Gateway" と呼ばれていました。

セットアップするには、[パイプラインの作成手順に従ってください](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline)

## <a name="adflinkedservices"></a>データ リソースに接続するためにリンクされたサービスを作成する
リンクされたサービスは、Azure Data Factory がデータ リソースに接続するために必要な情報を定義します。 このシナリオには、リンクされたサービスを必要とする 3 つのリソースがあります。

1. オンプレミスの SQL Server
2. Azure Blob Storage
3. Azure SQL データベース

リンクされたサービスを作成するための手順は、「[リンクされたサービスを作成する](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)」を参照してください。


## <a name="adf-tables"></a>データセットへのアクセス方法を指定するためのテーブルを定義して作成する
以下のスクリプトベースの手順に従って、データセットの構造、場所、可用性を指定するテーブルを作成します。 テーブルを定義するには、JSON ファイルを使用します。 これらのファイルの構造の詳細については、「 [データセット](../../data-factory/concepts-datasets-linked-services.md)」を参照してください。

> [!NOTE]
> コマンドの実行に適した Azure サブスクリプションが選択されていることを確認するために、`Add-AzureAccount` コマンドレットを実行してから [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) コマンドレットを実行する必要があります。 このコマンドレットの説明については、「 [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0)」を参照してください。
>
>

テーブル内の JSON ベースの定義では、次の名前が使用されます。

* オンプレミスの SQL サーバーでは、**テーブル名**は *nyctaxi_data* です。
* the **コンテナー名** は *containername*

この ADF パイプラインには、次の 3 つのテーブル定義が必要です。

1. [オンプレミスの SQL テーブル](#adf-table-onprem-sql)
2. [BLOB テーブル](#adf-table-blob-store)
3. [SQL Azure テーブル](#adf-table-azure-sql)

> [!NOTE]
> 次の手順では、Azure PowerShell を使用して ADF アクティビティの定義と作成を行います。 これらのタスクは、Azure ポータルを使用して実行することもできます。 詳しくは、「[データセットを作成する](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)」をご覧ください。
>
>

### <a name="adf-table-onprem-sql"></a>オンプレミスの SQL テーブル
オンプレミス SQL Server のテーブル定義は、次の JSON ファイルで指定されています。

```json
{
    "name": "OnPremSQLTable",
    "properties":
    {
        "location":
        {
            "type": "OnPremisesSqlServerTableLocation",
            "tableName": "nyctaxi_data",
            "linkedServiceName": "adfonpremsql"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1,
            "waitOnExternal":
            {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

ここでは列名が含まれていません。 ここで列名を含めることで、列名を副選択できます (詳細については、 [ADF のドキュメント](../../data-factory/copy-activity-overview.md) をご覧ください)。

テーブルの JSON 定義を *onpremtabledef.json* というファイルにコピーし、それを既知の場所に保存します (ここでは、*C:\temp\onpremtabledef.json*)。 次の Azure PowerShell コマンドレッドを使用して、ADF 内にテーブルを作成します。

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store"></a>BLOB テーブル
以下は、出力 BLOB の場所用のテーブル定義です (これはオンプレミスから取り込まれたデータを Azure BLOB にマップします)。

```json
{
    "name": "OutputBlobTable",
    "properties":
    {
        "location":
        {
            "type": "AzureBlobLocation",
            "folderPath": "containername",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            },
            "linkedServiceName": "adfds"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

テーブルの JSON 定義を *bloboutputtabledef.json* というファイルにコピーし、それを既知の場所に保存します (ここでは、*C:\temp\bloboutputtabledef.json*)。 次の Azure PowerShell コマンドレッドを使用して、ADF 内にテーブルを作成します。

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json

### <a name="adf-table-azure-sql"></a>SQL Azure テーブル
以下は、SQL Azure 出力の場所用のテーブル定義です (このスキーマは BLOB からのデータをマップします)。

```json
{
    "name": "OutputSQLAzureTable",
    "properties":
    {
        "structure":
        [
            { "name": "column1", "type": "String"},
            { "name": "column2", "type": "String"}
        ],
        "location":
        {
            "type": "AzureSqlTableLocation",
            "tableName": "your_db_name",
            "linkedServiceName": "adfdssqlazure_linked_servicename"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

テーブルの JSON 定義を *AzureSqlTable.json* というファイルにコピーし、それを既知の場所に保存します (ここでは、*C:\temp\AzureSqlTable.json*)。 次の Azure PowerShell コマンドレッドを使用して、ADF 内にテーブルを作成します。

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json


## <a name="adf-pipeline"></a>パイプラインを作成して定義する
次のスクリプトベースの手順に従って、パイプラインに属するアクティビティを指定し、パイプラインを作成します。 パイプラインのプロパティを定義するため、JSON ファイルを使用します。

* このスクリプトでは、 **パイプライン名** を *AMLDSProcessPipeline*としています。
* また、既定の実行時間 (12 am UTC) を使用して、ジョブが毎日実行されるようにパイプラインの周期性を設定していることにも注目してください。

> [!NOTE]
> 次の手順では、Azure PowerShell を使用して ADF パイプラインの定義と作成を行います。 このタスクは、Azure ポータルを使用して実行することもできます。 詳しくは、「[パイプラインの作成](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)」をご覧ください。
>
>

前述のテーブル定義を使用して、ADF のパイプライン定義を次のように指定します。

```json
{
    "name": "AMLDSProcessPipeline",
    "properties":
    {
        "description" : "This pipeline has one Copy activity that copies data from an on-premises SQL to Azure blob",
        "activities":
        [
            {
                "name": "CopyFromSQLtoBlob",
                "description": "Copy data from on-premises SQL server to blob",
                "type": "CopyActivity",
                "inputs": [ {"name": "OnPremSQLTable"} ],
                "outputs": [ {"name": "OutputBlobTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from nyctaxi_data"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            },
            {
                "name": "CopyFromBlobtoSQLAzure",
                "description": "Push data to Sql Azure",
                "type": "CopyActivity",
                "inputs": [ {"name": "OutputBlobTable"} ],
                "outputs": [ {"name": "OutputSQLAzureTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "BlobSource"
                    },
                    "sink":
                    {
                        "type": "SqlSink",
                        "WriteBatchTimeout": "00:5:00",
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 2,
                    "timeout": "02:00:00"
                }
            }
        ]
    }
}
```

パイプラインのこの JSON 定義を *pipelinedef.json* というファイルにコピーし、それを既知の場所に保存します (ここでは、*C:\temp\pipelinedef.json*)。 次の Azure PowerShell コマンドレッドを使用して、ADF 内にパイプラインを作成します。

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>パイプラインを開始する
これで、次のコマンドを使用してパイプラインを実行できます。

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

*startdate* と *enddate* のパラメーター値を、パイプラインを実行する実際の開始日と終了日に置き換える必要があります。

パイプラインを実行すると、BLOB に選択したコンテナー内に表示されるデータを確認することができます (1 日につき 1 ファイル)。

ADF が提供するデータを段階的にパイプ処理する機能をまだ活用していないことに注意してください。 これを行う方法と ADF が提供するその他の機能の詳細については、 [ADF のドキュメント](https://azure.microsoft.com/services/data-factory/)をご覧ください。
