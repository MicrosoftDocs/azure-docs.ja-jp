---
title: Azure Table との間でデータを移動する
description: Azure Data Factory を使用して Azure Table Storage に、または Azure Table Storage からデータを移動する方法を説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 07b046b1-7884-4e57-a613-337292416319
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 462d54a9d89d6f03aed5e221fa02609da786c8c1
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74918732"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Azure Data Factory を使用した Azure テーブルとの間でのデータの移動
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](data-factory-azure-table-connector.md)
> * [バージョン 2 (最新バージョン)](../connector-azure-table-storage.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[V2 の Azure Table Storage コネクタ](../connector-azure-table-storage.md)に関するページを参照してください。

この記事では、Azure Data Factory のコピー アクティビティを使って、Azure Table Storage との間でデータを移動する方法について説明します。 この記事は、コピー アクティビティによるデータ移動の一般的な概要について説明している、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事に基づいています。 

サポートされる任意のソース データ ストアのデータを Azure Table Storage にコピーしたり、Azure Table Storage のデータをサポートされる任意のシンク データ ストアにコピーしたりできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)に関するページの表をご覧ください。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>使用の開始
さまざまなツールや API を使用して、Azure Table Storage との間でデータを移動するコピー アクティビティを含むパイプラインを作成できます。

