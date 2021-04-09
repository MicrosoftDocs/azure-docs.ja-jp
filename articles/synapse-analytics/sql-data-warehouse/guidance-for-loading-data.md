---
title: 専用 SQL プールのデータ読み込みのベスト プラクティス
description: Azure Synapse Analytics の専用 SQL プール を使用したデータの読み込みに関する推奨事項とパフォーマンスの最適化。
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 004084097e2f24a4ceb36f7a016049a574b80b42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594837"
---
# <a name="best-practices-for-loading-data-using-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics の専用 SQL プールを使用したデータの読み込みのベスト プラクティス

この記事では、専用 SQL プールを使用したデータの読み込みに関する推奨事項とパフォーマンスの最適化について説明します。

## <a name="preparing-data-in-azure-storage"></a>Azure Storage のデータの準備

待機時間を最小限に抑えるには、ストレージ層と専用 SQL プールを併置します。

ORC ファイル形式でデータをエクスポートすると、大きなテキスト列がある場合に、Java のメモリ不足エラーが発生することがあります。 この制限を回避するには、列のサブセットのみをエクスポートします。

すべてのファイル形式は、異なるパフォーマンス特性を持っています。 最速で読み込むには、圧縮されたテキスト区切りファイルを使用します。 UTF-8 と UTF-16 のパフォーマンスの違いはごくわずかです。

大きな圧縮ファイルは小さな圧縮ファイルに分割します。

## <a name="running-loads-with-enough-compute"></a>十分なコンピューティング リソースで読み込みを実行する

読み込み速度を最速にするには、一度に 1 つの読み込みジョブのみを実行します。 それが実行できない場合は、同時に実行する読み込み数を最小限に抑えます。 大規模な読み込みジョブが予想される場合は、読み込み前に専用 SQL プールをスケール アップすることを検討してください。

適切なコンピューティング リソースで読み込みを実行するには、読み込みを実行するように指定された読み込みユーザーを作成します。 各読み込みユーザーを特定のワークロード グループに分類します。 読み込みを実行するには、いずれかの読み込みユーザーとしてサインインし、読み込みを実行します。 読み込みは、ユーザーのワークロード グループを使用して実行されます。  

### <a name="example-of-creating-a-loading-user"></a>読み込みユーザーの作成の例

この例では、特定のワークロード グループに分類された読み込みユーザーを作成します。 まず、**マスターに接続** し、ログインを作成します。

```sql
   -- Connect to master
   CREATE LOGIN loader WITH PASSWORD = 'a123STRONGpassword!';
```

専用 SQL プールに接続し、ユーザーを作成します。 次のコードは、mySampleDataWarehouse という名前のデータベースに接続していることを前提としています。 これは、loader と呼ばれるユーザーを作成し、テーブルを作成するためのユーザーのアクセス許可を付与した後、[COPY ステートメント](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)を使用して読み込む方法を示しています。 次に、このユーザーを最大のリソースを含む DataLoads ワークロード グループに分類します。 

```sql
   -- Connect to the dedicated SQL pool
   CREATE USER loader FOR LOGIN loader;
   GRANT ADMINISTER DATABASE BULK OPERATIONS TO loader;
   GRANT INSERT ON <yourtablename> TO loader;
   GRANT SELECT ON <yourtablename> TO loader;
   GRANT CREATE TABLE TO loader;
   GRANT ALTER ON SCHEMA::dbo TO loader;
   
   CREATE WORKLOAD GROUP DataLoads
   WITH ( 
       MIN_PERCENTAGE_RESOURCE = 100
       ,CAP_PERCENTAGE_RESOURCE = 100
       ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 100
    );

   CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
   WITH (
         WORKLOAD_GROUP = 'DataLoads'
       ,MEMBERNAME = 'loader'
   );
```
<br><br>
>[!IMPORTANT] 
>これは、SQL プールの 100% のリソースを単一の読み込みに割り当てる極端な例です。 これにより、最大コンカレンシーが 1 になります。 これを使用する必要があるのは、ワークロード間でリソースのバランスをとるために独自の構成で追加のワークロード グループを作成する必要がある初期読み込みに対してのみであることに注意してください。 

読み込みワークロード グループのリソースを使用して読み込みを実行するには、loader としてサインインし、読み込みを実行します。

## <a name="allowing-multiple-users-to-load-polybase"></a>複数のユーザーによる読み込みの許可 (PolyBase)

