---
title: SQL オンデマンド (プレビュー) で OPENROWSET を使用する方法
description: この記事では、SQL オンデマンド (プレビュー) での OPENROWSET の構文と、引数の使用方法について説明します。
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: b7b8a0d98db1411a08afdb33fa272bb7e6d6313e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87280479"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>SQL オンデマンド (プレビュー) で OPENROWSET を使用する方法

`OPENROWSET(BULK...)` 関数を使用すると、Azure Storage 内のファイルにアクセスできます。 `OPENROWSET` 関数は、リモート データ ソース (ファイルなど) の内容を読み取って行のセットとして返します。 SQL オンデマンド (プレビュー) リソース内では、OPENROWSET 関数を呼び出し、BULK オプションを指定することによって、OPENROWSET BULK 行セット プロバイダーにアクセスします。  

`OPENROWSET` 関数は、テーブル名 `OPENROWSET` であるかのように、クエリの `FROM` 句で参照できます。 組み込みの BULK プロバイダーによる一括操作がサポートされ、ファイルのデータを行セットとして読み取り、返すことができます。

## <a name="data-source"></a>データ ソース

Synapse SQL の OPENROWSET 関数は、データ ソースからファイルの内容を読み取ります。 データ ソースは Azure ストレージ アカウントであり、`OPENROWSET` 関数で明示的に参照することも、読み取ろうとするファイルの URL から動的に推論することもできます。
`OPENROWSET` 関数には、必要に応じて、ファイルを含むデータ ソースを指定するための `DATA_SOURCE` パラメーターを含めることができます。
- `DATA_SOURCE` を指定しない `OPENROWSET` を使用すると、`BULK` オプションに指定された URL の場所からファイルの内容を直接読み取ることができます。

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://<storage account>.dfs.core.windows.net/container/folder/*.parquet',
                    FORMAT = 'PARQUET') AS file
    ```

これは、事前に構成しなくても、ファイルの内容をすばやく簡単に読み取ることができる方法です。 この方法では、基本認証オプションを使用してストレージにアクセスできます (Azure AD ログインでは Azure AD パススルー、および SQL ログインでは SAS トークン)。 

- `DATA_SOURCE` を指定した `OPENROWSET` を使用すると、指定したストレージ アカウントのファイルにアクセスできます。

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    FORMAT = 'PARQUET') AS file
    ```


    この方法では、データソースにストレージ アカウントの場所を構成し、ストレージへのアクセスに使用する認証方法を指定できます。 
    
    > [!IMPORTANT]
    > `DATA_SOURCE` を指定しない `OPENROWSET` では、ストレージ ファイルにすばやく簡単にアクセスできますが、認証オプションが限られます。 例として、Azure AD プリンシパルは、[Azure AD ID](develop-storage-files-storage-access-control.md?tabs=user-identity) を使用した場合にのみファイルにアクセスできるほか、公開されているファイルにアクセスすることができます。 より強力な認証オプションが必要な場合は、`DATA_SOURCE` オプションを使用して、ストレージへのアクセスに使用する資格情報を定義します。


## <a name="security"></a>セキュリティ

データベース ユーザーが `OPENROWSET` 関数を使用するには `ADMINISTER BULK OPERATIONS` 権限が必要です。

また、ストレージ管理者が、有効な SAS トークンを提供するか、 Azure AD プリンシパルでストレージ ファイルにアクセスできるようにして、ユーザーがファイルにアクセスできるようにする必要があります。 ストレージ アクセス制御の詳細については、[この記事](develop-storage-files-storage-access-control.md)を参照してください。

