---
title: Azure Data Factory を使用した SQL Server との間でのデータのコピー | Microsoft Docs
description: Azure Data Factory を使用してオンプレミスまたは Azure VM の SQL Server データベースとの間でデータを移動する方法を説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: jingwang
ms.openlocfilehash: cb1b8171dc45c286d3f87a3c33e366d818cfaad9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59283411"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Azure Data Factory を使用した SQL Server との間でのデータのコピー
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-sqlserver-connector.md)
> * [現在のバージョン](connector-sql-server.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、SQL Server データベースをコピー先またはコピー元としてデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

SQL Server データベースにデータをコピーする、SQL Server データベースから任意のサポートされるシンク データ ストアにデータをコピーする、または任意のサポートされるソース データ ストアから SQL Server データベースにデータをコピーすることができます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この SQL Server コネクタは以下をサポートします。

- SQL Server バージョン 2016、2014、2012、2008 R2、2008、および 2005
- **SQL** または **Windows** 認証を使用したデータのコピー
- ソースとしての SQL クエリまたはストアド プロシージャを使用したデータの取得。
- シンクとして、宛先テーブルにデータを追記する、またはコピー中にカスタム ロジックを使用してストアド プロシージャを起動する。

SQL Server での [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) は現在サポートされていません。

## <a name="prerequisites"></a>前提条件

パブリックにアクセスできない SQL Server データベースからコピーしたデータを使用するには、セルフホステッド統合ランタイムを設定する必要があります。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。 統合ランタイムには SQL Server データベース ドライバーが組み込まれているため、SQL Server データベースとの間でデータをコピーするときにドライバーを手動でインストールする必要はありません。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、SQL Server データベース コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SQL Server のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**SqlServer** | はい |
| connectionString |SQL 認証または Windows 認証を使用して、SQL Server データベースに接続するために必要な connectionString 情報を指定します。 以下のサンプルを参照してください。<br/>Data Factory に安全に格納するには、このフィールドを SecureString として指定します。 パスワードを Azure Key Vault に格納して、それが SQL 認証の場合は接続文字列から `password` 構成をプルすることもできます。 詳しくは、表の下の JSON の例と、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」の記事をご覧ください。 |はい |
| userName |Windows 認証を使用している場合は、ユーザー名を指定します。 例: **domainname\\username**。 |いいえ  |
| password |userName に指定したユーザー アカウントのパスワードを指定します。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 |いいえ  |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 セルフホステッド統合ランタイムまたは Azure 統合ランタイム (データ ストアがパブリックにアクセスできる場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ  |

>[!TIP]
>エラー コード "UserErrorFailedToConnectToSqlServer" および "The session limit for the database is XXX and has been reached." (データベースのセッション制限 XXX に達しました) のようなメッセージのエラーが発生する場合は、`Pooling=false` を接続文字列に追加して、もう一度試してください。

**例 1: SQL 認証の使用**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例 2: Azure Key Vault でのパスワードによる SQL 認証の使用**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
            },
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

**例 3: Windows 認証の使用**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、SQL Server データセットでサポートされるプロパティの一覧を示します。

SQL Server データベースとの間でデータをコピーするには、データセットの type プロパティを **SqlServerTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**SqlServerTable** | はい |
| tableName |リンクされたサービスが参照する SQL Server Database インスタンスのテーブルまたはビューの名前です。 | ソースの場合はいいえ、シンクの場合ははい |

**例:**

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
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、SQL Server ソースおよびシンクでサポートされるプロパティの一覧を示します。

### <a name="sql-server-as-source"></a>ソースとしての SQL Server

SQL Server からデータをコピーするには、コピー アクティビティのソースの種類を **SqlSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**SqlSource** | はい |
| sqlReaderQuery |カスタム SQL クエリを使用してデータを読み取ります。 例: `select * from MyTable`. |いいえ  |
| sqlReaderStoredProcedureName |ソース テーブルからデータを読み取るストアド プロシージャの名前。 最後の SQL ステートメントはストアド プロシージャの SELECT ステートメントにする必要があります。 |いいえ  |
| storedProcedureParameters |ストアド プロシージャのパラメーター。<br/>使用可能な値: 名前/値ペア。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ  |

**注意する点:**

- SqlSource に **sqlReaderQuery** が指定されている場合、コピー アクティビティでは、データを取得するために SQL Server ソースに対してこのクエリを実行します。 または、**sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することができます (ストアド プロシージャでパラメーターを使用する場合)。
- "sqlReaderQuery" または "sqlReaderStoredProcedureName" を指定しない場合は、データセット JSON の "structure" セクションに定義された列を使用して、SQL Server に対して実行するクエリ (`select column1, column2 from mytable`) が作成されます。 データセット定義に "構造" がない場合は、すべての列がテーブルから選択されます。

**例: SQL クエリの使用**

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

**例: ストアド プロシージャの使用**

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

**ストアド プロシージャの定義:**

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

### <a name="sql-server-as-sink"></a>シンクとしての SQL Server

SQL Server にデータをコピーするには、コピー アクティビティのシンクの種類を **SqlSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは、次のように設定する必要があります: **SqlSink** | はい |
| writeBatchSize |SQL テーブルに挿入する**バッチあたりの**行数。<br/>使用可能な値: 整数 (行数)。 |いいえ (既定値: 10000) |
| writeBatchTimeout |タイムアウトする前に一括挿入操作の完了を待つ時間です。<br/>使用可能な値: 期間。 例:"00:30:00" (30 分)。 |いいえ  |
| preCopyScript |コピー アクティビティでデータを SQL Server に書き込む前に実行する SQL クエリを指定します。 これは、コピー実行ごとに 1 回だけ呼び出されます。 このプロパティを使用して、事前に読み込まれたデータをクリーンアップできます。 |いいえ  |
| sqlWriterStoredProcedureName |ソース データをターゲット テーブルに適用する方法、たとえば、独自のビジネス ロジックを使用してアップサートまたは変換を実行する方法を定義するストアド プロシージャの名前です。 <br/><br/>このストアド プロシージャは**バッチごとに呼び出される**ことに注意してください。 1 回だけ実行され、ソース データとは関係がない操作 (削除/切り詰めなど) を実行する場合は、`preCopyScript` プロパティを使用します。 |いいえ  |
| storedProcedureParameters |ストアド プロシージャのパラメーター。<br/>使用可能な値: 名前/値ペア。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ  |
| sqlWriterTableType |ストアド プロシージャで使用するテーブル型の名前を指定します。 コピー アクティビティでは、このテーブル型の一時テーブルでデータを移動できます。 その後、ストアド プロシージャのコードにより、コピーされたデータを既存のデータと結合できます。 |いいえ  |

> [!TIP]
> SQL Server にデータをコピーすると、既定では、コピー アクティビティによりデータがシンク テーブルに付加されます。 UPSERT または追加のビジネス ロジックを実行するには、SqlSink でストアド プロシージャを使用します。 詳細については、「[SQL シンクのストアド プロシージャの呼び出し](#invoking-stored-procedure-for-sql-sink)」を参照してください。

**例 1: データの付加**

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
                "writeBatchSize": 100000
            }
        }
    }
]
```

**例 2: upsert でコピー中にストアド プロシージャを呼び出す**

詳細については、「[SQL シンクのストアド プロシージャの呼び出し](#invoking-stored-procedure-for-sql-sink)」を参照してください。

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
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="identity-columns-in-the-target-database"></a>ターゲット データベースの ID 列

このセクションでは、ID 列がないソース テーブルから ID 列がある対象テーブルにデータをコピーする例を示します。

**ソース テーブル:**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```

