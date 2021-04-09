---
title: Synapse SQL を使用したテーブルの設計
description: Synapse SQL でのテーブル設計の概要。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 27cc53c3eef1bb2a9962d2c21ae80db3c8b0383d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585436"
---
# <a name="design-tables-using-synapse-sql-in-azure-synapse-analytics"></a>Azure Synapse Analytics での Synapse SQL を使用したテーブルの設計

このドキュメントでは、専用 SQL プールとサーバーレス SQL プールによるテーブル設計の主要な概念について説明します。  

[サーバーレス SQL プール](on-demand-workspace-overview.md)は、データ レイク内のデータに対するクエリ サービスです。 これには、データ インジェスト用のローカル ストレージはありません。 [専用 SQL プール](best-practices-dedicated-sql-pool.md)は、Synapse SQL を使用するときにプロビジョニングされる分析リソースのコレクションを表します。 専用 SQL プールのサイズは、Data Warehouse ユニット (DWU) によって決まります。

次の表は、専用 SQL プールとサーバーレス SQL プールに関連するトピックを示しています。

| トピック                                                        | 専用 SQL プール | サーバーレス SQL プール |
| ------------------------------------------------------------ | ------------------ | ----------------------- |
| [テーブル カテゴリを決定する](#determine-table-category)        | はい                | いいえ                      |
| [スキーマ名](#schema-names)                                | はい                | はい                     |
| [テーブル名](#table-names)                                  | はい                | いいえ                      |
| [テーブルの永続性](#table-persistence)                      | はい                | いいえ                      |
| [通常のテーブル](#regular-table)                              | はい                | いいえ                      |
| [一時テーブル](#temporary-table)                          | はい                | はい                     |
| [外部テーブル](#external-table)                            | はい                | はい                     |
| [データ型](#data-types)                                    | はい                | はい                     |
| [分散テーブル](#distributed-tables)                    | はい                | いいえ                      |
| [ハッシュ分散テーブル](#hash-distributed-tables)          | はい                | いいえ                      |
| [レプリケート テーブル](#replicated-tables)                      | はい                | いいえ                      |
| [ラウンドロビン テーブル](#round-robin-tables)                    | はい                | いいえ                      |
| [テーブルの一般的な分散方法](#common-distribution-methods-for-tables) | はい                | いいえ                      |
| [パーティション](#partitions)                                    | はい                | はい                     |
| [列ストア インデックス](#columnstore-indexes)                  | はい                | いいえ                      |
| [統計](#statistics)                                    | はい                | はい                     |
| [主キーと一意キー](#primary-key-and-unique-key)    | はい                | いいえ                      |
| [テーブルを作成するためのコマンド](#commands-for-creating-tables) | はい                | いいえ                      |
| [ソース データをデータ ウェアハウスに配置する](#align-source-data-with-the-data-warehouse) | はい                | いいえ                      |
| [サポートされていないテーブルの機能](#unsupported-table-features)    | はい                | いいえ                      |
| [テーブル サイズのクエリ](#table-size-queries)                    | はい                | いいえ                      |

## <a name="determine-table-category"></a>テーブル カテゴリを決定する

[スター スキーマ](https://en.wikipedia.org/wiki/Star_schema)は、データをファクト テーブルとディメンション テーブルに編成します。 一部のテーブルは、ファクト テーブルまたはディメンション テーブルに移動する前に統合またはステージング データに使用されます。 テーブルを設計する際には、テーブルのデータがファクト、ディメンション、統合のいずれのテーブルに属するかを決定します。 この決定は、適切なテーブル構造体および配布を通知します。

- **ファクト テーブル** には、一般にトランザクション システムで生成された後、データ ウェアハウスに読み込まれる定量的データが含まれています。 たとえば、小売業では販売トランザクションを毎日生成した後、そのデータを分析のためにデータ ウェアハウス ファクト テーブルに読み込みます。

- **ディメンション テーブル** には、変化する可能性はあるが通常は変更頻度が低い属性データが含まれます。 たとえば、顧客の名前と住所はディメンション テーブルに格納され、その顧客のプロファイルが変更された場合にのみ更新されます。 大規模なファクト テーブルのサイズを最小限に抑えるために、ファクト テーブルのすべての行に顧客の名前と住所を格納する必要はありません。 代わりに、ファクト テーブルとディメンション テーブルで顧客 ID を共有できます。 クエリで 2 つのテーブルを結合して、顧客のプロファイルとトランザクションに関連付けることができます。

- **統合テーブル** は、統合またはステージング データの場所を提供します。 統合テーブルは、通常のテーブル、外部テーブル、または一時テーブルとして作成できます。 たとえば、ステージング テーブルにデータを読み込み、ステージングでデータの変換を実行してから、データを運用環境テーブルに挿入できます。

## <a name="schema-names"></a>スキーマ名

スキーマは、同様の方法で使用されるオブジェクトをグループ化するのに適しています。 次のコードは、wwi と呼ばれる[ユーザー定義スキーマ](/sql/t-sql/statements/create-schema-transact-sql?view=azure-sqldw-latest&preserve-view=true)を作成します。

```sql
CREATE SCHEMA wwi;
```

## <a name="table-names"></a>テーブル名

複数のデータベースをオンプレミス ソリューションから専用 SQL プールに移行する場合、ベスト プラクティスとして、ファクト テーブル、ディメンション テーブル、および統合テーブルのすべてを 1 つの SQL プール スキーマに移行することをお勧めします。 たとえば、すべてのテーブルを、wwi と呼ばれる 1 つのスキーマ内の [WideWorldImportersDW](/sql/samples/wide-world-importers-dw-database-catalog?view=azure-sqldw-latest&preserve-view=true) サンプル データ ウェアハウスに格納できます。

専用 SQL プール内のテーブルの構成を表示するには、テーブル名のプレフィックスとして fact、dim、および int を使用できます。 次の表に、WideWorldImportersDW のスキーマ名とテーブル名の一部を示します。  

| WideWorldImportersDW テーブル  | テーブルの種類です。 | 専用 SQL プール |
|:-----|:-----|:------|:-----|
| City | Dimension | wwi.DimCity |
| Order | ファクト | wwi.FactOrder |

## <a name="table-persistence"></a>テーブルの永続性

テーブルは、データを Azure Storage に永続的に格納するか、Azure Storage に一時的に格納するか、またはデータ ウェアハウスの外部にあるデータ ストアに格納します。

### <a name="regular-table"></a>通常のテーブル

通常のテーブルは、データ ウェアハウスの一部として Azure Storage にデータを格納します。 テーブルとデータは、セッションが開かれているかどうかにかかわらず保持されます。  次の例では、2 つの列を含む通常のテーブルを作成します。

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>一時テーブル

一時テーブルは、セッション中のみ存在します。 一時テーブルを使用して、一時的な結果が他のユーザーに表示されないようにすることができます。 一時テーブルを使用すると、クリーンアップの必要性も低減されます。  一時テーブルではローカル ストレージが利用されるため、専用 SQL プールではパフォーマンスを向上させることができます。  

サーバーレス SQL プールでは、一時テーブルがサポートされています。 しかし、一時テーブルから選択することはできますが、ストレージ内のファイルと結合できないため、使用は制限されます。

詳しくは、[一時テーブル](develop-tables-temporary.md)に関する記事をご覧ください。

### <a name="external-table"></a>外部テーブル

[外部テーブル](develop-tables-external-tables.md)は、Azure Storage BLOB または Azure Data Lake Store 内にあるデータを指します。

データを外部テーブルから専用 SQL プールにインポートするには、[CREATE TABLE AS SELECT](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ステートメントを使用します。 読み込みのチュートリアルについては、「[PolyBase を使用して Azure Blob Storage から Azure SQL Data Warehouse にデータを読み込む](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)」をご覧ください。

サーバーレス SQL プールでは、[CETAS](develop-tables-cetas.md) を使用してクエリの結果を Azure Storage 内の外部テーブルに保存できます。

## <a name="data-types"></a>データ型

専用 SQL プールでは、最も一般的に使用されるデータ型がサポートされています。 サポートされるデータ型の一覧については、CREATE TABLE ステートメントの「[data types in CREATE TABLE reference (CREATE TABLE 内のデータ型のリファレンス)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest#DataTypes&preserve-view=true)」を参照してください。 データ型の使用の詳細については、[データ型](../sql/develop-tables-data-types.md)に関するページをご覧ください。

## <a name="distributed-tables"></a>分散テーブル

専用 SQL プールの基本的な特徴は、テーブルを[複数のディストリビューション](../sql-data-warehouse/massively-parallel-processing-mpp-architecture.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#distributions)にわたって格納し、操作できる方法にあります。  専用 SQL プールでは、データを分散させるための次の 3 つの方法がサポートされます。

- ラウンドロビン (既定)
- ハッシュ インデックス
- レプリケート

### <a name="hash-distributed-tables"></a>ハッシュ分散テーブル

ハッシュ分散テーブルは、ディストリビューション列内の値に基づいて行を分散します。 ハッシュ分散テーブルは、大規模なテーブルのクエリでハイパフォーマンスを達成するように設計されています。 ディストリビューション列を選択する際に検討すべきいくつかの要素があります。

詳細については、「[Design guidance for distributed tables (分散テーブルの設計ガイダンス)](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)」を参照してください。

### <a name="replicated-tables"></a>レプリケート テーブル

レプリケート テーブルには、すべてのコンピューティング ノードで使用可能なテーブルの完全なコピーが含まれています。 レプリケート テーブルの結合ではデータ移動は必要ないため、レプリケート テーブルに対するクエリは高速に実行されます。 ただし、レプリケーションには余分なストレージが必要であるため、大きなテーブルには適していません。

詳しくは、[レプリケート テーブルを使用するための設計ガイダンス](../sql-data-warehouse/design-guidance-for-replicated-tables.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関する記事をご覧ください。

### <a name="round-robin-tables"></a>ラウンドロビン テーブル

ラウンドロビン テーブルは、すべてのディストリビューションにわたって均等にテーブル行を分散させます。 これらの行は、ランダムに分散されます。 ラウンドロビン テーブルへのデータの読み込みは高速です。  ただし、クエリは、他の分散方法より多くのデータ移動を必要とする可能性があります。

詳細については、「[Design guidance for distributed tables (分散テーブルの設計ガイダンス)](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)」を参照してください。

### <a name="common-distribution-methods-for-tables"></a>テーブルの一般的な分散方法

多くの場合、テーブル カテゴリによって、テーブルの最適な分散オプションが決定されます。

| テーブル カテゴリ | 推奨される分散オプション |
|:---------------|:--------------------|
| ファクト           | クラスター化列ストア インデックスによるハッシュ分散を使用します。 2 つのハッシュ テーブルが同じディストリビューション列に結合される場合にパフォーマンスが向上します。 |
| Dimension      | 小さなテーブルにはレプリケートを使用します。 各コンピューティング ノードに保存するにはテーブルが大きすぎる場合は、ハッシュ分散を使用します。 |
| ステージング        | ステージング テーブルにはラウンド ロビンを使用します。 CTAS での読み込みが高速です。 データがステージング テーブルに格納されたら、INSERT...SELECT を使用してデータを運用環境テーブルに移動します。 |

## <a name="partitions"></a>メジャー グループ

専用 SQL プールでは、パーティション テーブルにより、データ範囲に基づいてテーブル行が格納され、操作が実行されます。 たとえば、day、month、または year でテーブルをパーティション分割できます。 クエリ スキャンをあるパーティション内のデータに制限するパーティション除外によってクエリ パフォーマンスを向上させることができます。

パーティションを切り替えてデータを維持することもできます。 専用 SQL プールのデータは既に分散されているため、パーティションが多すぎるとクエリ パフォーマンスが低下することがあります。 詳しくは、[パーティション分割のガイダンス](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関する記事をご覧ください。  

> [!TIP]
> 空でないテーブル パーティションにパーティションを切り替えるとき、既存のデータが切り詰められる場合は、[ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true) ステートメントに TRUNCATE_TARGET オプションを使用することを検討してください。

次のコードでは、変換された日次データを SalesFact パーティションに切り替えて、既存のデータを上書きします。

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

サーバーレス SQL プールでは、クエリで読み取られるファイルまたはフォルダー (パーティション) を制限できます。 パスによるパーティション分割は、[ストレージ ファイルに対するクエリ](develop-storage-files-overview.md)に関するページで説明されている filepath 関数と fileinfo 関数を使用することでサポートされます。 次の例では、2017 年のデータを含むフォルダーを読み取ります。

```sql
SELECT
    nyc.filepath(1) AS [year],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
GROUP BY
    nyc.filepath(1),
    payment_type
ORDER BY
    nyc.filepath(1),
    payment_type
```

## <a name="columnstore-indexes"></a>列ストア インデックス

既定では、専用 SQL プールには、テーブルがクラスター化列ストア インデックスとして格納されます。 この形式のデータ ストレージでは、大きなテーブルに対する高いデータ圧縮およびクエリ パフォーマンスが実現されます。  クラスター化列ストア インデックスは、通常は最適な選択肢ですが、場合によっては、クラスター化インデックスまたはヒープが適切なストレージ構造体の場合もあります。  

> [!TIP]
> ヒープ テーブルは、最終的なテーブルに変換されるステージング テーブルなどの一時的なデータを読み込むのに特に役立ちます。

列ストア機能の一覧については[列ストア インデックスの新機能](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?view=azure-sqldw-latest&preserve-view=true)に関する記事をご覧ください。 列ストア インデックスのパフォーマンスを向上させるには、[列ストア インデックスの行グループの品質の最大化](../sql/data-load-columnstore-compression.md)に関する記事をご覧ください。

## <a name="statistics"></a>統計

クエリ オプティマイザーでは、クエリ実行のプランの作成時に列レベルの統計が使用されます。 クエリのパフォーマンスを向上させるには、個々の列、特にクエリの結合で使用される列の統計を作成することが重要です。 Synapse SQL では、統計の自動作成がサポートされます。 

統計の更新は自動的には行われません。 大量の行が追加または変更された後に統計を更新します。 たとえば、読み込みの後に統計を更新します。 詳細については、[統計のガイダンス](develop-tables-statistics.md)に関する記事をご覧ください。

## <a name="primary-key-and-unique-key"></a>主キーと一意キー

専用 SQL プールでは、PRIMARY KEY は、NONCLUSTERED と NOT ENFORCED の両方が使用されている場合にのみサポートされます。  UNIQUE 制約は、NOT ENFORCED が使用されている場合にのみサポートされます。  詳細については、[SQL プールのテーブル制約](../sql-data-warehouse/sql-data-warehouse-table-constraints.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関する記事をご覧ください。

## <a name="commands-for-creating-tables"></a>テーブルを作成するためのコマンド

専用 SQL プールでは、テーブルは新しい空のテーブルとして作成することができます。 テーブルを作成し、SELECT ステートメントの結果を使用して値を設定することもできます。 テーブルを作成するための T-SQL コマンドを次に示します。

| T-SQL ステートメント | 説明 |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | すべてのテーブル列およびオプションを定義して空のテーブルを作成します。 |
| [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true) | 外部テーブルを作成します。 テーブルの定義は、専用 SQL プールに格納されます。 テーブル データは Azure Blob Storage または Azure Data Lake Storage に格納されます。 |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | SELECT ステートメントの結果を使用して新しいテーブルに値が設定されます。 テーブルの列とデータ型は、SELECT ステートメントの結果に基づきます。 データをインポートするには、このステートメントで外部テーブルから選択できます。 |
| [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true) | 外部の場所に SELECT ステートメントの結果をエクスポートして、新しい外部テーブルを作成します。  その場所は Azure Blob Storage または Azure Data Lake Storage のいずれかです。 |

## <a name="align-source-data-with-the-data-warehouse"></a>ソース データをデータ ウェアハウスに配置する

専用 SQL プール テーブルは、別のデータ ソースからデータを読み込むことで設定されます。 読み込みを正常に完了させるには、ソース データ内の列の数とデータ型が、データ ウェアハウス内のテーブル定義と合致している必要があります。

> [!NOTE]
> 配置するデータの取得は、テーブルの設計の最大の難関となる可能性があります。

データを複数のデータ ストアから読み込む場合、データをデータ ウェアハウスに移植して、統合テーブルに格納できます。 データが統合テーブルに格納されたら、専用 SQL プールの機能を使用して変換操作を実行できます。 データの準備ができたら、それを運用テーブルに挿入できます。

## <a name="unsupported-table-features"></a>サポートされていないテーブルの機能

専用 SQL プールでは、他のデータベースで提供されるテーブル機能の多くがサポートされますが、すべてサポートされるわけではありません。  次の一覧は、専用 SQL プールでサポートされていないテーブル機能の一部を示しています。

- 外部キー ([テーブル制約](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?view=azure-sqldw-latest&preserve-view=true)に関するページを確認してください)
- [計算列](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [インデックス付きビュー](/sql/relational-databases/views/create-indexed-views?view=azure-sqldw-latest&preserve-view=true)
- [Sequence](/sql/t-sql/statements/create-sequence-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [スパース列](/sql/relational-databases/tables/use-sparse-columns?view=azure-sqldw-latest&preserve-view=true)
- 代理キー ([IDENTITY](../sql-data-warehouse/sql-data-warehouse-tables-identity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) を使用して実装)
- [シノニム](/sql/t-sql/statements/create-synonym-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [トリガー](/sql/t-sql/statements/create-trigger-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [一意のインデックス](/sql/t-sql/statements/create-index-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [ユーザー定義型](/sql/relational-databases/native-client/features/using-user-defined-types?view=azure-sqldw-latest&preserve-view=true)

## <a name="table-size-queries"></a>テーブル サイズのクエリ

専用 SQL プールにおいて、60 個の各ディストリビューション内のテーブルで使用される領域と行を簡単に識別する方法の 1 つは、[DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?view=azure-sqldw-latest&preserve-view=true) を使用することです。

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

ただし、DBCC コマンドの使用はかなり制限される可能性があることに注意してください。  動的管理ビュー (DMV) には、DBCC コマンドよりも詳しい情報が表示されます。 まず、次のビューを作成します。

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count]
    + nps.[row_overflow_used_page_count]
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count]
 - (nps.[reserved_page_count] - nps.[used_page_count])
 - ([in_row_data_page_count]
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
WHERE pn.[type] = 'COMPUTE'
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT *
FROM size
;
```

### <a name="table-space-summary"></a>テーブル領域の概要

次のクエリはテーブルごとに行と領域を返します。  テーブル領域の概要では、どのテーブルが最大のテーブルであるかが表示されます。 また、テーブルが、ラウンドロビン、レプリケート、またはハッシュ分散のいずれであるかも表示されます。  ハッシュ分散テーブルの場合、クエリによってディストリビューション列が表示されます。  

```sql
SELECT
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM
    dbo.vTableSizes
GROUP BY
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>ディストリビューションの種類別のテーブル領域

```sql
SELECT
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>インデックスの種類別のテーブル領域

```sql
SELECT
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>ディストリビューション領域の概要

```sql
SELECT
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>次のステップ

データ ウェアハウスにテーブルを作成した後、次の手順はテーブルへのデータの読み込みです。  読み込みのチュートリアルについては、[専用 SQL プールへのデータの読み込み](../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#load-the-data-into-sql-pool)に関するページを参照してください。