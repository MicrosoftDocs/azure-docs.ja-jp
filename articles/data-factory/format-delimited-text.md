---
title: Azure Data Factory での区切りテキスト形式
description: このトピックでは、Azure Data Factory で区切りテキスト形式を処理する方法について説明します。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/10/2020
ms.author: jingwang
ms.openlocfilehash: 81fdb404b99dc5456e9e544b6ff45dff73a7940d
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042838"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Azure Data Factory での区切りテキスト形式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**区切りテキスト ファイルの解析または区切りテキスト形式へのデータの書き込み**を行う場合は、この記事に従ってください。 

区切りテキスト形式は次のコネクタでサポートされています。[Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure File Storage](connector-azure-file-storage.md)、[ファイル システム](connector-file-system.md)、[FTP](connector-ftp.md)、[Google Cloud Storage](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md)、[HTTP](connector-http.md)、および [SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、区切りテキスト データセットでサポートされるプロパティの一覧を示します。

| プロパティ         | 説明                                                  | 必須 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | データセットの type プロパティは、**DelimitedText** に設定する必要があります。 | はい      |
| location         | ファイルの場所の設定。 ファイル ベースの各コネクタには、固有の場所の種類と `location` でサポートされるプロパティがあります。  | はい      |
| columnDelimiter  | ファイル内の列を区切るために使用する文字。 <br>既定値は**コンマ `,`** です。 列区切り記号が区切り記号なしを意味する空の文字列として定義されている場合は、行全体が 1 つの列と見なされます。<br>現時点では、空の文字列または複数文字の列区切り記号はマッピング データ フローについてのみサポートされており、コピー アクティビティではサポートされていません。  | いいえ       |
| rowDelimiter     | ファイル内の行を区切るために使われる 1 文字または "\r\n"。 <br>既定値は、**読み取りでは "\r\n"、"\r"、"\n" のいずれか**、**書き込みでは "\n" または "\r\n"** (それぞれ、マッピング データ フローおよびコピー アクティビティ)。 <br>行区切り記号を区切り記号なし (空の文字列) に設定するときは、列区切り記号も区切り記号なし (空の文字列) に設定する必要があります。これは、コンテンツ全体を 1 つの値として扱うことを意味します。<br>現時点では、空の文字列の行区切り記号はマッピング データ フローについてのみサポートされており、コピー アクティビティではサポートされていません。 | いいえ       |
| quoteChar        | 列区切り記号が含まれる場合に列の値を引用符で囲むための 1 文字です。 <br>既定値は**二重引用符** `"` です。 <br>マッピング データ フローの場合は、`quoteChar` を空の文字列にすることはできません。 <br>コピー アクティビティの場合は、`quoteChar` が空の文字列として定義されているときは、引用符がなく、列の値が引用符で囲まれないことを意味し、列区切り記号とそれ自体をエスケープするには `escapeChar` が使われます。 | いいえ       |
| escapeChar       | 引用符で囲まれた値の内部の引用符をエスケープするための 1 文字です。<br>既定値は**バックスラッシュ (円記号) `\`** です。 <br>マッピング データ フローの場合は、`escapeChar` を空の文字列にすることはできません。 <br/>コピー アクティビティの場合は、`escapeChar` が空の文字列として定義されているときは、`quoteChar` も空の文字列として設定する必要があり、その場合は、すべての列の値に区切り記号が含まれないことを確認します。 | いいえ       |
| firstRowAsHeader | 1 行目を列の名前が含まれるヘッダー行として扱うかどうか、またはヘッダー行にするかどうかを指定します。<br>使用できる値は **true** と **false** (既定値) です。<br>firstRowAsHeader が false の場合、UI データ プレビューと検索アクティビティ出力によって Prop_{n} (0 から開始) という列名が自動生成されることに注意してください。コピー アクティビティでは、ソースからシンクへの[明示的なマッピング](copy-activity-schema-and-type-mapping.md#explicit-mapping)が必要であり、序数 (1 から始まる) とマッピング データ フロー リストによって列が検索され、名前が Column_{n} (1 から始まる) の列が検索されます。  | いいえ       |
| nullValue        | null 値の文字列表現を指定します。 <br>既定値は**空の文字列**です。 | いいえ       |
| encodingName     | テスト ファイルの読み取り/書き込みに使用するエンコードの種類です。 <br>使用できる値は次のとおりです。"UTF-8"、"UTF-16"、"UTF-16BE"、"UTF-32"、"UTF-32BE"、"US-ASCII"、"UTF-7"、"BIG5"、"EUC-JP"、"EUC-KR"、"GB2312"、"GB18030"、"JOHAB"、"SHIFT-JIS"、"CP875"、"CP866"、"IBM00858"、"IBM037"、"IBM273"、"IBM437"、"IBM500"、"IBM737"、"IBM775"、"IBM850"、"IBM852"、"IBM855"、"IBM857"、"IBM860"、"IBM861"、"IBM863"、"IBM864"、"IBM865"、"IBM869"、"IBM870"、"IBM01140"、"IBM01141"、"IBM01142"、"IBM01143"、"IBM01144"、"IBM01145"、"IBM01146"、"IBM01147"、"IBM01148"、"IBM01149"、"ISO-2022-JP"、"ISO-2022-KR"、"ISO-8859-1"、"ISO-8859-2"、"ISO-8859-3"、"ISO-8859-4"、"ISO-8859-5"、"ISO-8859-6"、"ISO-8859-7"、"ISO-8859-8"、"ISO-8859-9"、"ISO-8859-13"、"ISO-8859-15"、"WINDOWS-874"、"WINDOWS-1250"、"WINDOWS-1251"、"WINDOWS-1252"、"WINDOWS-1253"、"WINDOWS-1254"、"WINDOWS-1255"、"WINDOWS-1256"、"WINDOWS-1257"、"WINDOWS-1258"。<br>マッピング データ フローでは UTF-7 エンコードはサポートされないことに注意してください。 | いいえ       |
| compressionCodec | テキスト ファイルの読み取り/書き込みに使用される圧縮コーデックです。 <br>使用できる値は、**bzip2**、**gzip**、**deflate**、**ZipDeflate**、**snappy**、**lz4** です。 既定では圧縮されません。 <br>現在、コピー アクティビティでは "snappy" と "lz4" はサポートされておらず、マッピング データ フローでは "ZipDeflate" はサポートされていないことに**注意**してください。 <br>コピー アクティビティを使用して **ZipDeflate** ファイルを圧縮解除し、ファイルベースのシンク データ ストアに書き込む場合、ファイルは既定で、`<path specified in dataset>/<folder named as source zip file>/` フォルダーに抽出され、[コピー アクティビティのソースの](#delimited-text-as-source) `preserveZipFileNameAsFolder` を使用して、zip ファイル名をフォルダー構造として保持するかどうかが制御されることに**注意**してください。 | いいえ       |
| compressionLevel | 圧縮率です。 <br>使用できる値は、**Optimal** または **Fastest** です。<br>- **Fastest:** 圧縮操作は可能な限り短時間で完了しますが、圧縮後のファイルが最適に圧縮されていない場合があります。<br>- **Optimal**: 圧縮操作で最適に圧縮されますが、操作が完了するまでに時間がかかる場合があります。 詳細については、 [圧縮レベル](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) に関するトピックをご覧ください。 | いいえ       |

Azure Blob Storage の区切りテキスト データセットの例を次に示します。

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "escapeChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、区切りテキストのソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="delimited-text-as-source"></a>ソースとしての区切りテキスト 

コピー アクティビティの ***\*source\**** セクションでは、次のプロパティがサポートされます。

| プロパティ       | 説明                                                  | 必須 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | コピー アクティビティのソースの type プロパティは **DelimitedTextSource** に設定する必要があります。 | はい      |
| formatSettings | プロパティのグループ。 後の**区切りテキストの読み取り設定**に関する表をご覧ください。 | いいえ       |
| storeSettings  | データ ストアからデータを読み取る方法を指定するプロパティのグループ。 ファイル ベースの各コネクタには、`storeSettings` に、固有のサポートされる読み取り設定があります。 | いいえ       |

`formatSettings` でサポートされている**区切りテキストの読み取り設定**:

| プロパティ      | 説明                                                  | 必須 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | formatSettings の type は、**DelimitedTextReadSetting** に設定する必要があります。 | はい      |
| skipLineCount | 入力ファイルからのデータ読み取り時にスキップする**空でない**行数を示します。 <br>skipLineCount と firstRowAsHeader の両方が指定されている場合、行が最初にスキップされ、次に、入力ファイルからヘッダー情報が読まれます。 | いいえ       |
| compressionProperties | 特定の圧縮コーデックのデータを圧縮解除する方法のプロパティ グループ。 | いいえ       |
| preserveZipFileNameAsFolder<br>( *`compressionProperties` の下にあります*) | **ZipDeflate** で入力データセットが圧縮構成されている場合に適用されます。 コピー時にソースの ZIP ファイル名をフォルダー構造として保持するかどうかを指定します。<br>- **true (既定)** に設定した場合、Data Factory は解凍されたファイルを `<path specified in dataset>/<folder named as source zip file>/` に書き込みます。<br>- **false** に設定した場合、Data Factory は解凍されたファイルを `<path specified in dataset>` に直接書き込みます。 競合または予期しない動作を避けるために、異なるソース ZIP ファイルに重複したファイル名がないことを確認します。  | いいえ |

```json
"activities": [
    {
        "name": "CopyFromDelimitedText",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                },
                "formatSettings": {
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 3,
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="delimited-text-as-sink"></a>シンクとしての区切りテキスト

コピー アクティビティの ***\* sink \**** セクションでは、次のプロパティがサポートされます。

| プロパティ       | 説明                                                  | 必須 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | コピー アクティビティのソースの type プロパティは **DelimitedTextSink** に設定する必要があります。 | はい      |
| formatSettings | プロパティのグループ。 後の**区切りテキストの書き込み設定**に関する表をご覧ください。 |          |
| storeSettings  | データ ストアにデータを書き込む方法を指定するプロパティのグループ。 ファイル ベースの各コネクタには、`storeSettings` に、固有のサポートされる書き込み設定があります。  | いいえ       |

`formatSettings` でサポートされている**区切りテキストの書き込み設定**:

| プロパティ      | 説明                                                  | 必須                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | formatSettings の type は、**DelimitedTextWriteSetting** に設定する必要があります。 | はい                                                   |
| fileExtension | 出力ファイルの名前に使用するファイル拡張子 (`.csv`、`.txt` など)。 出力の DelimitedText データセットで `fileName` が指定されていない場合、指定する必要があります。 ファイル名が出力データセット内で構成されている場合は、シンク ファイル名として使用され、ファイル拡張子の設定は無視されます。  | 出力データセットでファイル名が指定されていない場合、はい |

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

マッピング データ フローでは、次のデータ ストアで区切りテキスト形式での読み取りと書き込みを実行できます。[Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。

### <a name="source-properties"></a>ソース プロパティ

次の表に、区切りテキスト ソースでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[ソース オプション]** タブで編集できます。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| ワイルド カードのパス | ワイルドカードのパスに一致するすべてのファイルが処理されます。 データセットで設定されているフォルダーとファイル パスはオーバーライドされます。 | no | String[] | wildcardPaths |
| パーティションのルート パス | パーティション分割されたファイル データについては、パーティション フォルダーを列として読み取るためにパーティションのルート パスを入力できます | no | String | partitionRootPath |
| ファイルの一覧 | 処理するファイルを一覧表示しているテキスト ファイルをソースが指しているかどうか | no | `true` または `false` | fileList |
| 複数行 | ソース ファイルに複数行の行が含まれているか。 複数行の値は引用符で囲む必要があります。 | no `true` または `false` | multiLineRow |
| ファイル名を格納する列 | ソース ファイル名とパスを使用して新しい列を作成します | no | String | rowUrlColumn |
| 完了後 | 処理後にファイルを削除または移動します。 ファイル パスはコンテナー ルートから始まります | no | 削除: `true` または `false` <br> 移動: `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| 最終更新日時でフィルター処理 | 最後に変更された日時に基づいてファイルをフィルター処理する場合に選択 | no | Timestamp | modifiedAfter <br> modifiedBefore |

### <a name="source-example"></a>ソースの例

次の図は、マッピング データ フローにおける区切りテキスト ソースの構成例です。

![DelimitedText ソース](media/data-flow/delimited-text-source.png)

関連付けられているデータ フロー スクリプトは次のとおりです。

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    multiLineRow: true,
    wildcardPaths:['*.csv']) ~> CSVSource
```

### <a name="sink-properties"></a>シンクのプロパティ

次の表に、区切りテキスト シンクでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[設定]** タブで編集できます。

| 名前 | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| フォルダーのクリア | 書き込みの前に宛先フォルダーがクリアされるかどうか | no | `true` または `false` | truncate |
| ファイル名のオプション | 書き込まれたデータの名前付け形式です。 既定では、`part-#####-tid-<guid>` という形式で、パーティションごとに 1 ファイルです | no | パターン:String <br> [Per partition] (パーティションごと): String[] <br> [As data in column] (列内のデータとして): String <br> 1 つのファイルに出力する: `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |
| すべてを引用符で囲む | すべての値を引用符で囲みます | no | `true` または `false` | quoteAll |

### <a name="sink-example"></a>シンクの例

次の図は、マッピング データ フローにおける区切りテキスト シンクの構成例です。

![DelimitedText シンク](media/data-flow/delimited-text-sink.png)

関連付けられているデータ フロー スクリプトは次のとおりです。

```
CSVSource sink(allowSchemaDrift: true,
    validateSchema: false,
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CSVSink
```

## <a name="next-steps"></a>次のステップ

- [コピー アクティビティの概要](copy-activity-overview.md)
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
