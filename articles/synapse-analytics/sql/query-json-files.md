---
title: サーバーレス SQL プールを使用して JSON ファイルに対してクエリを実行する
description: このセクションでは、Azure Synapse Analytics の サーバーレス SQL プールを使用して JSON ファイルを読み取る方法について説明します。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 5fcf688bbe8a5be2fc10b70950990b7b6ca71df8
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225593"
---
# <a name="query-json-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics でサーバーレス SQL プールを使用して JSON ファイルのクエリを実行する

この記事では、Azure Synapse Analytics のサーバーレス SQL プールを使用してクエリを作成する方法について説明します。 このクエリの目的は、[OPENROWSET](develop-openrowset.md) を使用して JSON ファイルを読み取ることです。 
- 複数の JSON ドキュメントが JSON 配列として格納されている標準の JSON ファイル。
- JSON ドキュメントが改行文字で区切られている行区切りの JSON ファイル。 これらの種類のファイルの一般的な拡張機能には、`jsonl`、`ldjson`、および `ndjson` があります。

## <a name="read-json-documents"></a>JSON ドキュメントの読み取り

JSON ファイルの内容を確認する最も簡単な方法は、`OPENROWSET` 関数にそのファイルの URL を指定し、csv `FORMAT` を指定し、`fieldterminator` と `fieldquote` に `0x0b` 値を設定することです。 行区切りの JSON ファイルを読み取る必要がある場合は、これで十分です。 従来の JSON ファイルがある場合は、`rowterminator` に `0x0b` 値を設定する必要があります。 `OPENROWSET` 関数は JSON を解析し、次の形式ですべてのドキュメントを返します。

| ドキュメント |
| --- |
|{"date_rep":"2020-07-24","day":24,"month":7,"year":2020,"cases":3,"deaths":0,"geo_id":"AF"}|
|{"date_rep":"2020-07-25","day":25,"month":7,"year":2020,"cases":7,"deaths":0,"geo_id":"AF"}|
|{"date_rep":"2020-07-26","day":26,"month":7,"year":2020,"cases":4,"deaths":0,"geo_id":"AF"}|
|{"date_rep":"2020-07-27","day":27,"month":7,"year":2020,"cases":8,"deaths":0,"geo_id":"AF"}|

ファイルが一般公開されている場合、または Azure AD ID でこのファイルにアクセスできる場合は、次の例に示すようなクエリを使用して、ファイルの内容を表示することができます。

### <a name="read-json-files"></a>JSON ファイルの読み取り

次のサンプル クエリは、JSON および行区切りの JSON ファイルを読み取り、すべてのドキュメントを個別の行として返します。

```sql
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

前のサンプル クエリの JSON ドキュメントには、オブジェクトの配列が含まれています。 このクエリでは、各オブジェクトが個別の行として結果セットに返されます。 このファイルにアクセスできることを確認します。 ファイルが SAS キーまたはカスタム ID で保護されている場合は、[SQL ログインのためのサーバー レベルの資格情報](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)を設定する必要があります。 

### <a name="data-source-usage"></a>データ ソースの使用状況

前の例では、ファイルへの完全なパスを使用しています。 別の方法として、ストレージのルート フォルダーを示す場所を持つ外部データ ソースを作成し、そのデータ ソースとファイルへの相対パスを `OPENROWSET` 関数で使用することができます。

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.json',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

データ ソースが SAS キーまたはカスタム ID で保護されている場合は、[データベース スコープ資格情報を使用してデータ ソース](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)を構成できます。

次のセクションでは、さまざまな種類の JSON ファイルに対してクエリを実行する方法について説明します。

## <a name="parse-json-documents"></a>JSON ドキュメントの解析

前の例のクエリでは、結果セットの行ごとに 1 つの文字列としてすべての JSON ドキュメントが返されます。 次の例に示すように、関数 `JSON_VALUE` と `OPENJSON` を使用して JSON ドキュメント内の値を解析し、それらをリレーショナル値として返すことができます。

| date\_rep | cases | geo\_id |
| --- | --- | --- |
| 2020-07-24 | 3 | AF |
| 2020-07-25 | 7 | AF |
| 2020-07-26 | 4 | AF |
| 2020-07-27 | 8| AF |

### <a name="sample-json-document"></a>サンプル JSON ドキュメント

クエリの例では、次の構造を持つドキュメントを含む *json* ファイルを読み取ります。

```json
{
    "date_rep":"2020-07-24",
    "day":24,"month":7,"year":2020,
    "cases":13,"deaths":0,
    "countries_and_territories":"Afghanistan",
    "geo_id":"AF",
    "country_territory_code":"AFG",
    "continent_exp":"Asia",
    "load_date":"2020-07-25 00:05:14",
    "iso_country":"AF"
}
```

> [!NOTE]
> これらのドキュメントが行区切りの JSON として格納されている場合は、0x0b に `FIELDTERMINATOR` と `FIELDQUOTE` を を設定する必要があります。 標準の JSON 形式を使用している場合は、`ROWTERMINATOR` を0x0b に設定する必要があります。

### <a name="query-json-files-using-json_value"></a>JSON_VALUE を使用して JSON ファイルに対してクエリを実行する

以下のクエリは、[JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) を使用して、JSON ドキュメントからスカラー値 (`date_rep`、`countries_and_territories`、`cases`) を取得する方法を示しています。

```sql
select
    JSON_VALUE(doc, '$.date_rep') AS date_reported,
    JSON_VALUE(doc, '$.countries_and_territories') AS country,
    CAST(JSON_VALUE(doc, '$.deaths') AS INT) as fatal,
    JSON_VALUE(doc, '$.cases') as cases,
    doc
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
order by JSON_VALUE(doc, '$.geo_id') desc
```

JSON ドキュメントから JSON プロパティを抽出したら、列の別名を定義し、必要に応じてテキスト値を何らかの型にキャストすることができます。

### <a name="query-json-files-using-openjson"></a>OPENJSON を使用して JSON ファイルに対してクエリを実行する

次のクエリでは [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?view=azure-sqldw-latest&preserve-view=true) を使用します。 これは、セルビアで報告された COVID の統計情報を取得します。

```sql
select
    *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
    cross apply openjson (doc)
        with (  date_rep datetime2,
                cases int,
                fatal int '$.deaths',
                country varchar(100) '$.countries_and_territories')
where country = 'Serbia'
order by country, date_rep desc;
```
結果は、`JSON_VALUE` 関数を使用して返された結果と機能的に同じです。 場合によっては、`OPENJSON` は `JSON_VALUE` に比べて利点があります。
- `WITH` 句では、すべてのプロパティに対して列の別名と型を明示的に設定できます。 `SELECT` リスト内のすべての列に `CAST` 関数を配置する必要はありません。
- 大量のプロパティを返す場合は、`OPENJSON` の方が処理が速い場合があります。 1 つまたは 2 つのプロパティだけを返す場合、`OPENJSON` 関数はオーバーヘッドとなる可能性があります。
- 各ドキュメントから配列を解析し、親の行と結合する必要がある場合は、`OPENJSON` 関数を使用する必要があります。

## <a name="next-steps"></a>次のステップ

このシリーズの次の記事では、次の方法について説明します。

- [フォルダーと複数のファイルに対するクエリ](query-folders-multiple-csv-files.md)
- [ビューの作成および使用](create-use-views.md)
