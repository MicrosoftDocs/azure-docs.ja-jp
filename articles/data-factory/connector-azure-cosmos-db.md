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
ms.date: 01/29/2021
ms.openlocfilehash: 1d9e43aafbe1f9fdd48596c54138075e23a25590
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222918"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Cosmos DB (SQL API) のデータをコピーおよび変換する

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-azure-documentdb-connector.md)
> * [現在のバージョン](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure Cosmos DB (SQL API) との間でデータをコピーする方法、および Data Flow を使用して Azure Cosmos DB (SQL API) のデータを変換する方法について説明します。 Azure Data Factory については、[入門記事で](introduction.md)をご覧ください。



>[!NOTE]
>このコネクタでは、Cosmos DB SQL API のみがサポートされます。 MongoDB API については、[Azure Cosmos DB の MongoDB 用の API コネクタ](connector-azure-cosmos-db-mongodb-api.md)に関する記事を参照してください。 現在、他の種類の API はサポートされていません。

## <a name="supported-capabilities"></a>サポートされる機能

この Azure Cosmos DB (SQL API) コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

コピー アクティビティの場合、この Azure Cosmos DB (SQL API) コネクタは次のことをサポートします。

- Azure Cosmos DB [SQL API](../cosmos-db/introduction.md) との間で双方向にデータをコピーします。
- **挿入** または **upsert** として Azure Cosmos DB に書き込みます。
- JSON ドキュメントをインポートおよびエクスポートしたり、表形式データセットに、または表形式データセットからデータをコピーしたりします。 例としては、SQL データベースや CSV ファイルなどがあります。 JSON ファイルまたは他の Azure Cosmos DB コレクションをコピー先またはコピー元としてドキュメントをそのままコピーするには、「[JSON ドキュメントのインポートとエクスポート](#import-and-export-json-documents)」を参照してください。

Data Factory は、Azure Cosmos DB に書き込むときに最適なパフォーマンスを提供できるよう、[Azure Cosmos DB Bulk Executor ライブラリ](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)と統合されます。

> [!TIP]
> [データ移行に関するビデオ](https://youtu.be/5-SRNiC_qOU)では、Azure Blob Storage から Azure Cosmos DB にデータをコピーする手順について説明されています。 また、Azure Cosmos DB にデータを取り込むときのパフォーマンスのチューニングに関する一般的な考慮事項も説明されています。

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、Azure Cosmos DB (SQL API) に固有の Data Factory エンティティを定義するために使用できるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure Cosmos DB (SQL API) のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティは **CosmosDb** に設定する必要があります。 | はい |
| connectionString |Azure Cosmos DB データベースに接続するために必要な情報を指定します。<br />**注**:後の例で示すように、接続文字列でデータベース情報を指定する必要があります。 <br/> アカウント キーを Azure Key Vault に格納して、接続文字列から `accountKey` 構成をプルすることもできます。 詳細については、下記の例と、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」の記事を参照してください。 |はい |
| connectVia | データ ストアに接続するために使用される [Integration Runtime](concepts-integration-runtime.md)。 Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます (データ ストアがプライベート ネットワークにある場合)。 このプロパティを指定しないと、既定の Azure Integration Runtime が使用されます。 |いいえ |

**例**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
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
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

このセクションでは、Azure Cosmos DB (SQL API) のソースとシンクでサポートされるプロパティの一覧を示します。 アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。

### <a name="azure-cosmos-db-sql-api-as-source"></a>ソースとしての Azure Cosmos DB (SQL API)

Azure Cosmos DB (SQL API) からデータをコピーするには、コピー アクティビティの **source** の種類を **DocumentDbCollectionSource** に設定します。 

コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティを **CosmosDbSqlApiSource** に設定する必要があります。 |はい |
| query |データを読み取る Azure Cosmos DB クエリを指定します。<br/><br/>例:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |いいえ <br/><br/>指定しないと、SQL ステートメント `select <columns defined in structure> from mycollection` が実行されます |
| preferredRegions | Cosmos DB からデータを取得するときに接続するリージョンの優先リスト。 | いいえ |
| PageSize | クエリ結果のページあたりのドキュメント数。 既定値は "-1" で、これはサービス側の動的ページ サイズが最大 1000 まで使用されることを意味します。 | いいえ |
| detectDatetime | ドキュメント内の文字列値から datetime を検出するかどうか。 使用可能な値: **true** (既定値)、**false**。 | いいえ |

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

Cosmos DB からデータをコピーする場合、[JSON ドキュメントをそのままエクスポートする](#import-and-export-json-documents)場合を除き、ベスト プラクティスはコピー アクティビティでマッピングを指定することです。 Data Factory では、アクティビティで指定したマッピングが優先されます。行に列の値が含まれていない場合、列の値には null 値が指定されます。 マッピングを指定しない場合、Data Factory では、データの最初の行を使用してスキーマが推論されます。 最初の行に完全なスキーマが含まれていないと、アクティビティ操作の結果で一部の行が欠落します。

### <a name="azure-cosmos-db-sql-api-as-sink"></a>シンクとしての Azure Cosmos DB (SQL API)

Azure Cosmos DB (SQL API) にデータをコピーするには、コピー アクティビティの **sink** の種類を **DocumentDbCollectionSink** に設定します。 

コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの **type** プロパティは **CosmosDbSqlApiSink** に設定する必要があります。 |はい |
| writeBehavior |Azure Cosmos DB にデータを書き込む方法を示します。 使用可能な値は、**Insert**、**Upsert** です。<br/><br/>**upsert** の動作は、同じ ID を持つドキュメントが既に存在する場合に、そのドキュメントを置き換えることです。それ以外の場合はドキュメントを挿入します。<br /><br />**注**: 元のドキュメントまたは列のマッピングで ID が指定されていない場合、Data Factory によってドキュメントの ID が自動的に生成されます。 つまり、**upsert** が期待どおりに動作するには、ドキュメントに ID があることを確認する必要があります。 |いいえ<br />(既定値は **insert** です) |
| writeBatchSize | Data Factory では、[Azure Cosmos DB Bulk Executor ライブラリ](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)を使用して Azure Cosmos DB にデータが書き込まれます。 **writeBatchSize** プロパティにより、ADF でライブラリに提供されるドキュメントのサイズが制御されます。 パフォーマンスを向上させるには **writeBatchSize** の値を大きくしてみて、ドキュメントのサイズが大きい場合は値を小さくしてみます。以下のヒントをご覧ください。 |いいえ<br />(既定値は **10,000**) |
| disableMetricsCollection | Data Factory では、コピーのパフォーマンスの最適化と推奨のために、Cosmos DB RU などのメトリックが収集されます。 この動作に不安がある場合は、`true` を指定してオフにします。 | いいえ (既定値は `false`) |

>[!TIP]
>JSON ドキュメントをそのままインポートするには、「[JSON ドキュメントのインポートとエクスポート](#import-and-export-json-documents)」セクションを参照してください。表形式のデータからコピーするには、「[リレーショナル データベースから Cosmos DB への移行](#migrate-from-relational-database-to-cosmos-db)」を参照してください。

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

### <a name="schema-mapping"></a>スキーマ マッピング

Azure Cosmos DB から表形式のシンク、あるいはその逆の方向でデータをコピーするには、[スキーマ マッピング](copy-activity-schema-and-type-mapping.md#schema-mapping)に関するセクションを参照してください。

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

マッピング データフローでデータを変換する場合は、Cosmos DB でコレクションの読み取りと書き込みを行うことができます。 詳細については、マッピング データ フローの[ソース変換](data-flow-source.md)と[シンク変換](data-flow-sink.md)に関する記事をご覧ください。

### <a name="source-transformation"></a>ソース変換

Azure Cosmos DB に固有の設定は、ソース変換の **[Source Options]\(ソース オプション\)** タブにあります。 

**Include system columns (システム列を含める):** true にすると、CosmosDB からのデータ フローのメタデータに ```id``` や ```_ts``` といったシステム列が含められます。 コレクションを更新するときは、既存の行 ID を把握できるように、これを含めることが重要となります。

**ページ サイズ:** クエリ結果のページあたりのドキュメント数。 既定値は "-1" で、サービスの動的ページが最大 1000 個使用されます。

**スループット**: このデータ フローの各実行について、CosmosDB コレクションの読み取り操作時に適用したい RU 数に対するオプションの値を設定します。 最小は 400 です。

**Preferred regions (優先リージョン):** このプロセスの優先読み取りリージョンを選択します。

#### <a name="json-settings"></a>JSON 設定

**1 つのドキュメント:** ADF でファイル全体を 1 つの JSON ドキュメントとして扱う場合は、このオプションを選択します。

**引用符で囲まれていない列名:** JSON の列名が引用符で囲まれていない場合は、このオプションを選択します。

**コメントあり:** JSON ドキュメントのデータにコメントが含まれている場合は、このオプションを使用します。

**一重引用符付き:** ドキュメント内の列と値が一重引用符で囲まれている場合は、このオプションを選択する必要があります。

**円記号によるエスケープ:** バックスラッシュを使用して JSON 内の文字をエスケープする場合は、このオプションを選択します。

### <a name="sink-transformation"></a>シンク変換

Azure Cosmos DB に固有の設定は、シンク変換の **[設定]** タブにあります。

**[Update method]\(更新方法\)** : 対象となるデータベースに対して許可される操作を指定します。 既定では、挿入のみが許可されます。 行を更新、アップサート、または削除するには、それらのアクションに対して行をタグ付けするために行の変更変換が必要になります。 更新、アップサート、削除の場合、1 つまたは複数のキー列を設定して、変更する行を決定する必要があります。

**Collection action (コレクション アクション):** 書き込み前にターゲット コレクションを再作成するかどうかを決定します。
* なし: コレクションに対してアクションは実行されません。
* 再作成: コレクションは削除され、再作成されます

**Batch size**: 各バッチの Cosmos DB コレクションに書き込まれるオブジェクトの数を表す整数。 通常、既定のバッチ サイズで開始するだけで十分です。 この値をさらに調整するには、次の点に注意してください。

- Cosmos DB では、1 つの要求のサイズは 2 MB に制限されます。 式は、"要求サイズ = 1 つのドキュメント サイズ * バッチ サイズ" となります。 "要求のサイズが大きすぎる" というエラーが発生する場合は、バッチ サイズの値を小さくします。
- バッチ サイズを大きくすると、ADF のスループット向上を達成できますが、ワークロードを強化するために十分な RU を割り当てるようにしてください。

**パーティション キー**:コレクションのパーティション キーを表す文字列を入力します。 例: ```/movies/title```

**スループット**: このデータ フローの実行ごとに CosmosDB コレクションに適用する RU の数のオプション値を設定します。 最小は 400 です。

**Write throughput budget (書き込みスループット予算)** : コレクションに割り当てられた合計スループットのうち、このデータ フロー書き込み操作に割り当てる RU を表す整数。

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="import-and-export-json-documents"></a>JSON ドキュメントのインポートとエクスポート

Azure Cosmos DB (SQL API) コネクタを使用して簡単に次のことができます。

* 2 つの Azure Cosmos DB コレクション間でドキュメントをそのままコピーします。
* Azure Blob Storage、Azure Data Lake Store、Azure Data Factory でサポートされているその他のファイル ベースのストアなどのさまざまなソースから Azure Cosmos DB に JSON ドキュメントをインポートします。
* JSON ドキュメントを Azure Cosmos DB コレクションからさまざまなファイル ベースのストアにエクスポートします。

スキーマに依存しないコピーを実行するには:

* データ コピー ツールを使うときに、**[Export as-is to JSON files or Cosmos DB collection]\(JSON ファイルまたは Cosmos DB コレクションにそのままエクスポートする\)** オプションを選択します。
* アクティビティの作成を使用する場合は、ソースまたはシンクの対応するファイル ストアで JSON 形式を選択します。

## <a name="migrate-from-relational-database-to-cosmos-db"></a>リレーショナル データベースから Cosmos DB への移行

SQL Server などのリレーショナル データベースから Azure Cosmos DB に移行する場合、コピー アクティビティを使うと、ソースの表形式のデータを Cosmos DB のフラット化された JSON ドキュメントに簡単にマッピングできます。 場合によっては、たとえば、1 つの JSON ドキュメント内に関連するすべてのサブアイテムを埋め込むことでデータを非正規化するためなどに、データ モデルを再設計し、[Azure Cosmos DB のデータ モデリング](../cosmos-db/modeling-data.md)に従って NoSQL ユース ケースに合わせて最適化することもできます。 そのような場合は、[こちらの記事](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md)を参照してください。Azure Data Factory のコピー操作でこれを行う方法のチュートリアルがあります。

## <a name="next-steps"></a>次のステップ

Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。