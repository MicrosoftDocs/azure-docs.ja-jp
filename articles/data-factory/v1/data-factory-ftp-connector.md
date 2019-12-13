---
title: Azure Data Factory を使用して FTP サーバーからデータを移動する
description: Azure Data Factory を使用して FTP サーバーからデータを移動する方法を説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 55c8bf2210eb0990a91aeff1f90e4af4db2c22ab
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927178"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Azure Data Factory を使用して FTP サーバーからデータを移動する
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](data-factory-ftp-connector.md)
> * [バージョン 2 (最新バージョン)](../connector-ftp.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[V2 の FTP コネクタ](../connector-ftp.md)に関するページを参照してください。

この記事では、Azure Data Factory のコピー アクティビティを使って、FTP サーバーからデータを移動する方法について説明します。 この記事は、コピー アクティビティによるデータ移動の一般的な概要について説明している、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事に基づいています。

FTP サーバーから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)の表をご覧ください。 データ ファクトリは、他のデータ ストアから FTP サーバーへのデータの移動ではなく、FTP サーバーから他のデータ ストアへのデータの移動のみをサポートします。 オンプレミスとクラウド FTP サーバーの両方をサポートします。

> [!NOTE]
> コピー アクティビティでは、コピー先にコピーされた後にソース ファイルが削除されることはありません。 コピー後にソース ファイルを削除する必要がある場合、カスタム アクティビティを作成してファイルを削除し、パイプラインのアクティビティを使用します。

## <a name="enable-connectivity"></a>接続性の確保
データを**オンプレミス**の FTP サーバーからクラウド データ ストア (例: Azure Blob ストレージ) に移動する場合は、Data Management Gateway をインストールして使用します。 Data Management Gateway はオンプレミスのコンピューターにインストールされたクライアント エージェントで、これにより、クラウド サービスはオンプレミスのリソースに接続できます。 詳細については、[データ管理ゲートウェイ](data-factory-data-management-gateway.md)をご覧ください。 ゲートウェイの設定とその使用に関する手順は、[オンプレミスの場所とクラウド間のデータ移動](data-factory-move-data-between-onprem-and-cloud.md)を参照してください。 サーバーが Azure IaaS 仮想マシン (VM) 上にある場合でも、FTP サーバーに接続するためにゲートウェイを使用します。

FTP サーバーとして、同じオンプレミスのコンピューターまたは IaaS VM にゲートウェイをインストールすることが可能です。 ただし、リソースの競合を防ぎ、パフォーマンスの向上を図るために、別のコンピューターまたは IaaS VM にゲートウェイをインストールすることをお勧めします。 別のコンピューターにゲートウェイをインストールすると、そのコンピューターが FTP サーバーにアクセスできるようになります。

## <a name="get-started"></a>作業開始
さまざまなツールまたは API を使用して、FTP ソースからデータを移動するコピー アクティビティを含むパイプラインを作成できます。

