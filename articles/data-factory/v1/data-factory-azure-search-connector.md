---
title: Data Factory を使用して検索インデックスにデータをプッシュする
description: Azure Data Factory を使用して Azure Cognitive Search インデックスにデータをプッシュする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5b1170f721cf8521cfe1762df0cc616c938ddf28
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929977"
---
# <a name="push-data-to-an-azure-cognitive-search-index-by-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Cognitive Search インデックスにデータをプッシュする
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](data-factory-azure-search-connector.md)
> * [バージョン 2 (最新バージョン)](../connector-azure-search.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[V2 の Azure Cognitive Search コネクタ](../connector-azure-search.md)に関するページを参照してください。

この記事では、コピー アクティビティを使用して、サポートされているソース データ ストアから Azure Cognitive Search インデックスにデータをプッシュする方法について説明します。 サポートされているソース データ ストアについては、[サポートされているソースとシンク](data-factory-data-movement-activities.md#supported-data-stores-and-formats)の表のソースの列を参照してください。 この記事は、「 [データ移動アクティビティ](data-factory-data-movement-activities.md) 」という記事に基づき、コピー アクティビティによるデータ移動の一般概要とサポートされるデータ ストアの組み合わせについて紹介しています。

## <a name="enabling-connectivity"></a>接続を有効にする
Data Factory サービスからオンプレミスのデータ ストアに接続できるようにするには、オンプレミスの環境に Data Management Gateway をインストールします。 データ ストアをホストするコンピューターと同じコンピューター、またはデータ ストアとのリソースの競合を避けるために別のコンピューター上にゲートウェイをインストールできます。

Data Management Gateway では、安全かつ管理された方法でオンプレミスのデータがクラウド サービスに接続されます。 Data Management Gateway の詳細については、 [オンプレミスとクラウド間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md) に関する記事を参照してください。

## <a name="getting-started"></a>使用の開始
さまざまなツールや API を使用して、ソース データ ストアから検索インデックスにデータをプッシュするコピー アクティビティを含むパイプラインを作成できます。

パイプラインを作成する最も簡単な方法は、**コピー ウィザード**を使うことです。 手順については、「[チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」を参照してください。データのコピー ウィザードを使用してパイプラインを作成する簡単なチュートリアルです。

また、次のツールを使用してパイプラインを作成することもできます。**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、 **.NET API**、**REST API**。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。

ツールと API のいずれを使用する場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。

1. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。
2. コピー操作用の入力データと出力データを表す**データセット**を作成します。
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む**パイプライン**を作成します。

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 (.NET API を除く) ツールまたは API を使う場合は、JSON 形式でこれらの Data Factory エンティティを定義します。  検索インデックスにデータをコピーするために使用される Data Factory エンティティに関する JSON 定義のサンプルについては、この記事の「[JSON 例: オンプレミスの SQL Server から Azure Cognitive Search インデックスにデータをコピーする](#json-example-copy-data-from-on-premises-sql-server-to-azure-cognitive-search-index)」のセクションを参照してください。

以降のセクションでは、検索インデックスに固有の Data Factory エンティティを定義するために使用される JSON プロパティに関する詳細について説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

次の表では、Azure Cognitive Search のリンクされたサービスに固有の JSON 要素について説明しています。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| type | type プロパティは、次のように設定する必要があります:**AzureSearch**。 | はい |
| url | 検索サービスの URL。 | はい |
| key | 検索サービスの管理者キー。 | はい |

## <a name="dataset-properties"></a>データセットのプロパティ

データセットの定義に使用できるセクションとプロパティの完全な一覧については、 [データセットの作成](data-factory-create-datasets.md) に関する記事をご覧ください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型でほぼ同じです。 **typeProperties** セクションは、データセットの型ごとに異なります。 **AzureSearchIndex** 型のデータセットの typeProperties セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| type | type プロパティを **AzureSearchIndex** に設定する必要があります。| はい |
| indexName | 検索インデックスの名前。 Data Factory では、インデックスは作成されません。 このインデックスは Azure Cognitive Search に存在する必要があります。 | はい |


## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に使用できるセクションとプロパティの完全な一覧については、 [パイプラインの作成](data-factory-create-pipelines.md) に関する記事をご覧ください。 名前、説明、入力テーブル、出力テーブル、さまざまなポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。 一方、typeProperties セクションで使用できるプロパティは各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります。

コピー アクティビティで、シンクの種類が **AzureSearchIndexSink** である場合は、typeProperties セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | ドキュメントがそのインデックスに既に存在する場合に、マージするか置換するかを指定します。 詳細については、「[WriteBehavior プロパティ](#writebehavior-property)」を参照してください。| マージ (既定値)<br/>アップロード| いいえ |
| WriteBatchSize | バッファー サイズが writeBatchSize に達すると、検索インデックスにデータをアップロードします。 詳細については、「[WriteBatchSize プロパティ](#writebatchsize-property)」を参照してください。 | 1 ～ 1,000。 既定値は 1,000 です。 | いいえ |

### <a name="writebehavior-property"></a>WriteBehavior プロパティ
データを書き込むときに AzureSearchSink で upsert されます。 つまり、ドキュメントを書き込むとき、そのドキュメントのキーが検索インデックスに既に存在する場合、Azure Cognitive Search は競合の例外をスローするのではなく、既存のドキュメントを更新します。

AzureSearchSink で提供される upsert 動作 (AzureSearch SDK の使用による) は、次の 2 とおりあります。

- **マージ**: 新しいドキュメントのすべての列を既存の列と結合します。 新しいドキュメント内に null 値を持つ列がある場合は、既存の列の値が保持されます。
- **アップロード**: 既存のドキュメントが新しいドキュメントで置き換えられます。 新しいドキュメントで指定されていない列の場合は、既存のドキュメントに null 以外の値があるかどうかに関係なく、値は null に設定されます。

既定の動作は**マージ**です。

### <a name="writebatchsize-property"></a>WriteBatchSize プロパティ
Azure Cognitive Search サービスは、バッチとしてのドキュメントの書き込みをサポートしています。 バッチには、1 ～ 1,000 のアクションを含めることができます。 1 つのアクションで、1 つのドキュメントのアップロード/マージ操作の実行を処理します。

### <a name="data-type-support"></a>データ型のサポート
次の表は、Azure Cognitive Search のデータ型がサポートされているかどうかを示しています。

| Azure Cognitive Search のデータ型 | Azure Cognitive Search のシンクでのサポート |
| ---------------------- | ------------------------------ |
| string | Y |
| Int32 | Y |
| Int64 | Y |
| Double | Y |
| Boolean | Y |
| DataTimeOffset | Y |
| String Array | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-cognitive-search-index"></a>JSON の使用例:オンプレミスの SQL Server から Azure Cognitive Search インデックスにデータをコピーする

次のサンプルは以下を示しています。

1. [AzureSearch](#linked-service-properties) 型のリンクされたサービス。
2. [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties)型のリンクされたサービス。
3. [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureSearchIndex](#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
4. [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) と [AzureSearchIndexSink](#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは、オンプレミスの SQL Server データベースから検索インデックスに時系列データを 1 時間ごとにコピーします。 このサンプルで使用される JSON プロパティの説明は、サンプルに続くセクションにあります。

最初の手順として、オンプレミスのコンピューターでデータ管理ゲートウェイを設定します。 設定手順は、 [オンプレミスの場所とクラウドの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md) に関する記事に記載されています。

**Azure Cognitive Search のリンクされたサービス:**

```JSON
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**SQL Server のリンクされているサービス**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**SQL Server 入力データセット**

このサンプルでは、SQL Server で「MyTable」という名前のテーブルを作成し、時系列データ用に「timestampcolumn」という名前の列が含まれているものと想定しています。 1 つのデータセットを使用して同じデータベース内の複数のテーブルに対してクエリを実行することはできますが、データセットの tableName typeProperty には 1 つのテーブルを使用する必要があります。

"external": "true" の設定により、このデータセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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

**Azure Cognitive Search の出力データセット:**

このサンプルは、**products** という名前の Azure Cognitive Search インデックスにデータをコピーします。 Data Factory では、インデックスは作成されません。 サンプルをテストするには、この名前を持つインデックスを作成します。 入力データセットと同じ列数を持つ検索インデックスを作成します。 新しいエントリが検索インデックスに 1 時間ごとに追加されます。

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

**SQL ソースと Azure Cognitive Search インデックス シンクを使用したパイプラインでのコピー アクティビティ:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **SqlSource** に設定され、**sink** 型が **AzureSearchIndexSink** に設定されています。 **SqlReaderQuery** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
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

クラウド データ ストアから Azure Cognitive Search にデータをコピーしている場合は、`executionLocation` プロパティが必要です。 たとえば、コピー アクティビティ `typeProperties` で必要となる変更は次の JSON スニペットのようになります。 「[クラウド データ ストア間でのデータのコピー](data-factory-data-movement-activities.md#global)」セクションで、サポートされている値および詳細を確認してください。

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```


## <a name="copy-from-a-cloud-source"></a>クラウド ソースからコピー
クラウド データ ストアから Azure Cognitive Search にデータをコピーしている場合は、`executionLocation` プロパティが必要です。 たとえば、コピー アクティビティ `typeProperties` で必要となる変更は次の JSON スニペットのようになります。 「[クラウド データ ストア間でのデータのコピー](data-factory-data-movement-activities.md#global)」セクションで、サポートされている値および詳細を確認してください。

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

コピー アクティビティ定義で、ソース データセットの列をシンク データセットの列にマップすることもできます。 詳細については、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページを参照してください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
データ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」をご覧ください。

## <a name="next-steps"></a>次の手順
次の記事を参照してください。

* [コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) を参照してください。
