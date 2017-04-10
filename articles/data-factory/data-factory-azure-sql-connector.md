---
title: "Azure SQL Database との間でのデータの移動 | Microsoft Docs"
description: "Azure Data Factory を使用して Azure SQL Database に、または Azure SQL Database からデータを移動する方法を説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: beec132956ed4d382517379a47da57db983e7b54
ms.lasthandoff: 03/29/2017


---
# <a name="move-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Azure Data Factory を使用した Azure SQL Database との間でのデータの移動
この記事では、Azure Data Factory のコピー アクティビティを使って、Azure SQL Database との間でデータを移動する方法について説明します。 この記事は、コピー アクティビティによるデータ移動の一般的な概要について説明している、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事に基づいています。  

サポートされる任意のソース データ ストアのデータを、Azure SQL Database にコピーしたり、Azure SQL Database のデータを、サポートされる任意のシンク データ ストアにコピーしたりできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)の表をご覧ください。

## <a name="getting-started"></a>使用の開始
さまざまなツール/API を使用して、Azure SQL Database との間でデータを移動するコピー アクティビティを含むパイプラインを作成できます。

パイプラインを作成する最も簡単な方法は、**コピー ウィザード**を使うことです。 データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「 [チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md) 」をご覧ください。

**Azure Portal**、**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、**.NET API**、**REST API** などのツールを使ってパイプラインを作成することもできます。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。 

ツールまたは API のいずれを使用した場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。 

1. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。
2. コピー操作用の入力データと出力データを表す**データセット**を作成します。 
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む**パイプライン**を作成します。 

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 ツール/API (.NET API を除く) を使う場合、こうした Data Factory エンティティは、JSON 形式で定義します。  Azure SQL Database との間でデータをコピーするときに使用する Data Factory エンティティの JSON 定義のサンプルについては、この記事の「[JSON の使用例](#json-examples)」を参照してください。 

以下のセクションでは、Azure SQL Database に固有の Data Factory エンティティの定義に使用される JSON プロパティについて詳しく説明します。 

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
Azure SQL のリンクされたサービスは、Azure SQL データベースをデータ ファクトリにリンクします。 次の表は、Azure SQL のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティを **AzureSqlDatabase** |はい |
| connectionString |connectionString プロパティの Azure SQL Database インスタンスに接続するために必要な情報を指定します。 |はい |

> [!IMPORTANT]
> [サーバーへのアクセスを Azure サービスに許可する](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)ように [Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) データベース サーバーを構成します。 また、Azure の外部から (たとえば、Data Factory ゲートウェイのあるオンプレミスのデータ ソースから) Azure SQL Database にデータをコピーする場合、Azure SQL Database にデータを送信するマシンに適切な IP アドレス範囲を設定します。

## <a name="dataset-properties"></a>データセットのプロパティ
データセットを指定して Azure SQL Database の入力データまたは出力データを表すには、そのデータセットの type プロパティを **AzureSqlTable** に設定します。 また、データセットの **linkedServiceName** プロパティは、Azure SQL のリンクされたサービスの名前に設定します。  

データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

typeProperties セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **AzureSqlTable** 型のデータセットの **typeProperties** セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |リンクされたサービスが参照する Azure SQL Database インスタンスのテーブルまたはビューの名前です。 |はい |

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

> [!NOTE]
> コピー アクティビティは入力を 1 つだけ受け取り、出力を 1 つだけ生成します。

一方、アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります。

Azure SQL データベースからデータを移動する場合は、コピー アクティビティのソースの種類を **SqlSource**に設定します。 同様に、Azure SQL データベースにデータを移動する場合は、コピー アクティビティのシンクの種類を **SqlSink**に設定します。 このセクションでは、SqlSource と SqlSink でサポートされるプロパティの一覧を示します。

### <a name="sqlsource"></a>SqlSource
コピー アクティビティで、source の種類が **SqlSource** である場合は、**typeProperties** セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| SqlReaderQuery |カスタム クエリを使用してデータを読み取ります。 |SQL クエリ文字列。 例: `select * from MyTable`. |なし |
| sqlReaderStoredProcedureName |ソース テーブルからデータを読み取るストアド プロシージャの名前。 |ストアド プロシージャの名前。 |いいえ |
| storedProcedureParameters |ストアド プロシージャのパラメーター。 |名前と値のペア。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |なし |

SqlSource に **sqlReaderQuery** が指定されている場合、コピー アクティビティでは、データを取得するために Azure SQL Database ソースに対してこのクエリを実行します。 または、**sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することができます (ストアド プロシージャでパラメーターを使用する場合)。

sqlReaderQuery や sqlReaderStoredProcedureName を指定しない場合は、Azure SQL Database に対して実行するクエリを作成する (`select column1, column2 from mytable`) ために、データセット JSON の構造セクションで定義された列が使用されます。 データセット定義に構造がない場合は、すべての列がテーブルから選択されます。

> [!NOTE]
> **sqlReaderStoredProcedureName** を使用する場合でも、データセット JSON の **tableName** プロパティの値を指定する必要があります。 ただし、このテーブルに対して実行される検証はありません。
>
>

### <a name="sqlsource-example"></a>SqlSource の例

```JSON
"source": {
    "type": "SqlSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**ストアド プロシージャの定義:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqlsink"></a>パイプライン
**SqlSink** では次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| writeBatchTimeout |タイムアウトする前に一括挿入操作の完了を待つ時間です。 |timespan<br/><br/> 例: "00:30:00" (30 分)。 |いいえ |
| writeBatchSize |バッファー サイズが writeBatchSize に達したときに SQL テーブルにデータを挿入します。 |整数 (行数) |いいえ (既定値: 10000) |
| sqlWriterCleanupScript |特定のスライスのデータを消去するコピー アクティビティのクエリを指定します。 詳細については、 [再現性に関するセクション](#repeatability-during-copy)をご覧ください。 |クエリ ステートメント。 |いいえ |
| sliceIdentifierColumnName |自動生成スライス ID を入力するためのコピー アクティビティの列名を指定します。再実行時、特定のスライスのデータを消去するときに使用されます。 詳細については、 [再現性に関するセクション](#repeatability-during-copy)をご覧ください。 |バイナリ (32) のデータ型の列の列名。 |いいえ |
| sqlWriterStoredProcedureName |対象テーブルにデータをアップサート (更新/挿入) するストアド プロシージャの名前。 |ストアド プロシージャの名前。 |いいえ |
| storedProcedureParameters |ストアド プロシージャのパラメーター。 |名前と値のペア。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ |
| sqlWriterTableType |ストアド プロシージャで使用するテーブル型の名前を指定します。 コピー アクティビティでは、このテーブル型の一時テーブルでデータを移動できます。 その後、ストアド プロシージャのコードにより、コピーされたデータを既存のデータと結合できます。 |テーブルの種類の名前。 |なし |

#### <a name="sqlsink-example"></a>SqlSink の例

```JSON
"sink": {
    "type": "SqlSink",
    "writeBatchSize": 1000000,
    "writeBatchTimeout": "00:05:00",
    "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
    "sqlWriterTableType": "CopyTestTableType",
    "storedProcedureParameters": {
        "identifier": { "value": "1", "type": "Int" },
        "stringData": { "value": "str1" },
        "decimalData": { "value": "1", "type": "Decimal" }
    }
}
```

## <a name="json-examples"></a>JSON の使用例
以下の例は、[Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)、または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 これらの例は、Azure SQL Database と Azure BLOB ストレージの間でデータをコピーする方法を示しています。 ただし、Azure Data Factory のコピー アクティビティを使用して、 **こちら** に記載されているいずれかのシンクに、任意のソースからデータを [直接](data-factory-data-movement-activities.md#supported-data-stores-and-formats) コピーすることができます。

## <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>例: Azure SQL Database から Azure BLOB へのデータのコピー
このサンプルでは、次の Data Factory のエンティティを定義します。

1. [AzureSqlDatabase](#linked-service-properties)型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
3. [AzureSqlTable](#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [Azure BLOB](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [SqlSource](#copy-activity-properties) と [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは、Azure SQL Database のテーブルから BLOB に時系列データ (時間単位、日単位など) を 1 時間おきにコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。  

**Azure SQL Database のリンクされたサービス:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
このリンクされたサービスでサポートされているプロパティの一覧については、「 [Azure SQL のリンクされたサービス](#linked-service) 」を参照してください。

**Azure BLOB ストレージのリンクされたサービス:**

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
このリンクされたサービスでサポートされているプロパティの一覧については、[Azure BLOB](data-factory-azure-blob-connector.md#azure-storage-linked-service) に関する記事を参照してください。


**Azure SQL の入力データセット:**

このサンプルでは、Azure SQL で「MyTable」という名前のテーブルを作成し、時系列データ用に「timestampcolumn」という名前の列が含まれているものと想定しています。

"external" を "true" に設定すると、データセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Azure Data Factory サービスに通知されます。

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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

このデータセット型でサポートされているプロパティの一覧については、「 [Azure SQL データセットの type プロパティ](#dataset) 」を参照してください。  

**Azure BLOB の出力データセット:**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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
このデータセット型でサポートされているプロパティの一覧については、「 [Azure BLOB データセットの type プロパティ](data-factory-azure-blob-connector.md#dataset-properties) 」を参照してください。  

**SQL ソースおよび BLOB シンクを使用するパイプラインでのコピー アクティビティ:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **SqlSource** に設定され、**sink** 型が **BlobSink** に設定されています。 **SqlReaderQuery** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "AzureSQLtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
この例では、SqlSource に **sqlReaderQuery** が指定されています。 コピー アクティビティでは、データを取得するために Azure SQL Database ソースに対してこのクエリを実行します。 または、**sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することができます (ストアド プロシージャでパラメーターを使用する場合)。

sqlReaderQuery や sqlReaderStoredProcedureName を指定しない場合は、Azure SQL Database に対して実行するクエリを作成するために、データセット JSON の構造セクションで定義された列が使用されます。 (例: `select column1, column2 from mytable`)。 データセット定義に構造がない場合は、すべての列がテーブルから選択されます。

SqlSource と BlobSink でサポートされるプロパティの一覧については、「[SqlSource](#sqlsource)」および [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) に関する記述を参照してください。

## <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>例: Azure BLOB から Azure SQL Database へのデータのコピー
このサンプルでは、次の Data Factory のエンティティを定義します。  

1. [AzureSqlDatabase](#linked-service-properties)型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureSqlTable](#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) と [SqlSink](#copy-activity-properties) を使用するコピー アクティビティの[パイプライン](data-factory-create-pipelines.md)。

このサンプルは、Azure SQL Database のテーブルから Azure BLOB に時系列データ (時間単位、日単位など) を 1 時間おきにコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure SQL のリンクされたサービス:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
このリンクされたサービスでサポートされているプロパティの一覧については、「 [Azure SQL のリンクされたサービス](#linked-service) 」を参照してください。

**Azure BLOB ストレージのリンクされたサービス:**

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
このリンクされたサービスでサポートされているプロパティの一覧については、[Azure BLOB](data-factory-azure-blob-connector.md#azure-storage-linked-service) に関する記事を参照してください。


**Azure BLOB の入力データセット:**

データは新しい BLOB から 1 時間おきに取得されます (頻度: 時間、間隔: 1)。 BLOB のフォルダー パスとファイル名は、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日の部分を利用し、ファイル名は開始時間の時刻部分を使用します。 "external": "true" 設定は Data Factory サービスにこのテーブルが Data Factory の外部にあり、Data Factory のアクティビティでは生成されないことを通知します。

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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
このデータセット型でサポートされているプロパティの一覧については、「 [Azure BLOB データセットの type プロパティ](data-factory-azure-blob-connector.md#dataset-properties) 」を参照してください。

**Azure SQL Database の出力データセット:**

このサンプルは Azure SQL の「MyTable」というテーブルにデータをコピーします。 BLOB CSV ファイルに含めることが予想される列の数と同じ列数で Azure SQL にテーブルを作成します。 新しい行は 1 時間ごとにテーブルに追加されます。

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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
このデータセット型でサポートされているプロパティの一覧については、「 [Azure SQL データセットの type プロパティ](#dataset) 」を参照してください。

**BLOB ソースおよび SQL シンクを使用するパイプラインでのコピー アクティビティ:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプラインの JSON 定義で、**source** 型が **BlobSource** に設定され、**sink** 型が **SqlSink** に設定されています。

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlSink"
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
SqlSink と BlobSource でサポートされるプロパティの一覧については、「[Sql Sink](#sqlsink)」および [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) に関する記述を参照してください。

## <a name="identity-columns-in-the-target-database"></a>ターゲット データベースの ID 列
このセクションでは、ID 列がないソース テーブルから ID 列がある対象テーブルにデータをコピーする例を示します。

**ソース テーブル:**

```SQL
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```
**対象テーブル:**

```SQL
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```
ターゲット テーブルには ID 列があることに注意してください。

**ソース データセット JSON の定義**

```JSON
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**対象データセット JSON の定義**

```JSON
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }    
}
```

ソースとターゲット テーブルには異なるスキーマがあることに注意してください (ターゲットには ID を持つ追加の列があります)。 このシナリオでは、ターゲット データセット定義で **structure** プロパティを指定する必要があります。ここでは、ID 列は含みません。

## <a name="map-source-to-sink-columns"></a>ソース列からシンク列へのマップ
ソース データセット列からシンク データセット列へのマッピングの詳細については、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページをご覧ください。

## <a name="repeatable-copy"></a>反復可能なコピー
SQL Server Database にデータをコピーすると、既定では、コピー アクティビティによりデータがシンク テーブルに付加されます。 代わりに UPSERT を実行するには、「[SqlSink への反復可能な書き込み](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink)」を参照してください。 

リレーショナル データ ストアからデータをコピーする場合は、意図しない結果を避けるため、再現性に注意する必要があります。 Azure Data Factory では、スライスを手動で再実行できます。 障害が発生したときにスライスを再実行できるように、データセットの再試行ポリシーを構成することもできます。 いずれかの方法でスライスが再実行された際は、何度スライスが実行されても同じデータが読み取られることを確認する必要があります。 「[リレーショナル ソースからの反復可能な読み取り](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)」を参照してください。

## <a name="invoke-stored-procedure-from-sql-sink"></a>SQL シンクからのストアド プロシージャの呼び出し
パイプラインのコピー アクティビティで SQL シンクからストアド プロシージャを呼び出す例については、[コピー アクティビティでの SQL シンクのストアド プロシージャの呼び出し](data-factory-invoke-stored-procedure-from-copy-activity.md)に関する記事をご覧ください。 

## <a name="sql-database-to-net-type-mapping"></a>SQL Database と .NET の型のマッピング
[データ移動アクティビティ](data-factory-data-movement-activities.md) に関する記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

Azure SQL との間でデータを移動するとき、SQL 型から .NET 型へのマッピング (およびその逆) に次のマッピングが使用されます。 マッピングは ADO.NET の SQL Server データ型マッピングと同じです。

| SQL Server Databases エンジンの型 | .NET Framework 型 |
| --- | --- |
| bigint |Int64 |
| バイナリ |Byte[] |
| ビット |Boolean |
| char |String、Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| Decimal |Decimal |
| FILESTREAM 属性 (varbinary(max)) |Byte[] |
| Float |Double |
| イメージ |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String、Char[] |
| ntext |String、Char[] |
| numeric |Decimal |
| nvarchar |String、Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object * |
| テキスト |String、Char[] |
| time |timespan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String、Char[] |
| xml |xml |

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。

