---
title: Query Performance Insight - Azure Database for MySQL
description: この記事では、Azure Database for MySQL の Query Performance Insight 機能について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 5/12/2020
ms.openlocfilehash: 51b478e5184c79e11d95ff004a652b2e5298558f
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402613"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Azure Database for MySQL の Query Performance Insight

**適用対象:** Azure Database for MySQL 5.7、8.0

Query Performance Insight では、実行時間が最長のクエリ、クエリの経時変化､影響を受けている待機状態を素早く特定することができます。

## <a name="common-scenarios"></a>一般的なシナリオ

### <a name="long-running-queries"></a>実行時間の長いクエリ

- 過去 X 時間に実行時間が最も長かったクエリを識別する
- リソースを待機している上位 N 件のクエリを特定する
 
### <a name="wait-statistics"></a>待機統計

- クエリの待機の性質を理解する
- リソースの待機の傾向とリソースの競合が存在する場所を理解する

## <a name="permissions"></a>アクセス許可

Query Performance Insight で、クエリのテキストを表示するために必要な**所有者**または**共同作成者**のアクセス許可。 **リーダー**はグラフとテーブルを表示できますが、クエリ テキストは表示できません。

## <a name="prerequisites"></a>前提条件

Query Performance Insight が機能するには、[クエリ ストア](concepts-query-store.md)にデータが存在している必要があります｡

## <a name="viewing-performance-insights"></a>パフォーマンス洞察の表示

Azure portal の [Query Performance Insight](concepts-query-performance-insight.md) ビューは、クエリ ストアからの重要な情報を視覚化します。

Azure Database for MySQL サーバーのポータル ページのメニュー バーの **[インテリジェント パフォーマンス]** セクションにある **[Query Performance Insight]** を選択します。

### <a name="long-running-queries"></a>実行時間の長いクエリ

**[実行時間の長いクエリ]** タブには、実行ごとの平均実行時間の上位 5 クエリが示され、15 分間隔で集計されます。 **[クエリの数]** ドロップダウンから選択することで、より多くのクエリを表示できます。 グラフの色は、これを行うときに、特定のクエリ ID に対して変更できます。

特定の時間枠を絞り込むために、クリックして、グラフにドラッグします。 または、拡大および縮小アイコン使用して、より短い期間またはより長い期間をそれぞれ表示します。

![Query Performance Insight 実行時間が長いクエリ](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>待機統計

> [!NOTE]
> 待機統計は、クエリ パフォーマンスの問題をトラブルシューティングするためのものです。 トラブルシューティングの目的でのみ有効にすることをお勧めします。 <br>待機統計の表示中に Azure portal で " *'Microsoft.DBforMySQL' で問題が発生しました。要求に対処できません。この問題が解決しないか、予想外のものである場合、サポートにお問い合わせいただき、この情報をお知らせください。* " というエラー メッセージが表示された場合は、より短い期間を使用してください。

待機統計では、特定のクエリの実行中に発生する待機イベントのビューが提供されます。 待機イベントの種類の詳細については、[MySQL エンジンに関するドキュメント](https://go.microsoft.com/fwlink/?linkid=2098206)をご覧ください。

**Wait Statistics** タブを選択し、サーバー内の待機時間に関して、対応する視覚化を表示します。

待機統計ビューに表示されるクエリは、指定された時間間隔での最大待機を示すクエリでグループ化されます。

![Query Performance Insight の待機時間の統計](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>次のステップ

- Azure Database for MySQL での[監視とチューニング](concepts-monitoring.md)の詳細を確認する。