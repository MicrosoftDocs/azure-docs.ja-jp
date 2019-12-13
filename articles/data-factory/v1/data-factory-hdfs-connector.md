---
title: オンプレミスの HDFS からデータを移動する
description: Azure Data Factory を使用してオンプレミスの HDFS からデータを移動する方法を説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7652ab72fb972230d98913c2d7e2601737982532
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74924342"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Azure Data Factory を使用してオンプレミスの HDFS からデータを移動する
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](data-factory-hdfs-connector.md)
> * [バージョン 2 (最新バージョン)](../connector-hdfs.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[V2 の HDFS コネクタ](../connector-hdfs.md)に関するページを参照してください。

この記事では、Azure Data Factory のコピー アクティビティを使って、オンプレミスの HDFS からデータを移動する方法について説明します。 この記事は、コピー アクティビティによるデータ移動の一般的な概要について説明している、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事に基づいています。

HDFS から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)の表をご覧ください。 Data Factory が現在サポートしているのは、オンプレミスの HDFS から他のデータ ストアへのデータの移動だけで、他のデータ ストアからオンプレミスの HDFS への移動はサポートしていません。

> [!NOTE]
> コピー アクティビティでは、コピー先にコピーされた後にソース ファイルが削除されることはありません。 コピー後にソース ファイルを削除する必要がある場合、カスタム アクティビティを作成してファイルを削除し、パイプラインのアクティビティを使用します。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>接続を有効にする
Data Factory サービスでは、Data Management Gateway を使用したオンプレミスの HDFS への接続をサポートします。 Data Management Gateway の詳細およびゲートウェイの設定手順については、「 [オンプレミスの場所とクラウド間のデータ移動](data-factory-move-data-between-onprem-and-cloud.md) 」を参照してください。 Azure IaaS VM でホストされている場合でも、HDFS への接続にゲートウェイを使用します。

> [!NOTE]
> データ管理ゲートウェイが、Hadoop クラスターの**すべて**の [name node server]:[name node port] および [data node servers]:[data node port] にアクセスできることを確認します。 既定の [name node port] は 50070、既定の [data node port] は 50075 です。

ゲートウェイは同じオンプレミスのマシンまたは Azure VM に HDFS としてインストールできますが、別個のマシンおよびAzure IaaS VM にゲートウェイをインストールすることをお勧めします。 ゲートウェイを別のコンピューターにインストールすることで、リソースの競合が減少し、パフォーマンスが向上します。 別のマシンにゲートウェイをインストールすると、そのマシンが HDFS を持つマシンにアクセスできるようになります。

## <a name="getting-started"></a>使用の開始
さまざまなツール/API を使用して、HDSF ソースからデータを移動するコピー アクティビティを含むパイプラインを作成できます。

