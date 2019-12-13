---
title: Azure Data Factory を使用して SFTP サーバーからデータを移動する
description: Azure Data Factory を使用してオンプレミスまたはクラウド SFTP サーバーからデータを移動する方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3f78934fb11dd4f9e34bf27d565d471d47f250b4
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928187"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Azure Data Factory を使用した SFTP サーバーからのデータ移動
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください。"]
> * [Version 1](data-factory-sftp-connector.md)
> * [バージョン 2 (最新バージョン)](../connector-sftp.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[V2 の SFTPconnector](../connector-sftp.md) に関するページを参照してください。

この記事では、Azure Data Factory のコピー アクティビティを使用して、オンプレミス/クラウドの SFTP サーバーからサポートされたシンク データ ストアにデータを移動する方法について説明します。 この記事では、「[データ移動アクティビティ](data-factory-data-movement-activities.md)」という記事に基づき、コピー アクティビティによるデータ移動の一般概要と、ソース/シンクとしてサポートされるデータ ストアの一覧を紹介します。

Data Factory が現在サポートしているのは、SFTP サーバーから他のデータ ストアへのデータの移動だけで、他のデータ ストアから SFTP サーバーへの移動はサポートしていません。 オンプレミスとクラウド SFTP サーバーの両方をサポートします。

> [!NOTE]
> コピー アクティビティでは、コピー先にコピーされた後にソース ファイルが削除されることはありません。 コピー後にソース ファイルを削除する必要がある場合、カスタム アクティビティを作成してファイルを削除し、パイプラインのアクティビティを使用します。

## <a name="supported-scenarios-and-authentication-types"></a>サポートされているシナリオと認証の種類
この SFTP コネクタを使用すると、データをコピーする**クラウド SFTP サーバーとオンプレミスの SFTP サーバーの両方**からデータをコピーできます。 SFTP サーバーに接続するときにサポートされる認証の種類は、**基本**認証と **SshPublicKey** 認証です。

オンプレミスの SFTP サーバーからデータをコピーする場合は、オンプレミスの環境/Azure VM に Data Management Gateway をインストールする必要があります。 ゲートウェイについて詳しくは、[Data Management Gateway に関する記事](data-factory-data-management-gateway.md)をご覧ください。 ゲートウェイの設定手順とその使用方法については、[オンプレミスの場所とクラウド間のデータ移動に関する記事](data-factory-move-data-between-onprem-and-cloud.md)を参照してください。

## <a name="getting-started"></a>使用の開始
さまざまなツール/API を使用して、SFTP ソースからデータを移動するコピー アクティビティを含むパイプラインを作成できます。

- パイプラインを作成する最も簡単な方法は、**コピー ウィザード**を使うことです。 手順については、「[チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」を参照してください。データのコピー ウィザードを使用してパイプラインを作成する簡単なチュートリアルです。

- また、次のツールを使用してパイプラインを作成することもできます。**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、 **.NET API**、**REST API**。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。 SFTP サーバーから Azure Blob Storage にデータをコピーする JSON サンプルについては、この記事の「[JSON の使用例:SFTP サーバーから Azure BLOB にデータをコピーする](#json-example-copy-data-from-sftp-server-to-azure-blob)」セクションを参照してください。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
次の表は、FTP のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type | type プロパティを `Sftp` に設定する必要があります。 |はい |
| host | SFTP サーバーの名前または IP アドレス。 |はい |
| port |SFTP サーバーがリッスンしているポート。 既定値は21 |いいえ |
| authenticationType |認証の種類を指定します。 使用できる値は以下の通りです。**Basic**、**SshPublicKey**。 <br><br> プロパティと JSON サンプルの詳細については、「[基本認証を使用する](#using-basic-authentication)」および「[SSH 公開キー認証を使用する](#using-ssh-public-key-authentication)」をそれぞれ参照してください。 |はい |
| skipHostKeyValidation | ホスト キーの検証をスキップするかどうかを指定します。 | No. 既定値: false |
| hostKeyFingerprint | ホスト キーの指紋を指定します。 | はい (`skipHostKeyValidation` が false に設定されている場合)。  |
| gatewayName |オンプレミスの SFTP サーバーに接続するための Data Management Gateway の名前。 | はい (オンプレミスの SFTP サーバーからデータをコピーする場合)。 |
| encryptedCredential | SFTP サーバーにアクセスするための暗号化された資格情報。 コピー ウィザードまたは ClickOnce ポップアップ ダイアログで、基本認証 (ユーザー名とパスワード) または SshPublicKey 認証 (ユーザー名と秘密キーのパスまたはコンテンツ) を指定すると自動生成されます。 | No. オンプレミスの SFTP サーバーからデータをコピーする場合にのみ適用します。 |

### <a name="using-basic-authentication"></a>基本認証を使用する

基本認証を使用するには、`authenticationType` を `Basic` に設定し、前のセクションで導入した一般的な SFTP コネクタ プロパティのほかに、次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| username | SFTP サーバーにアクセスできるユーザー。 |はい |
| password | ユーザー (username) のパスワード。 | はい |

#### <a name="example-basic-authentication"></a>例:基本認証
```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>例:暗号化された資格情報を使用する基本認証

```JSON
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
      }
}
```

### <a name="using-ssh-public-key-authentication"></a>SSH 公開キー認証を使用する

SSH 公開キー認証を使用するには、`authenticationType` を `SshPublicKey` に設定し、前のセクションで導入した一般的な SFTP コネクタ プロパティのほかに、次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| username |SFTP サーバーにアクセスできるユーザー |はい |
| privateKeyPath | ゲートウェイがアクセスできる秘密キー ファイルへの絶対パスを指定します。 | `privateKeyPath` または `privateKeyContent` を指定します。 <br><br> オンプレミスの SFTP サーバーからデータをコピーする場合にのみ適用します。 |
| privateKeyContent | 秘密キーのコンテンツのシリアル化された文字列。 コピー ウィザードでは、秘密キー ファイルを読み取り、秘密キーのコンテンツを自動的に抽出できます。 その他のツール/SDK を使用している場合は、代わりに privateKeyPath プロパティを使用します。 | `privateKeyPath` または `privateKeyContent` を指定します。 |
| passPhrase | キー ファイルがパス フレーズで保護されている場合は、パス フレーズ/パスワードを指定して、秘密キーを復号化します。 | はい (秘密キー ファイルがパス フレーズで保護されている場合)。 |

> [!NOTE]
> SFTP コネクタは RSA/DSA OpenSSH キーをサポートします。 キー ファイルの内容が "-----BEGIN [RSA/DSA] PRIVATE KEY-----" で始まることを確認します。 秘密キー ファイルが ppk 形式である場合は、Putty ツールを使用して .ppk から OpenSSH 形式に変換してください。

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>例:秘密キーの filePath を使用した SshPublicKey 認証

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>例:秘密キーのコンテンツを使用した SshPublicKey 認証

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ
データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型でほぼ同じです。

**typeProperties** セクションは、データセットの型ごとに異なります。 これは、データセット型に固有の情報を提供します。 **FileShare** 型のデータセットの typeProperties セクションには、次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| folderPath |フォルダーへのサブ パス。 文字列内の特殊文字にはエスケープ文字 "\" を使用します。 例については、「サンプルのリンクされたサービスとデータセットの定義」を参照してください。<br/><br/>このプロパティを **partitionBy** と組み合わせて、スライスの開始/終了日時に基づくフォルダー パスを使用できます。 |はい |
| fileName |テーブルでフォルダー内の特定のファイルを参照するには、**folderPath** にファイルの名前を指定します。 このプロパティの値を設定しない場合、テーブルはフォルダー内のすべてのファイルを参照します。<br/><br/>出力データセットに fileName が指定されていない場合、生成されるファイルの名前は次の形式になります。 <br/><br/>`Data.<Guid>.txt` (例:Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |いいえ |
| fileFilter |すべてのファイルではなく、folderPath 内のファイルのサブセットを選択するために使用するフィルターを指定します。<br/><br/>使用可能な値: `*` (複数の文字) および `?` (単一の文字)。<br/><br/>例 1: `"fileFilter": "*.log"`<br/>例 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter は FileShare 入力データセットに適用されます。 このプロパティは、HDFS ではサポートされません。 |いいえ |
| partitionedBy |partitionedBy を使用して時系列データに動的な folderPath と fileName を指定できます。 たとえば、1 時間ごとのデータに対して folderPath がパラメーター化されます。 |いいえ |
| format | 次の種類の形式がサポートされます:**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text Format](data-factory-supported-file-and-compression-formats.md#text-format)、[Json Format](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)、[Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) の各セクションを参照してください。 <br><br> ファイルベースのストア間で**ファイルをそのままコピー** (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。 |いいえ |
| compression | データの圧縮の種類とレベルを指定します。 サポートされる種類は、**GZip**、**Deflate**、**BZip2**、**ZipDeflate** です。 サポートされるレベルは、**Optimal** と **Fastest** です。 詳細については、「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md#compression-support)」を参照してください。 |いいえ |
| useBinaryTransfer |バイナリ転送モードを使用するかどうかを指定します。 バイナリ モードの場合は true、ASCII モードの場合は false です。 既定値:True。 このプロパティを使用できるのは、関連するリンクされたサービスの種類が FtpServer である場合だけです。 |いいえ |

> [!NOTE]
> fileName と fileFilter は、同時に使用することができません。

### <a name="using-partionedby-property"></a>partitionedBy プロパティの使用
前のセクションで説明したように、partitionedBy を使用して時系列データに動的な folderPath、fileName を指定できます。 これは、Data Factory のマクロ、および指定したデータ スライスの論理的な期間を示す システム変数 SliceStart と SliceEnd で行うことができます。

時系列データセット、スケジュール作成、スライスの詳細については、[データセットの作成](data-factory-create-datasets.md)、[スケジュール設定と実行](data-factory-scheduling-and-execution.md)、および[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。

#### <a name="sample-1"></a>サンプル 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
この例では、{Slice} は、指定された形式 (YYYYMMDDHH) で、Data Factory システム変数の SliceStart の値に置き換えられます。 SliceStart はスライスの開始時刻です。 folderPath はスライスごとに異なります。 例: wikidatagateway/wikisampledataout/2014100103 または wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>サンプル 2:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
この例では、SliceStart の年、月、日、時刻が folderPath プロパティと fileName プロパティで使用される個別の変数に抽出されます。

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」という記事を参照してください。 名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの typeProperties セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、type プロパティはソースとシンクの種類によって異なります。

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>サポートされているファイル形式と圧縮形式
詳細については、「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md)」に関する記事を参照してください。

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>JSON の使用例:SFTP サーバーから Azure BLOB にデータをコピーする
次の例は、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 これらの例は、SFTP ソースからAzure Blob Storage にデータをコピーする方法を示しています。 ただし、Azure Data Factory のコピー アクティビティを使用して、 **こちら** に記載されているいずれかのシンクに、任意のソースからデータを [直接](data-factory-data-movement-activities.md#supported-data-stores-and-formats) コピーすることができます。

> [!IMPORTANT]
> このサンプルでは、JSON のスニペットを使用します。 データ ファクトリを作成する手順は含まれてません。 手順については、記事「 [Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する](data-factory-move-data-between-onprem-and-cloud.md) 」を参照してください。

このサンプルでは、次の Data Factory のエンティティがあります。

* [sftp](#linked-service-properties) 型のリンクされたサービス。
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
* [FileShare](#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
* [FileSystemSource](#copy-activity-properties) および [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプル データでは、1 時間おきに SFTP サーバーから Azure BLOB にデータがコピーされます。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**SFTP のリンクされたサービス**

この例では、ユーザー名とパスワードをプレーン テキストで使用した基本認証を使用します。 また、次のいずれかの方法を使用することもできます。

* 資格情報が暗号化された基本認証
* SSH 公開キー認証

使用可能なさまざまな種類の認証については、[FTP のリンクされたサービス](#linked-service-properties)に関するセクションを参照してください。

```JSON

{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "myuser",
            "password": "mypassword",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```
**Azure Storage のリンクされたサービス**

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**SFTP の入力データセット**

このデータセットは `mysharedfolder` SFTP フォルダーと `test.csv` ファイルを参照します。 パイプラインにより、ファイルがコピー先にコピーされます。

"external" を "true" に設定すると、データセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

```JSON
{
  "name": "SFTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "SftpLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Azure BLOB の出力データセット**

データは新しい BLOB に 1 時間おきに書き込まれます (frequency: hour、interval: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**コピー アクティビティのあるパイプライン**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **FileSystemSource** に設定され、**sink** 型が **BlobSink** に設定されています。

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00Z",
        "end": "2017-02-20T19:00:00Z"
    }
}
```

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。

## <a name="next-steps"></a>次の手順
次の記事を参照してください。

* [コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) を参照してください。
