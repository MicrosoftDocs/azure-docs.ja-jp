---
title: Azure Data Factory を使用して Azure SQL Database Managed Instance をコピー先またはコピー元としてデータをコピーする | Microsoft Docs
description: Azure Data Factory を使用して Azure SQL Database Managed Instance に、または Azure SQL Database Managed Instance からデータを移動する方法を説明します。
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
ms.openlocfilehash: 3e1978c761c365125ac94a1ecbef5f9ac7375eba
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67338618"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Azure Data Factory を使用して Azure SQL Database Managed Instance をコピー先またはコピー元としてデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure SQL Database Managed Instance をコピー先またはコピー元としてデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

Azure SQL Database Managed Instance のデータを、サポートされているシンク データ ストアにコピーできます。 サポートされている任意のソース データ ストアからマネージド インスタンスにデータをコピーすることもできます。 コピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表を参照してください。

具体的には、この Azure SQL Database Managed Instance コネクタは以下の機能をサポートします。

- SQL 認証を使用してデータをコピーする。
- ソースとして、SQL クエリまたはストアド プロシージャを使用してデータを取得する。
- シンクとして、宛先テーブルにデータを追記する、またはコピー中にカスタム ロジックを使用してストアド プロシージャを起動する。

>[!NOTE]
>Azure SQL Database Managed Instance の [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) は現在、このコネクタではサポートされていません。 回避するには、セルフホステッド統合ランタイム経由で[汎用 ODBC コネクタ](connector-odbc.md)と SQL Server ODBC ドライバーを使用できます。 ODBC ドライバーのダウンロードおよび接続文字列の構成については、[このガイダンス](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current)に従ってください。

>[!NOTE]
>サービス プリンシパルとマネージド ID の認証は現在、このコネクタではサポートされていません。 回避するには、Azure SQL Database コネクタを選択し、マネージド インスタンスのサーバーを手動で指定してください。

## <a name="prerequisites"></a>前提条件

Azure SQL Database Managed Instance の[パブリック エンドポイント](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)にアクセスするには、Azure Data Factory のマネージド Azure 統合ランタイムを使用できます。 パブリック エンドポイントを有効にするだけでなく、ネットワーク セキュリティ グループでのパブリック エンドポイント トラフィックも許可して、Azure Data Factory が確実にデータベースに接続できるようにしてください。 詳細については、[このガイダンス](../sql-database/sql-database-managed-instance-public-endpoint-configure.md)を参照してください。

Azure SQL Database Managed Instance のプライベート エンドポイントにアクセスするには、そのデータベースにアクセスできる[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)を設定します。 セルフホステッド統合ランタイムをマネージド インスタンスと同じ仮想ネットワーク内でプロビジョニングする場合は、統合ランタイム コンピューターがマネージド インスタンスとは別のサブネットにあることを確認してください。 セルフホステッド統合ランタイムをマネージド インスタンスとは別の仮想ネットワークにプロビジョニングする場合は、仮想ネットワーク ピアリングまたは仮想ネットワーク間接続のどちらかを使用できます。 詳細については、「[Azure SQL Database Managed Instance にアプリケーションを接続する](../sql-database/sql-database-managed-instance-connect-app.md)」を参照してください。

## <a name="get-started"></a>作業開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以降のセクションでは、Azure SQL Database Managed Instance コネクタに固有の Azure Data Factory エンティティを定義するために使用されるプロパティについて詳細に説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure SQL Database Managed Instance のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **SqlServer** に設定する必要があります。 | はい |
| connectionString |このプロパティは、SQL 認証を使用してマネージド インスタンスに接続するために必要な **connectionString** 情報を指定します。 詳細については、次の例を参照してください。 <br/>このフィールドは、Azure Data Factory で安全に格納するために **SecureString** としてマークします。 また、Azure Key Vault にパスワードを格納することもできます。 それが SQL 認証である場合は、接続文字列から `password` 構成を取得します。 詳細については、この表の後にある JSON の例および「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」を参照してください。 |はい |
| connectVia | この[統合ランタイム](concepts-integration-runtime.md)は、データ ストアに接続するために使用されます。 マネージド インスタンスにパブリック エンドポイントがあり、Azure Data Factory からそれにアクセスできるようにする場合は、セルフホステッド統合ランタイムまたは Azure 統合ランタイムを使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |はい |

**例 1:SQL 認証を使用する**

既定のポートは 1433 です。 パブリック エンドポイントを持つ Azure SQL Database Managed Instance を使用している場合は、ポート 3342 を明示的に指定します。

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
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

