---
title: Azure Data Factory を使用した Teradata からのデータ コピー | Microsoft Docs
description: Data Factory サービスの Teradata コネクタについて説明します。このコネクタを使用すると、Teradata データベースから、Data Factory によってサポートされているデータ ストアに、シンクとしてデータをコピーすることができます。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 63f28c8b6eaceed12e1f76e9c0c5984e3b63b500
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561427"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Azure Data Factory を使用して Teradata からデータをコピーする
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
>
> * [Version 1](v1/data-factory-onprem-teradata-connector.md)
> * [現在のバージョン](connector-teradata.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、Teradata データベースからデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

Teradata データベースから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この Teradata コネクタは以下をサポートします。

- Teradata **バージョン 14.10、15.0、15.10、16.0、16.10、16.20**。
- **基本**または **Windows** 認証を使用したデータのコピー。
- Teradata ソースからの並列コピー。 詳細については、「[Teradata からの並列コピー](#parallel-copy-from-teradata)」セクションを参照してください。

> [!NOTE]
>
> Azure Data Factory は、セルフホステッド Integration Runtime v3.18 以降、Teradata コネクタをアップグレードしました。これは、組み込みの ODBC ドライバーによって実現し、柔軟な接続オプションや既定の並列コピーを提供してパフォーマンスを向上させます。 .NET Data Provider for Teradata によって動作する、以前の Teradata コネクタを使用している既存のワークロードは、引き続きそのままサポートされますが、今後は新しいものを使用することをお勧めします。 新しいパスには、リンクされたサービス、データセット、およびコピー ソースの別のセットが必要であることに注意してください。 構成の詳細については、各セクションを参照してください。

## <a name="prerequisites"></a>前提条件

お使いの Teradata にパブリックにアクセスできない場合は、セルフホステッド統合ランタイムを設定する必要があります。 統合ランタイムの詳細については、「[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)」を参照してください。 Integration Runtime のバージョン 3.18 以降には組み込みの Teradata ドライバーが用意されているため、ドライバーを手動でインストールする必要はありません。 このドライバーでは、セルフホステッド IR マシン上に "Visual C ++ 再頒布可能パッケージ 2012 Update 4" を必要とします。まだインストールしていない場合は、[こちら](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)からダウンロードしてください。

セルフホステッド IR のバージョンが 3.18 より前の場合は、Integration Runtime マシン上に [.NET Data Provider for Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) バージョン 14 以降をインストールする必要があります。 

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Teradata コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Teradata のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**Teradata** | はい |
| connectionString | Teradata Database インスタンスに接続するために必要な情報を指定します。 以下のサンプルを参照してください。<br/>パスワードを Azure Key Vault に格納して、接続文字列から `password` 構成をプルすることもできます。 詳細については、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」の記事を参照してください。 | はい |
| username | Teradata データベースに接続するユーザー名を指定します。 Windows 認証の使用時に適用されます。 | いいえ |
| password | ユーザー名に指定したユーザー アカウントのパスワードを指定します。 [Azure Key Vault に格納されているシークレットを参照する](store-credentials-in-key-vault.md)ことも選択できます。 <br>Windows 認証の使用時、または基本認証で Key Vault のパスワードを参照するときに適用されます。 | いいえ |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 「[前提条件](#prerequisites)」に記されているように、セルフホステッド統合ランタイムが必要です。 |はい |

**例: 基本認証の使用**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例: Windows 認証の使用**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> 次のペイロードで、.NET Data Provider for Teradata によって動作する、Teradata のリンクされたサービスを使用していた場合、引き続きそのままサポートされますが、今後は新しいものを使用することをお勧めします。

**以前のペイロード:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、Teradata データセット でサポートされるプロパティの一覧を示します。

Teradata からデータをコピーするために、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**TeradataTable** | はい |
| database | Teradata データベースの名前。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |
| table | Teradata データベースのテーブルの名前。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

> [!NOTE]
>
> 次のような "RelationalTable" 型のデータセットを使用していた場合、引き続きそのままサポートされますが、今後は新しいものを使用することをお勧めします。

**以前のペイロード:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Teradata ソースでサポートされるプロパティの一覧を示します。

### <a name="teradata-as-source"></a>ソースとしての Teradata

> [!TIP]
>
> データのパーティション分割を使用して Teradata からデータを効率的に読み込む方法の詳細については、「[Teradata からの並列コピー](#parallel-copy-from-teradata)」セクションを参照してください。

Teradata からデータをコピーするために、コピー アクティビティの **source** セクションでは次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**TeradataSource** | はい |
| query | カスタム SQL クエリを使用してデータを読み取ります。 (例: `"SELECT * FROM MyTable"`)。<br>パーティション分割された読み込みを有効にするときは、クエリ内で対応する組み込みのパーティション パラメーターをフックする必要があります。 例については、「[Teradata からの並列コピー](#parallel-copy-from-teradata)」セクションを参照してください。 | いいえ (データセットのテーブルが指定されている場合) |
| partitionOptions | Teradata からのデータの読み込みに使用されるデータ パーティション分割オプションを指定します。 <br>指定できる値は、**None** (既定値)、**Hash**、**DynamicRange** です。<br>パーティション オプションが有効になっている ("None" ではない) 場合は、コピー アクティビティの **[`parallelCopies`](copy-activity-performance.md#parallel-copy)** の設定も構成してください (たとえば 4)。これにより、Teradata データベースから同時にデータを読み込むときの並列度が決定されます。 | いいえ |
| partitionSettings | データ パーティション分割の設定のグループを指定します。 <br>パーティション オプションが `None` でない場合に適用されます。 | いいえ |
| partitionColumnName | 並列コピーの範囲パーティション分割で使用されるソース列の名前を**整数型で**指定します。 指定しない場合、そのテーブルの主キーが自動的に検出され、パーティション列として使用されます。 <br>パーティション オプションが `Hash` または `DynamicRange` である場合に適用されます。 クエリを使用してソース データを取得する場合は、WHERE 句で `?AdfHashPartitionCondition` または `?AdfRangePartitionColumnName` をフックします。 例については、「[Teradata からの並列コピー](#parallel-copy-from-teradata)」セクションを参照してください。 | いいえ |
| partitionUpperBound | データをコピーするパーティション列の最大値。 <br>パーティション オプションが `DynamicRange` である場合に適用されます。 クエリを使用してソース データを取得する場合は、WHERE 句で `?AdfRangePartitionUpbound` をフックします。 例については、「[Teradata からの並列コピー](#parallel-copy-from-teradata)」セクションを参照してください。 | いいえ |
| PartitionLowerBound | データをコピーするパーティション列の最小値。 <br>パーティション オプションが `DynamicRange` である場合に適用されます。 クエリを使用してソース データを取得する場合は、WHERE 句で `?AdfRangePartitionLowbound` をフックします。 例については、「[Teradata からの並列コピー](#parallel-copy-from-teradata)」セクションを参照してください。 | いいえ |

> [!NOTE]
>
> "RelationalSource" 型のコピー ソースを使用していた場合は、引き続きそのままサポートされますが、新しく組み込まれた Teradata からの並列読み込み (パーティション オプション) はサポートされません。 今後はこの新しいものを使用することをお勧めします。

**例: パーティションなしで基本的なクエリを使用してデータをコピーする**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Teradata からの並列コピー

Data Factory の Teradata コネクタは、優れたパフォーマンスで Teradata からデータを並列でコピーするために、組み込みのデータ パーティション分割を提供します。 データ パーティション分割のオプションは、コピー アクティビティの Teradata ソースにあります。

![パーティション オプション](./media/connector-teradata/connector-teradata-partition-options.png)

パーティション分割されたコピーを有効にすると、Data Factory によって Teradata ソースに対する並列クエリが実行され、パーティションごとにデータが読み込まれます。 並列度は、コピー アクティビティの **[`parallelCopies`](copy-activity-performance.md#parallel-copy)** 設定を使用して構成および制御されます。 たとえば、`parallelCopies` を 4 に設定した場合、Data Factory では、指定したパーティション オプションと設定に基づいて 4 つのクエリが同時に生成され、実行されます。各クエリは、Teradata データベースからデータの一部を取得します。

特に、Teradata データベースから大量のデータを読み込む場合は、データのパーティション分割を使用した並列コピーを有効にすることをお勧めします。 各種シナリオに推奨される構成を以下に示します。

| シナリオ                                                     | 推奨設定                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 大きなテーブル全体を読み込む                                   | **パーティション オプション**: Hash。 <br><br/>実行中に、Data Factory によって自動的に PK 列が検出され、それにハッシュが適用されて、データがパーティションごとにコピーされます。 |
| カスタム クエリを使用して大量のデータを読み込む                 | **パーティション オプション**: Hash。<br>**クエリ**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`。<br>**パーティション列**: ハッシュ パーティションの適用に使用される列を指定します。 指定しない場合は、Teradata データセットで指定したテーブルの PK 列が ADF によって自動的に検出されます。<br><br>実行中に、Data Factory によって `?AdfHashPartitionCondition` がハッシュ パーティション ロジックに置き換えられ、Teradata に送信されます。 |
| カスタム クエリを使用して大量のデータを読み込む (範囲パーティション分割のために値が均等に分散されている整数列がある場合) | **パーティション オプション**: 動的範囲パーティション。<br>**クエリ**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`。<br>**パーティション列**: データのパーティション分割に使用される列を指定します。 整数データ型の列に対してパーティション分割を実行できます。<br>**パーティションの上限**と**パーティション下限**: パーティション列に対してフィルター処理を実行して、下限から上限までの範囲のデータのみを取得する場合に指定します。<br><br>実行中に、Data Factory によって `?AdfRangePartitionColumnName`、`?AdfRangePartitionUpbound`、`?AdfRangePartitionLowbound` が各パーティションの実際の列名および値の範囲に置き換えられ、Teradata に送信されます。 <br>たとえば、パーティション列 "ID" で下限が 1、上限が 80 に設定され、並列コピーが 4 に設定されている場合、ADF によって、ID の範囲が [1,20]、[21, 40]、[41, 60]、[61, 80] の 4 つのパーティションでデータが取得されます。 |

**例: ハッシュ パーティションを使用してクエリを実行する**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**例: 動的範囲パーティションを使用してクエリを実行する**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Teradata のデータ型マッピング

Teradata からデータをコピーするとき、Teradata のデータ型から Azure Data Factory の中間データ型への、以下のマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| Teradata のデータ型 | Data Factory の中間データ型 |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |string |
| Clob |string |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Graphic |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |
| 整数 |Int32 |
| Interval Day |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |
| Interval Day To Hour |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |
| Interval Day To Minute |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |
| Interval Day To Second |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |
| Interval Hour |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |
| Interval Hour To Minute |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |
| Interval Hour To Second |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |
| Interval Minute |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |
| Interval Minute To Second |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |
| Interval Month |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |
| Interval Second |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |
| Interval Year |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |
| Interval Year To Month |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |
| Number |Double |
| Period (Date) |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |
| Period (Time) |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |
| Period (Time With Time Zone) |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |
| Period (Timestamp) |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |
| Period (Timestamp With Time Zone) |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |string |
| VarGraphic |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |
| Xml |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |


## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
