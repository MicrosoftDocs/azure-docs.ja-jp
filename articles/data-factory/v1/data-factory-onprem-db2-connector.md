---
title: Azure Data Factory を使用して DB2 からデータを移動する
description: Azure Data Factory のコピー アクティビティを使用して、オンプレミスの DB2 データベースからデータを移動する方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e5d2c6b0460c3a7566adb17601aceb57e57f4d0b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931782"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Azure Data Factory のコピー アクティビティを使用した DB2 からのデータ移動
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](data-factory-onprem-db2-connector.md)
> * [バージョン 2 (最新バージョン)](../connector-db2.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[V2 の DB2 コネクタ](../connector-db2.md)に関するページをご覧ください。


この記事では、Azure Data Factory のコピー アクティビティを使用して、オンプレミスの DB2 データベースからデータ ストアにデータをコピーする方法について説明します。 データは、[Data Factory のデータ移動アクティビティ](data-factory-data-movement-activities.md#supported-data-stores-and-formats)に関する記事でサポートされているシンクとして挙げられているストアにコピーできます。 Data Factory についてのこの記事は、本トピックのベースになっており、コピー アクティビティを使ったデータ移動の概要や、サポートされるデータ ストアの組み合わせの一覧を掲載しています。 

現在 Data Factory でサポートされているのは、DB2 データベースから[サポートされているシンク データ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)へのデータ移動だけです。 他のデータ ストアから DB2 データベースへの移動はサポートされません。

## <a name="prerequisites"></a>前提条件
Data Factory では、[データ管理ゲートウェイ](data-factory-data-management-gateway.md)を使用して、オンプレミスの DB2 データベースへの接続がサポートされます。 データを移動するためのゲートウェイ データ パイプラインを設定するための具体的な手順については、[オンプレミスからクラウドへのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事を参照してください。

DB2 が Azure IaaS VM でホストされている場合でも、ゲートウェイは必須です。 ゲートウェイは、データ ストアと同じ IaaS VM にインストールできます。 ゲートウェイがデータベースに接続できれば別の VM にインストールしてもかまいません。

データ管理ゲートウェイでは組み込みの DB2 ドライバーが提供されているため、DB2 からデータをコピーするためにドライバーを手動でインストールする必要はありません。

> [!NOTE]
> 接続とゲートウェイに関する問題のトラブルシューティングのヒントについては、[ゲートウェイの問題のトラブルシューティング](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)に関する記事を参照してください。


## <a name="supported-versions"></a>サポートされているバージョン
Data Factory DB2 コネクタでは、分散型リレーショナル データベース アーキテクチャ (DRDA) SQL アクセス マネージャー バージョン 9、10、および 11 に対応した、次の IBM DB2 のプラットフォームとバージョンがサポートされています。

* IBM DB2 for z/OS バージョン 11.1
* IBM DB2 for z/OS バージョン 10.1
* IBM DB2 for i (AS400) バージョン 7.2
* IBM DB2 for i (AS400) バージョン 7.1
* IBM DB2 for Linux, UNIX, and Windows (LUW) バージョン 11 のサポート
* IBM DB2 for LUW バージョン 10.5
* IBM DB2 for LUW バージョン 10.1

> [!TIP]
> "SQL ステートメント実行要求に対応するパッケージが見つかりませんでした。 SQLSTATE=51002 SQLCODE=-805" というエラー メッセージは、その OS の標準ユーザーでは必要なパッケージが作成されないために表示されます。 この問題を解決するには、DB2 サーバーの種類に応じて次の手順に従ってください。
> - DB2 for i (AS400):標準ユーザーの代わりにパワー ユーザーにコレクションを作成してもらったうえで、コピー アクティビティを実行します。 コレクションを作成するには、`create collection <username>` コマンドを使用します。
> - DB2 for z/OS または LUW:高い権限があるアカウント (パッケージの権限および BIND、BINDADD、GRANT EXECUTE TO PUBLIC アクセス許可を付与されたパワー ユーザーまたは管理者) を使用して、コピー アクティビティを 1 回実行します。 するとコピー時に必要なパッケージが自動作成されます。 その後、通常のユーザーに切り替えて後続のコピー操作を実行することができます。

## <a name="getting-started"></a>使用の開始
さまざまなツールと API を使用して、オンプレミスの DB2 データ ストアからデータを移動するコピー アクティビティを含むパイプラインを作成できます。 

- パイプラインを作成する最も簡単な方法は、Azure Data Factory コピー ウィザードを使うことです。 コピー ウィザードを使用してパイプラインを作成する簡単な手順については、[コピー ウィザードを使用したパイプラインの作成のチュートリアル](data-factory-copy-data-wizard-tutorial.md)に関するページを参照してください。 
- Visual Studio、Azure PowerShell、Azure Resource Manager テンプレート、.NET API、REST API などのツールを使ってパイプラインを作成することもできます。 コピー アクティビティが含まれたパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。 

ツールと API のいずれを使用する場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。

1. リンクされたサービスを作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。
2. コピー操作用の入力データと出力データを表すデータセットを作成します。 
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティが含まれたパイプラインを作成します。 

コピー ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 (.NET API を除く) ツールまたは API を使う場合は、JSON 形式でこれらの Data Factory エンティティを定義します。 JSON の例の「DB2 から Azure Blob Storage にデータをコピーする」に、オンプレミスの DB2 データ ストアからデータをコピーするときに使用する Data Factory エンティティの JSON 定義が紹介されています。

次の各セクションでは、DB2 データ ストアに固有の Data Factory エンティティの定義に使用される JSON プロパティについて詳しく説明します。

## <a name="db2-linked-service-properties"></a>DB2 のリンクされたサービスのプロパティ
次の表は、DB2 のリンクされたサービスに固有の JSON プロパティの一覧です。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| **type** |このプロパティは **OnPremisesDb2** に設定されている必要があります。 |はい |
| **server** |DB2 サーバーの名前です。 |はい |
| **database** |DB2 データベースの名前です。 |はい |
| **schema** |DB2 データベース内のスキーマの名前です。 このプロパティは、大文字と小文字が区別されます。 |いいえ |
| **authenticationType** |DB2 データベースへの接続に使用される認証の種類です。 指定できる値は、Anonymous、Basic、および Windows です。 |はい |
| **username** |Basic 認証または Windows 認証を使用する場合はユーザー アカウントの名前です。 |いいえ |
| **password** |ユーザー アカウントのパスワードです。 |いいえ |
| **gatewayName** |Data Factory サービスが、オンプレミスの DB2 データベースへの接続に使用するゲートウェイの名前です。 |はい |

## <a name="dataset-properties"></a>データセットのプロパティ
データセットの定義に使用できるセクションとプロパティの完全な一覧については、[データセットの作成](data-factory-create-datasets.md)に関する記事を参照してください。 データセット JSON の **structure**、**availability**、**policy** などのセクションは、データセットのすべての型 (Azure SQL、Azure Blob Storage、Azure Table Storage など) でほぼ同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **RelationalTable** 型のデータセット (DB2 データセットを含む) の **typeProperties** セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| **tableName** |リンクされたサービスが参照する DB2 データベース インスタンスのテーブルの名前です。 このプロパティは、大文字と小文字が区別されます。 |いいえ (種類が **RelationalSource** のコピー アクティビティの **query** プロパティが指定されている場合) |

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
コピー アクティビティの定義に使用できるセクションとプロパティの一覧については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 コピー アクティビティのプロパティ (**名前**、**説明**、**入力**テーブル、**出力**テーブル、**ポリシー**など) は、あらゆる種類のアクティビティで使用できます。 アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、プロパティはデータ ソースとシンクの種類によって異なります。

コピー アクティビティで、source の種類が **RelationalSource** (DB2 を含む) である場合は、**typeProperties** セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| **query** |カスタム クエリを使用してデータを読み取ります。 |SQL クエリ文字列。 次に例を示します。`"query": "select * from "MySchema"."MyTable""` |いいえ (データセットの **tableName** プロパティが指定されている場合) |

> [!NOTE]
> スキーマ名とテーブル名は、大文字と小文字が区別されます。 query ステートメントでは、プロパティ名を "" (二重引用符) で囲んでください。

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>JSON の使用例:DB2 から Azure Blob Storage にデータをコピーする
次の例は、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 この例で紹介しているのは、DB2 データベースから Blob Storage にデータをコピーする方法ですが、 Azure Data Factory コピー アクティビティを使用すると、[サポートされている各種データ ストア シンク](data-factory-data-movement-activities.md#supported-data-stores-and-formats)にデータをコピーすることができます。

このサンプルでは、次の Data Factory のエンティティがあります。

- DB2 の [OnPremisesDb2](data-factory-onprem-db2-connector.md) 型のリンクされたサービス
- Azure Blob Storage の [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 型のリンクされたサービス
- [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)
- [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)
- [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) プロパティと [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) プロパティを使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)

このサンプルは DB2 データベースのクエリ結果のデータを Azure BLOB に 1 時間ごとにコピーします。 サンプルに使用されている JSON プロパティについては、エンティティの定義に続くセクションで説明しています。

最初の手順として、データ ゲートウェイをインストールして構成します。 これらの手順は、[オンプレミスの場所とクラウドの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事に記載されています。

**DB2 のリンクされたサービス**

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

**Azure BLOB ストレージのリンクされたサービス**

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

**DB2 の入力データセット**

このサンプルは、"MyTable" という名前のテーブルが DB2 に作成済みで、かつそのテーブルに、時系列データを格納する "timestamp" という列が含まれていることを想定しています。

**external** プロパティは "true" に設定されています。 この設定により、このデータセットがデータ ファクトリの外部にあり、データ ファクトリのアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。 **type** プロパティが **RelationalTable** に設定されている点に注目してください。


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

**Azure BLOB の出力データセット**

**frequency** プロパティを "Hour" に、**interval** プロパティを 1 に設定することによって、新しい BLOB に 1 時間おきにデータを書き込みます。 BLOB の **folderPath** プロパティは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

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

**コピー アクティビティのパイプライン**

パイプラインには、指定した入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプラインの JSON 定義で、**source** の型が **RelationalSource** に設定され、**sink** の型が **BlobSink** に設定されています。 **query** プロパティに指定された SQL クエリは、"Orders" テーブルからデータを選択します。

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
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
[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

コピー アクティビティでデータを DB2 型から .NET 型に変換するときに、次のマッピングが使用されます。

| DB2 データベース型 | .NET Framework 型 |
| --- | --- |
| SmallInt |Int16 |
| 整数 |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numeric |Decimal |
| Date |DateTime |
| Time |TimeSpan |
| Timestamp |Datetime |
| xml |Byte[] |
| Char |string |
| VarChar |string |
| LongVarChar |string |
| DB2DynArray |string |
| Binary |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Graphic |string |
| VarGraphic |string |
| LongVarGraphic |string |
| Clob |string |
| Blob |Byte[] |
| DbClob |string |
| SmallInt |Int16 |
| 整数 |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numeric |Decimal |
| Date |DateTime |
| Time |TimeSpan |
| Timestamp |Datetime |
| xml |Byte[] |
| Char |string |

## <a name="map-source-to-sink-columns"></a>ソース列からシンク列へのマップ
ソース データセット列のシンク データセット列へのマッピング方法の詳細については、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページを参照してください。

## <a name="repeatable-reads-from-relational-sources"></a>リレーショナル ソースからの反復可能な読み取り
リレーショナル データ ストアからデータをコピーする場合は、意図しない結果を避けるため、再現性に注意する必要があります。 Azure Data Factory では、スライスを手動で再実行できます。 障害が発生したときにスライスを再実行する再試行ポリシー (**policy**) プロパティをデータセットに構成することもできます。 どのようにスライスを再実行するかに関係なく、何度スライスが実行されても同じデータが読み込まれることを確認してください。 詳細については、「[リレーショナル ソースからの反復可能な読み取り](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)」を参照してください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
コピー アクティビティのパフォーマンスに影響を及ぼす主な要因とパフォーマンスを最適化する方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。