**対象テーブル:**

```sql
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```

ターゲット テーブルには ID 列があることに注意してください。

**ソース データセット JSON の定義**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**ターゲット データセット JSON の定義**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

ソースとターゲット テーブルには異なるスキーマがあることに注意してください (ターゲットには ID を持つ追加の列があります)。 このシナリオでは、ターゲット データセット定義で **structure** プロパティを指定する必要があります。ここでは、ID 列は含みません。

## <a name="invoking-stored-procedure-for-sql-sink"></a> SQL シンクからのストアド プロシージャの呼び出し

データの SQL Server データベースへのコピー時に、ユーザーが指定したストアド プロシージャを構成し、追加のパラメーターと共に呼び出すことができます。

組み込みのコピー メカニズムが目的どおり機能しない場合は、ストアド プロシージャを使用できます。 通常は、宛先テーブルでのソース データの最終挿入前に、upsert (挿入 + 更新) または追加処理 (列の結合、追加の値の検索、複数のテーブルへの挿入など) を実行する必要があるときに使用します。

次の例では、SQL Server データベース内のテーブルに upsert を行うストアド プロシージャを使用する方法を示します。 入力データと、シンクの **Marketing** テーブルのそれぞれに 3 つの列 (**ProfileID**、**State**、**Category**) があるものとします。 **ProfileID** 列に基づいてアップサートを行い、特定のカテゴリに対してのみ適用します。

