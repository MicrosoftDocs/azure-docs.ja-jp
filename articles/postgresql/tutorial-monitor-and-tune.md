---
title: Azure Database for PostgreSQL での監視およびチューニングに関するチュートリアル
description: このチュートリアルでは、Azure Database for PostgreSQL での監視およびチューニングについて説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: f05e0eef7680b08ce116cc0243d944f6a1db597c
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536443"
---
# <a name="tutorial-monitor-and-tune-azure-database-for-postgresql"></a>チュートリアル:Azure Database for PostgreSQL の監視とチューニング

Azure Database for PostgreSQL は、サーバーのパフォーマンスの理解および向上に役立つ機能を備えています。 このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * クエリを有効にして、待機統計を収集する
> * 収集されたデータにアクセスし、利用する
> * クエリのパフォーマンスおよび経時的待機統計を表示する
> * データベースを分析し、パフォーマンスの推奨事項を受け取る
> * パフォーマンスに関する推奨事項の適用

## <a name="before-you-begin"></a>開始する前に
PostgreSQL バージョン 9.6 または 10 の Azure Database for PostgreSQL が必要です。 [Create チュートリアル](tutorial-design-database-using-azure-portal.md) の手順に従ってサーバー作成できます。

> [!IMPORTANT]
> **クエリ ストア**、 **Query Performance Insight**、および**パフォーマンスの推奨事項**はパブリック プレビュー内にあります。

## <a name="enabling-data-collection"></a>データ収集の有効化
[クエリ ストア](concepts-query-store.md)は、サーバー上のクエリと待機統計の履歴をキャプチャし、サーバー上の **azure_sys** データベース内に格納します。 これはオプトイン機能です。 これを有効にするには:

1. Azure Portal を開きます。

2. Azure Database for PostgreSQL サーバーを選択します。

3. 左側のメニューの **[設定]** セクションで、**サーバー パラメーター**を選択します。

4. **pg_qs.query_capture_mode** を **TOP** に設定し、クエリ パフォーマンス データの収集を開始します。 **pgms_wait_sampling.query_capture_mode** を **ALL** に設定し、待機統計の収集を開始します。 保存します。
   
   ![クエリストア サーバー パラメータ](./media/tutorial-performance-intelligence/query-store-parameters.png)

5. **azure_sys** データベースに保持するデータの最初のバッチには、最大で 20 分ほどかかります。


## <a name="performance-insights"></a>パフォーマンスの洞察
Azure portal の [Query Performance Insight](concepts-query-performance-insight.md) ビューは、クエリ ストアからの重要な情報を視覚化します。 

1. 左側のメニューの**サポート + トラブルシューティング**セクション下にある、Azure Database for PostgreSQL サーバーのポータル ページで、**Query Performance Insight** を選択します。

2. **実行時間の長いクエリ**タブは、実行ごとの平均実行時間上位 5 クエリを示し、15 分間隔で集計しています。 
   
   ![Query Performance Insight ランディング ページ](./media/tutorial-performance-intelligence/query-performance-insight-landing-page.png)

   **Number of Queries** ドロップダウンから選択してより多くのクエリを表示することができます。 グラフの色は、これを行うときに、特定のクエリ ID に対して変更できます。

3. 特定の時間枠を絞り込むために、クリックして、グラフにドラッグします。

4. 拡大および縮小アイコン使用し、より小さい、または大きい期間をそれぞれ表示できます。

5. その時間枠での実行時間の長いクエリの詳細については、グラフの下の表を表示します。

6. **Wait Statistics** タブを選択し、サーバー内の待機時間に関して、対応する視覚化を表示します。
   
   ![Query Performance Insight の待機の統計](./media/tutorial-performance-intelligence/query-performance-insight-wait-statistics.png)

### <a name="permissions"></a>アクセス許可
Query Performance Insight で、クエリのテキストを表示するために必要な**所有者**または**共同作成者**のアクセス許可。 **リーダー**はグラフとテーブルを表示できますが、クエリ テキストは表示できません。


## <a name="performance-recommendations"></a>パフォーマンスに関する推奨事項
[パフォーマンスに関する推奨事項](concepts-performance-recommendations.md)機能は、パフォーマンスを向上させる可能性のあるインデックスを識別するために、サーバーの間でワークロードを分析します。

1. PostgreSQL サーバーの Azure Portal ページで、メニュー バーの**サポート + トラブルシューティング**セクションから、**パフォーマンスに関する推奨事項**を開きます。
   
   ![パフォーマンスに関する推奨事項ランディング ページ](./media/tutorial-performance-intelligence/performance-recommendations-landing-page.png)

2. **分析**を選択し、データベースを選択します。 これにより、分析が開始されます。

3. ワークロードによって完了するまで数分かかる場合があります。 分析が完了すると、ポータルで通知があります。

4. **パフォーマンスに関する推奨事項**ウィンドウには、何かがわかった場合に、推奨事項の一覧に表示されます。 

5. 推奨事項は、該当する**データベース**、**テーブル**、**列**、および**インデックス サイズ**に関する情報を表示します。

   ![パフォーマンスに関する推奨事項の結果](./media/tutorial-performance-intelligence/performance-recommendations-result.png)

6. 推奨事項を実装するには、クエリ テキストをコピーし、任意のクライアントから実行します。

### <a name="permissions"></a>アクセス許可
パフォーマンスに関する推奨事項機能を使用して分析を実行するには、**所有者**または**共同作成者**の許可が必要です。

## <a name="next-steps"></a>次の手順
- Azure Database for PostgreSQL での[監視およびチューニング](concepts-monitoring.md)の詳細を確認する。