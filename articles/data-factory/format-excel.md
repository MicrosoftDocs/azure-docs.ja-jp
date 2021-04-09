---
title: Azure Data Factory での Excel 形式
description: このトピックでは、Azure Data Factory で Excel 形式を処理する方法について説明します。
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: jingwang
ms.openlocfilehash: bef29bc958253be0498442f842dda67105ce799b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386528"
---
# <a name="excel-format-in-azure-data-factory"></a>Azure Data Factory での Excel 形式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**Excel ファイルを解析する場合** は、この記事に従ってください。 Azure Data Factory は ".xls" と ".xlsx" の両方をサポートしています。

Excel 形式は、以下のコネクタでサポートされています。[Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure File Storage](connector-azure-file-storage.md)、[ファイル システム](connector-file-system.md)、[FTP](connector-ftp.md)、[Google Cloud Storage](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md)、[HTTP](connector-http.md)、および [SFTP](connector-sftp.md)。 これはソースとしてはサポートされますが、シンクとしてはサポートされません。 

**注**: [HTTP](connector-http.md) の使用中は、".xls" 形式はサポートされません。 

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Excel データセットでサポートされるプロパティの一覧を示します。

| プロパティ         | 説明                                                  | 必須 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | データセットの type プロパティは、**Excel** に設定する必要があります。   | はい      |
| location         | ファイルの場所の設定。 ファイル ベースの各コネクタには、固有の場所の種類と `location` でサポートされるプロパティがあります。 | はい      |
| sheetName        | データを読み取る Excel ワークシートの名前。                       | `sheetName` または `sheetIndex` を指定します |
| sheetIndex | データを読み取る Excel ワークシートのインデックス (0 から開始)。 | `sheetName` または `sheetIndex` を指定します |
| range            | 特定のワークシート内で選択データを見つけるセル範囲。例:<br>- 指定なし: 空ではない最初の行と列からワークシート全体を表として読み取ります<br>- `A3`: 特定のセルから始まる表を読み取り、下にあるすべての行と右にあるすべての列を動的に検出します<br>- `A3:H5`: この固定範囲を表として読み取ります<br>- `A3:A3`: この単一セルを読み取ります | いいえ       |
| firstRowAsHeader | 指定したワークシート (または範囲) 内の先頭行を、列名を含んだヘッダー行として扱うかどうかを指定します。<br>使用できる値は **true** と **false** (既定値) です。 | いいえ       |
| nullValue        | null 値の文字列表現を指定します。 <br>既定値は **空の文字列** です。 | いいえ       |
| compression | ファイル圧縮を構成するためのプロパティのグループ。 アクティビティの実行中に圧縮/圧縮解除を行う場合は、このセクションを構成します。 | いいえ |
| type<br/>( *`compression` の下にあります*) | JSON ファイルの読み取り/書き込みに使用される圧縮コーデックです。 <br>使用できる値は、**bzip2**、**gzip**、**deflate**、**ZipDeflate**、**TarGzip**、**Tar**、**snappy**、または **lz4** です。 既定では圧縮されません。<br>現在、Copy アクティビティでは "snappy" と "lz4" がサポートされておらず、マッピング データ フローでは "ZipDeflate"、"TarGzip"、"Tar" がサポートされていないことに **注意してください**。<br>コピー アクティビティを使用して **ZipDeflate** ファイルを展開し、ファイルベースのシンク データ ストアに書き込むと、`<path specified in dataset>/<folder named as source zip file>/` フォルダーにファイルが抽出されることに **注意** してください。 | いいえ。  |
| level<br/>( *`compression` の下にあります*) | 圧縮率です。 <br>使用できる値は、**Optimal** または **Fastest** です。<br>- **Fastest:** 圧縮操作は可能な限り短時間で完了しますが、圧縮後のファイルが最適に圧縮されていない場合があります。<br>- **Optimal**: 圧縮操作で最適に圧縮されますが、操作が完了するまでに時間がかかる場合があります。 詳細については、 [圧縮レベル](/dotnet/api/system.io.compression.compressionlevel) に関するトピックをご覧ください。 | いいえ       |

