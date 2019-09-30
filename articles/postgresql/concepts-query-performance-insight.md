---
title: Azure Database for PostgreSQL (単一サーバー) の Query Performance Insight
description: この記事では、Azure Database for PostgreSQL (単一サーバー) の Query Performance Insight 機能について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 748c6f08da535013724f68b8be424e50a2d49dfd
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764431"
---
# <a name="query-performance-insight"></a>Query Performance Insight 

**適用対象:** Azure Database for PostgreSQL - 単一サーバー バージョン 9.6、10、11

Query Performance Insight では、実行時間が最長のクエリ、クエリの経時変化､影響を受けている待機状態を素早く特定することができます。

## <a name="permissions"></a>アクセス許可
Query Performance Insight で、クエリのテキストを表示するために必要な**所有者**または**共同作成者**のアクセス許可。 **リーダー**はグラフとテーブルを表示できますが、クエリ テキストは表示できません。

## <a name="prerequisites"></a>前提条件
Query Performance Insight が機能するには、[クエリ ストア](concepts-query-store.md)にデータが存在している必要があります｡

## <a name="viewing-performance-insights"></a>パフォーマンス洞察の表示
Azure portal の [Query Performance Insight](concepts-query-performance-insight.md) ビューは、クエリ ストアからの重要な情報を視覚化します。 

Azure Database for PostgreSQL サーバーのポータル ページのメニュー バーの **[インテリジェント パフォーマンス]** セクションにある **[Query Performance Insight]** を選択します。

![Query Performance Insight 実行時間が長いクエリ](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

**[実行時間の長いクエリ]** タブには、15 分間隔で集計された、実行ごとの平均実行時間上位 5 クエリが示されます。 **Number of Queries** ドロップダウンから選択してより多くのクエリを表示することができます。 グラフの色は、これを行うときに、特定のクエリ ID に対して変更できます。

特定の時間枠を絞り込むために、クリックして、グラフにドラッグします。 拡大および縮小アイコン使用して、それぞれ期間を短くしたり､長くしたりすることもできます｡

グラフの下の表は､その時間枠での実行時間の長いクエリの詳細情報を示しています｡

**Wait Statistics** タブを選択し、サーバー内の待機時間に関して、対応する視覚化を表示します。

![Query Performance Insight の待機時間の統計](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="considerations"></a>考慮事項
* Query Performance Insight は、[読み取りレプリカ](concepts-read-replicas.md)では使用できません。

## <a name="next-steps"></a>次の手順
- Azure Database for PostgreSQL での[監視およびチューニング](concepts-monitoring.md)の詳細を確認する。


