---
title: Data Factory を使用した Azure SQL Database との間でのデータのコピー | Microsoft Docs
description: Data Factory を使用して、サポートされるソース データ ストアから Azure SQL Database にデータをコピーしたり、SQL Database からサポートされるシンク ストアにコピーしたりする方法を説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: jingwang
ms.openlocfilehash: 5dbd739070b1f66fe5ff04f319a3818269d0bdaa
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449606"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Azure Data Factory を使用した Azure SQL Database との間でのデータのコピー
> [!div class="op_single_selector" title1="使用している Azure Data Factory のバージョンを選択してください。"]
> * [Version 1](v1/data-factory-azure-sql-connector.md)
> * [現在のバージョン](connector-azure-sql-database.md)

この記事では、Azure SQL Database をコピー先またはコピー元としてデータをコピーする方法について説明します。 Azure Data Factory については、[入門記事で](introduction.md)をご覧ください。

## <a name="supported-capabilities"></a>サポートされる機能

この Azure SQL Database コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)表での[コピー アクティビティ](copy-activity-overview.md)
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)

具体的には、この Azure SQL Database コネクタは以下の機能をサポートします。

- SQL 認証を使って、およびサービス プリンシパルまたは Azure リソースのマネージド ID で Azure Active Directory (Azure AD) アプリケーション トークン認証を使って、データをコピーする。
- ソースとして、SQL クエリまたはストアド プロシージャを使用してデータを取得する。
- シンクとして、宛先テーブルにデータを追記する、またはコピー中にカスタム ロジックを使用してストアド プロシージャを起動する。

>[!NOTE]
>Azure SQL Database の [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) は現在、このコネクタではサポートされていません。 回避するには、セルフホステッド統合ランタイム経由で[汎用 ODBC コネクタ](connector-odbc.md)と SQL Server ODBC ドライバーを使用できます。 ODBC ドライバーのダウンロードおよび接続文字列の構成については、[このガイダンス](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current)に従ってください。

> [!IMPORTANT]
> Azure Data Factory 統合ランタイムを使ってデータをコピーする場合は、Azure サービスがサーバーにアクセスできるように [Azure SQL Server ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)を構成します。
> セルフホステッド統合ランタイムを使用してデータをコピーする場合は、適切な IP 範囲を許可するように Azure SQL Server ファイアウォールを構成します。 この範囲には、Azure SQL Database への接続に使用されるコンピューターの IP アドレスが含まれています。

## <a name="get-started"></a>作業開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、Azure SQL Database コネクタに固有の Azure Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure SQL Database のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティを **AzureSqlDatabase** に設定する必要があります。 | はい |
| connectionString | **connectionString** プロパティの Azure SQL データベース インスタンスに接続するために必要な情報を指定します。 <br/>このフィールドは、Azure Data Factory で安全に格納するために **SecureString** としてマークします。 Azure Key Vault にパスワードまたはサービス プリンシパル キーを設定することもできます。 それが SQL 認証である場合は、接続文字列から `password` 構成を取得します。 詳細については、この表の後にある JSON の例および「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」を参照してください。 | はい |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 | サービス プリンシパルで Azure AD 認証を使う場合は、はい。 |
| servicePrincipalKey | アプリケーションのキーを取得します。 このフィールドを **SecureString** としてマークして Azure Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | サービス プリンシパルで Azure AD 認証を使う場合は、はい。 |
| tenant | ドメイン名やテナント ID など、アプリケーションが存在するテナントの情報を指定します。 これは、Azure portal の右上隅をマウスでポイントすることで取得できます。 | サービス プリンシパルで Azure AD 認証を使う場合は、はい。 |
| connectVia | この[統合ランタイム](concepts-integration-runtime.md)は、データ ストアに接続するために使用されます。 データ ストアがプライベート ネットワークにある場合、Azure 統合ランタイムまたはセルフホステッド統合ランタイムを使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | いいえ |

さまざまな認証の種類の前提条件と JSON サンプルについては、以下のセクションをご覧ください。

