---
title: "Azure Data Factory を使用した DB2 からのデータ移動 | Microsoft Docs"
description: "Azure Data Factory を使用して DB2 データベースからデータを移動する方法を説明します"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 59a83a62ddee89c44533060b811bc8fc2f144bee
ms.lasthandoff: 03/29/2017


---
# <a name="move-data-from-db2-using-azure-data-factory"></a>Azure Data Factory を使用して DB2 からデータを移動する
この記事では、Azure Data Factory のコピー アクティビティを使用して、オンプレミスの DB2 データベースから、[サポートされているソースとシンク](data-factory-data-movement-activities.md#supported-data-stores-and-formats)に関するセクションのシンクの欄に一覧表示されている任意のデータ ストアに、データをコピーする方法について説明します。 この記事は、「 [データ移動アクティビティ](data-factory-data-movement-activities.md) 」という記事に基づき、コピー アクティビティによるデータ移動の一般概要とサポートされるデータ ストアの組み合わせについて紹介しています。

Data Factory が現在サポートしているのは、DB2 データベースから[サポートされているシンク データ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)へのデータの移動だけで、他のデータ ストアから DB2 データベースへの移動はサポートしていません。

## <a name="prerequisites"></a>前提条件
Azure Data Factory サービスをオンプレミスの DB2 データベースに接続できるようにするには、データベースとのリソースの競合を避けるため、データベースをホストするコンピューターと同じコンピューターまたは別のコンピューターに Data Management Gateway をインストールする必要があります。 Data Management Gateway は、安全かつ管理された方法でオンプレミスのデータ ソースをクラウド サービスに接続するコンポーネントです。 Data Management Gateway の詳細については、「 [Data Management Gateway](data-factory-data-management-gateway.md) 」をご覧ください。 データを移動するデータ パイプラインにゲートウェイをセットアップする手順については、[オンプレミスからクラウドへのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事をご覧ください。

DB2 データベースが Azure IaaS VM などのクラウドでホストされている場合でも、データベースへの接続にはゲートウェイを使用する必要があります。 ゲートウェイは、データベースをホストする VM で使用できます。また、そのゲートウェイがデータベースに接続できれば別の VM で使用することもできます。  

Data Management Gateway では組み込みの DB2 ドライバーが提供されているため、DB2 からデータをコピーするときにドライバーを手動でインストールする必要はありません。

> [!NOTE]
> 接続/ゲートウェイに関する問題のトラブルシューティングのヒントについては、 [ゲートウェイの問題のトラブルシューティング](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) に関するセクションをご覧ください。

## <a name="supported-versions"></a>サポートされているバージョン
この DB2 コネクタでは、分散型リレーショナル データベース アーキテクチャ (DRDA) SQL アクセス マネージャー (SQLAM) バージョン 9、10、および 11 に対応した、次の IBM DB2 のプラットフォームとバージョンをサポートしています。

* IBM DB2 for z/OS 11.1
* IBM DB2 for z/OS 10.1
* IBM DB2 for i 7.2
* IBM DB2 for i 7.1
* IBM DB2 for LUW 11
* IBM DB2 for LUW 10.5
* IBM DB2 for LUW 10.1

## <a name="getting-started"></a>使用の開始
さまざまなツール/API を使用して、オンプレミスの DB2 データ ストアからデータを移動するコピー アクティビティを含むパイプラインを作成できます。 

- パイプラインを作成する最も簡単な方法は、**コピー ウィザード**を使うことです。 データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「 [チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md) 」をご覧ください。 
- **Azure Portal**、**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、**.NET API**、**REST API** などのツールを使ってパイプラインを作成することもできます。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。 

ツールまたは API のいずれを使用した場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。

1. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。
2. コピー操作用の入力データと出力データを表す**データセット**を作成します。 
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む**パイプライン**を作成します。 

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 ツール/API (.NET API を除く) を使う場合、こうした Data Factory エンティティは、JSON 形式で定義します。  オンプレミスの DB2 データ ストアからデータをコピーするときに使用する Data Factory エンティティの JSON 定義のサンプルについては、この記事の「[JSON の使用例: DB2 から Azure BLOB へのデータのコピー](#json-example-copy-data-from-db2-to-azure-blob)」を参照してください。 

次のセクションでは、DB2 データ ストアに固有のデータ ファクトリ エンティティの定義に使用される JSON プロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
次の表は、DB2 のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティを **OnPremisesDB2** |はい |
| server |DB2 サーバーの名前です。 |はい |
| database |DB2 データベースの名前です。 |はい |
| schema |データベース内のスキーマの名前です。 スキーマ名は、大文字と小文字が区別されます。 |なし |
| authenticationType |DB2 データベースへの接続に使用される認証の種類です。 Anonymous、Basic、Windows のいずれかの値になります。 |はい |
| username |Basic または Windows 認証を使用している場合は、ユーザー名を指定します。 |いいえ |
| パスワード |ユーザー名に指定したユーザー アカウントのパスワードを指定します。 |いいえ |
| gatewayName |Data Factory サービスが、オンプレミスの DB2 データベースへの接続に使用するゲートウェイの名前です。 |はい |


## <a name="dataset-properties"></a>データセットのプロパティ
データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

typeProperties セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 RelationalTable 型のデータセット (DB2 データセットを含む) の typeProperties セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |リンクされたサービスが参照する DB2 データベース インスタンスのテーブルの名前です。 tableName は、大文字と小文字が区別されます。 |いいえ (**RelationalSource** の **クエリ** が指定されている場合) |

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの typeProperties セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります。

コピー アクティビティで、source の種類が **RelationalSource** (DB2 を含む) である場合は、typeProperties セクションで次のプロパティを使用できます:

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query |カスタム クエリを使用してデータを読み取ります。 |SQL クエリ文字列。 (例: `"query": "select * from "MySchema"."MyTable""`)。 |いいえ (**データセット**の **tableName** が指定されている場合) |

> [!NOTE]
> スキーマ名とテーブル名は、大文字と小文字が区別されます。 クエリ内では、名前を "" (二重引用符) で囲みます。  

**例:**

```sql
"query": "select * from "DB2ADMIN"."Customers""
```


## <a name="json-example-copy-data-from-db2-to-azure-blob"></a>JSON の使用例: DB2 から Azure BLOB へのデータのコピー
次の例は、[Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)、または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 ここでは、DB2 データベースから Azure Blob Storage にデータをコピーする方法を示します。 ただし、Azure Data Factory のコピー アクティビティを使用して、 [こちら](data-factory-data-movement-activities.md#supported-data-stores-and-formats) に記載されているシンクのいずれかにデータをコピーすることができます。

このサンプルでは、次の Data Factory のエンティティがあります。

1. [OnPremisesDb2](data-factory-onprem-db2-connector.md#linked-service-properties)型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
3. [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) と [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)

このサンプルは DB2 データベースのクエリ結果のデータを Azure BLOB に 1 時間ごとにコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

最初の手順として、Data Management Gateway をインストールして構成します。 設定手順は、 [オンプレミスの場所とクラウドの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md) に関する記事に記載されています。

**DB2 のリンクされたサービス:**

```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Azure BLOB ストレージのリンクされたサービス:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**DB2 の入力データセット:**

このサンプルでは、DB2 で「MyTable」という名前のテーブルを作成し、時系列データ用に「timestamp」という名前の列が含まれているものと想定しています。

"external": true の設定により、このデータセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。 **type** が **RelationalTable** に設定されている点に注目してください。

```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Azure BLOB の出力データセット:**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**コピー アクティビティのあるパイプライン:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **RelationalSource** に設定され、**sink** 型が **BlobSink** に設定されています。 **query** プロパティに指定された SQL クエリは、Orders テーブルからデータを選択します。

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
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
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```


## <a name="type-mapping-for-db2"></a>DB2 の型マッピング
[データ移動アクティビティ](data-factory-data-movement-activities.md) に関する記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

DB2 にデータを移動する場合、DB2 型から .NET 型に対する次のマッピングが使用されます。

| DB2 データベース型 | .NET Framework 型 |
| --- | --- |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numeric |Decimal |
| Date |Datetime |
| Time |TimeSpan |
| Timestamp |Datetime |
| xml |Byte[] |
| Char |String |
| VarChar |String |
| LongVarChar |String |
| DB2DynArray |String |
| Binary |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Graphic |String |
| VarGraphic |String |
| LongVarGraphic |String |
| Clob |String |
| BLOB |Byte[] |
| DbClob |String |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numeric |Decimal |
| Date |Datetime |
| Time |TimeSpan |
| Timestamp |Datetime |
| xml |Byte[] |
| Char |String |

## <a name="map-source-to-sink-columns"></a>ソース列からシンク列へのマップ
ソース データセット列からシンク データセット列へのマッピングの詳細については、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページをご覧ください。

## <a name="repeatable-read-from-relational-sources"></a>リレーショナル ソースからの反復可能な読み取り
リレーショナル データ ストアからデータをコピーする場合は、意図しない結果を避けるため、再現性に注意する必要があります。 Azure Data Factory では、スライスを手動で再実行できます。 障害が発生したときにスライスを再実行できるように、データセットの再試行ポリシーを構成することもできます。 いずれかの方法でスライスが再実行された際は、何度スライスが実行されても同じデータが読み取られることを確認する必要があります。 「[リレーショナル ソースからの反復可能な読み取り](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)」を参照してください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。

