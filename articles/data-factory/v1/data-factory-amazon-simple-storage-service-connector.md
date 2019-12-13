---
title: Data Factory を使用して Amazon Simple Storage Service からデータを移動する
description: Azure Data Factory を使用した Amazon Simple Storage Service (S3) からのデータの移動方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 636d3179-eba8-4841-bcb4-3563f6822a26
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b23be9901df7ca435f412d9f49e1a7ad88382ade
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74924837"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Azure Data Factory を使用した Amazon Simple Storage Service からのデータの移動
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](data-factory-amazon-simple-storage-service-connector.md)
> * [バージョン 2 (最新バージョン)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 最新バージョンの Data Factory サービスを使用している場合は、[V2 の Amazon S3 コネクタ](../connector-amazon-simple-storage-service.md)に関するページを参照してください。

この記事では、Azure Data Factory のコピー アクティビティを使って、Amazon Simple Storage Service (S3) からデータを移動する方法について説明します。 この記事は、コピー アクティビティによるデータ移動の一般的な概要について説明している、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事に基づいています。

Amazon S3 から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)の表をご覧ください。 データ ファクトリは、他のデータ ストアから Amazon S3 へのデータの移動ではなく、Amazon S3 から他のデータ ストアへのデータの移動のみをサポートします。

## <a name="required-permissions"></a>必要なアクセス許可
Amazon S3 からデータをコピーするには、次のアクセス許可が付与されている必要があります。

* Amazon S3 オブジェクトの操作には `s3:GetObject` と `s3:GetObjectVersion`。
* Amazon S3 バケットの操作には `s3:ListBucket`。 Data Factory コピー ウィザードを使用している場合は、`s3:ListAllMyBuckets` も必要です。

Amazon S3 のアクセス許可の完全な一覧については、「[ポリシーでのアクセス許可の指定](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html)」を参照してください。

## <a name="getting-started"></a>使用の開始
さまざまなツールや API を使用して、Amazon S3 ソースからデータを移動するコピー アクティビティを含むパイプラインを作成できます。

