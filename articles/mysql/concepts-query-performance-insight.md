---
title: Azure Database for MySQL の Query Performance Insight
description: この記事では、Azure Database for MySQL の Query Performance Insight 機能について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 8f142933ebf955cbe3aa119f42779109fb6ef7db
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67589065"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Azure Database for MySQL の Query Performance Insight

**適用対象:**   Azure Database for MySQL 5.7

> [!NOTE]
> Query Performance Insight はプレビュー段階にあります。

Query Performance Insight では、実行時間が最長のクエリ、クエリの経時変化､影響を受けている待機状態を素早く特定することができます。

## <a name="common-scenarios"></a>一般的なシナリオ

### <a name="long-running-queries"></a>実行時間の長いクエリ

- 過去 X 時間に実行時間が最も長かったクエリを識別する
- リソースを待機している上位 N 件のクエリを特定する
 
### <a name="wait-statistics"></a>待機統計

- クエリの待機の性質を理解する
- リソースの待機の傾向とリソースの競合が存在する場所を理解する

## <a name="permissions"></a>アクセス許可

Query Performance Insight でクエリのテキストを表示するために必要な**所有者** または **共同作成者** のアクセス許可。 **閲覧者** はグラフとテーブルを表示できますが、クエリ テキストを表示することはできません。

## <a name="prerequisites"></a>前提条件

Query Performance Insight を機能させるには、 [クエリ ストア](concepts-query-store.md)にデータが存在している必要があります｡

## <a name="viewing-performance-insights"></a>パフォーマンス洞察の表示

Azure portal の [Query Performance Insight](concepts-query-performance-insight.md) ビューは、クエリ ストアからの重要な情報を視覚化します。

Azure Database for MySQL サーバーのポータル ページで、メニュー バーの  **[インテリジェント パフォーマンス]**   セクションにある  **[Query Performance Insight]**   を選択します。

### <a name="long-running-queries"></a>実行時間の長いクエリ

 **[実行時間の長いクエリ]**   タブには、実行ごとの平均実行時間の上位 5 件のクエリが示され、15 分間隔で集計されます。  **[クエリの数]**   ドロップダウンから選択することで、より多くのクエリを表示できます。 グラフの色は、これを行うときに、特定のクエリ ID に対して変更できます。

特定の時間枠を絞り込むために、クリックして、グラフにドラッグします。 または、拡大および縮小アイコン使用して、より短い期間またはより長い期間をそれぞれ表示します。

![Query Performance Insight 実行時間が長いクエリ](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>待機統計

> [!NOTE]
> 待機統計は、クエリ パフォーマンスの問題をトラブルシューティングするためのものです。 トラブルシューティングの目的でのみ有効にすることをお勧めします。

待機統計では、特定のクエリの実行中に発生する待機イベントのビューが提供されます。 待機イベントの種類の詳細については、[MySQL エンジンに関するドキュメント](https://go.microsoft.com/fwlink/?linkid=2098206)をご覧ください。

 **[待機統計]** タブを選択すると、サーバーでの待機に関する対応する視覚化が表示されます。

待機統計ビューに表示されるクエリは、指定された時間間隔での最大待機を示すクエリでグループ化されます。

![Query Performance Insight の待機時間の統計](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>次の手順

- Azure Database for MySQL での[監視とチューニング](concepts-monitoring.md)の詳細を確認する。