**出力データセット:** "tableName" は、ストアド プロシージャ内の同じテーブル型のパラメーター名である必要があります (次のストアド プロシージャ スクリプトを参照)。

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
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

次のように、コピー アクティビティの **SQL シンク** セクションを定義します。

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

データベース内で、**SqlWriterStoredProcedureName** と同じ名前のストアド プロシージャを定義します。 これによって指定したソースの入力データが処理され、出力テーブルにマージされます。 ストアド プロシージャ内のテーブル型のパラメーター名は、データセットで定義された **tableName** と同じにする必要があります。

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

データベースで、sqlWriterTableType と同じ名前のテーブル型を定義します。 テーブル型のスキーマは、入力データから返されるスキーマと同じにする必要があります。

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

ストアド プロシージャ機能は [テーブル値パラメーター](https://msdn.microsoft.com/library/bb675163.aspx)を利用しています。

## <a name="data-type-mapping-for-sql-server"></a>SQL Server のデータ型のマッピング

SQL Server との間でデータをコピーするとき、SQL Server のデータ型から Azure Data Factory の中間データ型への、以下のマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| SQL Server のデータ型 | Data Factory の中間データ型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String、Char[] |
| date |Datetime |
| DateTime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM 属性 (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String、Char[] |
| ntext |String、Char[] |
| numeric |Decimal |
| nvarchar |String、Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String、Char[] |
| time |timespan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String、Char[] |
| xml |xml |

>[!NOTE]
> 10 進の中間型にマップされるデータ型の場合、現在 ADF では最大 28 の有効桁数をサポートしています。 28 よりも大きな有効桁数のデータがある場合は、SQL クエリで文字列に変換することを検討してください。

## <a name="troubleshooting-connection-issues"></a>接続の問題のトラブルシューティング

1. リモート接続を許可するよう、SQL Server を構成します。 **SQL Server Management Studio** を起動し、**サーバー**を右クリックして、**[プロパティ]** をクリックします。 一覧から **[接続]** を選択し、**[このサーバーへのリモート接続を許可する]** をオンにします。

    ![リモート接続を有効にする](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    詳細な手順については、「 [remote access サーバー構成オプションの構成](https://msdn.microsoft.com/library/ms191464.aspx) 」をご覧ください。

2. **SQL Server 構成マネージャー**を起動します。 目的のインスタンスの **[SQL Server ネットワークの構成]** を展開し、**[MSSQLSERVER のプロトコル]** を選択します。 右側のウィンドウにプロトコルが表示されます。 **[TCP/IP]** を右クリックし、**[有効化]** をクリックして TCP/IP を有効にします。

    ![TCP/IP を有効にする](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    詳細および TCP/IP プロトコルを有効にする別の方法については、「 [サーバー ネットワーク プロトコルの有効化または無効化](https://msdn.microsoft.com/library/ms191294.aspx) 」をご覧ください。

3. 同じウィンドウで、**[TCP/IP]** をダブルクリックして、**[TCP/IP のプロパティ]** ウィンドウを起動します。
4. **[IP アドレス]** タブに切り替えます。下へスクロールして **[IPAll]** セクションを表示します。 **[TCP ポート]** の値をメモしておきます (既定値は **1433** です)。
5. コンピューターに **Windows Firewall のルール** を作成し、このポート経由の受信トラフィックを許可します。  
6. **接続の確認**: 完全修飾名を使って SQL Server に接続するには、別のマシンから SQL Server Management Studio を使用します。 (例: `"<machine>.<domain>.corp.<company>.com,1433"`)。

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。
