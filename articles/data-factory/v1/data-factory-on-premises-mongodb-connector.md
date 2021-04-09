---
title: MongoDB からデータを移動する
description: Azure Data Factory を使用して MongoDB データベースからデータを移動する方法を説明します。
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/13/2018
ms.openlocfilehash: cedb0b99f04df00763a3ee83287eec90bd5fb45d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387514"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Azure Data Factory を使用して MongoDB からデータを移動する

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](data-factory-on-premises-mongodb-connector.md)
> * [バージョン 2 (最新バージョン)](../connector-mongodb.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 最新バージョンの Data Factory サービスを使用している場合は、[V2 の MongoDB コネクタ](../connector-mongodb.md)に関するページをご覧ください。


この記事では、Azure Data Factory のコピー アクティビティを使って、オンプレミスの MongoDB データベースからデータを移動させる方法について説明します。 この記事は、コピー アクティビティによるデータ移動の一般的な概要について説明している、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事に基づいています。

オンプレミスの MongoDB データ ストアから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)の表をご覧ください。 Data Factory は、現時点では MongoDB データ ストアから他のデータ ストアへのデータ移動のみをサポートし、他のデータ ストアから MongoDB データ ストアへのデータ移動に関してはサポートしていません。

## <a name="prerequisites"></a>前提条件
Azure Data Factory サービスをオンプレミスの MongoDB データベースに接続できるようにするには、次のコンポーネントをインストールする必要があります。

