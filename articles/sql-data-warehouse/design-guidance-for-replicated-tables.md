---
title: レプリケート テーブルの設計ガイダンス - Azure SQL Data Warehouse | Microsoft Docs
description: Azure SQL Data Warehouse スキーマでレプリケート テーブルを設計するための推奨事項。 
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/19/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: c622edc6c3a37b2bc71323cf0e2c155f7aec6e33
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479326"
---
# <a name="design-guidance-for-using-replicated-tables-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse でレプリケート テーブルを使用するための設計ガイダンス
この記事では、SQL Data Warehouse スキーマでレプリケート テーブルを設計するための推奨事項を紹介します。 これらの推奨事項を使用すると、データの移動が少なくなり、クエリの複雑さが軽減されることでクエリ パフォーマンスが向上します。

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>前提条件
この記事では、読者が SQL Data Warehouse のデータ分散とデータ移動の概念を理解していることを前提としています。  詳細については、[アーキテクチャ](massively-parallel-processing-mpp-architecture.md)に関する記事を参照してください。 

テーブル設計の一環として、ご利用のデータと、そのデータを照会する方法についてできる限り理解してください。  たとえば、次のような質問を考えてみます。

- テーブルの大きさはどの程度か。   
- どの程度の頻度でテーブルが更新されるか。   
- データ ウェアハウス内にファクト テーブルとディメンション テーブルがあるか。   

## <a name="what-is-a-replicated-table"></a>レプリケート テーブルとは
レプリケート テーブルには、各コンピューティング ノード上でアクセスできるテーブルの完全なコピーがあります。 テーブルをレプリケートすると、結合または集計の前に、コンピューティング ノード内のデータを転送する必要がなくなります。 テーブルには複数のコピーが含まれているため、テーブルのサイズが 2 GB 未満に圧縮されている場合にレプリケート テーブルが最も効果的に機能します。  2 GB はハード制限ではありません。  データが静的で変化しない場合は、さらに大きなテーブルをレプリケートできます。

次の図は、各コンピューティング ノード上でアクセスできるレプリケート テーブルを示したものです。 SQL Data Warehouse では、レプリケート テーブルは各コンピューティング ノード上のディストリビューション データベースに完全にコピーされます。 

![レプリケート テーブル](media/guidance-for-using-replicated-tables/replicated-table.png "レプリケート テーブル")  

レプリケート テーブルは、スター スキーマのディメンションのテーブルに適しています。 通常、ディメンション テーブルが結合されるファクト テーブルは、ディメンション テーブルとは異なる方法で分散されます。  ディメンションは、通常、複数のコピーの格納および保持を可能にするサイズです。 ディメンションは、顧客名、住所、製品の詳細など、変更頻度の低い説明的なデータを格納します。 変更頻度が低いというデータの性質により、レプリケート テーブルのメンテナンス回数は少なくなります。 

次の場合は、レプリケート テーブルの使用を検討してください。

- ディスク上のテーブル サイズが、行数には関係なく、2 GB 未満である。 テーブルのサイズを調べるには、次のように [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql) コマンドを使用することができます。`DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')` 
- データ移動を必要とする結合でテーブルが使用されている。 ハッシュ分散テーブルなど同じ列で分散されていないテーブルを結合して､1 つのラウンドロビン テーブルにする場合､クエリを完了するにはデータの移動が必要です｡  テーブルの 1 つが小さい場合は、レプリケート テーブルを検討してください。 ほとんどの場合、ラウンド ロビン テーブルの代わりにレプリケート テーブルを使用することをお勧めします。 クエリ プランでのデータ移動操作を表示するには、[sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) を使用してください。  BroadcastMoveOperation は､レプリケート テーブルを使用することで不要にできる代表的なデータ移動操作です｡  
 
次の場合、レプリケート テーブルでは最適なクエリ パフォーマンスが得られない可能性があります。

- テーブルで、頻繁な挿入、更新、削除操作が行われる。 これらのデータ操作言語 (DML) 操作では、レプリケート テーブルの再構築が必要となります。 頻繁に再構築すると、パフォーマンスが低下する場合があります。
- データ ウェアハウスが頻繁にスケーリングされる。 データ ウェアハウスをスケーリングすると、コンピューティング ノードの数が変わるため、レプリケート テーブルの再構築が発生します。
- テーブルに多数の列があっても、通常、データ操作でアクセスする列はごく少数である。 このシナリオでは、テーブル全体をレプリケートするのではなく、テーブルを分散し、頻繁にアクセスする列にインデックスを作成する方が効果的であると考えられます｡ クエリでデータ移動が必要な場合、SQL Data Warehouse は要求された列のデータを移動するだけです。 