多くの場合、複数のユーザーが 1 つの専用 SQL プールにデータを読み込む必要があります。 [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (PolyBase) を使用して読み込むには、データベースの CONTROL アクセス許可が必要です。  CONTROL アクセス許可は、すべてのスキーマに対する制御アクセス権を付与します。

読み込みを実行するすべてのユーザーに、すべてのスキーマに対する制御アクセス権を付与したくない場合があります。 アクセス許可を制限するには、DENY CONTROL ステートメントを使用します。

たとえば、部門 A に schema_A、部門 B に schema_B というデータベース スキーマがあるとします。データベース ユーザーの user_A と user_B を、部門 A と B のそれぞれで読み込みを行う PolyBase のユーザーにします。 両方のユーザーには CONTROL データベースのアクセス許可が付与されています。 スキーマ A と B の作成者はここで、次のように DENY を使用してスキーマをロックダウンします。

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

user_A と user_B は、他の部門のスキーマからロックアウトされるようになりました。

## <a name="loading-to-a-staging-table"></a>ステージング テーブルに読み込む

データを専用 SQL プール テーブルに移動する際に最速の読み込み速度を達成するには、データを 1 つのステージング テーブルに読み込みます。  ステージング テーブルをヒープとして定義し、分散オプションにラウンドロビンを使用します。

通常、読み込みは 2 段階のプロセスです。まずステージング テーブルにデータを読み込んでから、運用環境の専用 SQL プール テーブルに挿入します。 運用テーブルでハッシュ分散を使用している場合、ハッシュ分散を使用してステージング テーブルを定義すると、読み込みと挿入の合計時間が速くなる可能性があります。

ステージング テーブルへの読み込みに時間はかかりますが、運用テーブルに行を挿入する 2 つ目の手順では、分散全体でデータの移動は発生しません。

## <a name="loading-to-a-columnstore-index"></a>列ストア インデックスを読み込む

列ストア インデックスは、高品質の行グループにデータを圧縮するために多くのメモリを必要とします。 最適な圧縮とインデックスの効率を得るには、列ストア インデックスで各行グループに最大 1,048,576 行を圧縮する必要があります。

メモリが不足気味である場合、列ストア インデックスは最大圧縮率を達成できないことがあります。 このシナリオは、クエリのパフォーマンスに影響します。 詳細については、[列ストア メモリの最適化](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)に関するページを参照してください。

- 最大圧縮率を達成するための十分なメモリを読み込みユーザーが確実に持つようにするには、中規模または大規模なリソース クラスのメンバーである読み込みユーザーを使用します。
- 新しい行グループを完全に埋められるように、十分な行を読み込みます。 一括読み込みでは、1,048,576 行ごとに、1 つの完全な行グループとして列ストアに直接圧縮されます。 102,400 行未満の読み込みの場合、行はデルタストアに送信され、行は b ツリー インデックスに保持されます。

> [!NOTE]
> 読み込みの行が少なすぎる場合は、すべての行がデルタストアにルーティングされ、すぐには列ストア形式に圧縮されません。

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>SqLBulkCopy API または bcp を使用するときはバッチ サイズを増やす

COPY ステートメントを使用して読み込むと、専用 SQL プールで最大のスループットが得られます。 COPY を使用して読み込むことができず、[SqLBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) または [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) を使用する必要がある場合は、スループットを向上させるためにバッチ サイズを増やすことを検討してください。

> [!TIP]
> 最適なバッチ サイズの容量を決定するために推奨されるベースラインは、10 万行から 100 万行のバッチ サイズです。

## <a name="handling-loading-failures"></a>読み込みエラーを処理する

外部テーブルを使用した読み込みが、"*クエリは中止されました。外部ソースの読み取り中に最大拒否しきい値に達しました*" というエラーで失敗する場合があります。 このメッセージは、外部データにダーティなレコードが含まれていることを示します。

次のいずれかの条件を満たしている場合、データ レコードはダーティであると見なされます。

- データ型と列数が、外部テーブルの列定義と一致しません。
- データが、指定された外部ファイル形式に準拠していません。

ダーティなレコードを修正するには、外部テーブルと外部ファイルの形式の定義が正しいこと、および外部データがこれらの定義に従っていることを確認します。

外部データ レコードのサブセットがダーティである場合は、[CREATE EXTERNAL TABLE (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) の中で拒否オプションを使用することで、クエリでこれらのレコードを拒否することを選択できます。

## <a name="inserting-data-into-a-production-table"></a>運用テーブルにデータを挿入する

[INSERT ステートメント](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)で小さなテーブルに 1 回だけ読み込む場合や、検索を定期的に再読み込みする場合は、`INSERT INTO MyLookup VALUES (1, 'Type 1')` などのステートメントで十分です。  ただし、単一挿入は、一括読み込みを実行するほど効率的ではありません。

一日に何千もの単一の挿入がある場合は、一括読み込みができるように、挿入をバッチ化します。  単一の挿入をファイルに追加する処理を開発し、定期的にファイルを読み込む別の処理を作成します。

## <a name="creating-statistics-after-the-load"></a>読み込み後に統計を作成する

クエリ パフォーマンスを向上させるには、最初に読み込んだ後またはデータに大きな変更が加えられた後に、すべてのテーブルのすべての列で統計を作成することが重要です。 手動で統計を作成するか、[AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic) を有効にすることができます。

統計の詳細については、 [統計](sql-data-warehouse-tables-statistics.md)に関する記事を参照してください。 次の例では、Customer_Speed テーブルの 5 つの列に関する統計を手動で作成する方法を示しています。

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys-polybase"></a>ストレージ キーを切り替える (PolyBase)

セキュリティのために BLOB ストレージへのアクセス キーを定期的に変更することをお勧めします。 BLOB ストレージ アカウントには 2 つのストレージ キーがあり、キーの切り替えに使用できます。

Azure Storage のアカウント キーを切り替えるには:

キーが変更されているストレージ アカウントごとに、[ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) を発行します。

例:

元のキーを作成します。

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

key 1 から key 2 にキーを交換します。

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

基となる外部データ ソースに対するこの他の変更は不要です。

## <a name="next-steps"></a>次のステップ

- 抽出、読み込み、変換 (ELT) プロセスを設計するときの COPY ステートメントまたは PolyBase の詳細については、[Azure Synapse Analytics の ELT の設計](design-elt-data-loading.md)に関するページを参照してください。
- 読み込みのチュートリアルについては、[COPY ステートメントを使用した Azure BLOB ストレージから Synapse SQL へのデータの読み込み](./load-data-from-azure-blob-storage-using-copy.md)に関するページを参照してください。
- データの読み込みの監視については、「[DMV を利用してワークロードを監視する](sql-data-warehouse-manage-monitor.md)」を参照してください。