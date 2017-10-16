---
title: "Azure Data Factory を使用して HDFS からデータをコピーする | Microsoft Docs"
description: "Azure Data Factory パイプラインでコピー アクティビティを使用して、クラウドまたはオンプレミスの HDFS ソースからサポートされているシンク データ ストアへデータをコピーする方法について説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 8db368017ef25603598ec92e8bbbb1fc69230145
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-and-to-hdfs-using-azure-data-factory"></a>Azure Data Factory を使用して HDFS からデータをコピーする
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-hdfs-connector.md)
> * [バージョン 2 - プレビュー](connector-hdfs.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、HDFS からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[V1 の HDFS コネクタ](v1/data-factory-hdfs-connector.md)に関する記事を参照してください。


## <a name="supported-scenarios"></a>サポートされるシナリオ

HDFS から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。

具体的には、この HDFS コネクタは以下をサポートします。

- **Windows** (Kerberos) 認証または **匿名**認証を使用するファイルのコピー。
- **webhdfs** プロトコルまたは **組み込みの DistCp** のサポートを使用するファイルのコピー。
- ファイルをそのままコピーするか、[サポートされているファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md)を使用したファイルの解析/生成。

## <a name="prerequisites"></a>前提条件

パブリックにアクセスできない HDFS をコピー元またはコピー先としてデータをコピーするには、セルフホステッド統合ランタイムを設定する必要があります。 詳細については、[セルフホステッド統合ランタイム](concepts-integration-runtime.md)に関する記事をご覧ください。

## <a name="getting-started"></a>使用の開始
コピー アクティビティを含むパイプラインは、.NET SDK、Python SDK、Azure PowerShell、REST API、または Azure Resource Manager テンプレートを使用して作成できます。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](quickstart-create-data-factory-dot-net.md)をご覧ください。

次のセクションでは、HDFS に固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

