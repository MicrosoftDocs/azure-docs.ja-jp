---
title: Azure Data Factory を使用して SAP Business Warehouse からデータを移動する
description: Azure Data Factory を使用して SAP Business Warehouse データベースからデータを移動する方法を説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 108bdf057cd375e28b10a6838ec5c8c6f57749a8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929227"
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Azure Data Factory を使用した SAP Business Warehouse からのデータ移動
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください。"]
> * [Version 1](data-factory-sap-business-warehouse-connector.md)
> * [バージョン 2 (最新バージョン)](../connector-sap-business-warehouse.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[V2 の SAP Business Warehouse コネクタ](../connector-sap-business-warehouse.md)に関するページを参照してください。


この記事では、Azure Data Factory のコピー アクティビティを使って、オンプレミスの SAP Business Warehouse (BW) からデータを移動させる方法について説明します。 この記事は、コピー アクティビティによるデータ移動の一般的な概要について説明している、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事に基づいています。

オンプレミスの SAP Business Warehouse データ ストアから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)の表をご覧ください。 Data Factory は、現時点では SAP Business Warehouse から他のデータ ストアへの移動のみをサポートし、他のデータ ストアから SAP Business Warehouse へのデータ移動に関してはサポートしていません。 

## <a name="supported-versions-and-installation"></a>サポートされているバージョンとインストール
このコネクタは、SAP Business Warehouse バージョン 7.x のほか、 MDX クエリを使用した、InfoCubes および QueryCubes (BEx クエリなど) からのデータ コピーをサポートしています。

