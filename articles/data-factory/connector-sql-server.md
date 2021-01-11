---
title: SQL Server との間でデータをコピーする
description: Azure Data Factory を使用してオンプレミスまたは Azure VM の SQL Server データベースとの間でデータを移動する方法を説明します。
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/25/2020
ms.openlocfilehash: df100d73bd137f0c471079af976cf657353fd184
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816815"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Azure Data Factory を使用して SQL Server をコピー元またはコピー先としてデータをコピーする

> [!div class="op_single_selector" title1="使用している Azure Data Factory のバージョンを選択してください:"]
> * [Version 1](v1/data-factory-sqlserver-connector.md)
> * [現在のバージョン](connector-sql-server.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティを使用して、SQL Server データベースをコピー先またはコピー元としてデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

この SQL Server コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)

SQL Server データベースから、サポートされている任意のシンク データ ストアにデータをコピーできます。 または、任意のサポートされているソース データ ストアから SQL Server データベースにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この SQL Server コネクタは以下をサポートします。

- SQL Server バージョン 2005 以降。
- SQL または Windows 認証を使用したデータのコピー。
- ソースとして、SQL クエリまたはストアド プロシージャを使用してデータを取得する。 SQL Server ソースからの並列コピーを選択することもできます。詳細については、「[SQL データベースからの並列コピー](#parallel-copy-from-sql-database)」セクションを参照してください。
- シンクとして、ソースのスキーマに基づいて、宛先テーブルが存在しない場合はこれを自動的に作成する。テーブルにデータを追加するか、コピー中にカスタム ロジックを使用してストアド プロシージャを呼び出す。 

[SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017) はサポートされていません。

>[!NOTE]
>SQL Server の [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) は現在、このコネクタではサポートされていません。 回避するには、[汎用 ODBC コネクタ](connector-odbc.md)と SQL Server ODBC ドライバーを使用できます。 ODBC ドライバーのダウンロードおよび接続文字列の構成については、[このガイダンス](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017)に従ってください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、SQL Server データベース コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SQL Server のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **SqlServer** に設定する必要があります。 | はい |
| connectionString |SQL 認証または Windows 認証を使用して、SQL Server データベースに接続するために必要な **connectionString** 情報を指定します。 以下のサンプルを参照してください。<br/>また、Azure Key Vault にパスワードを格納することもできます。 それが SQL 認証である場合は、接続文字列から `password` 構成を取得します。 詳細については、この表の後にある JSON の例および「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」を参照してください。 |はい |
| userName |Windows 認証を使用しする場合は、ユーザー名を指定します。 例: **domainname\\username**。 |いいえ |
| password |ユーザー名に指定したユーザー アカウントのパスワードを指定します。 このフィールドは、Azure Data Factory で安全に格納するために **SecureString** としてマークします。 また、[Azure Key Vault に格納されているシークレットを参照する](store-credentials-in-key-vault.md)こともできます。 |いいえ |
| connectVia | この[統合ランタイム](concepts-integration-runtime.md)は、データ ストアに接続するために使用されます。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

>[!TIP]
>エラー コード "UserErrorFailedToConnectToSqlServer" および "The session limit for the database is XXX and has been reached" (データベースのセッション制限 XXX に達しました) のようなメッセージのエラーが発生する場合は、`Pooling=false` を接続文字列に追加して、もう一度試してください。

**例 1: SQL 認証を使用する**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例 2: Azure Key Vault 内のパスワードで SQL 認証を使用する**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例 3: Windows 認証を使用する**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
            "userName": "<domain\\username>",
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、SQL Server データセットでサポートされるプロパティの一覧を示します。

SQL Server データベースをコピー元またはコピー先にしたデータ コピーについては、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは **SqlServerTable** に設定する必要があります。 | はい |
| schema | スキーマの名前。 |ソースの場合はいいえ、シンクの場合ははい  |
| table | テーブル/ビューの名前。 |ソースの場合はいいえ、シンクの場合ははい  |
| tableName | スキーマがあるテーブル/ビューの名前。 このプロパティは下位互換性のためにサポートされています。 新しいワークロードでは、`schema` と `table` を使用します。 | ソースの場合はいいえ、シンクの場合ははい |

**例**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、SQL Server のソースおよびシンクでサポートされるプロパティの一覧を示します。

### <a name="sql-server-as-a-source"></a>ソースとしての SQL Server

>[!TIP]
>データ パーティション分割を使用して、SQL Server からデータを効率的に読み込む方法の詳細については、[SQL データベースからの並列コピー](#parallel-copy-from-sql-database)に関するセクションを参照してください。

SQL Server からデータをコピーするには、コピー アクティビティのソースの種類を **SqlSource** に設定します。 コピー アクティビティの source セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **SqlSource** に設定する必要があります。 | はい |
| sqlReaderQuery |カスタム SQL クエリを使用してデータを読み取ります。 たとえば `select * from MyTable` です。 |いいえ |
| sqlReaderStoredProcedureName |このプロパティは、ソース テーブルからデータを読み取るストアド プロシージャの名前です。 最後の SQL ステートメントはストアド プロシージャの SELECT ステートメントにする必要があります。 |いいえ |
| storedProcedureParameters |これらのパラメーターは、ストアド プロシージャ用です。<br/>使用可能な値は、名前または値のペアです。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ |
| isolationLevel | SQL ソースのトランザクション ロック動作を指定します。 使用できる値は、次のとおりです。**ReadCommitted**、**ReadUncommitted**、**RepeatableRead**、**Serializable**、**Snapshot**。 指定しなかった場合は、データベースの既定の分離レベルが使用されます。 詳細については[こちらのドキュメント](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel)をご覧ください。 | いいえ |
| partitionOptions | SQL Server からデータを読み込むときに使用するデータ パーティション分割オプションを指定します。 <br>使用できる値は、以下のとおりです。**None** (既定値)、**PhysicalPartitionsOfTable**、および **DynamicRange**。<br>パーティション オプションが有効になっている (つまり、`None` ではない) 場合、SQL Server から同時にデータを読み込む並列度は、コピー アクティビティの [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) の設定によって制御されます。 | いいえ |
| partitionSettings | データ パーティション分割の設定のグループを指定します。 <br>パーティション オプションが `None` でない場合に適用されます。 | いいえ |
| ***`partitionSettings` の下:*** | | |
| partitionColumnName | 並列コピーの範囲パーティション分割で使用される**整数型または日付/日時型**のソース列の名前を指定します。 指定されない場合は、テーブルのインデックスまたは主キーが自動検出され、パーティション列として使用されます。<br>パーティション オプションが `DynamicRange` である場合に適用されます。 クエリを使用してソース データを取得する場合は、WHERE 句で `?AdfDynamicRangePartitionCondition ` をフックします。 例については、「[SQL データベースからの並列コピー](#parallel-copy-from-sql-database)」セクションを参照してください。 | いいえ |
| partitionUpperBound | パーティション範囲の分割のための、パーティション列の最大値。 この値は、テーブル内の行のフィルター処理用ではなく、パーティションのストライドを決定するために使用されます。 テーブルまたはクエリ結果に含まれるすべての行がパーティション分割され、コピーされます。 指定されていない場合は、コピー アクティビティによって値が自動検出されます。  <br>パーティション オプションが `DynamicRange` である場合に適用されます。 例については、「[SQL データベースからの並列コピー](#parallel-copy-from-sql-database)」セクションを参照してください。 | いいえ |
| partitionLowerBound | パーティション範囲の分割のための、パーティション列の最小値。 この値は、テーブル内の行のフィルター処理用ではなく、パーティションのストライドを決定するために使用されます。 テーブルまたはクエリ結果に含まれるすべての行がパーティション分割され、コピーされます。 指定されていない場合は、コピー アクティビティによって値が自動検出されます。<br>パーティション オプションが `DynamicRange` である場合に適用されます。 例については、「[SQL データベースからの並列コピー](#parallel-copy-from-sql-database)」セクションを参照してください。 | いいえ |

**注意する点:**

- **SqlSource** に **sqlReaderQuery** が指定されている場合、コピー アクティビティでは、データを取得するために SQL Server ソースに対してこのクエリを実行します。 **sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することもできます (ストアド プロシージャでパラメーターを使用する場合)。
- **sqlReaderQuery** または **sqlReaderStoredProcedureName** を指定しない場合は、データセット JSON の "structure" セクションで定義されている列を使用して、クエリが作成されます。 クエリ `select column1, column2 from mytable` は SQL Server に対して実行されます。 データセット定義に "structure" がない場合は、すべての列がテーブルから選択されます。

**例:SQL クエリを使用する**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**例:ストアド プロシージャの使用**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**ストアド プロシージャの定義**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-server-as-a-sink"></a>シンクとしての SQL Server

> [!TIP]
> サポートされる書き込み動作、構成、およびベスト プラクティスの詳細については、「[SQL Server にデータを読み込むときのベスト プラクティス](#best-practice-for-loading-data-into-sql-server)」を参照してください。

SQL Server にデータをコピーするには、コピー アクティビティのシンクの種類を **SqlSink** に設定します。 コピー アクティビティの sink セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは、**SqlSink** に設定する必要があります。 | はい |
| preCopyScript |このプロパティでは、コピー アクティビティで SQL Server にデータを書き込む前に実行する SQL クエリを指定します。 これは、コピー実行ごとに 1 回だけ呼び出されます。 このプロパティを使用して、事前に読み込まれたデータをクリーンアップできます。 |いいえ |
| tableOption | ソースのスキーマに基づいて[自動的にシンク テーブルを作成する](copy-activity-overview.md#auto-create-sink-tables)かどうかを指定します (存在しない場合)。 シンクでストアド プロシージャが指定されている場合、テーブルの自動作成はサポートされません。 使用できる値は `none` (既定値)、`autoCreate` です。 |いいえ |
| sqlWriterStoredProcedureName | ターゲット テーブルにソース データを適用する方法を定義しているストアド プロシージャの名前です。 <br/>このストアド プロシージャは*バッチごとに呼び出されます*。 1 回だけ実行され、ソース データとは関係がない操作 (削除/切り詰めなど) の場合は、`preCopyScript` プロパティを使用します。<br>例については、「[SQL シンクからのストアド プロシージャの呼び出し](#invoke-a-stored-procedure-from-a-sql-sink)」を参照してください。 | いいえ |
| storedProcedureTableTypeParameterName |ストアド プロシージャで指定されたテーブル型のパラメーター名。  |いいえ |
| sqlWriterTableType |ストアド プロシージャで使用するテーブル型の名前。 コピー アクティビティでは、このテーブル型の一時テーブルでデータを移動できます。 その後、ストアド プロシージャのコードにより、コピーされたデータを既存のデータと結合できます。 |いいえ |
| storedProcedureParameters |ストアド プロシージャのパラメーター。<br/>使用可能な値は、名前と値のペアです。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 | いいえ |
| writeBatchSize |SQL テーブルに挿入する "*バッチあたりの*" 行数。<br/>使用可能な値は、行数の場合整数です。 既定では、Azure Data Factory により行のサイズに基づいて適切なバッチ サイズが動的に決定されます。 |いいえ |
| writeBatchTimeout |このプロパティは、タイムアウトする前に一括挿入操作の完了を待つ時間を指定します。<br/>使用可能な値は期間に対する値です。 たとえば "00:30:00" (30 分) を指定できます。 値を指定しなかった場合、タイムアウトの既定値は "02:00:00" です。 |いいえ |

**例 1: データを追加する**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**例 2: コピー中にストアド プロシージャを呼び出す**

詳しくは、「[SQL シンクからのストアド プロシージャの呼び出し](#invoke-a-stored-procedure-from-a-sql-sink)」をご覧ください。

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="parallel-copy-from-sql-database"></a>SQL データベースからの並列コピー

SQL Server コネクタでは、コピー アクティビティの際に、データを並列でコピーするための組み込みのデータ パーティション分割が提供されます。 データ パーティション分割オプションは、コピー アクティビティの **[ソース]** タブにあります。

![パーティションのオプションのスクリーンショット](./media/connector-sql-server/connector-sql-partition-options.png)

パーティション分割でのコピーを有効にすると、コピー アクティビティによってユーザーの SQL Server ソースに対して並列クエリが実行され、パーティションごとにデータが読み込まれます。 並列度は、コピー アクティビティの [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 設定によって制御されます。 たとえば、`parallelCopies` を 4 に設定した場合、Data Factory では、指定したパーティション オプションと設定に基づいて 4 つのクエリが同時に生成され、実行されます。各クエリでは、SQL Server からデータの一部を取得します。

特に、自分の SQL Server から大量のデータを読み込む場合は、データのパーティション分割を使用した並列コピーを有効にすることをお勧めします。 さまざまなシナリオの推奨構成を以下に示します。 ファイルベースのデータ ストアにデータをコピーする場合は、複数のファイルとしてフォルダーに書き込む (フォルダー名のみを指定する) ことをお勧めします。この場合、1 つのファイルに書き込むよりもパフォーマンスが優れています。

| シナリオ                                                     | 推奨設定                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 物理パーティションに分割された大きなテーブル全体から読み込む。        | **パーティション オプション**: テーブルの物理パーティション。 <br><br/>実行中に、Data Factory によって物理パーティションが自動的に検出され、パーティションごとにデータがコピーされます。 |
| 物理パーティションがなく、データ パーティション分割用の整数または日時の列がある大きなテーブル全体から読み込む。 | **パーティション オプション**: 動的範囲パーティション。<br>**パーティション列** (省略可能):データのパーティション分割に使用される列を指定します。 指定されていない場合は、インデックスまたは主キー列が使用されます。<br/>**パーティションの上限**と**パーティションの下限** (省略可能):パーティションのストライドを決定する場合に指定します。 これは、テーブル内の行のフィルター処理用ではなく、テーブル内のすべての行がパーティション分割されてコピーされます。 指定されていない場合は、コピー アクティビティによって値が自動検出されます。<br><br>たとえば、パーティション列 "ID" の値の範囲が 1 ～ 100 で、下限を 20 に、上限を 80 に設定し、並列コピーを 4 にした場合、Data Factory によって 4 つのパーティションでデータが取得されます。ID の範囲は、それぞれ、20 以下、21 ～ 50、51 ～ 80、81 以上となります。 |
| 物理パーティションがなく、データ パーティション分割用の整数列または日付/日時列がある大量のデータを、カスタム クエリを使用して読み込む。 | **パーティション オプション**: 動的範囲パーティション。<br>**クエリ**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br>**パーティション列**: データのパーティション分割に使用される列を指定します。<br>**パーティションの上限**と**パーティションの下限** (省略可能):パーティションのストライドを決定する場合に指定します。 これは、テーブル内の行のフィルター処理用ではなく、クエリ結果のすべての行がパーティション分割されてコピーされます。 指定されていない場合は、コピー アクティビティによって値が自動検出されます。<br><br>実行中に、Data Factory によって `?AdfRangePartitionColumnName` が各パーティションの実際の列名および値の範囲に置き換えられ、SQL Server に送信されます。 <br>たとえば、パーティション列 "ID" の値の範囲が 1 ～ 100 で、下限を 20 に、上限を 80 に設定し、並列コピーを 4 にした場合、Data Factory によって 4 つのパーティションでデータが取得されます。ID の範囲は、それぞれ、20 以下、21 ～ 50、51 ～ 80、81 以上となります。 |

パーティション オプションを使用してデータを読み込む場合のベスト プラクティス:

1. データ スキューを回避するため、パーティション列 (主キーや一意キーなど) には特徴のある列を選択します。 
2. テーブルに組み込みパーティションがある場合は、パフォーマンスを向上させるためにパーティション オプションとして "テーブルの物理パーティション" を使用します。  
3. Azure Integration Runtime を使用してデータをコピーする場合は、より大きな (4 より大きい) "[データ統合単位 (DIU)](copy-activity-performance-features.md#data-integration-units)" を設定すると、より多くのコンピューティング リソースを利用できます。 そこで、該当するシナリオを確認してください。
4. パーティション数は、"[コピーの並列処理の次数](copy-activity-performance-features.md#parallel-copy)" によって制御されます。この数値を大きくしすぎるとパフォーマンスが低下するため、この数値は、(DIU またはセルフホステッド IR ノードの数) × (2 から 4) に設定することをお勧めします。

**例: 複数の物理パーティションがある大きなテーブル全体から読み込む**

```json
"source": {
    "type": "SqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**例: 動的範囲パーティションを使用してクエリを実行する**

```json
"source": {
    "type": "SqlSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

## <a name="best-practice-for-loading-data-into-sql-server"></a>SQL Server にデータを読み込むときのベスト プラクティス

SQL Server にデータをコピーするときは、さまざまな書き込み動作が必要になることがあります。

- [追加](#append-data): ソース データには新しいレコードのみが含まれている。
- [アップサート](#upsert-data): ソース データには挿入と更新の両方が含まれている。
- [上書き](#overwrite-the-entire-table): 毎回ディメンション テーブル全体を再度読み込みたい。
- [カスタム ロジックでの書き込み](#write-data-with-custom-logic): 宛先テーブルへの最終挿入の前に追加の処理が必要である。

Azure Data Factory で構成する方法およびベスト プラクティスについては、対応するセクションを参照してください。

### <a name="append-data"></a>データを追加する

データの追加は、この SQL Server シンク コネクタの既定の動作です。 Azure Data Factory では、テーブルに効率的に書き込むために一括挿入が実行されます。 コピー アクティビティで、それに応じてソースとシンクを構成できます。

### <a name="upsert-data"></a>データをアップサートする

**オプション 1:** 大量のデータをコピーする場合は、コピー アクティビティを使用してすべてのレコードをステージング テーブルに一括読み込みしてから、ストアド プロシージャ アクティビティを実行して、[MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=sql-server-ver15) または INSERT/UPDATE ステートメントを 1 回で適用します。 

コピー アクティビティでは現在、データベース一時テーブルへのデータの読み込みはネイティブでサポートされていません。 複数のアクティビティを組み合わせて設定するための高度な方法があります。[SQL Database の一括 upsert シナリオの最適化](https://github.com/scoriani/azuresqlbulkupsert)に関するページを参照してください。 以下に、永続的テーブルをステージングとして使用する例を示します。

例として、Azure Data Factory で、**コピー アクティビティ**と**ストアド プロシージャ アクティビティ**を連結させたパイプラインを作成できます。 前者では、ソース ストアから SQL Server ステージング テーブル (たとえば、データセット内のテーブル名 **UpsertStagingTable**) にデータがコピーされます。 次に、後者によってストアド プロシージャが呼び出され、ステージング テーブルのソース データがターゲット テーブルにマージされて、ステージング テーブルがクリーンアップされます。

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

データベースで、前のストアド プロシージャ アクティビティから指し示されている、次の例に示すような MERGE ロジックを含むストアド プロシージャを定義します。 ターゲットは **Marketing** テーブルであり、そこには 3 つの列 (**ProfileID**、**State**、**Category**) があるものとします。 **ProfileID** 列に基づいて、アップサートを実行します。

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING UpsertStagingTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE UpsertStagingTable
END
```

**オプション 2:** [コピー アクティビティ内でのストアド プロシージャの呼び出し](#invoke-a-stored-procedure-from-a-sql-sink)を選択できます。 このアプローチでは、コピー アクティビティでの既定のアプローチとしての一括挿入を使用する代わりに、ソース テーブル内の (`writeBatchSize` プロパティで管理される) 各バッチを実行します。

### <a name="overwrite-the-entire-table"></a>テーブル全体を上書きする

コピー アクティビティ シンクで **preCopyScript** プロパティを構成できます。 この場合、実行されるコピー アクティビティごとに、Azure Data Factory で最初にスクリプトが実行されます。 次に、コピーが実行されてデータが挿入されます。 たとえば、テーブル全体を最新のデータで上書きするには、ソースから新しいデータを一括で読み込む前に、すべてのレコードを最初に削除するスクリプトを指定します。

### <a name="write-data-with-custom-logic"></a>カスタム ロジックでデータを書き込む

カスタム ロジックでデータを書き込む手順は、「[データをアップサートする](#upsert-data)」セクションで説明されている手順に似ています。 宛先テーブルへのソース データの最終挿入の前に追加の処理を適用する必要がある場合は、ステージング テーブルに読み込んだ後、ストアド プロシージャ アクティビティを呼び出すか、コピー アクティビティのシンクのストアド プロシージャを呼び出してデータを適用することができます。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> SQL シンクからのストアド プロシージャの呼び出し

SQL Server データベースにデータをコピーするときに、ユーザーが指定したストアド プロシージャを構成し、ソース テーブルの各バッチに関する追加のパラメーターと共に呼び出すこともできます。 ストアド プロシージャ機能は [テーブル値パラメーター](https://msdn.microsoft.com/library/bb675163.aspx)を利用しています。

組み込みのコピー メカニズムでは目的を達成できない場合は、ストアド プロシージャを使用できます。 1 つの例は、宛先テーブルへのソース データの最終挿入の前に追加の処理を適用する場合です。 その他の処理の例をいくつか挙げると、列のマージ、追加の値の検索、複数のテーブルへの挿入があります。

次の例では、SQL Server データベース内のテーブルに upsert を行うストアド プロシージャを使用する方法を示します。 入力データとシンクの **Marketing** テーブルには、それぞれ 3 つの列 (**ProfileID**、**State**、**Category**) があるものとします。 **ProfileID** 列に基づいてアップサートを行い、"ProductA" という特定のカテゴリに対してのみ適用します。

1. データベースで、**sqlWriterTableType** と同じ名前のテーブル型を定義します。 テーブル型のスキーマは、入力データから返されるスキーマと同じです。

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. データベース内で、**sqlWriterStoredProcedureName** と同じ名前のストアド プロシージャを定義します。 これによって指定したソースの入力データが処理され、出力テーブルにマージされます。 ストアド プロシージャ内のテーブル型のパラメーター名は、データセットで定義されている **tableName** と同じです。

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. Azure Data Factory で、コピー アクティビティの **SQL シンク** セクションを次のように定義します。

    ```json
    "sink": {
        "type": "SqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>SQL Server のデータ型のマッピング

SQL Server との間でデータをコピーするとき、SQL Server のデータ型から Azure Data Factory の中間データ型への、以下のマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| SQL Server のデータ型 | Azure Data Factory の中間データ型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| INT |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |String |

>[!NOTE]
> 10 進の中間型にマップされるデータ型の場合、コピー アクティビティでは、現在、最大 28 の有効桁数がサポートされています。 28 よりも大きな有効桁数を必要とするデータがある場合は、SQL クエリで文字列に変換することを検討してください。

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="getmetadata-activity-properties"></a>GetMetadata アクティビティのプロパティ

プロパティの詳細については、[GetMetadata アクティビティ](control-flow-get-metadata-activity.md)に関するページを参照してください。 

## <a name="using-always-encrypted"></a>Always Encrypted の使用

[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-ver15) を使用して SQL Server との間でデータをコピーする場合は、セルフホステッド統合ランタイムを介して[汎用 ODBC コネクタ](connector-odbc.md)および SQL Server ODBC ドライバーを使用します。 この SQL Server コネクタでは Always Encrypted は現在サポートされていません。 

具体的には次のとおりです。

1. セルフホステッド統合ランタイムを設定します (存在しない場合)。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。

2. SQL Server 用の 64 ビット ODBC ドライバーを[こちら](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server?view=sql-server-ver15)からダウンロードし、Integration Runtime コンピューターにインストールします。 このドライバーがどのように機能するかについては、「[SQL Server 用 ODBC ドライバーと共に Always Encrypted を使用する](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-ver15#using-the-azure-key-vault-provider)」を参照してください。

3. SQL データベースに接続するための、ODBC のデータ型を持つリンクされたサービスを作成します。 SQL 認証を使用するには、次のように ODBC 接続文字列を指定し、ユーザー名とパスワードを設定するための**基本**認証を選択します。

    ```
    Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
    ```

4. これに応じて、データセットとコピー アクティビティを ODBC のデータ型で作成します。 詳細については [ODBC コネクタ](connector-odbc.md)に関する記事をご覧ください。

## <a name="troubleshoot-connection-issues"></a>接続の問題のトラブルシューティング

1. リモート接続を受け付けるように、SQL Server インスタンスを構成します。 **SQL Server Management Studio** を開始し、**サーバー**を右クリックして、 **[プロパティ]** を選択します。 一覧から **[接続]** を選択し、 **[このサーバーへのリモート接続を許可する]** チェック ボックスをオンにします。

    ![リモート接続を有効にする](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    詳細な手順については、「[remote access サーバー構成オプションの構成](https://msdn.microsoft.com/library/ms191464.aspx)」をご覧ください。

2. **SQL Server 構成マネージャー**を開始します。 目的のインスタンスの **[SQL Server ネットワークの構成]** を展開し、 **[MSSQLSERVER のプロトコル]** を選択します。 右側のウィンドウにプロトコルが表示されます。 **[TCP/IP]** を右クリックして **[有効化]** を選択し、TCP/IP を有効にします。

    ![TCP/IP を有効にする](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    TCP/IP プロトコルの有効化の詳細および別の方法については、「[サーバー ネットワーク プロトコルの有効化または無効化](https://msdn.microsoft.com/library/ms191294.aspx)」をご覧ください。

3. 同じウィンドウで、 **[TCP/IP]** をダブルクリックして、 **[TCP/IP のプロパティ]** ウィンドウを起動します。
4. **[IP アドレス]** タブに切り替えます。下へスクロールして **[IPAll]** セクションを表示します。 **[TCP ポート]** を書き留めます。 既定値は **1433** です。
5. コンピューターに **Windows Firewall のルール** を作成し、このポート経由の受信トラフィックを許可します。 
6. **接続の確認**: 完全修飾名を使って SQL Server に接続するには、別のコンピューターから SQL Server Management Studio を使用します。 たとえば `"<machine>.<domain>.corp.<company>.com,1433"` です。

## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するページをご覧ください。