既定のポートは 1433 です。 パブリック エンドポイントを持つ Azure SQL Database Managed Instance を使用している場合は、ポート 3342 を明示的に指定します。

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
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

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、Azure SQL Database Managed Instance データセットでサポートされるプロパティの一覧を示します。

Azure SQL Database Managed Instance をコピー元またはコピー先にしたデータ コピーについては、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは **SqlServerTable** に設定する必要があります。 | はい |
| tableName |このプロパティは、リンクされたサービスが参照するデータベース インスタンスのテーブルまたはビューの名前です。 | ソースの場合はいいえ、シンクの場合ははい |

**例**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Azure SQL Database Managed Instance のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-sql-database-managed-instance-as-a-source"></a>ソースとしての Azure SQL Database Managed Instance

Azure SQL Database Managed Instance からデータをコピーする場合は、コピー アクティビティのソースの種類を **SqlSource** に設定します。 コピー アクティビティの source セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **SqlSource** に設定する必要があります。 | はい |
| sqlReaderQuery |このプロパティは、カスタム SQL クエリを使用してデータを読み取ります。 例: `select * from MyTable`。 |いいえ |
| sqlReaderStoredProcedureName |このプロパティは、ソース テーブルからデータを読み取るストアド プロシージャの名前です。 最後の SQL ステートメントはストアド プロシージャの SELECT ステートメントにする必要があります。 |いいえ |
| storedProcedureParameters |これらのパラメーターは、ストアド プロシージャ用です。<br/>使用可能な値は、名前または値のペアです。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ |

**以下の点に注意してください。**

- **SqlSource** に **sqlReaderQuery** が指定されている場合、コピー アクティビティでは、データを取得するためにマネージド インスタンス ソースに対してこのクエリを実行します。 **sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することもできます (ストアド プロシージャでパラメーターを使用する場合)。
- **sqlReaderQuery** または **sqlReaderStoredProcedureName** プロパティを指定しない場合は、データセット JSON の "structure" セクションで定義されている列を使用して、クエリが作成されます。 クエリ `select column1, column2 from mytable` はマネージド インスタンスに対して実行されます。 データセット定義に "structure" がない場合は、すべての列がテーブルから選択されます。

**例:SQL クエリの使用**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>シンクとしての Azure SQL Database Managed Instance

> [!TIP]
> サポートされる書き込み動作、構成、およびベスト プラクティスの詳細については、「[Azure SQL Database Managed Instance にデータを読み込むためのベスト プラクティス](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)」を参照してください。

Azure SQL Database Managed Instance にデータをコピーする場合は、コピー アクティビティのシンクの種類を **SqlSink** に設定します。 コピー アクティビティの sink セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは、**SqlSink** に設定する必要があります。 | はい |
| writeBatchSize |SQL テーブルに挿入する*バッチあたりの*行数。<br/>使用可能な値は、行数の場合整数です。 既定では、Azure Data Factory は、行のサイズに基づいて適切なバッチ サイズを動的に決定します。  |いいえ |
| writeBatchTimeout |このプロパティは、タイムアウトする前に一括挿入操作の完了を待つ時間を指定します。<br/>使用可能な値は期間に対する値です。 たとえば "00:30:00" (30 分) を指定できます。 |いいえ |
| preCopyScript |このプロパティは、コピー アクティビティでマネージド インスタンスにデータを書き込む前に実行する SQL クエリを指定します。 これは、コピー実行ごとに 1 回だけ呼び出されます。 このプロパティを使用して、事前に読み込まれたデータをクリーンアップできます。 |いいえ |
| sqlWriterStoredProcedureName |この名前は、ターゲット テーブルにソース データを適用する方法を定義しているストアド プロシージャの名前です。 <br/>このストアド プロシージャは*バッチごとに呼び出されます*。 1 回だけ実行され、ソース データとは関係がない操作 (削除/切り詰めなど) を実行するには、`preCopyScript` プロパティを使用します。 |いいえ |
| storedProcedureParameters |これらのパラメーターはストアド プロシージャに使います。<br/>使用可能な値は、名前または値のペアです。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ |
| sqlWriterTableType |このプロパティは、ストアド プロシージャで使用するテーブル型の名前を指定します。 コピー アクティビティでは、このテーブル型の一時テーブルでデータを移動できます。 その後、ストアド プロシージャのコードにより、コピーされたデータを既存のデータと結合できます。 |いいえ |

**例 1:データを追加する**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
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
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance にデータを読み込むためのベスト プラクティス

Azure SQL Database Managed Instance にデータをコピーする場合は、さまざまな書き込み動作が必要になることがあります。

