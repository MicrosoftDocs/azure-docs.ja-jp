---
title: Snowflake のデータをコピーして変換する
description: Data Factory を使用して、Snowflake のデータをコピーして変換する方法について説明します。
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/16/2021
ms.openlocfilehash: c18a48f8e72c28fd39f839566b18528806e7245d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561585"
---
# <a name="copy-and-transform-data-in-snowflake-by-using-azure-data-factory"></a>Azure Data Factory を使用して Snowflake のデータをコピーして変換する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティを使用して、Snowflake との間でデータをコピーし合い、Data Flow を使用してSnowflake のデータを変換する方法について説明します。 Data Factory の詳細については、[概要の記事](introduction.md)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

この Snowflake コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)で表の[コピー アクティビティ](copy-activity-overview.md)
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

コピー アクティビティの場合、この Snowflake コネクタは次の機能をサポートします。

- Snowflake からのデータのコピー。Snowflake の [COPY into [location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) コマンドを利用して、最適なパフォーマンスを実現します。
- Snowflake へのデータのコピー。Snowflake の [COPY into [table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) コマンドを利用して、最適なパフォーマンスを実現します。 Azure 上の Snowflake がサポートされています。 

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Snowflake コネクタに固有の Data Factory エンティティを定義するプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Snowflake のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ         | 説明                                                  | 必須 |
| :--------------- | :----------------------------------------------------------- | :------- |
| type             | type プロパティは **Snowflake** に設定する必要があります。              | はい      |
| connectionString | Snowflake インスタンスに接続するために必要な情報を指定します。 Azure Key Vault には、パスワードまたは接続文字列全体を格納できます。 詳しくは、表の下の例と、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」の記事をご覧ください。<br><br>いくつかの一般的な設定:<br>- **アカウント名:** Snowflake アカウントの [完全なアカウント名](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name) (リージョンとクラウド プラットフォームを識別する追加のセグメントを含む)。xy12345.east-us-2.azure など。<br/>- **ユーザー名:** 接続に対するユーザーのログイン名。<br>- **パスワード:** ユーザーのパスワードです。<br>- **データベース:** 接続後に使用する既定のデータベース。 指定したロールが特権を持っている既存のデータベースである必要があります。<br>- **ウェアハウス:** 接続後に使用する仮想ウェアハウス。 指定したロールが特権を持っている既存のウェアハウスである必要があります。<br>- **ロール:** Snowflake セッションで使用する既定のアクセス制御ロール。 指定するロールは、指定したユーザーに既に割り当てられている既存のロールである必要があります。 既定のロールは PUBLIC です。 | はい      |
| connectVia       | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたはセルフホステッド統合ランタイムを使用できます (データ ストアがプライベート ネットワークにある場合)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | いいえ       |

**例:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&password=<password>&db=<database>&warehouse=<warehouse>&role=<myRole>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Azure Key Vault 内のパスワード:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>&role=<myRole>",
            "password": {
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 

Snowflake データセットでは、次のプロパティがサポートされます。

| プロパティ  | 説明                                                  | 必須                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | データセットの type プロパティは **SnowflakeTable** に設定する必要があります。 | はい                         |
| schema | スキーマの名前。 スキーマ名は、ADF では大文字と小文字が区別される点に注意してください。 |ソースの場合はいいえ、シンクの場合ははい  |
| table | テーブル/ビューの名前。 テーブル名は、ADF では大文字と小文字が区別される点に注意してください。 |ソースの場合はいいえ、シンクの場合ははい  |

**例:**

```json
{
    "name": "SnowflakeDataset",
    "properties": {
        "type": "SnowflakeTable",
        "typeProperties": {
            "schema": "<Schema name for your Snowflake database>",
            "table": "<Table name for your Snowflake database>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Snowflake のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="snowflake-as-the-source"></a>ソースとしての Snowflake

Snowflake コネクタは、Snowflake の [COPY into [location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) コマンドを利用して、最適なパフォーマンスを実現します。

シンクのデータ ストアと形式が Snowflake COPY コマンドでネイティブにサポートされている場合は、コピー アクティビティを使用して Snowflake からシンクに直接コピーできます。 詳しくは、「[Snowflake から直接コピーする](#direct-copy-from-snowflake)」をご覧ください。 それ以外の場合は、組み込みの [Snowflake からのステージング コピー](#staged-copy-from-snowflake)を使用します。

Snowflake からデータをコピーするために、コピー アクティビティの **source** セクションでは次のプロパティがサポートされています。

| プロパティ                     | 説明                                                  | 必須 |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | コピー アクティビティのソースの type プロパティは **SnowflakeSource** に設定する必要があります。 | はい      |
| query          | Snowflake からデータを読み取る SQL クエリを指定します。 スキーマ、テーブル、および列の名前に小文字が含まれている場合は、クエリでオブジェクト識別子を引用符で囲みます (例: `select * from "schema"."myTable"`)。<br>ストアド プロシージャの実行はサポートされていません。 | いいえ       |
| exportSettings | Snowflake からデータを取得するために使用される詳細設定。 COPY into コマンドでサポートされるものを構成できます。これは、ステートメントを呼び出すときに Data Factory によって渡されます。 | いいえ       |
| ***`exportSettings` の下:*** |  |  |
| type | エクスポート コマンドの type を **SnowflakeExportCopyCommand** に設定します。 | はい |
| additionalCopyOptions | 追加のコピー オプション。キーと値のペアのディクショナリとして指定されます。 例 :MAX_FILE_SIZE、OVERWRITE。 詳細については、「[Snowflake コピー オプション](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions)」を参照してください。 | いいえ |
| additionalFormatOptions | キーと値のペアのディクショナリとして COPY コマンドに指定される、追加のファイル形式オプション。 例 :DATE_FORMAT、TIME_FORMAT、TIMESTAMP_FORMAT。 詳細については、「[Snowflake 形式の種類のオプション](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions)」を参照してください。 | いいえ |

#### <a name="direct-copy-from-snowflake"></a>Snowflake から直接コピーする

シンクのデータ ストアと形式がこのセクションで説明する基準を満たす場合は、コピー アクティビティを使用して、Snowflake からシンクに直接コピーできます。 Data Factory によって設定が確認され、次の条件が満たされない場合は、コピー アクティビティの実行が失敗します。

- **シンクのリンクされたサービス** が、**Shared Access Signature** 認証を使用する [**Azure Blob Storage**](connector-azure-blob-storage.md) です。 サポートされている次の形式で Azure Data Lake Storage Gen2 にデータを直接コピーする場合は、ADLS Gen2 アカウントに対する SAS 認証を使用して Azure BLOB のリンクされたサービスを作成し、[Snowflake からのステージング コピー](#staged-copy-from-snowflake)を使用しないようにすることができます。

- **シンク データ形式** が、次のように構成された **Parquet**、**区切りテキスト**、または **JSON** です。

    - **Parquet** 形式の場合は、圧縮コーデックが **None**、**Snappy**、または **Lzo** です。
    - **区切りテキスト** 形式の場合:
        - `rowDelimiter` が **\r\n** または任意の 1 文字です。
        - `compression` が、**no compression**、**gzip**、**bzip2**、または **deflate** です。
        - `encodingName` が既定値のままか、**utf-8** に設定されている。
        - `quoteChar` が、**double quote**、**single quote**、または **empty string** (引用符なし) です。
    - **JSON** 形式の場合、直接コピーでは、ソースの Snowflake テーブルまたはクエリ結果に 1 つの列しかなく、この列のデータ型が **VARIANT**、**OBJECT**、または **ARRAY** であるケースのみがサポートされます。
        - `compression` が、**no compression**、**gzip**、**bzip2**、または **deflate** です。
        - `encodingName` が既定値のままか、**utf-8** に設定されている。
        - コピー アクティビティのシンクでは、`filePattern` は既定値のままにするか、**setOfObjects** に設定します。
- コピー アクティビティのソースでは、`additionalColumns` は指定しません。
- 列マッピングが指定されていません。

**例:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MYTABLE",
                "exportSettings": {
                    "type": "SnowflakeExportCopyCommand",
                    "additionalCopyOptions": {
                        "MAX_FILE_SIZE": "64000000",
                        "OVERWRITE": true
                    },
                    "additionalFormatOptions": {
                        "DATE_FORMAT": "'MM/DD/YYYY'"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-snowflake"></a>Snowflake からのステージング コピー

シンクのデータ ストアまたは形式が、前のセクションで説明したように Snowflake COPY コマンドとネイティブの互換性がない場合は、中間の Azure Blob Storage インスタンスを使用して組み込みのステージング コピーを有効にします。 ステージング コピー機能はスループットも優れています。 Data Factory が、Snowflake のデータをステージング ストレージにエクスポートしてから、データをシンクにコピーし、最後にステージング ストレージの一時データをクリーンアップします。 ステージングを使用したデータのコピーの詳細は、「[ステージング コピー](copy-activity-performance-features.md#staged-copy)」を参照してください。

この機能を使うには、中間ステージとして、Azure ストレージ アカウントを参照する [Azure Blob Storage のリンクされたサービス](connector-azure-blob-storage.md#linked-service-properties)を作成します。 次に、コピー アクティビティに `enableStaging` プロパティと `stagingSettings` プロパティを指定します。

> [!NOTE]
> ステージングのための Azure Blob Storage のリンクされたサービスは、Snowflake COPY コマンドで必要な、Shared Access Signature 認証を使用する必要があります。 

**例:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="snowflake-as-sink"></a>シンクとしての Snowflake

Snowflake コネクタは、Snowflake の [COPY into [table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) コマンドを利用して、最適なパフォーマンスを実現します。 Azure での Snowflake へのデータの書き込みがサポートされています。

ソースのデータ ストアと形式が Snowflake COPY コマンドでネイティブにサポートされている場合は、コピー アクティビティを使用してソースから Snowflake に直接コピーできます。 詳しくは、「[Snowflake に直接コピーする](#direct-copy-to-snowflake)」をご覧ください。 それ以外の場合は、組み込みの [Snowflake へのステージング コピー](#staged-copy-to-snowflake)を使用します。

Snowflake にデータをコピーするために、コピー アクティビティの **sink** セクションでは次のプロパティがサポートされています。

| プロパティ          | 説明                                                  | 必須                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | コピー アクティビティの sink の type プロパティは、**SnowflakeSink** に設定します。 | はい                                           |
| preCopyScript     | コピー アクティビティの毎回の実行で、データを Snowflake に書き込む前に実行する SQL クエリを指定します。 前に読み込まれたデータをクリーンアップするには、このプロパティを使います。 | いいえ                                            |
| importSettings | Snowflake にデータを書き込むために使用される詳細設定。 COPY into コマンドでサポートされるものを構成できます。これは、ステートメントを呼び出すときに Data Factory によって渡されます。 | いいえ |
| ***`importSettings` の下:*** |                                                              |  |
| type | インポート コマンドの type を **SnowflakeImportCopyCommand** に設定します。 | はい |
| additionalCopyOptions | 追加のコピー オプション。キーと値のペアのディクショナリとして指定されます。 例 :ON_ERROR、FORCE、LOAD_UNCERTAIN_FILES。 詳細については、「[Snowflake コピー オプション](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions)」を参照してください。 | いいえ |
| additionalFormatOptions | キーと値のペアのディクショナリとして COPY コマンドに指定される、追加のファイル形式オプション。 例 :DATE_FORMAT、TIME_FORMAT、TIMESTAMP_FORMAT。 詳細については、「[Snowflake 形式の種類のオプション](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions)」を参照してください。 | いいえ |

#### <a name="direct-copy-to-snowflake"></a>Snowflake に直接コピーする

ソースのデータ ストアと形式がこのセクションで説明する基準を満たす場合は、コピー アクティビティを使用して、ソースから Snowflake に直接コピーできます。 Azure Data Factory によって設定が確認され、次の条件が満たされない場合は、コピー アクティビティの実行が失敗します。

- **ソースのリンクされたサービス** が、**Shared Access Signature** 認証を使用する [**Azure Blob Storage**](connector-azure-blob-storage.md) です。 サポートされている次の形式で Azure Data Lake Storage Gen2 からデータを直接コピーする場合は、ADLS Gen2 アカウントに対する SAS 認証を使用して Azure BLOB のリンクされたサービスを作成し、[Snowflake へのステージング コピー](#staged-copy-to-snowflake)を使用しないようにすることができます。

- **ソース データ形式** が、次のように構成された **Parquet**、**区切りテキスト**、または **JSON** です。

    - **Parquet** 形式の場合は、圧縮コーデックが **None** または **Snappy** です。

    - **区切りテキスト** 形式の場合:
        - `rowDelimiter` が **\r\n** または任意の 1 文字です。 行区切りが "\r\n" でない場合、`firstRowAsHeader` を **false** に指定する必要があります。`skipLineCount` は指定されません。
        - `compression` が、**no compression**、**gzip**、**bzip2**、または **deflate** です。
        - `encodingName` が既定値のままになっているか、"UTF-8"、"UTF-16"、"UTF-16BE"、"UTF-32"、"UTF-32BE"、"BIG5"、"EUC-JP"、"EUC-KR"、"GB18030"、"ISO-2022-JP"、"ISO-2022-KR"、"ISO-8859-1"、"ISO-8859-2"、"ISO-8859-5"、"ISO-8859-6"、"ISO-8859-7"、"ISO-8859-8"、"ISO-8859-9"、"WINDOWS-1250"、"WINDOWS-1251"、"WINDOWS-1252"、"WINDOWS-1253"、"WINDOWS-1254"、"WINDOWS-1255" に設定されています。
        - `quoteChar` が、**double quote**、**single quote**、または **empty string** (引用符なし) です。
    - **JSON** 形式の場合、直接コピーでは、シンクの Snowflake テーブルに 1 つの列しかなく、この列のデータ型が **VARIANT**、**OBJECT**、または **ARRAY** であるケースのみがサポートされます。
        - `compression` が、**no compression**、**gzip**、**bzip2**、または **deflate** です。
        - `encodingName` が既定値のままか、**utf-8** に設定されている。
        - 列マッピングが指定されていません。

- コピー アクティビティ ソース内: 

   -  `additionalColumns` が指定されていません。
   - ソースがフォルダーの場合、`recursive` が true に設定されています。
   - `prefix`、`modifiedDateTimeStart`、`modifiedDateTimeEnd`、および `enablePartitionDiscovery` が指定されていない。

**例:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink",
                "importSettings": {
                    "type": "SnowflakeImportCopyCommand",
                    "copyOptions": {
                        "FORCE": "TRUE",
                        "ON_ERROR": "SKIP_FILE",
                    },
                    "fileFormatOptions": {
                        "DATE_FORMAT": "YYYY-MM-DD",
                    }
                }
            }
        }
    }
]
```

#### <a name="staged-copy-to-snowflake"></a>Snowflake へのステージング コピー

ソース データ ストアまたは形式が、前のセクションで説明したように、Snowflake COPY コマンドとネイティブに互換性がない場合は、中間の Azure Blob Storage インスタンスを使用して、組み込みのステージング コピーを有効にします。 ステージング コピー機能はスループットも優れています。 Data Factory は、Snowflake のデータ形式要件を満たすようにデータを自動的に変換します。 次に、COPY コマンドを呼び出して、Snowflake にデータを読み込みます。 最後に、BLOB ストレージから一時データをクリーンアップします。 ステージングを使用したデータのコピーの詳細は、「[ステージング コピー](copy-activity-performance-features.md#staged-copy)」を参照してください。

この機能を使うには、中間ステージとして、Azure ストレージ アカウントを参照する [Azure Blob Storage のリンクされたサービス](connector-azure-blob-storage.md#linked-service-properties)を作成します。 次に、コピー アクティビティに `enableStaging` プロパティと `stagingSettings` プロパティを指定します。

> [!NOTE]
> ステージングのための Azure Blob Storage のリンクされたサービスは、Snowflake COPY コマンドで必要な、Shared Access Signature 認証を使用する必要があります。

**例:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

マッピング データ フローでデータを変換する場合、Snowflake のテーブルから読み書きすることができます。 詳細については、マッピング データ フローの[ソース変換](data-flow-source.md)と[シンク変換](data-flow-sink.md)に関する記事をご覧ください。 ソースとシンクの種類として、Snowflake データセットまたは[インライン データセット](data-flow-source.md#inline-datasets)を使用することができます。

### <a name="source-transformation"></a>ソース変換

次の表に、Snowflake ソースでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[ソース オプション]** タブで編集できます。コネクタは、Snowflake [内部データ転送](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer)を利用します。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| テーブル | 入力として [テーブル] を選択した場合、データ フローは、Snowflake データセットで指定された、またはインライン データセットを使用するときにソース オプションで指定されたテーブルからすべてのデータをフェッチします。 | いいえ | String | *(インライン データセットのみ)*<br>tableName<br>schemaName |
| クエリ | 入力として [クエリ] を選択した場合は、Snowflake からデータをフェッチするクエリを入力します。 この設定により、データセットで選択したすべてのテーブルがオーバーライドされます。<br>スキーマ、テーブル、および列の名前に小文字が含まれている場合は、クエリでオブジェクト識別子を引用符で囲みます (例: `select * from "schema"."myTable"`)。 | いいえ | String | query |

#### <a name="snowflake-source-script-examples"></a>Snowflake のソース スクリプトの例

ソースの種類として Snowflake データセットを使用すると、関連付けられているデータ フロー スクリプトは次のようになります。

```
source(allowSchemaDrift: true,
    validateSchema: false,
    query: 'select * from MYTABLE',
    format: 'query') ~> SnowflakeSource
```

インライン データセットを使用する場合、関連付けられているデータ フロー スクリプトは次のようになります。

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'query',
    query: 'select * from MYTABLE',
    store: 'snowflake') ~> SnowflakeSource
```

### <a name="sink-transformation"></a>シンク変換

次の表に、Snowflake シンクでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[設定]** タブで編集できます。インライン データセットを使用する場合、「[データセットのプロパティ](#dataset-properties)」セクションで説明されているプロパティと同じ追加の設定が表示されます。 コネクタは、Snowflake [内部データ転送](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer)を利用します。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 更新方法 | 対象となる Snowflake に対して許可される操作を指定します。<br>行を更新、アップサート、または削除するには、それらのアクションに対して行をタグ付けするために[行の変更変換](data-flow-alter-row.md)が必要になります。 | はい | `true` または `false` | deletable <br/>insertable <br/>updateable <br/>upsertable |
| [キー列] | 更新、アップサート、削除の場合、1 つまたは複数のキー列を設定して、変更する行を決定する必要があります。 | いいえ | Array | キー |
| テーブル アクション | 書き込み前に変換先テーブルのすべての行を再作成するか削除するかを指定します。<br>- **なし**: テーブルに対してアクションは実行されません。<br>- **Recreate**:テーブルが削除され、再作成されます。 新しいテーブルを動的に作成する場合に必要です。<br>- **Truncate**:ターゲット テーブルのすべての行が削除されます。 | いいえ | `true` または `false` | recreate<br/>truncate |

#### <a name="snowflake-sink-script-examples"></a>Snowflake シンク スクリプトの例

シンクの種類として Snowflake データセットを使用する際、関連付けられているデータ フロー スクリプトは次のようになります。

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:true,
    insertable:true,
    updateable:true,
    upsertable:false,
    keys:['movieId'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

インライン データセットを使用する場合、関連付けられているデータ フロー スクリプトは次のようになります。

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    format: 'table',
    tableName: 'table',
    schemaName: 'schema',
    deletable: true,
    insertable: true,
    updateable: true,
    upsertable: false,
    store: 'snowflake',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[ルックアップ アクティビティ](control-flow-lookup-activity.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