## <a name="use-replicated-tables-with-simple-query-predicates"></a>単純なクエリ述語でレプリケート テーブルを使用する
テーブルを分散するかレプリケートするかを選択する前に、そのテーブルに対して実行を計画するクエリの種類について考えてみます。 できる限り、次のようにしてください。

- 等値や非等値など、単純なクエリ述語を使用したクエリには、レプリケート テーブルを使用します。
- LIKE や NOT LIKE など、複雑なクエリ述語を使用したクエリには、分散テーブルを使用します。

CPU を集中的に使用するクエリでは、作業がすべてのコンピューティング ノードに分散されたときに、最高のパフォーマンスが得られます。 たとえば、テーブルの各行の計算を実行するクエリのパフォーマンスは、レプリケート テーブルよりも分散テーブルの方が高くなります。 レプリケート テーブル全体が各コンピューティング ノードに格納されるため、CPU を集中的に使用する、レプリケート テーブルに対するクエリは、各コンピューティング ノード上のテーブル全体に対して実行されます。 余分な計算により、クエリのパフォーマンスが低下する場合があります。

たとえば、次のクエリには、複雑な述語が使用されています。  データがレプリケート テーブルではなく分散テーブルにある場合、処理速度が速くなります。 この例では､データをラウンド ロビン方式で分散できます｡

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>既存のラウンド ロビン テーブルをレプリケート テーブルに変換する
既にラウンド ロビン テーブルがある場合、この記事に記載されている条件を満たしているのであれば、レプリケート テーブルに変換することをお勧めします。 レプリケート テーブルは、データ移動の必要がなくなるため、ラウンド ロビン テーブルよりもパフォーマンスが高くなります。  ラウンド ロビン テーブルでは、常に、結合のためにデータ移動が必要になります。 

この例では [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) を使用して、DimSalesTerritory テーブルをレプリケート テーブルに変更します。 この例は、DimSalesTerritory がハッシュ分散かラウンド ロビンかに関係なく動作します。

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE') 

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```  

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>ラウンド ロビンとレプリケートのクエリ パフォーマンスの例 

レプリケート テーブルでは、結合のためのデータ移動は必要ありません。これは、テーブル全体が既に各コンピューティング ノード上に存在するためです。 ディメンション テーブルがラウンド ロビン分散の場合、結合によって、ディメンション テーブル全体が各コンピューティング ノードにコピーされます。 データを移動するために、クエリ プランには BroadcastMoveOperation と呼ばれる操作が含まれています。 この種類のデータ移動操作では、クエリのパフォーマンスが低下します。レプリケート テーブルを使用すると、この操作は使用されなくなります。 クエリ プランのステップを表示するには、[sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) システム カタログ ビューを使用します。 

たとえば、AdventureWorks スキーマに対する次のクエリでは、`FactInternetSales` テーブルがハッシュ分散です。 `DimDate` テーブルと `DimSalesTerritory` テーブルは、小さいディメンション テーブルです。 このクエリでは、会計年度 2004 年の北米における売上合計が返されます。

```sql
SELECT [TotalSalesAmount] = SUM(SalesAmount)
FROM dbo.FactInternetSales s
INNER JOIN dbo.DimDate d
  ON d.DateKey = s.OrderDateKey
INNER JOIN dbo.DimSalesTerritory t
  ON t.SalesTerritoryKey = s.SalesTerritoryKey
WHERE d.FiscalYear = 2004
  AND t.SalesTerritoryGroup = 'North America'
