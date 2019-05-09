---
title: Gen2 キャッシュの最適化 |Microsoft Docs
description: Azure portal を使用して Gen2 キャッシュを監視する方法について説明します。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 26791aecb2ca57b31358d3385d07230c73c84904
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57903865"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Gen2 キャッシュを監視する方法
Gen2 ストレージ アーキテクチャでは、Gen2 データ ウェアハウス用に設計された NVMe ベースの SSD に搭載されているキャッシュ内で、最も頻繁に照会される列ストア セグメントを自動的に階層化します。 キャッシュに常駐しているセグメントをクエリが取得すると、より優れたパフォーマンスが実現します。 この記事では、ワークロードが Gen2 キャッシュを最適に活用しているかどうかを判断することで、パフォーマンスが遅いクエリを監視し、トラブルシューティングする方法について説明します。  
## <a name="troubleshoot-using-the-azure-portal"></a>Azure Portal を使用したトラブルシューティング
Azure Monitor を使用すれば、Gen2 キャッシュ メトリックを表示して、クエリのパフォーマンスをトラブルシューティングできます。 まず Azure portal に移動し、[監視] をクリックします。

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

メトリック ボタンを選択し、データ ウェアハウスの **[サブスクリプション]**、**[リソース]**、**[グループ]**、**[リソースの種類]**、**[リソース名]** を記入します。

Gen2 キャッシュをトラブルシューティングするための主要なメトリックは、**[キャッシュ ヒット率]** と **[キャッシュ使用率]** です。 次の 2 つのメトリックを表示するように、Azure メトリック グラフを構成します。

![キャッシュ メトリック](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>キャッシュのヒット率と使用率
以下の表は、キャッシュ メトリックの値に基づくシナリオを示しています。

|                                | **高いキャッシュ ヒット率** | **低いキャッシュ ヒット率** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **高いキャッシュ使用率** |          シナリオ 1           |          シナリオ 2          |
| **低いキャッシュ使用率**  |          シナリオ 3           |          シナリオ 4          |

**シナリオ 1:** キャッシュを最適に使用しています。 クエリを遅くしている可能性があるその他の領域を[トラブルシューティング](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)してください。

**シナリオ 2:** 現在稼働中のデータ セットはキャッシュに収まりきらないため、物理的な読み取りのためにキャッシュ ヒット率が低くなります。 パフォーマンス レベルの拡張を検討し、ワークロードを再実行してキャッシュを設定してください。

**シナリオ 3:** キャッシュとは無関係な原因により、クエリが遅く実行されている可能性があります。 クエリを遅くしている可能性があるその他の領域を[トラブルシューティング](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)してください。 また、[インスタンスを縮小し](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)、キャッシュ サイズを削減してコストを節約することも検討してください。 

**シナリオ 4:** クエリが遅かったのは、コールド キャッシュが存在していたことが原因だった可能性があります。 現在稼働中のデータ セットがキャッシュされるように、クエリを再実行することを検討してください。 

**重要:キャッシュヒット率またはキャッシュ使用率がワークロードの再実行後にも更新されない場合、稼働中のセットは既にメモリに常駐している可能性があります。クラスター化された列ストア テーブルのみがキャッシュされることに注意してください。**

## <a name="next-steps"></a>次の手順
一般的なクエリのパフォーマンス チューニングの詳細については、「[クエリの実行の監視](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor#monitor-query-execution)」を参照してください。


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: https://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
