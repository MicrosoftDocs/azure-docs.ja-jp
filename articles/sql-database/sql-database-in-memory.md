---
title: "Azure SQL Database インメモリ テクノロジ | Microsoft Docs"
description: "Azure SQL Database インメモリは、トランザクション ワークロードと分析ワークロードのパフォーマンスを大幅に向上するテクノロジです。 これらのテクノロジを活用する方法について説明します。"
services: sql-database
documentationCenter: 
author: jodebrui
manager: jhubbard
editor: 
ms.assetid: 250ef341-90e5-492f-b075-b4750d237c05
ms.service: sql-database
ms.custom: development
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: jodebrui
translationtype: Human Translation
ms.sourcegitcommit: 187954f3ddafdbc17e341ce41f5b109cb95f8a24
ms.openlocfilehash: 620572f66367f61c6ee61d3c044083a0f71aca6f

---

# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>SQL Database でのインメモリ テクノロジを使用したパフォーマンスの最適化

Azure SQL Database のインメモリ テクノロジを使用することで、トランザクション (オンライン トランザクション処理 (OLTP))、分析 (オンライン分析処理 (OLAP))、およびその混合 (ハイブリッド トランザクション/分析処理 (HTAP)) といった、さまざまなワークロードでパフォーマンスの向上を実現できます。 クエリとトランザクションの処理が効率化するため、インメモリ テクノロジはコストの低減にも役立ちます。 通常は、パフォーマンスの向上を実現するためにデータベースの価格レベルをアップグレードする必要はありません。 場合によっては、インメモリ テクノロジでパフォーマンスを向上させながら価格レベルを下げられる場合さえあります。

インメモリ OLTP がパフォーマンスの著しい向上を促した例を&2; つ紹介します。

