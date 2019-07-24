---
title: Data Factory を使用して Azure Cosmos DB (SQL API) との間でデータを双方向にコピーする | Microsoft Docs
description: Data Factory を使用して、サポートされるソース データ ストアのデータを Azure Cosmos DB (SQL API) にコピーしたり、Azure Cosmos DB のデータをサポートされるシンク ストアにコピーしたりする方法を説明します。
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: eca5e4cc96996c35e7c2181746cdb3de2e5a602c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820064"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Cosmos DB (SQL API) との間でデータを双方向にコピーする

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-azure-documentdb-connector.md)
> * [現在のバージョン](connector-azure-cosmos-db.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure Cosmos DB (SQL API) をコピー元またはコピー先としてデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要が説明されている「[Azure Data Factory のコピー アクティビティ](copy-activity-overview.md)」を基に作成されています。

>[!NOTE]
>このコネクタでは、Cosmos DB SQL API との間でのデータの双方向コピーのみがサポートされます。 MongoDB API については、[Azure Cosmos DB の MongoDB 用の API コネクタ](connector-azure-cosmos-db-mongodb-api.md)に関する記事を参照してください。 現在、他の種類の API はサポートされていません。

## <a name="supported-capabilities"></a>サポートされる機能

Azure Cosmos DB (SQL API) のデータをサポートされる任意のシンク データ ストアにコピーしたり、サポートされる任意のソース データ ストアのデータを Azure Cosmos DB (SQL API) にコピーしたりすることができます。 コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。

Azure Cosmos DB (SQL API) コネクタを使用して次のことができます。

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
| connectVia | データ ストアに接続するために使用される [Integration Runtime](concepts-integration-runtime.md)。 Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます (データ ストアがプライベート ネットワークにある場合)。 このプロパティを指定しないと、既定の Azure Integration Runtime が使用されます。 |いいえ  |

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

このセクションでは、Azure Cosmos DB (SQL API) データセットでサポートされているプロパティの一覧を示します。 

データセットの定義に使用できるセクションとプロパティの完全な一覧については、「[データセットとリンクされたサービス](concepts-datasets-linked-services.md)」を参照してください。 

Azure Cosmos DB (SQL API) をコピー元またはコピー先としてデータのコピーを行うには、データセットの **type** プロパティを **DocumentDbCollection** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの **type** プロパティは、**DocumentDbCollection** を設定する必要があります。 |はい |
| collectionName |Azure Cosmos DB ドキュメント コレクションの名前です。 |はい |

**例**

```json
{
    "name": "CosmosDbSQLAPIDataset",
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

Azure Cosmos DB などのスキーマのないデータ ストアの場合、コピー アクティビティは、次の一覧で説明するいずれかの方法でスキーマを推論します。 [JSON ドキュメントをそのままインポートまたはエクスポートする](#import-or-export-json-documents)場合以外は、**structure** セクションにデータの構造を指定することがベスト プラクティスです。

* データセット定義で **structure** プロパティを使用してデータの構造を指定した場合、Data Factory はスキーマとしてこの構造を優先します。 

    行に列の値が含まれていない場合、列の値には null 値が指定されます。
* データセット定義で **structure** プロパティを使用してデータの構造を指定しなかった場合、Data Factory サービスはデータの最初の行を使用してスキーマを推論します。 

    最初の行に完全なスキーマが含まれていないと、コピー操作の結果で一部の行が欠落します。

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

このセクションでは、Azure Cosmos DB (SQL API) のソースとシンクでサポートされるプロパティの一覧を示します。

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。

### <a name="azure-cosmos-db-sql-api-as-source"></a>ソースとしての Azure Cosmos DB (SQL API)

Azure Cosmos DB (SQL API) からデータをコピーするには、コピー アクティビティの **source** の種類を **DocumentDbCollectionSource** に設定します。 

コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティは **DocumentDbCollectionSource** に設定する必要があります。 |はい |
| query |データを読み取る Azure Cosmos DB クエリを指定します。<br/><br/>例:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |いいえ  <br/><br/>指定しないと、SQL ステートメント `select <columns defined in structure> from mycollection` が実行されます |
| nestingSeparator |ドキュメントが入れ子になっていることと、結果セットの入れ子を解除する方法を示す特殊文字。<br/><br/>たとえば、Azure Cosmos DB クエリで入れ子になった結果 `"Name": {"First": "John"}` が返される場合、コピー アクティビティは値が "John" である列名 `Name.First` として識別します (**nestedSeparator** の値が **.**  (ドット) のとき)。 |いいえ <br />(既定値は **.**  (ドット) です) |

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

### <a name="azure-cosmos-db-sql-api-as-sink"></a>シンクとしての Azure Cosmos DB (SQL API)

Azure Cosmos DB (SQL API) にデータをコピーするには、コピー アクティビティの **sink** の種類を **DocumentDbCollectionSink** に設定します。 

コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの **type** プロパティは **DocumentDbCollectionSink** に設定する必要があります。 |はい |
| writeBehavior |Azure Cosmos DB にデータを書き込む方法を示します。 使用可能な値は、**Insert**、**Upsert** です。<br/><br/>**upsert** の動作は、同じ ID を持つドキュメントが既に存在する場合に、そのドキュメントを置き換えることです。それ以外の場合はドキュメントを挿入します。<br /><br />**メモ**:元のドキュメントまたは列のマッピングで ID が指定されていない場合、Data Factory によってドキュメントの ID が自動的に生成されます。 つまり、**upsert** が期待どおりに動作するには、ドキュメントに ID があることを確認する必要があります。 |いいえ <br />(既定値は **insert** です) |
| writeBatchSize | Data Factory では、[Azure Cosmos DB Bulk Executor ライブラリ](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)を使用して Azure Cosmos DB にデータが書き込まれます。 **writeBatchSize** プロパティにより、ADF でライブラリに提供されるドキュメントのサイズが制御されます。 パフォーマンスを向上させるには **writeBatchSize** の値を大きくしてみて、ドキュメントのサイズが大きい場合は値を小さくしてみます。以下のヒントをご覧ください。 |いいえ <br />(既定値は **10,000**) |
| nestingSeparator |入れ子になった文書が必要であることを示す **source** 列名の特殊文字。 <br/><br/>たとえば、出力データセット構造内の `Name.First` は、**nestedSeparator** が **.**  (ドット) の場合は、次の JSON 構造を Azure Cosmos DB ドキュメント内に生成します。`"Name": {"First": "[value maps to this column from source]"}`  |いいえ <br />(既定値は **.**  (ドット) です) |

>[!TIP]
>Cosmos DB では、1 つの要求のサイズは 2 MB に制限されます。 式は、"要求サイズ = 1 つのドキュメント サイズ * バッチ書き込みサイズ" です。 "**要求のサイズが大きすぎる**" というエラーが発生する場合は、コピー シンクの構成で **`writeBatchSize` の値を小さくします**。

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
                "type": "DocumentDbCollectionSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-or-export-json-documents"></a>JSON ドキュメントをインポートまたはエクスポートする

Azure Cosmos DB (SQL API) コネクタを使用して簡単に次のことができます。

* Azure Blob Storage、Azure Data Lake Store、Azure Data Factory でサポートされているその他のファイル ベースのストアなどのさまざまなソースから Azure Cosmos DB に JSON ドキュメントをインポートします。
* JSON ドキュメントを Azure Cosmos DB コレクションからさまざまなファイル ベースのストアにエクスポートします。
* 2 つの Azure Cosmos DB コレクション間でドキュメントをそのままコピーします。

スキーマに依存しないコピーを実行するには:

* データ コピー ツールを使うときに、**[Export as-is to JSON files or Cosmos DB collection]\(JSON ファイルまたは Cosmos DB コレクションにそのままエクスポートする\)** オプションを選択します。
* アクティビティの作成を使用するときは、Azure Cosmos DB データセットの **structure** (*schema* という名前のこともあります) セクションを指定しないでください。 また、コピー アクティビティの Azure Cosmos DB の source または sink で **nestingSeparator** プロパティを指定しないでください。 JSON ファイルをインポートまたはエクスポートするときは、対応するファイル ストアのデータセットで、**format** の種類を **JsonFormat** として指定し、**filePattern** を「[JSON 形式](supported-file-formats-and-compression-codecs.md#json-format)」セクションで説明されているように構成します。 そして、**structure** セクションを指定せず、書式設定の残りの部分をスキップします。

## <a name="next-steps"></a>次の手順

Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。
