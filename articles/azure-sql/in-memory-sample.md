---
title: In-Memory のサンプル
description: OLTP と列ストアのサンプルを使用して Azure SQL Database In-Memory テクノロジを試してください。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: b5a1035f8a213a6ce02dd3252ff7d3ddea46faf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92786583"
---
# <a name="in-memory-sample"></a>In-Memory のサンプル
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database のインメモリ テクノロジにより、アプリケーションのパフォーマンスを向上させることができ、また、データベースのコストを削減できる可能性があります。 Azure SQL Database のインメモリ テクノロジを使用すれば、さまざまなワークロードでパフォーマンスの向上を実現できます。

この記事では、インメモリ OLTP と列ストア インデックスを Azure SQL Database で使用する方法を示す 2 つのサンプルを確認します。

詳細については、次を参照してください。

- [インメモリ OLTP の概要と使用シナリオ](/sql/relational-databases/in-memory-oltp/overview-and-usage-scenarios) (開始するためのお客様の導入事例と情報への参照)
- [インメモリ OLTP のドキュメント](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [列ストア インデックスの説明](/sql/relational-databases/indexes/columnstore-indexes-overview)
- [リアルタイム運用分析](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)とも呼ばれる、ハイブリッド トランザクション/分析処理 (HTAP)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1.インメモリ OLTP のサンプルをインストールする

[Azure Portal](https://portal.azure.com/) で数回クリックするだけで、AdventureWorksLT のサンプル データベースを作成できます。 このセクションの手順では、インメモリ OLTP オブジェクトを使用して AdventureWorksLT データベースを強化する方法について説明し、パフォーマンス上のメリットを示します。

より簡潔でありながら見栄えの良いインメモリ OLTP のパフォーマンス デモについては、次を参照してください。

- リリース: [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- ソース コード: [in-memory-oltp-demo-source-code](https://github.com/microsoft/sql-server-samples/tree/master/samples/features/in-memory-database)

### <a name="installation-steps"></a>インストール手順

1. [Azure Portal](https://portal.azure.com/) で、サーバー上に Premium または Business Critical データベースを作成します。 **ソース** を AdventureWorksLT サンプル データベースに設定します。 詳細な手順については、[Azure SQL Database での最初のデータベースの作成](database/single-database-create-quickstart.md)に関するページを参照してください。

2. SQL Server Management Studio [(SSMS.exe)](/sql/ssms/download-sql-server-management-studio-ssms)を使用して、データベースに接続します。

3. [インメモリ OLTP Transact-SQL スクリプト](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_oltp_sample.sql) をクリップボードにコピーします。 この T-SQL スクリプトによって、手順 1. で作成した AdventureWorksLT サンプル データベース内に、必要なインメモリ オブジェクトが作成されます。

4. T-SQL スクリプトを SSMS に貼り付け、スクリプトを実行します。 `MEMORY_OPTIMIZED = ON` 句の CREATE TABLE ステートメントが重要です。 次に例を示します。

```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```

### <a name="error-40536"></a>エラー 40536

T-SQL スクリプトを実行するときにエラー 40536 が発生する場合は、次の T-SQL スクリプトを実行し、データベースがインメモリをサポートしているかどうかを確認します。

```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```

結果が **0** の場合、インメモリがサポートされていないことを示します。**1** の場合はサポートされています。 問題を診断するには、データベースを Premium サービス レベルにします。

### <a name="about-the-created-memory-optimized-items"></a>作成されるメモリ最適化項目の概要

**[テーブル]** : このサンプルには、次のメモリ最適化テーブルが含まれています。

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed

SSMS で **オブジェクト エクスプローラー** を使用してメモリ最適化テーブルを確認できます。 **[テーブル]** を右クリックし、 >  **[フィルター]**  >  **[フィルターの設定]**  >  **[Is Memory Optimized (メモリ最適化済み)]** の順に選択します。 値は 1 です。

または、次のようにカタログ ビューにクエリを実行できます。

```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```

**ネイティブ コンパイル ストアド プロシージャ**:SalesLT.usp_InsertSalesOrder_inmem は、カタログ ビューのクエリを使用して確認できます。

```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```

&nbsp;

### <a name="run-the-sample-oltp-workload"></a>サンプルの OLTP ワークロードを実行する

次の 2 つの *ストアド プロシージャ* の違いは、1 つ目のプロシージャはメモリ最適化バージョンのテーブルを使用し、2 つ目のプロシージャは通常のディスク上のテーブルを使用している点です。

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**

このセクションでは、便利な **ostress.exe** ユーティリティを使用して、負荷が高い状態で 2 つのストアド プロシージャを実行する方法について説明します。 2 つのストレス実行が完了するまでの時間を比較することができます。

ostress.exe を実行する場合、次の両方について指定したパラメーター値を渡すことをお勧めします。

- 多数のコンカレント接続を実行するには、-n100 を使用します。
- 各接続を数百回ループさせるには、-r500 を使用します。

一方、すべてが適切に動作するようにするには、-n10、-r50 などの小さな値から始めることもできます。

### <a name="script-for-ostressexe"></a>ostress.exe のスクリプト

このセクションでは、ostress.exe コマンド ラインに埋め込まれた T-SQL スクリプトを示します。 このスクリプトでは、インストールした T-SQL スクリプトで作成されたアイテムを使用します。

次のスクリプトでは、5 行のアイテムがあるサンプルの販売注文を、次のメモリ最適化 *テーブル* に挿入します。

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem

```sql
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```

前述した ostress.exe の T-SQL スクリプトの *_ondisk* バージョンを作成するには、両方の *_inmem* サブストリングを *_ondisk* に置き換えます。 これらの置換は、テーブルとストアド プロシージャの名前に影響があります。

#### <a name="install-rml-utilities-and-ostress"></a>RML ユーティリティと `ostress` をインストールする

Azure 仮想マシン (VM) で ostress.exe を実行する計画を立てるのが理想的です。 AdventureWorksLT データベースがある Azure リージョンと同じリージョンに [Azure VM](https://azure.microsoft.com/documentation/services/virtual-machines/) を作成します。 代わりにノートパソコンで ostress.exe を実行することもできます。

VM または選択した任意のホストに、Replay Markup Language (RML) ユーティリティをインストールします。 このユーティリティに ostress.exe が含まれています。

詳細については、次を参照してください。

- 「[インメモリ OLTP のサンプル データベース](/sql/relational-databases/in-memory-oltp/sample-database-for-in-memory-oltp)」にある ostress.exe の説明。
- [インメモリ OLTP のサンプル データベース](/sql/relational-databases/in-memory-oltp/sample-database-for-in-memory-oltp)。
- [ostress.exe のインストールに関するブログ](https://techcommunity.microsoft.com/t5/sql-server-support/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql/ba-p/317910)。

<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->

#### <a name="run-the-_inmem-stress-workload-first"></a>最初に *_inmem* stress ワークロードを実行する

*RML コマンド プロンプト* ウィンドウを使用して、ostress.exe コマンド ラインを実行できます。 コマンドライン パラメーターは `ostress` に次のことを行うように求めます。

- 100 個の接続を同時に実行する (-n100)。
- 各接続に T-SQL スクリプトを 50 回実行させる (-r50)。

```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```

前述の ostress.exe コマンド ラインを実行するには:

1. SSMS で次のコマンドを実行してデータベースのデータ コンテンツをリセットし、前回の実行で挿入されたすべてのデータを削除します。

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. 上記の ostress.exe コマンドラインのテキストをクリップボードにコピーします。

3. パラメーター -S -U -P -d の `<placeholders>` を実際の正しい値に置き換えます。

4. 編集したコマンドラインを RML コマンド ウィンドウで実行します。

#### <a name="result-is-a-duration"></a>結果は期間

`ostress.exe` が完了すると、RML コマンド ウィンドウに表示される出力の最終行に実行時間が出力されます。 たとえば、短いテストの場合、約 1.5 分かかります。

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`

#### <a name="reset-edit-for-_ondisk-then-rerun"></a>リセット、 *_ondisk* の編集、再実行

*_inmem* 実行の結果を取得したら、 *_ondisk* 実行に次の手順を実行します。

1. SSMS で次のコマンドを実行してデータベースをリセットし、前回の実行で挿入されたすべてのデータを削除します。

   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. ostress.exe コマンド ラインを編集して、すべての *_inmem* を *_ondisk* に置き換えます。

3. ostress.exe を再び実行し、期間の結果を取得します。

4. (大量のテスト データとなる可能性があるデータを確実に削除するために) もう一度データベースをリセットします。

#### <a name="expected-comparison-results"></a>予想される比較結果

インメモリ テストの結果、`ostress` をデータベースと同じ Azure リージョンにある Azure VM で実行した場合、この単純なワークロードではパフォーマンスが **9 倍** 向上することがわかりました。

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2.インメモリ分析のサンプルをインストールする

このセクションでは、列ストア インデックスと従来の B ツリー インデックスを使用した場合の IO と統計情報の結果を比較します。

OLTP ワークロードのリアルタイム分析では、多くの場合、非クラスター化列ストア インデックスを使用するのが最適です。 詳細については、[列ストア インデックスの説明](/sql/relational-databases/indexes/columnstore-indexes-overview)に関するページを参照してください。

### <a name="prepare-the-columnstore-analytics-test"></a>列ストア分析テストを準備する

1. Azure Portal を使用して、サンプルから最新の AdventureWorksLT データベースを作成します。
   - 正確な名前を使用します。
   - 任意の Premium サービス階層を選択します。

2. [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_analytics_sample.sql) をクリップボードにコピーします。
   - この T-SQL スクリプトによって、手順 1. で作成した AdventureWorksLT サンプル データベース内に、必要なインメモリ オブジェクトが作成されます。
   - このスクリプトでは、Dimension テーブルと 2 つのファクト テーブルを作成します。 fact テーブルには、それぞれ 350 万行のデータが設定されています。
   - スクリプトが完了するには約 15 分かかります。

3. T-SQL スクリプトを SSMS に貼り付け、スクリプトを実行します。 次のように、**CREATE INDEX** ステートメントの **COLUMNSTORE** キーワードが重要です。<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. AdventureWorksLT を互換性レベル 130 に設定します。<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    レベル 130 はインメモリ機能に直接、関係ありません。 しかし、レベル 130 は一般に、120 の場合よりも高いクエリ パフォーマンスを提供します。

#### <a name="key-tables-and-columnstore-indexes"></a>重要なテーブルと列ストア インデックス

- dbo.FactResellerSalesXL_CCI は、クラスター化列ストア インデックスがあるテーブルで、"*データ*" レベルで高度に圧縮されます。

- dbo.FactResellerSalesXL_PageCompressed は、同等の標準のクラスター化されたインデックスがあるテーブルで、*ページ* レベルでのみ圧縮されます。

#### <a name="key-queries-to-compare-the-columnstore-index"></a>列ストア インデックスを比較する重要なクエリ

パフォーマンスの改善を確認できるいくつかの T-SQL クエリの種類については、[こちら](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/clustered_columnstore_sample_queries.sql)を参照してください。 T-SQL スクリプトの手順 2. では、このペアのクエリに注意してください。 2 つのクエリの違いは次の 1 行のみです。

- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`

クラスター化列ストア インデックスは FactResellerSalesXL\_CCI テーブルにあります。

次の T-SQL スクリプトの抜粋では、各テーブルのクエリの IO と TIME の統計情報を出力します。

```sql
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

P2 価格レベルのデータベースでは、クラスター化列ストア インデックスを使用したクエリについて、従来のインデックスと比較して約 9 倍のパフォーマンス向上が期待できます。 P15 では、列ストア インデックスを使用することで約 57 倍のパフォーマンス向上が期待できます。

## <a name="next-steps"></a>次のステップ

- [クイック スタート 1:T-SQL のパフォーマンスの高速化のためのインメモリ OLTP テクノロジ](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp)

- [既存の Azure SQL アプリケーションにおけるインメモリ OLTP の使用](in-memory-oltp-configure.md)

- インメモリ OLTP の[インメモリ OLTP ストレージの監視](in-memory-oltp-monitor-space.md)

## <a name="additional-resources"></a>その他のリソース

### <a name="deeper-information"></a>詳細情報

- [クォーラムが Azure SQL Database でインメモリ OLTP を使用して DTU の 70% を削減しながら主要なデータベースのワークロードを 2 倍にする方法について](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Azure SQL Database のインメモリ OLTP に関するブログ記事](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [インメモリ OLTP の詳細情報](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)

- [列ストア インデックスの詳細情報](/sql/relational-databases/indexes/columnstore-indexes-overview)

- [リアルタイム運用分析の詳細情報](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

- (インメモリ OLTP によってパフォーマンスが大幅に向上する一般的なワークロード パターンが記載されている) [一般的なワークロード パターンと移行の考慮事項](/previous-versions/dn673538(v=msdn.10))に関するホワイトペーパーを参照してください

#### <a name="application-design"></a>アプリケーションの設計

- [インメモリ OLTP (インメモリ最適化)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)

- [既存の Azure SQL アプリケーションにおけるインメモリ OLTP の使用](in-memory-oltp-configure.md)

#### <a name="tools"></a>ツール

- [Azure Portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)

- [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt)