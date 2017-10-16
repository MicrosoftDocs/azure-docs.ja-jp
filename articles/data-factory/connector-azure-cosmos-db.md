---
title: "Data Factory を使用して Azure Cosmos DB をコピー先またはコピー元としてデータをコピーする | Microsoft Docs"
description: "Data Factory を使用して、サポートされるソース データ ストアのデータを Azure Cosmos DB にコピーしたり、Cosmos DB のデータをサポートされるシンク ストアにコピーしたりできます。"
services: data-factory, cosmosdb
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 7d914684a0ee5598cee7972b78c3ec6296184466
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-to-or-from-azure-cosmos-db-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Cosmos DB をコピー先またはコピー元としてデータをコピーする

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-azure-documentdb-connector.md)
> * [バージョン 2 - プレビュー](connector-azure-cosmos-db.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure Cosmos DB (DocumentDB API) をコピー元またはコピー先としてデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[V1 の Azure Cosmos DB コネクタ](v1/data-factory-azure-documentdb-connector.md)に関する記事を参照してください。

## <a name="supported-scenarios"></a>サポートされるシナリオ

Azure Cosmos DB のデータをサポートされる任意のシンク データ ストアにコピーしたり、サポートされる任意のソース データ ストアのデータを Azure Cosmos DB にコピーしたりできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。

具体的には、この Azure Cosmos DB コネクタは、以下をサポートします。

- Cosmos DB [DocumentDB API](https://docs.microsoft.com/en-us/azure/cosmos-db/documentdb-introduction)
- JSON ドキュメントをそのままインポートまたはエクスポートするか、表形式のデータセット (SQL Database、CSV ファイルなど) をコピー元またはコピー先としてデータをコピーします。

JSON ファイルまたは他の Cosmos DB コレクションをコピー先またはコピー元としてドキュメントをそのままコピーするには、「[JSON ドキュメントのインポート/エクスポート](#importexport-json-documents)」を参照してください。

## <a name="getting-started"></a>使用の開始
コピー アクティビティを含むパイプラインは、.NET SDK、Python SDK、Azure PowerShell、REST API、または Azure Resource Manager テンプレートを使用して作成できます。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](quickstart-create-data-factory-dot-net.md)をご覧ください。

以下のセクションで、Azure Cosmos DB に固有の Data Factory エンティティを定義するために使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure Cosmos DB のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **CosmosDb** に設定する必要があります。 | あり |
| connectionString |Azure Cosmos DB データベースに接続するために必要な情報を指定します。 次の例に示すように、接続文字列にデータベース情報を指定する必要があります。 このフィールドを SecureString とマークします。 |あり |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイム (データ ストアがプライベート ネットワークにある場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

**例:**

```json
{
    "name": "CosmosDbLinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、Azure Cosmos DB データセットでサポートされるプロパティの一覧を示します。

Azure Cosmos DB をコピー元またはコピー先としてデータのコピーを行うには、データセットの type プロパティを **DocumentDbCollection** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、**DocumentDbCollection** を設定する必要があります。 |あり |
| collectionName |Cosmos DB ドキュメント コレクションの名前です。 |あり |

**例:**

```json
{
    "name": "CosmosDbDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Data Factory によるスキーマ

Azure Cosmos DB などのスキーマのないデータ ストアの場合、コピー アクティビティは、次のいずれかの方法でスキーマを推論します。 したがって、[JSON ドキュメントをそのままインポートまたはエクスポートする](#importexport-json-documents)場合以外は、**structure** セクションにデータの構造を指定することがベスト プラクティスです。

1. データセット定義で **structure** プロパティを使用してデータの構造を指定した場合、Data Factory サービスはスキーマとしてこの構造を優先します。 この場合、行に列の値が含まれていないと、null 値が指定されます。
2. データセット定義で **structure** プロパティを使用してデータの構造を指定しなかった場合、Data Factory サービスはデータの最初の行を使用してスキーマを推論します。 この場合、最初の行に完全なスキーマが含まれていないと、コピー操作の結果で一部の行が欠落します。

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Azure Cosmos DB のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-cosmos-db-as-source"></a>ソースとしての Azure Cosmos DB

Azure Cosmos DB からデータをコピーするには、コピー アクティビティのソースの種類を **DocumentDbCollectionSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **DocumentDbCollectionSource** を設定する必要があります。 |あり |
| クエリ |データを読み取る Cosmos DB クエリを指定します。<br/><br/>例: `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |なし <br/><br/>指定されていない場合に実行される SQL ステートメント: `select <columns defined in structure> from mycollection` |
| nestingSeparator |ドキュメントが入れ子になっていることと、結果セットの入れ子を解除する方法を示す特殊文字。<br/><br/>たとえば、Cosmos DB クエリで入れ子になった結果 `"Name": {"First": "John"}` が返された場合、nestedSeparator がドットであるとき、コピー アクティビティは、値が "John" である列名 "Name.First" として識別します。 |いいえ (既定値はドット `.`) |

**例:**

```json
"activities":[
    {
        "name": "CopyFromCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Document DB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-as-sink"></a>シンクとしての Azure Cosmos DB

Azure Cosmos DB からデータをコピーするには、コピー アクティビティのシンクの種類を **DocumentDbCollectionSink** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **DocumentDbCollectionSink** を設定する必要があります。 |あり |
| nestingSeparator |入れ子になった文書が必要であることを示すソース列名の特殊文字。 <br/><br/>たとえば、出力データセット構造内の `Name.First` は、nestedSeparator がドットの場合は、次の JSON 構造を Cosmos DB ドキュメント内に生成します。`"Name": {"First": "[value maps to this column from source]"}` |いいえ (既定値はドット `.`) |
| writeBatchTimeout |タイムアウトする前に操作の完了を待つ時間です。<br/><br/>使用可能な値: 期間。 例: "00:30:00" (30 分)。 |いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyToCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink"
            }
        }
    }
]
```

## <a name="importexport-json-documents"></a>JSON ドキュメントのインポート/エクスポート

この Cosmos DB コネクタを使って、次の作業を簡単に実行できます。

* Azure BLOB、Azure Data Lake Store、Azure Data Factory でサポートされているその他のファイル ベースのストアなどのさまざまなソースから Cosmos DB に JSON ドキュメントをインポートする。
* JSON ドキュメントを Cosmos DB コレクションからさまざまなファイル ベースのストアにエクスポートする。
* 2 つの Cosmos DB コレクション間でドキュメントをそのままコピーする。

このようなスキーマに依存しないコピーを実行するには:

- Cosmos DB データセットに "structure" セクションを指定しません。コピー アクティビティの Cosmos DB のソース/シンクに "nestingSeparator" プロパティを指定しません。
- JSON ファイルに対するインポート/エクスポートを行うときに、対応するファイル ストア データセットに、形式の種類 "JsonFormat" を指定し、"filePattern" プロパティを構成します (詳細は「[JSON 形式](supported-file-formats-and-compression-codecs.md#json-format)」セクションを参照)。次に、"structure" セクションを指定せず、残りの形式設定セクションをスキップします。

## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。