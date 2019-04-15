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
ms.date: 03/13/2019
ms.author: jingwang
ms.openlocfilehash: e9efe96490ea1c9351d87b5b2477474ef68fbda9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57875239"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Azure Data Factory を使用した Azure SQL Database との間でのデータのコピー
> [!div class="op_single_selector" title1="Select the version of Data Factory service you use:"]
> * [Version 1](v1/data-factory-azure-sql-connector.md)
> * [現在のバージョン](connector-azure-sql-database.md)

この記事では、Azure Data Factory のコピー アクティビティを使って Azure SQL Database との間でデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

Azure SQL Database のデータを、サポートされているシンク データ ストアにコピーできます。 また、Azure SQL Database には、サポートされているソース データ ストアからデータをコピーすることができます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表を参照してください。

具体的には、この Azure SQL Database コネクタは以下の機能をサポートします。

- SQL 認証を使って、およびサービス プリンシパルまたは Azure リソースのマネージド ID で Azure Active Directory (Azure AD) アプリケーション トークン認証を使って、データをコピーする。
- ソースとして、SQL クエリまたはストアド プロシージャを使用してデータを取得する。
- シンクとして、宛先テーブルにデータを追記する、またはコピー中にカスタム ロジックを使用してストアド プロシージャを起動する。

Azure SQL Database での [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) は現在サポートされていません。 

> [!IMPORTANT]
> Azure Data Factory Integration Runtime を使ってデータをコピーする場合は、Azure サービスがサーバーにアクセスできるように [Azure SQL Server ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)を構成します。
> セルフホステッド IR を使用してデータをコピーする場合は、適切な IP 範囲を許可するように Azure SQL Server ファイアウォールを構成します。 この範囲には、Azure SQL Database への接続に使用されるコンピューターの IP アドレスが含まれています。

## <a name="get-started"></a>作業開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、Azure SQL Database コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure SQL Database のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティを **AzureSqlDatabase** に設定する必要があります。 | はい |
| connectionString | **connectionString** プロパティの Azure SQL データベース インスタンスに接続するために必要な情報を指定します。 <br/>Data Factory に安全に格納するには、このフィールドを SecureString として指定します。 パスワード/サービス プリンシパル キーを Azure Key Vault に格納して、それが SQL 認証の場合は接続文字列から `password` 構成をプルすることもできます。 詳しくは、表の下の JSON の例と、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」の記事をご覧ください。 | はい |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 | サービス プリンシパルで Azure AD 認証を使う場合は、はい。 |
| servicePrincipalKey | アプリケーションのキーを取得します。 このフィールドを **SecureString** としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | サービス プリンシパルで Azure AD 認証を使う場合は、はい。 |
| tenant | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure portal の右上隅にマウスを置くことで取得します。 | サービス プリンシパルで Azure AD 認証を使う場合は、はい。 |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 データ ストアがプライベート ネットワークにある場合、Azure Integration Runtime またはセルフホステッド IR を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | いいえ  |

さまざまな認証の種類の前提条件と JSON サンプルについては、以下のセクションをご覧ください。

