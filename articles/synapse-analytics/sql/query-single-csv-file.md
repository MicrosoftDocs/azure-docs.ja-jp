---
title: サーバーレス SQL プールを使用して CSV ファイルに対してクエリを実行する
description: この記事では、サーバーレス SQL プールを使用して、さまざまなファイル形式の単一の CSV ファイルに対してクエリを実行する方法について説明します。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: f2f0cdf307e91fb40c55d4a98139bad1a5eca886
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462591"
---
# <a name="query-csv-files"></a>CSV ファイルに対してクエリを実行する

この記事では、Azure Synapse Analytics の サーバーレス SQL プールを使用して、単一の CSV ファイルに対してクエリを実行する方法について説明します。 CSV ファイルには、次のようなさまざまな形式があります。 

- ヘッダー行を含む、ヘッダー行を含まない
- コンマ区切りの値およびタブ区切りの値
- Windows スタイルおよび Unix スタイルの行の終わり
- 引用符で囲まれていない値、引用符で囲まれた値、エスケープ文字

上記のすべてのバリエーションについては、以下で説明します。

## <a name="quickstart-example"></a>クイック スタートの例

`OPENROWSET` 関数を使用すると、ファイルの URL を指定することによって、CSV ファイルの内容を読み取ることができます。

### <a name="read-a-csv-file"></a>csv ファイルの読み取り

`CSV` ファイルの内容を確認する最も簡単な方法は、`OPENROWSET` 関数にファイルの URL を指定し、csv `FORMAT` および 2.0 `PARSER_VERSION` を指定することです。 ファイルが一般公開されている場合、または Azure AD ID でこのファイルにアクセスできる場合は、次の例に示すようなクエリを使用して、ファイルの内容を表示することができます。

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    format = 'csv',
    parser_version = '2.0',
    firstrow = 2 ) as rows
```

オプション `firstrow` は、この場合のヘッダーを表す CSV ファイルの最初の行をスキップするために使用されます。 このファイルにアクセスできることを確認します。 ファイルが SAS キーまたはカスタム ID で保護されている場合は、[SQL ログインのためのサーバー レベルの資格情報](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)を設定する必要があります。

> [!IMPORTANT]
> CSV ファイルに UTF-8 文字が含まれている場合は、UTF-8 データベース照合順序 (`Latin1_General_100_CI_AS_SC_UTF8`など) を使用してください。
> ファイル内のテキスト エンコードと照合順序が一致しないと、予期しない変換エラーが発生する可能性があります。
> 現在のデータベースの既定の照合順序は、`alter database current collate Latin1_General_100_CI_AI_SC_UTF8` という T-SQL ステートメントを使用して容易に変更できます。

### <a name="data-source-usage"></a>データ ソースの使用状況

前の例では、ファイルへの完全なパスを使用しています。 別の方法として、ストレージのルート フォルダーを示す場所を持つ外部データ ソースを作成します。

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
```

データ ソースを作成したら、そのデータソースと `OPENROWSET` 関数のファイルへの相対パスを使用できます。

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) as rows
```

データ ソースが SAS キーまたはカスタム ID で保護されている場合は、[データベーススコープ資格情報を使用してデータ ソース](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)を構成できます。

### <a name="explicitly-specify-schema"></a>スキーマを明示的に指定する

`OPENROWSET` を使用すると、`WITH` 句によってファイルから読み取る列を明示的に指定できます。

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) with (
        date_rep date 1,
        cases int 5,
        geo_id varchar(6) 8
    ) as rows
```

`WITH` 句のデータ型の後の数値は、CSV ファイル内の列インデックスを表します。

> [!IMPORTANT]
> CSV ファイルに UTF-8 文字が含まれている場合は、必ず `WITH` 句のすべての列に対して何らかの UTF-8 照合順序 (`Latin1_General_100_CI_AS_SC_UTF8` など) を明示的に指定してください。または、データベース レベルで何らかの UTF-8 照合順序を設定してください。
> ファイル内のテキスト エンコードと照合順序が一致しないと、予期しない変換エラーが発生する可能性があります。
> 現在のデータベースの既定の照合順序は、`alter database current collate Latin1_General_100_CI_AI_SC_UTF8` という T-SQL ステートメントを使用して容易に変更できます。
> `geo_id varchar(6) collate Latin1_General_100_CI_AI_SC_UTF8 8` という定義を使用して、列の型に照合順序を簡単に設定できます。

次のセクションでは、さまざまな種類の CSV ファイルに対してクエリを実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

最初の手順として、テーブルが作成される **データベースを作成** します。 次に、そのデータベースで[セットアップ スクリプト](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)を実行して、オブジェクトを初期化します。 このセットアップ スクリプトにより、データ ソース、データベース スコープの資格情報、これらのサンプルで使用される外部ファイル形式が作成されます。

## <a name="windows-style-new-line"></a>Windows スタイルの改行