- [追加](#append-data): ソース データには新しいレコードのみが含まれている。
- [アップサート](#upsert-data): ソース データには挿入と更新の両方が含まれている。
- [上書き](#overwrite-the-entire-table): 毎回ディメンション テーブル全体を再度読み込みたい。
- [カスタム ロジックでの書き込み](#write-data-with-custom-logic): 宛先テーブルへの最終挿入の前に追加の処理が必要である。 

Azure Data Factory で構成する方法およびベスト プラクティスについては、対応するセクションを参照してください。

### <a name="append-data"></a>データを追加する

データの追加は、この Azure SQL Database Managed Instance シンク コネクタの既定の動作です。 Azure Data Factory は、テーブルに効率的に書き込むために一括挿入を実行します。 コピー アクティビティで、それに応じてソースとシンクを構成できます。

### <a name="upsert-data"></a>データをアップサートする

**オプション 1:** コピーするデータが大量に存在する場合は、次のアプローチを使用してアップサートを実行します。 

- 最初に、[一時テーブル](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables)を使用して、コピー アクティビティですべてのレコードを一括で読み込みます。 一時テーブルに対する操作はログに記録されないため、数百万のレコードを数秒で読み込むことができます。
- Azure Data Factory でストアド プロシージャ アクティビティを実行して [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) または INSERT/UPDATE ステートメントを適用します。 一時テーブルをソースとして使用して、すべての更新または挿入を 1 つのトランザクションとして実行します。 この方法により、ラウンド トリップやログ操作の数が削減されます。 ストアド プロシージャ アクティビティの最後に、次のアップサート サイクルの準備のために一時テーブルを切り捨てることができます。

例として、Azure Data Factory で、**コピー アクティビティ**と**ストアド プロシージャ アクティビティ**を連結させたパイプラインを作成できます。 前者は、ソース ストアから一時テーブル (たとえば、データセット内のテーブル名 **##UpsertTempTable**) にデータをコピーします。 次に、後者がストアド プロシージャを呼び出して、一時テーブルのソース データをターゲット テーブルにマージし、一時テーブルをクリーンアップします。

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

コピー アクティビティ シンクで **preCopyScript** プロパティを構成できます。 この場合は、実行されるコピー アクティビティごとに、Azure Data Factory は最初にスクリプトを実行します。 次に、コピーを実行してデータを挿入します。 たとえば、テーブル全体を最新のデータで上書きするには、ソースから新しいデータを一括で読み込む前に、すべてのレコードを最初に削除するスクリプトを指定します。

### <a name="write-data-with-custom-logic"></a>カスタム ロジックでデータを書き込む

カスタム ロジックでデータを書き込む手順は、「[データをアップサートする](#upsert-data)」セクションで説明されている手順に似ています。 宛先テーブルへのソース データの最終挿入の前に (大規模な) 追加の処理を適用する必要がある場合は、次の 2 つのうちの 1 つを実行できます。 

- 一時テーブルに読み込んでから、ストアド プロシージャを呼び出す。
- コピー中にストアド プロシージャを呼び出す。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> SQL シンクからのストアド プロシージャの呼び出し

Azure SQL Database Managed Instance にデータをコピーする場合は、ユーザーが指定したストアド プロシージャを構成し、追加のパラメーターを指定して呼び出すこともできます。

> [!TIP]
> ストアド プロシージャを呼び出すと、一括操作を使用する (これは、大規模なコピーにはお勧めできません) 代わりに行ごとにデータが処理されます。 詳細については、「[Azure SQL Database Managed Instance にデータを読み込むためのベスト プラクティス](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)」を参照してください。

組み込みのコピー メカニズムでは目的を達成できない場合は、ストアド プロシージャを使用できます。 1 つの例は、宛先テーブルへのソース データの最終挿入の前に追加の処理を適用する場合です。 追加の処理のいくつかの例として、列のマージ、追加の値の検索、複数のテーブルへのデータの挿入があります。

次の例では、SQL Server データベース内のテーブルに upsert を行うストアド プロシージャを使用する方法を示します。 入力データとシンクの **Marketing** テーブルには、それぞれ 3 つの列 (**ProfileID**、**State**、**Category**) があるものとします。 **ProfileID** 列に基づいてアップサートを行い、特定のカテゴリに対してのみ適用します。

**出力データセット:** "tableName" は、次のストアド プロシージャ スクリプトに示すように、ストアド プロシージャ内の同じテーブル型パラメーター名です。

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

コピー アクティビティの **SQL シンク**セクションを次のように定義します。

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

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance のデータ型のマッピング

Azure SQL Database Managed Instance をコピー元またはコピー先としてデータをコピーするとき、次の Azure SQL Database Managed Instance のデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| Azure SQL Database Managed Instance のデータ型 | Azure Data Factory の中間データ型 |
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

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)に関するページをご覧ください。