パイプラインを作成する最も簡単な方法は、**コピー ウィザード**を使うことです。 手順については、「[チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」を参照してください。データのコピー ウィザードを使用してパイプラインを作成する簡単なチュートリアルです。

また、次のツールを使用してパイプラインを作成することもできます。**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、 **.NET API**、**REST API**。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。 

ツールと API のいずれを使用する場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。 

1. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。
2. コピー操作用の入力データと出力データを表す**データセット**を作成します。 
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む**パイプライン**を作成します。 

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 (.NET API を除く) ツールまたは API を使う場合は、JSON 形式でこれらの Data Factory エンティティを定義します。 Azure Table Storage との間でのデータ コピーに使用する Data Factory エンティティの JSON 定義サンプルは、この記事の「[JSON の使用例](#json-examples)」セクションをご覧ください。

以下のセクションでは、Azure Table Storage に固有の Data Factory エンティティの定義に使用される JSON プロパティの詳細を説明します。 

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
Azure BLOB ストレージを Azure Data Factory にリンクするために使用できるリンクされたサービスは 2 種類あります。 次に例を示します。それらは、**AzureStorage** のリンクされたサービスと **AzureStorageSas** のリンクされたサービスです。 Azure Storage のリンクされたサービスは、Azure Storage へのグローバル アクセスを Data Factory に提供します。 一方、Azure Storage SAS (Shared Access Signature) のリンクされたサービスは、Azure Storage への制限付き/期限付きアクセスを Data Factory に提供します。 これら 2 つのリンクされたサービスには、これ以外の相違点はありません。 ニーズに適したリンクされたサービスを選択します。 以下のセクションで、これら 2 つのリンクされたサービスについて詳しく説明します。

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>データセットのプロパティ
データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

typeProperties セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **AzureTable** 型のデータセットの **typeProperties** セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |リンクされたサービスが参照する Azure テーブル データベース インスタンスのテーブルの名前です。 |はい。 azureTableSourceQuery を付けないで tableName を指定すると、テーブルのすべてのレコードがコピー先にコピーされます。 azureTableSourceQuery も指定した場合、クエリを満たすテーブルのレコードがコピー先にコピーされます。 |

### <a name="schema-by-data-factory"></a>Data Factory によるスキーマ
Azure Table などのスキーマのないデータ ストアの場合、Data Factory サービスは次のいずれかの方法でスキーマを推論します。

1. データセット定義で **structure** プロパティを使用してデータの構造を指定した場合、Data Factory サービスはスキーマとしてこの構造を優先します。 この場合、行に列の値が含まれていないと、null 値が指定されます。
2. データセット定義で **structure** プロパティを使用してデータの構造を指定しなかった場合、Data Factory はデータの最初の行を使用してスキーマを推論します。 この場合、最初の行に完全なスキーマが含まれていないと、コピー操作の結果で一部の列が欠落します。

したがって、スキーマのないデータ ソースでは、 **structure** プロパティを使用してデータの構造を指定するのがベスト プラクティスです。

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」という記事を参照してください。 プロパティ (名前、説明、入力データセット、出力データセット、ポリシーなど) は、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの typeProperties セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります。

**AzureTableSource** の typeProperties セクションでは次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| azureTableSourceQuery |カスタム クエリを使用してデータを読み取ります。 |Azure テーブルのクエリ文字列。 次のセクションの例を参照してください。 |No. azureTableSourceQuery を付けないで tableName を指定すると、テーブルのすべてのレコードがコピー先にコピーされます。 azureTableSourceQuery も指定した場合、クエリを満たすテーブルのレコードがコピー先にコピーされます。 |
| azureTableSourceIgnoreTableNotFound |テーブルが存在しないという例外を受け入れるかどうかを示します。 |TRUE<br/>FALSE |いいえ |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery の例
Azure テーブルの列が文字列型の場合:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Azure テーブルの列が datetime 型の場合:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** の typeProperties セクションでは次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |シンクで使用できる既定のパーティション キー値です。 |文字列の値。 |いいえ |
| azureTablePartitionKeyName |値をパーティション キーとして使用する列の名前を指定します。 指定しない場合、AzureTableDefaultPartitionKeyValue がパーティション キーとして使用されます。 |列の名前。 |いいえ |
| azureTableRowKeyName |値を行キーとして使用する列の名前を指定します。 指定しない場合、各行に GUID を使用します。 |列の名前。 |いいえ |
| azureTableInsertType |Azure テーブルにデータを挿入する方法です。<br/><br/>このプロパティは、一致するパーティションと列キーを持つ出力テーブル内の既存の行で、値を置換するか結合するかを制御します。 <br/><br/>これらの設定 (結合と置換) の機能については、「[Insert or Merge Entity (エンティティの挿入または結合)](https://msdn.microsoft.com/library/azure/hh452241.aspx)」および「[Insert or Replace Entity (エンティティの挿入または置換)](https://msdn.microsoft.com/library/azure/hh452242.aspx)」をご覧ください。 <br/><br> この設定は、テーブル レベルではなく、行レベルで適用されます。どちらのオプションでも、出力テーブル内の、入力内に存在しない行は削除されません。 |merge (既定値)<br/>replace |いいえ |
| writeBatchSize |writeBatchSize または writeBatchTimeout に達したときに、Azure テーブルにデータを挿入します。 |整数 (行数) |いいえ (既定値:10000) |
| writeBatchTimeout |writeBatchSize または writeBatchTimeout に達したときに、Azure テーブルにデータを挿入します。 |TimeSpan<br/><br/>例:"00:20:00" (20 分) |No (既定値はストレージ クライアントの既定のタイムアウト値の 90 秒) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
azureTablePartitionKeyName として宛先列を使用する前に、translator JSON プロパティを使用して、ソース列を宛先列にマップします。

次の例では、ソース列の DivisionID が宛先列の DivisionID にマップされます。  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
DivisionID は、パーティション キーとして指定されます。

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>JSON の使用例
次の例は、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 これらの例は、Azure Table Storage と Azure BLOB Database の間でデータをコピーする方法を示しています。 ただし、任意のソースのデータを、サポートされている任意のシンクに **直接** コピーできます。 詳細については、「[コピー アクティビティを使用したデータの移動](data-factory-data-movement-activities.md)」の「サポートされるデータ ストアと形式」のセクションを参照してください。

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>例:Azure テーブルから Azure BLOB にデータをコピーする
次のサンプルは以下を示しています。

1. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 型のリンクされたサービス (テーブルと BLOB の両方に使用)
2. [AzureTable](#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
4. AzureTableSource と [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは Azure テーブルのデフォルト パーティションに属するデータを 1 時間ごとに BLOB にコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure Storage のリンクされたサービス:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory では、2 種類の Azure Storage のリンクされたサービス **AzureStorage** と **AzureStorageSas** をサポートしています。 前者ではアカウント キーを含む接続文字列を指定し、後者では Shared Access Signature (SAS) の URI を指定します。 詳細については、「 [リンクされたサービス](#linked-service-properties) 」をご覧ください。  

**Azure テーブルの入力データセット:**

このサンプルでは、Azure テーブルに「MyTable」という名前のテーブルが作成されているものと想定しています。

”external” を ”true” に設定すると、データセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

```JSON
{
  "name": "AzureTableInput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure BLOB の出力データセット:**

データは新しい BLOB に 1 時間おきに書き込まれます (frequency: hour、interval: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**AzureTableSource と BlobSink を使用するパイプラインでのコピー アクティビティ:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **AzureTableSource** に設定され、**sink** 型が **BlobSink** に設定されています。 **AzureTableSourceQuery** プロパティで指定された SQL クエリにより、コピーするデータが 1 時間ごとにデフォルト パーティションから選択されます。

```JSON
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureTabletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureTableInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureTableSource",
                        "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>例:Azure BLOB から Azure テーブルにデータをコピーする
次のサンプルは以下を示しています。

1. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 型のリンクされたサービス (テーブルと BLOB の両方に使用)
2. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
3. [AzureTable](#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
4. [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) と [AzureTableSink](#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは、Azure BLOB から Azure テーブルに時系列データを 1 時間おきにコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure Storage のリンクされたサービス (Azure テーブルと BLOB の両方で使用):**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

Azure Data Factory では、2 種類の Azure Storage のリンクされたサービス **AzureStorage** と **AzureStorageSas** をサポートしています。 前者ではアカウント キーを含む接続文字列を指定し、後者では Shared Access Signature (SAS) の URI を指定します。 詳細については、「 [リンクされたサービス](#linked-service-properties) 」をご覧ください。

**Azure BLOB の入力データセット:**

データは新しい BLOB から 1 時間おきに取得されます (頻度: 時間、間隔:1)。 BLOB のフォルダー パスとファイル名は、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスでは開始時間の年、月、日の部分を使用し、ファイル名では開始時間の時刻部分を使用します。 "external": "true" の設定により、このデータセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure テーブルの出力データセット:**

このサンプルは Azure テーブルの「MyTable」というテーブルにデータをコピーします。 BLOB CSV ファイルに含める予定の数の列を持つ Azure テーブルを作成します。 新しい行は 1 時間ごとにテーブルに追加されます。

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**BlobSource と AzureTableSink を使用するパイプラインでのコピー アクティビティ:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **BlobSource** に設定され、**sink** 型が **AzureTableSink** に設定されています。

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoTable",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureTableOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "AzureTableSink",
            "writeBatchSize": 100,
            "writeBatchTimeout": "01:00:00"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
## <a name="type-mapping-for-azure-table"></a>Azure テーブルの型のマッピング
[データ移動アクティビティ](data-factory-data-movement-activities.md) に関する記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

Azure テーブル間でデータの移動時に、次の [Azure Table service により定義されたマッピング](https://msdn.microsoft.com/library/azure/dd179338.aspx)が Azure テーブルの OData 型と .NET 型の間の移動に利用されます。

| OData データ型 | .NET 型 | 詳細 |
| --- | --- | --- |
| Edm.Binary |byte[] |バイトの配列 (最大 64 KB)。 |
| Edm.Boolean |bool |ブール値。 |
| Edm.DateTime |DateTime |世界協定時刻 (UTC) を表す 64 ビット値。 サポートされている DateTime 範囲は西暦 1601 年 1 月 1 日 UTC 深夜 12:00 から 始まります。 この範囲は 9999 年 12 月 31 日に終了します。 |
| Edm.Double |double |64 ビットの浮動小数点値。 |
| Edm.Guid |Guid |グローバルで一意となる 128 ビットの識別子。 |
| Edm.Int32 |Int32 |32 ビットの整数。 |
| Edm.Int64 |Int64 |64 ビットの整数。 |
| Edm.String |string |UTF-16 エンコードの値。 文字列値は最大 64 KB になります。 |

### <a name="type-conversion-sample"></a>型変換の例
次は型変換で Azure BLOB から Azure テーブルにデータをコピーする操作のサンプルです。

BLOB のデータセットは CSV 形式で、3 つの列を含んでいるとします。 列のうち 1 つは、カスタム日付/時刻形式の datetime 型の列です。名前は週の曜日を表すフランス語の省略形を使用しています。

次のようにコピー元の BLOB データセットと列の型を定義します。

```JSON
{
    "name": " AzureBlobInput",
    "properties":
    {
        "structure":
        [
            { "name": "userid", "type": "Int64"},
            { "name": "name", "type": "String"},
            { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1,
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Azure テーブル OData 型から .NET 型への型マッピングを所与として、次のスキーマで Azure テーブルのテーブルを定義します。

**Azure テーブル スキーマ:**

| 列名 | 種類 |
| --- | --- |
| userid |Edm.Int64 |
| 名前 |Edm.String |
| lastlogindate |Edm.DateTime |

続いて、次のように Azure テーブルのデータセットを定義します。 基になるデータ ストアで既に指定されているため、“structure” セクションのタイプ情報を指定する必要はありません。

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

ここでは、データを BLOB から Azure テーブルに移動するときに、Data Factory はカスタム日付/時刻を持つ Datetime フィールドを含むタイプ変換を "fr-fr" カルチャを使用して自動的に実行します。

> [!NOTE]
> ソース データセット列からシンク データセット列へのマッピングについては、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページをご覧ください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。
