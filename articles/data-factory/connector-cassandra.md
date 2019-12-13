---
title: Azure Data Factory を使用して Cassandra からデータをコピーする
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、Cassandra からサポートされているシンク データ ストアへデータをコピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 41547787a3b7add1baa05a41d6785d1cd926165d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929587"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Azure Data Factory を使用して Cassandra からデータをコピーする
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください。"]
> * [Version 1](v1/data-factory-onprem-cassandra-connector.md)
> * [現在のバージョン](connector-cassandra.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、Cassandra データベースからデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

この Cassandra コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

Cassandra データベースから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この Cassandra コネクタは以下をサポートします。

- Cassandra **バージョン 2.X および 3.x**。
- **基本**または**匿名**認証を使用したデータのコピー。

>[!NOTE]
>セルフホステッド統合ランタイムでアクティビティを実行する場合、IR バージョン 3.7 以降で Cassandra 3.x がサポートされています。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

統合ランタイムには Cassandra ドライバーが組み込まれているため、Cassandra をコピー元またはコピー先としてデータをコピーするときにドライバーを手動でインストールする必要はありません。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Cassandra コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Cassandra のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type |type プロパティは、次のように設定する必要があります:**Cassandra** |はい |
| host |Cassandra サーバーの 1 つまたは複数の IP アドレスかホスト名。<br/>IP アドレスまたはホスト名のコンマ区切りのリストを指定して、すべてのサーバーに同時に接続します。 |はい |
| port |Cassandra サーバーがクライアント接続のリッスンに使用する TCP ポート。 |いいえ (既定値は 9042) |
| authenticationType | Cassandra データベースへの接続に使用される認証の種類です。<br/>使用できる値は、以下のとおりです。**Basic**、**Anonymous**。 |はい |
| username |ユーザー アカウントのユーザー名を指定します。 |はい (authenticationType が Basic に設定されている場合)。 |
| password |ユーザー アカウントのパスワードを指定します。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 |はい (authenticationType が Basic に設定されている場合)。 |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

>[!NOTE]
>現在のところ、SSL で Cassandra に接続することはできません。

**例:**

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "Cassandra",
        "typeProperties": {
            "host": "<host>",
            "authenticationType": "Basic",
            "username": "<username>",
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Cassandra データセット でサポートされるプロパティの一覧を示します。

Cassandra からデータをコピーするには、データセットの type プロパティを **CassandraTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**CassandraTable** | はい |
| keyspace |Cassandra データベースのkeyspace またはスキーマの名前。 |はい ("CassandraSource" の "クエリ" が指定されている場合)。 |
| tableName |Cassandra データベースのテーブル名。 |はい ("CassandraSource" の "クエリ" が指定されている場合)。 |

**例:**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ


アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Cassandra ソースでサポートされるプロパティの一覧を示します。

### <a name="cassandra-as-source"></a>ソースとしての Cassandra

Cassandra からデータをコピーするには、コピー アクティビティのソース タイプを **CassandraSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**CassandraSource** | はい |
| query |カスタム クエリを使用してデータを読み取ります。 SQL-92 クエリまたはCQL クエリ。 「 [CQL reference (CQL リファレンス)](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html)」をご覧ください。 <br/><br/>SQL クエリを使用する場合は、クエリを実行するテーブルを表す **keyspace name.table name** を指定します。 |いいえ (データセットの "tableName" と "keyspace" が指定されている場合)。 |
| consistencyLevel |一貫性レベルは、データがクライアント アプリケーションに返される前に、読み取り要求に応答する必要があるレプリカの数を指定します。 Cassandra は読み取り要求を満たすために、データの指定された数のレプリカを確認します。 詳細については、「 [Configuring data consistency (データ整合性の構成)](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) 」をご覧ください。<br/><br/>使用できる値は、以下のとおりです。**ONE**、**TWO**、**THREE**、**QUORUM**、**ALL**、**LOCAL_QUORUM**、**EACH_QUORUM**、および **LOCAL_ONE**。 |いいえ (既定値は `ONE`) |

**例:**

```json
"activities":[
    {
        "name": "CopyFromCassandra",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cassandra input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CassandraSource",
                "query": "select id, firstname, lastname from mykeyspace.mytable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-cassandra"></a>Cassandra のデータ型マッピング

Cassandra からデータをコピーするとき、次の Cassandra のデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| Cassandra のデータ型 | Data Factory の中間データ型 |
|:--- |:--- |
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

## <a name="work-with-collections-using-virtual-table"></a>仮想テーブルを使用したコレクションの取り扱い

Azure Data Factory では、ビルトインの ODBC ドライバーを使用して Cassandra データベースへの接続や、Cassandra データベースからのデータのコピーを行います。 マップ、セット、およびリストを含むコレクションの種類については、ODBC ドライバーが、対応する仮想テーブルへのデータの再正規化を行います。 具体的には、テーブルにコレクション列が含まれている場合に、ドライバーが次の仮想テーブルを生成します。

* 実テーブルと同じデータ (コレクション列を除く) を含む **ベース テーブル**。 ベース テーブルには、それが表す実テーブルと同じ名前が使用されます。
* 入れ子になったデータを展開する、各コレクション列の **仮想テーブル** 。 コレクションを表す仮想テーブルには、実テーブルの名前、区切り文字の "*vt*"、および列の名前を使用して名前が付けられます。

仮想テーブルは、非正規化データへのドライバーのアクセスを有効にして、実テーブルのデータを参照します。 詳細については、「例」セクションを参照してください。 仮想テーブルのクエリや結合により、Cassandra コレクションの内容にアクセスできます。

### <a name="example"></a>例

たとえば、次の "ExampleTable" は、整数の主キー列 ("pk_int" という名前)、テキスト列 (値という名前)、リスト列、 マップ列、 およびセット列 ("StringSet" という名前) で構成された、Cassandra データベース テーブルです。

| pk_int | 値 | 一覧表示 | マップ | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"サンプル値 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"サンプル値 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

ドライバーによって、この単一のテーブルを表す複数の仮想テーブルが生成されます。 仮想テーブルの外部キー列は、実テーブルの主キー列を参照して、仮想テーブルの行が対応する実テーブルの行を示します。

最初の仮想テーブルは、次の表に示す "ExampleTable" という名前のベース テーブルです。 

| pk_int | 値 |
| --- | --- |
| 1 |"サンプル値 1" |
| 3 |"サンプル値 3" |

ベース テーブルには、元のデータベース テーブルと同じデータ (コレクションを除く) が含まれています。このテーブルで省略されたコレクションは、他の仮想テーブルで展開されます。

次のテーブルは、リスト、マップ、および StringSet の列からのデータを再正規化した仮想テーブルを表しています。 "_index" や "_key" で終わる名前の列は、元のリストまたはマップ内のデータの位置を示しています。 "_value" で終わる名前の列には、コレクションから展開されたデータが含まれています。

**テーブル "ExampleTable_vt_List":**

| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

**テーブル "ExampleTable_vt_Map":**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

**テーブル "ExampleTable_vt_StringSet":**

| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |b |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="lookup-activity-properties"></a>ルックアップ アクティビティのプロパティ

プロパティの詳細については、[ルックアップ アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。
