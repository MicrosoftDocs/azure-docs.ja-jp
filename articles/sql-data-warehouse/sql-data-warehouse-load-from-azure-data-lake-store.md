---
title: 'チュートリアル: Azure Data Lake Storage から Azure SQL Data Warehouse に読み込む | Microsoft Docs'
description: PolyBase 外部テーブルを使用して Azure Data Lake Storage から Azure SQL Data Warehouse にデータを読み込みます。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 07/17/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: cbf642b47e4233cec2e2d860288b3bb35b419cf2
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304167"
---
# <a name="load-data-from-azure-data-lake-storage-to-sql-data-warehouse"></a>Azure Data Lake Storage から Azure SQL Data Warehouse へのデータの読み込み
PolyBase 外部テーブルを使用して Azure Data Lake Storage から Azure SQL Data Warehouse にデータを読み込みます。 Data Lake Storage の格納データに対してアドホック クエリを実行できますが、最高のパフォーマンスを得るには、SQL Data Warehouse にデータをインポートすることをお勧めします。

> [!div class="checklist"]
> * Data Lake Storage から読み込む必要のあるデータベース オブジェクトを作成する。
> * Data Lake Storage ディレクトリに接続する。
> * Azure SQL Data Warehouse にデータを読み込む。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="before-you-begin"></a>開始する前に
このチュートリアルを始める前に、最新バージョンの [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) をダウンロードしてインストールします。

このチュートリアルを実行するには、次のものが必要です。

* Gen1 から読み込む場合は、サービス間認証に使用する Azure Active Directory アプリケーション。 作成方法については、[Azure Active Directory 認証](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)に関するページを参照してください。

>[!NOTE] 
> Azure Data Lake Stroage Gen1 から読み込む場合は、SQL Data Warehouse からストレージ アカウントに接続するために、Active Directory アプリケーションのクライアント ID、キー、OAuth2.0 トークン エンドポイント値が必要です。 これらの値を取得する方法の詳細については、上記のリンクを参照してください。 Azure Active Directory アプリの登録では、アプリケーション ID をクライアント ID として使います。
> 

* Azure SQL Data Warehouse。 [Azure SQL Data Warehouse の作成とクエリ](create-data-warehouse-portal.md)に関するページをご覧ください。

* Data Lake Storage アカウント。 [Azure Data Lake Storage の使用開始](../data-lake-store/data-lake-store-get-started-portal.md)に関する記事を参照してください。 

##  <a name="create-a-credential"></a>資格情報を作成する
Data Lake Storage アカウントにアクセスするには、次の手順で使用する資格情報シークレットを暗号化するためのデータベース マスター キーを作成する必要があります。 その後、データベース スコープ資格情報を作成します。 Gen1 の場合、AAD のサービス プリンシパルの資格情報設定が格納されたデータベース スコープ資格情報を作成します。 Gen2 のデータベース スコープ資格情報のストレージ アカウント キーを使用する必要があります。 

Data Lake Store Gen1 に接続するには、**最初に** Azure Active Directory Application を作成し、アクセス キーを作成して、アプリケーションのアクセス許可を Data Lake Storage Gen1 のリソースに付与する必要があります。 手順については、[Active Directory を使用した Azure Data Lake Storage Gen1 での認証](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)に関するページをご覧ください。

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B (for Gen1): Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- B (for Gen2): Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;

-- It should look something like this for Gen1:
CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>外部データ ソースを作成する
データの場所を格納するには、この [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) コマンドを使用します。 

```sql
-- C (for Gen1): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<datalakestoregen1accountname>.azuredatalakestore.net',
    CREDENTIAL = ADLSCredential
);

-- C (for Gen2): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen2 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION='abfss://<container>@<AzureDataLake account_name>.dfs.core.windows.net', -- Please note the abfs endpoint
    CREDENTIAL = ADLSCredential
);
```

## <a name="configure-data-format"></a>データ形式の構成
Data Lake Storage からデータをインポートするには、外部ファイル形式を指定する必要があります。 このオブジェクトでは、Data Lake Storage にファイルを書き込む方法が定義されます。
完全な一覧については、[CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql) に関する T-SQL のドキュメントをご覧ください。

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-external-tables"></a>外部テーブルを作成する
データ ソースとファイル形式を指定したら、外部テーブルを作成できます。 外部テーブルは、外部データを処理する方法を示したものです。 場所のパラメーターは、ファイルまたはディレクトリを指定できます。 ディレクトリを指定すると、そのディレクトリ内のすべてのファイルが読み込まれます。

