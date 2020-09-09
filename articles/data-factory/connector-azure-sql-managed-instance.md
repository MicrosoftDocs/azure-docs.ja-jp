---
title: Azure SQL Managed Instance との間でデータをコピーする
description: Azure Data Factory を使用して、Azure SQL Managed Instance との間でデータを移動する方法を学習します。
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 08/25/2020
ms.openlocfilehash: 2a861a31a36d30bfec9ad3bde9dc6e91ac067c8a
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816622"
---
# <a name="copy-data-to-and-from-azure-sql-managed-instance-by-using-azure-data-factory"></a>Azure Data Factory を使用して Azure SQL Managed Instance との間でデータをコピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure SQL Managed Instance との間でデータをコピーする方法の概要を示します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

この SQL Managed Instance コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)

SQL Managed Instance から、サポートされている任意のシンク データ ストアにデータをコピーすることができます。 サポートされている任意のソース データ ストアから、SQL Managed Instance にデータをコピーすることもできます。 コピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表を参照してください。

具体的には、この SQL Managed Instance コネクタでは以下がサポートされます。

- SQL 認証を使って、およびサービス プリンシパルまたは Azure リソースのマネージド ID で Azure Active Directory (Azure AD) アプリケーション トークン認証を使って、データをコピーする。
- ソースとして、SQL クエリまたはストアド プロシージャを使用してデータを取得する。 SQL MI ソースからの並列コピーを選択することもできます。詳細については、[SQL MI からの並列コピー](#parallel-copy-from-sql-mi)に関するセクションを参照してください。
- シンクとして、ソース スキーマに基づいて、宛先テーブルが存在しない場合はこれを自動的に作成する。テーブルにデータを追加するか、コピー中にカスタム ロジックを使用してストアド プロシージャを呼び出す。

>[!NOTE]
> SQL Managed Instance の [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) は現在、このコネクタではサポートされていません。 回避するには、セルフホステッド統合ランタイム経由で[汎用 ODBC コネクタ](connector-odbc.md)と SQL Server ODBC ドライバーを使用できます。 詳細については、「[Always Encrypted の使用](#using-always-encrypted)」セクションを参照してください。 

## <a name="prerequisites"></a>前提条件

SQL Managed Instance の[パブリック エンドポイント](../azure-sql/managed-instance/public-endpoint-overview.md)にアクセスするために、Azure Data Factory のマネージド Azure Integration Runtime を使用できます。 パブリック エンドポイントを有効にするだけでなく、ネットワーク セキュリティ グループでのパブリック エンドポイント トラフィックも許可して、Azure Data Factory が確実にデータベースに接続できるようにしてください。 詳細については、[このガイダンス](../azure-sql/managed-instance/public-endpoint-configure.md)を参照してください。

SQL Managed Instance のプライベート エンドポイントにアクセスするには、そのデータベースにアクセスできる[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)を設定します。 セルフホステッド統合ランタイムをマネージド インスタンスと同じ仮想ネットワーク内でプロビジョニングする場合は、統合ランタイム コンピューターがマネージド インスタンスとは別のサブネットにあることを確認してください。 セルフホステッド統合ランタイムをマネージド インスタンスとは別の仮想ネットワークにプロビジョニングする場合は、仮想ネットワーク ピアリングまたは仮想ネットワーク間接続のどちらかを使用できます。 詳細については、[SQL Managed Instance へのアプリケーションの接続](../azure-sql/managed-instance/connect-application-instance.md)に関するページを参照してください。

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以降のセクションでは、SQL Managed Instance コネクタに固有の Azure Data Factory エンティティを定義するために使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SQL Managed Instance のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **AzureSqlMI** に設定する必要があります。 | はい |
| connectionString |このプロパティでは、SQL 認証を使用して SQL Managed Instance に接続するために必要な **connectionString** 情報を指定します。 詳細については、次の例を参照してください。 <br/>既定のポートは 1433 です。 パブリック エンドポイントを持つ SQL Managed Instance を使用する場合は、ポート 3342 を明示的に指定します。<br> また、Azure Key Vault にパスワードを格納することもできます。 それが SQL 認証である場合は、接続文字列から `password` 構成を取得します。 詳細については、この表の後にある JSON の例および「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」を参照してください。 |はい |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 | サービス プリンシパルで Azure AD 認証を使う場合は、はい。 |
| servicePrincipalKey | アプリケーションのキーを取得します。 このフィールドを **SecureString** としてマークして Azure Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | サービス プリンシパルで Azure AD 認証を使う場合は、はい。 |
| tenant | ドメイン名やテナント ID など、アプリケーションが存在するテナントの情報を指定します。 これは、Azure portal の右上隅をマウスでポイントすることで取得できます。 | サービス プリンシパルで Azure AD 認証を使う場合は、はい。 |
| azureCloudType | サービス プリンシパル認証用に、ご利用の Azure AD アプリケーションが登録されている Azure クラウド環境の種類を指定します。 <br/> 指定できる値は、**AzurePublic**、**AzureChina**、**AzureUsGovernment**、および **AzureGermany** です。 既定では、データ ファクトリのクラウド環境が使用されます。 | いいえ |
| connectVia | この[統合ランタイム](concepts-integration-runtime.md)は、データ ストアに接続するために使用されます。 マネージド インスタンスにパブリック エンドポイントがあり、Azure Data Factory からそれにアクセスできるようにする場合は、セルフホステッド統合ランタイムまたは Azure 統合ランタイムを使用できます。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |はい |

さまざまな認証の種類の前提条件と JSON サンプルについては、以下のセクションをご覧ください。

- [SQL 認証](#sql-authentication)
- [Azure AD アプリケーション トークン認証: サービス プリンシパル](#service-principal-authentication)
- [Azure AD アプリケーション トークン認証: Azure リソースのマネージド ID](#managed-identity)

### <a name="sql-authentication"></a>SQL 認証

**例 1: SQL 認証を使用する**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
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
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

1. [Managed Instance の Azure Active Directory 管理者をプロビジョニングする](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)手順に従います。

2. Azure portal から [Azure Active Directory アプリケーションを作成します](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)。 アプリケーション名と、リンクされたサービスを定義する次の値を記録しておきます。

    - アプリケーション ID
    - アプリケーション キー
    - テナント ID

3. Azure Data Factory マネージド ID の[ログインを作成](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)します。 SQL Server Management Studio (SSMS) で、**sysadmin** である SQL Server アカウントを使用して、マネージド インスタンスに接続します。 **マスター** データベースで、次の T-SQL を実行します。

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. Azure Data Factory のマネージド ID 用に[包含データベース ユーザーを作成](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)します。 データのコピー元またはコピー先のデータベースに接続し、次の T-SQL を実行します。 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. SQL ユーザーや他のユーザーに対する通常の方法と同様に、Data Factory のマネージド ID に必要なアクセス許可を付与します。 次のコードを実行します。 詳細については、[こちらのドキュメント](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current)を参照してください。

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Azure Data Factory で、SQL Managed Instance のリンクされたサービスを構成します。

**例: サービス プリンシパル認証を使用する**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;",
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

データ ファクトリは、特定のデータ ファクトリを表す [Azure リソースのマネージド ID](data-factory-service-identity.md) に関連付けることができます。 このマネージド ID を SQL Managed Instance の認証に使用できます。 この ID を使用して指定したファクトリからデータベースにアクセスし、データベースに、またはデータベースからデータをコピーできます。

マネージド ID 認証を使用するには、次の手順に従います。

1. [Managed Instance の Azure Active Directory 管理者をプロビジョニングする](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)手順に従います。

2. Azure Data Factory マネージド ID の[ログインを作成](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)します。 SQL Server Management Studio (SSMS) で、**sysadmin** である SQL Server アカウントを使用して、マネージド インスタンスに接続します。 **マスター** データベースで、次の T-SQL を実行します。

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. Azure Data Factory のマネージド ID 用に[包含データベース ユーザーを作成](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)します。 データのコピー元またはコピー先のデータベースに接続し、次の T-SQL を実行します。 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. SQL ユーザーや他のユーザーに対する通常の方法と同様に、Data Factory のマネージド ID に必要なアクセス許可を付与します。 次のコードを実行します。 詳細については、[こちらのドキュメント](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current)を参照してください。

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Azure Data Factory で、SQL Managed Instance のリンクされたサービスを構成します。

**例: マネージ ID 認証を使用する**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、SQL Managed Instance データセットでサポートされるプロパティの一覧を示します。

SQL Managed Instance との間でデータをコピーするために、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティを **AzureSqlMITable** に設定する必要があります。 | はい |
| schema | スキーマの名前。 |ソースの場合はいいえ、シンクの場合ははい  |
| table | テーブル/ビューの名前。 |ソースの場合はいいえ、シンクの場合ははい  |
| tableName | スキーマがあるテーブル/ビューの名前。 このプロパティは下位互換性のためにサポートされています。 新しいワークロードでは、`schema` と `table` を使用します。 | ソースの場合はいいえ、シンクの場合ははい |

**例**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<SQL Managed Instance linked service name>",
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

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、SQL Managed Instance のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="sql-managed-instance-as-a-source"></a>ソースとしての SQL Managed Instance

>[!TIP]
>データ パーティション分割を使用して、SQL MI からデータを効率的に読み込む方法の詳細については、[SQL MI からの並列コピー](#parallel-copy-from-sql-mi)に関するセクションを参照してください。

SQL Managed Instance からデータをコピーするために、コピー アクティビティ ソース セクションでは次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **SqlMISource** に設定する必要があります。 | はい |
| sqlReaderQuery |このプロパティは、カスタム SQL クエリを使用してデータを読み取ります。 たとえば `select * from MyTable` です。 |いいえ |
| sqlReaderStoredProcedureName |このプロパティは、ソース テーブルからデータを読み取るストアド プロシージャの名前です。 最後の SQL ステートメントはストアド プロシージャの SELECT ステートメントにする必要があります。 |いいえ |
| storedProcedureParameters |これらのパラメーターは、ストアド プロシージャ用です。<br/>使用可能な値は、名前または値のペアです。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ |
| isolationLevel | SQL ソースのトランザクション ロック動作を指定します。 使用できる値は、次のとおりです。**ReadCommitted**、**ReadUncommitted**、**RepeatableRead**、**Serializable**、**Snapshot**。 指定しなかった場合は、データベースの既定の分離レベルが使用されます。 詳細については[こちらのドキュメント](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel)をご覧ください。 | いいえ |
| partitionOptions | SQL MI からデータを読み込むときに使用するデータ パーティション分割オプションを指定します。 <br>使用できる値は、以下のとおりです。**None** (既定値)、**PhysicalPartitionsOfTable**、および **DynamicRange**。<br>パーティション オプションが有効になっている (つまり、`None` ではない) 場合、SQL MI から同時にデータを読み込む並列処理の次数は、コピー アクティビティの [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) の設定によって制御されます。 | いいえ |
| partitionSettings | データ パーティション分割の設定のグループを指定します。 <br>パーティション オプションが `None` でない場合に適用されます。 | いいえ |
| ***`partitionSettings` の下:*** | | |
| partitionColumnName | 並列コピーの範囲パーティション分割で使用するソース列の名前を、**整数型または date/datetime 型**で指定します。 指定されない場合は、テーブルのインデックスまたは主キーが自動検出され、パーティション列として使用されます。<br>パーティション オプションが `DynamicRange` である場合に適用されます。 クエリを使用してソース データを取得する場合は、WHERE 句で `?AdfDynamicRangePartitionCondition ` をフックします。 例については、「[SQL データベースからの並列コピー](#parallel-copy-from-sql-mi)」セクションを参照してください。 | いいえ |
| partitionUpperBound | パーティション範囲を分割する、パーティション列の最大値。 この値は、テーブル内の行をフィルター処理するためではなく、パーティションのストライドを決定するために使用します。 テーブルまたはクエリ結果に含まれるすべての行がパーティション分割され、コピーされます。 指定しない場合、コピー アクティビティによって値が自動検出されます。  <br>パーティション オプションが `DynamicRange` である場合に適用されます。 例については、「[SQL データベースからの並列コピー](#parallel-copy-from-sql-mi)」セクションを参照してください。 | いいえ |
| partitionLowerBound | パーティション範囲を分割する、パーティション列の最小値。 この値は、テーブル内の行をフィルター処理するためではなく、パーティションのストライドを決定するために使用します。 テーブルまたはクエリ結果に含まれるすべての行がパーティション分割され、コピーされます。 指定しない場合、コピー アクティビティによって値が自動検出されます。<br>パーティション オプションが `DynamicRange` である場合に適用されます。 例については、「[SQL データベースからの並列コピー](#parallel-copy-from-sql-mi)」セクションを参照してください。 | いいえ |

**以下の点に注意してください。**

- **SqlMISource** に **sqlReaderQuery** が指定されている場合、コピー アクティビティでは、データを取得するために SQL Managed Instance のインスタンス ソースに対してこのクエリを実行します。 **sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することもできます (ストアド プロシージャでパラメーターを使用する場合)。
- **sqlReaderQuery** または **sqlReaderStoredProcedureName** プロパティを指定しない場合は、データセット JSON の "structure" セクションで定義されている列を使用して、クエリが作成されます。 クエリ `select column1, column2 from mytable` は SQL Managed Instance に対して実行されます。 データセット定義に "structure" がない場合は、すべての列がテーブルから選択されます。

**例:SQL クエリの使用**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
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
                "type": "SqlMISource",
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
                "referenceName": "<SQL Managed Instance input dataset name>",
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
                "type": "SqlMISource",
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

### <a name="sql-managed-instance-as-a-sink"></a>シンクとしての SQL Managed Instance

> [!TIP]
> サポートされる書き込み動作、構成、およびベスト プラクティスの詳細については、「[SQL Managed Instance にデータを読み込む場合のベスト プラクティス](#best-practice-for-loading-data-into-sql-managed-instance)」を参照してください。

データを SQL Managed Instance にコピーするために、コピー アクティビティ シンク セクションでは次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは、**SqlMISink** に設定する必要があります。 | はい |
| preCopyScript |このプロパティでは、コピー アクティビティで SQL Managed Instance にデータを書き込む前に実行する SQL クエリを指定します。 これは、コピー実行ごとに 1 回だけ呼び出されます。 このプロパティを使用して、事前に読み込まれたデータをクリーンアップできます。 |いいえ |
| tableOption | ソースのスキーマに基づいて[自動的にシンク テーブルを作成する](copy-activity-overview.md#auto-create-sink-tables)かどうかを指定します (存在しない場合)。 シンクでストアド プロシージャが指定されている場合、テーブルの自動作成はサポートされません。 使用できる値は `none` (既定値)、`autoCreate` です。 |いいえ |
| sqlWriterStoredProcedureName | ターゲット テーブルにソース データを適用する方法を定義しているストアド プロシージャの名前です。 <br/>このストアド プロシージャは*バッチごとに呼び出されます*。 1 回だけ実行され、ソース データとは関係がない操作 (削除/切り詰めなど) の場合は、`preCopyScript` プロパティを使用します。<br>例については、「[SQL シンクからのストアド プロシージャの呼び出し](#invoke-a-stored-procedure-from-a-sql-sink)」を参照してください。 | いいえ |
| storedProcedureTableTypeParameterName |ストアド プロシージャで指定されたテーブル型のパラメーター名。  |いいえ |
| sqlWriterTableType |ストアド プロシージャで使用するテーブル型の名前。 コピー アクティビティでは、このテーブル型の一時テーブルでデータを移動できます。 その後、ストアド プロシージャのコードにより、コピーされたデータを既存のデータと結合できます。 |いいえ |
| storedProcedureParameters |ストアド プロシージャのパラメーター。<br/>使用可能な値は、名前と値のペアです。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 | いいえ |
| writeBatchSize |SQL テーブルに挿入する "*バッチあたりの*" 行数。<br/>使用可能な値は、行数の場合整数です。 既定では、Azure Data Factory により行のサイズに基づいて適切なバッチ サイズが動的に決定されます。  |いいえ |
| writeBatchTimeout |このプロパティは、タイムアウトする前に一括挿入操作の完了を待つ時間を指定します。<br/>使用可能な値は期間に対する値です。 たとえば "00:30:00" (30 分) を指定できます。 |いいえ |

**例 1: データを追加する**

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
                "referenceName": "<SQL Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**例 2: コピー中にストアド プロシージャを呼び出す**

詳細については、[SQL MI シンクからのストアド プロシージャの呼び出し](#invoke-a-stored-procedure-from-a-sql-sink)に関するセクションを参照してください。

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
                "referenceName": "<SQL Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
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

## <a name="parallel-copy-from-sql-mi"></a>SQL MI からの並列コピー

コピー アクティビティのときに、Azure SQL Managed Instance コネクタには、データを並列でコピーする組み込みのデータ パーティション分割が用意されています。 データ パーティション分割オプションは、コピー アクティビティの **[ソース]** タブにあります。

![パーティションのオプションのスクリーンショット](./media/connector-sql-server/connector-sql-partition-options.png)

パーティション分割でのコピーを有効にすると、コピー アクティビティによって自分の SQL MI ソースに対して並列クエリが実行され、パーティションごとにデータが読み込まれます。 並列度は、コピー アクティビティの [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 設定によって制御されます。 たとえば、`parallelCopies` を 4 に設定した場合、Data Factory では、自分の指定したパーティション オプションと設定に基づいて 4 つのクエリを同時に生成し、実行します。各クエリは、自分の SQL MI からデータの一部を取得します。

特に、自分の SQL MI から大量のデータを読み込む場合は、データのパーティション分割を使用した並列コピーを有効にすることをお勧めします。 さまざまなシナリオの推奨構成を以下に示します。 ファイルベースのデータ ストアにデータをコピーする場合は、複数のファイルとしてフォルダーに書き込む (フォルダー名のみを指定する) ことをお勧めします。この場合、1 つのファイルに書き込むよりもパフォーマンスが優れています。

| シナリオ                                                     | 推奨設定                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 物理パーティションに分割された大きなテーブル全体から読み込む。        | **パーティション オプション**: テーブルの物理パーティション。 <br><br/>実行中に、Data Factory によって物理パーティションが自動的に検出され、パーティションごとにデータがコピーされます。 |
| データ パーティション分割用の整数または datetime の列がある、物理パーティションがない、大きなテーブルを完全に読み込む。 | **パーティション オプション**: 動的範囲パーティション。<br>**パーティション列** (省略可能):データのパーティション分割に使用される列を指定します。 指定しない場合は、インデックスまたは主キー列が使用されます。<br/>**パーティションの上限**と**パーティションの下限** (省略可能):パーティションのストライドを決定する場合に指定します。 これは、テーブル内の行をフィルター処理するのではなく、テーブル内のすべての行をパーティション分割してコピーします。 指定しない場合、コピー アクティビティによって値が自動検出されます。<br><br>たとえば、自分のパーティション列 "ID" の値の範囲が 1 から 100 で、下限を 20 に、上限を 80 に設定し、並列コピーを 4 にした場合、Data Factory によって 4 つのパーティションでデータが取得されます。ID の範囲は、それぞれ、20 以下、21 から 50、51 から 80、81 以上となります。 |
| データ パーティション分割用の整数列または date/datetime 列を使用して、物理パーティションがない大量のデータを、カスタム クエリを使用して読み込む。 | **パーティション オプション**: 動的範囲パーティション。<br>**クエリ**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br>**パーティション列**: データのパーティション分割に使用される列を指定します。<br>**パーティションの上限**と**パーティションの下限** (省略可能):パーティションのストライドを決定する場合に指定します。 これは、テーブル内の行をフィルター処理するのではなく、クエリ結果のすべての行をパーティション分割してコピーします。 指定しない場合、コピー アクティビティによって値が自動検出されます。<br><br>実行中に、Data Factory によって `?AdfRangePartitionColumnName` が各パーティションの実際の列名および値の範囲に置き換えられ、SQL MI に送信されます。 <br>たとえば、パーティション列 "ID" の値の範囲が 1 から 100 で、下限を 20 に、上限を 80 に設定し、並列コピーを 4 にした場合、Data Factory によって 4 つのパーティションでデータが取得されます。ID の範囲は、それぞれ、20 以下、21 から 50、51 から 80、81 以上となります。 |

パーティション オプションを使用してデータを読み込む場合のベスト プラクティス:

1. パーティション列 (主キーや一意キーなど) として特徴のある列を選択し、データ スキューを回避します。 
2. テーブルに組み込みパーティションがある場合は、パーティション オプションとして "テーブルの物理パーティション" を使用してパフォーマンスを向上させます。  
3. Azure Integration Runtime を使用してデータをコピーする場合は、より大きな (4 より大きい) "[データ統合単位 (DIU)](copy-activity-performance-features.md#data-integration-units)" を設定すると、より多くのコンピューティング リソースを利用できます。 そこで、該当するシナリオを確認してください。
4. パーティション数は、"[コピーの並列処理の次数](copy-activity-performance-features.md#parallel-copy)" によって制御されます。この数値を大きくしすぎるとパフォーマンスが低下するため、この数値は、(DIU またはセルフホステッド IR ノードの数) x (2 から 4) に設定することをお勧めします。

**例: 複数の物理パーティションがある大きなテーブルを完全に読み込む**

```json
"source": {
    "type": "SqlMISource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**例: 動的範囲パーティションを使用してクエリを実行する**

```json
"source": {
    "type": "SqlMISource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

## <a name="best-practice-for-loading-data-into-sql-managed-instance"></a>SQL Managed Instance にデータを読み込む場合のベスト プラクティス

SQL Managed Instance にデータをコピーする場合は、さまざまな書き込み動作が必要になることがあります。

- [追加](#append-data): ソース データには新しいレコードのみが含まれている。
- [アップサート](#upsert-data): ソース データには挿入と更新の両方が含まれている。
- [上書き](#overwrite-the-entire-table): 毎回ディメンション テーブル全体を再度読み込みたい。
- [カスタム ロジックでの書き込み](#write-data-with-custom-logic): 宛先テーブルへの最終挿入の前に追加の処理が必要である。 

Azure Data Factory で構成する方法およびベスト プラクティスについては、対応するセクションを参照してください。

### <a name="append-data"></a>データを追加する

データの追加は、SQL Managed Instance シンク コネクタの既定の動作です。 Azure Data Factory では、テーブルに効率的に書き込むために一括挿入が実行されます。 コピー アクティビティで、それに応じてソースとシンクを構成できます。

### <a name="upsert-data"></a>データをアップサートする

**オプション 1:** 大量のデータをコピーする場合は、コピー アクティビティを使用してすべてのレコードをステージング テーブルに一括読み込みしてから、ストアド プロシージャ アクティビティを実行して、[MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-mi-current) または INSERT/UPDATE ステートメントを 1 回で適用します。 

コピー アクティビティでは現在、データベース一時テーブルへのデータの読み込みはネイティブでサポートされていません。 複数のアクティビティを組み合わせて設定するための高度な方法があります。[SQL Database の一括 upsert シナリオの最適化](https://github.com/scoriani/azuresqlbulkupsert)に関するページを参照してください。 以下に、永続的テーブルをステージングとして使用する例を示します。

例として、Azure Data Factory で、**コピー アクティビティ**と**ストアド プロシージャ アクティビティ**を連結させたパイプラインを作成できます。 前者では、ソース ストアから Azure SQL Managed Instance ステージング テーブル (たとえば、データセット内のテーブル名 **UpsertStagingTable**) にデータがコピーされます。 その後、後者でストアド プロシージャが呼び出され、ステージング テーブルのソース データがターゲット テーブルにマージされて、ステージング テーブルがクリーンアップされます。

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

SQL Managed Instance にデータをコピーする場合は、ソース テーブルの各バッチに対して追加のパラメーターを使用して、ユーザー指定のストアド プロシージャを構成して呼び出すこともできます。 ストアド プロシージャ機能は [テーブル値パラメーター](https://msdn.microsoft.com/library/bb675163.aspx)を利用しています。

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

3. Azure Data Factory で、コピー アクティビティの **SQL MI シンク** セクションを次のように定義します。

    ```json
    "sink": {
        "type": "SqlMISink",
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

## <a name="data-type-mapping-for-sql-managed-instance"></a>SQL Managed Instance のデータ型のマッピング

SQL Managed Instance との間でデータをコピーするときに、次の SQL Managed Instance のデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| SQL Managed Instance のデータ型 | Azure Data Factory の中間データ型 |
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

[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) を使用して Azure SQL Managed Instance との間でデータをコピーする場合は、セルフホステッド統合ランタイムを介して[汎用 ODBC コネクタ](connector-odbc.md)および SQL Server ODBC ドライバーを使用します。 この Azure SQL Managed Instance コネクタでは、現在、Always Encrypted はサポートされていません。 

具体的には次のとおりです。

1. セルフホステッド統合ランタイムを設定します (存在しない場合)。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。

2. SQL Server 用の 64 ビット ODBC ドライバーを[こちら](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server?view=azuresqldb-mi-current)からダウンロードし、Integration Runtime コンピューターにインストールします。 このドライバーがどのように機能するかについて詳しくは、「[SQL Server 用 ODBC ドライバーと共に Always Encrypted を使用する](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current#using-the-azure-key-vault-provider)」を参照してください。

3. SQL データベースに接続するために、ODBC データ型を使用してリンクされたサービスを作成します。以下のサンプルを参照してください。

    - **SQL 認証**を使用するには: 以下のように ODBC 接続文字列を指定し、ユーザー名とパスワードを設定するための**基本**認証を選択します。

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - **Data Factory マネージド ID の認証**を使用するには: 

        1. 同じ[前提条件](#managed-identity)に従って、マネージド ID のデータベース ユーザーを作成し、データベースに適切なロールを付与します。
        2. リンクされたサービスで、次のように ODBC 接続文字列を指定し、**匿名**認証を選択します。接続文字列自体では `Authentication=ActiveDirectoryMsi` と示されます。

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. これに応じて、データセットとコピー アクティビティを ODBC データ型で作成します。 詳細については [ODBC コネクタ](connector-odbc.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するページをご覧ください。
