---
title: Azure Database for PostgreSQL のデータのコピーと変換
description: Azure Data Factory を使用して、Azure Database for PostgreSQL のデータをコピーして変換する方法について説明します。
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/25/2021
ms.openlocfilehash: ec4ea645e325ef48d4cb5951cd39fd4e9cbe1617
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738057"
---
# <a name="copy-and-transform-data-in-azure-database-for-postgresql-by-using-azure-data-factory"></a>Azure Data Factory を使用して、Azure Database for PostgreSQL のデータをコピーして変換する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure Database for PostgreSQL との間でデータをコピーする方法、および Data Flow を使用して Azure Database for PostgreSQL のデータを変換する方法について説明します。 Azure Data Factory については、[入門記事で](introduction.md)をご覧ください。

このコネクタは、[Azure Database for PostgreSQL サービス](../postgresql/overview.md)用に特化しています。 オンプレミスまたはクラウドにある汎用 PostgreSQL データベースからデータをコピーするには、[PostgreSQL コネクタ](connector-postgresql.md)を使用します。

## <a name="supported-capabilities"></a>サポートされる機能

この Azure Database for PostgreSQL コネクタは、以下のアクティビティでサポートされています。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

現在、データ フローでは Azure Database for PostgreSQL のシングル サーバーがサポートされていますが、フレキシブル サーバーや Hyperscale (Citus) はサポートしていません。

