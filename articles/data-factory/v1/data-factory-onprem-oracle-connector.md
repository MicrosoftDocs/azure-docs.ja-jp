---
title: Data Factory を使用して Oracle との間でデータをコピーする
description: Azure Data Factory を使用してオンプレミスの Oracle データベースとの間でデータをコピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 066e32d5ab21f88b170498173606043c54fec586
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928163"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Azure Data Factory を使用してオンプレミスの Oracle との間でデータをコピーする

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください。"]
> * [Version 1](data-factory-onprem-oracle-connector.md)
> * [バージョン 2 (最新バージョン)](../connector-oracle.md)

> [!NOTE]
> この記事は、Azure Data Factory のバージョン 1 に適用されます。 現在のバージョンの Azure Data Factory サービスを使用している場合は、[V2 の Oracle コネクタ](../connector-oracle.md)に関するページを参照してください。


この記事では、Azure Data Factory のコピー アクティビティを使って、オンプレミスの Oracle データベースとの間でデータを移動させる方法について説明します。 この記事は、コピー アクティビティを使用したデータ移動の一般的な概要について説明している、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事に基づいています。

## <a name="supported-scenarios"></a>サポートされるシナリオ

*Oracle データベースから*以下のデータ ストアにデータをコピーできます。

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

以下のデータ ストアから *Oracle データベースに*データをコピーできます。

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>前提条件