- インメモリ OLTP を利用することで、[クォーラム ビジネス ソリューションで DTU (つまり、リソースの消費量) を 70% 向上させながら、ワークロードを倍増させることができました](https://customers.microsoft.com/en-US/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)。
- ビデオ「[In-Memory OLTP in Azure SQL Database (Azure SQL Database のインメモリ OLTP)](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB)」では、サンプル ワークロードでリソースの消費量が大幅に向上したことが示されています。

インメモリ テクノロジは、Premium エラスティック プール内のデータベースを含む Premium レベル内のすべてのデータベースで利用できます。

次のビデオでは、Azure SQL Database でのインメモリ テクノロジ使用によるパフォーマンス向上の可能性について説明します。 表示されるパフォーマンスの向上は、常にさまざまな要因 (ワークロードとデータの性質、データベースのアクセス パターンなど) に依存していることに注意してください。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

Azure SQL Database には、次のインメモリ テクノロジがあります。

- *インメモリ OLTP*: スループットが向上し、トランザクション処理の遅延が減少します。 インメモリ OLTP が有益なシナリオには、取引やゲームなどのスループットの高いトランザクション処理、イベントまたは IoT デバイスからのデータの取り込み、キャッシュ、データの読み込み、一時テーブルやテーブル変数のシナリオなどがあります。
- "*クラスター化列ストア インデックス*": ストレージのフットプリントを減らし (最大 10 倍)、レポートと分析のクエリのパフォーマンスを向上させます。 データ マートでファクト テーブルと共に使用してデータベースにより多くのデータを格納し、パフォーマンスを向上させることができます。 さらに、オペレーション データベースで履歴データと共に使用してアーカイブし、最大で 10 倍のデータのクエリを実行可能にすることができます。
- HTAP 用の "*非クラスター化列ストア インデックス*": オペレーション データベースに直接クエリを実行して、ビジネスのリアルタイムの情報を取得します。抽出、変換、ロード (ETL) の高コストなプロセスを実行してデータ ウェアハウスが設定されるまで待機する必要はありません。 非クラスター化列ストア インデックスにより、OLTP データベースで非常に高速の分析クエリを実行しながら、運用ワークロードの影響を軽減できます。
- インメモリ OLTP と列ストア インデックスを組み合わせることもできます。 列ストア インデックス付きのメモリ最適化テーブルを使用できます。 これを使用すると、高速なトランザクション処理を実行できると同時に、同じデータに対して分析クエリを迅速に実行できます。

列ストア インデックスは 2012 年以降、インメモリ OLTP は 2014 年以降、SQL Server 製品の一部です。 Azure SQL Database と SQL Server では、インメモリ テクノロジの同一の実装が使用されています。 今後、これらのテクノロジの新しい機能は最初に Azure SQL Database でリリースされてから、SQL Server でリリースされます。

このトピックでは、Azure SQL Database に固有のインメモリ OLTP と列ストア インデックスの側面について説明し、サンプルも示します。 まず、これらのテクノロジがストレージに及ぼす影響と、データ サイズの上限について説明します。 次に、これらのテクノロジを活用するデータベースを、異なる価格レベルの間で移動する際の管理方法を説明します。 最後に、インメモリ OLTP と列ストア インデックスを Azure SQL Database で使用する方法を示す&2; つのサンプルを確認します。

詳細については、次のリソースを参照してください。

テクノロジについての詳細な情報:

- [インメモリ OLTP の概要と使用シナリオ](https://msdn.microsoft.com/library/mt774593.aspx) (開始するためのお客様の導入事例と情報への参照)
- [インメモリ OLTP のドキュメント](http://msdn.microsoft.com/library/dn133186.aspx)
- [列ストア インデックスの説明](https://msdn.microsoft.com/library/gg492088.aspx)
- [リアルタイム運用分析](https://msdn.microsoft.com/library/dn817827.aspx)とも呼ばれる、ハイブリッド トランザクション/分析処理 (HTAP)

インメモリ OLTP の簡単な手引き: [クイック スタート 1: T-SQL のパフォーマンスを高速化するためのインメモリ OLTP テクノロジ](http://msdn.microsoft.com/library/mt694156.aspx) (作業の開始に役立つ記事)

テクノロジについての詳細なビデオ:

- [Azure SQL Database のインメモリ OLTP](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (パフォーマンス上のメリットと、こうした結果を自身で再現する手順を示しています)
- [In-Memory OLTP Videos: What it is and When/How to use it (インメモリ OLTP のビデオ: 概要と使用方法)](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/03/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)
- [Ignite 2016 からの列ストア インデックス: インメモリ分析 (つまり列ストア インデックス) のビデオ](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/04/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

## <a name="storage-and-data-size"></a>ストレージとデータのサイズ

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>インメモリ OLTP のデータのサイズとストレージ上限

インメモリ OLTP には、ユーザー データの格納に使用されるメモリ最適化テーブルが含まれています。 これらのテーブルは、メモリに格納する必要があります。 メモリは SQL Database サービスで直接管理するため、ユーザー データについてクォータの概念があります。 この考え方は、"*インメモリ OLTP ストレージ*" と呼ばれます。

サポートされている各スタンドアロン データベースの価格レベルと各エラスティック プールの価格レベルには、一定量のインメモリ OLTP ストレージが含まれます。 この記事の執筆時点では、125 のデータベース トランザクション ユニット (DTU) またはエラスティック データベース トランザクション ユニット (eDTU) ごとに 1 GB のストレージが提供されます。

[SQL Database のサービス レベル](sql-database-service-tiers.md)に関する記事には、サポートされるスタンドアロン データベースとエラスティック プールの価格レベルごとに使用できるインメモリ OLTP ストレージの公式の一覧があります。

インメモリ OLTP ストレージ上限では、以下が考慮されます。

- メモリ最適化テーブルとテーブル変数内のアクティブなユーザー データの行。 古い行バージョンはこの上限では考慮されません。
- メモリ最適化テーブル上のインデックス。
- ALTER TABLE 操作の運用上のオーバーヘッド。

上限に達した場合、クォータ不足エラーが発生し、データの挿入や更新ができなくなります。 これを軽減するには、データを削除するか、データベースまたはプールの価格レベルを上げます。

インメモリ OLTP のストレージ使用率を監視する方法と、ほぼ上限に達したときのアラートを構成する方法の詳細については、[インメモリ ストレージの監視](sql-database-in-memory-oltp-monitoring.md)に関するページを参照してください。

#### <a name="about-elastic-pools"></a>エラスティック プールについて

エラスティック プールでは、インメモリ OLTP ストレージはプール内のすべてのデータベースで共有されます。 したがって、1 つのデータベースでの使用が他のデータベースに影響を及ぼす可能性があります。 これに対する軽減策は次の&2; つです。

- データベースに対し、プール全体の eDTU 数よりも少ない最大 eDTU を構成します。 これにより、プール内のすべてのデータベースでインメモリ OLTP のストレージ使用率について、この eDTU に対応する上限が設定されます。
- 0 より大きい最小 eDTU を構成します。 これにより、プール内の各データベースに、構成された最小 eDTU に対応する利用可能なインメモリ OLTP ストレージの量が確保されます。

### <a name="data-size-and-storage-for-columnstore-indexes"></a>列ストア インデックスのデータ サイズとストレージ

列ストア インデックスはメモリに収まる必要がありません。 そのため、インデックス サイズの唯一の上限は、[SQL Database のサービス レベル](sql-database-service-tiers.md)に関する記事で記述されているデータベース全体の最大サイズです。

クラスター化列ストア インデックスを使用する場合、ベース テーブル ストレージでは列圧縮が使用されます。 これにより、ユーザー データのストレージ フットプリントが大幅に削減されるため、データベースにより多くのデータを格納できます。 これは、[列アーカイブ圧縮](https://msdn.microsoft.com/library/cc280449.aspx#Using Columnstore and Columnstore Archive Compression)でさらに拡張できます。 実行できる圧縮の量はデータの性質に依存しますが、10 倍の圧縮は珍しくありません。

たとえば、最大サイズが 1 テラバイト (TB) のデータベースがあり、列ストア インデックスを使用して 10 倍の比率で圧縮した場合、データベースに合計 10 TB のユーザー データを書き込むことができます。

非クラスター化列ストア インデックスを使用する場合、ベース テーブルは従来の行ストア形式のままで格納されます。 そのため、ストレージはクラスター化列ストア インデックスほど大きく節約されません。 ただし、多数の従来の非クラスター化インデックスを&1; つの列ストア インデックスに置き換えても、テーブルのストレージ フットプリントにおける節約全体を確認できます。

## <a name="moving-databases-that-use-in-memory-technologies-between-pricing-tiers"></a>インメモリ テクノロジが使用されているデータベースを価格レベルをまたいで移動

より高い価格レベルは、常により多くの機能とリソースを備えているため、インメモリ テクノロジが使用されているデータベースの価格レベルを上げる際に特別な考慮事項はありません。 価格レベルを下げると、データベースに影響が生じる可能性があります。 これは特に、Premium から Standard または Basic に移動する場合と、インメモリ OLTP を使用するデータベースを下位の Premium レベルに移動する場合に当てはまります。 エラスティック プールの価格レベルを下げる場合、またはインメモリ テクノロジを使用しているデータベースを Standard または Basic のエラスティック プールに移動する場合にも同じ考慮事項が該当します。

### <a name="in-memory-oltp"></a>インメモリ OLTP

"*Basic または Standard へのダウングレード*": Standard または Basic レベルのデータベースでは、インメモリ OLTP はサポートされていません。 さらに、インメモリ OLTP オブジェクトがあるデータベースを Standard または Basic レベルに移動することはできません。

データベースを Standard または Basic にダウングレードする前に、すべてのメモリ最適化テーブルとテーブルの種類に加えて、すべてのネイティブ コンパイル T-SQL モジュールを削除してください。

特定のデータベースがインメモリ OLTP をサポートしているかどうかをプログラムによって確認する方法があります。 次の Transact-SQL クエリを実行できます。

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

クエリが **1** を返す場合、インメモリ OLTP はこのデータベースでサポートされています。


"*下位の Premium レベルへのダウングレード*": メモリ最適化テーブルのデータは、データベースの価格レベルに関連するインメモリ OLTP ストレージ内に格納するか、エラスティック プールで使用できる必要があります。 価格レベルを下げようとしたり、使用できるインメモリ OLTP ストレージが十分ではないプールにデータベースを移動しようとしたりすると、操作が失敗します。

### <a name="columnstore-indexes"></a>列ストア インデックス

"*Basic または Standard へのダウングレード*": Standard または Basic レベルのデータベースでは、列ストア インデックスはサポートされていません。 Standard または Basic にデータベースをダウングレードすると、列ストア インデックスを使用できなくなります。 つまり、クラスター化列ストア インデックスを使用している場合は、テーブル全体が使用できなくなります。

データベースを Standard または Basic にダウングレードする前に、クラスター化列ストア インデックスをすべて削除してください。

"*下位の Premium レベルへのダウングレード*": これは、データベース全体がターゲット価格レベルの最大データベース サイズまたはエラスティック プールの使用可能なストレージに収まっている限りは成功します。 列ストア インデックスからの特定の影響はありません。


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1.インメモリ OLTP のサンプルをインストールする

[Azure Portal](https://portal.azure.com/) で数回クリックするだけで、AdventureWorksLT [V12] のサンプル データベースを作成できます。 このセクションの手順では、インメモリ OLTP オブジェクトを使用して AdventureWorksLT データベースを強化する方法について説明し、パフォーマンス上のメリットを示します。

より簡潔でありながら見栄えの良いインメモリ OLTP のパフォーマンス デモについては、次を参照してください。

- リリース: [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- ソース コード: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>インストール手順

1. [Azure Portal](https://portal.azure.com/) で、V12 サーバー上に Premium データベースを作成します。 **ソース** を AdventureWorksLT [V12] サンプル データベースに設定します。 詳細な手順については、[最初の Azure SQL データベースの作成](sql-database-get-started.md)に関する記事を参照してください。

2. SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx)を使用して、データベースに接続します。

3. [インメモリ OLTP Transact-SQL スクリプト](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) をクリップボードにコピーします。 この T-SQL スクリプトによって、手順 1. で作成した AdventureWorksLT サンプル データベース内に、必要なインメモリ オブジェクトが作成されます。

4. T-SQL スクリプトを SSMS に貼り付け、スクリプトを実行します。 `MEMORY_OPTIMIZED = ON` 句の CREATE TABLE ステートメントが重要です。 次に例を示します。


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>エラー 40536


T-SQL スクリプトを実行するときにエラー 40536 が発生する場合は、次の T-SQL スクリプトを実行し、データベースがインメモリをサポートしているかどうかを確認します。


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


結果が **0** の場合、インメモリがサポートされていないことを示します。**1** の場合はサポートされています。 問題を診断するには、データベースを Premium サービス レベルにします。


#### <a name="about-the-created-memory-optimized-items"></a>作成されるメモリ最適化項目の概要

**テーブル**: このサンプルには、次のメモリ最適化テーブルが含まれています。

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


SSMS で**オブジェクト エクスプローラー**を使用してメモリ最適化テーブルを確認できます。 **[テーブル]** を右クリックし、 > **[フィルター]** > **[フィルターの設定]** > **[Is Memory Optimized (メモリ最適化済み)]** の順に選択します。 値は 1 です。


または、次のようにカタログ ビューにクエリを実行できます。


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**ネイティブ コンパイル ストアド プロシージャ**: SalesLT.usp_InsertSalesOrder_inmem は、カタログ ビューのクエリを使用して確認できます。


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>サンプルの OLTP ワークロードを実行する

次の&2; つの *ストアド プロシージャ* の違いは、1 つ目のプロシージャはメモリ最適化バージョンのテーブルを使用し、2 つ目のプロシージャは通常のディスク上のテーブルを使用している点です。

- SalesLT**.**usp_InsertSalesOrder**_inmem**
- SalesLT**.**usp_InsertSalesOrder**_ondisk**


このセクションでは、便利な **ostress.exe** ユーティリティを使用して、負荷が高い状態で&2; つのストアド プロシージャを実行する方法について説明します。 2 つのストレス実行が完了するまでの時間を比較することができます。


ostress.exe を実行する場合、次の両方について指定したパラメーター値を渡すことをお勧めします。

- 多数の同時接続を実行するには、-n100 を使用します。
- 各接続を数百回ループさせるには、-r500 を使用します。


一方、すべてが適切に動作するようにするには、-n10、-r50 などの小さな値から始めることもできます。


### <a name="script-for-ostressexe"></a>ostress.exe のスクリプト


このセクションでは、ostress.exe コマンド ラインに埋め込まれた T-SQL スクリプトを示します。 このスクリプトでは、インストールした T-SQL スクリプトで作成されたアイテムを使用します。


次のスクリプトでは、5 行のアイテムがあるサンプルの販売注文を、次のメモリ最適化 *テーブル*に挿入します。

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```
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


### <a name="install-rml-utilities-and-ostress"></a>RML ユーティリティと ostress をインストールする


Azure 仮想マシン (VM) で ostress.exe を実行する計画を立てるのが理想的です。 AdventureWorksLT データベースがある Azure リージョンと同じリージョンに [Azure VM](https://azure.microsoft.com/documentation/services/virtual-machines/) を作成します。 代わりにノートパソコンで ostress.exe を実行することもできます。


VM または選択した任意のホストに、Replay Markup Language (RML) ユーティリティをインストールします。 このユーティリティに ostress.exe が含まれています。

詳細については、次を参照してください。
- 「[インメモリ OLTP のサンプル データベース](http://msdn.microsoft.com/library/mt465764.aspx)」にある ostress.exe の説明。
- [インメモリ OLTP のサンプル データベース](http://msdn.microsoft.com/library/mt465764.aspx)。
- [ostress.exe のインストールに関するブログ](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)。



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>最初に *_inmem* stress ワークロードを実行する


*RML コマンド プロンプト* ウィンドウを使用して、ostress.exe コマンド ラインを実行できます。 コマンドライン パラメーターは ostress に次のことを行うように求めます。

- 100 個の接続を同時に実行する (-n100)。
- 各接続に T-SQL スクリプトを 50 回実行させる (-r50)。


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


前述の ostress.exe コマンド ラインを実行するには:


1. SSMS で次のコマンドを実行してデータベースのデータ コンテンツをリセットし、前回の実行で挿入されたすべてのデータを削除します。
```
EXECUTE Demo.usp_DemoReset;
```

2. 上記の ostress.exe コマンドラインのテキストをクリップボードにコピーします。

3. パラメーター -S -U -P -d の `<placeholders>` を実際の正しい値に置き換えます。

4. 編集したコマンドラインを RML コマンド ウィンドウで実行します。


#### <a name="result-is-a-duration"></a>結果は期間


ostress.exe が完了すると、RML コマンド ウィンドウに表示される出力の最終行に実行時間が出力されます。 たとえば、短いテストの場合、約 1.5 分かかります。

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>リセット、*_ondisk* の編集、再実行


*_inmem* 実行の結果を取得したら、*_ondisk* 実行に次の手順を実行します。


1. SSMS で次のコマンドを実行してデータベースをリセットし、前回の実行で挿入されたすべてのデータを削除します。
```
EXECUTE Demo.usp_DemoReset;
```

2. ostress.exe コマンド ラインを編集して、すべての *_inmem* を *_ondisk* に置き換えます。

3. ostress.exe を再び実行し、期間の結果を取得します。

4. (大量のテスト データとなる可能性があるデータを確実に削除するために) もう一度データベースをリセットします。


#### <a name="expected-comparison-results"></a>予想される比較結果

インメモリ テストの結果、ostress をデータベースと同じ Azure リージョンにある Azure VM で実行した場合、この単純なワークロードではパフォーマンスが **9 倍**向上することがわかりました。

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2.インメモリ分析のサンプルをインストールする


このセクションでは、列ストア インデックスと従来の B ツリー インデックスを使用した場合の IO と統計情報の結果を比較します。


OLTP ワークロードのリアルタイム分析では、多くの場合、非クラスター化列ストア インデックスを使用するのが最適です。 詳細については、[列ストア インデックスの説明](http://msdn.microsoft.com/library/gg492088.aspx)に関するページを参照してください。



### <a name="prepare-the-columnstore-analytics-test"></a>列ストア分析テストを準備する


1. Azure Portal を使用して、サンプルから最新の AdventureWorksLT データベースを作成します。
 - 正確な名前を使用します。
 - 任意の Premium サービス階層を選択します。

2. [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) をクリップボードにコピーします。
 - この T-SQL スクリプトによって、手順 1. で作成した AdventureWorksLT サンプル データベース内に、必要なインメモリ オブジェクトが作成されます。
 - このスクリプトでは、Dimension テーブルと&2; つのファクト テーブルを作成します。 fact テーブルには、それぞれ 350 万行のデータが設定されています。
 - スクリプトが完了するには約 15 分かかります。

3. T-SQL スクリプトを SSMS に貼り付け、スクリプトを実行します。 次のように、**CREATE INDEX** ステートメントの **COLUMNSTORE** キーワードが重要です。<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. AdventureWorksLT を互換性レベル 130 に設定します。<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    レベル 130 はインメモリ機能に直接、関係ありません。 しかし、レベル 130 は一般に、120 の場合よりも高いクエリ パフォーマンスを提供します。


#### <a name="key-tables-and-columnstore-indexes"></a>重要なテーブルと列ストア インデックス


- dbo.FactResellerSalesXL_CCI は、クラスター化列ストア インデックスがあるテーブルで、"*データ*" レベルで高度に圧縮されます。

- dbo.FactResellerSalesXL_PageCompressed は、同等の標準のクラスター化されたインデックスがあるテーブルで、*ページ* レベルでのみ圧縮されます。


#### <a name="key-queries-to-compare-the-columnstore-index"></a>列ストア インデックスを比較する重要なクエリ


パフォーマンスの改善を確認できるいくつかの T-SQL クエリの種類については、[こちら](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql)を参照してください。 T-SQL スクリプトの手順 2. では、このペアのクエリに注意してください。 2 つのクエリの違いは次の&1; 行のみです。


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


クラスター化列ストア インデックスは FactResellerSalesXL\_CCI テーブルにあります。

次の T-SQL スクリプトの抜粋では、各テーブルのクエリの IO と TIME の統計情報を出力します。


```
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

P2 価格レベルのデータベースでは、クラスター化列ストア インデックスを使用したクエリについて、従来のインデックスと比較して約&9; 倍のパフォーマンス向上が期待できます。 P15 では、列ストア インデックスを使用することで約 57 倍のパフォーマンス向上が期待できます。



## <a name="next-steps"></a>次のステップ

- [クイック スタート 1: T-SQL のパフォーマンスの高速化のためのインメモリ OLTP テクノロジ](http://msdn.microsoft.com/library/mt694156.aspx)

- [既存の Azure SQL アプリケーションにおけるインメモリ OLTP の使用](sql-database-in-memory-oltp-migration.md)

- インメモリ OLTP の[インメモリ OLTP ストレージの監視](sql-database-in-memory-oltp-monitoring.md)


## <a name="additional-resources"></a>その他のリソース

#### <a name="deeper-information"></a>詳細情報

- [Learn how Quorum doubles key database’s workload while lowering DTU by 70% with In-Memory OLTP in SQL Database (クォーラムが SQL Database でインメモリ OLTP を使用して DTU の 70% を削減しながら主要なデータベースのワークロードを 2 倍にする方法について)](https://customers.microsoft.com/en-US/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [インメモリ OLTP の詳細情報](http://msdn.microsoft.com/library/dn133186.aspx)

- [列ストア インデックスの詳細情報](https://msdn.microsoft.com/library/gg492088.aspx)

- [リアルタイム運用分析の詳細情報](http://msdn.microsoft.com/library/dn817827.aspx)

- (インメモリ OLTP によってパフォーマンスが大幅に向上する一般的なワークロード パターンが記載されている) [一般的なワークロード パターンと移行の考慮事項](http://msdn.microsoft.com/library/dn673538.aspx)に関するホワイトペーパーを参照してください

#### <a name="application-design"></a>アプリケーションの設計

- [インメモリ OLTP (インメモリ最適化)](http://msdn.microsoft.com/library/dn133186.aspx)

- [既存の Azure SQL アプリケーションにおけるインメモリ OLTP の使用](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>ツール

- [Azure ポータル](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx)



<!--HONumber=Jan17_HO2-->


