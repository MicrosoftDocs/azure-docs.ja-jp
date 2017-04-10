---
title: "DocumentDB との間でのデータの移動 | Microsoft Docs"
description: "Azure Data Factory を使用して Azure DocumentDB コレクションに、または Azure DocumentDB コレクションからデータを移動する方法を説明します。"
services: data-factory, documentdb
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: e0cd1eb986e137e1e8877286b2efe9a6924da931
ms.lasthandoff: 03/27/2017


---
# <a name="move-data-to-and-from-documentdb-using-azure-data-factory"></a>Azure Data Factory を使用した DocumentDB との間でのデータの移動
この記事では、Azure Data Factory のコピー アクティビティを使って、Azure DocumentDB との間でデータを移動する方法について説明します。 この記事は、コピー アクティビティによるデータ移動の一般的な概要について説明している、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事に基づいています。 

サポートされる任意のソース データ ストアのデータを、Azure DocumentDB にコピーしたり、Azure DocumentDB のデータを、サポートされる任意のシンク データ ストアにコピーすることができます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)に関するページの表をご覧ください。 

> [!NOTE]
> オンプレミス/Azure IaaS データ ストアと Azure DocumentDB 間でのデータのコピーは、Data Management Gateway バージョン 2.1 以降でサポートされています。

## <a name="supported-versions"></a>サポートされているバージョン
この DocumentDB コネクタでは、DocumentDB の単一パーティション コレクションとパーティション分割コレクションとの間のデータのコピーをサポートします。 [MongoDB 用 DocDB](../documentdb/documentdb-protocol-mongodb.md) はサポートされていません。

## <a name="getting-started"></a>使用の開始
さまざまなツール/API を使用して、Azure DocumentDB との間でデータを移動するコピー アクティビティを含むパイプラインを作成できます。

パイプラインを作成する最も簡単な方法は、**コピー ウィザード**を使うことです。 データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「 [チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md) 」をご覧ください。

次のツールを使ってパイプラインを作成することもできます。**Azure Portal**、**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、**.NET API**、**REST API**。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。 

ツールと API のいずれを使用する場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。 

1. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。
2. コピー操作用の入力データと出力データを表す**データセット**を作成します。 
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む**パイプライン**を作成します。 

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 (.NET API を除く) ツールまたは API を使う場合は、JSON 形式でこれらの Data Factory エンティティを定義します。  DocumentDB との間でデータをコピーするときに使用する Data Factory エンティティの JSON 定義のサンプルについては、この記事の「[JSON の使用例](#json-examples)」を参照してください。 

次のセクションでは、DocumentDB に固有の Data Factory エンティティの定義に使用される JSON プロパティについて詳しく説明します。 

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
次の表は、Azure DocumentDB のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| **プロパティ** | **説明** | **必須** |
| --- | --- | --- |
| type |type プロパティを **DocumentDB** |はい |
| connectionString |Azure DocumentDB データベースに接続するために必要な情報を指定します。 |はい |

## <a name="dataset-properties"></a>データセットのプロパティ
データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

typeProperties セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **DocumentDbCollection** 型のデータセットの typeProperties セクションには次のプロパティがあります。

| **プロパティ** | **説明** | **必須** |
| --- | --- | --- |
| collectionName |DocumentDB ドキュメント コレクションの名前です。 |はい |

例:

```JSON
{
  "name": "PersonDocumentDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "DocumentDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
### <a name="schema-by-data-factory"></a>Data Factory によるスキーマ
DocumentDB などのスキーマのないデータ ストアの場合、Data Factory サービスは次のいずれかの方法でスキーマを推論します。  

1. データセット定義で **structure** プロパティを使用してデータの構造を指定した場合、Data Factory サービスはスキーマとしてこの構造を優先します。 この場合、行に列の値が含まれていないと、null 値が指定されます。
2. データセット定義で **structure** プロパティを使用してデータの構造を指定しなかった場合、Data Factory サービスはデータの最初の行を使用してスキーマを推論します。 この場合、最初の行に完全なスキーマが含まれていないと、コピー操作の結果で一部の行が欠落します。

したがって、スキーマのないデータ ソースでは、 **structure** プロパティを使用してデータの構造を指定するのがベスト プラクティスです。

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

> [!NOTE]
> コピー アクティビティは入力を 1 つだけ受け取り、出力を 1 つだけ生成します。

一方で、アクティビティの typeProperties セクションで利用できるプロパティはアクティビティの種類により異なり、コピー アクティビティの場合、source と sink の種類によって異なります。

コピー アクティビティで、source の種類が **DocumentDbCollectionSource** である場合は、**typeProperties** セクションで次のプロパティを使用できます。

| **プロパティ** | **説明** | **使用できる値** | **必須** |
| --- | --- | --- | --- |
| query |データを読み取るためのクエリを指定します。 |DocumentDB でサポートされているクエリ文字列。 <br/><br/>例: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |なし <br/><br/>指定されていない場合に実行される SQL ステートメント: `select <columns defined in structure> from mycollection` |
| nestingSeparator |ドキュメントが入れ子であることを示す特殊文字 |任意の文字。 <br/><br/>DocumentDB は JSON 文書の NoSQL ストアであり、入れ子構造が許可されます。 Azure Data Factory を利用すると、nestingSeparator で階層を示すことができます。 上記の例では「.」です。 区切り記号により、コピー アクティビティで「Name」オブジェクトが 3 つの子要素 (First、Middle、Last) で生成されます。これはテーブル定義の「Name.First」、「Name.Middle」、「Name.Last」に基づきます。 |なし |

**DocumentDbCollectionSink** では次のプロパティがサポートされます。

| **プロパティ** | **説明** | **使用できる値** | **必須** |
| --- | --- | --- | --- |
| nestingSeparator |入れ子になった文書が必要であることを示すソース列名の特殊文字。 <br/><br/>上記の例の場合: 出力テーブルの `Name.First` は、DocumentDB ドキュメントで次の JSON 構造を生成します。<br/><br/>"Name": {<br/>    "First":"John"<br/>}, |入れ子レベルの分割に使用される文字。<br/><br/>既定値は `.` (ドット) です。 |入れ子レベルの分割に使用される文字。 <br/><br/>既定値は `.` (ドット) です。 |
| writeBatchSize |DocumentDB サービスにドキュメントの作成を要求する並列要求の数。<br/><br/>このプロパティを使用して、DocumentDB との間でコピーするときのパフォーマンスを微調整できます。 writeBatchSize を増やすとパフォーマンスが良くなります。DocumentDB に送信される並列要求の数が増えるためです。 ただし、スロットルは回避する必要があります。「Request rate is large」というエラー メッセージをスローする可能性があります。<br/><br/>スロットルは、ドキュメントのサイズ、ドキュメント内の語句の数、ターゲット コレクションの索引作成ポリシーなど、さまざまな要因により決定されます。コピー操作の場合、もっとよいコレクションを利用し (S3 など)、最大のスループットを得ることができます (毎秒 2,500 要求単位)。 |Integer |いいえ (既定値: 5) |
| writeBatchTimeout |タイムアウトする前に操作の完了を待つ時間です。 |timespan<br/><br/> 例: "00:30:00" (30 分)。 |いいえ |

## <a name="json-examples"></a>JSON の使用例
以下の例は、[Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)、または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 ここでは、Azure DocumentDB と Azure Blob Storage の間でデータをコピーする方法を示します。 ただし、Azure Data Factory のコピー アクティビティを使用して、**こちら**に記載されているいずれかのシンクに、任意のソースからデータを[直接](data-factory-data-movement-activities.md#supported-data-stores-and-formats)コピーすることができます。

## <a name="example-copy-data-from-documentdb-to-azure-blob"></a>例: Azure DocumentDB から Azure BLOB にデータをコピーする
下のサンプルで確認できる要素:

1. [DocumentDB](#linked-service-properties)型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
3. [DocumentDbCollection](#dataset-properties)型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [DocumentDbCollectionSource](#copy-activity-properties) と [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む [パイプライン](data-factory-create-pipelines.md)。

このサンプルでは、Azure DocumentDB のデータが Azure BLOB にコピーされます。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure DocumentDB のリンクされたサービス:**

```JSON
{
  "name": "DocumentDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
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
**Azure Document DB 入力データセット:**

このサンプルでは、「 **Person** 」というコレクションが Azure DocumentDB データベースにあるものと想定しています。

「“external”: ”true”」を設定して externalData ポリシーを指定すると、テーブルが Data Factory に対して外部にあり、Data Factory のアクティビティでは生成されていないことが Azure Data Factory のサービスに通知されます。

```JSON
{
  "name": "PersonDocumentDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "DocumentDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Azure BLOB の出力データセット:**

データは、時間の粒度で特定の日時を反映している BLOB のパスの新しい BLOB に 1 時間ごとにコピーされます。

```JSON
{
  "name": "PersonBlobTableOut",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
DocumentDB データベースの「Person」コレクションのサンプル JSON 文書:

```JSON
{
  "PersonId": 2,
  "Name": {
    "First": "Jane",
    "Middle": "",
    "Last": "Doe"
  }
}
```
DocumentDB では、階層的な JSON 文書に SQL タイプの構文を使用し、文書にクエリを実行できます。

例: 

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

次のパイプラインは DocumentDB データベースの「Person」コレクションから Azure BLOB にデータをコピーします。 コピー アクティビティの一部として、入力データセットと出力データセットが指定されています。  

```JSON
{
  "name": "DocDbToBlobPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "DocumentDbCollectionSource",
            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
            "nestingSeparator": "."
          },
          "sink": {
            "type": "BlobSink",
            "blobWriterAddHeader": true,
            "writeBatchSize": 1000,
            "writeBatchTimeout": "00:00:59"
          }
        },
        "inputs": [
          {
            "name": "PersonDocumentDbTable"
          }
        ],
        "outputs": [
          {
            "name": "PersonBlobTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromDocDbToBlob"
      }
    ],
    "start": "2015-04-01T00:00:00Z",
    "end": "2015-04-02T00:00:00Z"
  }
}
```
## <a name="example-copy-data-from-azure-blob-to-azure-documentdb"></a>例: Azure BLOB から Azure DocumentDB にデータをコピーする
下のサンプルで確認できる要素:

1. [DocumentDB](#azure-documentdb-linked-service-properties)型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [DocumentDbCollection](#azure-documentdb-dataset-type-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) と [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties) を使用するコピー アクティビティを含む [パイプライン](data-factory-create-pipelines.md)。

このサンプルでは、Azure BLOB から Azure DocumentDB にデータがコピーされます。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

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
**Azure DocumentDB のリンクされたサービス:**

```JSON
{
  "name": "DocumentDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Azure BLOB の入力データセット:**

```JSON
{
  "name": "PersonBlobTableIn",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "MiddleName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "fileName": "input.csv",
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
**Azure DocumentDB 出力データセット:**

このサンプルでは、「Person」という名前のコレクションにデータをコピーします。

```JSON
{
  "name": "PersonDocumentDbTableOut",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "Name.First",
        "type": "String"
      },
      {
        "name": "Name.Middle",
        "type": "String"
      },
      {
        "name": "Name.Last",
        "type": "String"
      }
    ],
    "type": "DocumentDbCollection",
    "linkedServiceName": "DocumentDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
次のパイプラインは Azure BLOB から DocumentDB データベースの「Person」コレクションにデータをコピーします。 コピー アクティビティの一部として、入力データセットと出力データセットが指定されています。

```JSON
{
  "name": "BlobToDocDbPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "DocumentDbCollectionSink",
            "nestingSeparator": ".",
            "writeBatchSize": 2,
            "writeBatchTimeout": "00:00:00"
          }
          "translator": {
              "type": "TabularTranslator",
              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
          }
        },
        "inputs": [
          {
            "name": "PersonBlobTableIn"
          }
        ],
        "outputs": [
          {
            "name": "PersonDocumentDbTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromBlobToDocDb"
      }
    ],
    "start": "2015-04-14T00:00:00Z",
    "end": "2015-04-15T00:00:00Z"
  }
}
```
サンプル BLOB 入力が次の場合

```
1,John,,Doe
```
DocumentDB の JSON は次のようになります。

```JSON
{
  "Id": 1,
  "Name": {
    "First": "John",
    "Middle": null,
    "Last": "Doe"
  },
  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
}
```
DocumentDB は JSON 文書の NoSQL ストアであり、入れ子構造が許可されます。 Azure Data Factory を利用すると、**nestingSeparator** で階層を示すことができます。 この例では「.」です。 区切り記号により、コピー アクティビティで「Name」オブジェクトが 3 つの子要素 (First、Middle、Last) で生成されます。これはテーブル定義の「Name.First」、「Name.Middle」、「Name.Last」に基づきます。

## <a name="importexport-json-documents"></a>JSON ドキュメントのインポート/エクスポート
この DocumentDB コネクタを使用して、次の作業を簡単に実行できます。

* Azure BLOB、Azure Data Lake、オンプレミスのファイル システム、Azure Data Factory でサポートされているその他のファイル ベースのストアなど、さまざまなソースから DocumentDB に JSON ドキュメントをインポートする。
* JSON ドキュメントを DocumentDB コレクションからさまざまなファイル ベースのストアにエクスポートする。
* 2 つの DocumentDB コレクション間でそのままデータを移行する。

スキーマに依存しないこのようなコピーを実現するには、コピー アクティビティで、入力データセットの "structure" セクションまたは DocumentDB ソース/シンクの "nestingSeparator" プロパティを指定しないでください。 JSON 形式の構成の詳細については、対応するファイル ベースのコネクタのトピックで、"形式の指定" に関するセクションを参照してください。

## <a name="appendix"></a>付録
1. **質問:**
    コピー アクティビティは、既存のレコードの更新をサポートしていますか?

    **回答:**
    いいえ。
2. **質問:**
    DocumentDB へのコピーを再試行すると、既にコピーしたレコードはどのように扱われますか?

    **回答:**
    レコードに "ID" フィールドがあり、コピー操作で同じ ID のレコードが挿入される場合、そのコピー操作はエラーをスローします。  
3. **質問:**
    Data Factory は、[範囲またはハッシュ ベースのデータのパーティション分割](https://azure.microsoft.com/documentation/articles/documentdb-partition-data/)をサポートしていますか?

    **回答:**
    いいえ。
4. **質問:**
    1 つのテーブルに複数の DocumentDB コレクションを指定できますか?

    **回答:**
    いいえ。 現時点では、1 つのコレクションだけを指定できます。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。