Data Factory は、Data Management Gateway を使用したオンプレミスの Oracle ソースへの接続をサポートします。 Data Management Gateway の詳細については、[Data Management Gateway](data-factory-data-management-gateway.md)に関するページを参照してください。 データを移行するデータ パイプラインにゲートウェイを設定する手順については、[オンプレミスからクラウドへのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関するページを参照してください。

Azure サービスとしてのインフラストラクチャ (IaaS) VM で Oracle がホストされている場合でも、ゲートウェイは必須です。 ゲートウェイはデータ ストアと同じ IaaS VM にインストールできるほか、ゲートウェイがデータベースに接続できれば別の VM にインストールしてもかまいません。

> [!NOTE]
> 接続とゲートウェイに関連する問題のトラブルシューティングのヒントについては、[ゲートウェイの問題のトラブルシューティング](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)に関するページを参照してください。

## <a name="supported-versions-and-installation"></a>サポートされているバージョンとインストール

この Oracle コネクタでは、2 つのバージョンのドライバーがサポートされます。

- **Oracle 用 Microsoft ドライバー (推奨)** : Data Management Gateway バージョン 2.7 から、Oracle 用 Microsoft ドライバーがゲートウェイと共に自動的にインストールされます。 Oracle との接続を確立するためにドライバーをインストールまたは更新する必要はありません。 また、このドライバーを使用すると、コピーのパフォーマンスも改善されます。 サポートされている Oracle データベースのバージョンは、以下のとおりです。
  - Oracle 12c R1 (12.1)
  - Oracle 11g R1、R2 (11.1、11.2)
  - Oracle 10g R1、R2 (10.1、10.2)
  - Oracle 9i R1、R2 (9.0.1、9.2)
  - Oracle 8i R3 (8.1.7)

    > [!NOTE]
    > Oracle プロキシ サーバーはサポートされていません。

    > [!IMPORTANT]
    > 現在、Oracle 用 Microsoft ドライバーは、Oracle からのデータのコピーのみをサポートしています。 このドライバーは Oracle への書き込みをサポートしていません。 Data Managemetn Gateway の **[診断]** タブのテスト接続機能では、このドライバーはサポートされていません。 代わりに、コピー ウィザードを使用して接続を確認できます。
    >

- **Oracle Data Provider for .NET**: Oracle Data Provider を使用すると、Oracle との間でデータをコピーできます。 このコンポーネントは、 [Oracle Data Access Components for Windows](https://www.oracle.com/technetwork/topics/dotnet/downloads/)に含まれます。 ゲートウェイがインストールされているマシンに、関連するバージョン (32 ビットまたは 64 ビット) をインストールしてください。 [Oracle Data Provider .NET 12.1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) は Oracle Database 10g リリース 2 以降のバージョンにアクセスできます。

    **[XCopy Installation]\(XCopy のインストール\)** を選択した場合は、readme.htm ファイルに記載されている手順を実行します。 (XCopy のインストーラーではなく) UI があるインストーラーを選択することをお勧めします。

    プロバイダーのインストール後、サービス アプレットまたは Data Management Gateway 構成マネージャーを使用して、コンピューターで Data Management Gateway ホスト サービスを再開します。

コピー ウィザードを使用してコピー パイプラインを作成する場合は、ドライバーのタイプも自動的に決定されます。 ゲートウェイのバージョンが 2.7 より前か、シンクとして Oracle を選択していない限り、既定で Microsoft ドライバーが使用されます。

## <a name="get-started"></a>作業開始

コピー アクティビティがあるパイプラインを作成できます。 パイプラインは、異なるツールまたは API を使用して、オンプレミスの Oracle データベースとの間でデータを移動します。

パイプラインを作成する最も簡単な方法は、コピー ウィザードを使用することです。 手順については、「[チュートリアル: データのコピー ウィザードを使用してパイプラインを作成する簡単なチュートリアルについては、コピー ウィザードを使用したパイプラインの作成](data-factory-copy-data-wizard-tutorial.md)に関するチュートリアルを参照してください。

また、次のいずれかのツールを使用してパイプラインを作成することもできます。**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、 **.NET API**、**REST API**。 コピー アクティビティを使用するパイプラインを作成する詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。

ツールと API のいずれを使用する場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。

1. **Data Factory**を作成します。 データ ファクトリには、1 つまたは複数のパイプラインを設定できます。
2. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。 たとえば、Oralce データベースから Azure BLOB ストレージにデータをコピーする場合、リンクされたサービスを 2 つ作成して、Oralce データベースと Azure ストレージ アカウントをデータ ファクトリにリンクします。 Oralce に固有のリンクされたサービスのプロパティについては、「[リンクされたサービスのプロパティ](#linked-service-properties)」を参照してください。
3. コピー操作用の入力データと出力データを表す**データセット**を作成します。 前述の手順の例では、データセットを作成して入力データを含む Oralce データベース内のテーブルを指定します。 もう 1 つのデータセットを作成して、BLOB コンテナーと Oralce データベースからコピーされたデータを保持するフォルダーを指定します。 Oralce に固有のデータセットのプロパティについては、「[データセットのプロパティ](#dataset-properties)」を参照してください。
4. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティがある**パイプライン**を作成します。 前述の例では、コピー アクティビティのソースとして **OracleSource** を、シンクとして **BlobSink** を使用します。 同様に、Azure BLOB ストレージから Oracle データベースにコピーする場合は、**BlobSource** と **OracleSink** をコピー アクティビティで使います。 Oracle データベースに固有のコピー アクティビティのプロパティについては、「[コピー アクティビティのプロパティ](#copy-activity-properties)」を参照してください。 データ ストアをソースまたはシンクとして使用する方法の詳細については、前のセクションに記載されているデータ ストアのリンクを選択してください。

ウィザードを使用すると、リンクされたサービス、データ セット、パイプラインでなどの Data Factory エンティティの JSON 定義が自動的に作成されます。 (.NET API を除く) ツールまたは API を使う場合は、JSON 形式でこれらの Data Factory エンティティを定義します。 オンプレミスの Oracle データベースとの間でデータをコピーするために使用する Data Factory エンティティに関する JSON 定義のサンプルについては、JSON の例を参照してください。

次のセクションでは、Data Factory エンティティの定義に使用される JSON プロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

次の表は、Oracle のリンクされたサービスに固有の JSON 要素について説明しています。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |**type** プロパティを **OnPremisesOracle** に設定する必要があります。 |はい |
| driverType | Oracle データベースとの間でデータをコピーするときに使用するドライバーを指定します。 使用できる値は **Microsoft** と **ODP** (既定値) です。 ドライバーの詳細については、「[サポートされているバージョンとインストール](#supported-versions-and-installation)」を参照してください。 | いいえ |
| connectionString | **connectionString** プロパティの Oracle データベース インスタンスに接続するために必要な情報を指定します。 | はい |
| gatewayName | オンプレミスの Oracle サーバーへの接続に使用されるゲートウェイの名前です |はい |

**例:Microsoft ドライバーの使用**

> [!TIP]
> "ORA-01025: UPI パラメータの値が有効範囲外です" というエラーが表示され、Oracle のバージョンが 8i である場合は、接続文字列に `WireProtocolMode=1` を追加して再試行してください。

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**例:ODP ドライバーの使用**

使用できる形式の詳細については、[.NET ODP 用の Oracle データ プロバイダー](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/)に関するページを参照してください。

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>))); User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットの定義に使用できるセクションとプロパティの完全な一覧については、[データセットの作成](data-factory-create-datasets.md)をご覧ください。

構造、可用性、ポリシーなどのデータセット JSON ファイルのセクションは、どのデータセットの種類 (Oracle、Azure BLOB ストレージ、Azure Table ストレージなど) でも同様です。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **OracleTable** 型のデータセットの **typeProperties** セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |リンクされたサービスが参照する Oracle データベースのテーブルの名前です。 |いいえ (**OracleSource** の **oracleReaderQuery** が指定されている場合) |

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。

名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

> [!NOTE]
> コピー アクティビティは入力を 1 つだけ受け取り、出力を 1 つだけ生成します。

アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティのプロパティは、ソースとシンクの種類によって変わります。

### <a name="oraclesource"></a>oracleReaderQuery

コピー アクティビティで、ソースの種類が **OracleSource** の場合は、**typeProperties** セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| oracleReaderQuery |カスタム クエリを使用してデータを読み取ります。 |SQL クエリ文字列。 たとえば、"select \* from **MyTable**"。 <br/><br/>指定されていない場合、SQL ステートメント "select \* from **MyTable**" が実行されます |いいえ<br />(**データセット**の **tableName** が指定されている場合) |

### <a name="oraclesink"></a>パイプライン

**OracleSink** では次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| writeBatchTimeout |タイムアウトする前に一括挿入操作の完了を待つ時間です。 |**timespan**<br/><br/> 例:00:30:00 (30 分) |いいえ |
| writeBatchSize |バッファー サイズが **writeBatchSize** の値に達したら、SQL テーブルにデータを挿入します。 |整数 (行数) |いいえ (既定値: 100) |
| sqlWriterCleanupScript |特定のスライスのデータを消去するようにコピー アクティビティのクエリを指定します。 |クエリ ステートメント。 |いいえ |
| sliceIdentifierColumnName |コピー アクティビティの列名を指定して、自動生成されたスライス識別子を入力します。 **sliceIdentifierColumnName** の値は、再実行時に特定のスライスのデータを消去するために使用されます。 |データ型が **binary(32)** の列の列名。 |いいえ |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>Oracle データベースとの間でのデータのコピーに関する JSON の例

次の例は、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 これらの例は、Oracle データベースと Azure BLOB ストレージの間でデータをコピーする方法を示しています。 ただし、Azure Data Factory のコピー アクティビティを使用して、「[サポートされるデータ ストアと形式](data-factory-data-movement-activities.md#supported-data-stores-and-formats)」で説明されているシンクのいずれかにデータをコピーすることができます。

**例:Oracle から Azure Blob Storage にデータをコピーする**

このサンプルでは、次の Data Factory のエンティティがあります。

* [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties)型のリンクされたサービス。
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
* [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
* source として [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) を、sink として [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティがある[パイプライン](data-factory-create-pipelines.md)。

このサンプルはオンプレミスの Oracle データベース内のテーブルから BLOB に 1 時間ごとにデータをコピーします。 サンプルで使用されているさまざまなプロパティの詳細については、サンプルの後のセクションを参照してください。

**Oracle のリンクされたサービス**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Azure BLOB ストレージのリンクされたサービス**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Oracle の入力データセット**

このサンプルは、**MyTable** という名前のテーブルが Oracle に作成されていることを前提としています。 これには、時系列データ用の **timestampcolumn** という列が含まれています。

**external** を **true** に設定すると、データセットが Data Factory の外部にあり、データセットが Data Factory のアクティビティによって生成されていないことが Data Factory サービスに通知されます。

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2014-02-27T12:00:00",
            "frequency": "Hour"
        },
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

データは、1 時間おきに新しい BLOB に書き込まれます (**頻度**: **時間**、**間隔**: **1**)。 BLOB のフォルダー パスとファイル名は、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t",
                "rowDelimiter": "\n"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**コピー アクティビティのあるパイプライン**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** の型が **OracleSource** に設定され、**sink** の型が **BlobSink** に設定されています。 **oracleReaderQuery** プロパティを使用して指定されている SQL クエリでは、過去のデータが選択され、コピーされます。

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for a copy activity",
        "activities":[
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "OracleSource",
                        "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

**例:Azure Blob Storage から Oracle にデータをコピーする**

このサンプルは、Azure BLOB ストレージ アカウントからオンプレミスの Oracle データベースにデータをコピーする方法を示します。 ただし、Azure Data Factory のコピー アクティビティを使用して、「[Supported data stores and formats](data-factory-data-movement-activities.md#supported-data-stores-and-formats)」(サポートされているデータ ストアと形式) に記載されているいずれかのソースからデータを*直接*コピーできます。

このサンプルでは、次の Data Factory のエンティティがあります。

* [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties)型のリンクされたサービス。
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
* [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
* source として [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) を、sink として [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) を使用するコピー アクティビティがある[パイプライン](data-factory-create-pipelines.md)。

このサンプルはオンプレミスの Oracle データベース内のテーブルに BLOB から 1 時間ごとにデータをコピーします。 サンプルで使用されているさまざまなプロパティの詳細については、サンプルの後のセクションを参照してください。

**Oracle のリンクされたサービス**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Azure BLOB ストレージのリンクされたサービス**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Azure BLOB の入力データセット**

データは、1 時間おきに新しい BLOB から選択されます (**頻度**: **時間**、**間隔**: **1**)。 BLOB のフォルダー パスとファイル名は、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは、開始時刻の年、月、日の部分を使います。 ファイル名は、開始時刻の時の部分を使います。 **external**: **true** の設定により、このテーブルが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
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

**Oracle 出力データセット**

このサンプルは、**MyTable** という名前のテーブルが Oracle に作成されていることを前提としています。 BLOB CSV ファイルに含めることが予想される列の数と同じ列数で Oracle にテーブルを作成します。 新しい行は 1 時間ごとにテーブルに追加されます。

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**コピー アクティビティのあるパイプライン**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** の型が **BlobSource** に設定され、**sink** の型が **OracleSink** に設定されています。

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with a copy activity",
        "activities":[
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "OracleSink"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```


## <a name="troubleshooting-tips"></a>トラブルシューティングのヒント

### <a name="problem-1-net-framework-data-provider"></a>問題 1: .NET Framework データ プロバイダー

**エラー メッセージ**

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.

**考えられる原因**

* Oracle 用の .NET Framework Data Provider がインストールされていません。
* Oracle 用の .NET Framework Data Provider が .NET Framework 2.0 にインストールされ、.NET Framework 4.0 フォルダーで検出されません。

**解決策**

* Oracle 用の .NET Provider をインストールしていない場合は [インストール](https://www.oracle.com/technetwork/topics/dotnet/downloads/) した後、シナリオをやり直します。
* プロバイダーのインストール後もこのエラー メッセージが表示される場合は、次の手順を実行します。
    1. フォルダー <システム ディスク\>:\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config から .NET 2.0 のマシン構成ファイルを開きます。
    2. **Oracle Data Provider for .NET** を検索します。 **system.data** > **DbProviderFactories** 以下に次のサンプルのようなエントリが見つかります。`<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* v4.0 フォルダー <システム ディスク\>:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config 内の machine.config ファイルに、このエントリをコピーします。次に、バージョンを 4.xxx.x.x に変更します。
* **gacutil /i [プロバイダーのパス]** を実行して、グローバル アセンブリ キャッシュ (GAC) に <ODP.NET がインストールされているパス\>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll をインストールします。

### <a name="problem-2-datetime-formatting"></a>問題 2:日付/時刻の書式設定

**エラー メッセージ**

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**解決策**

Oracle データベースでの日付の構成方法に基づいて、コピー アクティビティ内のクエリ文字列を調整する必要があります。 次に例を示します (**to_date** 関数を使用します)。

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Oracle の型マッピング

[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事のとおり、コピー アクティビティは次の 2 段階のアプローチを使用して source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する。
2. .NET 型からネイティブの sink 型に変換する。

Oracle からデータを移行すると、Oracle データ型から .NET 型へのマッピング (およびその逆) に、次のマッピングが使用されます。

| Oracle データ型 | .NET Framework データ型 |
| --- | --- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(Microsoft ドライバーを使用する場合は、Oracle 10g 以降のバージョンでのみサポートされています) |
| CHAR |string |
| CLOB |string |
| DATE |DateTime |
| FLOAT |Decimal、String (有効桁数が 28 を超える場合) |
| INTEGER |Decimal、String (有効桁数が 28 を超える場合) |
| INTERVAL YEAR TO MONTH |Int32 |
| INTERVAL DAY TO SECOND |timespan |
| LONG |string |
| LONG RAW |Byte[] |
| NCHAR |string |
| NCLOB |string |
| NUMBER |Decimal、String (有効桁数が 28 を超える場合) |
| NVARCHAR2 |string |
| RAW |Byte[] |
| ROWID |string |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |DateTime |
| TIMESTAMP WITH TIME ZONE |DateTime |
| 符号なし INTEGER |NUMBER |
| VARCHAR2 |string |
| XML |string |

> [!NOTE]
> Microsoft ドライバーを使用する場合、データ型 **INTERVAL YEAR TO MONTH** および **INTERVAL DAY TO SECOND** はサポートされません。

## <a name="map-source-to-sink-columns"></a>ソース列からシンク列へのマップ

ソース データセット列のシンク データセット列へのマッピング方法の詳細については、[Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページを参照してください。

## <a name="repeatable-read-from-relational-sources"></a>リレーショナル ソースからの反復可能読み取り

リレーショナル データ ストアからデータをコピーする場合は、意図しない結果を避けるため、再現性に注意する必要があります。 Azure Data Factory では、スライスを手動で再実行できます。 障害が発生したときにスライスを再実行できるように、データセットの再試行ポリシーを構成することもできます。 手動または再試行ポリシーのいずれかでスライスが再実行された際は、何度スライスが実行されても同じデータが読み込まれることを確認する必要があります。 詳細については、「[リレーショナル ソースからの反復可能な読み取り](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)」を参照してください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング

Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因については、[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)を参照してください。 また、最適化のさまざまな方法についても説明されています。
