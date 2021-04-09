---
title: ワークロード管理用のリソース クラス
description: Azure Synapse Analytics で、リソース クラスを使用して、クエリのコンカレンシーとコンピューティング リソースを管理するためのガイダンスです。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 7714ce748eb172565357723924ab2212e9559e1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685329"
---
# <a name="workload-management-with-resource-classes-in-azure-synapse-analytics"></a>Azure Synapse Analytics のリソース クラスを使用したワークロード管理

Azure Synapse で、リソース クラスを使用して、Synapse SQL プール クエリのメモリとコンカレンシーを管理するためのガイダンスです。  

## <a name="what-are-resource-classes"></a>リソース クラスについて

クエリのパフォーマンス能力は、ユーザーのリソース クラスによって決定されます。  リソース クラスは、クエリ実行のためにコンピューティング リソースとコンカレンシーを制御する Synapse SQL プールのあらかじめ決定されたリソース制限です。 リソース クラスを利用して、同時実行されるクエリの数と、各クエリに割り当てられているコンピューティング リソースの数に制限を設定することで、クエリに対するリソースを構成することができます。  メモリとコンカレンシーの間にはトレードオフがあります。

- リソース クラスが少数の場合、クエリごとの最大メモリは減少しますが、コンカレンシーは増えます。
- より大規模なリソース クラスでは、クエリあたりの最大メモリは増えますが、コンカレンシーは減ります。

リソース クラスは 2 種類あります｡

- 静的リソース クラス｡コンカレンシー数が高くデータセットのサイズが固定のでコンカレンシーが増える場合に適しています｡
- 動的リソース クラス。サービス レベルのスケール アップに応じてデータ セットのサイズが大きくなり、パフォーマンスを高める必要がある場合に適しています。

