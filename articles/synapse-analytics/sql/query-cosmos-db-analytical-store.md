---
title: Azure Synapse Link でサーバーレス SQL プールを使用して Azure Cosmos DB データのクエリを実行する
description: この記事では、Azure Synapse Link でサーバーレス SQL プールを使用して、Azure Cosmos DB のクエリを実行する方法について説明します。
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 03/02/2021
ms.author: jovanpop
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 10262b168b91370956c9559ba688c72213ba7618
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870995"
---
# <a name="query-azure-cosmos-db-data-with-a-serverless-sql-pool-in-azure-synapse-link"></a>Azure Synapse Link でサーバーレス SQL プールを使用して Azure Cosmos DB データのクエリを実行する

サーバーレス SQL プールを使用すると、トランザクション ワークロードのパフォーマンスに影響を与えることなく、[Azure Synapse Link](../../cosmos-db/synapse-link.md) で有効になっている Azure Cosmos DB コンテナー内のデータをほぼリアルタイムで分析できます。 T-SQL インターフェイスを使用して[分析ストア](../../cosmos-db/analytical-store-introduction.md)および統合された接続からさまざまなビジネス インテリジェンス (BI) やアドホック クエリ ツールへのデータのクエリを実行するために、使い慣れた T-SQL 構文が用意されています。

