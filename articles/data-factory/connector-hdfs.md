---
title: Azure Data Factory を使用して HDFS からデータをコピーする
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、クラウドまたはオンプレミスの HDFS ソースからサポートされているシンク データ ストアへデータをコピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: jingwang
ms.openlocfilehash: 43ab59f109e311d9d7312b77d34321fa98a952d6
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926809"
---
# <a name="copy-data-from-the-hdfs-server-by-using-azure-data-factory"></a>Azure Data Factory を使用して HDFS サーバーからデータをコピーする

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-hdfs-connector.md)
> * [現在のバージョン](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Hadoop 分散ファイル システム (HDFS) サーバーからデータをコピーする方法について説明します。 Azure Data Factory については、[入門記事で](introduction.md)をご覧ください。

## <a name="supported-capabilities"></a>サポートされる機能

HDFS コネクタは、次のアクティビティでサポートされます。

- [サポートされるソースとシンクのマトリックス](copy-activity-overview.md)に従う[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

具体的には、HDFS コネクタは以下をサポートします。

- *Windows* (Kerberos) または "*匿名*" 認証を使用するファイルのコピー。
- *webhdfs* プロトコルまたは "*組み込みの DistCp*" のサポートを使用するファイルのコピー。
- ファイルをそのままコピーするか、[サポートされているファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md)を使用してファイルを解析または生成する。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> 統合ランタイムが、Hadoop クラスターの "*すべて*" の [name node server]:[name node port] および [data node servers]:[data node port] にアクセスできることを確認します。 既定の [name node port] は 50070、既定の [data node port] は 50075 です。

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、HDFS に固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

HDFS のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | *type* プロパティは *Hdfs* に設定する必要があります。 | はい |
| url |HDFS への URL |はい |
| authenticationType | 使用可能な値: *Anonymous* または*Windows*。 <br><br> オンプレミス環境を設定するには、「[HDFS コネクタでの Kerberos 認証の使用](#use-kerberos-authentication-for-the-hdfs-connector)」セクションを参照してください。 |はい |
| userName |Windows 認証のユーザー名。 Kerberos 認証の場合は **\<username>@\<domain>.com** を指定します。 |はい (Windows 認証用) |
| password |Windows 認証のパスワード。 このフィールドを SecureString としてマークしてデータ ファクトリに安全に保存するか、[Azure キー コンテナーに格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 |あり (Windows 認証用) |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 統合ランタイムが指定されていない場合は、サービスでは既定の Azure Integration Runtime が使用されます。 |いいえ |

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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、「[Azure Data Factory のデータセット](concepts-datasets-linked-services.md)」を参照してください。 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

HDFS では、形式ベースのデータセットの `location` 設定において、次のプロパティがサポートされています。

| プロパティ   | 説明                                                  | 必須 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | データセットの `location` の *type* プロパティは、*HdfsLocation* に設定する必要があります。 | はい      |
| folderPath | フォルダーのパス。 ワイルドカードを使用してフォルダーをフィルター処理する場合は、この設定をスキップし、アクティビティのソース設定でパスを指定します。 | いいえ       |
| fileName   | 指定された folderPath の下のファイル名。 ワイルドカードを使用してファイルをフィルター処理する場合は、この設定をスキップし、アクティビティのソース設定でファイル名を指定します。 | いいえ       |

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

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[Azure Data Factory のパイプラインとアクティビティ](concepts-pipelines-activities.md)」を参照してください。 このセクションでは、HDFS ソースでサポートされるプロパティの一覧を示します。

### <a name="hdfs-as-source"></a>ソースとしての HDFS

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

HDFS では、形式ベースのコピー ソースの `storeSettings` 設定において、次のプロパティがサポートされています。

| プロパティ                 | 説明                                                  | 必須                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` の *type* プロパティは **HdfsReadSettings** に設定する必要があります。 | はい                                           |
| "***コピーするファイルを特定する***" |  |  |
| オプション 1: 静的パス<br> | データセットに指定されているフォルダーまたはファイル パスからコピーします。 フォルダーからすべてのファイルをコピーする場合は、さらに `*` として `wildcardFileName` を指定します。 |  |
| オプション 2: ワイルドカード<br>- wildcardFolderPath | ソース フォルダーをフィルター処理するための、ワイルドカード文字を含むフォルダー パス。 <br>使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。 実際のフォルダー名にワイルドカードまたは `^` が含まれている場合は、このエスケープ文字を使用してエスケープします。 <br>他の例については、「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」を参照してください。 | いいえ                                            |
| オプション 2: ワイルドカード<br>- wildcardFileName | ソース ファイルをフィルター処理するための、指定された folderPath または wildcardFolderPath の下のワイルドカード文字を含むファイル名。 <br>使用できるワイルドカードは、`*` (0 個以上の文字に一致) と `?` (0 個または 1 個の文字に一致) です。実際のフォルダー名にワイルドカードまたは `^` が含まれている場合は、このエスケープ文字を使用してエスケープします。  他の例については、「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」を参照してください。 | はい |
| オプション 3: ファイルの一覧<br>- fileListPath | 指定されたファイル セットをコピーすることを示します。 コピーするファイルの一覧を含むテキスト ファイルをポイントします (データセットで構成されているパスへの相対パスを使用して、ファイルを 1 行につき 1 つずつ指定します)。<br/>このオプションを使用する場合は、データセットにファイル名を指定しないでください。 その他の例については、「[ファイル リストの例](#file-list-examples)」を参照してください。 |いいえ |
| ***追加設定*** |  | |
| recursive | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 `recursive` が *true* に設定されていて、シンクがファイル ベースのストアである場合、シンクでは空のフォルダーまたはサブフォルダーがコピーも作成もされません。 <br>使用可能な値: *true* (既定値) および *false*。<br>`fileListPath` を構成する場合、このプロパティは適用されません。 |いいえ |
| modifiedDatetimeStart    | ファイルは、属性 *Last Modified* に基づいてフィルター処理されます。 <br>最終変更日時が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の範囲内にあるファイルが選択されます。 時刻は *2018-12-01T05:00:00Z* の形式で UTC タイム ゾーンに適用されます。 <br> 各プロパティには NULL を指定できます。これは、ファイル属性フィルターをデータセットに適用しないことを意味します。  `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が NULL の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。  `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が NULL の場合は、最終変更日時属性が datetime 値以下であるファイルが選択されます。<br/>`fileListPath` を構成する場合、このプロパティは適用されません。 | いいえ                                            |
| maxConcurrentConnections | ストレージ ストアに同時に接続できる接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ値を指定します。 | いいえ                                            |
| "***DistCp 設定***" |  | |
| distcpSettings | HDFS DistCp を使用する場合に使用するプロパティ グループ。 | いいえ |
| resourceManagerEndpoint | YARN (Yet Another Resource Negotiator) エンドポイント | はい (DistCp を使用する場合) |
| tempScriptPath | 一時 DistCp コマンド スクリプトを格納するために使用するフォルダー パス。 このスクリプト ファイルは Data Factory によって生成され、コピー ジョブ完了後に削除されます。 | はい (DistCp を使用する場合) |
| distcpOptions | DistCp コマンドに指定する追加オプション。 | いいえ |

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HdfsReadSettings",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
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

### <a name="folder-and-file-filter-examples"></a>フォルダーとファイル フィルターの例

このセクションでは、ワイルドカード フィルターをフォルダー パスとファイル名とともに使用する場合の結果の動作について説明します。

| folderPath | fileName             | recursive | ソースのフォルダー構造とフィルターの結果 (**太字**のファイルが取得されます) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (空、既定値を使用) | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | (空、既定値を使用) | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>ファイル リストの例

このセクションでは、コピー アクティビティのソースでファイル リスト パスを使用した結果の動作について説明します。 次のソース フォルダー構造があり、太字のファイルをコピーするものとします。

| サンプルのソース構造                                      | FileListToCopy.txt のコンテンツ                             | Azure Data Factory の構成                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadata<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **データセット内:**<br>- フォルダー パス: `root/FolderA`<br><br>**コピー アクティビティ ソース内:**<br>- ファイル リストのパス: `root/Metadata/FileListToCopy.txt` <br><br>ファイル リストのパスは、コピーするファイルの一覧を含む同じデータ ストア内のテキスト ファイルをポイントします (データセットで構成されているパスへの相対パスを使用して、1 行につき 1 つのファイルを指定します)。 |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>DistCp を使用して HDFS からデータをコピーする

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) は、Hadoop クラスターにコピーを配布するための Hadoop のネイティブ コマンドライン ツールです。 DistCp でコマンドを実行すると、コピーされるファイルが最初にすべてリストされ、その後 Hadoop クラスターでいくつかの Map ジョブが作成されます。 それぞれの Map ジョブは、ソースからシンクへのバイナリ コピーを実行します。

コピー アクティビティでは、DistCp を使用した、Azure BLOB ストレージ ([ステージング コピー](copy-activity-performance.md)を含む) または Azure データ レイク ストアへのファイルのそのままのコピーがサポートされています。 この場合、DistCp はセルフホステッド統合ランタイムを実行せず、ご使用のクラスターの機能を活用することができます。 特にクラスターが非常に強力な場合は、DistCp を使用することでコピーのスループットが向上します。 データ ファクトリの構成に基づいて、コピー アクティビティは、DistCp コマンドを自動的に作成し、Hadoop クラスターに送信し、コピー状態を監視します。

### <a name="prerequisites"></a>前提条件

DistCp を使用して HDFS から Azure BLOB ストレージ (ステージング コピーを含む) または Azure データ レイク ストアにファイルをそのままコピーする場合は、Hadoop クラスターが次の要件を満たしていることを確認します。

* MapReduce と YARN サービスが有効であること。  
* YARN のバージョンが 2.5 以降であること。  
* HDFS サーバーが、次の対象のデータ ストアと統合されていること。**Azure BLOB ストレージ**または **Azure Data Lake Store (ADLS Gen1)** : 

    - Azure Blob ファイル システムは、Hadoop 2.7 以降、ネイティブにサポートされています。 Hadoop 環境構成で JAR のパスを指定するだけで十分です。
    - Azure Data Lake Store ファイル システムは、Hadoop 3.0.0-alpha1 からパッケージ化されます。 ご使用の Hadoop クラスターのバージョンがそのバージョンよりも前のものである場合、Azure Data Lake Store 関連の JAR パッケージ (azure-datalake-store.jar) を、[こちら](https://hadoop.apache.org/releases.html)からクラスターに手動でインポートし、Hadoop 環境構成で JAR ファイル パスを指定する必要があります。

* HDFS 内に一時フォルダーが準備されていること。 この一時フォルダーは DistCp シェル スクリプトを格納するために使用されるため、KB レベルで領域を消費します。
* HDFS のリンクされたサービスで提供されているユーザー アカウントに、次のアクセス許可が確実にあること。
   * YARN でのアプリケーションの送信。
   * 一時フォルダーでのサブフォルダーの作成と、ファイルの読み取りと書き込み。

### <a name="configurations"></a>構成

DistCp 関連の構成および例については、「[ソースとしての HDFS](#hdfs-as-source)」セクションを参照してください。

## <a name="use-kerberos-authentication-for-the-hdfs-connector"></a>HDFS コネクタでの Kerberos 認証の使用

HDFS コネクタで Kerberos 認証を使用するようにオンプレミス環境を設定するためのオプションは 2 つあります。 自分の状況に適した方法を選択できます。
* オプション 1: [セルフホステッド統合ランタイム コンピューターを Kerberos 領域に参加させる](#kerberos-join-realm)
* オプション 2:[Windows ドメインと Kerberos 領域の間の相互信頼関係を有効にする](#kerberos-mutual-trust)

### <a name="option-1-join-a-self-hosted-integration-runtime-machine-in-the-kerberos-realm"></a><a name="kerberos-join-realm"></a>オプション 1: セルフホステッド統合ランタイム コンピューターを Kerberos 領域に参加させる

#### <a name="requirements"></a>必要条件

* セルフホステッド統合ランタイム コンピューターは Kerberos 領域に参加している必要があります。Windows ドメインには参加できません。

#### <a name="how-to-configure"></a>構成方法

**セルフホステッド統合ランタイム コンピューターで:**

1.  Ksetup ユーティリティを実行して、Kerberos Key Distribution Center (KDC) サーバーと領域を構成します。

    Kerberos 領域は Windows ドメインとは異なるため、コンピューターをワークグループのメンバーとして構成する必要があります。 この構成は、次のコマンドを実行して Kerberos 領域を設定し、KDC サーバーを追加することによって達成できます。 *REALM.COM* は、実際の領域名で置き換えます。

    ```console
    C:> Ksetup /setdomain REALM.COM
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    ```

    これらのコマンドを実行した後、コンピューターを再起動します。

2.  `Ksetup` コマンドを使用して構成を確認します。 出力は次のようになります。

    ```output
    C:> Ksetup
    default realm = REALM.COM (external)
    REALM.com:
        kdc = <your_kdc_server_address>
    ```

**ご使用のデータ ファクトリで:**

* Kerberos プリンシパル名とパスワードによる Windows 認証を行って HDFS データ ソースに接続するように HDFS コネクタを構成します。 構成の詳細については、[HDFS のリンクされたサービスのプロパティ](#linked-service-properties)のセクションを参照してください。

### <a name="option-2-enable-mutual-trust-between-the-windows-domain-and-the-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>オプション 2: Windows ドメインと Kerberos 領域の間の相互信頼関係を有効にする

#### <a name="requirements"></a>必要条件

*   セルフホステッド統合ランタイム コンピューターは、Windows ドメインに参加している必要があります。
*   ドメイン コントローラーの設定を更新するアクセス許可が必要です。

#### <a name="how-to-configure"></a>構成方法

> [!NOTE]
> 次のチュートリアルの REALM.COM と AD.COM を、実際の領域名とドメイン コントローラーに置き換えます。

**KDC サーバーで:**

1. *krb5.conf* ファイルの KDC 構成を編集して、KDC が次の構成テンプレートを参照して、Windows ドメインを信頼するようにします。 既定で、この構成は */etc/krb5.conf* にあります。

   ```config
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
    ```

   ファイルを構成した後、KDC サービスを再起動します。

2. 次のコマンドを使用して、*krbtgt/REALM.COM\@AD.COM* という名前のプリンシパルを KDC サーバー内に準備します。

    ```cmd
    Kadmin> addprinc krbtgt/REALM.COM@AD.COM
    ```

3. *hadoop.security.auth_to_local* HDFS サービス構成ファイルに `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//` を追加します。

**ドメイン コントローラーで:**

1.  次の `Ksetup` コマンドを実行して、領域のエントリを追加します。

    ```cmd
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
    ```

2.  Windows ドメインから Kerberos 領域への信頼関係を確立します。 [password] は、*krbtgt/REALM.COM\@AD.COM* プリンシパルのパスワードです。

    ```cmd
    C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /password:[password]
    ```

3.  Kerberos で使用される暗号化アルゴリズムを選択します。

    a. **[サーバー マネージャー]**  >  **[グループ ポリシー管理]**  >  **[ドメイン]**  >  **[グループ ポリシー オブジェクト]**  >  **[既定のポリシー] または [Active Domain ポリシー]** の順に選択し、さらに **[編集]** を選択します。

    b. **[グループ ポリシー管理エディター]** ペインで、 **[コンピューターの構成]**  >  **[ポリシー]**  >  **[Windows の設定]**  >  **[セキュリティの設定]**  >  **[ローカル ポリシー]**  >  **[セキュリティ オプション]** を選択し、 **[ネットワーク セキュリティ:Kerberos で許可する暗号化の種類を構成する]** を構成します。

    c. KDC サーバーに接続する際に使用する暗号化アルゴリズムを選択します。 すべてのオプションを選択できます。

    ![スクリーンショット: [ネットワーク セキュリティ:Kerberos で許可する暗号化の種類を構成する] ペイン](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    d. `Ksetup` コマンドを使用して、指定された領域で使用される暗号化アルゴリズムを指定します。

    ```cmd
    C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
    ```

4.  Windows ドメインで Kerberos プリンシパルを使用できるように、ドメイン アカウントと Kerberos プリンシパル間のマッピングを作成します。

    a. **[管理ツール]**  >  **[Active Directory ユーザーとコンピューター]** を選択します。

    b. **[表示]**  >  **[高度な機能]** の順に選択して、高度な機能を構成します。

    c. **[高度な機能]** ペインでマッピングを作成するアカウントを右クリックし、 **[名前のマッピング]** ペインで **[Kerberos 名]** タブを選択します。

    d. 領域からプリンシパルを追加します。

       ![[セキュリティ ID マッピング] ペイン](media/connector-hdfs/map-security-identity.png)

**セルフホステッド統合ランタイム コンピューターで:**

* 次の `Ksetup` コマンドを実行して、領域のエントリを追加します。

   ```cmd
   C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
   C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**ご使用のデータ ファクトリで:**

* ドメイン アカウントまたは Kerberos プリンシパルのいずれかを使用して Windows 認証を行って HDFS データ ソースに接続するように HDFS コネクタを構成します。 構成の詳細については、[HDFS にリンクされたサービスのプロパティ](#linked-service-properties)のセクションを参照してください。

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

Lookup アクティビティのプロパティの詳細については、「[Azure Data Factory でのルックアップ アクティビティ](control-flow-lookup-activity.md)」を参照してください。

## <a name="legacy-models"></a>レガシ モデル

>[!NOTE]
>次のモデルは、下位互換性のために引き続きそのままサポートされます。 Azure Data Factory の作成用 UI は新しいモデルを生成するように変更されているため、既に説明した新しいモデルの使用をお勧めします。

### <a name="legacy-dataset-model"></a>レガシ データセット モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの *type* プロパティは *FileShare* に設定する必要があります |はい |
| folderPath | フォルダーのパス。 ワイルドカード フィルターがサポートされています。 使用できるワイルドカードは、`*` (0 個以上の文字に一致) と `?` (0 個または 1 個の文字に一致) です。実際のファイル名にワイルドカードまたは `^` が含まれている場合は、このエスケープ文字を使用してエスケープします。 <br/><br/>例: ルートフォルダー/サブフォルダー。「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の例を参照してください。 |はい |
| fileName |  指定された "folderPath" にあるファイルの名前またはワイルドカード フィルター。 このプロパティの値を指定しない場合、データセットはフォルダー内のすべてのファイルをポイントします。 <br/><br/>フィルターに使用できるワイルドカードは、`*` (0 個以上の文字に一致) と `?` (0 個または 1 個の文字に一致) です。<br/>- 例 1: `"fileName": "*.csv"`<br/>- 例 2: `"fileName": "???20180427.txt"`<br/>実際のフォルダー名にワイルドカードまたは `^` が含まれている場合は、このエスケープ文字を使用してエスケープします。 |いいえ |
| modifiedDatetimeStart | ファイルは、属性 *Last Modified* に基づいてフィルター処理されます。 最終変更日時が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の範囲内にあるファイルが選択されます。 時刻は *2018-12-01T05:00:00Z* の形式で UTC タイム ゾーンに適用されます。 <br/><br/> 多数のファイルにファイル フィルターを適用する場合は、この設定を有効にすることで、データ移動の全体的なパフォーマンスが影響を受けることに注意してください。 <br/><br/> 各プロパティには NULL を指定できます。これは、ファイル属性フィルターをデータセットに適用しないことを意味します。  `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が NULL の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。  `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が NULL の場合は、最終変更日時属性が datetime 値以下であるファイルが選択されます。| いいえ |
| modifiedDatetimeEnd | ファイルは、属性 *Last Modified* に基づいてフィルター処理されます。 最終変更日時が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の範囲内にあるファイルが選択されます。 時刻は *2018-12-01T05:00:00Z* の形式で UTC タイム ゾーンに適用されます。 <br/><br/> 多数のファイルにファイル フィルターを適用する場合は、この設定を有効にすることで、データ移動の全体的なパフォーマンスが影響を受けることに注意してください。 <br/><br/> 各プロパティには NULL を指定できます。これは、ファイル属性フィルターをデータセットに適用しないことを意味します。  `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が NULL の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。  `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が NULL の場合は、最終変更日時属性が datetime 値以下であるファイルが選択されます。| いいえ |
| format | ファイルベースのストア間でファイルをそのままコピー (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。<br/><br/>特定の形式のファイルを解析する場合にサポートされるファイル形式の種類は、*TextFormat*、*JsonFormat*、*AvroFormat*、*OrcFormat*、*ParquetFormat* です。 形式の *type* プロパティをいずれかの値に設定します。 詳細については、「[テキスト形式](supported-file-formats-and-compression-codecs-legacy.md#text-format)」、「[JSON 形式](supported-file-formats-and-compression-codecs-legacy.md#json-format)」、「[AVRO 形式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)」、「[ORC 形式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)」、[Parquet 形式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)」の各セクションをご覧ください。 |いいえ (バイナリ コピー シナリオのみ) |
| compression | データの圧縮の種類とレベルを指定します。 詳細については、[サポートされるファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs-legacy.md#compression-support)に関する記事を参照してください。<br/>サポートされる種類は、*Gzip*、*Deflate*、*Bzip2*、*ZipDeflate* です。<br/>サポートされるレベルは、*Optimal* と *Fastest* です。 |いいえ |

>[!TIP]
>フォルダーの下のすべてのファイルをコピーするには、**folderPath** のみを指定します。<br>指定された名前の単一のファイルをコピーするには、フォルダー部分に **folderPath**、ファイル名に **fileName** を指定します。<br>フォルダーの下のファイルのサブセットをコピーするには、フォルダー部分で **folderPath**、ワイルドカード フィルターで **fileName** を指定します。

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

### <a name="legacy-copy-activity-source-model"></a>レガシ コピー アクティビティ ソース モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティ ソースの *type* プロパティは *HdfsSource* に設定する必要があります。 |はい |
| recursive | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 recursive が *true* に設定されていて、シンクがファイル ベースのストアである場合、空のフォルダーまたはサブフォルダーはシンクでコピーも作成もされません。<br/>使用可能な値: *true* (既定値) および *false*。 | いいえ |
| distcpSettings | HDFS DistCp を使用する場合のプロパティ グループ。 | いいえ |
| resourceManagerEndpoint | YARN リソース マネージャー エンドポイント | はい (DistCp を使用する場合) |
| tempScriptPath | 一時 DistCp コマンド スクリプトを格納するために使用するフォルダー パス。 このスクリプト ファイルは Data Factory によって生成され、コピー ジョブ完了後に削除されます。 | はい (DistCp を使用する場合) |
| distcpOptions | 追加のオプションが DistCp コマンドに指定されます。 | いいえ |
| maxConcurrentConnections | ストレージ ストアに同時に接続できる接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ値を指定します。 | いいえ |

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

## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)をご確認ください。
