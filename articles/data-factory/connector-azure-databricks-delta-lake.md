---
title: Azure Databricks Delta Lake との間でデータをコピーする
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、Azure Databricks Delta Lake との間で双方向にデータをコピーする方法について説明します。
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/24/2020
ms.openlocfilehash: bdf71276d59dec9a19e29ae7f49cb92a0512c05a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100364241"
---
# <a name="copy-data-to-and-from-azure-databricks-delta-lake-by-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Databricks Delta Lake をコピー先またはコピー元としてデータをコピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure Databricks Delta Lake をコピー先またはコピー元としてデータをコピーする方法について説明します。 コピー アクティビティの概要が説明されている「[Azure Data Factory のコピー アクティビティ](copy-activity-overview.md)」という記事を基に作成されています。

## <a name="supported-capabilities"></a>サポートされる機能

この Azure Databricks Delta Lake コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)で表の[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

一般に、Azure Data Factory は、さまざまなニーズを満たすために、次の機能を備えた Delta Lake をサポートしています。

- コピー アクティビティでは、サポートされている任意のソース データ ストアから Azure Databricks Delta Lake テーブル、および Delta Lake テーブルからサポートされている任意のシンク データ ストアにデータをコピーするために、Azure Databricks Delta Lake コネクタがサポートされています。 Databricks クラスターを活用してデータ移動を実行します。詳細については、「[前提条件](#prerequisites)」セクションを参照してください。
- [マッピング データ フロー](concepts-data-flow-overview.md)では、ソースおよびシンクとして Azure Storage の汎用[差分形式](format-delta.md)がサポートされています。これにより、コーディング不要の ETL の差分ファイルの読み取りと書き込みが可能になり、マネージド Azure Integration Runtime で実行されます。
- [Databricks アクティビティ](transform-data-databricks-notebook.md)では、Delta Lake 上でコード中心の ETL または機械学習ワークロードを調整することがサポートされています。

## <a name="prerequisites"></a>前提条件

この Azure Databricks Delta Lake コネクタを使用するには、Azure Databricks でクラスターを設定する必要があります。

- Delta Lake にデータをコピーする場合、コピー アクティビティは Azure Databricks クラスターを呼び出して、Azure Storage からデータを読み取ります。これは元のソースまたはステージング領域で、Data Factory が組み込みのステージング コピーを介してソース データを最初に書き込みます。 詳細については、「[シンクとしての Delta Lake](#delta-lake-as-sink)」を参照してください。
- 同様に、Delta Lake からデータをコピーする場合、コピー アクティビティは Azure Databricks クラスターを呼び出して、Azure Storage にデータを書き込みます。これは元のシンクまたはステージング領域で、Data Factory が引き続き組み込みのステージング コピーを介して最終的なシンクにデータを書き込みます。 詳細については、「[ソースとしての Delta Lake](#delta-lake-as-source)」を参照してください。

Databricks クラスターは、Azure Blob または Azure Data Lake Storage Gen2 アカウントにアクセスできる必要があります。これは、ソース/シンク/ステージングに使用されるストレージ コンテナー/ファイル システムと、Data Lake テーブルを書き込むコンテナー/ファイル システムの両方です。

- **Azure Data Lake Storage Gen2** を使用するには、Apache Spark 構成の一部として、Databricks クラスターで **サービス プリンシパル** または **ストレージ アカウント アクセス キー** を構成します。 「[サービス プリンシパルを使用した直接アクセス](/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-with-service-principal-and-oauth-20)」または「[ストレージ アカウント アクセス キーを使用した直接アクセス](/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-using-the-storage-account-access-key)」の手順に従います。

- **Azure Blob Storage** を使用するには、Apache Spark 構成の一部として、Databricks クラスターで **ストレージ アカウント アクセス キー** または **SAS トークン** を構成します。 「[RDD API を使用した Azure Blob Storage へのアクセス](/azure/databricks/data/data-sources/azure/azure-storage#access-azure-blob-storage-using-the-rdd-api)」の手順に従います。

コピー アクティビティの実行中、構成したクラスターが終了した場合は、Data Factory によって自動的に開始されます。 Data Factory オーサリング UI を使用してパイプラインを作成する場合、データのプレビューなどの操作にはライブ クラスターが必要ですが、Data Factory によってクラスターが起動されることはありません。

#### <a name="specify-the-cluster-configuration"></a>クラスター構成の指定

1. **[クラスター モード]** ドロップダウンで、 **[標準]** を選択します。

2. **[Databricks Runtime のバージョン]** ドロップダウンで、Databricks Runtime のバージョンを選択します。

3. [Spark 構成](/azure/databricks/clusters/configure#spark-config)に次のプロパティを追加して、[自動最適化](/azure/databricks/delta/optimizations/auto-optimize)をオンにします。

   ```
   spark.databricks.delta.optimizeWrite.enabled true
   spark.databricks.delta.autoCompact.enabled true
   ```

4. 統合とスケーリングのニーズに応じて、クラスターを構成します。

クラスター構成の詳細については、「[クラスターの構成](/azure/databricks/clusters/configure)」を参照してください。

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Azure Databricks Delta Lake コネクタに固有の Data Factory エンティティを定義するプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure Databricks Delta Lake のリンクされたサービスに対して、次のプロパティがサポートされています。

| プロパティ    | 説明                                                  | 必須 |
| :---------- | :----------------------------------------------------------- | :------- |
| type        | type プロパティは、**AzureDatabricksDeltaLake** に設定する必要があります。 | はい      |
| domain      | Azure Databricks ワークスペースの URL を指定します (例: `https://adb-xxxxxxxxx.xx.azuredatabricks.net`)。 |          |
| clusterId   | 既存のクラスターのクラスター ID を指定します。 これは作成済みの対話型クラスターでなければなりません。 <br>対話型クラスターのクラスター ID は Databricks ワークスペース -> クラスター -> 対話型クラスター名 -> 構成 -> タグで見つけることができます。 [詳細については、こちらを参照してください](/azure/databricks/clusters/configure#cluster-tags)。 |          |
| accessToken | Data Factory の Azure Databricks の認証にはアクセス トークンが必要です。 アクセス トークンは、Databricks ワークスペースから生成する必要があります。 アクセス トークンを見つける詳細な手順については、[こちら](/azure/databricks/dev-tools/api/latest/authentication#generate-token)を参照してください。 |          |
| connectVia  | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたはセルフホステッド統合ランタイムを使用できます (データ ストアがプライベート ネットワークにある場合)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | いいえ       |

**例:**

```json
{
    "name": "AzureDatabricksDeltaLakeLinkedService",
    "properties": {
        "type": "AzureDatabricksDeltaLake",
        "typeProperties": {
            "domain": "https://adb-xxxxxxxxx.xx.azuredatabricks.net",
            "clusterId": "<cluster id>",
            "accessToken": {
                "type": "SecureString", 
                "value": "<access token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 

Azure Databricks Delta Lake データセットでは、次のプロパティがサポートされます。

| プロパティ  | 説明                                                  | 必須                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | データセットの type プロパティには、**AzureDatabricksDeltaLakeDataset** を設定する必要があります。 | はい                         |
| database | データベースの名前です。 |ソースの場合はいいえ、シンクの場合ははい  |
| table | デルタ テーブルの名前。 |ソースの場合はいいえ、シンクの場合ははい  |

**例:**

```json
{
    "name": "AzureDatabricksDeltaLakeDataset",
    "properties": {
        "type": "AzureDatabricksDeltaLakeDataset",
        "typeProperties": {
            "database": "<database name>",
            "table": "<delta table name>"
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

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Azure Databricks Delta Lake のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="delta-lake-as-source"></a>ソースとしての Delta Lake

Azure Databricks Delta Lake からデータをコピーするために、コピー アクティビティの **source** セクションでは次のプロパティがサポートされています。

| プロパティ                     | 説明                                                  | 必須 |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | コピー アクティビティのソースの type プロパティは **AzureDatabricksDeltaLakeSource** を設定する必要があります。 | はい      |
| query          | データを読み取るための SQL クエリを指定します。 タイム トラベル制御については、次のパターンに従います。<br>- `SELECT * FROM events TIMESTAMP AS OF timestamp_expression`<br>- `SELECT * FROM events VERSION AS OF version` | いいえ       |
| exportSettings | デルタ テーブルからデータを取得するために使用される詳細設定。 | いいえ       |
| ***`exportSettings` の下:*** |  |  |
| type | エクスポート コマンドの種類。**AzureDatabricksDeltaLakeExportCommand** に設定します。 | Yes |
| dateFormat | 日付型を日付形式の文字列に書式設定します。 カスタム日付形式は [datetime パターン](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html)の形式に従います。 指定しない場合は、既定値の `yyyy-MM-dd` が使用されます。 | いいえ |
| timestampFormat | タイムスタンプ型をタイムスタンプ形式の文字列に書式設定します。 カスタム日付形式は [datetime パターン](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html)の形式に従います。 指定しない場合は、既定値の `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` が使用されます。 | いいえ |

#### <a name="direct-copy-from-delta-lake"></a>Delta Lake から直接コピーする

シンクのデータ ストアと形式がこのセクションで説明する基準を満たす場合は、コピー アクティビティを使用して、Azure Databricks Delta テーブルからシンクに直接コピーできます。 Data Factory によって設定が確認され、次の条件が満たされない場合は、コピー アクティビティの実行が失敗します。

- **シンクのリンクされたサービス** は、[Azure Blob Storage](connector-azure-blob-storage.md) または [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) です。 アカウントの資格情報は Azure Databricks クラスター構成で事前に構成されている必要があります。詳細については、「[前提条件](#prerequisites)」を参照してください。

- **シンク データ形式** は、次のように構成された **Parquet**、**区切りテキスト**、または **Avro** であり、ファイルではなくフォルダーを指しています。

    - **Parquet** 形式の場合は、圧縮コーデックが **none**、**snappy**、または **gzip** です。
    - **区切りテキスト** 形式の場合:
        - `rowDelimiter` は任意の 1 文字です。
        - `compression` には **none**、**bzip2**、**gzip** を指定できます。
        - `encodingName` UTF-7 はサポートされていません。
    - **Avro** 形式の場合は、圧縮コーデックが **none**、**deflate**、または **snappy** です。

- コピー アクティビティ ソースでは、`additionalColumns` が指定されていません。
- 区切りテキストにデータをコピーする場合、コピー アクティビティ シンクでは、`fileExtension` は ".csv" である必要があります。
- コピー アクティビティのマッピングで、型変換が有効になっていません。

**例:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
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
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-delta-lake"></a>Delta Lake からステージング コピーする

シンクのデータ ストアまたは形式が、前のセクションで説明した直接コピーの基準に適合しない場合は、中間の Azure Storage インスタンスを使用して組み込みのステージング コピーを有効にします。 ステージング コピー機能はスループットも優れています。 Data Factory が、Azure Databricks Delta Lake のデータをステージング ストレージにエクスポートしてから、データをシンクにコピーし、最後にステージング ストレージの一時データをクリーンアップします。 ステージングを使用したデータのコピーの詳細は、「[ステージング コピー](copy-activity-performance-features.md#staged-copy)」を参照してください。

この機能を使うには、中間ステージングとしてストレージ アカウントを参照する [Azure Blob Storage のリンクされたサービス](connector-azure-blob-storage.md#linked-service-properties)または [Azure Data Lake Storage Gen2 のリンクされたサービス](connector-azure-data-lake-storage.md#linked-service-properties)を作成します。 次に、コピー アクティビティに `enableStaging` プロパティと `stagingSettings` プロパティを指定します。

>[!NOTE]
>ステージング ストレージ アカウントの資格情報は Azure Databricks クラスター構成で事前に構成されている必要があります。詳細については、「[前提条件](#prerequisites)」を参照してください。

**例:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
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
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="delta-lake-as-sink"></a>シンクとしての Delta Lake

Azure Databricks Delta Lake にデータをコピーするために、コピー アクティビティの **sink** セクションでは次のプロパティがサポートされています。

| プロパティ      | 説明                                                  | 必須 |
| :------------ | :----------------------------------------------------------- | :------- |
| type          | コピー アクティビティのシンクの type プロパティ。**AzureDatabricksDeltaLakeSink** に設定します。 | はい      |
| preCopyScript | コピー アクティビティの毎回の実行で、データを Databricks Delta テーブルに書き込む前に実行する SQL クエリを指定します。 このプロパティを使用して、事前に読み込まれたデータをクリーンアップしたり、TRUNCATE TABLE ステートメントまたは VACUUM ステートメントを追加したりできます。 | いいえ       |
| importSettings | デルタ テーブルにデータを書き込むために使用される詳細設定。 | いいえ |
| ***`importSettings` の下:*** |                                                              |  |
| type | インポート コマンドの種類。**AzureDatabricksDeltaLakeImportCommand** に設定します。 | Yes |
| dateFormat | 日付形式の文字列を日付型に書式設定します。 カスタム日付形式は [datetime パターン](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html)の形式に従います。 指定しない場合は、既定値の `yyyy-MM-dd` が使用されます。 | いいえ |
| timestampFormat | タイムスタンプ形式の文字列をタイムスタンプ型に書式設定します。 カスタム日付形式は [datetime パターン](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html)の形式に従います。 指定しない場合は、既定値の `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` が使用されます。 | いいえ |

#### <a name="direct-copy-to-delta-lake"></a>Delta Lake に直接コピーする

ソースのデータ ストアと形式がこのセクションで説明する基準を満たす場合は、コピー アクティビティを使用して、ソースから Azure Databricks Delta Lake に直接コピーできます。 Azure Data Factory によって設定が確認され、次の条件が満たされない場合は、コピー アクティビティの実行が失敗します。

- **ソースのリンクされたサービス** は、[Azure Blob Storage](connector-azure-blob-storage.md) または [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) です。 アカウントの資格情報は Azure Databricks クラスター構成で事前に構成されている必要があります。詳細については、「[前提条件](#prerequisites)」を参照してください。

- **ソース データ形式** は、次のように構成された **Parquet**、**区切りテキスト**、または **Avro** であり、ファイルではなくフォルダーを指しています。

    - **Parquet** 形式の場合は、圧縮コーデックが **none**、**snappy**、または **gzip** です。
    - **区切りテキスト** 形式の場合:
        - `rowDelimiter` は既定値または任意の 1 文字です。
        - `compression` には **none**、**bzip2**、**gzip** を指定できます。
        - `encodingName` UTF-7 はサポートされていません。
    - **Avro** 形式の場合は、圧縮コーデックが **none**、**deflate**、または **snappy** です。

- コピー アクティビティ ソース内: 

    - `wildcardFileName` にワイルドカード `*` のみが含まれ、`?` は含まれず、`wildcardFolderName` は指定されていません。
    - `prefix`、`modifiedDateTimeStart`、`modifiedDateTimeEnd`、および `enablePartitionDiscovery` が指定されていない。
    - `additionalColumns` が指定されていません。

- コピー アクティビティのマッピングで、型変換が有効になっていません。

**例:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            }
        }
    }
]
```

#### <a name="staged-copy-to-delta-lake"></a>Delta Lake にステージング コピーする

ソースのデータ ストアまたは形式が、前のセクションで説明した直接コピーの基準に適合しない場合は、中間の Azure Storage インスタンスを使用して組み込みのステージング コピーを有効にします。 ステージング コピー機能はスループットも優れています。 Data Factory は、データ形式の要件を満たすようにデータをステージング ストレージに自動的に変換し、そこから Delta Lake にデータを読み込みます。 最後に、ストレージから一時データがクリーンアップされます。 ステージングを使用したデータのコピーの詳細は、「[ステージング コピー](copy-activity-performance-features.md#staged-copy)」を参照してください。

この機能を使うには、中間ステージングとしてストレージ アカウントを参照する [Azure Blob Storage のリンクされたサービス](connector-azure-blob-storage.md#linked-service-properties)または [Azure Data Lake Storage Gen2 のリンクされたサービス](connector-azure-data-lake-storage.md#linked-service-properties)を作成します。 次に、コピー アクティビティに `enableStaging` プロパティと `stagingSettings` プロパティを指定します。

>[!NOTE]
>ステージング ストレージ アカウントの資格情報は Azure Databricks クラスター構成で事前に構成されている必要があります。詳細については、「[前提条件](#prerequisites)」を参照してください。

**例:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
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

## <a name="monitoring"></a>監視

Azure Data Factory は、他のコネクタと同じ[コピー アクティビティ監視エクスペリエンス](copy-activity-monitoring.md)を提供します。 さらに、Delta Lake との間のデータの読み込みが Azure Databricks クラスターで実行されているため、[詳細なクラスター ログを表示](/azure/databricks/clusters/clusters-manage#--view-cluster-logs)したり、[パフォーマンスを監視](/azure/databricks/clusters/clusters-manage#--monitor-performance)したりすることができます。

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[ルックアップ アクティビティ](control-flow-lookup-activity.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
