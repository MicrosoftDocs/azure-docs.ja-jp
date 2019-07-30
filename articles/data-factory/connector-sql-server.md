---
title: Azure Data Factory を使用して SQL Server をコピー元またはコピー先としてデータをコピーする | Microsoft Docs
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
ms.date: 06/13/2019
ms.author: jingwang
ms.openlocfilehash: a6767c7c8931898c44fd748dbe4299b8ed23eb9c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443284"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Azure Data Factory を使用して SQL Server をコピー元またはコピー先としてデータをコピーする
> [!div class="op_single_selector" title1="使用している Azure Data Factory のバージョンを選択してください。"]
> * [Version 1](v1/data-factory-sqlserver-connector.md)
> * [現在のバージョン](connector-sql-server.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、SQL Server データベースをコピー先またはコピー元としてデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

SQL Server データベースから、サポートされている任意のシンク データ ストアにデータをコピーできます。 または、任意のサポートされているソース データ ストアから SQL Server データベースにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この SQL Server コネクタは以下をサポートします。

- SQL Server バージョン 2016、2014、2012、2008 R2、2008、2005。
- SQL または Windows 認証を使用したデータのコピー。
- ソースとして、SQL クエリまたはストアド プロシージャを使用してデータを取得する。
- シンクとして、宛先テーブルにデータを追記する、またはコピー中にカスタム ロジックを使用してストアド プロシージャを起動する。

>[!NOTE]
>SQL Server の [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) は現在、このコネクタではサポートされていません。 回避するには、[汎用 ODBC コネクタ](connector-odbc.md)と SQL Server ODBC ドライバーを使用できます。 ODBC ドライバーのダウンロードおよび接続文字列の構成については、[このガイダンス](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017)に従ってください。

## <a name="prerequisites"></a>前提条件

パブリックにアクセスできない SQL Server データベースからコピーしたデータを使用するには、セルフホステッド統合ランタイムを設定する必要があります。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関するセクションを参照してください。 統合ランタイムには、組み込みの SQL Server データベース ドライバーがあります。 SQL Server データベースをコピー元またはコピー先としてデータをコピーするときに、ドライバーを手動でインストールする必要はありません。

## <a name="get-started"></a>作業開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、SQL Server データベース コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SQL Server のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **SqlServer** に設定する必要があります。 | はい |
| connectionString |SQL 認証または Windows 認証を使用して、SQL Server データベースに接続するために必要な **connectionString** 情報を指定します。 以下のサンプルを参照してください。<br/>このフィールドは、Azure Data Factory で安全に格納するために **SecureString** としてマークします。 また、Azure Key Vault にパスワードを格納することもできます。 それが SQL 認証である場合は、接続文字列から `password` 構成を取得します。 詳細については、この表の後にある JSON の例および「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」を参照してください。 |はい |
| userName |Windows 認証を使用しする場合は、ユーザー名を指定します。 例: **domainname\\username**。 |いいえ |
| password |ユーザー名に指定したユーザー アカウントのパスワードを指定します。 このフィールドは、Azure Data Factory で安全に格納するために **SecureString** としてマークします。 また、[Azure Key Vault に格納されているシークレットを参照する](store-credentials-in-key-vault.md)こともできます。 |いいえ |
| connectVia | この[統合ランタイム](concepts-integration-runtime.md)は、データ ストアに接続するために使用されます。 セルフホステッド統合ランタイムまたは Azure Integration Runtime (データ ストアがパブリックにアクセスできる場合) を使用できます。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

>[!TIP]
>エラー コード "UserErrorFailedToConnectToSqlServer" および "The session limit for the database is XXX and has been reached" (データベースのセッション制限 XXX に達しました) のようなメッセージのエラーが発生する場合は、`Pooling=false` を接続文字列に追加して、もう一度試してください。

**例 1:SQL 認証を使用する**

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

**例 2:Azure Key Vault 内のパスワードで SQL 認証を使用する**

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

**例 3: Windows 認証を使用する**

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

SQL Server データベースをコピー元またはコピー先にしたデータ コピーについては、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは **SqlServerTable** に設定する必要があります。 | はい |
| tableName |このプロパティは、リンクされたサービスが参照する SQL Server データベース インスタンスのテーブルまたはビューの名前です。 | ソースの場合はいいえ、シンクの場合ははい |

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
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、SQL Server のソースおよびシンクでサポートされるプロパティの一覧を示します。

### <a name="sql-server-as-a-source"></a>ソースとしての SQL Server

SQL Server からデータをコピーするには、コピー アクティビティのソースの種類を **SqlSource** に設定します。 コピー アクティビティの source セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **SqlSource** に設定する必要があります。 | はい |
| sqlReaderQuery |カスタム SQL クエリを使用してデータを読み取ります。 例: `select * from MyTable`。 |いいえ |
| sqlReaderStoredProcedureName |このプロパティは、ソース テーブルからデータを読み取るストアド プロシージャの名前です。 最後の SQL ステートメントはストアド プロシージャの SELECT ステートメントにする必要があります。 |いいえ |
| storedProcedureParameters |これらのパラメーターは、ストアド プロシージャ用です。<br/>使用可能な値は、名前または値のペアです。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ |

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
| writeBatchSize |SQL テーブルに挿入する "*バッチあたりの*" 行数。<br/>使用可能な値は、行数の場合整数です。 既定では、Azure Data Factory により行のサイズに基づいて適切なバッチ サイズが動的に決定されます。 |いいえ |
| writeBatchTimeout |このプロパティは、タイムアウトする前に一括挿入操作の完了を待つ時間を指定します。<br/>使用可能な値は期間に対する値です。 たとえば "00:30:00" (30 分) を指定できます。 |いいえ |
| preCopyScript |このプロパティでは、コピー アクティビティで SQL Server にデータを書き込む前に実行する SQL クエリを指定します。 これは、コピー実行ごとに 1 回だけ呼び出されます。 このプロパティを使用して、事前に読み込まれたデータをクリーンアップできます。 |いいえ |
| sqlWriterStoredProcedureName |この名前は、ターゲット テーブルにソース データを適用する方法を定義しているストアド プロシージャの名前です。<br/>このストアド プロシージャは*バッチごとに呼び出されます*。 1 回だけ実行され、ソース データとは関係がない操作 (削除/切り詰めなど) を実行するには、`preCopyScript` プロパティを使用します。 |いいえ |
| storedProcedureParameters |これらのパラメーターはストアド プロシージャに使います。<br/>使用可能な値は、名前または値のペアです。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ |
| sqlWriterTableType |このプロパティは、ストアド プロシージャで使用するテーブル型の名前を指定します。 コピー アクティビティでは、このテーブル型の一時テーブルでデータを移動できます。 その後、ストアド プロシージャのコードにより、コピーされたデータを既存のデータと結合できます。 |いいえ |

**例 1:データを追加する**

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

**例 2:コピー中にストアド プロシージャを呼び出す**

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

**オプション 1:** コピーするデータが大量に存在する場合は、次のアプローチを使用してアップサートを実行します。 

- 最初に、[一時テーブル](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables)を使用して、コピー アクティビティですべてのレコードを一括で読み込みます。 一時テーブルに対する操作はログに記録されないため、数百万のレコードを数秒で読み込むことができます。
- Azure Data Factory でストアド プロシージャ アクティビティを実行して [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) または INSERT/UPDATE ステートメントを適用します。 一時テーブルをソースとして使用して、すべての更新または挿入を 1 つのトランザクションとして実行します。 この方法により、ラウンド トリップやログ操作の数が削減されます。 ストアド プロシージャ アクティビティの最後に、次のアップサート サイクルの準備のために一時テーブルを切り捨てることができます。

例として、Azure Data Factory で、**コピー アクティビティ**と**ストアド プロシージャ アクティビティ**を連結させたパイプラインを作成できます。 前者では、ソース ストアからデータベース一時テーブル (たとえば、データセット内のテーブル名 **##UpsertTempTable**) にデータがコピーされます。 次に、後者によってストアド プロシージャが呼び出され、一時テーブルのソース データがターゲット テーブルにマージされて、一時テーブルがクリーンアップされます。

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

データベースで、前のストアド プロシージャ アクティビティから指し示されている、次の例に示すような MERGE ロジックを含むストアド プロシージャを定義します。 ターゲットは **Marketing** テーブルであり、そこには 3 つの列 (**ProfileID**、**State**、**Category**) があるものとします。 **ProfileID** 列に基づいて、アップサートを実行します。

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**オプション 2:** [コピー アクティビティ内でのストアド プロシージャの呼び出し](#invoke-a-stored-procedure-from-a-sql-sink)も選択できます。 このアプローチでは、コピー アクティビティでの既定のアプローチとして一括挿入を使用する (これは、大規模なアップサートには適していません) 代わりに、ソース テーブル内の各行を実行します。

### <a name="overwrite-the-entire-table"></a>テーブル全体を上書きする

コピー アクティビティ シンクで **preCopyScript** プロパティを構成できます。 この場合、実行されるコピー アクティビティごとに、Azure Data Factory で最初にスクリプトが実行されます。 次に、コピーが実行されてデータが挿入されます。 たとえば、テーブル全体を最新のデータで上書きするには、ソースから新しいデータを一括で読み込む前に、すべてのレコードを最初に削除するスクリプトを指定します。

### <a name="write-data-with-custom-logic"></a>カスタム ロジックでデータを書き込む

カスタム ロジックでデータを書き込む手順は、「[データをアップサートする](#upsert-data)」セクションで説明されている手順に似ています。 宛先テーブルへのソース データの最終挿入の前に (大規模な) 追加の処理を適用する必要がある場合は、次の 2 つのうちの 1 つを実行できます。 

- 一時テーブルに読み込んでから、ストアド プロシージャを呼び出す。 
- コピー中にストアド プロシージャを呼び出す。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> SQL シンクからのストアド プロシージャの呼び出し

データを SQL Server データベースにコピーするときは、ユーザーが指定したストアド プロシージャを構成し、追加のパラメーターと共に呼び出すこともできます。

> [!TIP]
> ストアド プロシージャを呼び出すと、一括操作を使用する代わりに行ごとにデータが処理されます (大規模なコピーにはお勧めできません)。 詳しくは、「[SQL Server にデータを読み込むときのベスト プラクティス](#best-practice-for-loading-data-into-sql-server)」をご覧ください。

組み込みのコピー メカニズムでは目的を達成できない場合は、ストアド プロシージャを使用できます。 1 つの例は、宛先テーブルへのソース データの最終挿入の前に追加の処理を適用する場合です。 追加の処理のいくつかの例として、列のマージ、追加の値の検索、複数のテーブルへのデータの挿入があります。

次の例では、SQL Server データベース内のテーブルに upsert を行うストアド プロシージャを使用する方法を示します。 入力データとシンクの **Marketing** テーブルには、それぞれ 3 つの列 (**ProfileID**、**State**、**Category**) があるものとします。 **ProfileID** 列に基づいてアップサートを行い、特定のカテゴリに対してのみ適用します。

**出力データセット:** "tableName" は、次のストアド プロシージャ スクリプトで示されているように、ストアド プロシージャ内の同じテーブル型パラメーターの名前です。

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

コピー アクティビティの **SQL シンク** セクションを次のように定義します。

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

データベース内で、**SqlWriterStoredProcedureName** と同じ名前のストアド プロシージャを定義します。 これによって指定したソースの入力データが処理され、出力テーブルにマージされます。 ストアド プロシージャ内のテーブル型のパラメーター名は、データセットで定義されている **tableName** と同じです。

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

データベースで、**sqlWriterTableType** と同じ名前のテーブル型を定義します。 テーブル型のスキーマは、入力データから返されるスキーマと同じです。

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

| SQL Server のデータ型 | Azure Data Factory の中間データ型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
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
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> 10 進の中間型にマップされるデータ型の場合、現在 Azure Data Factory では最大 28 の有効桁数をサポートしています。 28 よりも大きな有効桁数を必要とするデータがある場合は、SQL クエリで文字列に変換することを検討してください。

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
6. **接続の確認**: 完全修飾名を使って SQL Server に接続するには、別のコンピューターから SQL Server Management Studio を使用します。 例: `"<machine>.<domain>.corp.<company>.com,1433"`。

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)に関するページをご覧ください。