Azure Cosmos DB のクエリを実行する場合、[SELECT](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) のすべての機能が [OPENROWSET](develop-openrowset.md) 関数によってサポートされます。これには、[SQL の関数や演算子](overview-features.md)の大部分が含まれます。 また、Azure Blob Storage または Azure Data Lake Storage のデータと共に Azure Cosmos DB からデータを読み取るクエリの結果を、[create external table as select](develop-tables-cetas.md#cetas-in-serverless-sql-pool) (CETAS) を使用して格納することもできます。 現在は、CETAS を使用して、サーバーレス SQL プールのクエリの結果を Azure Cosmos DB に格納することはできません。

この記事では、Azure Synapse Link が有効になっている Azure Cosmos DB コンテナーのデータのクエリを実行する、サーバーレス SQL プールを使用するクエリの作成方法について説明します。 その後は、[このチュートリアル](./tutorial-data-analyst.md)で、Azure Cosmos DB コンテナー上にサーバーレス SQL プール ビューを構築し、それらを Power BI モデルに接続する方法について詳しく学ぶことができます。このチュートリアルには、[Azure Cosmos DB の定義済みスキーマ](../../cosmos-db/analytical-store-introduction.md#schema-representation)を使ったコンテナーが使用されています。

## <a name="overview"></a>概要

サーバーレス SQL プールを使用すると、`OPENROWSET` 関数を使用して Azure Cosmos DB 分析ストレージに対してクエリを実行できます。 
- `OPENROWSET` とインライン キー。 この構文を使用すると、資格情報を準備せずに Azure Cosmos DB コレクションに対してクエリを実行できます。
- Cosmos DB アカウント キーを含む資格情報を参照する `OPENROWSET`。 この構文を使用すると、Azure Cosmos DB コレクションに対してビューを作成できます。

### <a name="openrowset-with-key"></a>[OPENROWSET とキー](#tab/openrowset-key)

Azure Cosmos DB 分析ストア内のデータのクエリと分析をサポートするため、サーバーレス SQL プールでは次の `OPENROWSET` 構文を使用します。

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

Azure Cosmos DB の接続文字列には、Azure Cosmos DB のアカウント名、データベース名、データベース アカウント マスター キー、および `OPENROWSET` 関数に対するオプションのリージョン名を指定します。

接続文字列は次のような形式です。
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

`OPENROWSET` の構文では、引用符を使用しないで Azure Cosmos DB のコンテナー名を指定します。 コンテナー名に特殊文字 (ダッシュ "-" など) が含まれている場合、`OPENROWSET` 構文では名前を角かっこ (`[]`) で囲む必要があります。

### <a name="openrowset-with-credential"></a>[OPENROWSET と資格情報](#tab/openrowset-credential)

資格情報を参照する `OPENROWSET` 構文を使用できます。

```sql
OPENROWSET( 
       PROVIDER = 'CosmosDB',
       CONNECTION = '<Azure Cosmos DB connection string without account key>',
       OBJECT = '<Container name>',
       [ CREDENTIAL | SERVER_CREDENTIAL ] = '<credential name>'
    )  [ < with clause > ] AS alias
```

この場合、Azure Cosmos DB 接続文字列にはキーが含まれません。 接続文字列は次のような形式です。
```sql
'account=<database account name>;database=<database name>;region=<region name>'
```

データベース アカウントのマスター キーは、サーバー レベルの資格情報またはデータベース スコープの資格情報に配置されます。 

---

> [!IMPORTANT]
> Azure Cosmos DB 分析ストア内の文字列値は UTF-8 テキストとしてエンコードされているため、必ず何らかの UTF-8 データベース照合順序 (`Latin1_General_100_CI_AS_SC_UTF8` など) を使用してください。
> ファイル内のテキスト エンコードと照合順序が一致しないと、予期しないテキスト変換エラーが発生する可能性があります。
> 現在のデータベースの既定の照合順序は、`alter database current collate Latin1_General_100_CI_AI_SC_UTF8` という T-SQL ステートメントを使用して簡単に変更できます。

> [!NOTE]
> サーバーレス SQL プールでは、Azure Cosmos DB トランザクション ストアのクエリはサポートされていません。

## <a name="sample-dataset"></a>サンプル データセット

この記事の例は、「[European Centre for Disease Prevention and Control (ECDC) COVID-19 Cases](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/)」および「[COVID-19 Open Research Dataset (CORD-19) (doi:10.5281/zenodo.3715505)](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)」のデータに基づいています。

これらのページでは、ライセンスとデータの構造を確認できます。 [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) および [CORD-19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) データセットのサンプル データをダウンロードすることもできます。

サーバーレス SQL プールを使用して Azure Cosmos DB データのクエリを実行する方法を示すこの記事の内容をたどるには、以下のリソースを作成してください。

* [Azure Synapse Link が有効にされている](../../cosmos-db/configure-synapse-link.md) Azure Cosmos DB データベース アカウント。
* `covid` という名前の Azure Cosmos DB データベース。
* 前述のサンプル データセットが読み込まれた、`Ecdc` および `Cord19` という名前の 2 つの Azure Cosmos DB コンテナー。

テスト目的で次の接続文字列を使用できます: `Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==`。 この接続ではパフォーマンスが保証されないことに注意してください。これは、このアカウントが Synapse SQL エンドポイントと比較してリモート リージョンに配置される可能性があるためです。

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>自動スキーマ推論を使用して Azure Cosmos DB のデータを探索する

Azure Cosmos DB のデータを探索する最も簡単な方法は、自動スキーマ推論機能を使用することです。 `OPENROWSET` ステートメントで `WITH` 句を省略すると、Azure Cosmos DB コンテナーの分析ストアのスキーマを自動検出 (推論) するように、サーバーレス SQL プールに指示できます。

### <a name="openrowset-with-key"></a>[OPENROWSET とキー](#tab/openrowset-key)

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc) as documents
```

### <a name="openrowset-with-credential"></a>[OPENROWSET と資格情報](#tab/openrowset-credential)

```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

---

この例では、Azure Cosmos DB キー (この例ではダミー) を使用して認証された Azure Cosmos DB アカウント `MyCosmosDbAccount` の `covid` データベースに接続するように、サーバーレス SQL プールに指示しています。 その後、`West US 2` リージョンのコンテナー `Ecdc` の分析ストアにアクセスします。 特定のプロパティのプロジェクションはないため、`OPENROWSET` 関数からは、Azure Cosmos DB の項目のすべてのプロパティが返されます。

Azure Cosmos DB コンテナー内の項目に `date_rep`、`cases`、および `geo_id` プロパティがあると仮定すると、このクエリの結果は次の表のようになります。

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

同じ Azure Cosmos DB データベース内の他のコンテナーのデータを探索する必要がある場合は、同じ接続文字列を使用し、3 番目のパラメーターとして必要なコンテナーを参照することができます。

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>スキーマを明示的に指定する

`OPENROWSET` の自動スキーマ推論機能では、簡単で使いやすいクエリが提供されますが、ビジネス シナリオでは、Azure Cosmos DB データの関連する読み取り専用プロパティに対するスキーマの明示的指定が必要な場合があります。

`OPENROWSET` 関数を使用すると、コンテナー内のデータから読み取るプロパティを明示的に指定したり、データ型を指定したりすることができます。

次のような構造の [ECDC COVID データセット](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/)から、一部のデータを Azure Cosmos DB にインポートしたとします。

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Azure Cosmos DB のこのようなフラットな JSON ドキュメントは、Synapse SQL では行と列のセットとして表すことができます。 `OPENROWSET` 関数を使用すると、読み取り対象のプロパティのサブセットと列の厳密な型を、`WITH` 句で指定できます。

### <a name="openrowset-with-key"></a>[OPENROWSET とキー](#tab/openrowset-key)
```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```
### <a name="openrowset-with-credential"></a>[OPENROWSET と資格情報](#tab/openrowset-credential)
```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```
---
このクエリの結果は次の表のようになります。

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Azure Cosmos DB の値に使用する必要がある SQL 型の詳細については、記事の最後にある [SQL 型のマッピングの規則](#azure-cosmos-db-to-sql-type-mappings)を参照してください。

## <a name="create-view"></a>ビューを作成する

マスターまたは既定のデータベースにビューを作成することは推奨されず、サポートもされていません。 そのため、ビューのためのユーザー データベースを作成する必要があります。

スキーマを特定したら、Azure Cosmos DB データの上にビューを準備できます。 Azure Cosmos DB アカウント キーを別の資格情報に配置し、`OPENROWSET` 関数からこの資格情報を参照する必要があります。 ビュー定義でアカウント キーを保持しないでください。

```sql
CREATE CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
GO
CREATE OR ALTER VIEW Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

パフォーマンスに影響する可能性があるため、明示的に定義されたスキーマなしで `OPENROWSET` を使用しないでください。 列のサイズは可能な限り小さくしてください (たとえば、既定の VARCHAR(8000) ではなく VARCHAR(100))。 [UTF-8 の変換に関する問題](../troubleshoot/reading-utf8-text.md)を回避するために、何らかの UTF-8 照合順序を既定のデータベース照合順序として使用するか、明示的な列の照合順序として設定する必要があります。 照合順序 `Latin1_General_100_BIN2_UTF8` は、文字列型の列を使用してデータをフィルター処理する場合に最も高いパフォーマンスを提供します。

## <a name="query-nested-objects-and-arrays"></a>入れ子になったオブジェクトと配列のクエリ

Azure Cosmos DB を使用すると、入れ子になったオブジェクトまたは配列として構成することで、より複雑なデータ モデルを表すことができます。 Azure Cosmos DB に対する Azure Synapse Link の自動同期機能により、何もしなくても分析ストアでのスキーマ表現が管理されます。これには、サーバーレス SQL プールからの高度なクエリを可能にする、入れ子になったデータ型の処理が含まれます。

たとえば、[CORD-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) データ セットには、次の構造に従った JSON ドキュメントが含まれています。

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

Azure Cosmos DB 内の入れ子になったオブジェクトと配列は、`OPENROWSET` 関数で読み取ると、クエリ結果で JSON 文字列として表されます。 `WITH` 句を使用するときに、オブジェクト内の入れ子になった値へのパスを指定できます。

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19)
WITH (  paper_id    varchar(8000),
        title        varchar(1000) '$.metadata.title',
        metadata     varchar(max),
        authors      varchar(max) '$.metadata.authors'
) AS docs;
```

このクエリの結果は次の表のようになります。

| paper_id | title | metadata | 作成者 |
| --- | --- | --- |
| bb11206963e831f… | Supplementary Information An eco-epidemi… | `{"title":"Supplementary Informati…` | `[{"first":"Julien","last":"Mélade","suffix":"","af…`| 
| bb1206963e831f1… | The Use of Convalescent Sera in Immune-E… | `{"title":"The Use of Convalescent…` | `[{"first":"Antonio","last":"Lavazza","suffix":"", …` |
| bb378eca9aac649… | Tylosema esculentum (Marama) Tuber and B… | `{"title":"Tylosema esculentum (Ma…` | `[{"first":"Walter","last":"Chingwaru","suffix":"",…` | 

詳細については、[Azure Synapse Link での複雑なデータ型](../how-to-analyze-complex-schema.md)の分析に関するページと、[サーバーレス SQL プールでの入れ子構造](query-parquet-nested-types.md)の分析に関するページを参照してください。

> [!IMPORTANT]
> `Mélade` ではなく `MÃƒÂ©lade` のような予期しない文字がテキストで表示される場合は、データベースの照合順序が [UTF-8](/sql/relational-databases/collations/collation-and-unicode-support#utf8) の照合順序に設定されていません。
> `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` のような SQL ステートメントを使用して、UTF-8 照合順序に[データベースの照合順序を変更します](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation)。

## <a name="flatten-nested-arrays"></a>入れ子になった配列のフラット化

Azure Cosmos DB のデータには、[CORD-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) データ セットの authors 配列のように、入れ子になったサブ配列が存在する場合があります。

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

場合によっては、最上位の項目 (メタデータ) のプロパティを、配列 (authors) のすべての要素と "結合" することが必要になります。 サーバーレス SQL プールを使用すると、入れ子になった配列に対して `OPENJSON` 関数を適用することで、入れ子構造をフラット化できます。

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

このクエリの結果は次の表のようになります。

| title | 作成者 | first | last | affiliation |
| --- | --- | --- | --- | --- |
| Supplementary Information An eco-epidemi… |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Supplementary Information An eco-epidemi… | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4# |`{"laboratory":"","institution":"U…` | 
| Supplementary Information An eco-epidemi… |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Supplementary Information An eco-epidemi… |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> `Mélade` ではなく `MÃƒÂ©lade` のような予期しない文字がテキストで表示される場合は、データベースの照合順序が [UTF-8](/sql/relational-databases/collations/collation-and-unicode-support#utf8) の照合順序に設定されていません。 `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` のような SQL ステートメントを使用して、UTF-8 照合順序に[データベースの照合順序を変更します](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation)。

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Azure Cosmos DB から SQL 型へのマッピング

Azure Cosmos DB のトランザクション ストアはスキーマに依存しませんが、分析ストアは分析クエリのパフォーマンスを最適化するためにスキーマ化されています。 Azure Synapse Link の自動同期機能により、Azure Cosmos DB では何もしなくても分析ストアでのスキーマ表現が管理されます。これには、入れ子になったデータ型の処理が含まれます。 サーバーレス SQL プールでは分析ストアのクエリが実行されるため、Azure Cosmos DB の入力データ型を SQL データ型にマップする方法を理解することが重要です。

SQL (Core) API の Azure Cosmos DB アカウントでは、数値、文字列、ブール値、null、入れ子になったオブジェクトまたは配列の JSON プロパティの型がサポートされています。 `OPENROWSET` で `WITH` 句を使用する場合は、これらの JSON 型に一致する SQL 型を選択する必要があります。 次の表は、Azure Cosmos DB のさまざまなプロパティの型に対して使用する必要がある SQL 列の型を示しています。

| Azure Cosmos DB のプロパティの型 | SQL 列の型 |
| --- | --- |
| Boolean | bit |
| Integer | bigint |
| Decimal | float |
| String | varchar (UTF-8 データベース照合順序) |
| 日付と時刻 (ISO 形式の文字列) | varchar(30) |
| 日付と時刻 (UNIX タイムスタンプ) | bigint |
| [Null] | `any SQL type` 
| 入れ子になったオブジェクトまたは配列 | varchar(max) (UTF-8 データベース照合順序)、JSON テキストとしてシリアル化 |

## <a name="full-fidelity-schema"></a>完全に忠実なスキーマ

Azure Cosmos DB の完全に忠実なスキーマを使用すると、コンテナー内のすべてのプロパティについて、値と最も一致する型の両方を記録できます。 完全に忠実なスキーマを持つコンテナーで `OPENROWSET` 関数を使用すると、各セルに型と実際の値の両方が提供されます。 次のクエリでは、完全に忠実なスキーマでコンテナーから項目を読み取ると仮定します。

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) as rows
```

このクエリの結果は、JSON テキストとしてフォーマットされた型と値を返します。

| date_rep | cases | geo_id |
| --- | --- | --- |
| {"date":"2020-08-13"} | {"int32":"254"} | {"string":"RS"} |
| {"date":"2020-08-12"} | {"int32":"235"}| {"string":"RS"} |
| {"date":"2020-08-11"} | {"int32":"316"} | {"string":"RS"} |
| {"date":"2020-08-10"} | {"int32":"281"} | {"string":"RS"} |
| {"date":"2020-08-09"} | {"int32":"295"} | {"string":"RS"} |
| {"string":"2020/08/08"} | {"int32":"312"} | {"string":"RS"} |
| {"date":"2020-08-07"} | {"float64":"339.0"} | {"string":"RS"} |

すべての値について、Azure Cosmos DB コンテナーの項目で識別された型を確認できます。 `date_rep` プロパティの値のほとんどに `date` 値が含まれていますが、一部の値が Azure Cosmos DB に誤って文字列として格納されています。 完全に忠実なスキーマによって、正しく型指定された `date` 値と正しくフォーマットされていない `string` 値の両方が返されます。
cases の数値は `int32` 値として格納される情報ですが、小数として入力されている値が 1 つあります。 この値の型は `float64` です。 `int32` の最大値を超える値がある場合は `int64` 型として格納されます。 この例のすべての `geo_id` 値は `string` 型として格納されます。

> [!IMPORTANT]
> `WITH` 句が指定されていない `OPENROWSET` 関数では、予期される型を持つ値と、入力された型が正しくない値の両方が公開されます。 この関数は、レポート用ではなく、データの探索用に設計されています。 この関数から返された JSON 値を解析してレポートを作成しないでください。 レポートを作成するには、明示的な [WITH 句](#query-items-with-full-fidelity-schema) を使用します。 完全に忠実な分析ストアで補正を適用するためには、Azure Cosmos DB コンテナー内の型が正しくない値をクリーンアップする必要があります。

Mongo DB API の種類の Azure Cosmos DB アカウントに対してクエリを実行する必要がある場合は、分析ストア内の完全に忠実なスキーマ表現と、使用される拡張プロパティ名の詳細を、「[Azure Cosmos DB の分析ストアとは](../../cosmos-db/analytical-store-introduction.md#analytical-schema)」で確認してください。

### <a name="query-items-with-full-fidelity-schema"></a>完全に忠実なスキーマの項目に対してクエリを実行する

完全に忠実なスキーマにクエリを実行する際は、`WITH` 句で SQL 型と、想定される Azure Cosmos DB のプロパティ型を明示的に指定する必要があります。

次の例では、`string` が `geo_id` プロパティの正しい型であり、`int32` が `cases` プロパティの正しい型であると仮定します。

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

他の型を持つ `geo_id` および `cases` の値は `NULL` 値として返されます。 このクエリは、式 (`cases.int32`) で指定された型の `cases` のみを参照します。

Azure Cosmos DB コンテナーでクリーンアップできない他の型 (`cases.int64`、`cases.float64`) を持つ値がある場合は、`WITH` 句で明示的に参照し、その結果を結合する必要があります。 次のクエリでは、`cases` 列に格納されている `int32`、`int64`、および `float64` の両方を集約します。

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

この例では、cases の数値は `int32`、 `int64`、 `float64` のいずれかの値として格納されます。 国ごとの cases の数値を計算するには、すべての値を抽出する必要があります。

## <a name="known-issues"></a>既知の問題

- `OPENROWSET` 列の照合順序のエンコードが UTF-8 でない場合、サーバーレス SQL プールからコンパイル時警告が返されます。 現在のデータベースで実行されるすべての `OPENROWSET` 関数の既定の照合順序は、`alter database current collate Latin1_General_100_CI_AS_SC_UTF8` という T-SQL ステートメントを使用して簡単に変更できます。

次の表に、考えられるエラーとトラブルシューティングの操作を示します。

| エラー | 根本原因 |
| --- | --- |
| 構文エラー:<br/> - `Openrowset` 付近に不適切な構文があります。<br/> - `...` は、`BULK OPENROWSET` プロバイダー オプションとして認識されません。<br/> - `...` 付近に不適切な構文があります。 | 考えられる根本原因:<br/> - 最初のパラメーターとして CosmosDB を使用していません。<br/> - 3 番目のパラメーターで識別子の代わりに文字列リテラルを使用しています。<br/> - 3 番目のパラメーター (コンテナー名) が指定されていません。 |
| CosmosDB 接続文字列でエラーが発生しました。 | - アカウント、データベース、またはキーが指定されていません。 <br/> - 接続文字列に認識されないオプションがいくつかあります。<br/> - 接続文字列の末尾にセミコロン `;` が記述されています。 |
| CosmosDB パスを解決できませんでした。エラー: "アカウント名が正しくありません" または "データベース名が正しくありません"。 | 指定されたアカウント名、データベース名、またはコンテナーが見つからないか、指定されたコレクションで分析ストレージが有効になっていません。|
| CosmosDB パスを解決できませんでした。エラー: "シークレット値が正しくありません" または "シークレットが null または空です"。 | アカウント キーが無効であるか、存在しません。 |
| 型 `type name` の列 `column name` は外部データ型 `type name` と互換性がありません。 | `WITH` 句に指定された列の型が Azure Cosmos DB コンテナーの型と一致しません。 セクション「[Azure Cosmos DB から SQL 型へのマッピング](#azure-cosmos-db-to-sql-type-mappings)」で説明されているように列の型を変更するか、または `VARCHAR` 型を使用してください。 |
| すべてのセルで、この列には `NULL` 値が含まれます。 | `WITH` 句の列名またはパス式が間違っている可能性があります。 `WITH` 句の列名 (または列の型の後のパス式) は、Azure Cosmos DB コレクションの一部のプロパティ名と一致する必要があります。 比較では、"*大文字と小文字が区別されます*"。 たとえば、`productCode` と `ProductCode` は異なるプロパティです。 |

[Azure Synapse Analytics のフィードバック ページ](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862)で、提案や問題を報告できます。

## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。

- [Azure Synapse Link で Power BI とサーバーレス SQL プールを使用する](../../cosmos-db/synapse-link-power-bi.md)
- [サーバーレス SQL プールでビューを作成および使用する](create-use-views.md)
- [Azure Cosmos DB に対するサーバーレス SQL プール ビューの作成と、DirectQuery による Power BI モデルへのそれらの接続に関するチュートリアル](./tutorial-data-analyst.md)