HDFS のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **Hdfs** に設定する必要があります。 | あり |
| url |HDFS への URL |はい |
| authenticationType | 使用可能な値: **Anonymous** または**Windows**。 <br><br> HDFS コネクタに **Kerberos 認証**を使用するには、[こちらのセクション](#use-kerberos-authentication-for-hdfs-connector)を参照して、オンプレミス環境を設定します。 |はい |
| userName |Windows 認証のユーザー名。 |あり (Windows 認証用) |
| password |Windows 認証のパスワード。 このフィールドを SecureString とマークします。 |あり (Windows 認証用) |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 セルフホステッド統合ランタイムまたは Azure 統合ランタイム (データ ストアがパブリックにアクセスできる場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

**例: 匿名認証の使用**

```json
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
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
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、HDFS データセット でサポートされるプロパティの一覧を示します。

HDFS からデータをコピーするには、データセットの type プロパティを **FileShare** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは **FileShare** に設定する必要があります。 |あり |
| folderPath | フォルダーへのパス。 例: folder/subfolder/ |あり |
| fileName | 特定のファイルからコピーする場合は、**folderPath** にファイルの名前を指定します。 このプロパティの値を設定しない場合、データセットはフォルダー内のすべてのファイルをソースとしてポイントします。 |いいえ |
| format | ファイルベースのストア間で**ファイルをそのままコピー** (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。<br/><br/>ファイルを特定の形式で解析する場合、次のファイル形式がサポートされます。**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text Format](supported-file-formats-and-compression-codecs.md#text-format)、[Json Format](supported-file-formats-and-compression-codecs.md#json-format)、[Avro Format](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc Format](supported-file-formats-and-compression-codecs.md#orc-format)、[Parquet Format](supported-file-formats-and-compression-codecs.md#parquet-format) の各セクションを参照してください。 |いいえ (バイナリ コピー シナリオのみ) |
| compression | データの圧縮の種類とレベルを指定します。 詳細については、[サポートされるファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md#compression-support)に関する記事を参照してください。<br/>サポートされる種類は、**GZip**、**Deflate**、**BZip2**、および **ZipDeflate** です。<br/>サポートされるレベルは、**Optimal** と **Fastest** です。 |いいえ |

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
            "fileName": "myfile.csv.gz",
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

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、HDFS のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="hdfs-as-source"></a>ソースとしての HDFS

HDFS からデータをコピーするは、コピー アクティビティのソースの種類を **HdfsSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **HdfsSource** に設定する必要があります。 |あり |
| recursive | データをサブ フォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。<br/>使用可能な値: **true** (既定値)、**false** | いいえ |
| distcpSettings | HDFS DistCp を使用する場合の プロパティ グループ。 | いいえ |
| resourceManagerEndpoint | Yarn ResourceManager エンドポイント。 | はい (DistCp を使用する場合) |
| tempScriptPath | 一時 DistCp コマンド スクリプトを格納するために使用するフォルダー パス。 このスクリプト ファイルは ADF によって生成され、コピー ジョブ完了後に削除されます。 | はい (DistCp を使用する場合) |
| distcpOptions | DistCp コマンドに指定する追加オプション。 | いいえ |

**例: UNLOAD を使用するコピー アクティビティの HDFS ソース**

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

## <a name="use-distcp-to-copy-data-from-hdfs"></a>DistCp を使用して HDFS からデータをコピーする

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) は、Hadoop クラスターにコピーを配布するための Hadoop のネイティブ コマンドライン ツールです。 Distcp コマンドを実行すると、コマンドは、コピーするすべてのファイルをリストアップした後、複数の Map ジョブを Hadoop クラスターに作成します。各 Map ジョブがソースからシンクへのバイナリ コピーを実行します。

コピー アクティビティは、DistCp を使用して Azure Blob または Azure Data Lake Store にファイルをそのままコピーする ことをサポートします (Azure Blob の場合は[ステージング コピー](copy-activity-performance.md)も含みます)。この場合、セルフホステッド統合ランタイムで実行する代わりに、クラスターのパワーを十分に活用できます。 特にクラスターのパワーが非常に強い場合、コピーのスループットが向上します。 Azure Data Factory の構成に基づいて、コピー アクティビティは、distcp コマンドを自動的に作成し、Hadoop クラスターに送信し、コピー状態を監視します。

### <a name="prerequsites"></a>前提条件

DistCp を使用して HDFS から Azure Blob (ステージング コピーも含みます) または Azure Data Lake Store にファイルをそのままコピーする場合は、Hadoop クラスターが次の要件を満たしていることを確認します。

1. MapReduce と Yarn サービスが有効であること。
2. Yarn のバージョンが 2.5 以降であること。
3. HDFS サーバーがターゲット データ ストア (Azure Blob または Azure Data Lake Store) に統合されていること。

    - Azure Blob ファイル システムは、Hadoop 2.7 以降、ネイティブにサポートされています。 必要なのは、jar パスを Hadoop 環境構成に指定することだけです。
    - Azure Data Lake Store ファイル システムは、Hadoop 3.0.0-alpha1 からパッケージ化されます。 使用している Hadoop クラスターのバージョンがそれよりも小さい場合は、ADLS 関連 jar パッケージ (azure-datalake-store.jar) を [ここから](https://hadoop.apache.org/releases.html)クラスターに手動でインポートし、jar パスを Hadoop 環境構成に指定する必要があります。

4. HDFS 内に一時フォルダーが準備されていること。 この一時フォルダーは DistCpシェル スクリプトを格納するために使用されるため、KB レベルで領域を消費します。
5. HDFS のリンクされたサービスに提供するユーザー アカウントが、a) アプリケーションを Yarn で送信するためのアクセス許可、b) 上記の一時フォルダーの下にサブフォルダーを作成してファイルを読み取る/書き込むためのアクセス許可を持っていること。

### <a name="configurations"></a>構成

DistCp を使用して HDFS から Azure Blob にデータをコピーするためのコピー アクティビティの構成の例を次に示します。

**例:**

```json
"activities":[
    {
        "name": "CopyFromHDFSToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "HDFSDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "HdfsSource",
                "distcpSettings": {
                    "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                    "tempScriptPath": "/usr/hadoop/tempscript",
                    "distcpOptions": "-strategy dynamic -map 100"
                }
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>HDFS コネクタでの Kerberos 認証の使用

HDFS コネクタで Kerberos 認証を使用するようにオンプレミス環境を設定するためのオプションは 2 つあります。 ニーズに適した方法を選択できます。
* オプション 1: [セルフホステッド統合ラインタイム コンピューターを Kerberos 領域に参加させる](#kerberos-join-realm)
* オプション 2: [Windows ドメインと Kerberos 領域間の相互の信頼関係を有効にする](#kerberos-mutual-trust)

### <a name="kerberos-join-realm">オプション 1: セルフホステッド統合ラインタイム コンピューターを Kerberos 領域に参加させる</a>

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

1.  **krb5.conf** ファイルの KDC 構成を編集して、KDC が次の構成テンプレートを参照している Windows ドメインを信頼するようにします。 既定では、この構成は **/etc/krb5.conf** に置かれています。

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

2.  次のコマンドを使用して、**krbtgt/REALM.COM@AD.COM** という名前のプリンシパルを KDC サーバー内に準備します。

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  **hadoop.security.auth_to_local** HDFS サービス構成ファイルに、`RULE:[1:$1@$0](.*@AD.COM)s/@.*//` を追加します。

**ドメイン コントローラーで、以下を実行します。**

1.  次の **Ksetup** コマンドを実行して、領域エントリを追加します。

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Windows ドメインから Kerberos 領域への信頼関係を確立します。 [password] は、 **krbtgt/REALM.COM@AD.COM** プリンシパルのパスワードです。

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Kerberos で使用される暗号化アルゴリズムを選択します。

    1. サーバー マネージャーに移動し、[グループ ポリシー管理]、[ドメイン]、[グループ ポリシー オブジェクト]、[既定のポリシー] または [Active Domain ポリシー] の順に選択します。

    2. **グループ ポリシー管理エディター** ポップアップ ウィンドウで、[コンピューターの構成]、[ポリシー]、[Windows 設定]、[セキュリティ設定]、[ローカル ポリシー]、[セキュリティ オプション] の順に選択し、**[ネットワーク セキュリティ: Kerberos で許可する暗号化の種類を構成する]** を設定します。

    3. KDC に接続するときに使用する暗号化アルゴリズムを選択します。 通常は、単純にすべてのオプションを選択できます。

        ![Kerberos での暗号化の種類の構成](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. **Ksetup** コマンドを使用して、特定の領域で使用される暗号化アルゴリズムを指定します。

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Windows ドメインで Kerberos プリンシパルを使用するために、ドメイン アカウントと Kerberos プリンシパル間のマッピングを作成します。

    1. 管理ツールを起動し、**[Active Directory ユーザーとコンピュータ]** を選択します。

    2. **[ビュー]**  >  **[高度な機能]** をクリックして、高度な機能を構成します。

    3. マッピングを作成するアカウントを見つけます。そのアカウントをクリックして **[名前のマッピング]**を表示し、**[Kerberos 名]** タブをクリックします。

    4. 領域からプリンシパルを追加します。

        ![マップ セキュリティ ID](media/connector-hdfs/map-security-identity.png)

**セルフホステッド統合ランタイム コンピューター上で:**

* 次の **Ksetup** コマンドを実行して、領域エントリを追加します。

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Azure Data Factory で以下を実行します。**

* ドメイン アカウントまたは Kerberos プリンシパルのいずれかを使用して **Windows 認証** を行って HDFS データ ソースに接続するように HDFS コネクタを構成します。 構成の詳細については、「[HDFS のリンクされたサービスのプロパティ](#linked-service-properties)」セクションを参照してください。


## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。