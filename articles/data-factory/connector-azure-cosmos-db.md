---
title: Azure Cosmos DB のデータをコピーおよび変換する (SQL API)
description: Azure Cosmos DB (SQL API) との間でデータをコピーする方法、および Data Factory を使用して Azure Cosmos DB (SQL API) のデータを変換する方法について説明します。
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/13/2019
ms.openlocfilehash: bf24c12e8f1e5b7ee5c529ebffa6c15dd8acbcfc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74913405"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Cosmos DB (SQL API) のデータをコピーおよび変換する

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-azure-documentdb-connector.md)
> * [現在のバージョン](connector-azure-cosmos-db.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure Cosmos DB (SQL API) との間でデータをコピーする方法、および Data Flow を使用して Azure Cosmos DB (SQL API) のデータを変換する方法について説明します。 Azure Data Factory については、[入門記事で](introduction.md)をご覧ください。

>[!NOTE]
>このコネクタでは、Cosmos DB SQL API のみがサポートされます。 MongoDB API については、[Azure Cosmos DB の MongoDB 用の API コネクタ](connector-azure-cosmos-db-mongodb-api.md)に関する記事を参照してください。 現在、他の種類の API はサポートされていません。

## <a name="supported-capabilities"></a>サポートされる機能

この Azure Cosmos DB (SQL API) コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

コピー アクティビティの場合、この Azure Cosmos DB (SQL API) コネクタは次のことをサポートします。

- Azure Cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction) との間で双方向にデータをコピーします。
- **挿入**または **upsert** として Azure Cosmos DB に書き込みます。
- JSON ドキュメントをインポートおよびエクスポートしたり、表形式データセットに、または表形式データセットからデータをコピーしたりします。 例としては、SQL データベースや CSV ファイルなどがあります。 JSON ファイルまたは他の Azure Cosmos DB コレクションをコピー先またはコピー元としてドキュメントをそのままコピーするには、「JSON ドキュメントのインポート/エクスポート」を参照してください。

Data Factory は、Azure Cosmos DB に書き込むときに最適なパフォーマンスを提供できるよう、[Azure Cosmos DB Bulk Executor ライブラリ](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)と統合されます。

