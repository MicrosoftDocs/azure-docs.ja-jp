---
title: SFTP サーバーとの間でデータをコピーする
description: Azure Data Factory を使用して SFTP サーバーとの間でデータをコピーする方法について説明します。
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/25/2020
ms.openlocfilehash: a03a141a4140ca4ac000a8e2afb8dd8f45d40662
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816619"
---
# <a name="copy-data-from-and-to-the-sftp-server-by-using-azure-data-factory"></a>Azure Data Factory を使用して SFTP サーバーとの間でデータをコピーする

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-sftp-connector.md)
> * [現在のバージョン](connector-sftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、セキュリティで保護された FTP (SFTP) サーバーとの間でデータをコピーする方法について説明します。 Azure Data Factory については、[入門記事で](introduction.md)をご覧ください。

## <a name="supported-capabilities"></a>サポートされる機能

SFTP コネクタは、以下のアクティビティに対してサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [アクティビティを削除する](delete-activity.md)

具体的には、SFTP コネクタでは以下がサポートされます。

- *基本*認証または *SshPublicKey* 認証を使用した SFTP サーバーとの間のファイルのコピー。
- そのままでのファイルのコピー、または[サポートされているファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md)を使用したファイルの解析または生成によるファイルのコピー。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、SFTP に固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SFTP のリンクされたサービスでは、以下のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは *Sftp* に設定する必要があります。 |はい |
| host | SFTP サーバーの名前または IP アドレス。 |はい |
| port | SFTP サーバーがリッスンしているポート。<br/>指定できる値は整数で、既定値は *22* です。 |いいえ |
| skipHostKeyValidation | ホスト キーの検証をスキップするかどうかを指定します。<br/>使用できる値は *true* と *false* (既定値) です。  | いいえ |
| hostKeyFingerprint | ホスト キーのフィンガープリントを指定します。 | はい ("skipHostKeyValidation" が false に設定されている場合)。  |
| authenticationType | 認証の種類を指定します。<br/>指定できる値は *Basic* と *SshPublicKey* です。 詳細については、「[基本認証を使用する](#use-basic-authentication)」セクションを参照してください。 JSON の例については、「[SSH 公開キー認証を使用する](#use-ssh-public-key-authentication)」セクションを参照してください。 |はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 統合ランタイムが指定されていない場合、サービスでは既定の Azure Integration Runtime が使用されます。 |いいえ |

### <a name="use-basic-authentication"></a>基本認証を使用する

基本認証を使用するには、*authenticationType* プロパティを *Basic* に設定し、前のセクションで導入した一般的な SFTP コネクタ プロパティに加えて以下のプロパティを指定します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| userName | SFTP サーバーにアクセスできるユーザー。 |はい |
| password | ユーザー (userName) のパスワード。 このフィールドを SecureString としてマークしてデータ ファクトリに安全に格納するか、[Azure キー コンテナーに格納されているシークレットを参照します](store-credentials-in-key-vault.md)。 | はい |

**例:**

```json
{
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-ssh-public-key-authentication"></a>SSH 公開キー認証を使用する

SSH 公開キー認証を使用するには、"authenticationType" を **SshPublicKey** に設定し、前のセクションで導入した一般的な SFTP コネクタ プロパティのほかに、次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| userName | SFTP サーバーにアクセスできるユーザー。 |はい |
| privateKeyPath | 統合ランタイムがアクセスできる秘密キー ファイルへの絶対パスを指定します。 これは、"connectVia" でセルフホステッド型の統合ランタイムが指定されている場合にのみ適用されます。 | `privateKeyPath` または `privateKeyContent` を指定します。  |
| privateKeyContent | Base64 にエンコードされた SSH 秘密キーのコンテンツ。 SSH 秘密キーは、OpenSSH 形式にする必要があります。 このフィールドを SecureString としてマークしてデータ ファクトリに安全に格納するか、[Azure キー コンテナーに格納されているシークレットを参照します](store-credentials-in-key-vault.md)。 | `privateKeyPath` または `privateKeyContent` を指定します。 |
| passPhrase | キー ファイルまたはキー コンテンツがパス フレーズで保護されている場合は、パス フレーズやパスワードを指定して、秘密キーの暗号化を解除します。 このフィールドを SecureString としてマークしてデータ ファクトリに安全に格納するか、[Azure キー コンテナーに格納されているシークレットを参照します](store-credentials-in-key-vault.md)。 | はい (秘密キー ファイルまたはキー コンテンツがパス フレーズで保護されている場合)。 |

> [!NOTE]
> SFTP コネクタでは、RSA/DSA OpenSSH キーがサポートされます。 キー ファイルの内容が、"-----BEGIN [RSA/DSA] PRIVATE KEY-----" で始まることを確認します。 秘密キー ファイルが PPK 形式のファイルである場合は、PuTTY ツールを使用して PPK から OpenSSH 形式に変換します。 

**例 1: 秘密キーの filePath を使用した SshPublicKey 認証**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例 2: 秘密キーのコンテンツを使用した SshPublicKey 認証**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットの定義に使用できるセクションとプロパティの完全な一覧については、 [データセット](concepts-datasets-linked-services.md)に関する記事を参照してください。 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

SFTP では、形式ベースのデータセットの `location` 設定で、以下のプロパティがサポートされています。

| プロパティ   | 説明                                                  | 必須 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | データセットの `location` の下の *type* プロパティは、*SftpLocation* に設定する必要があります。 | はい      |
| folderPath | フォルダーへのパス。 ワイルドカードを使用してフォルダーをフィルター処理する場合は、この設定をスキップし、アクティビティのソース設定でパスを指定します。 | いいえ       |
| fileName   | 指定された folderPath の下のファイル名。 ワイルドカードを使用してファイルをフィルター処理する場合は、この設定をスキップし、アクティビティのソース設定でファイル名を指定します。 | いいえ       |

**例:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
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

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に使用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、SFTP ソースでサポートされるプロパティの一覧を示します。

### <a name="sftp-as-source"></a>ソースとしての SFTP

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

SFTP では、形式ベースのコピー ソースの `storeSettings` 設定で、以下のプロパティがサポートされています。

| プロパティ                 | 説明                                                  | 必須                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` の下の *type* プロパティは、*SftpReadSettings* に設定する必要があります。 | はい                                           |
| ***コピーするファイルを特定する*** |  |  |
| オプション 1: 静的パス<br> | データセットに指定されているフォルダーやファイル パスからコピーします。 フォルダーからすべてのファイルをコピーする場合は、さらに `*` として `wildcardFileName` を指定します。 |  |
| オプション 2: ワイルドカード<br>- wildcardFolderPath | ソース フォルダーをフィルター処理するための、ワイルドカード文字を含むフォルダー パス。 <br>使用できるワイルドカードは、`*` (0 個以上の文字に一致) と `?` (0 個または 1 個の文字に一致) です。実際のフォルダー名の中にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 <br>他の例については、「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」を参照してください。 | いいえ                                            |
| オプション 2: ワイルドカード<br>- wildcardFileName | ソース ファイルをフィルター処理するための、指定された folderPath または wildcardFolderPath の下のワイルドカード文字を含むファイル名。 <br>使用できるワイルドカードは、`*` (0 個以上の文字に一致) と `?` (0 個または 1 個の文字に一致) です。実際のフォルダー名の中にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。  他の例については、「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」を参照してください。 | はい |
| オプション 3: ファイルの一覧<br>- fileListPath | 指定されたファイル セットをコピーすることを示します。 コピーするファイルの一覧を含むテキスト ファイルをポイントします (データセットで構成されているパスへの相対パスを使用して、行ごとに 1 つのファイルが記載されています)。<br/>このオプションを使用する場合は、データセット内でファイル名を指定しないでください。 その他の例については、「[ファイル リストの例](#file-list-examples)」を参照してください。 |いいえ |
| ***追加設定*** |  | |
| recursive | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 recursive が true に設定されていて、シンクがファイル ベースのストアである場合、空のフォルダーまたはサブフォルダーはシンクでコピーも作成もされません。 <br>使用可能な値: *true* (既定値) および *false*。<br>`fileListPath` を構成する場合、このプロパティは適用されません。 |いいえ |
| deleteFilesAfterCompletion | コピー先ストアに正常に移動した後、バイナリ ファイルをソース ストアから削除するかどうかを指定します。 ファイルの削除はファイルごとに行われるので、コピー操作が失敗した場合、一部のファイルは既にコピー先にコピーされてソースから削除されているのに対して、他のファイルはまだソース ストアに残っています。 <br/>このプロパティは、データ ソース ストアが BLOB、ADLS Gen1、ADLS Gen2、S3、Google Cloud Storage、ファイル、Azure ファイル、SFTP、または FTP であるバイナリ コピーのシナリオでのみ有効です。 既定値: false。 |いいえ |
| modifiedDatetimeStart    | ファイルは、*最終変更日時*の属性に基づいてフィルター処理されます。 <br>最終変更日時が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の範囲内にあるファイルが選択されます。 時刻は *2018-12-01T05:00:00Z* の形式で、UTC タイム ゾーンに適用されます。 <br> 各プロパティには NULL を指定できます。これは、ファイル属性フィルターをデータセットに適用しないことを意味します。  `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が NULL の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。  `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が NULL の場合は、最終変更日時属性が datetime 値以下であるファイルが選択されます。<br/>`fileListPath` を構成する場合、このプロパティは適用されません。 | いいえ                                            |
| modifiedDatetimeEnd      | 上記と同じです。                                               | いいえ                                            |
| maxConcurrentConnections | ストレージ ストアに同時に接続できる接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ値を指定します。 | いいえ                                            |

**例:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "SftpReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sftp-as-a-sink"></a>シンクとしての SFTP

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

SFTP では、形式ベースのコピー シンクの `storeSettings` 設定で、以下のプロパティがサポートされています。

| プロパティ                 | 説明                                                  | 必須 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` の下の *type* プロパティは、*SftpWriteSettings* に設定する必要があります。 | はい      |
| copyBehavior             | ソースがファイル ベースのデータ ストアのファイルの場合は、コピー動作を定義します。<br/><br/>使用できる値は、以下のとおりです。<br/><b>- PreserveHierarchy (既定値)</b>:ターゲット フォルダー内でファイル階層を保持します。 ソース フォルダーへのソース ファイルの相対パスはターゲット フォルダーへのターゲット ファイルの相対パスと同じになります。<br/><b>- FlattenHierarchy</b>:ソース フォルダーのすべてのファイルをターゲット フォルダーの第一レベルに配置します。 ターゲット ファイルは、自動生成された名前になります。 <br/><b>- MergeFiles</b>:ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイル名を指定した場合、マージされたファイル名は指定した名前になります。 それ以外は自動生成されたファイル名になります。 | いいえ       |
| maxConcurrentConnections | ストレージ ストアに同時に接続できる接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ値を指定します。 | いいえ       |
| useTempFileRename | 一時ファイルにアップロードしてそれらの名前を変更するか、ターゲットのフォルダーまたはファイルの場所に直接書き込むかを指定します。 Azure Data Factory の既定では、最初に一時ファイルに書き込み、その後、アップロードが完了したらそれらの名前を変更します。 このシーケンスは、(1) 同じファイルに書き込み中の他のプロセスがある場合に、ファイルの破損に至る可能性がある競合を回避し、(2) 転送中に元のバージョンのファイルが確実に存在するようにするのに役立ちます。 実際の SFTP サーバーで名前の変更操作がサポートされていない場合は、このオプションを無効にして、ターゲット ファイルへの同時書き込みが発生しないようにします。 詳細については、この表の末尾にあるトラブルシューティングのヒントを参照してください。 | いいえ。 既定値は " *[はい]* " です。 |
| operationTimeout | SFTP サーバーに対する各書き込み要求がタイムアウトになるまでの待機時間。規定値は 60 分 (01:00:00) です。|いいえ |

>[!TIP]
>SFTP にデータを書き込もうとしているときに "UserErrorSftpPathNotFound"、"UserErrorSftpPermissionDenied"、"SftpOperationFail" のいずれかのエラーが表示され、使用する SFTP ユーザーに適切なアクセス許可が*確かに*ある場合は、実際の SFTP サーバーで、ファイル名の変更操作が機能しているかどうかを確認してください。 そうでない場合は、**一時ファイルを使用してアップロードする** (`useTempFileRename`) オプションを無効にして、再度試みてください。 このプロパティの詳細については、前出の表を参照してください。 コピー アクティビティにセルフホステッド統合ランタイムを使用する場合は、必ずバージョン 4.6 以降を使用してください。

**例:**

```json
"activities":[
    {
        "name": "CopyToSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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
                "type": "<source type>"
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings":{
                    "type": "SftpWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>フォルダーとファイル フィルターの例

このセクションでは、ワイルドカード フィルターを使用してフォルダー パスとファイル名を指定した場合に起きる動作について説明します。

| folderPath | fileName | recursive | ソースのフォルダー構造とフィルターの結果 (**太字**のファイルが取得されます)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (空、既定値を使用) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (空、既定値を使用) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>ファイル リストの例

この表では、コピー アクティビティのソースでファイル リスト パスを使用した結果の動作について説明します。 次のソース フォルダー構造があり、太字のファイルをコピーするとします。

| サンプルのソース構造                                      | FileListToCopy.txt のコンテンツ                             | Azure Data Factory の構成                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadata<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **データセット内:**<br>- フォルダー パス: `root/FolderA`<br><br>**コピー アクティビティ ソース内:**<br>- ファイル リストのパス: `root/Metadata/FileListToCopy.txt` <br><br>ファイル リストのパスは、コピーするファイルの一覧を含む同じデータ ストア内のテキスト ファイルをポイントします (データセットで構成されているパスへの相対パスを使用して、行ごとに 1 つのファイルが記載されています)。 |

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

Lookup アクティビティのプロパティの詳細については、「[Azure Data Factory でのルックアップ アクティビティ](control-flow-lookup-activity.md)」を参照してください。

## <a name="getmetadata-activity-properties"></a>GetMetadata アクティビティのプロパティ

GetMetadata アクティビティのプロパティの詳細については、「[Azure Data Factory の GetMetadata アクティビティ](control-flow-get-metadata-activity.md)」を参照してください。 

## <a name="delete-activity-properties"></a>Delete アクティビティのプロパティ

Delete アクティビティのプロパティの詳細については、「[Azure Data Factory の Delete アクティビティ](delete-activity.md)」を参照してください。

## <a name="legacy-models"></a>レガシ モデル

>[!NOTE]
>以下のモデルは、下位互換性のために引き続き現状のままサポートされます。 Azure Data Factory の作成用 UI は新しいモデルを生成するように切り替えられているため、前に説明した新しいモデルを使用することをお勧めします。

### <a name="legacy-dataset-model"></a>レガシ データセット モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの *type* プロパティは *FileShare* に設定する必要があります。 |はい |
| folderPath | フォルダーへのパス。 ワイルドカード フィルターがサポートされています。 使用できるワイルドカードは、`*` (0 個以上の文字に一致) と `?` (0 個または 1 個の文字に一致) です。実際のファイル名の中にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 <br/><br/>例: ルートフォルダー/サブフォルダー。「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の例を参照してください。 |はい |
| fileName |  指定された "folderPath" の下にあるファイルの**名前またはワイルドカード フィルター**。 このプロパティの値を指定しない場合、データセットはフォルダー内のすべてのファイルをポイントします。 <br/><br/>フィルターに使用できるワイルドカードは、`*` (0 個以上の文字に一致) と `?` (0 個または 1 個の文字に一致) です。<br/>- 例 1: `"fileName": "*.csv"`<br/>- 例 2: `"fileName": "???20180427.txt"`<br/>実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 |いいえ |
| modifiedDatetimeStart | ファイルは、*最終変更日時*の属性に基づいてフィルター処理されます。 最終変更日時が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の範囲内にあるファイルが選択されます。 時刻は *2018-12-01T05:00:00Z* の形式で、UTC タイム ゾーンに適用されます。 <br/><br/> 多数のファイルからファイル フィルターを実行する場合は、この設定を有効にすることで、データ移動の全体的なパフォーマンスが影響を受けます。 <br/><br/> 各プロパティには NULL を指定できます。これは、ファイル属性フィルターをデータセットに適用しないことを意味します。  `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が NULL の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。  `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が NULL の場合は、最終変更日時属性が datetime 値以下であるファイルが選択されます。| いいえ |
| modifiedDatetimeEnd | ファイルは、*最終変更日時*の属性に基づいてフィルター処理されます。 最終変更日時が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の範囲内にあるファイルが選択されます。 時刻は *2018-12-01T05:00:00Z* の形式で、UTC タイム ゾーンに適用されます。 <br/><br/> 多数のファイルからファイル フィルターを実行する場合は、この設定を有効にすることで、データ移動の全体的なパフォーマンスが影響を受けます。 <br/><br/> 各プロパティには NULL を指定できます。これは、ファイル属性フィルターをデータセットに適用しないことを意味します。  `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が NULL の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。  `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が NULL の場合は、最終変更日時属性が datetime 値以下であるファイルが選択されます。| いいえ |
| format | ファイルベースのストア間でファイルをそのままコピー (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。<br/><br/>特定の形式のファイルを解析する場合にサポートされるファイル形式の種類は、*TextFormat*、*JsonFormat*、*AvroFormat*、*OrcFormat*、*ParquetFormat*。 形式の *type* プロパティをいずれかの値に設定します。 詳細については、[テキスト形式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[Json 形式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 形式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 形式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)、[Parquet 形式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)についての各セクションを参照してください。 |いいえ (バイナリ コピー シナリオのみ) |
| compression | データの圧縮の種類とレベルを指定します。 詳細については、[サポートされるファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs-legacy.md#compression-support)に関する記事を参照してください。<br/>サポートされる種類は、*GZip*、*Deflate*、*BZip2*、および *ZipDeflate* です。<br/>サポートされるレベルは、*Optimal* と *Fastest* です。 |いいえ |

>[!TIP]
>フォルダーの下のすべてのファイルをコピーするには、*folderPath* のみを指定します。<br>指定された名前の単一のファイルをコピーするには、フォルダー部分で *folderPath* を、ファイル名に *fileName* を指定します。<br>フォルダーの下のファイルのサブセットをコピーするには、フォルダー部分で *folderPath* を、ワイルドカード フィルターで *fileName* を指定します。

>[!NOTE]
>ファイル フィルターで *fileFilter* プロパティを使用していた場合、引き続き現状のままサポートされますが、今後は *fileName* に追加された新しいフィルター機能を使用することをお勧めします。

**例:**

```json
{
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
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

### <a name="legacy-copy-activity-source-model"></a>レガシのコピー アクティビティ ソース モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの *type* プロパティを *FileSystemSource* に設定する必要があります |はい |
| recursive | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 recursive が *true* に設定されていて、シンクがファイル ベースのストアである場合、シンクでは空のフォルダーとサブフォルダーはコピーも作成もされません。<br/>指定できる値は *true* (既定値) と *false* です | いいえ |
| maxConcurrentConnections | ストレージ ストアに同時に接続できる接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ値を指定します。 | いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するページを参照してください。