パイプラインを作成する最も簡単な方法は、**Data Factory コピー ウィザード**を使うことです。 手順については、「[チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」で、簡単なチュートリアルをご覧いただけます。

また、次のツールを使用してパイプラインを作成することもできます。**Visual Studio**、**PowerShell**、**Azure Resource Manager テンプレート**、 **.NET API**、**REST API**。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。

ツールと API のいずれを使用する場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。

1. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。
2. コピー操作用の入力データと出力データを表す**データセット**を作成します。
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む**パイプライン**を作成します。

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 (.NET API を除く) ツールまたは API を使う場合は、JSON 形式でこれらの Data Factory エンティティを定義します。 FTP データ ストアからデータをコピーするために使用される Data Factory エンティティ用の JSON 定義のサンプルについては、この記事の「[JSON の使用例: FTP サーバーから Azure BLOB にデータをコピーする](#json-example-copy-data-from-ftp-server-to-azure-blob)」セクションを参照してください。

> [!NOTE]
> サポートされているファイルと使用する圧縮形式に関する詳細は、[Azure Data Factory のファイルおよび圧縮形式](data-factory-supported-file-and-compression-formats.md)を参照してください。

次のセクションでは、FTP に固有の Data Factory エンティティの定義に使用される JSON プロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
次の表は、FTP リンク サービスに固有の JSON 要素について説明しています。

| プロパティ | 説明 | 必須 | Default |
| --- | --- | --- | --- |
| type |FtpServer に設定します。 |はい |&nbsp; |
| host |FTP サーバーの名前または IP アドレスを指定します。 |はい |&nbsp; |
| authenticationType |認証の種類を指定します。 |はい |Basic、Anonymous |
| username |FTP サーバーへのアクセスを持つユーザーを指定します。 |いいえ |&nbsp; |
| password |ユーザー (username) のパスワードを指定します。 |いいえ |&nbsp; |
| encryptedCredential |FTP サーバーにアクセスするための暗号化された資格情報を指定します。 |いいえ |&nbsp; |
| gatewayName |オンプレミスの FTP サーバーに接続するための Data Management Gateway のゲートウェイの名前を指定します。 |いいえ |&nbsp; |
| port |FTP サーバーがリッスンしているポートを指定します。 |いいえ |21 |
| enableSsl |SSL/TLS チャネル上の FTP を使用するかどうかを指定します。 |いいえ |true |
| enableServerCertificateValidation |FTP over SSL/TLS チャネルを使用しているときにサーバーの SSL 証明書の検証を有効にするかどうかを指定します。 |いいえ |true |

>[!NOTE]
>FTP コネクタでは、暗号化なしまたは明示的な SSL/TLS 暗号化での FTP サーバーへのアクセスがサポートされています。暗黙的な SSL/TLS 暗号化はサポートされていません。

### <a name="use-anonymous-authentication"></a>匿名認証を使用

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>基本認証のためユーザー名とパスワードをプレーン テキストで使用

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>ポート、enableSsl、enableServerCertificateValidation を使用

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>認証およびゲートウェイに encryptedCredential を使用

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ
データセットの定義に使用できるセクションとプロパティの完全な一覧については、[データセットの作成](data-factory-create-datasets.md)をご覧ください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型でほぼ同じです。

**typeProperties** セクションは、データセットの型ごとに異なります。 これは、データセット型に固有の情報を提供します。 **FileShare** 型のデータセットの **typeProperties** セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| folderPath |フォルダーへのサブパス。 文字列内の特殊文字にはエスケープ文字 "\" を使用します。 例については、「サンプルのリンクされたサービスとデータセットの定義」を参照してください。<br/><br/>このプロパティを **partitionBy** と組み合わせて、スライスの開始および終了日時に基づくフォルダー パスを使用できます。 |はい |
| fileName |テーブルでフォルダー内の特定のファイルを参照するには、**folderPath** にファイルの名前を指定します。 このプロパティの値を設定しない場合、テーブルはフォルダー内のすべてのファイルを参照します。<br/><br/>出力データセットに **fileName** が指定されていない場合、生成されるファイルの名前は次の形式になります。 <br/><br/>`Data.<Guid>.txt` (例:Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |いいえ |
| fileFilter |すべてのファイルではなく、**folderPath** 内のファイルのサブセットを選択するために使用するフィルターを指定します。<br/><br/>使用可能な値: `*` (複数の文字) および `?` (単一の文字)。<br/><br/>例 1: `"fileFilter": "*.log"`<br/>例 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** は FileShare 入力データセットに適用されます。 このプロパティは、Hadoop 分散ファイル システム (HDFS) ではサポートされていません。 |いいえ |
| partitionedBy |時系列データに動的な **folderPath** と **fileName** を指定するために使用します。 たとえば、毎時間のデータとしてパラメーター化されている **folderPath** を指定できます。 |いいえ |
| format | 次の種類の形式がサポートされます:**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text Format](data-factory-supported-file-and-compression-formats.md#text-format)、[Json Format](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)、[Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) の各セクションを参照してください。 <br><br> コピーしたいファイルがファイルベース ストア間である場合 (バイナリ コピー) は、入力と出力の両方のデータセット定義で format セクションをスキップします。 |いいえ |
| compression | データの圧縮の種類とレベルを指定します。 サポートされる種類は **GZip**、**Deflate**、**BZip2**、**ZipDeflate** です。サポートされるレベルは **Optimal** と **Fastest** です。 詳細については、「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md#compression-support)」を参照してください。 |いいえ |
| useBinaryTransfer |バイナリ転送モードを使用するかどうかを指定します。 値は、バイナリ モードの場合は true (既定値)、ASCII の場合は false です。 このプロパティを使用できるのは、関連するリンクされたサービスの種類がFtpServer の場合のみです。 |いいえ |

> [!NOTE]
> **fileName** と **fileFilter** は、同時に使用することができません。

### <a name="use-the-partionedby-property"></a>partitionedBy プロパティの使用
前のセクションで説明したように、**partitionedBy** プロパティを使用して時系列データに動的な **folderPath** および **fileName** を指定できます。

時系列データセット、スケジュール作成、スライスの詳細については、[データセットの作成](data-factory-create-datasets.md)、[スケジュール設定と実行](data-factory-scheduling-and-execution.md)、および[パイプラインの作成](data-factory-create-pipelines.md)を参照してください。

#### <a name="sample-1"></a>サンプル 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
この例では、{Slice} が Data Factory システム変数 SliceStart の値に、指定された形式 (YYYYMMDDHH) で置き換えられます。 SliceStart はスライスの開始時刻です。 フォルダーパスはスライスごとに異なります。 (例: wikidatagateway/wikisampledataout/2014100103 または wikidatagateway/wikisampledataout/2014100104)

#### <a name="sample-2"></a>サンプル 2

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
この例では、SliceStart の年、月、日、時刻が **folderPath** プロパティと **fileName** プロパティで使用される個別の変数に抽出されます。

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、type プロパティはソースとシンクの種類によって異なります。

コピー アクティビティで、source が **FileSystemSource** 型の場合は、**typeProperties** セクションで次のプロパティを使用できます:

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| recursive |データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 |True、False (既定値) |いいえ |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>JSON の使用例:FTP サーバーから Azure BLOB にデータをコピーする
このサンプルは、FTP サーバーから Azure Blob ストレージにデータをコピーする方法を示します。 ただし、Data Factory のコピー アクティビティを使用して、[サポートされるデータ ストアと形式](data-factory-data-movement-activities.md#supported-data-stores-and-formats)で説明されているシンクのいずれかにデータを直接コピーすることができます。

以下の例は、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) または[ Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。

* [FtpServer](#linked-service-properties) 型のリンクされたサービス
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス
* [FileShare](#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)
* [FileSystemSource](#copy-activity-properties) および [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)

このサンプル データでは、1 時間おきに FTP サーバーから Azure BLOB にデータがコピーされます。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

### <a name="ftp-linked-service"></a>FTP のリンクされたサービス

この例では、ユーザー名とパスワードをプレーン テキストで使用した基本認証を使用します。 また、次のいずれかの方法を使用することもできます。

* 匿名認証
* 資格情報が暗号化された基本認証
* FTP over SSL/TLS (FTPS)

使用可能なさまざまな種類の認証については、[FTP のリンクされたサービス](#linked-service-properties)に関するセクションを参照してください。

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```
### <a name="azure-storage-linked-service"></a>Azure Storage のリンクされたサービス

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
### <a name="ftp-input-dataset"></a>FTP入力データセット

このデータセットは FTP フォルダー `mysharedfolder` とファイル `test.csv` を参照します。 パイプラインにより、ファイルがコピー先にコピーされます。

**external** を **true** に設定すると、データセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

### <a name="azure-blob-output-dataset"></a>Azure BLOB の出力データセット

データは新しい BLOB に 1 時間おきに書き込まれます (frequency: hour、interval: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>ファイル システム ソースおよび BLOB シンクを使用するパイプラインでのコピー アクティビティ

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **FileSystemSource** に設定され、**sink** 型が **BlobSink** に設定されています。

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
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
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> ソース データセット列からシンク データセット列へのマッピングについては、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順
次の記事を参照してください。

* Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」をご覧ください。

* コピー アクティビティを使用したパイプライン作成の詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。
