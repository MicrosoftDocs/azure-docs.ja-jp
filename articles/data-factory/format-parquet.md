---
title: Azure Data Factory での Parquet 形式
description: このトピックでは、Azure Data Factory で Parquet 形式を処理する方法について説明します。
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/27/2020
ms.author: jingwang
ms.openlocfilehash: a10403b5f26b551458a9e20330bc817512f707de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386393"
---
# <a name="parquet-format-in-azure-data-factory"></a>Azure Data Factory での Parquet 形式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**Parquet ファイルの解析または Parquet 形式でのデータの書き込み** を行う場合は、この記事に従ってください。 

Parquet 形式は次のコネクタでサポートされています。[Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure File Storage](connector-azure-file-storage.md)、[ファイル システム](connector-file-system.md)、[FTP](connector-ftp.md)、[Google Cloud Storage](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md)、[HTTP](connector-http.md)、および [SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Parquet データセットでサポートされるプロパティの一覧を示します。

| プロパティ         | 説明                                                  | 必須 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | データセットの type プロパティは **Parquet** に設定する必要があります。 | はい      |
| location         | ファイルの場所の設定。 ファイル ベースの各コネクタには、固有の場所の種類と `location` でサポートされるプロパティがあります。 **詳細については、コネクタの記事でデータセットのプロパティに関するセクションを参照してください**。 | はい      |
| compressionCodec | Parquet ファイルへの書き込み時に使用する圧縮コーデック。 データ ファクトリーは、Parquet ファイルから読み取るときに、ファイルのメタデータに基づいて圧縮コーデックを自動的に決定します。<br>サポートされている種類は、"**なし**"、"**gzip**"、"**snappy**" (既定値)、および "**lzo**" です。 Parquet ファイルの読み取りおよび書き込みの場合、コピー アクティビティでは現在、LZO がサポートされていないことにご注意ください。 | いいえ       |

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
| type          | コピー アクティビティ シンクの type プロパティには **ParquetSink** を設定する必要があります。 | はい      |
| formatSettings | プロパティのグループ。 後の **Parquet の書き込み設定** に関する表を参照してください。 |    いいえ      |
| storeSettings | データ ストアにデータを書き込む方法を指定するプロパティのグループ。 ファイル ベースの各コネクタには、`storeSettings` に、固有のサポートされる書き込み設定があります。 **詳細については、コネクタの記事でコピー アクティビティのプロパティに関するセクションを参照してください**。 | いいえ       |

`formatSettings` でサポートされている **Parquet 書き込み設定**:

| プロパティ      | 説明                                                  | 必須                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | formatSettings の type は、**ParquetWriteSettings** に設定する必要があります。 | Yes                                                   |
| maxRowsPerFile | データをフォルダーに書き込むとき、複数のファイルに書き込み、ファイルあたりの最大行を指定することを選択できます。  | No |
| fileNamePrefix | `maxRowsPerFile` が構成されている場合に使用されます。<br> データを複数のファイルに書き込むとき、ファイル名のプレフィックスを指定します。結果的に `<fileNamePrefix>_00000.<fileExtension>` のパターンになります。 指定されていない場合、ファイル名プレフィックスは自動生成されます。 このプロパティは、ソースがファイルベース ストアか[パーティション オプション対応データ ストア](copy-activity-performance-features.md)の場合、適用されません。  | いいえ |

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

マッピング データ フローでは、次のデータ ストアで Parquet 形式での読み取りと書き込みを実行できます。[Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。

### <a name="source-properties"></a>ソースのプロパティ

次の表に、Parquet ソースでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[ソース オプション]** タブで編集できます。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | 形式は `parquet` である必要があります | はい | `parquet` | format |
| Wild card paths (ワイルドカード パス) | ワイルドカードのパスに一致するすべてのファイルが処理されます。 データセットで設定されているフォルダーとファイル パスはオーバーライドされます。 | no | String[] | wildcardPaths |
| パーティションのルート パス | パーティション分割されたファイル データについては、パーティション フォルダーを列として読み取るためにパーティションのルート パスを入力できます | no | String | partitionRootPath |
| ファイルの一覧 | 処理するファイルを一覧表示しているテキスト ファイルをソースが指しているかどうか | no | `true` または `false` | fileList |
| ファイル名を格納する列 | ソース ファイル名とパスを使用して新しい列を作成します | no | String | rowUrlColumn |
| 完了後 | 処理後にファイルを削除または移動します。 ファイル パスはコンテナー ルートから始まります | no | 削除: `true` または `false` <br> 移動: `[<from>, <to>]` | purgeFiles <br> moveFiles |
| 最終更新日時でフィルター処理 | 最後に変更された日時に基づいてファイルをフィルター処理する場合に選択 | no | Timestamp | modifiedAfter <br> modifiedBefore |
| [Allow no files found]\(ファイルの未検出を許可\) | true の場合、ファイルが見つからない場合でもエラーはスローされない | no | `true` または `false` | ignoreNoFilesFound |

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

次の表に、Parquet シンクでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[設定]** タブで編集できます。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | 形式は `parquet` である必要があります | はい | `parquet` | format |
| Clear the folder (フォルダーのクリア) | 書き込みの前に宛先フォルダーがクリアされるかどうか | no | `true` または `false` | truncate |
| ファイル名のオプション | 書き込まれたデータの名前付け形式です。 既定では、`part-#####-tid-<guid>` という形式で、パーティションごとに 1 ファイルです | no | パターン:String <br> [Per partition] (パーティションごと): String[] <br> [As data in column] (列内のデータとして): String <br> 1 つのファイルに出力する: `['<fileName>']` | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

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

Parquet 複合データ型 (MAP、LIST、STRUCT など) は、現在、コピー アクティビティではなくデータ フローでのみサポートされています。 データ フローで複合型を使用するには、データセットにファイル スキーマをインポートしないで、データセット内のスキーマを空白のままにしておきます。 次に、ソース変換で、プロジェクションをインポートします。

## <a name="using-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムの使用

> [!IMPORTANT]
> オンプレミスとクラウドのデータ ストアの間など、セルフホステッド統合ランタイムを利用したコピーでは、Parquet ファイルを **そのまま** コピーしない場合は、**64-bit JRE 8 (Java Runtime Environment) または OpenJDK** と **Microsoft Visual C++ 2010 再頒布可能パッケージ** を IR マシンにインストールする必要があります。 詳細については、次の段落をご確認ください。

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