パイプラインを作成する最も簡単な方法は、**コピー ウィザード**を使うことです。 手順については、「[チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」を参照してください。データのコピー ウィザードを使用してパイプラインを作成する簡単なチュートリアルです。

また、次のツールを使用してパイプラインを作成することもできます。**Azure portal**、**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、 **.NET API**、**REST API**。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。

ツールと API のいずれを使用する場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。

1. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。
2. コピー操作用の入力データと出力データを表す**データセット**を作成します。
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む**パイプライン**を作成します。

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 (.NET API を除く) ツールまたは API を使う場合は、JSON 形式でこれらの Data Factory エンティティを定義します。  HDFS データ ストアからデータをコピーするために使用される Data Factory エンティティに関する JSON 定義のサンプルについては、この記事の「[JSON の使用例: オンプレミスの HDFS から Azure BLOB へのデータのコピー](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob)」セクションを参照してください。

次のセクションでは、HDFS に固有の Data Factory エンティティの定義に使用される JSON プロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
リンクされたサービスは、データ ストアをデータ ファクトリにリンクします。 オンプレミスの HDFS をデータ ファクトリにリンクするには、**Hdfs** 型のリンクされたサービスを作成します。 次の表は、HDFS のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティは、次のように設定する必要があります:**Hdfs** |はい |
| url |HDFS への URL |はい |
| authenticationType |Anonymous または Basic。 <br><br> HDFS コネクタに **Kerberos 認証**を使用するには、[こちらのセクション](#use-kerberos-authentication-for-hdfs-connector)を参照して、オンプレミス環境を設定します。 |はい |
| userName |Windows 認証のユーザー名。 Kerberos 認証の場合は `<username>@<domain>.com` を指定します。 |あり (Windows 認証用) |
| password |Windows 認証のパスワード。 |あり (Windows 認証用) |
| gatewayName |Data Factory サービスが、HDFS への接続に使用するゲートウェイの名前。 |はい |
| encryptedCredential |アクセス資格情報の [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) 出力。 |いいえ |

### <a name="using-anonymous-authentication"></a>匿名認証を使用する

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>Windows 認証を使用する

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```
## <a name="dataset-properties"></a>データセットのプロパティ
データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **FileShare** 型のデータセットの typeProperties セクション (HDFS データセットを含む) には次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| folderPath |フォルダーへのパス。 例: `myfolder`<br/><br/>文字列内の特殊文字にはエスケープ文字 "\" を使用します。 例: folder\subfolder には、folder\\\\subfolder を指定し、d:\samplefolder には、d:\\\\samplefolder を指定します。<br/><br/>このプロパティを **partitionBy** と組み合わせて、スライスの開始/終了日時に基づくフォルダー パスを使用できます。 |はい |
| fileName |テーブルでフォルダー内の特定のファイルを参照するには、**folderPath** にファイルの名前を指定します。 このプロパティの値を設定しない場合、テーブルはフォルダー内のすべてのファイルを参照します。<br/><br/>出力データセットに fileName が指定されていない場合、生成されるファイルの名前は次の形式になります。 <br/><br/>`Data.<Guid>.txt` (例:Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |いいえ |
| partitionedBy |partitionedBy を使用して時系列データに動的な folderPath と fileName を指定できます。 例: 1 時間ごとのデータに対して folderPath がパラメーター化されます。 |いいえ |
| format | 次の種類の形式がサポートされます:**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text Format](data-factory-supported-file-and-compression-formats.md#text-format)、[Json Format](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)、[Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) の各セクションを参照してください。 <br><br> ファイルベースのストア間で**ファイルをそのままコピー** (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。 |いいえ |
| compression | データの圧縮の種類とレベルを指定します。 サポートされる種類は、**GZip**、**Deflate**、**BZip2**、**ZipDeflate** です。 サポートされるレベルは、**Optimal** と **Fastest** です。 詳細については、「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md#compression-support)」を参照してください。 |いいえ |

> [!NOTE]
> fileName と fileFilter は、同時に使用することができません。

### <a name="using-partionedby-property"></a>partitionedBy プロパティの使用
前のセクションで説明したように、**partitionedBy** プロパティ、[Data Factory 関数、およびシステム変数](data-factory-functions-variables.md)を使用して、時系列データに動的な folderPath および filename を指定できます。

時系列データセット、スケジュール作成、スライスの詳細については、「[データセットの作成](data-factory-create-datasets.md)」、「[スケジュールと実行](data-factory-scheduling-and-execution.md)」、および「[パイプラインの作成](data-factory-create-pipelines.md)」に関する記事を参照してください。

#### <a name="sample-1"></a>サンプル 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
この例では、{Slice} は、指定された形式 (YYYYMMDDHH) で、Data Factory システム変数の SliceStart の値に置き換えられます。 SliceStart はスライスの開始時刻です。 folderPath はスライスごとに異なります。 例: wikidatagateway/wikisampledataout/2014100103 または wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>サンプル 2:

```JSON
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

一方、アクティビティの typeProperties セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります。

コピー アクティビティで、source が **FileSystemSource** 型の場合は、typeProperties セクションで次のプロパティを使用できます:

**FileSystemSource** では次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| recursive |データをサブ フォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 |True、False (既定値) |いいえ |

## <a name="supported-file-and-compression-formats"></a>サポートされているファイル形式と圧縮形式
詳細については、「[Azure Data Factory のファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md)」に関する記事を参照してください。

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>JSON の使用例:オンプレミスの HDFS から Azure BLOB へのデータのコピー
このサンプルは、オンプレミスの HDFS から Azure BLOB ストレージにデータをコピーする方法を示します。 Azure Data Factory のコピー アクティビティを使用して、 **こちら** に記載されているシンクのいずれかにデータを [直接](data-factory-data-movement-activities.md#supported-data-stores-and-formats) コピーすることもできます。  

この例は、次の Data Factory エンティティの JSON 定義を示しています。 [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) で、この定義を使用して、HDFS から Azure Blob Storage にデータをコピーするためのパイプラインを作成できます。

1. [OnPremisesHdfs](#linked-service-properties)型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
3. [FileShare](#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [FileSystemSource](#copy-activity-properties) および [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルはオンプレミスの HDFS のデータを Azure BLOB に 1 時間ごとにコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

最初の手順として、データ管理ゲートウェイを設定します。 設定手順は、 [オンプレミスの場所とクラウドの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md) に関する記事に記載されています。

**HDFS のリンクされたサービス:** この例では Windows 認証を使用します。 使用できるさまざまな種類の認証については、 [HDFS のリンクされたサービス](#linked-service-properties) に関するセクションをご覧ください。

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

**Azure Storage のリンクされたサービス:**

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

**HDFS の入力データセット:** このデータセットは HDFS フォルダー DataTransfer/UnitTest/ を参照します。 パイプラインは、このフォルダー内のすべてのファイルをコピー先にコピーします。

”external” を ”true” に設定すると、データセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Azure BLOB の出力データセット:**

データは新しい BLOB に 1 時間おきに書き込まれます (frequency: hour、interval: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**ファイル システム ソースおよび BLOB シンクを使用するパイプラインでのコピー アクティビティ:**

パイプラインには、この入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **FileSystemSource** に設定され、**sink** 型が **BlobSink** に設定されています。 **query** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>HDFS コネクタでの Kerberos 認証の使用
HDFS コネクタで Kerberos 認証を使用するようにオンプレミス環境を設定するためのオプションは 2 つあります。 ニーズに適した方法を選択できます。
* オプション 1:[ゲートウェイ マシンを Kerberos 領域に参加させる](#kerberos-join-realm)
* オプション 2:[Windows ドメインと Kerberos 領域間の相互の信頼関係を有効にする](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>オプション 1: ゲートウェイ マシンを Kerberos 領域に参加させる

#### <a name="requirement"></a>要件:

* ゲートウェイ コンピューターは Kerberos 領域に参加している必要があります。Windows ドメインには参加できません。

#### <a name="how-to-configure"></a>構成方法:

**ゲートウェイ コンピューターで以下を実行します。**

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

#### <a name="requirement"></a>要件:
*   ゲートウェイ コンピューターは、Windows ドメインに参加している必要があります。
*   ドメイン コントローラーの設定を更新できるアクセス許可が必要です。

#### <a name="how-to-configure"></a>構成方法:

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

        ![Kerberos での暗号化の種類の構成](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. **Ksetup** コマンドを使用して、特定の領域で使用される暗号化アルゴリズムを指定します。

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Windows ドメインで Kerberos プリンシパルを使用するために、ドメイン アカウントと Kerberos プリンシパル間のマッピングを作成します。

    1. 管理ツールを起動し、 **[Active Directory ユーザーとコンピュータ]** を選択します。

    2. **[ビュー]**  >  **[高度な機能]** をクリックして、高度な機能を構成します。

    3. マッピングを作成するアカウントを見つけます。そのアカウントをクリックして **[名前のマッピング]** を表示し、 **[Kerberos 名]** タブをクリックします。

    4. 領域からプリンシパルを追加します。

        ![マップ セキュリティ ID](media/data-factory-hdfs-connector/map-security-identity.png)

**ゲートウェイ コンピューターで以下を実行します。**

* 次の **Ksetup** コマンドを実行して、領域エントリを追加します。

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**Azure Data Factory で以下を実行します。**

* ドメイン アカウントまたは Kerberos プリンシパルのいずれかを使用して **Windows 認証** を行って HDFS データ ソースに接続するように HDFS コネクタを構成します。 構成の詳細については、「[HDFS のリンクされたサービスのプロパティ](#linked-service-properties)」セクションを参照してください。

> [!NOTE]
> ソース データセット列からシンク データセット列へのマッピングについては、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページをご覧ください。


## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。