```sql
-- D: Create an External Table
-- LOCATION: Folder under the Data Lake Storage root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStorage
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>外部テーブルに関する考慮事項
外部テーブルを作成するのは簡単ですが、いくつかの点について説明しておく必要があります。

外部テーブルは厳密に型指定されます。 これは、取り込むデータの各行がテーブルのスキーマ定義を満たす必要があることを意味します。
ある行がスキーマ定義に一致しない場合、その行の読み込みは拒否されます。

REJECT_TYPE および REJECT_VALUE オプションでは､最終のテーブルに存在する必要がある行数またはデータの割合を定義することができます｡ 読み込み中に値が定義した数または割合に達すると、読み込み操作が失敗します。 行が拒否される最も一般的な原因は、スキーマ定義との不一致です。 たとえば、ファイルに含まれるデータが文字列の場合に、列に int 型のスキーマが指定されていると、すべての行の読み込みが失敗します。

Data Lake Storage Gen1 は、ロール ベースのアクセス制御 (RBAC) を使って、データへのアクセスを制御します。 つまり、サービス プリンシパルは、Location パラメーターで定義されているディレクトリと、最終的なディレクトリとファイルの子に対する、読み取りアクセス許可を持っている必要があります。 これにより、PolyBase は認証を行って、そのデータを読み込むことができます。 

## <a name="load-the-data"></a>データを読み込む
Data Lake Storage からデータを読み込むには、[CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) ステートメントを使用します。 

CTAS により新しいテーブルが作成され、select ステートメントの結果が設定されます。 CTAS では、select ステートメントの結果と同じ列とデータ型が保持されるように、新しいテーブルが定義されます。 外部テーブルからすべての列を選択すると、新しいテーブルは、外部テーブルの列とデータ型のレプリカになります。

この例では、外部テーブル DimProduct_external から DimProduct というハッシュ分散テーブルを作成しています。

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>列ストア圧縮の最適化
既定では、SQL Data Warehouse には、テーブルがクラスター化列ストア インデックスとして格納されます。 読み込みの完了時、一部のデータ行が、列ストアに圧縮されない可能性があります。  これにはさまざまな理由があります。 詳細については、 [列ストア インデックスの管理](sql-data-warehouse-tables-index.md)に関するページをご覧ください。

読み込み後のクエリのパフォーマンスと列ストア圧縮を最適化するには、列ストア インデックスですべての行が強制的に圧縮されるようにテーブルを再構築します。

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>統計の最適化
読み込みの直後に単一列の統計を作成することをお勧めします。 統計には選択肢がいくつかあります。 たとえば、すべての列に対して単一列の統計を作成する場合は、すべての統計の再構築に時間がかかる場合があります。 クエリ述語に含まれない列があることがわかっている場合は、その列に対する統計の作成はスキップできます。

すべてのテーブルのすべての列で単一列の統計を作成する場合は、[統計に関する記事](sql-data-warehouse-tables-statistics.md)のストアド プロシージャのコード サンプル `prc_sqldw_create_stats` を使うことができます。

次の例は、統計の作成の出発点として適しています。 これによりディメンション テーブルの各列と、ファクト テーブルの各結合列に、単一列の統計が作成されます。 他のファクト テーブルの列には、後で単一列または複数列の統計をいつでも追加できます。

## <a name="achievement-unlocked"></a>結果
データが Azure SQL Data Warehouse に正常に読み込まれました。 すばらしい結果です。

## <a name="next-steps"></a>次の手順 
このチュートリアルでは、外部テーブルを作成して Data Lake Storage Gen1 の格納データの構造を定義した後、PolyBase の CREATE TABLE AS SELECT ステートメントを使って、データ ウェアハウスにデータを読み込みました。 

以下のことを行いました。
> [!div class="checklist"]
> * Data Lake Store Gen1 から読み込む必要のあるデータベース オブジェクトを作成しました。
> * Data Lake Storage Gen1 ディレクトリに接続しました。
> * Azure SQL Data Warehouse にデータを読み込みました。
> 

データの読み込みは、SQL Data Warehouse を使ってデータ ウェアハウス ソリューションを開発する際の最初の手順です。 開発リソースを確認してください。

> [!div class="nextstepaction"]
>[SQL Data Warehouse でテーブルを開発する方法を学習する](sql-data-warehouse-tables-overview.md)




