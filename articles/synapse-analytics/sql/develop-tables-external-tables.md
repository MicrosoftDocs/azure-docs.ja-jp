---
title: Synapse SQL で外部テーブルを使用する
description: Synapse SQL を使用したデータ ファイルの読み取りまたは書き込み
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 78e4b35feb4e830a9f4335614a55d49ca90cd791
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667637"
---
# <a name="use-external-tables-with-synapse-sql"></a>Synapse SQL で外部テーブルを使用する

外部テーブルは、Hadoop、Azure Storage BLOB、または Azure Data Lake Storage にあるデータを参照します。 外部テーブルは、Azure Storage 内のファイルからデータを読み取ったり、ファイルにデータを書き込んだりするために使用されます。 Synapse SQL では、外部テーブルを使用して、専用 SQL プールまたはサーバーレス SQL プールに対するデータの読み取りと書き込みを行えます。

## <a name="external-tables-in-dedicated-sql-pool-and-serverless-sql-pool"></a>専用 SQL プールとサーバーレス SQL プールにおける外部テーブル

### <a name="dedicated-sql-pool"></a>[専用 SQL プール](#tab/sql-pool) 

専用 SQL プールでは、外部テーブルを使用して以下を行えます。

- Transact-SQL ステートメントを使用して、Azure Blob Storage と Azure Data Lake Gen2 に対するクエリを実行する。
- Azure Blob Storage と Azure Data Lake Storage から専用 SQL プールにデータのインポートと格納を行う。