次のクエリは、CSV ファイル (ヘッダー行がなく、Windows スタイルの改行およびコンマ区切りの列を含む) を読み取る方法を示しています。

ファイル プレビュー:

![ヘッダーがなく、Windows スタイルの改行を含む CSV ファイルの最初の 10 行。](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>Unix スタイルの改行

次のクエリは、CSV ファイル (ヘッダー行がなく、Unix スタイルの改行およびコンマ区切りの列を含む) を読み取る方法を示しています。 他の例と比較して、ファイルの場所が異なることに注意してください。

ファイル プレビュー:

![ヘッダー行がなく Unix スタイルの改行を含む CSV ファイルの最初の 10 行。](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>ヘッダー行

次のクエリは、CSV ファイル (ヘッダー行があり、Unix スタイルの改行およびコンマ区切りの列を含む) を読み取る方法を示しています。 他の例と比較して、ファイルの場所が異なることに注意してください。

ファイル プレビュー:

![ヘッダー行があり、Unix スタイルの改行を含む CSV ファイルの最初の 10 行。](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>カスタム引用符文字

次のクエリは、ファイル (ヘッダー行があり、Unix スタイルの改行、コンマ区切りの列、および引用符で囲まれた値を含む) を読み取る方法を示しています。 他の例と比較して、ファイルの場所が異なることに注意してください。

ファイル プレビュー:

![ヘッダー行があり、Unix スタイルの改行と引用符で囲まれた値を含む CSV ファイルの最初の 10 行。](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> FIELDQUOTE の既定値は二重引用符であるため、このクエリでは、FIELDQUOTE パラメーターを省略した場合と同じ結果が返されます。

## <a name="escape-characters"></a>エスケープ文字

次のクエリは、ファイル (ヘッダー行があり、Unix スタイルの改行、コンマ区切りの列、および値内のフィールド区切り記号 (コンマ) に使用されるエスケープ文字を含む) を読み取る方法を示しています。 他の例と比較して、ファイルの場所が異なることに注意してください。

ファイル プレビュー:

![ヘッダー行があり、Unix スタイルの改行と、フィールド区切り記号に使用されるエスケープ文字を含む CSV ファイルの最初の 10 行。](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> ESCAPECHAR が指定されていない場合、このクエリは失敗します。"Slov,enia" 内のコンマが、国/地域名の一部ではなく、フィールド区切り記号として扱われることがその理由です。 "Slov,enia" は 2 つの列として扱われます。 そのため、特定の行には、他の行よりも列が 1 つ多く存在し、WITH 句で定義した列数より 1 つ多くなります。

### <a name="escape-quoting-characters"></a>引用符文字のエスケープ

次のクエリは、ファイル (ヘッダー行があり、Unix スタイルの改行、コンマ区切りの列、および値内のエスケープされた二重引用符文字を含む) を読み取る方法を示しています。 他の例と比較して、ファイルの場所が異なることに注意してください。

ファイル プレビュー:

![次のクエリは、ファイル (ヘッダー行があり、Unix スタイルの改行、コンマ区切りの列、および値内のエスケープされた二重引用符文字を含む) を読み取る方法を示しています。](./media/query-single-csv-file/population-unix-hdr-escape-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> 引用文字は、別の引用文字でエスケープする必要があります。 引用文字は、値が引用文字で囲まれている場合にのみ、列の値の中で使用できます。

## <a name="tab-delimited-files"></a>タブ区切りファイル

次のクエリは、ファイル (ヘッダー行があり、Unix スタイルの改行およびタブ区切りの列を含む) を読み取る方法を示しています。 他の例と比較して、ファイルの場所が異なることに注意してください。

ファイル プレビュー:

![ヘッダー行があり、Unix スタイルの改行およびタブ区切り記号を含む CSV ファイルの最初の 10 行。](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="return-a-subset-of-columns"></a>列のサブセットの選択

ここまで、WITH を使用し、すべての列をリストして CSV ファイル スキーマを指定してきました。 クエリにおいて実際に必要な列だけを指定するには、必要な各列に対して序数を使用します。 また、関心のない列も省略します。

次のクエリでは、ファイル内の異なる国/地域名の数が返されます。これにより必要な列のみを指定できます。

> [!NOTE]
> 次のクエリ内の WITH 句を見てください。 *[country_name]* 列を定義した行の末尾に "2" (引用符はなし) があることがわかります。 これは、 *[country_name]* 列がファイル内の 2 番目の列であることを意味します。 このクエリでは、2 番目の列を除く、ファイル内のすべての列が無視されます。

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5),
    [country_name] VARCHAR (100) 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>次のステップ

次の記事では、次の方法について説明します。

- [Parquet ファイルに対するクエリ](query-parquet-files.md)
- [フォルダーと複数のファイルに対するクエリ](query-folders-multiple-csv-files.md)