SAP BW への接続を有効にするには、次のコンポーネントをインストールします。
- **Data Management Gateway**:Data Factory サービスは、Data Management Gateway と呼ばれるコンポーネントを使用した、オンプレミスのデータ ストア (SAP Business Warehouse を含む) への接続をサポートしています。 Data Management Gateway の詳細およびゲートウェイの設定手順については、[オンプレミスのデータ ストアとクラウド データ ストアの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事をご覧ください。 SAP Business Warehouse が Azure IaaS 仮想マシン (VM) でホストされている場合でも、ゲートウェイは必要です。 ゲートウェイはデータ ストアと同じ VM にインストールできるほか、ゲートウェイがデータベースに接続できれば別の VM にインストールしてもかまいません。
- **SAP NetWeaver ライブラリ**: ゲートウェイ コンピューターにインストールします。 SAP Netweaver ライブラリは SAP 管理者から入手します。また、[SAP ソフトウェアのダウンロード センター](https://support.sap.com/swdc)から直接取得することもできます。 最新のバージョンをダウンロードできる場所は、"**SAP Note #1025361**" を検索して確認してください。 SAP NetWeaver ライブラリ (32 ビットまたは 64 ビット) のアーキテクチャが、ゲートウェイのインストールと一致していることを確認してから、 SAP Note に従って、SAP NetWeaver RFC SDK に含まれるすべてのファイルをインストールします。 SAP NetWeaver ライブラリは、SAP Client Tools のインストールにも含まれます。

> [!TIP]
> NetWeaver RFC SDK から抽出した dll を system32 フォルダーに格納します。

## <a name="getting-started"></a>使用の開始
さまざまなツールまたは API を使用して、オンプレミスの Cassandra データ ストアからデータを移動するコピー アクティビティでパイプラインを作成できます。 

- パイプラインを作成する最も簡単な方法は、**コピー ウィザード**を使うことです。 手順については、「[チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」を参照してください。データのコピー ウィザードを使用してパイプラインを作成する簡単なチュートリアルです。 
- また、次のツールを使用してパイプラインを作成することもできます。**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、 **.NET API**、**REST API**。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。 

ツールと API のいずれを使用する場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。

1. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。
2. コピー操作用の入力データと出力データを表す**データセット**を作成します。 
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む**パイプライン**を作成します。 

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 (.NET API を除く) ツールまたは API を使う場合は、JSON 形式でこれらの Data Factory エンティティを定義します。  オンプレミスの SAP Business Warehouse からデータをコピーするために使用される Data Factory エンティティに関する JSON 定義のサンプルについては、この記事の「[JSON の使用例: SAP Business Warehouse から Azure BLOB にデータをコピーする](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob)」のセクションを参照してください。 

次のセクションでは、SAP BW データ ストアに固有の Data Factory エンティティの定義に使用される JSON プロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
次の表は、SAP Business Warehouse (BW) のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
server | SAP BW インスタンスが存在するサーバーの名前。 | string | はい
systemNumber | SAP BW システムのシステムの数。 | 2 桁の 10 進数の文字列として表されます。 | はい
clientId | SAP BW システム内のクライアントのクライアント ID。 | 3 桁の 10 進数の文字列として表されます。 | はい
username | SAP サーバーにアクセスするユーザーの名前 | string | はい
password | ユーザーのパスワード。 | string | はい
gatewayName | Data Factory サービスが、オンプレミスの SAP BW インスタンスへの接続に使用するゲートウェイの名前。 | string | はい
encryptedCredential | 暗号化された資格情報の文字列。 | string | いいえ

## <a name="dataset-properties"></a>データセットのプロパティ
データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **RelationalTable** 型の SAP BW データセットに対して、サポートされている型固有のプロパティはありません。 


## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」という記事を参照してください。 名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります。

コピー アクティビティのソースの種類が **RelationalSource** (SAP BW を含む) である場合は、typeProperties セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query | SAP BW インスタンスからデータを読み取る MDX クエリを指定します。 | MDX クエリ。 | はい |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>JSON の使用例:SAP Business Warehouse から Azure BLOB にデータをコピーする
次の例は、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 このサンプルは、オンプレミスの SAP Business Warehouse からAzure Blob Storage にデータをコピーする方法を示しています。 Azure Data Factory のコピー アクティビティを使用して、 **こちら** に記載されているシンクのいずれかにデータを [直接](data-factory-data-movement-activities.md#supported-data-stores-and-formats) コピーすることもできます。  

> [!IMPORTANT]
> このサンプルでは、JSON のスニペットを使用します。 データ ファクトリを作成する手順は含まれてません。 手順については、記事「 [Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する](data-factory-move-data-between-onprem-and-cloud.md) 」を参照してください。

このサンプルでは、次の Data Factory のエンティティがあります。

1. [SapBw](#linked-service-properties) 型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
3. [RelationalTable](#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [RelationalSource](#copy-activity-properties) と [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)

このサンプルでは、SAP Business Warehouse インスタンスから Azure BLOB に 1 時間おきにデータをコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

最初の手順として、データ管理ゲートウェイを設定します。 設定手順は、 [オンプレミスの場所とクラウドの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md) に関する記事に記載されています。

### <a name="sap-business-warehouse-linked-service"></a>SAP Business Warehouse のリンクされたサービス
このリンクされたサービスでは、SAP BW インスタンスをデータ ファクトリにリンクします。 type プロパティは **SapBw** に設定されます。 typeProperties セクションは、SAP BW インスタンスの接続情報を示しています。 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure Storage のリンクされたサービス
このリンクされたサービスでは、Azure ストレージ アカウントをデータ ファクトリにリンクします。 type プロパティは **AzureStorage** に設定されます。 typeProperties セクションは、Azure ストレージ アカウントの接続情報を示しています。

```json
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

### <a name="sap-bw-input-dataset"></a>SAP BW 入力データセット
このデータセットでは、SAP Business Warehouse を定義します。 Data Factory データセットの型は **RelationalTable** に設定します。 現時点では、SAP BW データセットに対して型固有のプロパティは指定しません。 コピー アクティビティ定義のクエリでは、SAP BW インスタンスからどのデータを読み取るかが指定されます。 

external プロパティを true に設定すると、このテーブルが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

頻度と間隔のプロパティでは、スケジュールを定義します。 この場合、データは 1 時間ごとに SAP BW インスタンスから読み取られます。 

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```



### <a name="azure-blob-output-dataset"></a>Azure BLOB の出力データセット
このデータセットでは、出力 Azure BLOB データセットを定義します。 type プロパティは AzureBlob に設定されます。 typeProperties セクションは、SAP BW インスタンスからコピーしたデータの格納場所を示しています。 データは、1 時間おきに新しい BLOB に書き込まれます (頻度: 時間、間隔: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>コピー アクティビティのあるパイプライン
パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **RelationalSource** に設定され (SAP BW ソースの場合)、**sink** 型が **BlobSink** に設定されています。 **query** プロパティに指定されているクエリは、過去のデータを選択してコピーします。

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<MDX query for SAP BW>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapBwDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```



### <a name="type-mapping-for-sap-bw"></a>SAP BW の型マッピング
[データ移動アクティビティ](data-factory-data-movement-activities.md) に関する記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

SAP BW からデータを移動する場合、SAP BW 型から .NET 型に対する次のマッピングが使用されます。

ABAP ディクショナリのデータ型 | .NET データ型
-------------------------------- | --------------
ACCP |  int
CHAR | string
CLNT | string
CURR | Decimal
CUKY | string
DEC | Decimal
FLTP | Double
INT1 | Byte
INT2 | Int16
INT4 | int
LANG | string
LCHR | string
LRAW | Byte[]
PREC | Int16
QUAN | Decimal
RAW | Byte[]
RAWSTRING | Byte[]
STRING | string
UNIT | string
DATS | string
NUMC | string
TIMS | string

> [!NOTE]
> ソース データセット列からシンク データセット列へのマッピングについては、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページをご覧ください。


## <a name="map-source-to-sink-columns"></a>ソース列からシンク列へのマップ
ソース データセット列のシンク データセット列へのマッピングの詳細については、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページをご覧ください。

## <a name="repeatable-read-from-relational-sources"></a>リレーショナル ソースからの反復可能読み取り
リレーショナル データ ストアからデータをコピーする場合は、意図しない結果を避けるため、再現性に注意する必要があります。 Azure Data Factory では、スライスを手動で再実行できます。 障害が発生したときにスライスを再実行できるように、データセットの再試行ポリシーを構成することもできます。 いずれかの方法でスライスが再実行された際は、何度スライスが実行されても同じデータが読み取られることを確認する必要があります。 [リレーショナル ソースからの反復可能読み取り](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)に関するページをご覧ください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。
