---
title: Azure Data Factory での XML 形式
description: このトピックでは、Azure Data Factory で XML 形式を処理する方法について説明します。
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: a4082e1e00ecb1d3b0157fcc35616704eaab0aae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386358"
---
# <a name="xml-format-in-azure-data-factory"></a>Azure Data Factory での XML 形式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**XML ファイルを解析する場合** は、この記事に従ってください。 

XML 形式は、以下のコネクタでサポートされています。[Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure File Storage](connector-azure-file-storage.md)、[ファイル システム](connector-file-system.md)、[FTP](connector-ftp.md)、[Google Cloud Storage](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md)、[HTTP](connector-http.md)、および [SFTP](connector-sftp.md)。 これはソースとしてはサポートされますが、シンクとしてはサポートされません。

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、XML データセットでサポートされるプロパティの一覧を示します。

| プロパティ         | 説明                                                  | 必須 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | データセットの type プロパティは、**Xml** に設定する必要があります。 | はい      |
| location         | ファイルの場所の設定。 ファイル ベースの各コネクタには、固有の場所の種類と `location` でサポートされるプロパティがあります。 **詳細については、コネクタの記事でデータセットのプロパティに関するセクションを参照してください**。 | はい      |
| encodingName     | テスト ファイルの読み取り/書き込みに使用するエンコードの種類です。 <br>使用できる値は次のとおりです。"UTF-8"、"UTF-16"、"UTF-16BE"、"UTF-32"、"UTF-32BE"、"US-ASCII"、"UTF-7"、"BIG5"、"EUC-JP"、"EUC-KR"、"GB2312"、"GB18030"、"JOHAB"、"SHIFT-JIS"、"CP875"、"CP866"、"IBM00858"、"IBM037"、"IBM273"、"IBM437"、"IBM500"、"IBM737"、"IBM775"、"IBM850"、"IBM852"、"IBM855"、"IBM857"、"IBM860"、"IBM861"、"IBM863"、"IBM864"、"IBM865"、"IBM869"、"IBM870"、"IBM01140"、"IBM01141"、"IBM01142"、"IBM01143"、"IBM01144"、"IBM01145"、"IBM01146"、"IBM01147"、"IBM01148"、"IBM01149"、"ISO-2022-JP"、"ISO-2022-KR"、"ISO-8859-1"、"ISO-8859-2"、"ISO-8859-3"、"ISO-8859-4"、"ISO-8859-5"、"ISO-8859-6"、"ISO-8859-7"、"ISO-8859-8"、"ISO-8859-9"、"ISO-8859-13"、"ISO-8859-15"、"WINDOWS-874"、"WINDOWS-1250"、"WINDOWS-1251"、"WINDOWS-1252"、"WINDOWS-1253"、"WINDOWS-1254"、"WINDOWS-1255"、"WINDOWS-1256"、"WINDOWS-1257"、"WINDOWS-1258"。| いいえ       |
| nullValue | null 値の文字列表現を指定します。<br/>既定値は **空の文字列** です。 | いいえ |
| compression | ファイル圧縮を構成するためのプロパティのグループ。 アクティビティの実行中に圧縮/圧縮解除を行う場合は、このセクションを構成します。 | いいえ |
| type<br>( *`compression` の下にあります*) | XML ファイルの読み取り/書き込みに使用される圧縮コーデックです。 <br>使用できる値は、**bzip2**、**gzip**、**deflate**、**ZipDeflate**、**TarGzip**、**Tar**、**snappy**、または **lz4** です。 既定では圧縮されません。<br>現在、Copy アクティビティでは "snappy" と "lz4" がサポートされておらず、マッピング データ フローでは "ZipDeflate"、"TarGzip"、"Tar" がサポートされていないことに **注意してください**。<br>コピー アクティビティを使用して **ZipDeflate**/**TarGzip**/**Tar** ファイルを圧縮解除し、ファイルベースのシンク データ ストアに書き込む場合、ファイルは既定で `<path specified in dataset>/<folder named as source compressed file>/` フォルダーに解凍されることに **注意** してください。圧縮ファイル名をフォルダー構造として保持するかどうかを制御するには、[コピー アクティビティのソース](#xml-as-source)に対して `preserveZipFileNameAsFolder`/`preserveCompressionFileNameAsFolder` を使用します。 | いいえ。  |
| level<br/>( *`compression` の下にあります*) | 圧縮率です。 <br>使用できる値は、**Optimal** または **Fastest** です。<br>- **Fastest:** 圧縮操作は可能な限り短時間で完了しますが、圧縮後のファイルが最適に圧縮されていない場合があります。<br>- **Optimal**: 圧縮操作で最適に圧縮されますが、操作が完了するまでに時間がかかる場合があります。 詳細については、 [圧縮レベル](/dotnet/api/system.io.compression.compressionlevel) に関するトピックをご覧ください。 | いいえ       |

Azure Blob Storage 上の XML データセットの例を次に示します。

```json
{
    "name": "XMLDataset",
    "properties": {
        "type": "Xml",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、XML ソースでサポートされるプロパティの一覧を示します。

[スキーマ マッピング](copy-activity-schema-and-type-mapping.md)から XML データとシンク データ ストア/形式をマップする方法について説明します。 XML ファイルをプレビューするときは、データは JSON 階層で表示され、JSON パスを使用してフィールドを参照します。

### <a name="xml-as-source"></a>ソースとしての XML

コピー アクティビティの ***\*source\**** セクションでは、次のプロパティがサポートされます。 詳細については、「[XML コネクタの動作](#xml-connector-behavior)」を参照してください。

| プロパティ      | 説明                                                  | 必須 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | コピー アクティビティのソースの type プロパティを **XmlSource** に設定する必要があります。 | はい      |
| formatSettings | プロパティのグループ。 後の **XML の読み取り設定** に関する表を参照してください。 | いいえ       |
| storeSettings | データ ストアからデータを読み取る方法を指定するプロパティのグループ。 ファイル ベースの各コネクタには、`storeSettings` に、固有のサポートされる読み取り設定があります。 **詳細については、コネクタの記事でコピー アクティビティのプロパティに関するセクションを参照してください**。 | いいえ       |

`formatSettings` でサポートされている **XML の読み取り設定**:

| プロパティ      | 説明                                                  | 必須 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | formatSettings の type は、**XmlReadSettings** に設定する必要があります。 | はい      |
| validationMode | XML スキーマを検証するかどうかを指定します。<br>使用できる値は、**none** (既定値、検証なし)、**xsd** (XSD を使用して検証)、**dtd** (DTD を使用して検証) です。 | いいえ |
| namespaces | XML ファイルの解析時に名前空間を有効にするかどうか。 使用可能な値: **true** (既定値)、**false**。 | いいえ |
| namespacePrefixes | 名前空間 URI とプレフィックスのマッピング。XML ファイルの解析時にフィールドに名前を付けるために使用されます。<br/>XML ファイルに名前空間があり、名前空間が有効になっている場合、既定では、フィールド名は XML ドキュメント内のものと同じになります。<br>このマップの名前空間 URI に対して定義された項目がある場合、フィールド名は `prefix:fieldName` です。 | いいえ |
| detectDataType | 整数、倍精度、およびブール値のデータ型を検出するかどうか。 使用可能な値: **true** (既定値)、**false**。| いいえ |
| compressionProperties | 特定の圧縮コーデックのデータを圧縮解除する方法のプロパティ グループ。 | いいえ       |
| preserveZipFileNameAsFolder<br>(" *`compressionProperties`->`type` の下に `ZipDeflateReadSettings` として*")  | **ZipDeflate** で入力データセットが圧縮構成されている場合に適用されます。 コピー時にソースの ZIP ファイル名をフォルダー構造として保持するかどうかを指定します。<br>- **true (既定)** に設定した場合、Data Factory は解凍されたファイルを `<path specified in dataset>/<folder named as source zip file>/` に書き込みます。<br>- **false** に設定した場合、Data Factory は解凍されたファイルを `<path specified in dataset>` に直接書き込みます。 競合または予期しない動作を避けるために、異なるソース ZIP ファイルに重複したファイル名がないことを確認します。  | いいえ |
| preserveCompressionFileNameAsFolder<br>(" *`compressionProperties`->`type` で `TarGZipReadSettings` または `TarReadSettings` として*") | **TarGzip**/**Tar** で入力データセットが圧縮構成されている場合に適用されます。 コピー時にソースの圧縮ファイル名をフォルダー構造として保持するかどうかを指定します。<br>- **true (既定)** に設定した場合、Data Factory は圧縮解除されたファイルを `<path specified in dataset>/<folder named as source compressed file>/` に書き込みます。 <br>- **false** に設定した場合、Data Factory は圧縮解除されたファイルを `<path specified in dataset>` に直接書き込みます。 競合または予期しない動作を避けるために、異なるソース ファイルに重複したファイル名がないことを確認します。 | いいえ |

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

マッピング データ フローでは、次のデータ ストアで XML 形式での読み取りと書き込みを実行できます。[Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。 XML ファイルは、XML データセットまたは[インライン データセット](data-flow-source.md#inline-datasets)を使用して参照できます。

### <a name="source-properties"></a>ソースのプロパティ

次の表に、XML ソースでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[ソース オプション]** タブで編集できます。詳細については、「[XML コネクタの動作](#xml-connector-behavior)」を参照してください。 インライン データセットを使用する場合、「[データセットのプロパティ](#dataset-properties)」セクションで説明したプロパティと同じ追加のファイル設定が表示されます。 

| Name | 説明 | 必須 | 使用できる値 | データ フロー スクリプトのプロパティ |
| ---- | ----------- | -------- | -------------- | ---------------- |
| ワイルド カードのパス | ワイルドカードのパスに一致するすべてのファイルが処理されます。 データセットで設定されているフォルダーとファイル パスはオーバーライドされます。 | いいえ | String[] | wildcardPaths |
| パーティションのルート パス | パーティション分割されたファイル データについては、パーティション フォルダーを列として読み取るためにパーティションのルート パスを入力できます | いいえ | String | partitionRootPath |
| ファイルの一覧 | 処理するファイルを一覧表示しているテキスト ファイルをソースが指しているかどうか | いいえ | `true` または `false` | fileList |
| ファイル名を格納する列 | ソース ファイル名とパスを使用して新しい列を作成します | いいえ | String | rowUrlColumn |
| 完了後 | 処理後にファイルを削除または移動します。 ファイル パスはコンテナー ルートから始まります | いいえ | 削除: `true` または `false` <br> 移動: `['<from>', '<to>']` | purgeFiles <br>moveFiles |
| 最終更新日時でフィルター処理 | 最後に変更された日時に基づいてファイルをフィルター処理する場合に選択 | いいえ | Timestamp | modifiedAfter <br>modifiedBefore |
| 検証モード | XML スキーマを検証するかどうかを指定します。 | いいえ | `None` (既定。検証なし)<br>`xsd` (XSD を使用して検証)<br>`dtd` (DTD を使用して検証) | validationMode |
| 名前空間 | XML ファイルの解析時に名前空間を有効にするかどうか。 | いいえ | `true` (既定値) または `false` | namespaces |
| 名前空間プレフィックスのペア | 名前空間 URI とプレフィックスのマッピング。XML ファイルの解析時にフィールドに名前を付けるために使用されます。<br/>XML ファイルに名前空間があり、名前空間が有効になっている場合、既定では、フィールド名は XML ドキュメント内のものと同じになります。<br>このマップの名前空間 URI に対して定義された項目がある場合、フィールド名は `prefix:fieldName` です。 | いいえ | パターン `['URI1'->'prefix1','URI2'->'prefix2']` を含む配列 | namespacePrefixes |
| [Allow no files found]\(ファイルの未検出を許可\) | true の場合、ファイルが見つからない場合でもエラーはスローされない | no | `true` または `false` | ignoreNoFilesFound |

### <a name="xml-source-script-example"></a>XML ソース スクリプトの例

次のスクリプトは、データセット モードを使用したマッピング データ フローにおける XML ソースの構成例です。

```
source(allowSchemaDrift: true,
    validateSchema: false,
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

次のスクリプトは、インライン データセット モードを使用した XML ソースの構成例です。

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'xml',
    fileSystem: 'filesystem',
    folderPath: 'folder',
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

## <a name="xml-connector-behavior"></a>XML コネクタの動作

XML をソースとして使用する場合は、次の点に注意してください。

- XML 属性:

    - 要素の属性は、階層内の要素のサブフィールドとして解析されます。
    - 属性フィールドの名前は `@attributeName` のパターンに従います。

- XML スキーマの検証:

    - スキーマを検証しないこと、あるいは XSD または DTD を使用してスキーマを検証することを選択できます。
    - XSD または DTD を使用して XML ファイルを検証する場合、XSD/DTD は、相対パスを使用して XML ファイル内で参照する必要があります。

- 名前空間の処理:

    - データ フローを使用するときに名前空間を無効にできます。その場合、名前空間を定義する属性は、通常の属性として解析されます。
    - 名前空間が有効になっている場合、要素と属性の名前は、既定で `namespaceUri,elementName` と `namespaceUri,@attributeName` のパターンに従います。 ソース内の名前空間 URI ごとに名前空間プレフィックスを定義できます。この場合、要素と属性の名前は、代わりに `definedPrefix:elementName` または `definedPrefix:@attributeName` のパターンに従います。

- 値列:

    - XML 要素に単純なテキスト値と属性/子要素の両方がある場合、単純なテキスト値は、組み込みフィールド名 `_value_` を持つ "値列" の値として解析されます。 また、要素の名前空間も継承します (該当する場合)。

## <a name="next-steps"></a>次のステップ

- [コピー アクティビティの概要](copy-activity-overview.md)
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)