[CREATE TABLE AS SELECT](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ステートメントと組み合わせて使用する場合は、外部テーブルから選択するとデータが SQL プール内のテーブルにインポートされます。 [COPY ステートメント](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)に加えて、外部テーブルはデータの読み込みにも便利です。 

読み込みのチュートリアルについては、[PolyBase を使用した Azure Blob Storage からのデータの読み込み](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)に関するページを参照してください。

### <a name="serverless-sql-pool"></a>[サーバーレス SQL プール](#tab/sql-on-demand)

サーバーレス SQL プールでは、外部テーブルを使用して以下を行います。

- Transact-SQL ステートメントを使用して、Azure Blob Storage または Azure Data Lake Storage 内のデータのクエリを実行する
- [CETAS](develop-tables-cetas.md) を使用して、サーバーレス SQL プールのクエリ結果を Azure Blob Storage または Azure Data Lake Storage 内のファイルに格納する

次の手順を通じて、サーバーレス SQL プールを使用して外部テーブルを作成できます。

1. CREATE EXTERNAL DATA SOURCE
2. CREATE EXTERNAL FILE FORMAT
3. CREATE EXTERNAL TABLE

---

### <a name="security"></a>セキュリティ

ユーザーは、外部テーブルのデータを読み取る場合、それに対する `SELECT` アクセス許可が必要です。
外部テーブルから、基になる Azure Storage へのアクセスは、次の規則を使用してデータソース内で定義されているデータベース スコープ資格情報を使用して行います。
- 資格情報なしのデータソースの場合、外部テーブルからは、Azure Storage 上の一般公開されているファイルにアクセスできます。
- SAS トークンまたはワークスペースのマネージド ID を使用して外部テーブルが Azure Storage 上のファイルにしかアクセスできないようにするための資格情報をデータソースに含めることができます。例については、[ストレージ ファイルのストレージ アクセス制御の開発](develop-storage-files-storage-access-control.md#examples)に関する記事を参照してください。

> [!IMPORTANT]
> 専用 SQL プール内では、資格情報なしで作成されたデータソースから、Azure AD ユーザーは自分の Azure AD ID を使用してストレージ ファイルにアクセスすることができます。 サーバーレス SQL プールでは、`IDENTITY='User Identity'` プロパティを含むデータベース スコープ資格情報を使用してデータソースを作成する必要があります。[こちらの例](develop-storage-files-storage-access-control.md#examples)を参照してください。

## <a name="create-external-data-source"></a>CREATE EXTERNAL DATA SOURCE

外部データ ソースは、ストレージ アカウントへの接続に使用されます。 完全なドキュメントについては、[こちら](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)で概説されています。

### <a name="syntax-for-create-external-data-source"></a>CREATE EXTERNAL DATA SOURCE の構文

#### <a name="dedicated-sql-pool"></a>[専用 SQL プール](#tab/sql-pool)

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
     , TYPE = HADOOP
)
[;]
```

#### <a name="serverless-sql-pool"></a>[サーバーレス SQL プール](#tab/sql-on-demand)

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
)
[;]
```

---

### <a name="arguments-for-create-external-data-source"></a>CREATE EXTERNAL DATA SOURCE の引数

data_source_name

データ ソースのユーザー定義の名前を指定します。 名前は、データベース内で一意である必要があります。

#### <a name="location"></a>場所
LOCATION = `'<prefix>://<path>'`: 接続プロトコルと外部データ ソースへのパスを指定します。 場所では、次のパターンを使用できます。

| 外部データ ソース        | 場所プレフィックス | ロケーション パス                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Azure Blob Storage          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Azure Blob Storage          | `http[s]`       | `<storage_account>.blob.core.windows.net/<container>/subfolders` |
| Azure Data Lake Store Gen 1 | `http[s]`       | `<storage_account>.azuredatalakestore.net/webhdfs/v1` |
| Azure Data Lake Store Gen 2 | `http[s]`       | `<storage_account>.dfs.core.windows.net/<container>/subfolders`  |

`https:` プレフィックスを使用すると、パスでサブフォルダーを使用できます。

#### <a name="credential"></a>資格情報
CREDENTIAL = `<database scoped credential>` は、Azure Storage 上で認証を受けるために使用される省略可能な資格情報です。 資格情報を持たない外部データソースからは、パブリック ストレージ アカウントにアクセスできます。 

専用 SQL プール内の資格情報を持たない外部データソースからは、呼び出し元の Azure AD ID を使用して、ストレージ上のファイルにアクセスします。 資格情報 `IDENTITY='User Identity'` があるサーバーレス SQL プールの外部データ ソースからは、呼び出し元の Azure AD ID を使用してファイルにアクセスします。
- 専用 SQL プールでは、データベース スコープ資格情報を使用して、カスタム アプリケーション ID、ワークスのペース マネージド ID、または SAK キーを指定できます。 
- サーバーレス SQL プールでは、データベース スコープ資格情報を使用して、呼び出し元の Azure AD ID、ワークスペースのマネージド ID、または SAS キーを指定できます。 

#### <a name="type"></a>TYPE
TYPE = `HADOOP` は専用 SQL プールでの必須オプションです。これにより、Polybase テクノロジを使用して基になるファイルにアクセスすることが指定されます。 このパラメーターは、組み込みのネイティブ リーダーを使用するサーバーレス SQL プールでは使用できません。

### <a name="example-for-create-external-data-source"></a>CREATE EXTERNAL DATA SOURCE の例

#### <a name="dedicated-sql-pool"></a>[専用 SQL プール](#tab/sql-pool)

次の例では、New York データ セットを参照する Azure Data Lake Gen2 の外部データ ソースを作成します。

```sql
CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://data@newyorktaxidataset.dfs.core.windows.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

#### <a name="serverless-sql-pool"></a>[サーバーレス SQL プール](#tab/sql-on-demand)

次の例では、SAS 資格情報を使用してアクセスできる Azure Data Lake Gen2 の外部データソースを作成します。

```sql
CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO

CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

次の例では、一般公開されている New York データ セットを参照する Azure Data Lake Gen2 の外部データ ソースを作成します。

```sql
CREATE EXTERNAL DATA SOURCE YellowTaxi
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
```
---

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

Azure Blob Storage または Azure Data Lake Storage に格納される外部データを定義する外部ファイル形式オブジェクトを作成します。 外部ファイル形式の作成は、外部テーブルを作成するための前提条件です。 完全なドキュメントは[こちら](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)にあります。

外部ファイル形式を作成することで、外部テーブルによって参照されるデータの実際のレイアウトを指定します。

### <a name="syntax-for-create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT の構文

#### <a name="sql-pool"></a>[SQL プール](#tab/sql-pool)

```syntaxsql
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' ]
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
}
```

#### <a name="serverless-sql-pool"></a>[サーバーレス SQL プール](#tab/sql-on-demand)

```syntaxsql
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' ]
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
    | PARSER_VERSION = {'parser_version'}
}
```

---


### <a name="arguments-for-create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT の引数

file_format_name: 外部ファイル形式の名前を指定します。

FORMAT_TYPE = [ PARQUET | DELIMITEDTEXT]: 外部データの形式を指定します。

- PARQUET: Parquet 形式を指定します。
- DELIMITEDTEXT: フィールド ターミネータとも呼ばれる、列区切り記号付きのテキスト形式を指定します。

FIELD_TERMINATOR = *field_terminator*: 区切りテキスト ファイルにのみ適用されます。 フィールド ターミネータは、テキスト区切りファイルでの各フィールド (列) の終了を示す 1 つ以上の文字を指定します。 既定値はパイプ文字 (ꞌ|ꞌ) です。

例 :

- FIELD_TERMINATOR = '|'
- FIELD_TERMINATOR = ' '
- FIELD_TERMINATOR = ꞌ\tꞌ

STRING_DELIMITER = *string_delimiter*: テキスト区切りのファイル内の文字列型のデータにはフィールド ターミネータを指定します。 文字列の区切り記号の長さは 1 文字または複数文字とし、単一引用符で囲みます。 既定値は空の文字列 ("") です。

例 :

- STRING_DELIMITER = '"'
- STRING_DELIMITER = '*'
- STRING_DELIMITER = ꞌ,ꞌ

FIRST_ROW = *First_row_int*: 最初に読み取られる行の番号を指定し、すべてのファイルに適用します。 値を 2 に設定すると、データの読み込み時に各ファイルの最初の行 (ヘッダー行) がスキップされます。 行は、行ターミネータ (/r/n、/r、/n) の存在に基づいてスキップされます。

USE_TYPE_DEFAULT = { TRUE | **FALSE** }: テキスト ファイルからデータを取得するときに、区切りテキスト ファイル内の不足値を処理する方法を指定します。

TRUE: テキスト ファイルからデータを取得するときは、外部テーブルの定義内の対応する列に既定値のデータ型を使用して、各不足値を格納します。 たとえば、不足値を次の値に置き換えます。

- 列が numeric 型の列として定義されている場合は 0 decimal 型の列はサポートされておらず、エラーになります。
- 列が string 型の列である場合は空の文字列 ("") です。
- 列が date 列の場合は 1900-01-01 です。

FALSE: すべての不足値を NULL として格納します。 区切りテキスト ファイルで単語 NULL を使って格納されている NULL 値は、文字列 "NULL" としてインポートされます。

Encoding = {'UTF8' | 'UTF16'}: サーバーレス SQL プールでは、UTF8 および UTF16 でエンコードされた区切りテキスト ファイルを読み取ることができます。

DATA_COMPRESSION = *data_compression_method*: この引数では、外部データのデータ圧縮方法を指定します。 

PARQUET ファイル形式の種類では、次の圧縮方法がサポートされています。

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

PARQUET 外部テーブルから読み取る場合に、この引数は無視されます。しかし、[CETAS](develop-tables-cetas.md) を使用して外部テーブルに書き込む場合は使用されます。

DELIMITEDTEXT ファイル形式の種類では、次の圧縮方法がサポートされています。

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'

PARSER_VERSION = 'parser_version': ファイルの読み取り時に使用するパーサーのバージョンを指定します。 詳細については、[OPENROWSET 引数](develop-openrowset.md#arguments)に関するページの PARSER_VERSION 引数を確認してください。

### <a name="example-for-create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT の例

次の例では、国勢調査ファイルの外部ファイル形式を作成します。

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>CREATE EXTERNAL TABLE

CREATE EXTERNAL TABLE コマンドでは、Azure Blob Storage または Azure Data Lake Storage 内に格納されたデータにアクセスするために Synapse SQL の外部テーブルを作成します。 

### <a name="syntax-for-create-external-table"></a>CREATE EXTERNAL TABLE の構文

```sql
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( <column_definition> [ ,...n ] )  
    WITH (
        LOCATION = 'folder_or_filepath',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name
    )  
[;]  

<column_definition> ::=
column_name <data_type>
    [ COLLATE collation_name ]
```

### <a name="arguments-create-external-table"></a>CREATE EXTERNAL TABLE の引数

*{ database_name.schema_name.table_name | schema_name.table_name | table_name }*

作成するテーブルの 1 つから 3 つの部分で構成される名前。 外部テーブルの場合、サーバーレス SQL プールではテーブルのメタデータのみが格納されます。 サーバーレス SQL プールでは、実際のデータの移動または格納は行われません。

<column_definition>, ...*n* ]

CREATE EXTERNAL TABLE では、列名、データ型、照合順序を構成できます。 外部テーブルに対して DEFAULT CONSTRAINT を使用することはできません。

>[!IMPORTANT]
>データ型と列の数を含む列の定義は、外部ファイルのデータと一致している必要があります。 不一致がある場合、実際のデータに対してクエリを実行するときに、ファイルの行が拒否されます。

Parquet ファイルからの読み取りの場合は、読み取りたい列だけを指定して、残りをスキップすることができます。

LOCATION = '*folder_or_filepath*'

Azure Blob Storage にある実際のデータのフォルダーまたはファイル パスとファイル名を指定します。 ルート フォルダーから、場所を開始します。 ルート フォルダーは、外部データ ソースで指定されたデータの場所です。

フォルダーの LOCATION を指定すると、サーバーレス SQL プールのクエリで外部テーブルから選択が行われ、そのフォルダーからファイルが取得されます。

> [!NOTE]
> Hadoop や PolyBase とは異なり、サーバーレス SQL プールではサブフォルダーは返されません。 ファイル名が下線 (_) またはピリオド (.) で始まるファイルが返されます。

この例では、LOCATION='/webdata/' である場合、サーバーレス SQL プールのクエリで mydata.txt と _hidden.txt から行が返されます。 mydata2.txt と mydata3.txt はサブフォルダー内にあるため、これらは返されません。

![外部テーブルの再帰型データ](./media/develop-tables-external-tables/folder-traversal.png)

DATA_SOURCE = *external_data_source_name*: 外部データの場所が含まれている外部データ ソースの名前を指定します。 外部データ ソースを作成するには、[CREATE EXTERNAL DATA SOURCE](#create-external-data-source) を使用します。

FILE_FORMAT = *external_file_format_name*: 外部データのファイルの種類と圧縮方法を格納する外部ファイル形式のオブジェクトの名前を指定します。 外部ファイル形式を作成するには、[CREATE EXTERNAL FILE FORMAT](#create-external-file-format) を使用します。

### <a name="permissions-create-external-table"></a>CREATE EXTERNAL TABLE のアクセス許可

外部テーブルから選択を行うには、適切な資格情報のほか、リストと読み取りのアクセス許可が必要です。

### <a name="example-create-external-table"></a>CREATE EXTERNAL TABLE の例

次の例では、外部テーブルを作成します。 最初の行が返されます。

```sql
CREATE EXTERNAL TABLE census_external_table
(
    decennialTime varchar(20),
    stateName varchar(100),
    countyName varchar(100),
    population int,
    race varchar(50),
    sex    varchar(10),
    minAge int,
    maxAge int
)  
WITH (
    LOCATION = '/parquet/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)
GO

SELECT TOP 1 * FROM census_external_table
```

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>Azure Data Lake 内のファイルから外部テーブルを作成してクエリを実行する

Data Lake の探索機能を使用することで、ファイルを右クリックするだけで、専用 SQL プールまたはサーバーレス SQL プールを使って外部テーブルを作成してクエリを実行できるようになりました。 ADLS Gen2 ストレージ アカウントから外部テーブルを作成するワンクリック ジェスチャーは、Parquet ファイルでのみサポートされます。 

### <a name="prerequisites"></a>前提条件

- 少なくとも ADLS Gen2 アカウントに対するストレージ BLOB データ共同作成者 ARM アクセス ロールがある状態で、ワークスペースにアクセスできる必要があります

- SQL プールまたは SQL オンデマンドで外部テーブルを[作成してクエリを実行するためのアクセス許可](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#permissions-2&preserve-view=true)が少なくとも必要です

[データ] パネルで、外部テーブルの作成元にするファイルを選択します。
> [!div class="mx-imgBorder"]
>![externaltable1](./media/develop-tables-external-tables/external-table-1.png)

ダイアログ ウィンドウが開きます。 専用 SQL プールかサーバーレス SQL プールを選択し、テーブルに名前を付けて [スクリプトを開く] を選択します。

> [!div class="mx-imgBorder"]
>![externaltable2](./media/develop-tables-external-tables/external-table-2.png)

SQL スクリプトは、ファイルからのスキーマの推論により自動的に生成されます。
> [!div class="mx-imgBorder"]
>![externaltable3](./media/develop-tables-external-tables/external-table-3.png)

スクリプトを実行します。 このスクリプトでは、Select Top 100 *. が自動的に実行されます。
> [!div class="mx-imgBorder"]
>![externaltable4](./media/develop-tables-external-tables/external-table-4.png)

外部テーブルが作成されました。今後この外部テーブルの内容を探索するために、ユーザーは [データ] ペインから直接クエリを実行できます。
> [!div class="mx-imgBorder"]
>![externaltable5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>次のステップ

クエリの結果を Azure Storage の外部テーブルに保存する方法については、[CETAS](develop-tables-cetas.md) に関する記事をご覧ください。 また、[Azure Synapse 外部テーブルの Apache Spark](develop-storage-files-spark-tables.md) に対するクエリを開始することもできます。
