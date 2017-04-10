---
title: "Data Factory を使用して Amazon Redshift からデータを移動する | Microsoft Docs"
description: "Azure Data Factory を使用して Amazon Redshift からデータを移動する方法を説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 524b87d95d3060c780b296350797501847c80638
ms.lasthandoff: 03/29/2017


---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Azure Data Factory を使用して Amazon Redshift からデータを移動する
この記事では、Azure Data Factory のコピー アクティビティを使って、Amazon Redshift からデータを移動する方法について説明します。 この記事は、コピー アクティビティによるデータ移動の一般的な概要について説明している、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事に基づいています。 

Amazon Redshift から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)の表をご覧ください。 データ ファクトリは、他のデータ ストアから Amazon Redshift へのデータの移動ではなく、Amazon Redshift から他のデータ ストアへのデータの移動のみをサポートします。

## <a name="prerequisites"></a>前提条件
* データをオンプレミスのデータ ストアに移動する場合は、オンプレミス コンピューターに [Data Management Gateway](data-factory-data-management-gateway.md) をインストールする必要があります。 次に、Data Management Gateway (コンピューターの IP アドレスを使用) に、Amazon Redshift クラスターへのアクセスを付与します。 手順については、「 [クラスターへのアクセスを承認する](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 」を参照してください。
* Azure データ ストアにデータを移動する場合、Microsoft Azure データ センターで使用されるコンピューティング IP アドレス (SQL 範囲を含む) については 「 [Azure データ センターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653) 」を参照してください。

## <a name="getting-started"></a>使用の開始
さまざまなツール/API を使用して、Amazon Redshift ソースからデータを移動するコピー アクティビティを含むパイプラインを作成できます。

パイプラインを作成する最も簡単な方法は、**コピー ウィザード**を使うことです。 データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「 [チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md) 」をご覧ください。

**Azure Portal**、**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、**.NET API**、**REST API** などのツールを使ってパイプラインを作成することもできます。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。 

ツールまたは API のいずれを使用した場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。 

1. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。
2. コピー操作用の入力データと出力データを表す**データセット**を作成します。 
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む**パイプライン**を作成します。 

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 ツール/API (.NET API を除く) を使う場合、こうした Data Factory エンティティは、JSON 形式で定義します。  Amazon Redshift データ ストアからデータをコピーするときに使用する Data Factory エンティティの JSON 定義のサンプルについては、この記事の「[JSON の使用例: Amazon Redshift から Azure BLOB へのデータのコピー](#json-example-copy-data-from-amazon-redshift-to-azure-blob)」を参照してください。 

次のセクションでは、Amazon Redshift に固有の Data Factory エンティティの定義に使用される JSON プロパティについて詳しく説明します。 

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
次の表は、Amazon Redshift のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティを **AmazonRedshift**に設定する必要があります。 |はい |
| server |Amazon Redshift サーバーの IP アドレスまたはホスト名。 |はい |
| ポート |Amazon Redshift サーバーがクライアント接続のリッスンに使用する TCP ポートの数。 |いいえ、既定値: 5439 |
| database |Amazon Redshift データベースの名前。 |はい |
| username |データベースへのアクセスを持つユーザーの名前。 |はい |
| パスワード |ユーザー アカウントのパスワード。 |はい |

## <a name="dataset-properties"></a>データセットのプロパティ
データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。 構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **RelationalTable** 型のデータセット (Amazon Redshift データセットを含む) の typeProperties セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |リンクされたサービスが参照する Amazon Redshift データベースのテーブルの名前です。 |いいえ (**RelationalSource** の **クエリ** が指定されている場合) |

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります。

コピー アクティビティのソースの種類が **RelationalSource** (Amazon Redshift を含む) である場合は、typeProperties セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query |カスタム クエリを使用してデータを読み取ります。 |SQL クエリ文字列。 例: Select * from MyTable。 |いいえ (**データセット**の **tableName** が指定されている場合) |

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob"></a>JSON の使用例: Amazon Redshift から Azure BLOB へのデータのコピー
このサンプルは、Amazon Redshift データベースから Azure BLOB ストレージにデータをコピーする方法を示します。 Azure Data Factory のコピー アクティビティを使用して、 **こちら** に記載されているシンクのいずれかにデータを [直接](data-factory-data-movement-activities.md#supported-data-stores-and-formats) コピーすることもできます。  

このサンプルでは、次の Data Factory のエンティティがあります。

* [Amazon Redshift](#linked-service-properties)型のリンクされたサービス。
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
* [RelationalTable](#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
* [RelationalSource](#copy-activity-properties) と [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)

このサンプルは Amazon Redshift データベースのクエリ結果のデータを BLOB に 1 時間ごとにコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Amazon Redshift のリンクされたサービス:**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Azure Storage のリンクされたサービス:**

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
**Amazon Redshift の入力データセット:**

**”external” を true** に設定すると、データセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。 パイプラインのアクティビティで生成されない入力データセットで、このプロパティを true に設定します。

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

**Azure BLOB の出力データセット:**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

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

**Azure Redshift ソース (RelationalSource) および BLOB シンクを使用するパイプラインでのコピー アクティビティ:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **RelationalSource** に設定され、**sink** 型が **BlobSink** に設定されています。 **query** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。

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
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
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
[データ移動アクティビティ](data-factory-data-movement-activities.md) に関する記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

Amazon Redshift にデータを移動する場合、Amazon Redshift 型から .NET 型に対する次のマッピングが使用されます。

| Amazon Redshift 型 | .NET ベースの型 |
| --- | --- |
| SMALLINT |Int16 |
| INTEGER |Int32 |
| BIGINT |Int64 |
| DECIMAL |DECIMAL |
| REAL |Single |
| DOUBLE PRECISION |Double |
| BOOLEAN |String |
| CHAR |String |
| VARCHAR |String |
| DATE |DateTime |
| TIMESTAMP |DateTime |
| TEXT |String |

## <a name="map-source-to-sink-columns"></a>ソース列からシンク列へのマップ
ソース データセット列からシンク データセット列へのマッピングの詳細については、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページをご覧ください。

## <a name="repeatable-read-from-relational-sources"></a>リレーショナル ソースからの反復可能な読み取り
リレーショナル データ ストアからデータをコピーする場合は、意図しない結果を避けるため、再現性に注意する必要があります。 Azure Data Factory では、スライスを手動で再実行できます。 障害が発生したときにスライスを再実行できるように、データセットの再試行ポリシーを構成することもできます。 いずれかの方法でスライスが再実行された際は、何度スライスが実行されても同じデータが読み取られることを確認する必要があります。 [リレーショナル ソースからの反復可能な読み取り](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)に関するセクションをご覧ください

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
次の記事を参照してください。

* [コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) を参照してください。

