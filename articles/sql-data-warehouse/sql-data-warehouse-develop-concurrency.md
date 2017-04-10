---
title: "SQL Data Warehouse での同時実行とワークロード管理 | Microsoft Docs"
description: "ソリューション開発のための Azure SQL Data Warehouse での同時実行とワークロード管理を理解します。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1abac24447f709690c42add7c383394443ef957c


---
# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>SQL Data Warehouse での同時実行とワークロード管理
Microsoft Azure SQL Data Warehouse では、予測可能なパフォーマンスを大規模に実現するために、メモリと CPU の優先順位付けのようなリソース割り当てに加えて、同時実行レベルを制御できるようになっています。 この記事では、同時実行とワークロード管理の機能がどのように実装されたか、そしてこれらの機能をデータ ウェアハウスでどのように制御できるかについて説明しながら、両機能の概念を紹介します。 SQL Data Warehouse のワークロード管理は、マルチユーザー環境をサポートすることを目的としています。 マルチテナント ワークロードは想定されていません。

## <a name="concurrency-limits"></a>同時実行の制限
SQL Data Warehouse では、最大 1,024 個の同時接続が可能です。 1,024 のすべての接続でクエリを同時に送信することができます。 ただし、スループットを最適化する目的で、各クエリに与えられるメモリ許可が最小限になるようにするために、一部のクエリがキューに配置される場合があります。 クエリの実行時にキューに配置されます。 同時実行の制限に達したときにクエリをキューに配置することによって、アクティブなクエリが絶対に必要なメモリ リソースに必ずアクセスできるようになるため、全体のスループットが向上します。  

同時実行の制限は、*同時実行クエリ*と*同時実行スロット*の 2 つの概念によって管理されます。 クエリは、クエリの同時実行の制限内および同時実行スロット割り当て内で実行する必要があります。