- サポートされている MongoDB バージョンは、2.4、2.6、3.0、3.2、3.4 および 3.6 です。
- データベースをホストするコンピューターと同じコンピューター、またはデータベースとのリソースの競合を避けるために別のコンピューター上にインストールされた Data Management Gateway。 Data Management Gateway は、安全かつ管理された方法でオンプレミスのデータをクラウド サービスに接続するソフトウェアです。 Data Management Gateway の詳細については、「 [Data Management Gateway](data-factory-data-management-gateway.md) 」をご覧ください。 データを移動するデータ パイプラインにゲートウェイをセットアップする手順については、[オンプレミスからクラウドへのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事をご覧ください。

    ゲートウェイをインストールすると、MongoDB への接続に使用される Microsoft MongoDB ODBC ドライバーが自動的にインストールされます。

    > [!NOTE]
    > Azure IaaS VM でホストされている場合でも、MongoDB への接続にゲートウェイを使用する必要があります。 クラウドでホストされている MongoDB のインスタンスに接続しようとしている場合は、IaaS VM にゲートウェイ インスタンスをインストールすることもできます。

## <a name="getting-started"></a>作業の開始
さまざまなツール/API を使用して、オンプレミスの MongoDB データ ストアからデータを移動するコピー アクティビティを含むパイプラインを作成できます。

パイプラインを作成する最も簡単な方法は、**コピー ウィザード** を使うことです。 「[チュートリアル:コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」を参照してください。データのコピー ウィザードを使用してパイプラインを作成する簡単なチュートリアルです。

また、次のツールを使用してパイプラインを作成することもできます。**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、 **.NET API**、**REST API**。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。

ツールと API のいずれを使用する場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。

1. **リンクされたサービス** を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。
2. コピー操作用の入力データと出力データを表す **データセット** を作成します。
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む **パイプライン** を作成します。

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 (.NET API を除く) ツールまたは API を使う場合は、JSON 形式でこれらの Data Factory エンティティを定義します。  オンプレミスの MongoDB データ ストアからデータをコピーするために使用する Data Factory エンティティに関する JSON 定義のサンプルについては、この記事のセクション、「[JSON の使用例: MongoDB から Azure BLOB へのデータのコピー](#json-example-copy-data-from-mongodb-to-azure-blob)」をご覧ください。

次のセクションでは、MongoDB ソースに固有の Data Factory エンティティの定義に使用される JSON プロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
次の表は、 **OnPremisesMongoDB** のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティを **OnPremisesMongoDb** |はい |
| server |MongoDB サーバーの IP アドレスまたはホスト名。 |はい |
| port |MongoDB サーバーがクライアント接続のリッスンに使用する TCP ポート。 |省略可能、既定値: 27017 |
| authenticationType |Basic または Anonymous。 |はい |
| username |MongoDB にアクセスするためのユーザー アカウント。 |はい (基本認証が使用される場合)。 |
| password |ユーザーのパスワード。 |はい (基本認証が使用される場合)。 |
| authSource |認証のために資格情報を確認する際に使用する MongoDB データベースの名前。 |省略可能 (基本認証が使用される場合)。 既定では、管理者アカウントと、databaseName プロパティで指定されたデータベースが使用されます。 |
| databaseName |アクセスする MongoDB データベースの名前。 |はい |
| gatewayName |データ ストアにアクセスするゲートウェイの名前。 |はい |
| encryptedCredential |ゲートウェイによって暗号化された資格情報。 |省略可能 |

## <a name="dataset-properties"></a>データセットのプロパティ
データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **MongoDbCollection** 型のデータセットの typeProperties セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| collectionName |MongoDB データベースのコレクション名前。 |はい |

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」という記事を参照してください。 名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの **typeProperties** セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります。

コピー アクティビティで、source の種類が **MongoDbSource** である場合は、typeProperties セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query |カスタム クエリを使用してデータを読み取ります。 |SQL-92 クエリ文字列。 例: Select * from MyTable。 |いいえ (**データセット** の **collectionName** が指定されている場合) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>JSON の使用例: MongoDB から Azure BLOB へのデータのコピー
次の例は、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 このサンプルでは、オンプレミスの MongoDB から Azure Blob Storage にデータをコピーする方法を示します。 ただし、Azure Data Factory のコピー アクティビティを使用して、 [こちら](data-factory-data-movement-activities.md#supported-data-stores-and-formats) に記載されているシンクのいずれかにデータをコピーすることができます。

このサンプルでは、次の Data Factory のエンティティがあります。

1. [OnPremisesMongoDb](#linked-service-properties)型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
3. [MongoDbCollection](#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [MongoDbSource](#copy-activity-properties) と [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは MongoDB データベースのクエリ結果のデータを BLOB に 1 時間ごとにコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

最初の手順として、「 [Data Management Gateway](data-factory-data-management-gateway.md) 」の指示に従って Data Management Gateway を設定してください。

**MongoDB のリンクされたサービス**

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
        }
    }
}
```

**Azure Storage のリンクされたサービス:**

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

**MongoDB 入力データセット** external を true に設定すると、テーブルが Data Factory に対して外部にあり、Data Factory のアクティビティでは生成されていないことが Data Factory のサービスに通知されます。

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Azure BLOB の出力データセット:**

データは新しい BLOB に 1 時間おきに書き込まれます (frequency: hour、interval: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**MongoDB ソースおよび BLOB シンクを使用するパイプラインでのコピー アクティビティ:**

上記の入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティがパイプラインに含まれています。 パイプライン JSON 定義で、**source** 型が **MongoDbSource** に設定され、**sink** 型が **BlobSink** に設定されています。 **query** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
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
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```


## <a name="schema-by-data-factory"></a>Data Factory によるスキーマ
Azure Data Factory サービスは、MongoDB コレクション内の最新のドキュメント 100 個を使用してスキーマを推論します。 この 100 個のドキュメントにスキーマが完全には含まれていない場合は、コピー操作中に無視される列が生じる可能性があります。

## <a name="type-mapping-for-mongodb"></a>MongoDB の型マッピング
[データ移動アクティビティ](data-factory-data-movement-activities.md) に関する記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

MongoDB にデータを移動する場合、MongoDB 型から .NET 型に対する次のマッピングが使用されます。

| MongoDB 型 | .NET Framework 型 |
| --- | --- |
| Binary |Byte[] |
| Boolean |Boolean |
| Date |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |String |
| String |String |
| UUID |Guid |
| Object |入れ子の区切り文字に "_" を使用してフラット化された列に再正規化されます。 |

> [!NOTE]
> 仮想テーブルを使用した配列に対するサポートの詳細については、下の「 [仮想テーブルを使用した複合型のサポート](#support-for-complex-types-using-virtual-tables) 」セクションをご覧ください。

現時点では、次の MongoDB データ型はサポートされていません: DBPointer、JavaScript、Max/Min key、Regular Expression、Symbol、Timestamp、Undefined。

## <a name="support-for-complex-types-using-virtual-tables"></a>仮想テーブルを使用した複合型のサポート
Azure Data Factory では、ビルトインの ODBC ドライバーを使用して、MongoDB データベースへの接続や、MongoDB データベースからのデータのコピーを行います。 複数のドキュメントのさまざまな型が含まれた配列やオブジェクトなどの複合型については、ODBC ドライバーによって、対応する仮想テーブルへのデータの再正規化が行われます。 具体的には、テーブルにそのような列が含まれている場合に、ドライバーによって次の仮想テーブルが生成されます。

* 実テーブルと同じデータ (複合型列を除く) を含む **ベース テーブル**。 ベース テーブルには、それが表す実テーブルと同じ名前が使用されます。
* 複合型列ごとの **仮想テーブル**。入れ子になったデータが展開されます。 仮想テーブルの名前は、実テーブルの名前、区切り文字の "_"、配列またはオブジェクトの名前を使用して付けられます。

仮想テーブルは、非正規化データへのドライバーのアクセスを有効にして、実テーブルのデータを参照します。 詳細については、次の「例」を参照してください。 仮想テーブルのクエリや結合によって、MongoDB の配列の内容にアクセスできます。

[コピー ウィザード](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) を使用して、仮想テーブルを含む MongoDB データベースのテーブルの一覧を直感的に表示し、内部データをプレビューすることができます。 また、コピー ウィザードでクエリを構築して検証し、結果を確認することもできます。

### <a name="example"></a>例
例として下に挙げる "ExampleTable" は、各セルにオブジェクトの配列が 1 つ含まれた列 Invoices と、スカラー型の配列が 1 つ含まれた列 Ratings で構成された MongoDB テーブルです。

| _id | Customer Name | Invoices | サービス レベル | Ratings |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:”123”, item:”toaster”, price:”456”, discount:”0.2”}, {invoice_id:”124”, item:”oven”, price: ”1235”, discount: ”0.2”}] |シルバー |[5,6] |
| 2222 |XYZ |[{invoice_id:”135”, item:”fridge”, price: ”12543”, discount: ”0.0”}] |ゴールド |[1,2] |

ドライバーによって、この単一のテーブルを表す複数の仮想テーブルが生成されます。 最初の仮想テーブルは、次に示す "ExampleTable" という名前のベース テーブルです。 ベース テーブルには元のテーブルのすべてのデータが含まれますが、配列のデータは省略され、仮想テーブルで展開されます。

| _id | Customer Name | サービス レベル |
| --- | --- | --- |
| 1111 |ABC |シルバー |
| 2222 |XYZ |ゴールド |

次のテーブルは、例における元の配列を表す仮想テーブルを示しています。 これらのテーブルには、以下が含まれます。

* 元の配列の行に対応する元のプライマリ キー列への参照 (_Id 列を使用)
* 元の配列内のデータの位置の指定
* 配列内の各要素の展開されたデータ

テーブル "ExampleTable_Invoices":

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | price | Discount |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |toaster |456 |0.2 |
| 1111 |1 |124 |oven |1235 |0.2 |
| 2222 |0 |135 |fridge |12543 |0.0 |

テーブル "ExampleTable_Ratings":

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>ソース列からシンク列へのマップ
ソース データセット列のシンク データセット列へのマッピングの詳細については、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページをご覧ください。

## <a name="repeatable-read-from-relational-sources"></a>リレーショナル ソースからの反復可能読み取り
リレーショナル データ ストアからデータをコピーする場合は、意図しない結果を避けるため、再現性に注意する必要があります。 Azure Data Factory では、スライスを手動で再実行できます。 障害が発生したときにスライスを再実行できるように、データセットの再試行ポリシーを構成することもできます。 いずれかの方法でスライスが再実行された際は、何度スライスが実行されても同じデータが読み込まれることを確認する必要があります。 [リレーショナル ソースからの反復可能読み取り](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)に関するページをご覧ください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。

## <a name="next-steps"></a>次の手順
オンプレミスのデータ ストアから Azure データ ストアにデータを移動するデータ パイプラインを作成するための詳しい手順については、 [オンプレミスとクラウドの間でデータを移動する方法](data-factory-move-data-between-onprem-and-cloud.md) に関する記事をご覧ください。
