---
title: Azure Data Factory での Parquet 形式
description: このトピックでは、Azure Data Factory で Parquet 形式を処理する方法について説明します。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: jingwang
ms.openlocfilehash: 9ad0ccdabd0320d8821d0760ca9802db37049149
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84611044"
---
# <a name="parquet-format-in-azure-data-factory"></a>Azure Data Factory での Parquet 形式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**Parquet ファイルの解析または Parquet 形式でのデータの書き込み**を行う場合は、この記事に従ってください。 

Parquet 形式は次のコネクタでサポートされています。[Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure File Storage](connector-azure-file-storage.md)、[ファイル システム](connector-file-system.md)、[FTP](connector-ftp.md)、[Google Cloud Storage](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md)、[HTTP](connector-http.md)、および [SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Parquet データセットでサポートされるプロパティの一覧を示します。

| プロパティ         | 説明                                                  | 必須 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | データセットの type プロパティは **Parquet** に設定する必要があります。 | はい      |
| location         | ファイルの場所の設定。 ファイル ベースの各コネクタには、固有の場所の種類と `location` でサポートされるプロパティがあります。 **詳細については、コネクタの記事でデータセットのプロパティに関するセクションを参照してください**。 | はい      |
| compressionCodec | Parquet ファイルへの書き込み時に使用する圧縮コーデック。 データ ファクトリーは、Parquet ファイルから読み取るときに、ファイルのメタデータに基づいて圧縮コーデックを自動的に決定します。<br>サポートされている種類は、"**なし**"、"**gzip**"、"**snappy**" (既定)、および "**lzo**" です。 Parquet ファイルの読み取りおよび書き込みの場合、コピー アクティビティでは現在、LZO がサポートされていないことにご注意ください。 | いいえ       |

> [!NOTE]
> Parquet ファイルでは、列名に空白文字はサポートされません。

Azure Blob Storage の Parquet データセットの例を次に示します。

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Parquet のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="parquet-as-source"></a>ソースとしての Parquet

コピー アクティビティの ***\*source\**** セクションでは、次のプロパティがサポートされます。

| プロパティ      | 説明                                                  | 必須 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | コピー アクティビティのソースの type プロパティは **ParquetSource** に設定する必要があります。 | はい      |
| storeSettings | データ ストアからデータを読み取る方法を指定するプロパティのグループ。 ファイル ベースの各コネクタには、`storeSettings` に、固有のサポートされる読み取り設定があります。 **詳細については、コネクタの記事でコピー アクティビティのプロパティに関するセクションを参照してください**。 | いいえ       |

### <a name="parquet-as-sink"></a>シンクとしての Parquet

コピー アクティビティの ***\* sink \**** セクションでは、次のプロパティがサポートされます。

| プロパティ      | 説明                                                  | 必須 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | コピー アクティビティのソースの type プロパティは **ParquetSink** に設定する必要があります。 | はい      |
| storeSettings | データ ストアにデータを書き込む方法を指定するプロパティのグループ。 ファイル ベースの各コネクタには、`storeSettings` に、固有のサポートされる書き込み設定があります。 **詳細については、コネクタの記事でコピー アクティビティのプロパティに関するセクションを参照してください**。 | いいえ       |

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

マッピング データ フローでは、次のデータ ストアで Parquet 形式での読み取りと書き込みを実行できます。[Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。

### <a name="source-properties"></a>ソースのプロパティ

次の表に、Parquet ソースでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[ソース オプション]** タブで編集できます。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | 形式は `parquet` である必要があります | はい | `parquet` | format |
| Wild card paths (ワイルドカード パス) | ワイルドカード パスに一致するすべてのファイルが処理されます。 データセットで設定されているフォルダーとファイル パスを上書きします。 | no | String[] | wildcardPaths |
| Partition root path (パーティション ルート パス) | パーティション分割されたファイル データについては、パーティション分割されたフォルダーを列として読み取るためにパーティション ルート パスを入力できます。 | no | String | partitionRootPath |
| List of files (ファイルの一覧) | 処理するファイルを一覧表示しているテキスト ファイルをソースが指しているかどうか | no | `true` または `false` | fileList |
| Column to store file name (ファイル名を格納する列) | ソース ファイル名とパスを使用して新しい列を作成します | no | String | rowUrlColumn |
| After completion (完了後) | 処理後にファイルを削除または移動します。 ファイル パスはコンテナー ルートから始まります | no | 削除: `true` または `false` <br> 移動: `[<from>, <to>]` | purgeFiles <br> moveFiles |
| Filter by last modified (最終更新日時でフィルター処理) | 最後に変更された日時に基づいてファイルをフィルター処理することを選択します | no | Timestamp | modifiedAfter <br> modifiedBefore |

### <a name="source-example"></a>ソースの例

次の図は、マッピング データ フローにおける Parquet ソースの構成例です。

![Parquet ソース](media/data-flow/parquet-source.png)

関連付けられているデータ フロー スクリプトは次のとおりです。

```
source(allowSchemaDrift: true,
    validateSchema: false,
    rowUrlColumn: 'fileName',
    format: 'parquet') ~> ParquetSource
```

### <a name="sink-properties"></a>シンクのプロパティ

次の表に、Parquet ソースでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[ソース オプション]** タブで編集できます。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | 形式は `parquet` である必要があります | はい | `parquet` | format |
| Clear the folder (フォルダーのクリア) | 書き込みの前に宛先フォルダーをクリアするかどうか | no | `true` または `false` | truncate |
| File name option (ファイル名オプション) | 書き込まれたデータの名前付け形式です。 既定では、`part-#####-tid-<guid>` という形式で、パーティションごとに 1 ファイルです | no | パターン:String <br> [Per partition] (パーティションごと): String[] <br> [As data in column] (列内のデータとして): String <br> Output to a single file (1 つのファイルに出力する): `['<fileName>']` | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="sink-example"></a>シンクの例

次の図は、マッピング データ フローにおける Parquet シンクの構成例です。

![Parquet シンク](media/data-flow/parquet-sink.png)

関連付けられているデータ フロー スクリプトは次のとおりです。

```
ParquetSource sink(
    format: 'parquet',
    filePattern:'output[n].parquet',
    truncate: true,
    allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> ParquetSink
```

## <a name="data-type-support"></a>データ型のサポート

Parquet の複合データ型は、現在サポートされていません (MAP、LIST、STRUCT など)。

## <a name="using-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムの使用

> [!IMPORTANT]
> オンプレミスとクラウドのデータ ストアの間など、セルフホステッド統合ランタイムを利用したコピーでは、Parquet ファイルを **そのまま**コピーしない場合は、**64-bit JRE 8 (Java Runtime Environment) または OpenJDK** と **Microsoft Visual C++ 2010 再頒布可能パッケージ** を IR マシンにインストールする必要があります。 詳細については、次の段落をご確認ください。

Parquet ファイルのシリアル化/逆シリアル化を使用してセルフホステッド IR 上で実行されるコピーでは、ADF は最初に JRE のレジストリ *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* を調べ、見つからない場合は次に OpenJDK のシステム変数 *`JAVA_HOME`* を調べることで、Java ランタイムを見つけます。

- **JRE を使用する場合**:64 ビット IR には 64 ビット JRE が必要です。 [こちら](https://go.microsoft.com/fwlink/?LinkId=808605)から入手できます。
- **OpenJDK の使用方法**:IR バージョン 3.13 以降でサポートされています。 jvm.dll を他のすべての必要な OpenJDK のアセンブリと共にセルフホステッド IR マシンにパッケージ化し、それに応じてシステム環境変数 JAVA_HOME を設定します。
- **Microsoft Visual C++ 2010 再頒布可能パッケージのインストール方法**:Visual C++ 2010 再頒布可能パッケージは、セルフホステッド IR インストールではインストールされません。 [こちら](https://www.microsoft.com/download/details.aspx?id=14632)から入手できます。

> [!TIP]
> セルフホステッド統合ランタイムを使用して、 Parquet 形式をコピー元またはコピー先にしてデータをコピーしたときに、[An error occurred when invoking java, message: **java.lang.OutOfMemoryError:Java heap space** (java の呼び出し中にエラーが発生しました。メッセージ: java.lang.OutOfMemoryError:Java heap space)] というエラーが発生する場合は、まず、セルフホステッド IR のホストであるマシン内に環境変数 `_JAVA_OPTIONS` を追加してください。次に、JVM の最小/最大ヒープ サイズを調整し、コピーを行えるようにしてから、パイプラインを再実行してください。

![セルフホステッド IR 上での JVM ヒープ サイズの設定](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

例: 変数 `_JAVA_OPTIONS` を設定して、値 `-Xms256m -Xmx16g` を指定します。 フラグ `Xms` では、Java 仮想マシン (JVM) の初期メモリ割り当てプールを指定します。`Xmx` では、最大メモリ割り当てプールを指定します。 これは、JVM 起動時のメモリ量が `Xms`、使用可能なメモリ量が最大で `Xmx` であることを意味します。 既定では、ADF では、最小で 64MB、最大で 1G が使用されます。

## <a name="next-steps"></a>次のステップ

- [コピー アクティビティの概要](copy-activity-overview.md)
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