`OPENROWSET` は、次の規則を使用してストレージへの認証方法を決定します。
- `DATA_SOURCE` を指定しない `OPENROWSET` では、認証メカニズムは呼び出し元の種類によって異なります。
  - すべてのユーザーは、`DATA_SOURCE` なしで `OPENROWSET` を使用して、Azure Storage で公開されているファイルを読み取ることができます。
  - Azure Storage で Azure AD ユーザーによる基になるファイルへのアクセスが許可されている場合 (たとえば、呼び出し元が Azure Storage に対する `Storage Reader` アクセス許可を持っている場合)、Azure AD ログインは、独自の [Azure AD ID](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) を使用して保護されたファイルにアクセスできます。
  - SQL ログインは、`DATA_SOURCE` を指定しない `OPENROWSET` を使用して、一般公開されたファイル、または SAS トークンや Synapse ワークスペースのマネージド ID を使用して保護されたファイルにアクセスすることもできます。 ストレージのファイルへのアクセスを許可するには、[サーバースコープ資格情報を作成](develop-storage-files-storage-access-control.md#examples)する必要があります。 
- `DATA_SOURCE` を指定した `OPENROWSET` では、参照先のデータ ソースに割り当てられるデータベース スコープ資格情報に認証メカニズムが定義されます。 この方法では、一般公開されているストレージへのアクセスや、SAS トークン、ワークスペースのマネージド ID、または[呼び出し元の Azure AD ID](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) (呼び出し元が Azure AD プリンシパルの場合) を使用しているストレージへのアクセスが行えます。 公開されていない Azure Storage を `DATA_SOURCE` が参照している場合は、[データベース スコープ資格情報を作成](develop-storage-files-storage-access-control.md#examples)して、それを `DATA SOURCE` で参照し、ストレージ ファイルへのアクセスを許可する必要があります。

呼び出し元が、ストレージへの認証に資格情報を使用するには資格情報に対する `REFERENCES` 権限が必要です。

## <a name="syntax"></a>構文

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ]
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ] 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
```

## <a name="arguments"></a>引数

クエリの対象データを含む入力ファイルには、2 つの選択肢があります。 有効な値は次のとおりです。

- 'CSV' - 行と列の区切り文字を含む区切りテキスト ファイルが含まれます。 任意の文字をフィールド区切り文字として使用できます。例: TSV:FIELDTERMINATOR = tab。

- 'PARQUET' - Parquet 形式のバイナリ ファイル 

**'unstructured_data_path'**

データへのパスを確立する unstructured_data_path には、絶対パスまたは相対パスを指定できます。
- '\<prefix>://\<storage_account_path>/\<storage_path>' という形式の絶対パスを使用すると、ユーザーがファイルを直接読み取ることができるようになります。
- '<storage_path>' という形式の相対パスは、`DATA_SOURCE` パラメーターと一緒に使用する必要があり、`EXTERNAL DATA SOURCE` に定義された<storage_account_path> の場所にあるファイル パターンを指定します。 

 以下に、特定の外部データ ソースにリンクする、関連する <storage account path> 値を示します。 

| 外部データ ソース       | Prefix | ストレージ アカウント パス                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Storage         | http[s]  | \<storage_account>.blob.core.windows.net/path/file   |
| Azure Blob Storage         | wasb[s]  | \<container>@\<storage_account>.blob.core.windows.net/path/file |
| Azure Data Lake Store Gen1 | http[s]  | \<storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | http[s]  | \<storage_account>.dfs.core.windows.net /path/file   |
| Azure Data Lake Store Gen2 | abfs[s]  | [\<file_system>@\<account_name>.dfs.core.windows.net/path/file](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md#uri-syntax)              |
||||

'\<storage_path>'

 読み取り対象のフォルダーまたはファイルを指す、ストレージ内のパスを指定します。 パスがコンテナーまたはフォルダーを指している場合は、その特定のコンテナーまたはフォルダーからすべてのファイルが読み取られます。 サブフォルダー内のファイルは含まれません。 

 ワイルドカードを使用して、複数のファイルまたはフォルダーを対象にすることができます。 連続しない複数のワイルドカードを使用できます。
次に示すのは、 */csv/population* で始まるすべてのフォルダーから、*population* で始まるすべての *csv* ファイルを読み取る例です。  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

unstructured_data_path でフォルダーを指定すると、SQL オンデマンド クエリはそのフォルダーからファイルを取得します。 

> [!NOTE]
> Hadoop や PolyBase とは異なり、SQL オンデマンドではサブフォルダーは返されません。 また、Hadoop や PolyBase とは異なり、SQL オンデマンドでは、ファイル名が下線 (_) やピリオド (.) で始まるファイルが返されます。

次の例で、unstructured_data_path=`https://mystorageaccount.dfs.core.windows.net/webdata/` の場合、SQL オンデマンド クエリでは、mydata.txt と _hidden.txt からの行が返されます。 mydata2.txt と mydata3.txt はサブフォルダー内にあるため、これらは返されません。

