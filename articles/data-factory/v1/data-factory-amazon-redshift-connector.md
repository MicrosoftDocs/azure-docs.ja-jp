---
title: Azure Data Factory を使用して Amazon Redshift からデータを移動する
description: Azure Data Factory のコピー アクティビティを使用して Amazon Redshift からデータを移動する方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3c9e10df9f2be2a07bc7b7af0e01905d5b278d35
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74924884"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Azure Data Factory を使用して Amazon Redshift からデータを移動する
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](data-factory-amazon-redshift-connector.md)
> * [バージョン 2 (最新バージョン)](../connector-amazon-redshift.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 最新バージョンの Data Factory サービスを使用している場合は、[V2 の Amazon Redshift コネクタ](../connector-amazon-redshift.md)に関するページをご覧ください。

この記事では、Azure Data Factory のコピー アクティビティを使って、Amazon Redshift からデータを移動する方法について説明します。 この記事は、コピー アクティビティによるデータ移動の一般的な概要について説明している、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事に基づいています。

現在 Data Factory でサポートされているのは、Amazon Redshift から[サポートされているシンク データ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)へのデータ移動だけです。 他のデータ ストアから Amazon Redshift へのデータ移動はサポートされません。

> [!TIP]
> Amazon Redshift から大量のデータをコピーするときに最適なパフォーマンスを得るには、Amazon Simple Storage Service (Amazon S3) 経由で組み込みの Redshift **UNLOAD** コマンドを使用することを検討してください。 詳細については、「[Amazon Redshift からのデータ コピーで UNLOAD を使用する](#use-unload-to-copy-data-from-amazon-redshift)」を参照してください。

## <a name="prerequisites"></a>前提条件
* データをオンプレミスのデータ ストアに移動する場合は、オンプレミス コンピューターに [Data Management Gateway](data-factory-data-management-gateway.md) をインストールする必要があります。 オンプレミスのマシンの IP アドレスを使用してゲートウェイが Amazon Redshift クラスターにアクセスすることを許可します。 手順については、「[Authorize access to the cluster (クラスターへのアクセスの許可)](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html)」を参照してください。
* Azure データ ストアにデータを移動する場合は、[Microsoft Azure データセンターで使用されるコンピューティング IP アドレスと SQL 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に関するページを参照してください。

## <a name="getting-started"></a>使用の開始
さまざまなツールと API を使用して、Amazon Redshift ソースからデータを移動するコピー アクティビティが含まれたパイプラインを作成できます。

パイプラインを作成する最も簡単な方法は、Azure Data Factory コピー ウィザードを使うことです。 コピー ウィザードを使用してパイプラインを作成する簡単な手順については、[コピー ウィザードを使用したパイプラインの作成のチュートリアル](data-factory-copy-data-wizard-tutorial.md)に関するページを参照してください。

また、Visual Studio、Azure PowerShell、またはその他のツールを使用してパイプラインを作成することもできます。 Azure Resource Manager テンプレート、.NET API、または REST API を使用してパイプラインを作成することもできます。 コピー アクティビティが含まれたパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。

ツールと API のいずれを使用する場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。

1. リンクされたサービスを作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。
2. コピー操作用の入力データと出力データを表すデータセットを作成します。
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティが含まれたパイプラインを作成します。

コピー ウィザードを使用すると、これらの Data Factory エンティティの JSON 定義が自動的に作成されます。 (.NET API を除く) ツールまたは API を使う場合は、JSON 形式でこれらの Data Factory エンティティを定義します。 JSON の使用例:「Amazon Redshift から Azure Blob Storage へのデータのコピー」のページに、Amazon Redshift データ ストアからデータをコピーするときに使用する Data Factory エンティティの JSON 定義が紹介されています。

次のセクションでは、Amazon Redshift 用の Data Factory エンティティの定義に使用される JSON プロパティについて説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

次の表は、Amazon Redshift のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| **type** |このプロパティを **AmazonRedshift** に設定する必要があります。 |はい |
| **server** |Amazon Redshift サーバーの IP アドレスまたはホスト名。 |はい |
| **port** |Amazon Redshift サーバーがクライアント接続のリッスンに使用する TCP ポートの数。 |いいえ (既定値は 5439) |
| **database** |Amazon Redshift データベースの名前。 |はい |
| **username** |データベースへのアクセス権があるユーザーの名前。 |はい |
| **password** |ユーザー アカウントのパスワードです。 |はい |

## <a name="dataset-properties"></a>データセットのプロパティ

データセットの定義に使用できるセクションとプロパティの完全な一覧については、[データセットの作成](data-factory-create-datasets.md)に関する記事を参照してください。 **structure**、**availability**、および **policy** セクションは、データセットのすべての型でほぼ同じです。 データセットの型には、Azure SQL、Azure Blob Storage、Azure Table Storage などがあります。

**typeProperties** セクションはデータセット型ごとに異なり、ストアのデータの場所などに関する情報を提供します。 **RelationalTable** 型のデータセット (Amazon Redshift データセットを含む) の **typeProperties** セクションには、次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| **tableName** |リンクされたサービスが参照する Amazon Redshift データベースのテーブルの名前です。 |いいえ (種類が **RelationalSource** のコピー アクティビティの **query** プロパティが指定されている場合) |

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に使用できるセクションとプロパティの一覧については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 **name**、**description**、**inputs** テーブル、**outputs** テーブル、および **policy** プロパティは、あらゆる種類のアクティビティで使用できます。 **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、プロパティはデータ ソースとシンクの種類によって異なります。

コピー アクティビティのソースの種類が **AmazonRedshiftSource** である場合は、**typeProperties** セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| **query** | カスタム クエリを使用してデータを読み取ります。 |いいえ (データセットの **tableName** プロパティが指定されている場合) |
| **redshiftUnloadSettings** | Redshift の **UNLOAD** コマンドを使用する場合のプロパティ グループが含まれます。 | いいえ |
| **s3LinkedServiceName** | 中間ストアとして使用する Amazon S3 です。 リンクされたサービスは、**AwsAccessKey** 型の Azure Data Factory 名を使用して指定します。 | **redshiftUnloadSettings** プロパティを使用する場合に必要です |
| **bucketName** | 中間データを格納するための Amazon S3 バケットを示します。 このプロパティを指定しない場合、コピー アクティビティによってバケットが自動生成されます。 | **redshiftUnloadSettings** プロパティを使用する場合に必要です |

また、(Amazon Redshift を含む) **RelationalSource** 型を **typeProperties** セクションの以下のプロパティで使用することもできます。 このソースの型では Redshift の **UNLOAD** コマンドがサポートされないことに注意してください。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| **query** |カスタム クエリを使用してデータを読み取ります。 | いいえ (データセットの **tableName** プロパティが指定されている場合) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Amazon Redshift からのデータ コピーで UNLOAD を使用する

Amazon Redshift の [**UNLOAD**](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) コマンドでは、クエリの結果が Amazon S3 上の 1 つ以上のファイルにアンロードされます。 このコマンドは、Redshift から大きなデータセットをコピーするための方法として Amazon から推奨されています。

**例:Amazon Redshift から Azure SQL Data Warehouse へのデータのコピー**

この例では、Amazon Redshift から Azure SQL Data Warehouse にデータをコピーします。 この例では、Redshift の **UNLOAD** コマンド、ステージングされたコピー データ、Microsoft PolyBase を使用しています。

このサンプル ユース ケースでは、コピー アクティビティを使用して、**redshiftUnloadSettings** オプションで構成されているように、最初に Amazon Redshift から Amazon S3 にデータをアンロードします。 次に、**stagingSettings** オプションの指定に従って、Amazon S3 から Azure Blob Storage にデータをコピーします。 最後に、PolyBase によってデータが SQL Data Warehouse に読み込まれます。 すべての中間形式は、コピー アクティビティによって処理されます。

![Amazon Redshift から SQL Data Warehouse へのコピーのワークフロー](media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>JSON の使用例:Amazon Redshift から Azure Blob Storage へのデータのコピー
このサンプルは、Amazon Redshift データベースから Azure Blob Storage にデータをコピーする方法を示します。 データは、コピー アクティビティを使用して、任意の[サポートされているシンク](data-factory-data-movement-activities.md#supported-data-stores-and-formats)に直接コピーできます。

このサンプルでは、次の Data Factory のエンティティがあります。

* [AmazonRedshift](#linked-service-properties) 型のリンクされたサービス
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
* [RelationalTable](#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)
* [RelationalSource](#copy-activity-properties) プロパティと [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties) プロパティを使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)

このサンプルでは、Amazon Redshift のクエリ結果から Azure BLOB にデータを 1 時間ごとにコピーします。 サンプルに使用されている JSON プロパティについては、エンティティの定義に続くセクションで説明しています。

**Amazon Redshift のリンクされたサービス**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": "<The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>",
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Azure BLOB ストレージのリンクされたサービス**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Amazon Redshift の入力データセット**

データセットがデータ ファクトリの外部にあることを Data Factory サービスに通知するために、**external** プロパティが "true" に設定されています。 このプロパティ設定は、データセットがデータ ファクトリのアクティビティによって生成されないことを示します。 パイプラインのアクティビティで生成されない入力データセットでは、このプロパティを true に設定します。

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Azure BLOB の出力データセット**

**frequency** プロパティを "Hour" に、**interval** プロパティを 1 に設定することによって、新しい BLOB に 1 時間おきにデータを書き込みます。 BLOB の **folderPath** プロパティは動的に評価されます。 プロパティの値は、処理されているスライスの開始時刻に基づきます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Azure Redshift ソース (RelationalSource 型) および Azure BLOB シンクを使用するパイプラインでのコピー アクティビティ**

パイプラインには、入力データセットと出力データセットを使うように構成されたコピー アクティビティが含まれます。 パイプラインは、1 時間おきに実行するようにスケジュールされています。 パイプラインの JSON 定義で、**source** の型が **RelationalSource** に設定され、**sink** の型が **BlobSink** に設定されています。 **query** プロパティに指定されている SQL クエリは、コピーするデータを過去 1 時間分のデータから選択します。

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Amazon Redshift の型マッピング
[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事のとおり、コピー アクティビティは、型を source から sink に自動的に変換します。 型は、次の 2 段階のアプローチを使用して変換されます。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

コピー アクティビティでデータを Amazon Redshift 型から .NET 型に変換するときに、次のマッピングが使用されます。

| Amazon Redshift 型 | .NET 型 |
| --- | --- |
| SMALLINT |Int16 |
| INTEGER |Int32 |
| BIGINT |Int64 |
| DECIMAL |Decimal |
| REAL |Single |
| DOUBLE PRECISION |Double |
| BOOLEAN |string |
| CHAR |string |
| VARCHAR |string |
| DATE |DateTime |
| TIMESTAMP |DateTime |
| TEXT |string |

## <a name="map-source-to-sink-columns"></a>ソース列からシンク列へのマップ
ソース データセット列のシンク データセット列へのマッピング方法の詳細については、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページを参照してください。

## <a name="repeatable-reads-from-relational-sources"></a>リレーショナル ソースからの反復可能な読み取り
リレーショナル データ ストアからデータをコピーする場合は、意図しない結果を避けるため、再現性に注意する必要があります。 Azure Data Factory では、スライスを手動で再実行できます。 障害が発生したときにスライスを再実行する再試行**ポリシー**をデータセットに構成することもできます。 何度スライスが実行されても同じデータが読み取られるようにしてください。 また、どのようにスライスを再実行するかに関係なく同じデータが読み取られるようにしてください。 詳細については、「[リレーショナル ソースからの反復可能な読み取り](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)」を参照してください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
コピー アクティビティのパフォーマンスに影響を及ぼす主な要因とパフォーマンスを最適化する方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。

## <a name="next-steps"></a>次の手順
コピー アクティビティを使用したパイプライン作成の詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。
