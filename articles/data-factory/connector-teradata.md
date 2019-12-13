---
title: Azure Data Factory を使用して Teradata Vantage からデータをコピーする
description: Data Factory サービスの Teradata コネクタを使用すると、Teradata Vantage から、Data Factory によってサポートされているデータ ストアに、シンクとしてデータをコピーすることができます。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 5a41d5653de0d8a9f674009904756892ac343609
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930917"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Azure Data Factory を使用して Teradata Vantage からデータをコピーする
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
>
> * [Version 1](v1/data-factory-onprem-teradata-connector.md)
> * [現在のバージョン](connector-teradata.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、Teradata Vantage からデータをコピーする方法について説明します。 これは、[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

この Teradata コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

Teradata Vantage から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この Teradata コネクタは以下をサポートします。

- Teradata **バージョン 14.10、15.0、15.10、16.0、16.10、16.20**。
- **基本**認証または **Windows** 認証を使用したデータのコピー。
- Teradata ソースからの並列コピー。 詳細については、「[Teradata からの並列コピー](#parallel-copy-from-teradata)」セクションを参照してください。

> [!NOTE]
>
> セルフホステッド統合ランタイム v3.18 のリリースの後、Azure Data Factory で Teradata コネクタのアップグレードが行われました。 以前の Teradata コネクタを使用している既存のワークロードは、引き続きサポートされます。 ただし、新しいワークロードには、新しいものを使用することをお勧めします。 新しいパスには、リンクされたサービス、データセット、およびコピー ソースの別のセットが必要であることに注意してください。 構成の詳細については、以降の各セクションを参照してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

この統合ランタイムには、バージョン3.18 以降、組み込みの Teradata ドライバーが用意されています。 ドライバーを手動でインストールする必要はありません。 このドライバーでは、セルフホステッド統合ランタイム マシンに "Visual C++ 再頒布可能パッケージ 2012 Update 4" が必要です。 まだインストールしていない場合は、[こちら](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)からダウンロードしてください。

セルフホステッド統合ランタイムのバージョンが 3.18 より前の場合は、統合ランタイム マシンに [.NET Data Provider for Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) バージョン 14 以降をインストールしてください。 

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Teradata コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Teradata のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **Teradata** に設定する必要があります。 | はい |
| connectionString | Teradata インスタンスに接続するために必要な情報を指定します。 以下のサンプルを参照してください。<br/>パスワードを Azure Key Vault に格納して、接続文字列から `password` 構成をプルすることもできます。 詳細については、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」を参照してください。 | はい |
| username | Teradata に接続するユーザー名を指定します。 Windows 認証の使用時に適用されます。 | いいえ |
| password | ユーザー名に指定したユーザー アカウントのパスワードを指定します。 [Azure Key Vault に格納されているシークレットを参照する](store-credentials-in-key-vault.md)ことも選択できます。 <br>Windows 認証の使用時、または基本認証で Key Vault のパスワードを参照するときに適用されます。 | いいえ |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |はい |

接続文字列には他にも、ケースに応じてさまざまな接続プロパティを設定できます。それらのプロパティを次に示します。

| プロパティ | 説明 | 既定値 |
|:--- |:--- |:--- |
| 文字セット | セッションに使用する文字セット。 例: `CharacterSet=UTF16`。<br><br/>この値には、ユーザー定義の文字セットのほか、次のいずれかの定義済みの文字セットを指定できます。 <br/>- ASCII<br/>- UTF8<br/>- UTF16<br/>- LATIN1252_0A<br/>- LATIN9_0A<br/>- LATIN1_0A<br/>- Shift-JIS (Windows、DOS 互換、KANJISJIS_0S)<br/>- EUC (Unix 互換、KANJIEC_0U)<br/>- IBM Mainframe (KANJIEBCDIC5035_0I)<br/>- KANJI932_1S0<br/>- BIG5 (TCHBIG5_1R0)<br/>- GB (SCHGB2312_1T0)<br/>- SCHINESE936_6R0<br/>- TCHINESE950_8R0<br/>- NetworkKorean (HANGULKSC5601_2R4)<br/>- HANGUL949_7R0<br/>- ARABIC1256_6A0<br/>- CYRILLIC1251_2A0<br/>- HEBREW1255_5A0<br/>- LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- LATIN1258_8A0<br/>- THAI874_4A0 | 既定値は `ASCII` です。 |
| MaxRespSize |SQL 要求の応答バッファーの最大サイズ。単位はキロバイト (KB) です。 例: `MaxRespSize=‭10485760‬`。<br/><br/>Teradata Database バージョン 16.00 以降では、7361536 が最大値となります。 それより前のバージョンを使用する接続の場合、最大値は 1048576 です。 | 既定値は `65536` です。 |

**基本認証を使用した例**

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

**Windows 認証を使用した例**

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
> 次のペイロードは引き続きサポートされます。 しかし、今後は新しいものを使用する必要があります。

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

このセクションでは、Teradata データセットでサポートされるプロパティの一覧を示します。 データセットの定義に使用できるセクションとプロパティの一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。

Teradata からデータをコピーするために、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは `TeradataTable` に設定する必要があります。 | はい |
| database | Teradata インスタンスの名前。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |
| table | Teradata インスタンスのテーブルの名前。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> type が `RelationalTable` のデータセットは引き続きサポートされます。 ただし、新しいデータセットを使用することをお勧めします。

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

このセクションでは、Teradata ソースでサポートされるプロパティの一覧を示します。 アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関するページを参照してください。 

### <a name="teradata-as-source"></a>ソースとしての Teradata

>[!TIP]
>データのパーティション分割を使用して Teradata からデータを効率的に読み込む方法の詳細については、「[Teradata からの並列コピー](#parallel-copy-from-teradata)」セクションを参照してください。

Teradata からデータをコピーするために、コピー アクティビティの **source** セクションでは次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは `TeradataSource` に設定する必要があります。 | はい |
| query | カスタム SQL クエリを使用してデータを読み取ります。 例: `"SELECT * FROM MyTable"`。<br>パーティション分割された読み込みを有効にするときは、クエリ内で対応する組み込みのパーティション パラメーターをすべてフックする必要があります。 例については、「[Teradata からの並列コピー](#parallel-copy-from-teradata)」セクションを参照してください。 | いいえ (データセットのテーブルが指定されている場合) |
| partitionOptions | Teradata からのデータの読み込みに使用されるデータ パーティション分割オプションを指定します。 <br>指定できる値は、**None** (既定値)、**Hash**、**DynamicRange** です。<br>パーティション オプションが有効になっている場合 (つまり、`None` ではない場合)、Teradata から同時にデータを読み込む並列処理の次数は、コピー アクティビティの [`parallelCopies`](copy-activity-performance.md#parallel-copy) の設定によって制御されます。 | いいえ |
| partitionSettings | データ パーティション分割の設定のグループを指定します。 <br>パーティション オプションが `None` でない場合に適用されます。 | いいえ |
| partitionColumnName | 並列コピーの範囲パーティションまたはハッシュ パーティションで使用されるソース列の名前を指定します。 指定されていない場合は、テーブルのプライマリ インデックスが自動検出され、パーティション列として使用されます。 <br>パーティション オプションが `Hash` または `DynamicRange` である場合に適用されます。 クエリを使用してソース データを取得する場合は、WHERE 句で `?AdfHashPartitionCondition` または `?AdfRangePartitionColumnName` をフックします。 例については、「[Teradata からの並列コピー](#parallel-copy-from-teradata)」セクションを参照してください。 | いいえ |
| partitionUpperBound | データをコピーするパーティション列の最大値。 <br>パーティション オプションが `DynamicRange` である場合に適用されます。 クエリを使用してソース データを取得する場合は、WHERE 句で `?AdfRangePartitionUpbound` をフックします。 例については、「[Teradata からの並列コピー](#parallel-copy-from-teradata)」セクションを参照してください。 | いいえ |
| partitionLowerBound | データをコピーするパーティション列の最小値。 <br>パーティション オプションが `DynamicRange` である場合に適用されます。 クエリを使用してソース データを取得する場合は、WHERE 句で `?AdfRangePartitionLowbound` をフックします。 例については、「[Teradata からの並列コピー](#parallel-copy-from-teradata)」セクションを参照してください。 | いいえ |

> [!NOTE]
>
> type が `RelationalSource` のコピー ソースは引き続きサポートされますが、これは、新しく組み込まれた Teradata からの並列読み込み (パーティション オプション) をサポートしません。 ただし、新しいデータセットを使用することをお勧めします。

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

Data Factory の Teradata コネクタは、Teradata からデータを並列でコピーするために、組み込みのデータ パーティション分割を提供します。 データ パーティション分割オプションは、コピー アクティビティの **[ソース]** テーブルにあります。

![パーティションのオプションのスクリーンショット](./media/connector-teradata/connector-teradata-partition-options.png)

パーティション分割されたコピーを有効にすると、Data Factory によって Teradata ソースに対する並列クエリが実行され、パーティションごとにデータが読み込まれます。 並列度は、コピー アクティビティの [`parallelCopies`](copy-activity-performance.md#parallel-copy) 設定によって制御されます。 たとえば、`parallelCopies` を 4 に設定した場合、Data Factory では、指定したパーティション オプションと設定に基づいて 4 つのクエリが同時に生成され、実行されます。各クエリでは、Teradata からデータの一部が取得されます。

特に、Teradata から大量のデータを読み込む場合は、データのパーティション分割を使用した並列コピーを有効にすることをお勧めします。 さまざまなシナリオの推奨構成を以下に示します。 ファイルベースのデータ ストアにデータをコピーする場合は、複数のファイルとしてフォルダーに書き込む (フォルダー名のみを指定する) ことをお勧めします。この場合、1 つのファイルに書き込むよりもパフォーマンスが優れています。

| シナリオ                                                     | 推奨設定                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 大きなテーブル全体を読み込む。                                   | **パーティション オプション**: Hash。 <br><br/>実行中に、Data Factory によって自動的に PK 列が検出され、それにハッシュが適用されて、データがパーティションごとにコピーされます。 |
| カスタム クエリを使用して大量のデータを読み込む。                 | **パーティション オプション**: Hash。<br>**クエリ**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`。<br>**パーティション列**: ハッシュ パーティションの適用に使用される列を指定します。 指定されていない場合は、Teradata データセットで指定したテーブルの PK 列が Data Factory によって自動検出されます。<br><br>実行中に、Data Factory によって `?AdfHashPartitionCondition` がハッシュ パーティション ロジックに置き換えられ、Teradata に送信されます。 |
| カスタム クエリを使用して大量のデータを読み込む (範囲パーティション分割のために値が均等に分散されている整数列がある場合)。 | **パーティション オプション**: 動的範囲パーティション。<br>**クエリ**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`<br>**パーティション列**: データのパーティション分割に使用される列を指定します。 整数データ型の列に対してパーティション分割を実行できます。<br>**パーティションの上限**と**パーティションの下限**: パーティション列に対してフィルター処理を実行して、下限から上限までの範囲内のデータのみを取得する場合に指定します。<br><br>実行中に、Data Factory によって `?AdfRangePartitionColumnName`、`?AdfRangePartitionUpbound`、`?AdfRangePartitionLowbound` が各パーティションの実際の列名および値の範囲に置き換えられ、Teradata に送信されます。 <br>たとえば、パーティション列 "ID" で下限が 1、上限が 80 に設定され、並列コピーが 4 に設定されている場合、Data Factory は 4 つのパーティションでデータを取得します。 これらの ID の範囲はそれぞれ [1, 20]、[21, 40]、[41, 60]、[61, 80] です。 |

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

Teradata からデータをコピーするときには、次のマッピングが適用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| Teradata のデータ型 | Data Factory の中間データ型 |
|:--- |:--- |
| BigInt |Int64 |
| BLOB |Byte[] |
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
| xml |サポートされていません。 ソース クエリで明示的なキャストを適用します。 |


## <a name="lookup-activity-properties"></a>ルックアップ アクティビティのプロパティ

プロパティの詳細については、[ルックアップ アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。


## <a name="next-steps"></a>次の手順
Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