## <a name="getting-started"></a>作業の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、Azure Database for PostgreSQL コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure Database for PostgreSQL のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**AzurePostgreSql** | はい |
| connectionString | Azure Database for PostgreSQL に接続するための ODBC 接続文字列。<br/>パスワードを Azure Key Vault に格納して、接続文字列から `password` 構成をプルすることもできます。 詳細については、下記の例と、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」を参照してください。 | はい |
| connectVia | このプロパティは、データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)を表します。 Azure 統合ランタイムまたは自己ホスト型統合ランタイム (データ ストアがプライベート ネットワークにある場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

一般的な接続文字列は `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>` です。 ケースごとにさらに多くのプロパティを設定できます。それらのプロパティを次に示します。

| プロパティ | 説明 | Options | 必須 |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| ドライバーとデータベース サーバー間で送信されるデータを暗号化するためにドライバーが使用するメソッド。 たとえば、`EncryptionMethod=<0/1/6>;` のように入力します。| 0 (暗号化なし) **(既定)** /1 (SSL)/6 (RequestSSL) | いいえ |
| ValidateServerCertificate (VSC) | SSL 暗号化が有効 (Encryption Method=1) になっているときに、データベース サーバーによって送信される証明書をドライバーが検証するかどうかを決定します。 たとえば、`ValidateServerCertificate=<0/1>;` のように入力します。| 0 (無効) **(既定)** / 1 (有効) | いいえ |

**例**:

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**例**:

***Azure Key Vault にパスワードを格納する***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、「[Azure Data Factory のデータセット](concepts-datasets-linked-services.md)」を参照してください。 このセクションでは、Azure Database for PostgreSQL がデータセットでサポートするプロパティの一覧を示します。

Azure Database for PostgreSQL からデータをコピーするには、データセットの type プロパティを **AzurePostgreSqlTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、**AzurePostgreSqlTable** に設定する必要があります | はい |
| tableName | テーブルの名前 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例**:

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[Azure Data Factory のパイプラインとアクティビティ](concepts-pipelines-activities.md)」を参照してください。 このセクションでは、Azure Database for PostgreSQL ソースでサポートされるプロパティの一覧を示します。

### <a name="azure-database-for-postgresql-as-source"></a>ソースとしての Azure Database for PostgreSql

Azure Database for PostgreSQL からデータをコピーするには、コピー アクティビティのソースの種類を **AzurePostgreSqlSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **AzurePostgreSqlSource** に設定する必要があります | はい |
| query | カスタム SQL クエリを使用してデータを読み取ります。 たとえば、`SELECT * FROM mytable` や `SELECT * FROM "MyTable"` などです。 PostgreSQL では、エンティティ名が引用符で囲まれていない場合、大文字と小文字が区別されません。 | いいえ (データセットの tableName プロパティが指定されている場合) |

**例**:

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM mytable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>シンクとしての Azure Database for PostgreSQL

データを Azure Database for PostgreSQL にコピーするために、コピー アクティビティの **sink** セクションでは以下のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは **AzurePostgreSQLSink** に設定する必要があります | はい |
| preCopyScript | コピー アクティビティの毎回の実行で、データを Azure Database for PostgreSQL に書き込む前に実行する SQL クエリを指定します。 このプロパティを使用して、事前に読み込まれたデータをクリーンアップできます。 | いいえ |
| writeMethod | Azure Database for PostgreSQL にデータを書き込むために使用するメソッド。<br>使用できる値は、以下のとおりです。**CopyCommand** (プレビューであり、パフォーマンスがより高い)、**BulkInsert** (既定)。 | いいえ |
| writeBatchSize | バッチごとに Azure Database for PostgreSQL に読み込まれる行の数。<br>許可される値は行数を表す整数です。 | いいえ (既定値は 1,000,000) |
| writeBatchTimeout | タイムアウトする前に一括挿入操作の完了を待つ時間です。<br>Timespan 文字列を値として使用できます。 たとえば "00:30:00" (30 分) を指定できます。 | いいえ (既定値は 00:30:00) |

**例**:

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeMethod": "CopyCommand",
                "writeBatchSize": 1000000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

マッピング データ フローでデータを変換する場合、Azure Database for PostgreSQL からテーブルの読み取りと書き込みを実行できます。 詳細については、マッピング データ フローの[ソース変換](data-flow-source.md)と[シンク変換](data-flow-sink.md)に関する記事をご覧ください。 ソースとシンクの種類として、Azure Database for PostgreSQL データセットまたは[インライン データセット](data-flow-source.md#inline-datasets)を使用することができます。

### <a name="source-transformation"></a>ソース変換

次の表に、Azure Database for PostgreSQL ソースでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[ソース オプション]** タブで編集できます。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| テーブル | [テーブル] を入力として選択した場合、データセットで指定されたテーブルからすべてのデータがデータ フローによってフェッチされます。 | いいえ | - |*(インライン データセットのみ)*<br>tableName |
| クエリ | [クエリ] を入力として選択した場合は、ソースからデータをフェッチする SQL クエリを指定します。これにより、データセットで指定したテーブルがオーバーライドされます。 テストまたはルックアップ対象の行を減らすうえで、クエリの使用は有効な手段です。<br><br>**Order By** 句はサポートされていませんが、完全な SELECT FROM ステートメントを設定することができます。 ユーザー定義のテーブル関数を使用することもできます。 **select * from udfGetData()** は、データ フローで使用できるテーブルを返す SQL の UDF です。<br>クエリ例: `select * from mytable where customerId > 1000 and customerId < 2000` または `select * from "MyTable"`。 PostgreSQL では、エンティティ名が引用符で囲まれていない場合、大文字と小文字が区別されません。| いいえ | String | query |
| バッチ サイズ | 大量データをバッチにまとめるバッチ サイズを指定します。 | いいえ | Integer | batchSize |
| Isolation Level | 次のいずれかの分離レベルを選択します。<br>- コミットされたものを読み取り<br>- コミットされていないものを読み取り (既定値)<br>- 反復可能読み取り<br>- シリアル化可能<br>- なし (分離レベルを無視) | いいえ | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>NONE</small> |isolationLevel |

#### <a name="azure-database-for-postgresql-source-script-example"></a>Azure Database for PostgreSQL ソース スクリプトの例

ソースの種類として Azure Database for PostgreSQL を使用する場合、関連付けられているデータ フロー スクリプトは次のようになります。

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzurePostgreSQLSource
```

### <a name="sink-transformation"></a>シンク変換

次の表に、Azure Database for PostgreSQL シンクでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[シンク オプション]** タブで編集できます。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 更新方法 | 対象となるデータベースに対して許可される操作を指定します。 既定では、挿入のみが許可されます。<br>行を更新、アップサート、または削除するには、それらのアクションに対して行をタグ付けするために[行の変更変換](data-flow-alter-row.md)が必要になります。 | はい | `true` または `false` | deletable <br/>insertable <br/>updateable <br/>upsertable |
| [キー列] | 更新、upsert、削除の場合、キー列 (複数可) を設定して、変更する行を決定する必要があります。<br>キーとして選択する列の名前は、後続の更新、upsert、削除の一部として使用されます。 そのため、シンク マッピングに存在する列を選択する必要があります。 | いいえ | Array | キー |
| Skip writing key columns\(キー列の書き込みをスキップする) | キー列に値を書き込まない場合は、[Skip writing key columns]\(キー列の書き込みをスキップする\) を選択します。 | いいえ | `true` または `false` | skipKeyWrites |
| テーブル アクション |書き込み前に変換先テーブルのすべての行を再作成するか削除するかを指定します。<br>- **なし**: テーブルに対してアクションは実行されません。<br>- **Recreate**:テーブルが削除され、再作成されます。 新しいテーブルを動的に作成する場合に必要です。<br>- **Truncate**:ターゲット テーブルのすべての行が削除されます。 | いいえ | `true` または `false` | recreate<br/>truncate |
| バッチ サイズ | 各バッチで書き込まれる行の数を指定します。 バッチ サイズを大きくすると、圧縮とメモリの最適化が向上しますが、データをキャッシュする際にメモリ不足の例外が発生するリスクがあります。 | いいえ | Integer | batchSize |
| 事前および事後の SQL スクリプト | データがシンク データベースに書き込まれる前 (前処理) と書き込まれた後 (後処理) に実行される複数行の SQL スクリプトを指定します。 | いいえ | String | preSQLs<br>postSQLs |

#### <a name="azure-database-for-postgresql-sink-script-example"></a>Azure Database for PostgreSQL シンク スクリプトの例

シンクの種類として Azure Database for PostgreSQL を使用する場合、関連付けられているデータ フロー スクリプトは次のようになります。

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzurePostgreSQLSink
```

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、「[Azure Data Factory でのルックアップ アクティビティ](control-flow-lookup-activity.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するページをご覧ください。
