---
title: SQL オンデマンド (プレビュー) を使用して Parquet の入れ子にされた型に対するクエリを実行する
description: この記事では、入れ子にされた Parquet 型に対してクエリを実行する方法について説明します。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: fb56c4da77ddeb87ebc3724a3b138994e4da98e7
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87489692"
---
# <a name="query-nested-types-in-parquet-and-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics の SQL オンデマンド (プレビュー) を使用して Parquet および JSON ファイルで入れ子にされた型に対してクエリを実行する

この記事では、Azure Synapse Analytics の SQL オンデマンド (プレビュー) を使用してクエリを作成する方法について説明します。 このクエリでは、Parquet の入れ子にされた型が読み取られます。
入れ子になった型は、オブジェクトまたは配列を表す複雑な構造体です。 入れ子になった型は、次のように格納できます。 
- 配列とオブジェクトを含む複数の複合列を持つことができる [PARQUET](query-parquet-files.md)。
- 複雑な JSON ドキュメントを 1 つの列として読み取ることができる階層型の [JSON ファイル](query-json-files.md)。
- すべてのドキュメントに複雑な入れ子になったプロパティを含めることができる CosmosDB コレクション (現在はゲート付きパブリック プレビュー)。

Synapse SQL オンデマンドでは、入れ子になったすべての型が JSON オブジェクトおよび配列として書式設定されるので、[JSON 関数を使用して複雑なオブジェクトを抽出または変更](https://docs.microsoft.com/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server)したり、[OPENJSON 関数を使用して JSON データを解析](https://docs.microsoft.com/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server)したりできます。 

次に、入れ子になったオブジェクトを含む JSON ファイル [COVID-19 Open Research Dataset](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) からスカラー値とオブジェクト値を抽出するクエリの例を示します。 

```sql
SELECT
    title = JSON_VALUE(doc, '$.metadata.title'),
    first_author = JSON_QUERY(doc, '$.metadata.authors[0]'),
    first_author_name = JSON_VALUE(doc, '$.metadata.authors[0].first'),
    complex_object = doc
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
        FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b'
    )
    WITH ( doc varchar(MAX) ) AS docs;
```

`JSON_VALUE` 関数は、指定されたパスのフィールドからスカラー値を返します。 `JSON_QUERY` 関数は、指定されたパスのフィールドから JSON として書式設定されたオブジェクトを返します。

> [!IMPORTANT]
> この例では [COVID-19 Open Research Dataset](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) のファイルを使用します。 このページにあるライセンスとデータの構造を参照してください。

## <a name="prerequisites"></a>前提条件

最初の手順では、参照するデータソースを使用して**データベースを作成**します。 次に、そのデータベースで[セットアップ スクリプト](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)を実行して、オブジェクトを初期化します。 このセットアップ スクリプトにより、データ ソース、データベース スコープの資格情報、これらのサンプルで使用される外部ファイル形式が作成されます。

## <a name="project-nested-or-repeated-data"></a>入れ子にされたデータまたは繰り返しのデータを射影する

PARQUET ファイルには、複合型を持つ複数の列を含めることができます。 これらの列の値は、JSON テキストとして書式設定され、VARCHAR 列として返されます。 次のクエリでは、*structExample.parquet* ファイルが読み取られ、入れ子にされた列の値を読み取る方法が示されます。 

```sql
SELECT
    DateStruct, TimeStruct, TimestampStruct, DecimalStruct, FloatStruct
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        DateStruct VARCHAR(8000),
        TimeStruct VARCHAR(8000),
        TimestampStruct VARCHAR(8000),
        DecimalStruct VARCHAR(8000),
        FloatStruct VARCHAR(8000)
    ) AS [r];
```

このクエリは、入れ子になったすべてのオブジェクトの内容が JSON テキストとして返される次の結果を返します。

| DateStruct    | TimeStruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Date":"2009-04-25"}| {"Time":"20:51:54.3598000"}|    {"Timestamp":"5501-04-08 12:13:57.4821000"}|    {"Decimal":11143412.25350}| {"Float":0.5}|
|{"Date":"1916-04-29"}| {"Time":"00:16:04.6778000"}|    {"Timestamp":"1990-06-30 20:50:52.6828000"}|    {"Decimal":1963545.62800}|  {"Float":-2.125}|

次のクエリでは、*justSimpleArray.parquet* ファイルが読み取られます。 入れ子にされたデータまたは繰り返しデータを含む Parquet ファイルからすべての列が射影されます。

```sql
SELECT
    SimpleArray
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

クエリでは、次の結果が返されます。

|SimpleArray|
| --- |
|[11,12,13]|
|[21,22,23]|

## <a name="read-properties-from-nested-object-columns"></a>入れ子になったオブジェクト列からプロパティを読み取る

`JSON_VALUE` 関数を使用すると、JSON テキストとして書式設定された列から値を返すことができます。

```sql
SELECT
    title = JSON_VALUE(complex_column, '$.metadata.title'),
    first_author_name = JSON_VALUE(complex_column, '$.metadata.authors[0].first'),
    body_text = JSON_VALUE(complex_column, '$.body_text.text'),
    complex_column
FROM
    OPENROWSET( BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
                FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b' ) WITH ( complex_column varchar(MAX) ) AS docs;
```

結果を次の表に示します。

|title  | first_author_name | body_text | complex_column |
| --- | --- | --- | --- |
| Supplementary Information An eco-epidemiolo... | Julien   | - Figure S1 :Phylogeny of... | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

多くの場合に複雑な JSON オブジェクトを含む単一の列を返す JSON ファイルとは異なり、 PARQUET ファイルには複数の複合列が存在する場合があります。 各列で `JSON_VALUE` 関数を使用して、入れ子になった列のプロパティを読み取ることができます。 `OPENROWSET` では、`WITH` 句で入れ子になったプロパティのパスを直接指定できます。 パスを列の名前として設定するか、列の型の後に [JSON パス式](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server) を追加することができます。

次のクエリでは、*structExample.parquet* ファイルが読み取られ、入れ子にされた列の要素の表示方法が示されます。 入れ子にされた値を参照するには、2 つの方法があります。
- 型の指定の後に、入れ子にされた値のパス式を指定する
- "." を使用して、列名を入れ子にされたパスとして書式設定し、フィールドを参照する

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>繰り返される列から要素にアクセスする

次のクエリでは、*justSimpleArray.parquet* ファイルが読み取られ、さらに [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を使用して、配列やマップなど、繰り返される列内から**スカラー**要素が取得されます。

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

結果を次の表に示します。

|SimpleArray    | FirstElement  | SecondElement | ThirdElement |
| --- | --- | --- | --- |
| [11,12,13] | 11   | 12 | 13 |
| [21,22,23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>複合列からサブオブジェクトにアクセスする

次のクエリでは、*mapExample.parquet* ファイルが読み取られ、さらに [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を使用して、配列やマップなど、繰り返される列内から**非スカラー**要素が取得されます。

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

返す列を `WITH` 句で明示的に参照することもできます。

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

構造 `MapOfPersons` は `VARCHAR` 列として返され、JSON 文字列としてフォーマットされます。

## <a name="project-values-from-repeated-columns"></a>繰り返される列から値を射影する

一部の列にスカラー値の配列 (`[1,2,3]` など) がある場合は、次のスクリプトを使用して、それらを簡単に展開し、メインの行と結合することができます。

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>次のステップ

次の記事では、[JSON ファイルに対してクエリを実行](query-json-files.md)する方法について説明します。