![外部テーブルの再帰型データ](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

WITH 句を使用すると、ファイルから読み取る列を指定できます。

- CSV データ ファイルの場合、すべての列を読み取るには、列名とそのデータ型を指定します。 列のサブセットが必要な場合は、序数を使用して、元のデータ ファイルから序数で列を選択します。 列は、序数の指定によってバインドされます。 

    > [!IMPORTANT]
    > CSV ファイルの場合、WITH 句は必須です。
    >
    
- Parquet データ ファイルの場合は、元のデータ ファイル内の列名と一致する列名を指定します。 列は名前によってバインドされます。 WITH 句を省略すると、Parquet ファイルのすべての列が返されます。

column_name = 出力列の名前。 指定した場合、この名前はソース ファイル内の列名をオーバーライドします。

column_type = 出力列のデータ型。 ここでは、暗黙的なデータ型の変換が行われます。

column_ordinal = ソース ファイル内の列の序数。 Parquet ファイルの場合、バインドは名前で行われるため、この引数は無視されます。 次の例では、CSV ファイルからのみ 2 列目が返されます。

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

**\<bulk_options>**

FIELDTERMINATOR ='field_terminator'

使用するフィールド ターミネータを指定します。 既定のフィールド ターミネータはコンマ (" **,** ") です。

ROWTERMINATOR ='row_terminator'`

使用する行ターミネータを指定します。 行ターミネータが指定されていない場合は、既定のターミネータの 1 つが使用されます。 PARSER_VERSION = '1.0' の既定のターミネータは、\r\n、\n、および \r です。 PARSER_VERSION = '2.0' の既定のターミネータは、\r\n および \n です。

ESCAPE_CHAR = 'char'

ファイル内でそれ自体とすべての区切り記号の値をエスケープするために使用するファイル内の文字を指定します。 エスケープ文字の後にそれ自体以外の値、またはいずれかの区切り記号の値が続く場合は、その値を読み取るときにエスケープ文字が削除されます。 

ESCAPE_CHAR パラメーターは、FIELDQUOTE が有効かどうかに関係なく適用されます。 引用文字をエスケープするために使用されることはありません。 引用文字は、別の引用文字でエスケープする必要があります。 引用文字は、値が引用文字で囲まれている場合にのみ、列の値の中で使用できます。

FIRSTROW = 'first_row' 

読み込み開始行の行番号を指定します。 既定値は 1 です。 指定したデータ ファイルの最初の行を示します。 行番号は行ターミネータの数をカウントして決定されます。 FIRSTROW は 1 から始まります。

FIELDQUOTE = 'field_quote' 

CSV ファイルで引用符文字として使用される文字を指定します。 指定しない場合、引用文字 (") が使用されます。 

DATA_COMPRESSION = 'data_compression_method'

圧縮方法を指定します。 次の圧縮方法がサポートされています。

- org.apache.hadoop.io.compress.GzipCodec

PARSER_VERSION = 'parser_version'

ファイルの読み取り時に使用するパーサーのバージョンを指定します。 現在サポートされている CSV パーサーのバージョンは 1.0 および 2.0 です。

- PARSER_VERSION = '1.0'
- PARSER_VERSION = '2.0'

CSV パーサー バージョン 1.0 は既定で機能が豊富ですが、2.0 はパフォーマンスのために構築されており、すべてのオプションとエンコードがサポートされているわけではありません。 

CSV パーサー バージョン 2.0 の詳細:

- すべてのデータ型がサポートされているわけではありません。
- 行の最大サイズの上限は 8 MB です。
- 次のオプションはサポートされていません。DATA_COMPRESSION
- 引用符で囲まれた空の文字列 ("") は、空の文字列として解釈されます。

## <a name="examples"></a>例

次の例では、population*.csv ファイルから序数 1 と 4 の 2 列だけが返されます。 ファイルにはヘッダー行がないため、最初の行から読み取りを開始します。

```sql
SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```

次の例では、列名とデータ型を指定せずに、census データ セットから最初の行のすべての列が Parquet 形式で返されます。 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

## <a name="next-steps"></a>次のステップ

その他のサンプルについては、[データ ストレージに対するクエリに関するクイックスタート](query-data-storage.md)を参照して、`OPENROWSET` を使用して [CSV](query-single-csv-file.md)、[PARQUET](query-parquet-files.md)、および [JSON](query-json-files.md) ファイル形式を読み取る方法について学習してください。 また、[CETAS](develop-tables-cetas.md) を使用してクエリの結果を Azure Storage に保存する方法も確認できます。
