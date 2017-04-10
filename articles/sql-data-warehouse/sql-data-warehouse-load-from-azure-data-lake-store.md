---
title: "Azure Data Lake Store から SQL Data Warehouse への読み込み | Microsoft Docs"
description: "PolyBase 外部テーブルを使用して Azure Data Lake Store から Azure SQL Data Warehouse にデータを読み込む方法について説明します。"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 01/25/2017
ms.author: cakarst;barbkess
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: aca0e4cfdcfb3e3ed2e69ad8153b4c965b299806
ms.lasthandoff: 03/15/2017



---
# <a name="load-data-from-azure-data-lake-store-into-sql-data-warehouse"></a>Azure Data Lake Store から Azure SQL Data Warehouse へのデータの読み込み
このドキュメントでは、PolyBase を使用して Azure Data Lake Store (ADLS) から SQL Data Warehouse にデータを読み込むために必要な手順を&1; つずつ説明します。
外部テーブルを使用すると、ADLS に格納されているデータに対してアドホック クエリを実行できます。ただし、ベスト プラクティスとしては、SQL Data Warehouse にデータをインポートすることをお勧めします。
予想所要時間: 10 分 (前提条件が満たされている場合)。
>
このチュートリアルで学習する内容は次のとおりです。

1. Azure Data Lake Store から読み込む外部データベース オブジェクトを作成する。
2. Azure Data Lake Store ディレクトリに接続する。
3. Azure SQL Data Warehouse にデータを読み込む。

## <a name="before-you-begin"></a>開始する前に
このチュートリアルを実行するには、次のものが必要です。

* サービス間認証に使用する Azure Active Directory アプリケーション。 作成方法については、[Azure Active Directory 認証](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)に関するページを参照してください。

>[!NOTE] 
> SQL Data Warehouse から Azure Data Lake に接続するには、Active Directory アプリケーションのクライアント ID、キー、OAuth2.0 トークン エンドポイント値が必要です。 これらの値を取得する方法の詳細については、上記のリンクを参照してください。

* SQL Server Management Studio または SQL Server Data Tools。SSMS をダウンロードして接続する方法については、[SSMS に対してクエリを実行する](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-query-ssms)方法に関するページを参照してください。

* Azure SQL Data Warehouse。作成方法については、https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-provision を参照してください。

* 暗号化が有効になっていない Azure Data Lake Store。 作成方法については、https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal を参照してください。




## <a name="configure-the-data-source"></a>データ ソースの構成
PolyBase では T-SQL 外部オブジェクトを使用して、外部データの場所と属性を定義します。 外部オブジェクトは、SQL Data Warehouse に格納され、外部に格納されているデータを参照します。


###  <a name="create-a-credential"></a>資格情報を作成する
Azure Data Lake Store にアクセスするには、次の手順で使用する資格情報シークレットを暗号化するためのデータベース マスター キーを作成する必要があります。
次に、AAD のサービス プリンシパルの資格情報設定が格納されたデータベース スコープ資格情報を作成します。 資格情報の構文が異なるため、PolyBase を使用して Microsoft Azure Storage BLOB に接続しているユーザーはこの点に注意してください。
Azure Data Lake Store に接続するには、**最初に** Azure Active Directory Application を作成し、アクセス キーを作成して、アプリケーションのアクセス許可を Azure Data Lake のリソースに付与する必要があります。 これらの作業の実行手順については、[こちら](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)を参照してください。

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/en-us/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/en-us/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

