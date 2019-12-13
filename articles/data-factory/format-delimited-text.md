---
title: Azure Data Factory での区切りテキスト形式
description: このトピックでは、Azure Data Factory で区切りテキスト形式を処理する方法について説明します。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 77d2daf3fa17632d8a1c633c23815e0035e45481
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931259"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Azure Data Factory での区切りテキスト形式

**区切りテキスト ファイルの解析または区切りテキスト形式へのデータの書き込み**を行う場合は、この記事に従ってください。 

区切りテキスト形式は次のコネクタでサポートされています。[Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure File Storage](connector-azure-file-storage.md)、[ファイル システム](connector-file-system.md)、[FTP](connector-ftp.md)、[Google Cloud Storage](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md)、[HTTP](connector-http.md)、および [SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、区切りテキスト データセットでサポートされるプロパティの一覧を示します。

| プロパティ         | 説明                                                  | 必須 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | データセットの type プロパティは、**DelimitedText** に設定する必要があります。 | はい      |
| location         | ファイルの場所の設定。 ファイル ベースの各コネクタには、固有の場所の種類と `location` でサポートされるプロパティがあります。  | はい      |
| columnDelimiter  | ファイル内の列を区切るために使用する文字。 現時点では、複数文字の区切り記号はマッピング データ フローについてのみサポートされており、コピー アクティビティではサポートされていません。 <br>既定値は**コンマ `,`** です。列区切り記号が区切り記号なしを意味する空の文字列として定義されている場合は、行全体が 1 つの列と見なされます。 | いいえ       |
| rowDelimiter     | ファイル内の行を区切るために使われる 1 文字または "\r\n"。<br>既定値は、**読み取りでは "\r\n"、"\r"、"\n" のいずれか**、**書き込みでは "\n" または "\r\n"** (それぞれ、マッピング データ フローおよびコピー アクティビティ)。 <br>`rowDelimiter` を区切り記号なし (空の文字列) に設定するときは、`columnDelimiter` も区切り記号なし (空の文字列) に設定する必要があります。これは、コンテンツ全体を 1 つの値として扱うことを意味します。 | いいえ       |
| quoteChar        | 列区切り記号が含まれる場合に列の値を引用符で囲むための 1 文字です。 <br>既定値は**二重引用符** `"` です。 <br>マッピング データ フローの場合は、`quoteChar` を空の文字列にすることはできません。 <br>コピー アクティビティの場合は、`quoteChar` が空の文字列として定義されているときは、引用符がなく、列の値が引用符で囲まれないことを意味し、列区切り記号とそれ自体をエスケープするには `escapeChar` が使われます。 | いいえ       |
| escapeChar       | 引用符で囲まれた値の内部の引用符をエスケープするための 1 文字です。<br>既定値は**バックスラッシュ (円記号) `\`** です。 <br>マッピング データ フローの場合は、`escapeChar` を空の文字列にすることはできません。 <br/>コピー アクティビティの場合は、`escapeChar` が空の文字列として定義されているときは、`quoteChar` も空の文字列として設定する必要があり、その場合は、すべての列の値に区切り記号が含まれないことを確認します。 | いいえ       |
| firstRowAsHeader | 1 行目を列の名前が含まれるヘッダー行として扱うかどうか、またはヘッダー行にするかどうかを指定します。<br>使用できる値は **true** と **false** (既定値) です。 | いいえ       |
| nullValue        | null 値の文字列表現を指定します。 <br>既定値は**空の文字列**です。 | いいえ       |
| encodingName     | テスト ファイルの読み取り/書き込みに使用するエンコードの種類です。 <br>使用できる値は次のとおりです。"UTF-8"、"UTF-16"、"UTF-16BE"、"UTF-32"、"UTF-32BE"、"US-ASCII"、"UTF-7"、"BIG5"、"EUC-JP"、"EUC-KR"、"GB2312"、"GB18030"、"JOHAB"、"SHIFT-JIS"、"CP875"、"CP866"、"IBM00858"、"IBM037"、"IBM273"、"IBM437"、"IBM500"、"IBM737"、"IBM775"、"IBM850"、"IBM852"、"IBM855"、"IBM857"、"IBM860"、"IBM861"、"IBM863"、"IBM864"、"IBM865"、"IBM869"、"IBM870"、"IBM01140"、"IBM01141"、"IBM01142"、"IBM01143"、"IBM01144"、"IBM01145"、"IBM01146"、"IBM01147"、"IBM01148"、"IBM01149"、"ISO-2022-JP"、"ISO-2022-KR"、"ISO-8859-1"、"ISO-8859-2"、"ISO-8859-3"、"ISO-8859-4"、"ISO-8859-5"、"ISO-8859-6"、"ISO-8859-7"、"ISO-8859-8"、"ISO-8859-9"、"ISO-8859-13"、"ISO-8859-15"、"WINDOWS-874"、"WINDOWS-1250"、"WINDOWS-1251"、"WINDOWS-1252"、"WINDOWS-1253"、"WINDOWS-1254"、"WINDOWS-1255"、"WINDOWS-1256"、"WINDOWS-1257"、"WINDOWS-1258"。<br>マッピング データ フローでは UTF-7 エンコードはサポートされないことに注意してください。 | いいえ       |
| compressionCodec | テキスト ファイルの読み取り/書き込みに使用される圧縮コーデックです。 <br>使用できる値は、**bzip2**、**gzip**、**deflate**、**ZipDeflate**、**snappy**、**lz4** です。 ファイルを保存するときに使用します。 <br>現在、コピー アクティビティでは "snappy" と "lz4" はサポートされておらず、マッピング データ フローでは "ZipDeflate" はサポートされていないことに注意してください。 <br>コピー アクティビティを使用して ZipDeflate ファイルを圧縮解除し、ファイルベースのシンク データ ストアに書き込む場合、ファイルは `<path specified in dataset>/<folder named as source zip file>/` フォルダーに抽出されます。 | いいえ       |
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
| skipLineCount | 入力ファイルからのデータ読み取り時にスキップする**空でない**行数を示します。 <br>skipLineCount と firstRowAsHeader の両方を指定した場合は、まず行がスキップされ、次に入力ファイルからヘッダー情報が読み取られます。 | いいえ       |

### <a name="delimited-text-as-sink"></a>シンクとしての区切りテキスト

コピー アクティビティの ***\*sink\**** セクションでは、次のプロパティがサポートされます。

| プロパティ       | 説明                                                  | 必須 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | コピー アクティビティのソースの type プロパティは **DelimitedTextSink** に設定する必要があります。 | はい      |
| formatSettings | プロパティのグループ。 後の**区切りテキストの書き込み設定**に関する表をご覧ください。 |          |
| storeSettings  | データ ストアにデータを書き込む方法を指定するプロパティのグループ。 ファイル ベースの各コネクタには、`storeSettings` に、固有のサポートされる書き込み設定があります。  | いいえ       |

`formatSettings` でサポートされている**区切りテキストの書き込み設定**:

| プロパティ      | 説明                                                  | 必須                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | formatSettings の type は、**DelimitedTextWriteSetting** に設定する必要があります。 | はい                                                   |
| fileExtension | 出力ファイルの名前に使用するファイル拡張子 (`.csv`、`.txt` など)。 出力の DelimitedText データセットで `fileName` が指定されていない場合、指定する必要があります。 | 出力データセットでファイル名が指定されていない場合、はい |

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

Mapping Data Flow の[ソース変換](data-flow-source.md)と[シンク変換](data-flow-sink.md)に関する記事で詳細を確認してください。

## <a name="next-steps"></a>次の手順

- [コピー アクティビティの概要](copy-activity-overview.md)
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
