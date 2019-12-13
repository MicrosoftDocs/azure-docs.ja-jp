---
title: Data Factory を使用して Cassandra からデータを移動する
description: Azure Data Factory を使用してオンプレミスの Cassandra データベースからデータを移動する方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 05cee60fb1f4d43d1b4ce371aa9f22650b4782da
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931820"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Azure Data Factory を使用してオンプレミスの Cassandra データベースからデータを移動する
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](data-factory-onprem-cassandra-connector.md)
> * [バージョン 2 (最新バージョン)](../connector-cassandra.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[V2 の Cassandra コネクタ](../connector-cassandra.md)に関するページを参照してください。

この記事では、Azure Data Factory のコピー アクティビティを使って、オンプレミスの Cassandra データベースからデータを移動する方法について説明します。 この記事は、コピー アクティビティによるデータ移動の一般的な概要について説明している、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事に基づいています。

オンプレミスの Cassandra データ ストアから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)の表をご覧ください。 Data Factory が現在サポートしているのは、Cassandra データ ストアから他のデータ ストアへのデータの移動だけで、他のデータ ストアから Cassandra データ ストアへの移動はサポートしていません。

## <a name="supported-versions"></a>サポートされているバージョン
Cassandra コネクタでは、Cassandra バージョン 2.x および 3.x がサポートされます。 セルフホステッド統合ランタイムでアクティビティを実行する場合、IR バージョン 3.7 以降で Cassandra 3.x がサポートされています。

