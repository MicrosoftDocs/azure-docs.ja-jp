---
title: Azure SQL Database のデータをコピーし変換する
description: Azure SQL Database との間でデータをコピーする方法、および Azure Data Factory を使用して Azure SQL Database のデータを変換する方法について説明します。
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 01f43ceab36b519f3aafbbdc711df15c80481398
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597438"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Azure Data Factory を使用して Azure SQL Database のデータをコピーおよび変換する

> [!div class="op_single_selector" title1="使用している Azure Data Factory のバージョンを選択してください:"]
>
> - [Version 1](v1/data-factory-azure-sql-connector.md)
> - [現在のバージョン](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure SQL Database との間でデータをコピーする方法、および Data Flow を使用して Azure SQL Database のデータを変換する方法について説明します。 Azure Data Factory については、[入門記事で](introduction.md)をご覧ください。

## <a name="supported-capabilities"></a>サポートされる機能

この Azure SQL Database コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)表での[コピー アクティビティ](copy-activity-overview.md)
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)

コピー アクティビティについて、この Azure SQL Database コネクタは以下の機能をサポートします。

- SQL 認証を使って、およびサービス プリンシパルまたは Azure リソースのマネージド ID で Azure Active Directory (Azure AD) アプリケーション トークン認証を使って、データをコピーする。
- ソースとして、SQL クエリまたはストアド プロシージャを使用してデータを取得する。 Azure SQL Database ソースからの並列コピーを選択することもできます。詳細については、「[SQL Database からの並列コピー](#parallel-copy-from-sql-database)」セクションを参照してください。
- シンクとして、ソース スキーマに基づいて、宛先テーブルが存在しない場合はこれを自動的に作成する。テーブルにデータを追加するか、コピー中にカスタム ロジックを使用してストアド プロシージャを呼び出す。

Azure SQL Database の[サーバーレス レベル](../azure-sql/database/serverless-tier-overview.md)を使用している場合、サーバーが一時停止すると、自動再開の準備が整うまで待機せず、アクティビティの実行が失敗することに注意してください。 実際の実行時に確実にサーバーを稼働した状態にするには、アクティビティの再試行を追加するか、追加のアクティビティを連鎖させます。

>[!NOTE]
> Azure SQL Database の [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) は現在、このコネクタではサポートされていません。 回避するには、セルフホステッド統合ランタイム経由で[汎用 ODBC コネクタ](connector-odbc.md)と SQL Server ODBC ドライバーを使用できます。 詳細については、「[Always Encrypted の使用](#using-always-encrypted)」セクションを参照してください。 

> [!IMPORTANT]
> Azure Integration Runtime を使用してデータをコピーする場合は、Azure サービスからサーバーにアクセスできるように、[サーバーレベルのファイアウォール規則](../azure-sql/database/firewall-configure.md)を構成します。
> セルフホステッド統合ランタイムを使用してデータをコピーする場合は、適切な IP 範囲を許可するようにファイアウォールを構成します。 この範囲には、Azure SQL Database への接続に使用されるコンピューターの IP アドレスが含まれています。

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、Azure SQL Database コネクタに固有の Azure Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure SQL Database のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティを **AzureSqlDatabase** に設定する必要があります。 | はい |
| connectionString | **connectionString** プロパティの Azure SQL Database インスタンスに接続するために必要な情報を指定します。 <br/>Azure Key Vault にパスワードまたはサービス プリンシパル キーを設定することもできます。 それが SQL 認証である場合は、接続文字列から `password` 構成を取得します。 詳細については、この表の後にある JSON の例および「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」を参照してください。 | はい |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 | サービス プリンシパルで Azure AD 認証を使う場合は、はい。 |
| servicePrincipalKey | アプリケーションのキーを取得します。 このフィールドを **SecureString** としてマークして Azure Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | サービス プリンシパルで Azure AD 認証を使う場合は、はい。 |
| tenant | ドメイン名やテナント ID など、アプリケーションが存在するテナントの情報を指定します。 これは、Azure portal の右上隅をマウスでポイントすることで取得できます。 | サービス プリンシパルで Azure AD 認証を使う場合は、はい。 |
| azureCloudType | サービス プリンシパル認証用に、ご利用の Azure AD アプリケーションが登録されている Azure クラウド環境の種類を指定します。 <br/> 指定できる値は、**AzurePublic**、**AzureChina**、**AzureUsGovernment**、および **AzureGermany** です。 既定では、データ ファクトリのクラウド環境が使用されます。 | いいえ |
| connectVia | この[統合ランタイム](concepts-integration-runtime.md)は、データ ストアに接続するために使用されます。 データ ストアがプライベート ネットワークにある場合、Azure 統合ランタイムまたはセルフホステッド統合ランタイムを使用できます。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 | いいえ |

さまざまな認証の種類の前提条件と JSON サンプルについては、以下のセクションをご覧ください。

- [SQL 認証](#sql-authentication)
- [Azure AD アプリケーション トークン認証: サービス プリンシパル](#service-principal-authentication)
- [Azure AD アプリケーション トークン認証: Azure リソースのマネージド ID](#managed-identity)

>[!TIP]
>エラー コード "UserErrorFailedToConnectToSqlServer" および "The session limit for the database is XXX and has been reached" (データベースのセッション制限 XXX に達しました) のようなメッセージのエラーが発生する場合は、`Pooling=false` を接続文字列に追加して、もう一度試してください。

### <a name="sql-authentication"></a>SQL 認証

**例: SQL 認証の使用**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例: Azure Key Vault 内のパスワード**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": {
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                },
                "secretName": "<secretName>"
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

1. Azure portal から [Azure Active Directory アプリケーションを作成します](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)。 アプリケーション名と、リンクされたサービスを定義する次の値を記録しておきます。

    - アプリケーション ID
    - アプリケーション キー
    - テナント ID

2. まだ行っていない場合は、Azure portal でサーバーの [Azure Active Directory 管理者をプロビジョニング](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)します。 Azure AD 管理者は、Azure AD ユーザーまたは Azure AD グループでなければなりませんが、サービス プリンシパルにはなれません。 このステップは、次のステップで Azure AD ID を使ってサービス プリンシパルの包含データベース ユーザーを作成できるようにするために行われます。

3. サービス プリンシパルの[包含データベース ユーザーを作成します](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)。 SQL Server Management Studio のようなツールと、少なくとも ALTER ANY USER アクセス許可を持つ Azure AD ID を使用して、データをコピーするデータベースに接続します。 次の T-SQL を実行します。
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. SQL ユーザーや他のユーザーに対する通常の方法で、サービス プリンシパルに必要なアクセス許可を付与します。 次のコードを実行します。 詳細については、[こちらのドキュメント](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)を参照してください。

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Azure Data Factory で、Azure SQL Database のリンクされたサービスを構成します。

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>サービス プリンシパル認証を使うリンクされたサービスの例

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30",
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Azure リソースのマネージド ID 認証

データ ファクトリは、特定のデータ ファクトリを表す [Azure リソースのマネージド ID](data-factory-service-identity.md) に関連付けることができます。 このマネージド ID を Azure SQL Database の認証に使用できます。 この ID を使用して指定したファクトリからデータベースにアクセスし、データベースに、またはデータベースからデータをコピーできます。

マネージド ID 認証を使用するには、次の手順に従います。

1. まだ行っていない場合は、Azure portal でサーバーの [Azure Active Directory 管理者をプロビジョニング](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)します。 Azure AD 管理者には、Azure AD ユーザーまたは Azure AD グループを使用できます。 マネージド ID を持つグループに管理者ロールを付与する場合は、ステップ 3 と 4 をスキップします。 管理者はデータベースに対してフル アクセス権を持っています。

2. Azure Data Factory のマネージド ID 用に[包含データベース ユーザーを作成](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)します。 SQL Server Management Studio のようなツールと、少なくとも ALTER ANY USER アクセス許可を持つ Azure AD ID を使用して、データをコピーするデータベースに接続します。 次の T-SQL を実行します。
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. SQL ユーザーや他のユーザーに対する通常の方法と同様に、Data Factory のマネージド ID に必要なアクセス許可を付与します。 次のコードを実行します。 詳細については、[こちらのドキュメント](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)を参照してください。

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Azure Data Factory で、Azure SQL Database のリンクされたサービスを構成します。

**例**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットの定義に使用できるセクションとプロパティの詳細な一覧については、[データセット](./concepts-datasets-linked-services.md)に関するページを参照してください。

Azure SQL Database データセットでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの **type** プロパティは、**AzureSqlTable** に設定する必要があります。 | はい |
| schema | スキーマの名前。 |ソースの場合はいいえ、シンクの場合ははい  |
| table | テーブル/ビューの名前。 |ソースの場合はいいえ、シンクの場合ははい  |
| tableName | スキーマがあるテーブル/ビューの名前。 このプロパティは下位互換性のためにサポートされています。 新しいワークロードでは、`schema` と `table` を使用します。 | ソースの場合はいいえ、シンクの場合ははい |

### <a name="dataset-properties-example"></a>データセットのプロパティの例

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
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関するページを参照してください。 このセクションでは、Azure SQL Database のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-sql-database-as-the-source"></a>ソースとしての Azure SQL Database

>[!TIP]
>データ パーティション分割を使用して Azure SQL Database からデータを効率的に読み込むには、「[SQL Database からの並列コピー](#parallel-copy-from-sql-database)」を参照してください。

Azure SQL Database からデータをコピーするために、コピー アクティビティの **source** セクションでは次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティの source の **type** プロパティは **AzureSqlSource** に設定する必要があります。 "SqlSource" タイプは、現在も下位互換性のためにサポートされています。 | はい |
| sqlReaderQuery | このプロパティは、カスタム SQL クエリを使用してデータを読み取ります。 たとえば `select * from MyTable` です。 | いいえ |
| sqlReaderStoredProcedureName | ソース テーブルからデータを読み取るストアド プロシージャの名前。 最後の SQL ステートメントはストアド プロシージャの SELECT ステートメントにする必要があります。 | いいえ |
| storedProcedureParameters | ストアド プロシージャのパラメーター。<br/>使用可能な値は、名前または値のペアです。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 | いいえ |
| isolationLevel | SQL ソースのトランザクション ロック動作を指定します。 使用できる値は、次のとおりです。**ReadCommitted**、**ReadUncommitted**、**RepeatableRead**、**Serializable**、**Snapshot**。 指定しなかった場合は、データベースの既定の分離レベルが使用されます。 詳細については[こちらのドキュメント](/dotnet/api/system.data.isolationlevel)をご覧ください。 | いいえ |
| partitionOptions | Azure SQL Database からのデータの読み込みに使用されるデータ パーティション分割オプションを指定します。 <br>使用できる値は、以下のとおりです。**None** (既定値)、**PhysicalPartitionsOfTable**、および **DynamicRange**。<br>パーティション オプションが有効になっている場合 (つまり、`None` ではない場合)、Azure SQL Database から同時にデータを読み込む並列処理の次数は、コピー アクティビティの [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) の設定によって制御されます。 | いいえ |
| partitionSettings | データ パーティション分割の設定のグループを指定します。 <br>パーティション オプションが `None` でない場合に適用されます。 | いいえ |
| ***`partitionSettings` の下:*** | | |
| partitionColumnName | 並列コピーの範囲パーティション分割で使用される **整数型または日付/日時型** (`int`、`smallint`、`bigint`、`date`、`smalldatetime`、`datetime`、`datetime2`、または `datetimeoffset`) のソース列の名前を指定します。 指定しない場合、テーブルのインデックスまたは主キーが自動検出され、パーティション列として使用されます。<br>パーティション オプションが `DynamicRange` である場合に適用されます。 クエリを使用してソース データを取得する場合は、WHERE 句で `?AdfDynamicRangePartitionCondition ` をフックします。 例については、「[SQL データベースからの並列コピー](#parallel-copy-from-sql-database)」セクションを参照してください。 | いいえ |
| partitionUpperBound | パーティション範囲の分割のための、パーティション列の最大値。 この値は、テーブル内の行のフィルター処理用ではなく、パーティションのストライドを決定するために使用されます。 テーブルまたはクエリ結果に含まれるすべての行がパーティション分割され、コピーされます。 指定しない場合、コピー アクティビティによって値が自動検出されます。  <br>パーティション オプションが `DynamicRange` である場合に適用されます。 例については、「[SQL データベースからの並列コピー](#parallel-copy-from-sql-database)」セクションを参照してください。 | いいえ |
| partitionLowerBound | パーティション範囲の分割のための、パーティション列の最小値。 この値は、テーブル内の行のフィルター処理用ではなく、パーティションのストライドを決定するために使用されます。 テーブルまたはクエリ結果に含まれるすべての行がパーティション分割され、コピーされます。 指定しない場合、コピー アクティビティによって値が自動検出されます。<br>パーティション オプションが `DynamicRange` である場合に適用されます。 例については、「[SQL データベースからの並列コピー](#parallel-copy-from-sql-database)」セクションを参照してください。 | いいえ |

**以下の点に注意してください。**

- **AzureSqlSource** に **sqlReaderQuery** が指定されている場合、コピー アクティビティでは、データを取得するために Azure SQL Database ソースに対してこのクエリを実行します。 **sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することもできます (ストアド プロシージャでパラメーターを使用する場合)。
- ソースのストアド プロシージャを使用してデータを取得する場合、異なるパラメーター値が渡されたときにストアド プロシージャが別のスキーマを返すように設計されていると、UI からスキーマをインポートするとき、または自動テーブル作成を使用して SQL データベースにデータをコピーするときに、エラーが発生したり、予期しない結果が表示されます。

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
                "type": "AzureSqlSource",
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
                "type": "AzureSqlSource",
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

データを Azure SQL Database にコピーするために、コピー アクティビティの **sink** セクションでは次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティの sink の **type** プロパティは **AzureSqlSink** に設定する必要があります。 "SqlSink" タイプは、現在も下位互換性のためにサポートされています。 | はい |
| preCopyScript | コピー アクティビティがデータを Azure SQL Database に書き込む前に実行する SQL クエリを指定します。 これは、コピー実行ごとに 1 回だけ呼び出されます。 前に読み込まれたデータをクリーンアップするには、このプロパティを使います。 | いいえ |
| tableOption | ソースのスキーマに基づいて[自動的にシンク テーブルを作成する](copy-activity-overview.md#auto-create-sink-tables)かどうかを指定します (存在しない場合)。 <br>シンクでストアド プロシージャが指定されている場合、テーブルの自動作成はサポートされません。 <br>使用できる値は `none` (既定値)、`autoCreate` です。 | いいえ |
| sqlWriterStoredProcedureName | ターゲット テーブルにソース データを適用する方法を定義しているストアド プロシージャの名前です。 <br/>このストアド プロシージャは *バッチごとに呼び出されます*。 1 回だけ実行され、ソース データとは関係がない操作 (削除/切り詰めなど) の場合は、`preCopyScript` プロパティを使用します。<br>例については、「[SQL シンクからのストアド プロシージャの呼び出し](#invoke-a-stored-procedure-from-a-sql-sink)」を参照してください。 | いいえ |
| storedProcedureTableTypeParameterName |ストアド プロシージャで指定されたテーブル型のパラメーター名。  |いいえ |
| sqlWriterTableType |ストアド プロシージャで使用するテーブル型の名前。 コピー アクティビティでは、このテーブル型の一時テーブルでデータを移動できます。 その後、ストアド プロシージャのコードにより、コピーされたデータを既存のデータと結合できます。 |いいえ |
| storedProcedureParameters |ストアド プロシージャのパラメーター。<br/>使用可能な値は、名前と値のペアです。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 | いいえ |
| writeBatchSize | SQL テーブルに挿入する "*バッチあたりの*" 行数。<br/> 使用可能な値は **integer** (行数) です。 既定では、Azure Data Factory により行のサイズに基づいて適切なバッチ サイズが動的に決定されます。 | いいえ |
| writeBatchTimeout | タイムアウトする前に一括挿入操作の完了を待つ時間です。<br/> 使用可能な値は **timespan** です。 たとえば "00:30:00" (30 分) を指定できます。 | いいえ |
| disableMetricsCollection | Data Factory では、コピーのパフォーマンスの最適化とレコメンデーションのために、Azure SQL Database DTU などのメトリックが収集されます。これにより、マスター DB への追加アクセスが発生します。 この動作に不安がある場合は、`true` を指定してオフにします。 | いいえ (既定値は `false`) |
| maxConcurrentConnections |アクティビティの実行中にデータ ストアに対して確立されたコンカレント接続数の上限。 コンカレント接続を制限する場合にのみ、値を指定します。| いいえ |

**例 1: データを追加する**

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
                "type": "AzureSqlSink",
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
                "type": "AzureSqlSink",
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

Azure SQL Database コネクタでは、コピー アクティビティの際に、データを並列でコピーするための組み込みのデータ パーティション分割が提供されます。 データ パーティション分割オプションは、コピー アクティビティの **[ソース]** タブにあります。

![パーティションのオプションのスクリーンショット](./media/connector-sql-server/connector-sql-partition-options.png)

パーティション分割されるコピーを有効にすると、コピー アクティビティによって Azure SQL Database ソースに対する並列クエリが実行され、パーティションごとにデータが読み込まれます。 並列度は、コピー アクティビティの [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 設定によって制御されます。 たとえば、`parallelCopies` を 4 に設定した場合、Data Factory では、指定したパーティション オプションと設定に基づいて 4 つのクエリが同時に生成され、実行されます。そして各クエリが、Azure SQL Database からデータの一部を取得します。

Azure SQL Database から大量のデータを読み込む場合は特に、データ パーティション分割を行う並列コピーを有効にすることが推奨されます。 さまざまなシナリオの推奨構成を以下に示します。 ファイルベースのデータ ストアにデータをコピーする場合は、複数のファイルとしてフォルダーに書き込む (フォルダー名のみを指定する) ことをお勧めします。この場合、1 つのファイルに書き込むよりもパフォーマンスが優れています。

| シナリオ                                                     | 推奨設定                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 物理パーティションに分割された大きなテーブル全体から読み込む。        | **パーティション オプション**: テーブルの物理パーティション。 <br><br/>実行中に、Data Factory によって物理パーティションが自動的に検出され、パーティションごとにデータがコピーされます。 <br><br/>テーブルに物理パーティションがあるかどうかを確認するには、[こちらのクエリ](#sample-query-to-check-physical-partition)を参照してください。 |
| 物理パーティションがなく、データ パーティション分割用の整数または日時の列がある大きなテーブル全体から読み込む。 | **パーティション オプション**: 動的範囲パーティション。<br>**パーティション列** (省略可能):データのパーティション分割に使用される列を指定します。 指定されていない場合は、インデックスまたは主キー列が使用されます。<br/>**パーティションの上限** と **パーティションの下限** (省略可能):パーティションのストライドを決定する場合に指定します。 これは、テーブル内の行のフィルター処理用ではなく、テーブル内のすべての行がパーティション分割されてコピーされます。 指定されていない場合は、コピー アクティビティによって値が自動検出されます。<br><br>たとえば、パーティション列 "ID" の値の範囲が 1 ～ 100 で、下限を 20 に、上限を 80 に設定し、並列コピーを 4 にした場合、Data Factory によって 4 つのパーティションでデータが取得されます。ID の範囲は、それぞれ、20 以下、21 ～ 50、51 ～ 80、81 以上となります。 |
| 物理パーティションがなく、データ パーティション分割用の整数列または日付/日時列がある大量のデータを、カスタム クエリを使用して読み込む。 | **パーティション オプション**: 動的範囲パーティション。<br>**クエリ**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br>**パーティション列**: データのパーティション分割に使用される列を指定します。<br>**パーティションの上限** と **パーティションの下限** (省略可能):パーティションのストライドを決定する場合に指定します。 これは、テーブル内の行のフィルター処理用ではなく、クエリ結果のすべての行がパーティション分割されてコピーされます。 指定されない場合、コピー アクティビティによって値が自動検出されます。<br><br>実行中に、Data Factory によって `?AdfRangePartitionColumnName` が各パーティションの実際の列名および値の範囲に置き換えられ、Azure SQL Database に送信されます。 <br>たとえば、パーティション列 "ID" の値の範囲が 1 ～ 100 で、下限を 20 に、上限を 80 に設定し、並列コピーを 4 にした場合、Data Factory によって 4 つのパーティションでデータが取得されます。各パーティションの ID の範囲は、それぞれ、20 以下、21 ～ 50、51 ～ 80、81 以上となります。 <br><br>さまざまなシナリオのサンプル クエリを次に示します。<br> 1.テーブル全体に対してクエリを実行する: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2.列の選択と追加の where 句フィルターが含まれるテーブルからのクエリ: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3.サブクエリを使用したクエリ: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4.サブクエリにパーティションがあるクエリ: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

パーティション オプションを使用してデータを読み込む場合のベスト プラクティス:

1. データ スキューを回避するため、パーティション列 (主キーや一意キーなど) には特徴のある列を選択します。 
2. テーブルに組み込みパーティションがある場合は、パフォーマンスを向上させるためにパーティション オプションとして "テーブルの物理パーティション" を使用します。    
3. Azure Integration Runtime を使用してデータをコピーする場合は、より大きな (4 より大きい) "[データ統合単位 (DIU)](copy-activity-performance-features.md#data-integration-units)" を設定すると、より多くのコンピューティング リソースを利用できます。 そこで、該当するシナリオを確認してください。
4. パーティション数は、"[コピーの並列処理の次数](copy-activity-performance-features.md#parallel-copy)" によって制御されます。この数値を大きくしすぎるとパフォーマンスが低下するため、この数値は、(DIU またはセルフホステッド IR ノードの数) x (2 から 4) に設定することをお勧めします。

**例: 複数の物理パーティションがある大きなテーブル全体から読み込む**

```json
"source": {
    "type": "AzureSqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**例: 動的範囲パーティションを使用してクエリを実行する**

```json
"source": {
    "type": "AzureSqlSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>物理パーティションを確認するためのサンプル クエリ

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

テーブルに物理パーティションがある場合、次のように、"HasPartition" は "yes" と表示されます。

![SQL クエリの結果](./media/connector-azure-sql-database/sql-query-result.png)

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Azure SQL Database にデータを読み込む際のベスト プラクティス

Azure SQL Database にデータをコピーする場合は、さまざまな書き込み動作が必要になることがあります。

- [追加](#append-data): ソース データには新しいレコードのみが含まれている。
- [アップサート](#upsert-data): ソース データには挿入と更新の両方が含まれている。
- [上書き](#overwrite-the-entire-table): 毎回ディメンション テーブル全体を再度読み込みたい。
- [カスタム ロジックでの書き込み](#write-data-with-custom-logic): 宛先テーブルへの最終挿入の前に追加の処理が必要である。

Azure Data Factory で構成する方法およびベスト プラクティスについては、対応するセクションを参照してください。

### <a name="append-data"></a>データを追加する

データの追加は、この Azure SQL Database シンク コネクタの既定の動作です。 Azure Data Factory では、テーブルに効率的に書き込むために一括挿入が実行されます。 コピー アクティビティで、それに応じてソースとシンクを構成できます。

### <a name="upsert-data"></a>データをアップサートする

**オプション 1:** 大量のデータをコピーする場合は、コピー アクティビティを使用してすべてのレコードをステージング テーブルに一括読み込みしてから、ストアド プロシージャ アクティビティを実行して、[MERGE](/sql/t-sql/statements/merge-transact-sql) または INSERT/UPDATE ステートメントを 1 回で適用します。 

コピー アクティビティでは現在、データベース一時テーブルへのデータの読み込みはネイティブでサポートされていません。 複数のアクティビティを組み合わせて設定するための高度な方法があります。「[Azure SQL Database の一括 upsert シナリオを最適化する](https://github.com/scoriani/azuresqlbulkupsert)」を参照してください。 以下に、永続的テーブルをステージングとして使用する例を示します。

例として、Azure Data Factory で、**コピー アクティビティ** と **ストアド プロシージャ アクティビティ** を連結させたパイプラインを作成できます。 前者では、ソース ストアから Azure SQL Database ステージング テーブル (たとえば、データセット内のテーブル名 **UpsertStagingTable**) にデータをコピーします。 その後、後者でストアド プロシージャが呼び出され、ステージング テーブルのソース データがターゲット テーブルにマージされて、ステージング テーブルがクリーンアップされます。

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

**オプション 3:** 組み込みの insert、upsert、update メソッドを提供する、[マッピング データ フロー](#sink-transformation)を使用できます。

### <a name="overwrite-the-entire-table"></a>テーブル全体を上書きする

コピー アクティビティ シンクで **preCopyScript** プロパティを構成できます。 この場合、実行されるコピー アクティビティごとに、Azure Data Factory で最初にスクリプトが実行されます。 次に、コピーが実行されてデータが挿入されます。 たとえば、テーブル全体を最新のデータで上書きするには、ソースから新しいデータを一括で読み込む前に、すべてのレコードを最初に削除するスクリプトを指定します。

### <a name="write-data-with-custom-logic"></a>カスタム ロジックでデータを書き込む

カスタム ロジックでデータを書き込む手順は、「[データをアップサートする](#upsert-data)」セクションで説明されている手順に似ています。 宛先テーブルへのソース データの最終挿入の前に追加の処理を適用する必要がある場合は、ステージング テーブルに読み込んだ後、ストアド プロシージャ アクティビティを呼び出すか、コピー アクティビティのシンクのストアド プロシージャを呼び出してデータを適用するか、マッピング データ フローを使用できます。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> SQL シンクからのストアド プロシージャの呼び出し

Azure SQL Database にデータをコピーするときに、ユーザーが指定したストアド プロシージャを構成し、ソース テーブルの各バッチに関する追加のパラメーターと共に呼び出すこともできます。 ストアド プロシージャ機能は [テーブル値パラメーター](/dotnet/framework/data/adonet/sql/table-valued-parameters)を利用しています。

組み込みのコピー メカニズムでは目的を達成できない場合は、ストアド プロシージャを使用できます。 1 つの例は、宛先テーブルへのソース データの最終挿入の前に追加の処理を適用する場合です。 その他の処理の例をいくつか挙げると、列のマージ、追加の値の検索、複数のテーブルへの挿入があります。

次の例では、Azure SQL Database 内のテーブルに upsert を行うストアド プロシージャを使用する方法を示します。 入力データとシンクの **Marketing** テーブルには、それぞれ 3 つの列 (**ProfileID**、**State**、**Category**) があるものとします。 **ProfileID** 列に基づいてアップサートを行い、"ProductA" という特定のカテゴリに対してのみ適用します。

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
        "type": "AzureSqlSink",
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

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

マッピング データ フローでデータを変換する場合、Azure SQL Database からテーブルの読み取りと書き込みを実行できます。 詳細については、マッピング データ フローの[ソース変換](data-flow-source.md)と[シンク変換](data-flow-sink.md)に関する記事をご覧ください。

### <a name="source-transformation"></a>ソース変換

Azure SQL Database に固有の設定は、ソース変換の **[Source Options]\(ソース オプション\)** タブにあります。

**[Input]:** テーブルにあるソースを指す (```Select * from <table-name>``` に相当) かカスタム SQL クエリを入力するかを選択します。

**Query**: [Input] フィールドで [Query] を選択した場合は、ソースに対する SQL クエリを入力します。 この設定により、データセットで選択したすべてのテーブルがオーバーライドされます。 ここでは **Order By** 句はサポートされていませんが、完全な SELECT FROM ステートメントを設定することができます。 ユーザー定義のテーブル関数を使用することもできます。 **select * from udfGetData()** は、テーブルを返す SQL の UDF です。 このクエリでは、お使いのデータ フローで使用できるソース テーブルが生成されます。 テスト対象またはルックアップ対象の行を減らすうえでも、クエリの使用は有効な手段です。

**[Stored procedure]** : ソース データベースから実行されるストアド プロシージャからプロジェクションおよびソース データを生成する場合は、このオプションを選択します。 スキーマ、プロシージャ名、およびパラメーターを入力するか、[Refresh] をクリックして、ADF にスキーマとプロシージャ名を検出するように依頼できます。 次に、[Import ] をクリックして、すべてのプロシージャ パラメーターをフォーム ``@paraName`` を使用してインポートできます。

![ストアド プロシージャ](media/data-flow/stored-procedure-2.png "ストアド プロシージャ")

- SQL の例: ```Select * from MyTable where customerId > 1000 and customerId < 2000```
- パラメーター化された SQL の例: ``"select * from {$tablename} where orderyear > {$year}"``

**Batch size**: 大量データを読み取りにまとめるバッチ サイズを入力します。

**Isolation Level**: マッピング データ フローでの SQL ソースの既定値は [コミットされていないものを読み取り] です。 ここで分離レベルを次のいずれかの値に変更できます。

- コミットされたものを読み取り
- コミットされていないものを読み取り
- 反復可能読み取り
- シリアル化可能
- なし (分離レベルを無視)

![Isolation Level](media/data-flow/isolationlevel.png "Isolation Level")

### <a name="sink-transformation"></a>シンク変換

Azure SQL Database に固有の設定は、シンク変換の **[設定]** タブにあります。

**[Update method]\(更新方法\)** :対象となるデータベースに対して許可される操作を指定します。 既定では、挿入のみが許可されます。 行を更新、アップサート、または削除するには、それらのアクションに対して行をタグ付けするために行の変更変換が必要になります。 更新、アップサート、削除の場合、1 つまたは複数のキー列を設定して、変更する行を決定する必要があります。

![[キー列]](media/data-flow/keycolumn.png "[キー列]")

ここでキーとして選択する列の名前は後続の更新、アップサート、削除で ADF によって使用されます。 そのため、シンク マッピングに存在する列を選択する必要があります。 このキー列に値を書き込まない場合、[Skip writing key columns]\(キー列の書き込みをスキップする\) をクリックします。

ここで使用するキー列は、ターゲットの Azure SQL Database テーブルを更新するためにパラメーター化できます。 複合キーに対して複数の列がある場合、[カスタム式] をクリックすると、ADF データ フロー式言語を使用して動的コンテンツを追加できます。これには、複合キーの列名を持つ文字列の配列を含めることができます。

**[Table action]\(テーブル アクション\):** 書き込み前に変換先テーブルのすべての行を再作成するか削除するかを指定します。

- [なし]:テーブルに対してアクションは実行されません。
- [再作成]:テーブルが削除され、再作成されます。 新しいテーブルを動的に作成する場合に必要です。
- [切り詰め]:ターゲット テーブルのすべての行が削除されます。

**Batch size**: 各バケットに書き込まれる行数を制御します。 バッチ サイズを大きくすると、圧縮とメモリの最適化が向上しますが、データをキャッシュする際にメモリ不足の例外が発生するリスクがあります。

**Use TempDB (TempDB を使用):** 既定では、Data Factory には、読み込みプロセスの一部としてデータを格納するために、グローバル一時テーブルが使用されます。 あるいは、[Use TempDB]\(TempDB を使用\) オプションをオフにし、代わりに、このシンクに使用されているデータベース内にあるユーザー データベースに、一時的に保持するテーブルを格納するように Data Factory に求めることもできます。

![Temp DB を使用](media/data-flow/tempdb.png "Temp DB を使用")

**[Pre and Post SQL scripts] (事前および事後 SQL スクリプト)** : データがシンク データベースに書き込まれる前 (前処理) と書き込まれた後 (後処理) に実行される複数行の SQL スクリプトを入力します。

![事前および事後 SQL 処理スクリプト](media/data-flow/prepost1.png "SQL 処理スクリプト")

### <a name="error-row-handling"></a>エラー行の処理

Azure SQL DB に書き込む場合、書き込み先で設定されている制約によって、データの特定の行が失敗することがあります。 一般的なエラーには次のようなものがあります。

*    テーブル内の文字列データまたはバイナリ データが切り捨てられる
*    列に値 NULL を挿入できない
*    INSERT ステートメントが CHECK 制約と競合している

既定では、データ フローの実行は最初に発生したエラーで失敗します。 **[エラーのまま続行する]** を選択すると、個々の行でエラーが発生した場合でもデータ フローを完了することができます。 Azure Data Factory には、これらのエラー行を処理するためのさまざまなオプションが用意されています。

**[Transaction Commit]\(トランザクション コミット\):** データを 1 つのトランザクションまたはバッチのどちらで書き込むかを選択します。 1 つのトランザクションの場合はパフォーマンスが低下しますが、トランザクションが完了するまで書き込みデータは他のユーザーに表示されません。  

**[Output rejected data]\(拒否されたデータの出力\):** 有効にすると、エラー行を Azure Blob Storage または選択した Azure Data Lake Storage Gen2 アカウントの csv ファイルに出力できます。 これにより、3 つの列 (INSERT または UPDATE などの SQL 操作、データ フロー エラー コード、および行のエラー メッセージ) を含むエラー行が書き込まれます。

**[Report success on error]\(エラー発生時に成功を報告\):** 有効にすると、エラー行が見つかった場合でもデータ フローは成功としてマークされます。 

![エラー行の処理](media/data-flow/sql-error-row-handling.png "エラー行の処理")


## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL Database のデータ型のマッピング

Azure SQL Database をコピー元またはコピー先としてデータがコピーされると、次の Azure SQL Database のデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| Azure SQL Database のデータ型 | Azure Data Factory の中間データ型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal (10 進数型) |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| INT |Int32 |
| money |Decimal (10 進数型) |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal (10 進数型) |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal (10 進数型) |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |String |

>[!NOTE]
> 10 進の中間型にマップされるデータ型の場合、コピー アクティビティでは、現在、最大 28 の有効桁数がサポートされています。 28 よりも大きな有効桁数のデータがある場合は、SQL クエリで文字列に変換することを検討してください。

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="getmetadata-activity-properties"></a>GetMetadata アクティビティのプロパティ

プロパティの詳細については、[GetMetadata アクティビティ](control-flow-get-metadata-activity.md)に関するページを参照してください。

## <a name="using-always-encrypted"></a>Always Encrypted の使用

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) を使用して Azure SQL Database との間でデータをコピーする場合は、セルフホステッド統合ランタイムを介して[汎用 ODBC コネクタ](connector-odbc.md)および SQL Server ODBC ドライバーを使用します。 この Azure SQL Database コネクタでは、現在、Always Encrypted はサポートされていません。 

具体的には次のとおりです。

1. セルフホステッド統合ランタイムを設定します (存在しない場合)。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。

2. SQL Server 用の 64 ビット ODBC ドライバーを[こちら](/sql/connect/odbc/download-odbc-driver-for-sql-server)からダウンロードし、Integration Runtime コンピューターにインストールします。 このドライバーがどのように機能するかについて詳しくは、「[SQL Server 用 ODBC ドライバーと共に Always Encrypted を使用する](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider)」を参照してください。

3. SQL データベースに接続するために、ODBC データ型を使用してリンクされたサービスを作成します。以下のサンプルを参照してください。

    - **SQL 認証** を使用するには: 以下のように ODBC 接続文字列を指定し、ユーザー名とパスワードを設定するための **基本** 認証を選択します。

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - Azure 仮想マシンでセルフホステッド統合ランタイムを実行する場合、Azure VM の ID で **マネージド ID の認証** を使用できます。

        1. 同じ[前提条件](#managed-identity)に従って、マネージド ID のデータベース ユーザーを作成し、データベースに適切なロールを付与します。
        2. リンクされたサービスで、次のように ODBC 接続文字列を指定し、**匿名** 認証を選択します。接続文字列自体では `Authentication=ActiveDirectoryMsi` と示されます。

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. これに応じて、データセットとコピー アクティビティを ODBC データ型で作成します。 詳細については [ODBC コネクタ](connector-odbc.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)の記事を参照してください。
