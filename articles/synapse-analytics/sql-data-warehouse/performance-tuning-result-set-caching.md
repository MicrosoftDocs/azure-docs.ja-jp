---
title: 結果セットのキャッシュを使用したパフォーマンスのチューニング
description: Azure Synapse Analytics の Synapse SQL プールの結果セットのキャッシュ機能の概要
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: 2c29ad48e8dc626617786e519e84c92875bc6170
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378527"
---
# <a name="performance-tuning-with-result-set-caching"></a>結果セットのキャッシュを使用したパフォーマンスのチューニング

結果セットのキャッシュが有効になっている場合、Synapse SQL では、繰り返し使用できるように、クエリ結果がユーザー データベースに自動的にキャッシュされます。  これにより、以降のクエリ実行で永続キャッシュから直接結果を取得できるため、再計算は必要ありません。   結果セットのキャッシュにより、クエリのパフォーマンスが向上し、コンピューティング リソースの使用量が減少します。  さらに、キャッシュされた結果セットを使用するクエリはコンカレンシー スロットをまったく使用しないため、既存のコンカレンシー制限にはカウントされません。 セキュリティのため、キャッシュされた結果にアクセスできるのは、そのユーザーがキャッシュされた結果を作成したユーザーと同じデータ アクセス許可を持っている場合のみです。  

## <a name="key-commands"></a>主なコマンド

[ユーザー データベースに対する結果セットのキャッシュをオン/オフにする](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[セッションに対する結果セットのキャッシュをオン/オフにする](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[キャッシュされた結果セットのサイズを確認する](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

[キャッシュをクリーンアップする](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>キャッシュされないもの  

結果セットのキャッシュがデータベースに対してオンになると、キャッシュがいっぱいになるまで、すべてのクエリに対して結果がキャッシュされます。ただし、次のクエリは除きます。

- DateTime.Now() などの非決定論的関数を使用するクエリ
- ユーザー定義関数を使用したクエリ
- 行レベルのセキュリティまたは列レベルのセキュリティが有効になっているテーブルを使用したクエリ
- 64 KB を超える行サイズのデータを返すクエリ
- 10 GB を超えるサイズの大きなデータを返すクエリ 

> [!IMPORTANT]
> 結果セットのキャッシュを作成し、キャッシュからデータを取得する操作は、Synapse SQL プール インスタンスの制御ノードで行われます。
> 結果セットのキャッシュを有効にした場合、大きな結果セット (たとえば 1 GB 超) を返すクエリを実行すると、制御ノードでスロットリングが大きくなり、インスタンスでのクエリ応答全体が遅くなる可能性があります。  これらのクエリは、通常、データの探索または ETL 操作で使用されます。 制御ノードに負荷を与え、パフォーマンスの問題が発生するのを防ぐため、ユーザーは、このようなクエリを実行する前に、データベースの結果セットのキャッシュを無効にする必要があります。  

クエリの結果セットのキャッシュ操作にかかる時間に、次のクエリを実行します。

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

結果セットのキャッシュを無効にして実行されたクエリの出力例を次に示します。

![Query-steps-with-rsc-disabled](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

結果セットのキャッシュを有効にして実行されたクエリの出力例を次に示します。

![Query-steps-with-rsc-enabled](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>キャッシュされた結果が使用される場合

次のすべての要件が満たされている場合は、キャッシュされた結果セットがクエリで再利用されます。

- クエリを実行しているユーザーに、クエリで参照されているすべてのテーブルへのアクセス権がある。
- 新しいクエリと、結果セットのキャッシュを生成した前のクエリが完全に一致している。
- キャッシュされた結果セットが生成されたテーブル内でデータおよびスキーマの変更がない。

次のコマンドを実行して、クエリが結果キャッシュ ヒットで実行されたのか、結果キャッシュ ミスで実行されたのかを確認します。 result_cache_hit 列では、キャッシュ ヒットの場合は 1、キャッシュ ミスの場合は 0、結果セットのキャッシュが使用されなかった理由については負の値が返されます。 詳細については、[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) を確認してください。

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>キャッシュされた結果の管理

結果セットのキャッシュの最大サイズは、データベースあたり 1 TB です。  基になるクエリ データが変更されると、キャッシュされた結果は自動的に無効になります。  

キャッシュの削除は、このスケジュールに従って Synapse SQL によって自動的に管理されます。

- 結果セットが使用されていない場合、または無効になっている場合は、48 時間ごと。
- 結果セットのキャッシュが最大サイズに近づいた場合。

ユーザーは、次のいずれかのオプションを使用して、結果セットのキャッシュ全体を手動で空にすることができます。

- データベースの結果セットのキャッシュ機能をオフにする
- データベースに接続しているときに DBCC DROPRESULTSETCACHE を実行する

データベースを一時停止しても、キャッシュされた結果セットは空になりません。  

## <a name="next-steps"></a>次のステップ

開発についてのその他のヒントは、[開発の概要](sql-data-warehouse-overview-develop.md)に関するページをご覧ください。