- [SQL 認証](#sql-authentication)
- [Azure AD アプリケーション トークン認証: サービス プリンシパル](#service-principal-authentication)
- [Azure AD アプリケーション トークン認証: Azure リソースのマネージド ID](#managed-identity)

>[!TIP]
>エラー コード "UserErrorFailedToConnectToSqlServer" および "The session limit for the database is XXX and has been reached." (データベースのセッション制限 XXX に達しました) のようなメッセージのエラーが発生する場合は、`Pooling=false` を接続文字列に追加して、もう一度試してください。

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

**Azure Key Vault 内のパスワード:** 

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

1. Azure portal から **[Azure Active Directory アプリケーションを作成します](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)**。 アプリケーション名と、リンクされたサービスを定義する次の値を記録しておきます。

    - アプリケーション ID
    - アプリケーション キー
    - テナント ID

1. まだ行っていない場合は、Azure portal で Azure SQL Server の **[Azure Active Directory 管理者をプロビジョニングします](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**。 Azure AD 管理者は、Azure AD ユーザーまたは Azure AD グループでなければなりませんが、サービス プリンシパルにはなれません。 このステップは、次のステップで Azure AD ID を使ってサービス プリンシパルの包含データベース ユーザーを作成できるようにするために行われます。

1. サービス プリンシパルの**[包含データベース ユーザーを作成します](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**。 SSMS のようなツールと、少なくとも ALTER ANY USER アクセス許可を持つ Azure AD ID を使用して、データをコピーするデータベースに接続します。 次の T-SQL を実行します。 
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

1. SQL ユーザーや他のユーザーに対する通常の方法で、**サービス プリンシパルに必要なアクセス許可を付与**します。 次のコードを実行します。

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

1. Azure Data Factory で、**Azure SQL Database のリンクされたサービスを構成します**。


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

1. **Azure AD でグループを作成します。** マネージド ID をグループのメンバーにします。
    
   1. Azure portal でデータ ファクトリのマネージド ID を調べます。 データ ファクトリの **[プロパティ]** に移動します。 サービス ID をコピーします。
    
   1. [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) モジュールをインストールします。 `Connect-AzureAD` コマンドを使用してサインインします。 次のコマンドを実行し、グループを作成してマネージド ID をメンバーとして追加します。
      ```powershell
      $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
      Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory managed identity object ID>"
      ```
    
1. まだ行っていない場合は、Azure portal で Azure SQL Server の **[Azure Active Directory 管理者をプロビジョニングします](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**。 Azure AD 管理者には、Azure AD ユーザーまたは Azure AD グループを使用できます。 マネージド ID を含むグループに管理者ロールを付与する場合は、手順 3. と手順 4. をスキップします。 管理者には、データベースへのフル アクセスがあります。

1. Azure AD グループの**[包含データベース ユーザーを作成します](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**。 SSMS のようなツールと、少なくとも ALTER ANY USER アクセス許可を持つ Azure AD ID を使用して、データをコピーするデータベースに接続します。 次の T-SQL を実行します。 
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

1. SQL ユーザーや他のユーザーに対する通常の方法で、**Azure AD グループに必要なアクセス許可を付与します**。 たとえば、次のコードを実行します。

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

1. Azure Data Factory で、**Azure SQL Database のリンクされたサービスを構成します**。

**例:**

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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services)に関する記事をご覧ください。 このセクションでは、Azure SQL Database データセットでサポートされるプロパティの一覧を示します。

Azure SQL Database をコピー元またはコピー先としてデータをコピーするには、データセットの **type** プロパティを **AzureSqlTable** に設定します。 次のプロパティがサポートされています。

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
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Azure SQL Database のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-sql-database-as-the-source"></a>ソースとしての Azure SQL Database

Azure SQL Database からデータをコピーする場合は、コピー アクティビティのソースで **type** プロパティを **SqlSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティを **SqlSource** に設定する必要があります。 | はい |
| sqlReaderQuery | カスタム SQL クエリを使用してデータを読み取ります。 例: `select * from MyTable`. | いいえ  |
| sqlReaderStoredProcedureName | ソース テーブルからデータを読み取るストアド プロシージャの名前。 最後の SQL ステートメントはストアド プロシージャの SELECT ステートメントにする必要があります。 | いいえ  |
| storedProcedureParameters | ストアド プロシージャのパラメーター。<br/>使用可能な値は、名前または値のペアです。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 | いいえ  |

### <a name="points-to-note"></a>注意する点

- **SqlSource** に **sqlReaderQuery** が指定されている場合、コピー アクティビティでは、データを取得するために Azure SQL Database ソースに対してこのクエリを実行します。 または、ストアド プロシージャを指定できます。 ストアド プロシージャがパラメーターを受け取る場合は、**sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定します。
- **sqlReaderQuery** または **sqlReaderStoredProcedureName** を指定しない場合は、データセット JSON の **structure** セクションで定義されている列を使用して、クエリが作成されます。 `select column1, column2 from mytable` が Azure SQL Database に対して実行されます。 データセット定義に **structure** がない場合は、すべての列がテーブルから選択されます。

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

Azure SQL Database にデータをコピーする場合は、コピー アクティビティのシンクで **type** プロパティを **SqlSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの **type** プロパティは、**SqlSink** に設定する必要があります。 | はい |
| writeBatchSize | バッファー サイズが **writeBatchSize** に達したら、SQL テーブルにデータを挿入します。<br/> 使用可能な値は **integer** (行数) です。 | いいえ。 既定値は 10000 です。 |
| writeBatchTimeout | タイムアウトする前に一括挿入操作の完了を待つ時間です。<br/> 使用可能な値は **timespan** です。 例:"00:30:00" (30 分)。 | いいえ  |
| preCopyScript | コピー アクティビティがデータを Azure SQL Database に書き込む前に実行する SQL クエリを指定します。 これは、コピー実行ごとに 1 回だけ呼び出されます。 前に読み込まれたデータをクリーンアップするには、このプロパティを使います。 | いいえ  |
| sqlWriterStoredProcedureName | ターゲット テーブルにソース データを適用する方法を定義しているストアド プロシージャの名前です。 たとえば、独自のビジネス ロジックを使用してアップサートまたは変換するような場合です。 <br/><br/>このストアド プロシージャは**バッチごとに呼び出されます**。 1 回だけ実行され、ソース データと関係のない操作の場合は、`preCopyScript` プロパティを使います。 たとえば、削除や切り詰めなどの操作です。 | いいえ  |
| storedProcedureParameters |ストアド プロシージャのパラメーター。<br/>使用可能な値は、名前と値のペアです。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 | いいえ  |
| sqlWriterTableType | ストアド プロシージャで使用するテーブル型の名前を指定します。 コピー アクティビティでは、このテーブル型の一時テーブルでデータを移動できます。 その後、ストアド プロシージャのコードにより、コピーされたデータを既存のデータと結合できます。 | いいえ  |

> [!TIP]
> Azure SQL Database にデータをコピーすると、既定では、コピー アクティビティによりデータがシンク テーブルに付加されます。 アップサートまたは追加のビジネス ロジックを行うには、**SqlSink** でストアド プロシージャを使用します。 詳しくは、「[SQL シンクからのストアド プロシージャの呼び出し](#invoking-stored-procedure-for-sql-sink)」をご覧ください。

#### <a name="append-data-example"></a>データ追加の例

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

#### <a name="invoke-a-stored-procedure-during-copy-for-upsert-example"></a>アップサートのコピー中にストアド プロシージャを呼び出す例

詳しくは、「[SQL シンクからのストアド プロシージャの呼び出し](#invoking-stored-procedure-for-sql-sink)」をご覧ください。

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

## <a name="identity-columns-in-the-target-database"></a>ターゲット データベースの ID 列

このセクションでは、ID 列がないソース テーブルから ID 列があるターゲット テーブルにデータをコピーする方法を示します。

#### <a name="source-table"></a>ソース テーブル

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

#### <a name="destination-table"></a>ターゲット テーブル

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

> [!NOTE]
> ターゲット テーブルには ID 列があります。

#### <a name="source-dataset-json-definition"></a>ソース データセット JSON の定義

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " AzureSqlTable",
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

#### <a name="destination-dataset-json-definition"></a>ターゲット データセット JSON の定義

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
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

> [!NOTE]
> ソース テーブルとターゲット テーブルのスキーマが異なります。 

ターゲットには ID 列が追加されています。 このシナリオでは、ターゲット データセット定義で **structure** プロパティを指定する必要があります。ここでは、ID 列は含みません。

## <a name="invoking-stored-procedure-for-sql-sink"></a> SQL シンクからのストアド プロシージャの呼び出し

データの Azure SQL Database へのコピー時に、ユーザーが指定したストアド プロシージャを構成し、追加のパラメーターと共に呼び出すことができます。

組み込みのコピー メカニズムでは目的を達成できない場合は、ストアド プロシージャを使用できます。 通常、ストアド プロシージャは、アップサート、挿入と更新、またはターゲット テーブルへのソース データの最終挿入の前に追加処理を行う必要があるときに、使用されます。 追加処理の例としては、列のマージ、追加の値の検索、複数のテーブルへの挿入などがあります。

次の例では、Azure SQL Database 内のテーブルに upsert を行うストアド プロシージャを使用する方法を示します。 入力データと、シンクの **Marketing** テーブルのそれぞれに 3 つの列 (**ProfileID**、**State**、**Category**) があるものとします。 **ProfileID** 列に基づいてアップサートを行い、特定のカテゴリに対してのみ適用します。

#### <a name="output-dataset"></a>出力データセット

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

コピー アクティビティの **SqlSink** セクションを定義します。

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

データベースで、**sqlWriterTableType** と同じ名前のテーブル型を定義します。 テーブル型のスキーマは、入力データから返されるスキーマと同じにする必要があります。

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL
)
```

ストアド プロシージャ機能は [テーブル値パラメーター](https://msdn.microsoft.com/library/bb675163.aspx)を利用しています。

## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL Database のデータ型のマッピング

Azure SQL Database をコピー元またはコピー先としてデータをコピーするとき、次の Azure SQL Database のデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| Azure SQL Database のデータ型 | Data Factory の中間データ型 |
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
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String、Char[] |
| xml |xml |

>[!NOTE]
> 10 進の中間型にマップされるデータ型の場合、現在 ADF では最大 28 の有効桁数をサポートしています。 28 よりも大きな有効桁数のデータがある場合は、SQL クエリで文字列に変換することを検討してください。

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストアと形式](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。
