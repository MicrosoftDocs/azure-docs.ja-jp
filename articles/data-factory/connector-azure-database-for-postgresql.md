---
title: Azure Database for PostgreSQL との間でデータをコピーする
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、Azure Database for PostgreSQL との間で双方向にデータをコピーする方法について説明します。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/16/2019
ms.openlocfilehash: 3729d43716a0f4e133fb175da2546aac560b0525
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931611"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Database for PostgreSQL との間でデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティ機能を使用して、Azure Database for PostgreSQL からデータをコピーする方法について説明します。 コピー アクティビティの概要が説明されている「[Azure Data Factory のコピー アクティビティ](copy-activity-overview.md)」という記事を基に作成されています。

このコネクタは、[Azure Database for PostgreSQL サービス](../postgresql/overview.md)用に特化しています。 オンプレミスまたはクラウドにある汎用 PostgreSQL データベースからデータをコピーするには、[PostgreSQL コネクタ](connector-postgresql.md)を使用します。

## <a name="supported-capabilities"></a>サポートされる機能

この Azure Database for PostgreSQL コネクタは、以下のアクティビティでサポートされています。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

Azure Database for PostgreSQL のデータを、サポートされているシンク データ ストアにコピーできます。 または、サポートされているソース データ ストアのデータを Azure Database for PostgreSQL にコピーすることができます。 コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表を参照してください。

Azure Data Factory では、接続を可能にする組み込みのドライバーが提供されます。 そのため、このコネクタを使用するためにドライバーを手動でインストールする必要はありません。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、Azure Database for PostgreSQL コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure Database for PostgreSQL のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**AzurePostgreSql** | はい |
| connectionString | Azure Database for PostgreSQL に接続するための ODBC 接続文字列。<br/>Data Factory に安全に格納するには、このフィールドを SecureString として指定します。 パスワードを Azure Key Vault に格納して、接続文字列から `password` 構成をプルすることもできます。 詳細については、下記の例と、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」を参照してください。 | はい |
| connectVia | このプロパティは、データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)を表します。 Azure 統合ランタイムまたは自己ホスト型統合ランタイム (データ ストアがプライベート ネットワークにある場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

一般的な接続文字列は `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>` です。 ケースごとにさらに多くのプロパティを設定できます。それらのプロパティを次に示します。

| プロパティ | 説明 | オプション | 必須 |
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
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
            }
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
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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
| query | カスタム SQL クエリを使用してデータを読み取ります。 次に例を示します。`"SELECT * FROM MyTable"` | いいえ (データセットの tableName プロパティが指定されている場合) |

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
                "query": "<custom query e.g. SELECT * FROM MyTable>"
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
| writeBatchSize | バッファー サイズが writeBatchSize に達したら、Azure Database for PostgreSQL テーブルにデータを挿入します。<br>許可される値は行数を表す整数です。 | いいえ (既定値は 10,000) |
| writeBatchTimeout | タイムアウトする前に一括挿入操作の完了を待つ時間です。<br>Timespan 文字列を値として使用できます。 たとえば "00:30:00" (30 分) を指定できます。 | いいえ (既定値は 00:00:30) |

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
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、「[Azure Data Factory でのルックアップ アクティビティ](control-flow-lookup-activity.md)」を参照してください。

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するページをご覧ください。
