---
title: Azure Cosmos DB との間でデータを移動する
description: Azure Data Factory を使って Azure Cosmos DB コレクションに、または Azure Cosmos DB コレクションからデータを移動する方法を説明します
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a638184d5232de916ebd25360147301a93309dd9
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930082"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Azure Data Factory を使用した Azure Cosmos DB との間でのデータの移動
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](data-factory-azure-documentdb-connector.md)
> * [バージョン 2 (最新バージョン)](../connector-azure-cosmos-db.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[V2 の Azure Cosmos DB コネクタ](../connector-azure-cosmos-db.md)に関するページを参照してください。

この記事では、Azure Data Factory のコピー アクティビティを使って、Azure Cosmos DB (SQL API) との間でデータを移動する方法について説明します。 この記事は、コピー アクティビティによるデータ移動の一般的な概要について説明している、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事に基づいています。

サポートされる任意のソース データ ストアのデータを、Azure Cosmos DB にコピーしたり、Azure Cosmos DB のデータを、サポートされる任意のシンク データ ストアにコピーすることができます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)に関するページの表をご覧ください。

> [!IMPORTANT]
> Azure Cosmos DB コネクタは SQL API のみをサポートします。

JSON ファイルまたは他の Cosmos DB コレクションとの間でデータをそのままコピーするには、「[JSON ドキュメントのインポート/エクスポート](#importexport-json-documents)」を参照してください。

## <a name="getting-started"></a>使用の開始
さまざまなツール/API を使って、Azure Cosmos DB との間でデータを移動するコピー アクティビティを含むパイプラインを作成できます。

パイプラインを作成する最も簡単な方法は、**コピー ウィザード**を使うことです。 手順については、「[チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」を参照してください。データのコピー ウィザードを使用してパイプラインを作成する簡単なチュートリアルです。

また、次のツールを使用してパイプラインを作成することもできます。**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、 **.NET API**、**REST API**。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。

ツールと API のいずれを使用する場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。

1. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。
2. コピー操作用の入力データと出力データを表す**データセット**を作成します。
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む**パイプライン**を作成します。

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 (.NET API を除く) ツールまたは API を使う場合は、JSON 形式でこれらの Data Factory エンティティを定義します。 Cosmos DB との間でデータをコピーするときに使う Data Factory エンティティの JSON 定義のサンプルについては、この記事の「[JSON の使用例](#json-examples)」を参照してください。

次のセクションでは、Cosmos DB に固有の Data Factory エンティティの定義に使われる JSON プロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
次の表は、Azure Cosmos DB のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| **プロパティ** | **説明** | **必須** |
| --- | --- | --- |
| type |type プロパティは、次のように設定する必要があります:**DocumentDb** |はい |
| connectionString |Azure Cosmos DB データベースに接続するために必要な情報を指定します。 |はい |

例:

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ
データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

typeProperties セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **DocumentDbCollection** 型のデータセットの typeProperties セクションには次のプロパティがあります。

| **プロパティ** | **説明** | **必須** |
| --- | --- | --- |
| collectionName |Cosmos DB ドキュメント コレクションの名前です。 |はい |

例:

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
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
Azure Cosmos DB などのスキーマのないデータ ストアの場合、Data Factory サービスは次のいずれかの方法でスキーマを推論します。

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
| query |データを読み取るためのクエリを指定します。 |Azure Cosmos DB でサポートされているクエリ文字列。 <br/><br/>例: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |いいえ <br/><br/>指定されていない場合に実行される SQL ステートメント: `select <columns defined in structure> from mycollection` |
| nestingSeparator |ドキュメントが入れ子であることを示す特殊文字 |任意の文字。 <br/><br/>Azure Cosmos DB は JSON ドキュメントの NoSQL ストアであり、入れ子構造が許可されます。 Azure Data Factory を利用すると、nestingSeparator で階層を示すことができます。 上記の例では「.」です。 区切り記号により、コピー アクティビティで「Name」オブジェクトが 3 つの子要素 (First、Middle、Last) で生成されます。これはテーブル定義の「Name.First」、「Name.Middle」、「Name.Last」に基づきます。 |いいえ |

**DocumentDbCollectionSink** では次のプロパティがサポートされます。

| **プロパティ** | **説明** | **使用できる値** | **必須** |
| --- | --- | --- | --- |
| nestingSeparator |入れ子になった文書が必要であることを示すソース列名の特殊文字。 <br/><br/>上記の例の場合: 出力テーブルの `Name.First` は、Cosmos DB ドキュメントで次の JSON 構造を生成します。<br/><br/>"Name": {<br/>    "First":"John"<br/>}, |入れ子レベルの分割に使用される文字。<br/><br/>既定値は `.` (ドット) です。 |入れ子レベルの分割に使用される文字。 <br/><br/>既定値は `.` (ドット) です。 |
| writeBatchSize |Azure Cosmos DB サービスにドキュメントの作成を要求する並列要求の数。<br/><br/>このプロパティを使って、Cosmos DB との間でデータをコピーするときのパフォーマンスを微調整できます。 writeBatchSize を増やすと、Cosmos DB に送信される並列要求の数が増えるため、パフォーマンスを向上させることができます。 ただし、"要求レートが大きい" というエラー メッセージをスローする可能性のあるスロットルを回避する必要があります。<br/><br/>スロットルは、ドキュメントのサイズ、ドキュメント内の語句の数、ターゲット コレクションの索引作成ポリシーなど、さまざまな要因により決定されます。コピー操作の場合、もっとよいコレクションを利用し (S3 など)、最大のスループットを得ることができます (毎秒 2,500 要求単位)。 |整数 |いいえ (既定値:5) |
| writeBatchTimeout |タイムアウトする前に操作の完了を待つ時間です。 |TimeSpan<br/><br/> 例:"00:30:00" (30 分)。 |いいえ |

## <a name="importexport-json-documents"></a>JSON ドキュメントのインポート/エクスポート
この Cosmos DB コネクタを使って、次の作業を簡単に実行できます。

* Azure BLOB、Azure Data Lake、オンプレミスのファイル システム、Azure Data Factory でサポートされているその他のファイル ベースのストアなど、さまざまなソースから Cosmos DB に JSON ドキュメントをインポートする。
* JSON ドキュメントを Cosmos DB コレクションからさまざまなファイルベースのストアにエクスポートする。
* 2 つの Cosmos DB コレクション間でそのままデータを移行する。

このようなスキーマに依存しないコピーを実現するには、次のようにします。
* コピー ウィザードを使う場合は、 **[Export as-is to JSON files or Cosmos DB collection]\(JSON ファイルまたは Cosmos DB コレクションにそのままエクスポートする\)** オプションをオンにします。
* JSON の編集を使う場合は、コピー アクティビティで、Cosmos DB データセットの "structure" セクションも Cosmos DB ソース/シンクの "nestingSeparator" プロパティも指定しないでください。 JSON ファイルに対してインポート/エクスポートを行うには、ファイル ストア データセットで形式を "JsonFormat" に指定し、"filePattern" を構成し、その他の形式設定は省略します。詳細については、「[JSON 形式](data-factory-supported-file-and-compression-formats.md#json-format)」のセクションを参照してください。

## <a name="json-examples"></a>JSON の使用例
次の例は、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 ここでは、Azure Cosmos DB と Azure Blob Storage の間でデータをコピーする方法を示します。 ただし、Azure Data Factory のコピー アクティビティを使用して、**こちら**に記載されているいずれかのシンクに、任意のソースからデータを[直接](data-factory-data-movement-activities.md#supported-data-stores-and-formats)コピーすることができます。

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>例:Azure Cosmos DB から Azure BLOB にデータをコピーする
下のサンプルで確認できる要素:

1. [DocumentDB](#linked-service-properties)型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
3. [DocumentDbCollection](#dataset-properties)型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [DocumentDbCollectionSource](#copy-activity-properties) と [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む [パイプライン](data-factory-create-pipelines.md)。

この例は、Azure Cosmos DB のデータを Azure BLOB にコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure Cosmos DB のリンクされたサービス:**

```JSON
{
  "name": "CosmosDbLinkedService",
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

このサンプルでは、**Person** という名前のコレクションが Azure Cosmos DB データベースにあるものと想定しています。

「“external”: ”true”」を設定して externalData ポリシーを指定すると、テーブルが Data Factory に対して外部にあり、Data Factory のアクティビティでは生成されていないことが Azure Data Factory のサービスに通知されます。

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
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
Cosmos DB データベースの Person コレクションのサンプル JSON ドキュメント:

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
Cosmos DB では、階層的な JSON ドキュメントに SQL タイプの構文を使って、ドキュメントにクエリを実行できます。

例:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

次のパイプラインは、Azure Cosmos DB データベースの Person コレクションから Azure BLOB にデータをコピーします。 コピー アクティビティの一部として、入力データセットと出力データセットが指定されています。

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
            "name": "PersonCosmosDbTable"
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
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>例:Azure BLOB から Azure Cosmos DB にデータをコピーする
下のサンプルで確認できる要素:

1. DocumentDb 型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. DocumentDbCollection 型の出力[データセット](data-factory-create-datasets.md)。
5. [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) と DocumentDbCollectionSink を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは、Azure BLOB から Azure Cosmos DB にデータをコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

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
**Azure Cosmos DB のリンクされたサービス:**

```JSON
{
  "name": "CosmosDbLinkedService",
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
**Azure Cosmos DB の出力データセット:**

このサンプルでは、「Person」という名前のコレクションにデータをコピーします。

```JSON
{
  "name": "PersonCosmosDbTableOut",
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
    "linkedServiceName": "CosmosDbLinkedService",
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
次のパイプラインは、Azure BLOB から Cosmos DB データベースの Person コレクションにデータをコピーします。 コピー アクティビティの一部として、入力データセットと出力データセットが指定されています。

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
          },
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
            "name": "PersonCosmosDbTableOut"
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
Cosmos DB の出力 JSON は次のようになります。

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
Azure Cosmos DB は JSON ドキュメントの NoSQL ストアであり、入れ子構造が許可されます。 Azure Data Factory を利用すると、**nestingSeparator** で階層を示すことができます。 この例では「.」です。 区切り記号により、コピー アクティビティで「Name」オブジェクトが 3 つの子要素 (First、Middle、Last) で生成されます。これはテーブル定義の「Name.First」、「Name.Middle」、「Name.Last」に基づきます。

## <a name="appendix"></a>付録
1. **質問:** コピー アクティビティは、既存のレコードの更新をサポートしていますか?

    **回答:** No.
2. **質問:** Azure Cosmos DB へのコピーを再試行すると、既にコピーしたレコードはどのように扱われますか?

    **回答:** レコードに "ID" フィールドがあり、コピー操作で同じ ID のレコードが挿入される場合、そのコピー操作はエラーをスローします。
3. **質問:** Data Factory では、[範囲またはハッシュ ベースのデータのパーティション分割](../../cosmos-db/sql-api-partition-data.md)はサポートされていますか?

    **回答:** No.
4. **質問:** 1 つのテーブルに複数の Azure Cosmos DB コレクションを指定できますか?

    **回答:** No. 現時点では、1 つのコレクションだけを指定できます。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。
