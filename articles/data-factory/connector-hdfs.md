---
title: Azure Data Factory を使用して HDFS からデータをコピーする | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、クラウドまたはオンプレミスの HDFS ソースからサポートされているシンク データ ストアへデータをコピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 2cac2b350da5ca8738e40f9a288ecf4059e81060
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673904"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Azure Data Factory を使用して HDFS からデータをコピーする
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-hdfs-connector.md)
> * [現在のバージョン](connector-hdfs.md)

この記事では、HDFS サーバーからデータをコピーする方法について説明します。 Azure Data Factory については、[入門記事で](introduction.md)をご覧ください。

## <a name="supported-capabilities"></a>サポートされる機能

この HDFS コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

具体的には、この HDFS コネクタは以下をサポートします。

- **Windows** (Kerberos) 認証または **匿名**認証を使用するファイルのコピー。
- **webhdfs** プロトコルまたは **組み込みの DistCp** のサポートを使用するファイルのコピー。
- ファイルをそのままコピーするか、[サポートされているファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md)を使用したファイルの解析/生成。

## <a name="prerequisites"></a>前提条件

パブリックにアクセスできない HDFS からデータをコピーするには、セルフホステッド統合ランタイムを設定する必要があります。 詳細については、[セルフホステッド統合ランタイム](concepts-integration-runtime.md)に関する記事をご覧ください。

> [!NOTE]
> Integration Runtime が、Hadoop クラスターの**すべて**の [name node server]:[name node port] および [data node servers]:[data node port] にアクセスできることを確認します。 既定の [name node port] は 50070、既定の [data node port] は 50075 です。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、HDFS に固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