> [!TIP]
> [データ移行に関するビデオ](https://youtu.be/5-SRNiC_qOU)では、Azure Blob Storage から Azure Cosmos DB にデータをコピーする手順について説明されています。 また、Azure Cosmos DB にデータを取り込むときのパフォーマンスのチューニングに関する一般的な考慮事項も説明されています。

## <a name="get-started"></a>作業開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、Azure Cosmos DB (SQL API) に固有の Data Factory エンティティを定義するために使用できるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure Cosmos DB (SQL API) のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティは **CosmosDb** に設定する必要があります。 | はい |
| connectionString |Azure Cosmos DB データベースに接続するために必要な情報を指定します。<br />**メモ**:後の例で示すように、接続文字列でデータベース情報を指定する必要があります。 <br/>Data Factory に安全に格納するには、このフィールドを SecureString として指定します。 アカウント キーを Azure Key Vault に格納して、接続文字列から `accountKey` 構成をプルすることもできます。 詳細については、下記の例と、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」の記事を参照してください。 |はい |
| connectVia | データ ストアに接続するために使用される [Integration Runtime](concepts-integration-runtime.md)。 Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます (データ ストアがプライベート ネットワークにある場合)。 このプロパティを指定しないと、既定の Azure Integration Runtime が使用されます。 |いいえ |

**例**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
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

**例: アカウント キーを Azure Key Vault に格納する**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;Database=<Database>"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

データセットの定義に使用できるセクションとプロパティの完全な一覧については、「[データセットとリンクされたサービス](concepts-datasets-linked-services.md)」を参照してください。

Azure Cosmos DB (SQL API) データセットでは、次のプロパティがサポートされます。 

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの **type** プロパティは、**CosmosDbSqlApiCollection** に設定する必要があります. |はい |
| collectionName |Azure Cosmos DB ドキュメント コレクションの名前です。 |はい |

"DocumentDbCollection" 型のデータセットを使用する場合、コピーおよび検索のアクティビティの下位互換性のためにそのままサポートされています。Data Flow ではサポートされていません。 今後は新しいモデルを使用することをお勧めします。

**例**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Data Factory によるスキーマ

Azure Cosmos DB などのスキーマのないデータ ストアの場合、コピー アクティビティは、次の一覧で説明するいずれかの方法でスキーマを推論します。 [JSON ドキュメントをそのままインポートまたはエクスポートする](#import-or-export-json-documents)場合以外は、**structure** セクションにデータの構造を指定することがベスト プラクティスです。

Data Factory は、アクティビティに指定したマッピングを受け入れます。 行に列の値が含まれていない場合、列の値には null 値が指定されます。

マッピングを指定しない場合、Data Factory サービスは、データの最初の行を使用してスキーマを推論します。 最初の行に完全なスキーマが含まれていないと、アクティビティ操作の結果で一部の行が欠落します。

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

このセクションでは、Azure Cosmos DB (SQL API) のソースとシンクでサポートされるプロパティの一覧を示します。

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。

### <a name="azure-cosmos-db-sql-api-as-source"></a>ソースとしての Azure Cosmos DB (SQL API)

Azure Cosmos DB (SQL API) からデータをコピーするには、コピー アクティビティの **source** の種類を **DocumentDbCollectionSource** に設定します。 

コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティを **CosmosDbSqlApiSource** に設定する必要があります。 |はい |
| query |データを読み取る Azure Cosmos DB クエリを指定します。<br/><br/>例:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |いいえ <br/><br/>指定しないと、SQL ステートメント `select <columns defined in structure> from mycollection` が実行されます |
| preferredRegions | Cosmos DB からデータを取得しときに接続するリージョンの優先リスト。 | いいえ |
| PageSize | クエリ結果のページあたりのドキュメント数。 既定値は "-1" で、これはサービス側の動的ページ サイズが最大 1000 まで使用されることを意味します。 | いいえ |

"DocumentDbCollectionSource" 型のソースを使用する場合、下位互換性のためにそのままサポートされます。 Cosmos DB からデータをコピーする豊富な機能を提供する新しいモデルを使用することをお勧めします。

**例**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-sql-api-as-sink"></a>シンクとしての Azure Cosmos DB (SQL API)

Azure Cosmos DB (SQL API) にデータをコピーするには、コピー アクティビティの **sink** の種類を **DocumentDbCollectionSink** に設定します。 

コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの **type** プロパティは **CosmosDbSqlApiSink** に設定する必要があります。 |はい |
| writeBehavior |Azure Cosmos DB にデータを書き込む方法を示します。 使用可能な値は、**Insert**、**Upsert** です。<br/><br/>**upsert** の動作は、同じ ID を持つドキュメントが既に存在する場合に、そのドキュメントを置き換えることです。それ以外の場合はドキュメントを挿入します。<br /><br />**メモ**:元のドキュメントまたは列のマッピングで ID が指定されていない場合、Data Factory によってドキュメントの ID が自動的に生成されます。 つまり、**upsert** が期待どおりに動作するには、ドキュメントに ID があることを確認する必要があります。 |いいえ<br />(既定値は **insert** です) |
| writeBatchSize | Data Factory では、[Azure Cosmos DB Bulk Executor ライブラリ](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)を使用して Azure Cosmos DB にデータが書き込まれます。 **writeBatchSize** プロパティにより、ADF でライブラリに提供されるドキュメントのサイズが制御されます。 パフォーマンスを向上させるには **writeBatchSize** の値を大きくしてみて、ドキュメントのサイズが大きい場合は値を小さくしてみます。以下のヒントをご覧ください。 |いいえ<br />(既定値は **10,000**) |
| disableMetricsCollection | Data Factory では、コピーのパフォーマンスの最適化と推奨のために、Cosmos DB RU などのメトリックが収集されます。 この動作に不安がある場合は、`true` を指定してオフにします。 | いいえ (既定値は `false`) |

>[!TIP]
>Cosmos DB では、1 つの要求のサイズは 2 MB に制限されます。 式は、"要求サイズ = 1 つのドキュメント サイズ * バッチ書き込みサイズ" です。 "**要求のサイズが大きすぎる**" というエラーが発生する場合は、コピー シンクの構成で **`writeBatchSize` の値を小さくします**。

"DocumentDbCollectionSink" 型のソースを使用する場合、下位互換性のためにそのままサポートされます。 Cosmos DB からデータをコピーする豊富な機能を提供する新しいモデルを使用することをお勧めします。

**例**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
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
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

Mapping Data Flow の[ソース変換](data-flow-source.md)と[シンク変換](data-flow-sink.md)に関する記事で詳細を確認してください。

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[ルックアップ アクティビティ](control-flow-lookup-activity.md)に関する記事を参照してください。

## <a name="import-or-export-json-documents"></a>JSON ドキュメントをインポートまたはエクスポートする

Azure Cosmos DB (SQL API) コネクタを使用して簡単に次のことができます。

* Azure Blob Storage、Azure Data Lake Store、Azure Data Factory でサポートされているその他のファイル ベースのストアなどのさまざまなソースから Azure Cosmos DB に JSON ドキュメントをインポートします。
* JSON ドキュメントを Azure Cosmos DB コレクションからさまざまなファイル ベースのストアにエクスポートします。
* 2 つの Azure Cosmos DB コレクション間でドキュメントをそのままコピーします。

スキーマに依存しないコピーを実行するには:

* データ コピー ツールを使うときに、 **[Export as-is to JSON files or Cosmos DB collection]\(JSON ファイルまたは Cosmos DB コレクションにそのままエクスポートする\)** オプションを選択します。
* アクティビティの作成を使用する場合は、ソースまたはシンクの対応するファイル ストアで JSON 形式を選択します。

## <a name="next-steps"></a>次の手順

Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。