* 同時実行クエリとは、同時に実行されるクエリを指します。 SQL Data Warehouse では、大きな DWU サイズで最大 32 個の同時実行クエリをサポートしています。
* 同時実行スロットは、DWU に基づいて割り当てられます。 100 DWU ごとに、4 つの同時実行スロットが提供されます。 たとえば、DW100 では 4 つの同時実行スロットが割り当てられ、DW1000 では 40 個の同時実行スロットが割り当てられます。 各クエリは、クエリの [リソース クラス](#resource-classes) に応じて 1 つまたは複数の同時実行スロットを使用します。 smallrc リソース クラスで実行されているクエリは、1 つの同時実行スロットを使用します。 より高いリソース クラスで実行されているクエリは、複数の同時実行スロットを使用します。

次の表に、各 DWU サイズでの同時実行クエリと同時実行スロットの両方の制限を示します。

### <a name="concurrency-limits"></a>同時実行の制限
| DWU | 同時クエリの最大数 | 割り当てられる同時実行スロット数 |
|:--- |:---:|:---:|
| DW100 |4 |4 |
| DW200 |8 |8 |
| DW300 |12 |12 |
| DW400 |16 |16 |
| DW500 |20 |20 |
| DW600 |24 |24 |
| DW1000 |32 |40 |
| DW1200 |32 |48 |
| DW1500 |32 |60 |
| DW2000 |32 |80 |
| DW3000 |32 |120 |
| DW6000 |32 |240 |

これらのいずれかのしきい値に達すると、新しいクエリはキューに配置されます。キューに配置されたクエリは、先入れ先出し方式で実行されます。  クエリが完了してクエリとスロットの数が制限値を下回ると、キューに配置されたクエリは解放されます。 

> [!NOTE]
> *SELECT* クエリは、同時実行の制限を受けることはありません。 ユーザーは、システムで実行されるクエリの数にとらわれずにシステムを監視できます。
> 
> 

## <a name="resource-classes"></a>リソース クラス
リソース クラスにより、クエリに対するメモリ割り当てと CPU サイクルの制御が行われます。 4 つのリソース クラスを *データベース ロール*の形式でユーザーに割り当てることができます。 4 つのリソース クラスとは、**smallrc**、**mediumrc**、**largerc**、**xlargerc** です。 smallrc のユーザーにはより少ないメモリが割り当てられ、その結果、より多くの同時実行が可能になります。 これに対し、xlargerc に割り当てられたユーザーには多くのメモリが与えられるため、同時実行が許可されるクエリの数は少なくなります。

既定では、各ユーザーは小規模リソース クラス (smallrc) のメンバーです。 リソース クラスのサイズを大きくするにはプロシージャ `sp_addrolemember` を使用し、リソース クラスのサイズを小さくするには `sp_droprolemember` を使用します。 たとえば、次のコマンドは、loaduser のリソース クラスをサイズの大きな largerc に割り当てます。

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

お勧めの方法として、ユーザーのリソース クラスを変更するのではなく、ユーザーを永続的にリソース クラスに割り当てます。 たとえば、クラスター化列ストア テーブルへの読み込みでは、より多くのメモリを割り当てることでより高品質のインデックスを作成できます。 読み込み時により多くのメモリにアクセスできるようにするには、データの読み込み専用のユーザーを作成し、このユーザーを永続的に上位のリソース クラスに割り当てます。

クエリの中には、多くのメモリを割り当ててもメリットがない種類のクエリもあります。 リソース クラスの割り当ては無視されますが、その代わり、これらのクエリは常に小規模リソース クラスで実行されます。 これらのクエリが常に小規模リソース クラスで実行されると、同時実行スロットの空きが足りなくてもクエリを実行できます。このため、必要以上にスロットを消費することはなくなります。 詳細については、「[リソース クラスの例外](#query-exceptions-to-concurrency-limits)」をご覧ください。

リソース クラスに関する詳細をもう少し以下に示します。

* *ロールの変更* " アクセス許可が必要です。  
* ユーザーは 1 つまたは複数の上位のリソース クラスに割り当てることができますが、割り当てられた最上位のリソース クラスの属性がユーザーに設定されます。 つまり、ユーザーが mediumrc と largerc の両方に割り当てられている場合、上位のリソース クラスである largerc が優先されるリソース クラスとなります。  
* システム管理ユーザーのリソース クラスは変更できません。

詳細な例は、この記事の最後にある「 [ユーザー リソース クラスの変更例](#changing-user-resource-class-example)」を参照してください。

## <a name="memory-allocation"></a>メモリの割り当て
ユーザーのリソース クラスを引き上げることには長所と短所があります。 ユーザーのリソース クラスを引き上げると、クエリで利用可能なメモリが増え、クエリの実行速度が上がります。  その一方で、リソース クラスを引き上げると、同時実行可能なクエリの数が減ります。 これは、大量のメモリを 1 つのクエリに割り当てるか、同様にメモリの割り当てが必要な他のクエリの同時実行を許可するかのトレードオフです。 あるユーザーにクエリのメモリが大量に割り当てられている場合、他のユーザーがクエリを実行するために同じメモリにアクセスすることはできなくなります。

次の表に、DWU とリソース クラスごとに各ディストリビューションに割り当てられるメモリを示します。

### <a name="memory-allocations-per-distribution-mb"></a>ディストリビューションあたりのメモリ割り当て (MB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |100 |100 |200 |400 |
| DW200 |100 |200 |400 |800 |
| DW300 |100 |200 |400 |800 |
| DW400 |100 |400 |800 |1,600 |
| DW500 |100 |400 |800 |1,600 |
| DW600 |100 |400 |800 |1,600 |
| DW1000 |100 |800 |1,600 |3,200 |
| DW1200 |100 |800 |1,600 |3,200 |
| DW1500 |100 |800 |1,600 |3,200 |
| DW2000 |100 |1,600 |3,200 |6,400 |
| DW3000 |100 |1,600 |3,200 |6,400 |
| DW6000 |100 |3,200 |6,400 |12,800 |

この表から、xlargerc リソース クラスの DW2000 で実行されているクエリは、60 個の分散データベースそれぞれにある 6,400 MB のメモリにアクセスすることがわかります。  SQL Data Warehouse には、60 個のディストリビューションがあります。 そのため、特定のリソース クラスでのクエリの合計メモリ割り当て量を計算するには、上記の値に 60 を掛ける必要があります。

### <a name="memory-allocations-system-wide-gb"></a>システム全体のメモリ割り当て (GB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |6 |6 |12 |23 |
| DW200 |6 |12 |23 |47 |
| DW300 |6 |12 |23 |47 |
| DW400 |6 |23 |47 |94 |
| DW500 |6 |23 |47 |94 |
| DW600 |6 |23 |47 |94 |
| DW1000 |6 |47 |94 |188 |
| DW1200 |6 |47 |94 |188 |
| DW1500 |6 |47 |94 |188 |
| DW2000 |6 |94 |188 |375 |
| DW3000 |6 |94 |188 |375 |
| DW6000 |6 |188 |375 |750 |

このシステム全体のメモリ割り当ての表から、xlargerc リソース クラスの DW2000 で実行されているクエリには、SQL Data Warehouse 全体で合計 375 GB のメモリが割り当てられることがわかります (6,400 MB * 60 ディストリビューション / 1,024 (GB 単位))。

## <a name="concurrency-slot-consumption"></a>使用される同時実行スロット数
より上位のリソース クラスで実行されるクエリには、より多くのメモリが与えられます。 メモリは、固定のリソースです。  そのため、1 つのクエリに割り当てられるメモリが多くなるほど、同時に実行できるクエリの数が少なくなります。 次の表で、これまでに説明したすべての概念を 1 つにまとめ、各 DWU で利用可能な同時実行スロットの数と各リソース クラスで使用されるスロットの数を示します。

### <a name="allocation-and-consumption-of-concurrency-slots"></a>同時実行スロットの割り当てと使用数
| DWU | 同時クエリの最大数 | 割り当てられる同時実行スロット数 | smallrc で使用されるスロット数 | mediumrc で使用されるスロット数 | largerc で使用されるスロット数 | xlargerc で使用されるスロット数 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |1 |2 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |
| DW400 |16 |16 |1 |4 |8 |16 |
| DW500 |20 |20 |1 |4 |8 |16 |
| DW600 |24 |24 |1 |4 |8 |16 |
| DW1000 |32 |40 |1 |8 |16 |32 |
| DW1200 |32 |48 |1 |8 |16 |32 |
| DW1500 |32 |60 |1 |8 |16 |32 |
| DW2000 |32 |80 |1 |16 |32 |64 |
| DW3000 |32 |120 |1 |16 |32 |64 |
| DW6000 |32 |240 |1 |32 |64 |128 |

この表から、DW1000 として実行されている SQL Data Warehouse では、最大 32 個の同時実行クエリと合計 40 個の同時実行スロットが提供されることが分かります。 すべてのユーザーが smallrc で実行している場合、クエリごとに 1 つの同時実行スロットが使用されるため、32 個のクエリを同時実行できます。 DW1000 のすべてのユーザーが mediumrc で実行している場合、各クエリには総メモリの 47 GB の割り当てについてディストリビューションあたり 800 MB が割り当てられ、同時実行は 5 ユーザー (40 個の同時実行スロット/mediumrc ユーザーあたり 8 スロット) に制限されます。

## <a name="query-importance"></a>クエリの重要度
SQL Data Warehouse では、ワークロード グループを使用してリソース クラスを実装します。 各 DWU サイズでのリソース クラスの動作を制御するワークロード グループは合計で 8 つあります。 いずれの DWU でも、使用するのは 8 つのワークロード グループのうち 4 つだけです。 各ワークロード グループは smallrc、mediumrc、largerc、または xlargerc のうちの 1 つに割り当てられるため、これは当然のことです。 ワークロード グループを理解するうえで、これらのワークロード グループの一部は上位の *重要度*に設定されることに注意する必要があります。 重要度は、CPU のスケジュール設定に使用されます。 重要度が "高" のクエリには、重要度が "中" のクエリと比べて 3 倍の CPU サイクルが与えられます。 そのため、同時実行スロットのマッピングにより、CPU の優先度も決まります。 16 個以上のスロットを使用するクエリは、"高" 重要度として実行されます。

次の表に、各ワークロード グループの重要度のマッピングを示します。

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>同時実行スロットおよび重要度に対するワークロード グループのマッピング
| ワークロード グループ | 同時実行スロットのマッピング | MB / ディストリビューション | 重要度のマッピング |
|:--- |:---:|:---:|:--- |
| SloDWGroupC00 |1 |100 |中 |
| SloDWGroupC01 |2 |200 |中 |
| SloDWGroupC02 |4 |400 |中 |
| SloDWGroupC03 |8 |800 |中 |
| SloDWGroupC04 |16 |1,600 |高 |
| SloDWGroupC05 |32 |3,200 |高 |
| SloDWGroupC06 |64 |6,400 |高 |
| SloDWGroupC07 |128 |12,800 |高 |

**同時実行スロットの割り当てと使用数** の表から、DW500 では smallrc、mediumrc、largerc、および xlargerc のそれぞれで 1 個、4 個、8 個、または 16 個の同時実行スロットが使用されることが分かります。 上の表でこれらの値を調べることで、各リソース クラスの重要度を確認できます。

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>DW500 での重要度に対するリソース クラスのマッピング
| リソース クラス | ワークロード グループ | 使用される同時実行スロット数 | MB / ディストリビューション | 重要度 |
|:--- |:--- |:---:|:---:|:--- |
| smallrc |SloDWGroupC00 |1 |100 |中 |
| mediumrc |SloDWGroupC02 |4 |400 |中 |
| largerc |SloDWGroupC03 |8 |800 |中 |
| xlargerc |SloDWGroupC04 |16 |1,600 |高 |

次の DMV クエリを使用すると、リソース ガバナーの観点からメモリ リソースの割り当ての違いを詳細に確認できます。また、トラブルシューティングを行うときにワークロード グループのアクティブな使用状況と過去の使用状況を分析することもできます。

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                        AS node_type
    ,pn.pdw_node_id                    AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024                AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                    AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                        AS group_max_dop
    ,wg.effective_max_dop                AS group_effective_max_dop
    ,wg.total_request_count                AS group_total_request_count
    ,wg.total_queued_request_count            AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT    pool_name
,        pool_max_mem_MB
,        group_name
,        group_importance
,        (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,        node_name
,        node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,    group_request_max_memory_grant_pcnt
,    group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>同時実行の制限が考慮されるクエリ
ほとんどのクエリは、リソース クラスによって管理されます。 これらのクエリは、同時実行クエリと同時実行スロットの両方のしきい値以下である必要があります。 ユーザーは、同時実行スロット モデルからクエリを除外することはできません。

繰り返しになりますが、次のステートメントではリソース クラスが考慮されます。

* INSERT-SELECT
* UPDATE
* 削除
* SELECT (ユーザー テーブルのクエリを実行する場合)
* ALTER INDEX REBUILD
* ALTER INDEX REORGANIZE
* ALTER TABLE REBUILD
* CREATE INDEX
* CREATE CLUSTERED COLUMNSTORE INDEX
* CREATE TABLE AS SELECT (CTAS)
* データの読み込み
* Data Movement Service (DMS) によって実行されるデータ移動操作

## <a name="query-exceptions-to-concurrency-limits"></a>同時実行の制限に対するクエリの例外
クエリには、ユーザーの割り当てられているリソース クラスが考慮されないものがあります。 同時実行の制限に対するこうした例外は特定のコマンドに必要なメモリ リソースが少ない場合に発生します。その理由は多くの場合、コマンドがメタデータ操作となるためです。 これらの例外により、大量のメモリ割り当てを必要としないクエリに対して、多くのメモリが割り当てられる状態を回避できます。 このような場合、ユーザーに割り当てられた実際のリソース クラスに関係なく、既定の小規模リソース クラス (smallrc) が常に使用されます。 たとえば、`CREATE LOGIN` は常に smallrc で実行されます。 この操作の実行に必要なリソースは非常に少ないので、同時実行スロット モデルにクエリを含めても意味はありません。  また、これらのクエリには 32 ユーザーの同時実行の制限は適用されず、1,024 セッションというセッションの上限までクエリを無制限に実行できます。

次のステートメントでは、リソース クラスは考慮されません。

* CREATE または DROP TABLE
* ALTER TABLE ...SWITCH、SPLIT、または MERGE PARTITION
* ALTER INDEX DISABLE
* DROP INDEX
* CREATE、UPDATE、または DROP STATISTICS
* TRUNCATE TABLE
* ALTER AUTHORIZATION
* CREATE LOGIN
* CREATE、ALTER、または DROP USER
* CREATE、ALTER、または DROP PROCEDURE
* CREATE または DROP VIEW
* INSERT VALUES
* システム ビューおよび DMV からの SELECT
* EXPLAIN
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="change-a-user-resource-class-example"></a>ユーザー リソース クラスの変更例
1. **ログインを作成する:** SQL Data Warehouse データベースをホストしている SQL Server の**マスター** データベースへの接続を開き、次のコマンドを実行します。
   
    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```
   
   > [!NOTE]
   > Azure SQL Data Warehouse ユーザーの master データベースにユーザーを作成することをお勧めします。 マスターでユーザーを作成すると、ユーザーはデータベース名を指定せずに SSMS のようなツールを使用してログインできます。  また、オブジェクト エクスプ ローラーを使用して、SQL Server のすべてのデータベースを表示することもできます。  ユーザーの作成および管理の詳細については、[「SQL Data Warehouse でのデータベース保護」][SQL Data Warehouse でのデータベース保護]をご覧ください。
   > 
   > 
2. **SQL Data Warehouse ユーザーを作成する:** **SQL Data Warehouse** データベースへの接続を開き、次のコマンドを実行します。
   
    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```
3. **アクセス許可を与える:** 次の例では、**SQL Data Warehouse** データベースに対する `CONTROL` を付与します。 データベース レベルので `CONTROL` は、SQL Server での db_owner に相当します。
   
    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```
4. **リソース クラスのサイズを大きくする:** ワークロードの高い管理ロールにユーザーを追加するには、次のクエリを使用します。
   
    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```
5. **リソース クラスのサイズを小さくする:** ワークロード管理ロールからユーザーを削除するには、次のクエリを使用します。
   
    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```
   
   > [!NOTE]
   > smallrc からユーザーを削除することはできません。
   > 
   > 

## <a name="queued-query-detection-and-other-dmvs"></a>キューに配置されたクエリの検出とその他の DMV
`sys.dm_pdw_exec_requests` DMV を使用すると、同時実行キューで待機中のクエリを特定できます。 同時実行スロットを待機しているクエリは、 **中断**状態となります。

```sql
SELECT      r.[request_id]                 AS Request_ID
        ,r.[status]                 AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]                 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

ワークロード管理ロールを確認するには、 `sys.database_principals`を使用します。

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

次のクエリは、各ユーザーが割り当てられているロールを示します。

```sql
SELECT     r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id        = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE    r.name IN ('mediumrc','largerc', 'xlargerc');
```

SQL Data Warehouse には、次の待機の種類があります。

* **LocalQueriesConcurrencyResourceType**: 同時実行スロットのフレームワークの外に配置されたクエリ。 DMV クエリと、 `SELECT @@VERSION` のようなシステム関数は、ローカル クエリの例です。
* **UserConcurrencyResourceType**: 同時実行スロットのフレームワーク内に配置されたクエリ。 エンドユーザー テーブルに対するクエリは、このリソースの種類を使用した例です。
* **DmsConcurrencyResourceType**: データ移動操作に起因する待機。
* **BackupConcurrencyResourceType**: この待機は、データベースがバックアップ中であることを示します。 この種類のリソースの最大値は 1 です。 同時に複数のバックアップが要求された場合は、他の要求はキューに配置されます。

`sys.dm_pdw_waits` DMV を使用すると、要求がどのリソースを待っているのかを調べることができます。

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,    SESSION_ID()            AS Current_session
,    s.[status]            AS Session_status
,    s.[login_name]
,    s.[query_count]
,    s.[client_id]
,    s.[sql_spid]
,    r.[command]            AS Request_command
,    r.[label]
,    r.[status]            AS Request_status
,    r.[submit_time]
,    r.[start_time]
,    r.[end_compile_time]
,    r.[end_time]
,    DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,    DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,    DATEDIFF(ms,r.[end_compile_time],r.[end_time])        AS Request_execution_time_ms
,    r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID();
```

`sys.dm_pdw_resource_waits` DMV では、特定のクエリによって使用されるリソースの待機のみが表示されます。 リソースの待機時間では、リソースが提供されるまでの時間のみが考慮されます。これに対し、シグナルの待機時間は、基になる SQL Server によりクエリが CPU にスケジュール設定されるまでの時間です。

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID();
```

`sys.dm_pdw_wait_stats` DMV を使用すると、待機のこれまでの傾向を分析できます。

```sql
SELECT    w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>次のステップ
データベース ユーザーの管理とセキュリティの詳細については、[「SQL Data Warehouse でのデータベース保護」][SQL Data Warehouse でのデータベース保護]を参照してください。 大規模なリソース クラスを使用して、クラスター化された列ストア インデックスの品質を向上させる方法については、「 [セグメントの品質を向上させるためのインデックスの再構築]」を参照してください。

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse でのデータベース保護]: ./sql-data-warehouse-overview-manage-security.md
[セグメントの品質を向上させるためのインデックスの再構築]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[SQL Data Warehouse でのデータベース保護]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Azure SQL Database におけるデータベース、ログイン、およびユーザーの管理]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