Azure Blob Storage 上の Excel データセットの例を次に示します。

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
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
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Excel ソースでサポートされるプロパティの一覧を示します。

### <a name="excel-as-source"></a>ソースとしての Excel 

コピー アクティビティの ***\*source\**** セクションでは、次のプロパティがサポートされます。

| プロパティ      | 説明                                                  | 必須 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | コピー アクティビティのソースの type プロパティを **ExcelSource** に設定する必要があります。 | はい      |
| storeSettings | データ ストアからデータを読み取る方法を指定するプロパティのグループ。 ファイル ベースの各コネクタには、`storeSettings` に、固有のサポートされる読み取り設定があります。 | いいえ       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

マッピング データ フローでは、次のデータ ストアで Excel 形式での読み取りを実行できます。[Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)。 Excel ファイルは、Excel データセットまたは[インライン データセット](data-flow-source.md#inline-datasets)を使用して参照できます。

### <a name="source-properties"></a>ソースのプロパティ

次の表に、Excel ソースでサポートされるプロパティの一覧を示します。 これらのプロパティは、 **[ソース オプション]** タブで編集できます。インライン データセットを使用する場合、「[データセットのプロパティ](#dataset-properties)」セクションで説明したプロパティと同じ追加のファイル設定が表示されます。

| 名前                      | 説明                                                  | 必須 | 使用できる値                                            | データ フロー スクリプトのプロパティ         |
| ------------------------- | ------------------------------------------------------------ | -------- | --------------------------------------------------------- | --------------------------------- |
| ワイルド カードのパス           | ワイルドカードのパスに一致するすべてのファイルが処理されます。 データセットで設定されているフォルダーとファイル パスはオーバーライドされます。 | no       | String[]                                                  | wildcardPaths                     |
| パーティションのルート パス       | パーティション分割されたファイル データについては、パーティション フォルダーを列として読み取るためにパーティションのルート パスを入力できます | no       | String                                                    | partitionRootPath                 |
| ファイルの一覧             | 処理するファイルを一覧表示しているテキスト ファイルをソースが指しているかどうか | no       | `true` または `false`                                         | fileList                          |
| ファイル名を格納する列 | ソース ファイル名とパスを使用して新しい列を作成します       | no       | String                                                    | rowUrlColumn                      |
| 完了後          | 処理後にファイルを削除または移動します。 ファイル パスはコンテナー ルートから始まります | no       | 削除: `true` または `false` <br> 移動: `['<from>', '<to>']` | purgeFiles <br> moveFiles         |
| 最終更新日時でフィルター処理   | 最後に変更された日時に基づいてファイルをフィルター処理する場合に選択 | no       | Timestamp                                                 | modifiedAfter <br> modifiedBefore |
| [Allow no files found]\(ファイルの未検出を許可\) | true の場合、ファイルが見つからない場合でもエラーはスローされない | no | `true` または `false` | ignoreNoFilesFound |

### <a name="source-example"></a>ソースの例

次の図は、データセット モードを使用したマッピング データ フローにおける Excel ソースの構成例です。

![Excel ソース](media/data-flow/excel-source.png)

関連付けられているデータ フロー スクリプトは次のとおりです。

```
source(allowSchemaDrift: true,
    validateSchema: false,
    wildcardPaths:['*.xls']) ~> ExcelSource
```

インライン データセットを使用する場合、マッピング データ フローに次のソース オプションが表示されます。

![Excel ソースのインライン データセット](media/data-flow/excel-source-inline-dataset.png)

関連付けられているデータ フロー スクリプトは次のとおりです。

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'excel',
    fileSystem: 'container',
    folderPath: 'path',
    fileName: 'sample.xls',
    sheetName: 'worksheet',
    firstRowAsHeader: true) ~> ExcelSourceInlineDataset
```

## <a name="next-steps"></a>次のステップ

- [コピー アクティビティの概要](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)