リソース クラスでは、リソースの消費を測定するのにコンカレンシー スロットを使用します。  [コンカレンシー スロット](#concurrency-slots)については、この記事で後述します。

- リソース クラスのリソース使用率を表示するには、「[Memory and concurrency limits](memory-concurrency-limits.md)」 (メモリとコンカレンシーの制限) を参照してください。
- リソース クラスを調整するには、別のユーザーとしてクエリを実行するか、[現在のユーザーのリソース クラス](#change-a-users-resource-class) メンバーシップを変更します。

### <a name="static-resource-classes"></a>静的リソース クラス

静的リソース クラスは、現在のパフォーマンス レベルに関係なく、同じメモリ量を割り当てます。これは [Data Warehouse ユニット](what-is-a-data-warehouse-unit-dwu-cdwu.md)で測定されます。 パフォーマンス レベルに関係なく、クエリには同じ量のメモリが割り当てられるため、[データ ウェアハウスのスケール アウト](quickstart-scale-compute-portal.md)によってリソース クラス内でより多くのクエリが実行できるようになります。  データ ボリュームが既知でサイズが一定の場合は､静的リソース クラスが適しています｡

これらの静的なリソース クラスは、これらの事前定義されたデータベース ロールを使用して実装されます。

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>動的リソース クラス

動的リソース クラスは、現在のサービス レベルに応じたメモリ量を割り当てます。 静的リソース クラスがコンカレンシーが多く､かつデータ ボリュームが静的である場合に有用であるのに対し､動的リソース クラスはデータ量が増大または変化する場合に有用です｡  より大きなサービス レベルにスケールアップすると、クエリは自動的により多くのメモリを取得します。  

動的なリソース クラスは、次の定義済みのデータベース ロールによって実装されます。

- smallrc
- mediumrc
- largerc
- xlargerc

各リソース クラスのメモリ割り当ては、次のようになります。

| サービス レベル  | smallrc           | mediumrc               | largerc                | xlargerc               |
|:--------------:|:-----------------:|:----------------------:|:----------------------:|:----------------------:|
| DW100c         | 25%               | 25%                    | 25%                    | 70%                    |
| DW200c         | 12.5%             | 12.5%                  | 22%                    | 70%                    |
| DW300c         | 8%                | 10%                    | 22%                    | 70%                    |
| DW400c         | 6.25%             | 10%                    | 22%                    | 70%                    |
| DW500c         | 5%                | 10%                    | 22%                    | 70%                    |
| DW1000c から<br> DW30000c | 3%       | 10%                    | 22%                    | 70%                    |

### <a name="default-resource-class"></a>既定のリソース クラス

既定では、各ユーザーは動的リソース クラス **smallrc** のメンバーです。

サービス管理者のリソース クラスは smallrc に固定され、変更できません。  サービス管理者はプロビジョニング プロセス中に作成されるユーザーです。  このコンテキストでのサービス管理者とは、新しいサーバーで新しい Synapse SQL プールを作成するときに、[サーバー管理者ログイン] に指定されるログインです。

> [!NOTE]
> Active Directory 管理者として定義されたユーザーまたはグループは、サービス管理者でもあります。
>
>

## <a name="resource-class-operations"></a>リソース クラスの操作

リソース クラスは、データ管理と操作アクティビティのパフォーマンスを向上するために設計されています。 複雑なクエリも、大規模なリソース クラス下で実行するとメリットがあります。 たとえば、クエリをメモリ内で実行できるほどリソース クラスが十分に大きい場合、大規模な結合と並べ替えのクエリ パフォーマンスは向上します。

### <a name="operations-governed-by-resource-classes"></a>リソース クラスによって管理される操作

リソース クラスによって、次の操作が制御されます。

- INSERT-SELECT、UPDATE、DELETE
- SELECT (ユーザー テーブルのクエリを実行する場合)
- ALTER INDEX - REBUILD または REORGANIZE
- ALTER TABLE REBUILD
- CREATE INDEX
- CREATE CLUSTERED COLUMNSTORE INDEX
- CREATE TABLE AS SELECT (CTAS)
- データの読み込み
- Data Movement Service (DMS) によって実行されるデータ移動操作

> [!NOTE]  
> 動的管理ビュー (DMV) やその他のシステム ビューの SELECT ステートメントは、コンカレンシーの制限の対象になりません。 ユーザーは、システムで実行されるクエリの数にとらわれずにシステムを監視できます。
>
>

### <a name="operations-not-governed-by-resource-classes"></a>リソース クラスによって管理されない操作

一部のクエリは、ユーザーが大規模リソース クラスのメンバーであっても、常に smallrc リソース クラスで実行されます。 これらの対象外のクエリは、コンカレンシーの制限にはカウントされません。 たとえば、コンカレンシーの制限が 16 の場合、利用可能なコンカレンシー スロットに影響を与えることなく、システム ビューから多数のユーザーを選択できます。

次のステートメントはリソース クラスの対象外であり、常に smallrc で実行されます。

- CREATE または DROP TABLE
- このパーティション テーブルでは、ALTER TABLE ...SWITCH、SPLIT、または MERGE PARTITION
- ALTER INDEX DISABLE
- DROP INDEX
- CREATE、UPDATE、または DROP STATISTICS
- TRUNCATE TABLE
- ALTER AUTHORIZATION
- CREATE LOGIN
- CREATE、ALTER、または DROP USER
- CREATE、ALTER、または DROP PROCEDURE
- CREATE または DROP VIEW
- INSERT VALUES
- システム ビューおよび DMV からの SELECT
- EXPLAIN
- DBCC

<!--
Removed as these two are not confirmed / supported under Azure Synapse Analytics
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>コンカレンシー スロット

コンカレンシー スロットは、クエリの実行に使用可能なリソースを追跡するために便利な方法です。 座席数が制限されているため、コンサートの座席を予約するチケットに似ています。 データ ウェアハウスあたりのコンカレンシー スロットの合計数は、サービス レベルによって決まります。 クエリが実行を開始する前に、十分な数のコンカレンシー スロットを予約できる必要があります。 クエリが完了すると、そのコンカレンシー スロットは解放されます。  

- 10 個のコンカレンシー スロットを使用して実行されるクエリは、2 つのコンカレンシー スロットを使用して実行されるクエリよりも 5 倍のコンピューティング リソースにアクセスできます。
- クエリあたり 10 個のコンカレンシー スロットが必要で 40 のコンカレンシー スロットがある場合は、同時に実行できるのは 4 つのクエリのみになります。

リソースが管理されているクエリのみが、コンカレンシー スロットを使用します。 システム クエリやいくつかの重要度の低いクエリは、スロットを使用しません。 使用されるコンカレンシー スロットの正確な数は、クエリのリソース クラスによって決まります。

## <a name="view-the-resource-classes"></a>リソース クラスの参照

リソース クラスは、定義済みのデータベース ロールとして実装されています。 リソース クラスには、動的と静的の 2 種類があります。 リソース クラスの一覧を表示するには、次のクエリを使用します。

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>ユーザーのリソース クラスの変更

リソース クラスは、ユーザーにデータベース ロールを割り当てることによって実装されます。 ユーザーがクエリを実行すると、クエリはユーザーのリソース クラスで実行されます。 たとえば、ユーザーが staticrc10 データベース ロールのメンバーの場合、クエリは少量のメモリを使用して実行されます。 データベース ユーザーが xlargerc または staticrc80 データベース ロールのメンバーの場合、クエリは大量のメモリを使用して実行されます。

ユーザーのリソース クラスを大きくするには、[sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) を使用して、ユーザーをより大きなリソース クラスのデータベース ロールに追加します。  次のコードでは、largerc データベース ロールにユーザーを追加します。  各要求はシステム メモリの 22% を取得します。

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

リソース クラスを小さくするには、[sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) を使用します。  'loaduser' が他のどのリソース クラスのメンバーでもない場合、既定の smallrc リソース クラスに、3% のメモリを割り当てられて追加されます。  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>リソース クラスの優先順位

ユーザーは、複数のリソース クラスのメンバーになることができます。 ユーザーが 1 つ以上のリソース クラスに属す場合:

- 動的リソース クラスが静的リソースクラスに優先します。 たとえば、ユーザーが mediumrc (動的) と staticrc80 (静的) のメンバーの場合、クエリは mediumrc で実行されます。
- 大規模リソース クラスが小規模リソースクラスに優先します。 たとえば、ユーザーが mediumrc と largerc のメンバーの場合、クエリは largerc で実行されます。 同様に、ユーザーが staticrc20 と statirc80 の両方のメンバーの場合、クエリは staticrc80 に対するリソースの割り当てを使用して実行されます。

## <a name="recommendations"></a>Recommendations

>[!NOTE]
>ワークロードと予測可能なパフォーマンスをより詳細に制御するために、ワークロード管理機能 [(ワークロードの分離](sql-data-warehouse-workload-isolation.md)、[分類](sql-data-warehouse-workload-classification.md)および[重要度](sql-data-warehouse-workload-importance.md)) を活用することを検討してください。  
>
>

お勧めするのは、特定の種類のクエリまたは読み込み操作を実行する専用のユーザーを作成することです。 頻繁にリソース クラスを変更する代わりに、そのユーザーに永続的なリソース クラスを指定します。 静的リソース クラスを指定すると、ワークロード全体が制御しやすくなります。したがって、動的リソース クラスを検討する前に、静的リソース クラスを使用することをお勧めします。

### <a name="resource-classes-for-load-users"></a>読み込みユーザー用のリソース クラス

`CREATE TABLE` は、既定により、クラスター化列ストア インデックスを使用します。 列ストア インデックスへのデータの圧縮はメモリを消費する操作であり、メモリ不足によってインデックスの品質が低下する可能性があります。 メモリ不足により、データの読み込み時により高いリソース クラスが必要になる可能性があります。 読み込み時に十分なメモリがあることを保証するために、読み込みを実行するように指定されたユーザーを作成し、そのユーザーに高いリソース クラスを割り当てることができます。

読み込みを効率的に処理するために必要なメモリは、読み込まれるテーブルの性質とデータ量によって異なります。 メモリ要件の詳細については、「[列ストアの行グループの品質を最大限にする](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)」を参照してください。

メモリ要件を決定したら、静的または動的なリソース クラスのどちらを読み込みユーザーに割り当てるかを選択します。

- テーブルのメモリ要件が特定の範囲に収まる場合は、静的リソース クラスを使用します。 読み込みは適切なメモリで実行されます。 データ ウェアハウスをスケーリングしても、読み込みにさらに多くのメモリが必要になることはありません。 静的リソース クラスを使用しているため、メモリの割り当てが変化することはありません。 この一貫性によってメモリは節約され、より多くのクエリを同時に実行できます。 新しいソリューションでは、制御しやすいため、まず静的リソース クラスを使用することをお勧めします。
- テーブルのメモリ要件が大きく異なる場合は、動的なリソース クラスを使用します。 読み込みで、現在の DWU または cDWU レベルによって提供されるメモリを上回るメモリが必要になることがあります。 データ ウェアハウスをスケーリングすると、読み込み操作に多くのメモリが追加され、読み込みを高速で実行できます。

### <a name="resource-classes-for-queries"></a>クエリ ユーザー用のリソース クラス

クエリには、コンピューティング集中型とそうでないものがあります。  

- クエリは複雑だが、高いコンカレンシーを必要としない場合は、動的リソース クラスを選択します。  たとえば、日次または週次レポートの生成は、リソースに対する常時発生するものではないニーズです。 レポートが大量のデータを処理する場合は、データ ウェアハウスをスケーリングすることで、、ユーザーの既存のリソース クラスにより多くのメモリが提供されます。
- 要求されるリソースが日中変化する場合は、静的リソース クラスを選択します。 たとえば、データ ウェアハウスに対して多数のユーザーがクエリを実行する場合、静的リソース クラスは功を奏します。 データ ウェアハウスをスケーリングしても、ユーザーに割り当てられるメモリの量は変化しません。 その結果、より多くのクエリをシステムで並列に実行できます。

適切なメモリ許可は、クエリされるデータの量、テーブル スキーマの性質、さまざまな結合、選択、およびグループ述語などの多くの要因に依存します。 通常は、メモリ割り当て量を増やすと、クエリの実行時間が短縮されますが、全体的なコンカレンシーは減少します。 コンカレンシーが問題でない場合は、メモリを過剰に割り当てても、スループットに影響が出ることはありません。

パフォーマンスを調整するには、複数のリソース クラスを使用します。 次のセクションで、最適なリソース クラスを見つけるために役立つストアド プロシージャについて説明します。

## <a name="example-code-for-finding-the-best-resource-class"></a>最適なリソース クラスを見つけるためのコードの例

次の指定されたストアド プロシージャを使用して、特定の SLO のコンカレンシーとリソース クラスあたりのメモリ割り当てを割り出せば、特定のリソース クラスの非分割 CCI テーブルでのメモリ消費量の多い CCI 操作に最適なリソース クラスを特定することができます。

このストアド プロシージャの目的を次に示します。

1. 特定の SLO のリソース クラスあたりのコンカレンシーとメモリ許可を特定すること。 次の例に示すように、スキーマとテーブル名の両方に NULL を指定する必要があります。  
2. 特定のリソース クラスの非分割 CCI テーブルでのメモリ消費量の多い CCI 操作 (読み込み、テーブルのコピー、インデックスの再構築など) にとって、最適なリソース クラスを特定すること。 このストアド プロシージャでは、必要なメモリ許可を割り出すために、テーブル スキーマを使用します。

### <a name="dependencies--restrictions"></a>依存関係と制限事項

- このストアド プロシージャは、分割型 CCI テーブルのメモリ要件を計算するものではありません。
- このストアド プロシージャでは、CTAS/INSERT-SELECT の SELECT 部分のメモリ要件は計算に入れられておらず、単純な SELECT であると想定しています。
- このストアド プロシージャでは、このストアド プロシージャが作成されたセッションで使用できる一時テーブルを使用します。
- このストアド プロシージャは、現在の環境 (ハードウェア構成、DMS 構成など) に依存しており、そのいずれかが変更された場合は正しく動作しません。  
- このストアド プロシージャは、既存のコンカレンシーの制限に依存しており、それが変更された場合は正しく動作しません。  
- このストアド プロシージャは、既存のリソース クラスに依存しており、それが変更された場合は正しく動作しません。  

>[!NOTE]  
>指定されたパラメーターを使用してストアド プロシージャを実行しても出力が得られない場合は、次の 2 つの理由が考えられます。
>
>1. DW パラメーターに無効な SLO 値が含まれている
>2. テーブルに対する CCI 操作にマッチするリソース クラスがありません。
>
>たとえば、DW100c で利用可能な最大のメモリ許可が 1 GB であり、テーブル スキーマのサイズが 1 GB の要件を超える場合です。

### <a name="usage-example"></a>使用例

構文:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU:DW DB から現在の DWU を抽出するため NULL パラメーターを指定するか、'DW100c' の形式でサポートされている DWU を指定します。
2. @SCHEMA_NAME:テーブルのスキーマ名を指定します。
3. @TABLE_NAME:必要なテーブル名を指定します。

このストアド プロシージャの実行例:

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000c', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000c', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

次のステートメントは、前の例で使用される Table1 を作成します。
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>ストアド プロシージャの定義

```sql
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(8),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS

IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.

SELECT @DWU = 'DW'+ CAST(CASE WHEN Mem> 4 THEN Nodes*500
  ELSE Mem*100
  END AS VARCHAR(10)) +'c'
    FROM (
      SELECT Nodes=count(distinct n.pdw_node_id), Mem=max(i.committed_target_kb/1000/1000/60)
        FROM sys.dm_pdw_nodes n
        CROSS APPLY sys.dm_pdw_nodes_os_sys_info i
        WHERE type = 'COMPUTE')A
END

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END;

-- Creating ref. temp table (CTAS) to hold mapping info.
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
SELECT 'DW100c' AS DWU,4 AS max_queries,4 AS max_slots,1 AS slots_used_smallrc,1 AS slots_used_mediumrc,2 AS slots_used_largerc,4 AS slots_used_xlargerc,1 AS slots_used_staticrc10,2 AS slots_used_staticrc20,4 AS slots_used_staticrc30,4 AS slots_used_staticrc40,4 AS slots_used_staticrc50,4 AS slots_used_staticrc60,4 AS slots_used_staticrc70,4 AS slots_used_staticrc80
  UNION ALL
   SELECT 'DW200c',8,8,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW300c',12,12,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW400c',16,16,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW500c',20,20,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW1000c',32,40,1,4,8,28,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW1500c',32,60,1,6,13,42,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW2000c',48,80,2,8,17,56,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW2500c',48,100,3,10,22,70,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW3000c',64,120,3,12,26,84,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW5000c',64,200,6,20,44,140,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW6000c',128,240,7,24,52,168,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW7500c',128,300,9,30,66,210,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW10000c',128,400,12,40,88,280,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW15000c',128,600,18,60,132,420,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW30000c',128,1200,36,120,264,840,1,2,4,8,16,32,64,128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map  
AS
(
  SELECT CONVERT(varchar(20), 'SloDWGroupSmall') AS wg_name, slots_used_smallrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupMedium') AS wg_name, slots_used_mediumrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupLarge') AS wg_name, slots_used_largerc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupXLarge') AS wg_name, slots_used_xlargerc AS slots_used FROM alloc WHERE DWU = @DWU
  UNION ALL
  SELECT 'SloDWGroupC00',1
  UNION ALL
    SELECT 'SloDWGroupC01',2
  UNION ALL
    SELECT 'SloDWGroupC02',4
  UNION ALL
    SELECT 'SloDWGroupC03',8
  UNION ALL
    SELECT 'SloDWGroupC04',16
  UNION ALL
    SELECT 'SloDWGroupC05',32
  UNION ALL
    SELECT 'SloDWGroupC06',64
  UNION ALL
    SELECT 'SloDWGroupC07',128
)

-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.slots_used * 250 AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.slots_used * 250 AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.slots_used * 250 AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.slots_used * 250 AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.slots_used * 250 AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.slots_used * 250 AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.slots_used * 250 AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.slots_used * 250 AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.slots_used * 250 AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.slots_used * 250 AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.slots_used * 250 AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.slots_used * 250 AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used and m1.wg_name = 'SloDWGroupSmall'
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used and m2.wg_name = 'SloDWGroupMedium'
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used and m3.wg_name = 'SloDWGroupLarge'
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used and m4.wg_name = 'SloDWGroupXLarge'
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used and m5.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used and m6.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used and m7.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used and m8.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used and m9.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used and m10.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used and m11.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used and m12.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  WHERE   a1.DWU = @DWU
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;

-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239)
                                AND  co.max_length <= 32
                                THEN COUNT(co.column_id)
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239)
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id)
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as
(
SELECT  CASE
          WHEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000)) > 0
            AND CHARINDEX(@DWU,'c')=0
          THEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000))
          ELSE 1
        END AS multiplication_factor
)
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size
       , load_multiplier
       , #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="next-steps"></a>次のステップ

データベース ユーザーの管理とセキュリティの詳細については、[Synapse SQL でのデータベース保護](sql-data-warehouse-overview-manage-security.md)に関する記事を参照してください。 大規模なリソース クラスを使用してクラスター化列ストア インデックスの品質を向上させる方法については、[列ストアを圧縮するためのメモリの最適化](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)に関する記事を参照してください。