```
`DimDate` と `DimSalesTerritory` をラウンドロビン テーブルとして作成し直しました。 結果として、このクエリでは次のクエリ プランが示されました。これには、複数のブロードキャスト移動操作が含まれています。 
 
![ラウンドロビン クエリ プラン](media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

`DimDate` と `DimSalesTerritory` をレプリケート テーブルとして作成し直し、もう一度このクエリを実行しました。 その結果のクエリ プランは非常に短くなり、ブロードキャスト移動は含まれていません。

![レプリケートされたクエリ プラン](media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>レプリケート テーブルを変更する場合のパフォーマンスに関する考慮事項
SQL Data Warehouse は、テーブルのマスター バージョンを保持することによってレプリケート テーブルを実装します。 そのマスター バージョンは、各コンピューティング ノード上の 1 つのディストリビューション データベースにコピーされます。 変更があったときには、SQL Data Warehouse がまずマスター テーブルを更新します。 そして､Compute ノードのすべてでテーブルを再構築します｡ レプリケート テーブルの再構築では、すべての Compute ノードにテーブルがコピーされ､インデックスが再構築されます｡  たとえば､DW400 上のレプリケート テーブルには､データのコピーが 5 部あります｡  各 Compute ノードにマスター コピー 1 部と完全コピー 1 部  すべてのデータは分散データベースに格納されます｡ SQL Data Warehouse はこのモデルを使用して､データ変更ステートメントを高速に処理し､柔軟なスケーリング操作を行えるようにしています｡ 

再構築が必要になるのは、次の操作の後です。
- データが読み込まれるまたは変更される
- データ ウェアハウスが別のレベルにスケーリングされる
- テーブル定義が更新される

次の操作の後は、再構築は不要です。
- 一時停止操作
- 再開操作

再構築は、データが変更された直後には行われるわけではありません。 再構築は、初めてクエリがテーブルから選択するときにトリガーされます。  各 Compute ノードにデータが非同期にコピーされる間､再構築をトリガーしたクエリはマスター バージョンのテーブルからただちにデータを読み取ります｡ データのコピーが完了するまで､以降のクエリはマスター バージョンのテーブルを使い続けます｡  別の再構築を強制するレプリケート テーブルに対するアクティビティが発生すると､データ コピーは無効になり､次の select ステートメントによって再びデータ コピーがトリガーされます｡ 

### <a name="use-indexes-conservatively"></a>インデックスは控えめに使用する
標準的なインデックス作成方法は、レプリケート テーブルにも当てはまります。 SQL Data Warehouse は、再構築の一環として、各レプリケート テーブル インデックスを再構築します。 インデックスを使用するのは、インデックスの再構築にかかるコストよりもパフォーマンスの向上が重要な場合のみにしてください。  
 
### <a name="batch-data-loads"></a>データの読み込みをバッチ処理する
レプリケート テーブルにデータを読み込む場合、複数の読み込みをバッチ処理することで、再構築を最小限に抑えるようにします。 select ステートメントを実行する前に、バッチ処理された読み込みすべてを実行します。

たとえば、次の読み込みパターンでは、4 つのソースからデータを読み込み、4 つの再構築を呼び出します。 

- ソース 1 から読み込む。
- Select ステートメントで再構築 1 をトリガーする。
- ソース 2 から読み込む。
- Select ステートメントで再構築 2 をトリガーする。
- ソース 3 から読み込む。
- Select ステートメントで再構築 3 をトリガーする。
- ソース 4 から読み込む。
- Select ステートメントで再構築 4 をトリガーする。

たとえば、次の読み込みパターンでは、4 つのソースからデータを読み込みますが、呼び出す再構築は 1 つだけです。

- ソース 1 から読み込む。
- ソース 2 から読み込む。
- ソース 3 から読み込む。
- ソース 4 から読み込む。
- Select ステートメントで再構築をトリガーする。


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>バッチ読み込み後にレプリケート テーブルを再構築する
クエリの実行時間に一貫性を持たせるため、バッチ読み込み後に強制的にレプリケート テーブルを構築することを検討してください｡ 構築しない場合､最初のクエリは引き続きデータ移動を使用してクエリを完了します｡ 

次のクエリでは、[sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) DMV を使用して、変更されたものの再構築されていないレプリケート テーブルの一覧を表示します。

```sql 
SELECT [ReplicatedTable] = t.[name]
  FROM sys.tables t  
  JOIN sys.pdw_replicated_table_cache_state c  
    ON c.object_id = t.object_id 
  JOIN sys.pdw_table_distribution_properties p 
    ON p.object_id = t.object_id 
  WHERE c.[state] = 'NotReady'
    AND p.[distribution_policy_desc] = 'REPLICATE'
```
 
再構築をトリガーするには、前の出力に含まれる各テーブルに対して次のステートメントを実行します。 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>次の手順 
レプリケート テーブルを作成するには、次のいずれかのステートメントを使用します。

- [CREATE TABLE (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [CREATE TABLE AS SELECT (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

分散テーブルの概要については、[分散テーブル](sql-data-warehouse-tables-distribute.md)に関するページを参照してください。



