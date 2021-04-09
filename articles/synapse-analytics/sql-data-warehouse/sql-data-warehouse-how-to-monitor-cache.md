---
title: Gen2 キャッシュの最適化
description: Azure portal を使用して Gen2 キャッシュを監視する方法について説明します。
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: fed3ed2c87342d557872e97bfc2a6c4d142b5a3a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585623"
---
# <a name="how-to-monitor-the-adaptive-cache"></a>アダプティブ キャッシュを監視する方法

この記事では、ワークロードが専用 SQL プールのアダプティブ キャッシュを最適に活用しているかどうかを判断することで、パフォーマンスが遅いクエリを監視し、トラブルシューティングする方法について説明します。

専用 SQL プール ストレージ アーキテクチャでは、NVMe ベースの SSD に搭載されているキャッシュ内で、最も頻繁に照会される列ストア セグメントが自動的に階層化されます。 クエリでキャッシュに常駐しているセグメントを取得すると、パフォーマンスが向上します。
 
## <a name="troubleshoot-using-the-azure-portal"></a>Azure Portal を使用したトラブルシューティング

Azure Monitor を使用すれば、キャッシュ メトリックを表示して、クエリのパフォーマンスをトラブルシューティングできます。 まず Azure portal に移動し、 **[監視]** 、 **[メトリック]** 、 **[+ スコープの選択]** の順にクリックします。

![スクリーンショットには、Azure portal の [メトリック] で選択された [スコープの選択] が示されています。](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

検索バーとドロップダウン バーを使用して、お使いの専用 SQL プールを見つけます。 次に、[適用] を選択します。

![スクリーンショットには、使用しているデータ ウェアハウスを選択できる [スコープの選択] ウィンドウが示されています。](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

キャッシュをトラブルシューティングするための主要なメトリックは、 **[キャッシュ ヒット率]** と **[キャッシュ使用率]** です。 **[キャッシュ ヒット率]** を選択した後、 **[メトリックの追加]** ボタンをクリックして **[キャッシュの使用率]** を追加します。 

![キャッシュ メトリック](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>キャッシュのヒット率と使用率

以下の表は、キャッシュ メトリックの値に基づくシナリオを示しています。

|                                | **高いキャッシュ ヒット率** | **低いキャッシュ ヒット率** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **高いキャッシュ使用率** |          シナリオ 1           |          シナリオ 2          |
| **低いキャッシュ使用率**  |          シナリオ 3           |          シナリオ 4          |

**シナリオ 1:** キャッシュを最適に使用しています。 クエリを遅くしている可能性があるその他の領域を[トラブルシューティング](sql-data-warehouse-manage-monitor.md)してください。

**シナリオ 2:** 現在稼働中のデータ セットはキャッシュに収まりきらないため、物理的な読み取りのためにキャッシュ ヒット率が低くなります。 パフォーマンス レベルの拡張を検討し、ワークロードを再実行してキャッシュを設定してください。

**シナリオ 3:** キャッシュとは無関係な原因により、クエリが遅く実行されている可能性があります。 クエリを遅くしている可能性があるその他の領域を[トラブルシューティング](sql-data-warehouse-manage-monitor.md)してください。 また、[インスタンスを縮小し](sql-data-warehouse-manage-monitor.md)、キャッシュ サイズを削減してコストを節約することも検討してください。 

**シナリオ 4:** クエリが遅かったのは、コールド キャッシュが存在していたことが原因だった可能性があります。 現在稼働中のデータ セットがキャッシュされるように、クエリを再実行することを検討してください。 

> [!IMPORTANT]
> キャッシュヒット率またはキャッシュ使用率がワークロードの再実行後にも更新されない場合、稼働中のセットは既にメモリに常駐している可能性があります。 クラスター化された列ストア テーブルのみがキャッシュされます。

## <a name="next-steps"></a>次のステップ
一般的なクエリのパフォーマンス チューニングの詳細については、「[クエリの実行の監視](sql-data-warehouse-manage-monitor.md#monitor-query-execution)」を参照してください。