HDFS のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**Hdfs**。 | はい |
| url |HDFS への URL |はい |
| authenticationType | 使用できる値は、以下のとおりです。**Anonymous** または **Windows**。 <br><br> HDFS コネクタに **Kerberos 認証**を使用するには、[こちらのセクション](#use-kerberos-authentication-for-hdfs-connector)を参照して、オンプレミス環境を設定します。 |はい |
| userName |Windows 認証のユーザー名。 Kerberos 認証の場合は `<username>@<domain>.com` を指定します。 |あり (Windows 認証用) |
| password |Windows 認証のパスワード。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 |あり (Windows 認証用) |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 セルフホステッド統合ランタイムまたは Azure 統合ランタイム (データ ストアがパブリックにアクセスできる場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

**例: 匿名認証の使用**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
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
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 

- **Parquet 形式と区切りテキスト形式**については、「[Parquet 形式と区切りテキスト形式のデータセット](#parquet-and-delimited-text-format-dataset)」セクションを参照してください。
- **ORC/Avro/JSON/バイナリ形式**などのその他の形式については、「[他の形式のデータセット](#other-format-dataset)」セクションを参照してください。

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet 形式と区切りテキスト形式のデータセット

**Parquet 形式または区切りテキスト形式**のデータを HDFS からコピーする場合は、[Parquet 形式](format-parquet.md)および[区切りテキスト形式](format-delimited-text.md)に関する記事で、形式ベースのデータセットおよびサポートされる設定について参照してください。 HDFS では、形式ベースのデータセットの `location` 設定において、次のプロパティがサポートされています。

| プロパティ   | 説明                                                  | 必須 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | データセットの `location` の type プロパティは、**HdfsLocation** に設定する必要があります。 | はい      |
| folderPath | フォルダーのパス。 フォルダーをフィルター処理するためにワイルドカードを使用する場合は、この設定をスキップし、アクティビティのソースの設定で指定します。 | いいえ       |
| fileName   | 特定の folderPath の下のファイル名。 ファイルをフィルター処理するためにワイルドカードを使用する場合は、この設定をスキップし、アクティビティのソースの設定で指定します。 | いいえ       |

> [!NOTE]
> 次のセクションで説明する Parquet/テキスト形式の **FileShare** 型のデータセットは、下位互換性のために引き続きコピー/Lookup アクティビティでそのままサポートされます。 今後は、この新しいモデルを使用することをお勧めします。ADF オーサリング UI はこれらの新しい型を生成するように切り替えられています。

**例:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>他の形式のデータセット

**ORC/Avro/JSON/バイナリ形式**のデータを HDFS からコピーする場合、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります: **FileShare** |はい |
| folderPath | フォルダーへのパス。 ワイルドカード フィルターがサポートされいます。使用できるワイルドカーは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。実際のファイル名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 <br/><br/>例: ルートフォルダー/サブフォルダー。「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の例を参照してください。 |はい |
| fileName |  指定された "folderPath" の下にあるファイルの**名前またはワイルドカード フィルター**。 このプロパティの値を指定しない場合、データセットはフォルダー内のすべてのファイルをポイントします。 <br/><br/>フィルターに使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。<br/>- 例 1: `"fileName": "*.csv"`<br/>- 例 2: `"fileName": "???20180427.txt"`<br/>実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 |いいえ |
| modifiedDatetimeStart | ファイルはフィルター処理され、元になる属性は最終更新時刻です。 最終変更時刻が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の間に含まれる場合は、ファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br/><br/> 多数のファイルにファイル フィルターを実行する場合は、この設定を有効にすることで、データ移動の全体的なパフォーマンスが影響を受けることに注意してください。 <br/><br/> プロパティは、ファイル属性フィルターをデータセットに適用しないことを意味する NULL にすることができます。  `modifiedDatetimeStart` に datetime 値を設定し、`modifiedDatetimeEnd` を NULL にした場合は、最終更新時刻属性が datetime 値以上であるファイルが選択されることを意味します。  `modifiedDatetimeEnd` に datetime 値を設定し、`modifiedDatetimeStart` を NULL にした場合は、最終更新時刻属性が datetime 値以下であるファイルが選択されることを意味します。| いいえ |
| modifiedDatetimeEnd | ファイルはフィルター処理され、元になる属性は最終更新時刻です。 最終変更時刻が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の間に含まれる場合は、ファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br/><br/> 多数のファイルにファイル フィルターを実行する場合は、この設定を有効にすることで、データ移動の全体的なパフォーマンスが影響を受けることに注意してください。 <br/><br/> プロパティは、ファイル属性フィルターをデータセットに適用しないことを意味する NULL にすることができます。  `modifiedDatetimeStart` に datetime 値を設定し、`modifiedDatetimeEnd` を NULL にした場合は、最終更新時刻属性が datetime 値以上であるファイルが選択されることを意味します。  `modifiedDatetimeEnd` に datetime 値を設定し、`modifiedDatetimeStart` を NULL にした場合は、最終更新時刻属性が datetime 値以下であるファイルが選択されることを意味します。| いいえ |
| format | ファイルベースのストア間で**ファイルをそのままコピー** (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。<br/><br/>特定の形式のファイルを解析する場合にサポートされるファイル形式の種類は、**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text Format](supported-file-formats-and-compression-codecs.md#text-format)、[Json Format](supported-file-formats-and-compression-codecs.md#json-format)、[Avro Format](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc Format](supported-file-formats-and-compression-codecs.md#orc-format)、[Parquet Format](supported-file-formats-and-compression-codecs.md#parquet-format) の各セクションを参照してください。 |いいえ (バイナリ コピー シナリオのみ) |
| compression | データの圧縮の種類とレベルを指定します。 詳細については、[サポートされるファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md#compression-support)に関する記事を参照してください。<br/>サポートされる種類は、**GZip**、**Deflate**、**BZip2**、**ZipDeflate** です。<br/>サポートされるレベルは、**Optimal** と **Fastest** です。 |いいえ |

>[!TIP]
>フォルダーの下のすべてのファイルをコピーするには、**folderPath** のみを指定します。<br>特定の名前の単一のファイルをコピーするには、フォルダー部分で **folderPath**、ファイル名で **fileName** を指定します。<br>フォルダーの下のファイルのサブセットをコピーするには、フォルダー部分で **folderPath**、ワイルドカード フィルターで **fileName** を指定します。

**例:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、HDFS ソースでサポートされるプロパティの一覧を示します。

### <a name="hdfs-as-source"></a>ソースとしての HDFS

- コピー元から **Parquet 形式および区切りテキスト形式**でコピーする場合は、「[Parquet 形式と区切りテキスト形式のソース](#parquet-and-delimited-text-format-source)」セクションを参照してください。
- コピー元から **ORC/Avro/JSON/バイナリ形式**などの他の形式でコピーする場合は、「[他の形式のソース](#other-format-source)」セクションを参照してください。

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet 形式と区切りテキスト形式のソース

**Parquet 形式または区切りテキスト形式**のデータを HDFS からコピーする場合は、[Parquet 形式](format-parquet.md)および[区切りテキスト形式](format-delimited-text.md)に関する記事で、形式ベースのコピー アクティビティのソースと、サポートされる設定について参照してください。 HDFS では、形式ベースのコピー ソースの `storeSettings` 設定において、次のプロパティがサポートされています。

| プロパティ                 | 説明                                                  | 必須                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` の type プロパティは **HdfsReadSetting**に設定する必要があります。 | はい                                           |
| recursive                | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 recursive が true に設定され、シンクがファイル ベースのストアである場合、空のフォルダーおよびサブフォルダーはシンクでコピーも作成もされないことに注意してください。 使用可能な値: **true** (既定値) および **false**。 | いいえ                                            |
| wildcardFolderPath       | ソース フォルダーをフィルター処理するための、ワイルドカード文字を含むフォルダー パス。 <br>使用できるワイルドカーは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 <br>「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | いいえ                                            |
| wildcardFileName         | ソース ファイルをフィルター処理するための、特定の folderPath/wildcardFolderPath の下のワイルドカード文字を含むファイル名。 <br>使用できるワイルドカーは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。  「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | はい (データセットで `fileName` が指定されていない場合) |
| modifiedDatetimeStart    | ファイルはフィルター処理され、元になる属性は最終更新時刻です。 最終変更時刻が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の間に含まれる場合は、ファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br> プロパティは、ファイル属性フィルターをデータセットに適用しないことを意味する NULL にすることができます。  `modifiedDatetimeStart` に datetime 値を設定し、`modifiedDatetimeEnd` を NULL にした場合は、最終更新時刻属性が datetime 値以上であるファイルが選択されることを意味します。  `modifiedDatetimeEnd` に datetime 値を設定し、`modifiedDatetimeStart` を NULL にした場合は、最終更新時刻属性が datetime 値以下であるファイルが選択されることを意味します。 | いいえ                                            |
| modifiedDatetimeEnd      | 上記と同じです。                                               | いいえ                                            |
| maxConcurrentConnections | 同時にストレージ ストアに接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ                                            |

> [!NOTE]
> Parquet/区切りテキスト形式の場合、次のセクションで説明する **FileSystemSource** 型のコピー アクティビティ ソースは、下位互換性のために引き続きそのままサポートされます。 今後は、この新しいモデルを使用することをお勧めします。ADF オーサリング UI はこれらの新しい型を生成するように切り替えられています。

**例:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HdfsReadSetting",
                    "recursive": true
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>他の形式のソース

**ORC/Avro/JSON/バイナリ形式**のデータを HDFS からコピーする場合、コピー アクティビティの **source** セクションで次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**HdfsSource** |はい |
| recursive | データをサブ フォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 recursive が true に設定され、シンクがファイル ベースのストアである場合、空のフォルダー/サブフォルダーはシンクでコピー/作成されないことに注意してください。<br/>使用可能な値: **true** (既定値)、**false** | いいえ |
| distcpSettings | HDFS DistCp を使用する場合の プロパティ グループ。 | いいえ |
| resourceManagerEndpoint | YARN リソース マネージャー エンドポイント | はい (DistCp を使用する場合) |
| tempScriptPath | 一時 DistCp コマンド スクリプトを格納するために使用するフォルダー パス。 このスクリプト ファイルは Data Factory によって生成され、コピー ジョブ完了後に削除されます。 | はい (DistCp を使用する場合) |
| distcpOptions | DistCp コマンドに指定する追加オプション。 | いいえ |
| maxConcurrentConnections | 同時にストレージ ストアに接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ |

**例:DistCp を使用したコピー アクティビティでの HDFS ソース**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

次のセクションから、DistCp を使用して、HDFS から効率的にデータをコピーする方法の詳細について説明します。

### <a name="folder-and-file-filter-examples"></a>フォルダーとファイル フィルターの例

このセクションでは、ワイルドカード フィルターを使用した結果のフォルダーのパスとファイル名の動作について説明します。

| folderPath | fileName             | recursive | ソースのフォルダー構造とフィルターの結果 (**太字**のファイルが取得されます) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (空、既定値を使用) | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | (空、既定値を使用) | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>DistCp を使用して HDFS からデータをコピーする

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) は、Hadoop クラスターにコピーを配布するための Hadoop のネイティブ コマンドライン ツールです。 Distcp コマンドを実行すると、コマンドは、コピーするすべてのファイルをリストアップした後、複数の Map ジョブを Hadoop クラスターに作成します。各 Map ジョブがソースからシンクへのバイナリ コピーを実行します。

コピー アクティビティでは、DistCp を使用して Azure BLOB ([ステージング コピー](copy-activity-performance.md)を含む) または Azure Data Lake Store にファイルをそのままコピーできます。この場合、セルフホステッド統合ランタイムで実行する代わりに、クラスターのパワーを十分に活用できます。 特にクラスターのパワーが非常に強い場合、コピーのスループットが向上します。 Azure Data Factory の構成に基づいて、コピー アクティビティは、distcp コマンドを自動的に作成し、Hadoop クラスターに送信し、コピー状態を監視します。

### <a name="prerequisites"></a>前提条件

DistCp を使用して HDFS から Azure Blob (ステージング コピーも含みます) または Azure Data Lake Store にファイルをそのままコピーする場合は、Hadoop クラスターが次の要件を満たしていることを確認します。

1. MapReduce と Yarn サービスが有効であること。
2. Yarn のバージョンが 2.5 以降であること。
3. HDFS サーバーがターゲット データ ストア (Azure Blob または Azure Data Lake Store) に統合されていること。

    - Azure Blob ファイル システムは、Hadoop 2.7 以降、ネイティブにサポートされています。 必要なのは、jar パスを Hadoop 環境構成に指定することだけです。
    - Azure Data Lake Store ファイル システムは、Hadoop 3.0.0-alpha1 からパッケージ化されます。 使用している Hadoop クラスターのバージョンがそれよりも小さい場合は、ADLS 関連 jar パッケージ (azure-datalake-store.jar) を [ここから](https://hadoop.apache.org/releases.html)クラスターに手動でインポートし、jar パスを Hadoop 環境構成に指定する必要があります。

4. HDFS 内に一時フォルダーが準備されていること。 この一時フォルダーは DistCpシェル スクリプトを格納するために使用されるため、KB レベルで領域を消費します。
5. HDFS のリンクされたサービスに提供するユーザー アカウントが、a) アプリケーションを Yarn で送信するためのアクセス許可、b) 上記の一時フォルダーの下にサブフォルダーを作成してファイルを読み取る/書き込むためのアクセス許可を持っていること。

### <a name="configurations"></a>構成

DistCp 関連の構成および例については、「[ソースとしての HDFS](#hdfs-as-source)」セクションを参照してください。

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>HDFS コネクタでの Kerberos 認証の使用

HDFS コネクタで Kerberos 認証を使用するようにオンプレミス環境を設定するためのオプションは 2 つあります。 ニーズに適した方法を選択できます。
* オプション 1:[セルフホステッド統合ランタイム コンピューターを Kerberos 領域に参加させる](#kerberos-join-realm)
* オプション 2:[Windows ドメインと Kerberos 領域間の相互の信頼関係を有効にする](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>オプション 1: セルフホステッド統合ランタイム コンピューターを Kerberos 領域に参加させる

#### <a name="requirements"></a>必要条件

* セルフホステッド統合ランタイム コンピューターは Kerberos 領域に参加している必要があります。Windows ドメインには参加できません。

#### <a name="how-to-configure"></a>構成方法

**セルフホステッド統合ランタイム コンピューター上で:**

1.  **Ksetup** ユーティリティを実行して、Kerberos の KDC サーバーと領域を構成します。

    Kerberos 領域は Windows ドメインとは異なるため、コンピューターをワークグループのメンバーとして構成する必要があります。 これは、次のように Kerberos 領域を設定し、KDC サーバーを追加することで実現できます。 必要に応じて、*REALM.COM* を独自の領域に置き換えます。

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    これら 2 つのコマンドを実行した後、コンピューターを**再起動**します。

2.  **Ksetup** コマンドを使用して、構成を確認します。 出力は次のようになります。

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**Azure Data Factory で以下を実行します。**

* Kerberos プリンシパル名とパスワードによる **Windows 認証** を行って HDFS データ ソースに接続するように HDFS コネクタを構成します。 構成の詳細については、「[HDFS のリンクされたサービスのプロパティ](#linked-service-properties)」セクションを参照してください。

### <a name="kerberos-mutual-trust"></a>オプション 2: Windows ドメインと Kerberos 領域間の相互の信頼関係を有効にする

#### <a name="requirements"></a>必要条件

*   セルフホステッド統合ランタイム コンピューターは、Windows ドメインに参加している必要があります。
*   ドメイン コントローラーの設定を更新できるアクセス許可が必要です。

#### <a name="how-to-configure"></a>構成方法

> [!NOTE]
> 必要に応じて、次のチュートリアルの REALM.COM と AD.COM を独自の領域とドメイン コントローラーに置き換えてください。

**KDC サーバーで以下を実行します。**

1. **krb5.conf** ファイルの KDC 構成を編集して、KDC が次の構成テンプレートを参照している Windows ドメインを信頼するようにします。 既定では、この構成は **/etc/krb5.conf** に置かれています。

           [logging]
            default = FILE:/var/log/krb5libs.log
            kdc = FILE:/var/log/krb5kdc.log
            admin_server = FILE:/var/log/kadmind.log
            
           [libdefaults]
            default_realm = REALM.COM
            dns_lookup_realm = false
            dns_lookup_kdc = false
            ticket_lifetime = 24h
            renew_lifetime = 7d
            forwardable = true
            
           [realms]
            REALM.COM = {
             kdc = node.REALM.COM
             admin_server = node.REALM.COM
            }
           AD.COM = {
            kdc = windc.ad.com
            admin_server = windc.ad.com
           }
            
           [domain_realm]
            .REALM.COM = REALM.COM
            REALM.COM = REALM.COM
            .ad.com = AD.COM
            ad.com = AD.COM
            
           [capaths]
            AD.COM = {
             REALM.COM = .
            }

   構成したら KDC サービスを**再起動**します。

2. 次のコマンドを使用して、**krbtgt/REALM.COM\@AD.COM** という名前のプリンシパルを KDC サーバー内に準備します。

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. **hadoop.security.auth_to_local** HDFS サービス構成ファイルに、を追加します`RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`。

**ドメイン コントローラーで、以下を実行します。**

1.  次の **Ksetup** コマンドを実行して、領域エントリを追加します。

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Windows ドメインから Kerberos 領域への信頼関係を確立します。 [password] は、**krbtgt/REALM.COM\@AD.COM** プリンシパルのパスワードです。

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Kerberos で使用される暗号化アルゴリズムを選択します。

    1. サーバー マネージャーに移動し、[グループ ポリシー管理]、[ドメイン]、[グループ ポリシー オブジェクト]、[既定のポリシー] または [Active Domain ポリシー] の順に選択します。

    2. **[グループ ポリシー管理エディター]** ポップアップ ウィンドウで、[コンピューターの構成] > [ポリシー] > [Windows の設定] > [セキュリティの設定] > [ローカル ポリシー] > [セキュリティ オプション] に移動し、 **[ネットワーク セキュリティ: Kerberos で許可する暗号化の種類を構成する]** を構成します。

    3. KDC に接続するときに使用する暗号化アルゴリズムを選択します。 通常は、単純にすべてのオプションを選択できます。

        ![Kerberos での暗号化の種類の構成](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. **Ksetup** コマンドを使用して、特定の領域で使用される暗号化アルゴリズムを指定します。

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Windows ドメインで Kerberos プリンシパルを使用するために、ドメイン アカウントと Kerberos プリンシパル間のマッピングを作成します。

    1. 管理ツールを起動し、 **[Active Directory ユーザーとコンピュータ]** を選択します。

    2. **[ビュー]**  >  **[高度な機能]** をクリックして、高度な機能を構成します。

    3. マッピングを作成するアカウントを見つけます。そのアカウントをクリックして **[名前のマッピング]** を表示し、 **[Kerberos 名]** タブをクリックします。

    4. 領域からプリンシパルを追加します。

        ![マップ セキュリティ ID](media/connector-hdfs/map-security-identity.png)

**セルフホステッド統合ランタイム コンピューター上で:**

* 次の **Ksetup** コマンドを実行して、領域エントリを追加します。

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Azure Data Factory で以下を実行します。**

* ドメイン アカウントまたは Kerberos プリンシパルのいずれかを使用して **Windows 認証** を行って HDFS データ ソースに接続するように HDFS コネクタを構成します。 構成の詳細については、「[HDFS のリンクされたサービスのプロパティ](#linked-service-properties)」セクションを参照してください。


## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