## <a name="prerequisites"></a>前提条件
Azure Data Factory サービスをオンプレミスの Cassandra データベースに接続できるようにするには、データベースとのリソースの競合を避けるため、データベースをホストするコンピューターと同じコンピューターまたは別のコンピューターに Data Management Gateway をインストールする必要があります。 Data Management Gateway は、安全かつ管理された方法でオンプレミスのデータ ソースをクラウド サービスに接続するコンポーネントです。 Data Management Gateway の詳細については、「 [Data Management Gateway](data-factory-data-management-gateway.md) 」をご覧ください。 データを移動するデータ パイプラインにゲートウェイをセットアップする手順については、[オンプレミスからクラウドへのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事をご覧ください。

Cassandra データベースが Azure IaaS VM などのクラウドでホストされている場合でも、データベースへの接続にはゲートウェイを使用する必要があります。 ゲートウェイは、データベースをホストする VM で使用できます。また、そのゲートウェイがデータベースに接続できれば別の VM で使用することもできます。

ゲートウェイをインストールすると、Cassandra データベースへの接続に使用される Microsoft Cassandra ODBC ドライバーが自動的にインストールされます。 したがって、Cassandra データベースからデータをコピーするときに、ドライバーをゲートウェイ コンピューターに手動でインストールする必要はありません。

> [!NOTE]
> 接続/ゲートウェイに関する問題のトラブルシューティングのヒントについては、 [ゲートウェイの問題のトラブルシューティング](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) に関するセクションをご覧ください。

## <a name="getting-started"></a>使用の開始
さまざまなツールまたは API を使用して、オンプレミスの Cassandra データ ストアからデータを移動するコピー アクティビティでパイプラインを作成できます。

- パイプラインを作成する最も簡単な方法は、**コピー ウィザード**を使うことです。 手順については、「[チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」を参照してください。データのコピー ウィザードを使用してパイプラインを作成する簡単なチュートリアルです。
- また、次のツールを使用してパイプラインを作成することもできます。**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、 **.NET API**、**REST API**。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。

ツールと API のいずれを使用する場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。

1. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。
2. コピー操作用の入力データと出力データを表す**データセット**を作成します。
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む**パイプライン**を作成します。

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 (.NET API を除く) ツールまたは API を使う場合は、JSON 形式でこれらの Data Factory エンティティを定義します。 オンプレミスの Cassandra データ ストアからデータをコピーするために使用される Data Factory エンティティに関する JSON 定義のサンプルについては、この記事の「[JSON の使用例: Cassandra から Azure BLOB にデータをコピーする](#json-example-copy-data-from-cassandra-to-azure-blob)」セクションを参照してください。

次のセクションでは、Cassandra データ ストアに固有のデータ ファクトリ エンティティの定義に使用される JSON プロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
次の表は、Cassandra のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティは、次のように設定する必要があります:**OnPremisesCassandra** |はい |
| host |Cassandra サーバーの 1 つまたは複数の IP アドレスかホスト名。<br/><br/>IP アドレスまたはホスト名のコンマ区切りのリストを指定して、すべてのサーバーに同時に接続します。 |はい |
| port |Cassandra サーバーがクライアント接続のリッスンに使用する TCP ポート。 |いいえ、既定値: 9042 |
| authenticationType |Basic、または匿名 |はい |
| username |ユーザー アカウントのユーザー名を指定します。 |はい (authenticationType が Basic に設定されている場合)。 |
| password |ユーザー アカウントのパスワードを指定します。 |はい (authenticationType が Basic に設定されている場合)。 |
| gatewayName |オンプレミスの Cassandra データベースへの接続に使用されるゲートウェイの名前。 |はい |
| encryptedCredential |ゲートウェイによって暗号化された資格情報。 |いいえ |

>[!NOTE]
>現在のところ、SSL で Cassandra に接続することはできません。

## <a name="dataset-properties"></a>データセットのプロパティ
データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

**typeProperties** セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **CassandraTable** 型のデータセットの typeProperties セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| keyspace |Cassandra データベースのkeyspace またはスキーマの名前。 |はい (**CassandraSource** の**クエリ**が定義されていない場合)。 |
| tableName |Cassandra データベースのテーブル名。 |はい (**CassandraSource** の**クエリ**が定義されていない場合)。 |

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」という記事を参照してください。 名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの typeProperties セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります。

コピー アクティビティで、source の種類が **CassandraSource**である場合は、typeProperties セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| query |カスタム クエリを使用してデータを読み取ります。 |SQL-92 クエリまたはCQL クエリ。 「 [CQL reference (CQL リファレンス)](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html)」をご覧ください。 <br/><br/>SQL クエリを使用する場合は、クエリを実行するテーブルを表す **keyspace name.table name** を指定します。 |いいえ (データセットの tableName と keyspace が定義されていない場合)。 |
| consistencyLevel |一貫性レベルは、データがクライアント アプリケーションに返される前に、読み取り要求に応答する必要があるレプリカの数を指定します。 Cassandra は読み取り要求を満たすために、データの指定された数のレプリカを確認します。 |ONE、TWO、THREE、QUORUM、ALL、 LOCAL_QUORUM、EACH_QUORUM、 LOCAL_ONE。 詳細については、「 [Configuring data consistency (データ整合性の構成)](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) 」をご覧ください。 |No. 既定値は ONE です。 |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>JSON の使用例:Cassandra から Azure BLOB にデータをコピーする
次の例は、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 ここでは、オンプレミスの Cassandra データベースから Azure Blob Storage にデータをコピーする方法を示します。 ただし、Azure Data Factory のコピー アクティビティを使用して、 [こちら](data-factory-data-movement-activities.md#supported-data-stores-and-formats) に記載されているシンクのいずれかにデータをコピーすることができます。

> [!IMPORTANT]
> このサンプルでは、JSON のスニペットを使用します。 データ ファクトリを作成する手順は含まれてません。 手順については、記事「 [Data Management Gateway を使用してオンプレミスのソースとクラウドの間でデータを移動する](data-factory-move-data-between-onprem-and-cloud.md) 」を参照してください。

このサンプルでは、次の Data Factory のエンティティがあります。

* [OnPremisesCassandra](#linked-service-properties)型のリンクされたサービス。
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
* [CassandraTable](#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
* [CassandraSource](#copy-activity-properties) と [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む [パイプライン](data-factory-create-pipelines.md)。

**Cassandra のリンクされたサービス:**

この例では、 **Cassandra** のリンクされたサービスを使用します。 このリンクされたサービスでサポートされているプロパティについては、 [Cassandra のリンクされたサービス](#linked-service-properties) に関するセクションをご覧ください。

```json
{
    "name": "CassandraLinkedService",
    "properties":
    {
        "type": "OnPremisesCassandra",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "host": "mycassandraserver",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "mygateway"
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

**Cassandra の入力データセット:**

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "mykeyspace"
        },
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

**external** を **true** に設定すると、データセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

**Azure BLOB の出力データセット:**

データは新しい BLOB に 1 時間おきに書き込まれます (frequency: hour、interval: 1)。

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/fromcassandra"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Cassandra ソースおよび BLOB シンクを使用するパイプラインでのコピー アクティビティ:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** の型が **CassandraSource** に設定され、**sink** の型が **BlobSink** に設定されています。

RelationalSource でサポートされるプロパティの一覧については、「 [RelationalSource type プロパティ](#copy-activity-properties) 」をご覧ください。

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "CassandraInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"

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

### <a name="type-mapping-for-cassandra"></a>Cassandra の型マッピング
| Cassandra の型 | .NET ベースの型 |
| --- | --- |
| ASCII |string |
| BIGINT |Int64 |
| BLOB |Byte[] |
| BOOLEAN |BOOLEAN |
| DECIMAL |Decimal |
| DOUBLE |DOUBLE |
| FLOAT |Single |
| INET |string |
| INT |Int32 |
| TEXT |string |
| TIMESTAMP |DateTime |
| TIMEUUID |Guid |
| UUID |Guid |
| VARCHAR |string |
| VARINT |DECIMAL |

> [!NOTE]
> コレクションの種類 (マップ、セット、リストなど) については、 [仮想テーブルを使用した Cassandra コレクションの種類の取り扱い](#work-with-collections-using-virtual-table) に関するセクションをご覧ください。
>
> ユーザー定義型はサポートされていません。
>
> バイナリ列と文字列の列の長さは 4000 バイト以内である必要があります。
>
>

## <a name="work-with-collections-using-virtual-table"></a>仮想テーブルを使用したコレクションの取り扱い
Azure Data Factory では、ビルトインの ODBC ドライバーを使用して Cassandra データベースへの接続や、Cassandra データベースからのデータのコピーを行います。 マップ、セット、およびリストを含むコレクションの種類については、ODBC ドライバーが、対応する仮想テーブルへのデータの再正規化を行います。 具体的には、テーブルにコレクション列が含まれている場合に、ドライバーが次の仮想テーブルを生成します。

* 実テーブルと同じデータ (コレクション列を除く) を含む **ベース テーブル**。 ベース テーブルには、それが表す実テーブルと同じ名前が使用されます。
* 入れ子になったデータを展開する、各コレクション列の **仮想テーブル** 。 コレクションを表す仮想テーブルには、実テーブルの名前、区切り文字の "*vt*"、および列の名前を使用して名前が付けられます。

仮想テーブルは、非正規化データへのドライバーのアクセスを有効にして、実テーブルのデータを参照します。 詳細については、「例」セクションを参照してください。 仮想テーブルのクエリや結合により、Cassandra コレクションの内容にアクセスできます。

[コピー ウィザード](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity)を使用して、仮想テーブルを含む Cassandra データベースのテーブルの一覧を直感的に表示し、内部データをプレビューすることができます。 また、コピー ウィザードでクエリを構築して検証し、結果を確認することもできます。

### <a name="example"></a>例
たとえば、次の "ExampleTable" は、整数の主キー列 ("pk_int" という名前)、テキスト列 (値という名前)、リスト列、 マップ列、 およびセット列 ("StringSet" という名前) で構成された、Cassandra データベース テーブルです。

| pk_int | 値 | 一覧表示 | マップ | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"サンプル値 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"サンプル値 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

ドライバーによって、この単一のテーブルを表す複数の仮想テーブルが生成されます。 仮想テーブルの外部キー列は、実テーブルの主キー列を参照して、仮想テーブルの行が対応する実テーブルの行を示します。

最初の仮想テーブルは、次の表に示す "ExampleTable" という名前のベース テーブルです。 ベース テーブルには、元のデータベース テーブルと同じデータ (コレクションを除く) が含まれています。このテーブルで省略されたコレクションは、他の仮想テーブルで展開されます。

| pk_int | 値 |
| --- | --- |
| 1 |"サンプル値 1" |
| 3 |"サンプル値 3" |

次のテーブルは、リスト、マップ、および StringSet の列からのデータを再正規化した仮想テーブルを表しています。 "_index" や "_key" で終わる名前の列は、元のリストまたはマップ内のデータの位置を示しています。 "_value" で終わる名前の列には、コレクションから展開されたデータが含まれています。

#### <a name="table-exampletable_vt_list"></a>テーブル "ExampleTable_vt_List":
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletable_vt_map"></a>テーブル "ExampleTable_vt_Map":
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletable_vt_stringset"></a>テーブル "ExampleTable_vt_StringSet":
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |b |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>ソース列からシンク列へのマップ
ソース データセット列のシンク データセット列へのマッピングの詳細については、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページをご覧ください。

## <a name="repeatable-read-from-relational-sources"></a>リレーショナル ソースからの反復可能読み取り
リレーショナル データ ストアからデータをコピーする場合は、意図しない結果を避けるため、再現性に注意する必要があります。 Azure Data Factory では、スライスを手動で再実行できます。 障害が発生したときにスライスを再実行できるように、データセットの再試行ポリシーを構成することもできます。 いずれかの方法でスライスが再実行された際は、何度スライスが実行されても同じデータが読み込まれることを確認する必要があります。 [リレーショナル ソースからの反復可能読み取り](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)に関するページをご覧ください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。