```


### <a name="create-the-external-data-source"></a>外部データ ソースを作成する
この [CREATE EXTERNAL DATA SOURCE][CREATE EXTERNAL DATA SOURCE] コマンドを使って、データの場所とデータ型を格納します。
ADL URI は、Azure Portal と www.portal.azure.com で確認できます。

```sql
-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Store.
-- LOCATION: Provide Azure Data Lake accountname and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<AzureDataLake account_name>.azuredatalake.net',
    CREDENTIAL = ADLCredential
);
```



## <a name="configure-data-format"></a>データ形式の構成
ADLS からデータをインポートするには、外部ファイル形式を指定する必要があります。 このコマンドには、ファイルの形式ごとにデータの記述に関するオプションが用意されています。
一般的に使用されるファイル形式、つまりパイプで区切られたテキスト ファイルの例を次に示します。
[CREATE EXTERNAL FILE FORMAT][CREATE EXTERNAL FILE FORMAT] の完全な一覧については、T-SQL のドキュメントを参照してください。

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store all Missing values as NULL

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

## <a name="create-the-external-tables"></a>外部テーブルの作成
データ ソースとファイル形式を指定したら、外部テーブルを作成できます。 外部テーブルは、外部データを処理する方法を示したものです。 PolyBase は、再帰的なディレクトリ スキャンを使用して、location パラメーターで指定されたディレクトリのすべてのサブディレクトリを対象に、ファイルをすべて読み取ります。 また、次の例では、オブジェクトを作成する方法も紹介しています。 このステートメントは、ADLS 内のデータに合わせてカスタマイズする必要があります。

```sql
-- D: Create an External Table
-- LOCATION: Folder under the ADLS root folder.
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
,   DATA_SOURCE = AzureDataLakeStore
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>外部テーブルに関する考慮事項
外部テーブルを作成するのは簡単ですが、いくつかの点について説明しておく必要があります。

PolyBase によるデータの読み込みは、厳密に型指定されます。 これは、取り込むデータの各行がテーブルのスキーマ定義を満たす必要があることを意味します。
特定の行がスキーマの定義と一致しない場合、その行の読み込みは拒否されます。

Reject_Type と Reject_Value を使用すると、最終的なテーブルに必要なデータの行数または割合を定義できます。
読み込み中に値が定義した数または割合に達すると、読み込み操作が失敗します。 行が拒否される最も一般的な原因は、スキーマ定義との不一致です。
たとえば、ファイルに含まれるデータが文字列の場合に、列に int 型のスキーマが指定されていると、すべての行の読み込みが失敗します。

Location は、データの読み取りを開始する最上位のディレクトリを指定します。
この場合、/DimProduct/ の下にサブディレクトリがあると、PolyBase はサブディレクトリ内のすべてのデータをインポートします。

## <a name="load-the-data"></a>データを読み込む
Azure Data Lake Store からデータを読み込むには、[CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] ステートメントを使用します。 CTAS による読み込みでは、自分で作成した厳密に型指定された外部テーブルを使用します。

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
既定では、SQL Data Warehouse には、テーブルがクラスター化列ストア インデックスとして格納されます。 読み込みの完了時、一部のデータ行が、列ストアに圧縮されない可能性があります。  これにはさまざまな理由があります。 詳しくは、「[列ストア インデックスの管理][manage columnstore indexes]」をご覧ください。

読み込み後のクエリのパフォーマンスと列ストア圧縮を最適化するには、列ストア インデックスですべての行が強制的に圧縮されるようにテーブルを再構築します。

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

列ストア インデックスの保守について詳しくは、「[列ストア インデックスの管理][manage columnstore indexes]」をご覧ください。

## <a name="optimize-statistics"></a>統計の最適化
読み込みの直後に単一列の統計を作成することをお勧めします。 統計には選択肢がいくつかあります。 たとえば、すべての列に対して単一列の統計を作成する場合は、すべての統計の再構築に時間がかかる場合があります。 クエリ述語に含まれない列があることがわかっている場合は、その列に対する統計の作成はスキップできます。

すべてのテーブルのすべての列で単一列の統計を作成する場合は、[統計に関する記事][statistics]のストアド プロシージャのコード サンプル `prc_sqldw_create_stats` を使うことができます。

次の例は、統計の作成の出発点として適しています。 これによりディメンション テーブルの各列と、ファクト テーブルの各結合列に、単一列の統計が作成されます。 他のファクト テーブルの列には、後で単一列または複数列の統計をいつでも追加できます。


## <a name="achievement-unlocked"></a>結果
データが Azure SQL Data Warehouse に正常に読み込まれました。 すばらしい結果です。

##<a name="next-steps"></a>次のステップ
データの読み込みは、SQL Data Warehouse を使ってデータ ウェアハウス ソリューションを開発する際の最初の手順です。 [テーブル](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-overview)と [T-SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-loops.md) に関する開発リソースを確認してください。


<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[manage columnstore indexes]: sql-data-warehouse-tables-index.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md