パイプラインを作成する最も簡単な方法は、**コピー ウィザード**を使うことです。 簡単なチュートリアルについては、「[チュートリアル:コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」をご覧ください。

また、次のツールを使用してパイプラインを作成することもできます。**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、 **.NET API**、**REST API**。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。

ツールと API のいずれを使用する場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。

1. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。
2. コピー操作用の入力データと出力データを表す**データセット**を作成します。
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む**パイプライン**を作成します。

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 (.NET API を除く) ツールまたは API を使う場合は、JSON 形式でこれらの Data Factory エンティティを定義します。 Amazon S3 データ ストアからデータをコピーするために使用される Data Factory エンティティ用の JSON 定義のサンプルについては、この記事の「[JSON の使用例:Amazon S3 から Azure BLOB にデータをコピーする](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage)」セクションを参照してください。

> [!NOTE]
> コピー アクティビティでサポートされているファイルと使用する圧縮形式に関する詳細は、「[Azure Data Factory のファイルおよび圧縮形式](data-factory-supported-file-and-compression-formats.md)」を参照してください。

次のセクションでは、Amazon S3 に固有の Data Factory エンティティの定義に使用される JSON プロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
リンクされたサービスは、データ ストアをデータ ファクトリにリンクします。 Amazon S3 データ ストアをデータ ファクトリにリンクするには、**AwsAccessKey** 型のリンクされたサービスを作成します。 次の表は、Amazon S3 (AwsAccessKey) のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| accessKeyID |シークレット アクセス キーの ID。 |string |はい |
| secretAccessKey |シークレット アクセス キー自体。 |暗号化された秘密文字列 |はい |

>[!NOTE]
>IAM アカウントが Amazon S3 からデータをコピーするには、このコネクタにアクセス キーが必要です。 [一時的なセキュリティ資格情報](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html)はサポートされていません。
>

たとえば次のようになります。

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ
データセットを指定して Azure Blob Storage の入力データを表すには、そのデータセットの type プロパティを **AmazonS3** に設定します。 また、データセットの **linkedServiceName** プロパティは、Amazon S3 のリンクされたサービスの名前に設定します。 データセットの定義に使用できるセクションとプロパティの完全な一覧については、[データセットの作成](data-factory-create-datasets.md)に関する記事をご覧ください。 

構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (SQL Database、Azure BLOB、Azure テーブルなど) でほぼ同じです。 **typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **AmazonS3** 型のデータセットの **typeProperties** セクション (Amazon S3 データセットを含む) には次のプロパティがあります。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| bucketName |S3 バケットの名前。 |string |はい |
| key |S3 オブジェクト キー。 |string |いいえ |
| prefix |S3 オブジェクト キーのプレフィックス。 キーがこのプレフィックスで始まるオブジェクトが選択されます。 キーが空の場合にのみ適用されます。 |string |いいえ |
| version |S3 のバージョン管理が有効になっている場合の S3 オブジェクトのバージョン。 |string |いいえ |
| format | 次の種類の形式がサポートされます:**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text 形式](data-factory-supported-file-and-compression-formats.md#text-format)、[Json 形式](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro 形式](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc 形式](data-factory-supported-file-and-compression-formats.md#orc-format)、[Parquet 形式](data-factory-supported-file-and-compression-formats.md#parquet-format) の各セクションを参照してください。 <br><br> ファイルベースのストア間でファイルをそのままコピー (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。 | |いいえ |
| compression | データの圧縮の種類とレベルを指定します。 サポートされている型は次のとおりです。**GZip**、**Deflate**、**BZip2**、**ZipDeflate** です。 サポートされるレベルは、**Optimal** と **Fastest** です。 詳細については、「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md#compression-support)」を参照してください。 | |いいえ |


> [!NOTE]
> **bucketName とキーの組み合わせ**によって S3 オブジェクトの場所が指定されます。バケットは S3 オブジェクトのルート コンテナーであり、キーは、S3 オブジェクトへの完全パスです。

### <a name="sample-dataset-with-prefix"></a>プレフィックスを持つサンプル データセット

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "testbucket",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
### <a name="sample-dataset-with-version"></a>サンプル データセット (バージョンあり)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "testbucket",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="dynamic-paths-for-s3"></a>S3 の動的パス
前のサンプルは、Amazon S3 データセット内の **key** プロパティと **bucketName** プロパティの固定値を使用します。

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

実行時に、SliceStart などのシステム変数を使用して、Data Factory でこれらのプロパティを動的に計算できます。

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

同じことを、Amazon S3 データセットの **prefix** プロパティにも行うことができます。 サポートされる関数と変数の一覧については、「[Data Factory の関数およびシステム変数](data-factory-functions-variables.md)」を参照してください。

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。 アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、プロパティはソースとシンクの種類によって異なります。 コピー アクティビティのソースの種類が **FileSystemSource** (Amazon S3を含む) である場合は、**typeProperties** セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| recursive |ディレクトリで S3 オブジェクトを再帰的に一覧表示するかどうかを指定します。 |true または false |いいえ |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>JSON の使用例:Amazon S3 から Azure Blob Storage にデータをコピーする
このサンプルは、Amazon S3 から Azure Blob Storage にデータをコピーする方法を示します。 Data Factory のコピー アクティビティを使用して、[サポートされている任意のシンク](data-factory-data-movement-activities.md#supported-data-stores-and-formats)データを直接コピーすることもできます。

この例は、次の Data Factory エンティティの JSON 定義を示しています。 [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) または [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) のいずれかで、この定義を使用して、Amazon S3 から Blob ストレージにデータをコピーするためのパイプラインを作成できます。   

* [AwsAccessKey](#linked-service-properties)型のリンクされたサービス。
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
* [AmazonS3](#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
* [FileSystemSource](#copy-activity-properties) および [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプル データでは、1 時間おきに Amazon S3 から Azure BLOB にデータがコピーされます。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

### <a name="amazon-s3-linked-service"></a>Amazon S3 のリンクされたサービス

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure Storage のリンクされたサービス

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

### <a name="amazon-s3-input-dataset"></a>Amazon S3 の入力データセット

**"external": true** を設定すると、データセットが Data Factory の外部にあることが Data Factory サービスに通知されます。 パイプラインのアクティビティで生成されない入力データセットで、このプロパティを true に設定します。

```json
    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }
```


### <a name="azure-blob-output-dataset"></a>Azure BLOB の出力データセット

データは新しい BLOB に 1 時間おきに書き込まれます (frequency: hour、interval: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Amazon S3 ソースおよび BLOB シンクを使用するパイプラインでのコピー アクティビティ

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **FileSystemSource** に設定され、**sink** 型が **BlobSink** に設定されています。

```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource",
                        "recursive": true
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AmazonS3InputDataset"
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
                "name": "AmazonS3ToBlob"
            }
        ],
        "start": "2014-08-08T18:00:00Z",
        "end": "2014-08-08T19:00:00Z"
    }
}
```
> [!NOTE]
> ソース データセット列からシンク データセット列へのマッピングについては、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページをご覧ください。


## <a name="next-steps"></a>次の手順
次の記事を参照してください。

* Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」をご覧ください。

* コピー アクティビティを使用したパイプライン作成の詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。