- [SQL 認証](#sql-authentication)
- [Azure AD アプリケーション トークン認証: サービス プリンシパル](#service-principal-authentication)
- [Azure AD アプリケーション トークン認証: Azure リソースのマネージド ID](#managed-identity)

>[!TIP]
>エラー コード "UserErrorFailedToConnectToSqlServer" および "The session limit for the database is XXX and has been reached" (データベースのセッション制限 XXX に達しました) のようなメッセージのエラーが発生する場合は、`Pooling=false` を接続文字列に追加して、もう一度試してください。

### <a name="sql-authentication"></a>SQL 認証

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL 認証を使用するリンクされたサービスの例

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Azure Key Vault 内のパスワード** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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

### <a name="service-principal-authentication"></a>サービス プリンシパルの認証

サービス プリンシパル ベースの Azure AD アプリケーション トークン認証を使うには、以下の手順のようにします。

1. Azure portal から [Azure Active Directory アプリケーションを作成します](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)。 アプリケーション名と、リンクされたサービスを定義する次の値を記録しておきます。

    - アプリケーション ID
    - アプリケーション キー
    - テナント ID

2. まだ行っていない場合は、Azure portal で Azure SQL Server の [Azure Active Directory 管理者をプロビジョニングします](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)。 Azure AD 管理者は、Azure AD ユーザーまたは Azure AD グループでなければなりませんが、サービス プリンシパルにはなれません。 このステップは、次のステップで Azure AD ID を使ってサービス プリンシパルの包含データベース ユーザーを作成できるようにするために行われます。

3. サービス プリンシパルの[包含データベース ユーザーを作成します](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)。 SQL Server Management Studio のようなツールと、少なくとも ALTER ANY USER アクセス許可を持つ Azure AD ID を使用して、データをコピーするデータベースに接続します。 次の T-SQL を実行します。 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. SQL ユーザーや他のユーザーに対する通常の方法で、サービス プリンシパルに必要なアクセス許可を付与します。 次のコードを実行します。 詳細については、[こちらのドキュメント](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)を参照してください。

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. Azure Data Factory で、Azure SQL Database のリンクされたサービスを構成します。


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>サービス プリンシパル認証を使うリンクされたサービスの例

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Azure リソースのマネージド ID 認証

データ ファクトリは、特定のデータ ファクトリを表す [Azure リソースのマネージド ID](data-factory-service-identity.md) に関連付けることができます。 このマネージド ID を Azure SQL Database の認証に使用できます。 指定されたファクトリは、この ID を使用してデータベースにアクセスし、データを双方向にコピーできます。

マネージド ID 認証を使用するには、次の手順に従います。

1. まだ行っていない場合は、Azure portal で Azure SQL Server の [Azure Active Directory 管理者をプロビジョニングします](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)。 Azure AD 管理者には、Azure AD ユーザーまたは Azure AD グループを使用できます。 マネージド ID を含むグループに管理者ロールを付与する場合は、手順 3. と手順 4. をスキップします。 管理者はデータベースに対してフル アクセス権を持っています。

2. Azure Data Factory のマネージド ID 用に[包含データベース ユーザーを作成](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)します。 SQL Server Management Studio のようなツールと、少なくとも ALTER ANY USER アクセス許可を持つ Azure AD ID を使用して、データをコピーするデータベースに接続します。 次の T-SQL を実行します。 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. SQL ユーザーや他のユーザーに対する通常の方法と同様に、Data Factory のマネージド ID に必要なアクセス許可を付与します。 次のコードを実行します。 詳細については、[こちらのドキュメント](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)を参照してください。

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. Azure Data Factory で、Azure SQL Database のリンクされたサービスを構成します。

**例**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
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

データセットの定義に使用できるセクションとプロパティの詳細な一覧については、[データセット](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services)に関するページを参照してください。 このセクションでは、Azure SQL Database データセットでサポートされるプロパティの一覧を示します。

Azure SQL Database をコピー元またはコピー先にしたデータ コピーについては、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの **type** プロパティは、**AzureSqlTable** に設定する必要があります。 | はい |
| tableName | リンクされたサービスが参照する Azure SQL データベース インスタンスのテーブルまたはビューの名前。 | ソースの場合はいいえ、シンクの場合ははい |

#### <a name="dataset-properties-example"></a>データセットのプロパティの例

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関するページを参照してください。 このセクションでは、Azure SQL Database のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-sql-database-as-the-source"></a>ソースとしての Azure SQL Database

Azure SQL Database からデータをコピーする場合は、コピー アクティビティのソースで **type** プロパティを **SqlSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティを **SqlSource** に設定する必要があります。 | はい |
| sqlReaderQuery | このプロパティは、カスタム SQL クエリを使用してデータを読み取ります。 例: `select * from MyTable`。 | いいえ |
| sqlReaderStoredProcedureName | ソース テーブルからデータを読み取るストアド プロシージャの名前。 最後の SQL ステートメントはストアド プロシージャの SELECT ステートメントにする必要があります。 | いいえ |
| storedProcedureParameters | ストアド プロシージャのパラメーター。<br/>使用可能な値は、名前または値のペアです。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 | いいえ |

**注意する点:**

- **SqlSource** に **sqlReaderQuery** が指定されている場合、コピー アクティビティでは、データを取得するために Azure SQL Database ソースに対してこのクエリを実行します。 **sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することもできます (ストアド プロシージャでパラメーターを使用する場合)。
- **sqlReaderQuery** または **sqlReaderStoredProcedureName** を指定しない場合は、データセット JSON の "structure" セクションで定義されている列を使用して、クエリが作成されます。 クエリ `select column1, column2 from mytable` は Azure SQL Database に対して実行されます。 データセット定義に "structure" がない場合は、すべての列がテーブルから選択されます。

#### <a name="sql-query-example"></a>SQL クエリの例

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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

#### <a name="stored-procedure-example"></a>ストアド プロシージャの例

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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

### <a name="stored-procedure-definition"></a>ストアド プロシージャの定義

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

### <a name="azure-sql-database-as-the-sink"></a>シンクとしての Azure SQL Database

> [!TIP]
> サポートされる書き込み動作、構成、およびベスト プラクティスの詳細については、「[Azure SQL Database にデータを読み込む際のベスト プラクティス](#best-practice-for-loading-data-into-azure-sql-database)」を参照してください。

Azure SQL Database にデータをコピーする場合は、コピー アクティビティのシンクで **type** プロパティを **SqlSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの **type** プロパティは、**SqlSink** に設定する必要があります。 | はい |
| writeBatchSize | SQL テーブルに挿入する "*バッチあたりの*" 行数。<br/> 使用可能な値は **integer** (行数) です。 既定では、Azure Data Factory は、行のサイズに基づいて適切なバッチ サイズを動的に決定します。 | いいえ |
| writeBatchTimeout | タイムアウトする前に一括挿入操作の完了を待つ時間です。<br/> 使用可能な値は **timespan** です。 たとえば "00:30:00" (30 分) を指定できます。 | いいえ |
| preCopyScript | コピー アクティビティがデータを Azure SQL Database に書き込む前に実行する SQL クエリを指定します。 これは、コピー実行ごとに 1 回だけ呼び出されます。 前に読み込まれたデータをクリーンアップするには、このプロパティを使います。 | いいえ |
| sqlWriterStoredProcedureName | ターゲット テーブルにソース データを適用する方法を定義しているストアド プロシージャの名前です。 <br/>このストアド プロシージャは*バッチごとに呼び出されます*。 1 回だけ実行され、ソース データとは関係がない操作 (削除/切り詰めなど) の場合は、`preCopyScript` プロパティを使用します。 | いいえ |
| storedProcedureParameters |ストアド プロシージャのパラメーター。<br/>使用可能な値は、名前と値のペアです。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 | いいえ |
| sqlWriterTableType | ストアド プロシージャで使用するテーブル型の名前を指定します。 コピー アクティビティでは、このテーブル型の一時テーブルでデータを移動できます。 その後、ストアド プロシージャのコードにより、コピーされたデータを既存のデータと結合できます。 | いいえ |

**例 1:データを追加する**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
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
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Azure SQL Database にデータを読み込む際のベスト プラクティス

Azure SQL Database にデータをコピーする場合は、さまざまな書き込み動作が必要になることがあります。

- [追加](#append-data):ソース データには新しいレコードのみが含まれている。
- [アップサート](#upsert-data):ソース データには挿入と更新の両方が含まれている。
- [上書き](#overwrite-the-entire-table):毎回ディメンション テーブル全体を再度読み込みたい。
- [カスタム ロジックでの書き込み](#write-data-with-custom-logic):宛先テーブルへの最終挿入の前に追加の処理が必要である。

Azure Data Factory で構成する方法およびベスト プラクティスについては、対応するセクションを参照してください。

### <a name="append-data"></a>データを追加する

データの追加は、この Azure SQL Database シンク コネクタの既定の動作です。 Azure Data Factory は、テーブルに効率的に書き込むために一括挿入を実行します。 コピー アクティビティで、それに応じてソースとシンクを構成できます。

### <a name="upsert-data"></a>データをアップサートする

**オプション 1:** コピーするデータが大量に存在する場合は、次のアプローチを使用してアップサートを実行します。 

- 最初に、[データベース スコープ一時テーブル](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database)を使用して、コピー アクティビティですべてのレコードを一括で読み込みます。 データベース スコープ一時テーブルに対する操作はログに記録されないため、数百万のレコードを数秒で読み込むことができます。
- Azure Data Factory でストアド プロシージャ アクティビティを実行して [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) または INSERT/UPDATE ステートメントを適用します。 一時テーブルをソースとして使用して、すべての更新または挿入を 1 つのトランザクションとして実行します。 この方法により、ラウンド トリップやログ操作の数が削減されます。 ストアド プロシージャ アクティビティの最後に、次のアップサート サイクルの準備のために一時テーブルを切り捨てることができます。

例として、Azure Data Factory で、**コピー アクティビティ**と**ストアド プロシージャ アクティビティ**を連結させたパイプラインを作成できます。 前者は、ソース ストアから Azure SQL Database 一時テーブル (たとえば、データセット内のテーブル名 **##UpsertTempTable**) にデータをコピーします。 次に、後者がストアド プロシージャを呼び出して、一時テーブルのソース データをターゲット テーブルにマージし、一時テーブルをクリーンアップします。

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

データベースで、前のストアド プロシージャ アクティビティから指し示されている、次の例に示すような MERGE ロジックを含むストアド プロシージャを定義します。 ターゲットは **Marketing** テーブルであり、そこには 3 つの列(**ProfileID**、**State**、**Category**) があるものとします。 **ProfileID** 列に基づいて、アップサートを実行します。

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

- データベース スコープ一時テーブルに読み込んでから、ストアド プロシージャを呼び出す。 
- コピー中にストアド プロシージャを呼び出す。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> SQL シンクからのストアド プロシージャの呼び出し

データの Azure SQL Database へのコピー時に、ユーザーが指定したストアド プロシージャを構成し、追加のパラメーターと共に呼び出すこともできます。

> [!TIP]
> ストアド プロシージャを呼び出すと、一括操作を使用する (これは、大規模なコピーにはお勧めできません) 代わりに行ごとにデータが処理されます。 詳細については、「[Azure SQL Database にデータを読み込む際のベスト プラクティス](#best-practice-for-loading-data-into-azure-sql-database)」を参照してください。

組み込みのコピー メカニズムでは目的を達成できない場合は、ストアド プロシージャを使用できます。 1 つの例は、宛先テーブルへのソース データの最終挿入の前に追加の処理を適用する場合です。 その他の処理の例をいくつか挙げると、列のマージ、追加の値の検索、複数のテーブルへの挿入があります。

次の例では、Azure SQL Database 内のテーブルに upsert を行うストアド プロシージャを使用する方法を示します。 入力データとシンクの **Marketing** テーブルには、それぞれ 3 つの列(**ProfileID**、**State**、**Category**) があるものとします。 **ProfileID** 列に基づいてアップサートを行い、特定のカテゴリに対してのみ適用します。

**出力データセット:** "tableName" は、次のストアド プロシージャ スクリプトに示すように、ストアド プロシージャ内の同じテーブル型パラメーター名です。

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL
)
```

ストアド プロシージャ機能は [テーブル値パラメーター](https://msdn.microsoft.com/library/bb675163.aspx)を利用しています。

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

Mapping Data Flow の[ソース変換](data-flow-source.md)と[シンク変換](data-flow-sink.md)に関する記事で詳細を確認してください。

## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL Database のデータ型のマッピング

Azure SQL Database をコピー元またはコピー先としてデータがコピーされると、次の Azure SQL Database のデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| Azure SQL Database のデータ型 | Azure Data Factory の中間データ型 |
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
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> 10 進の中間型にマップされるデータ型の場合、現在 Azure Data Factory では最大 28 の有効桁数をサポートしています。 28 よりも大きな有効桁数のデータがある場合は、SQL クエリで文字列に変換することを検討してください。

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストアと形式](copy-activity-overview.md##supported-data-stores-and-formats)の記事を参照してください。
