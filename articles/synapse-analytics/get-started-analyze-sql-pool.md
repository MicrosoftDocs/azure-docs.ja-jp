---
title: チュートリアル:SQL プールを使用したデータ分析の概要
description: このチュートリアルでは、NYC タクシーのサンプル データを使用して、SQL プールの分析機能を探索します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: e2e1d0479b8edacaae8816d74db061eeedb805a7
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325221"
---
# <a name="analyze-data-with-sql-pools"></a>SQL プールを使用したデータ分析

Azure Synapse Analytics には、SQL プールを使用してデータを分析する機能が用意されています。 このチュートリアルでは、NYC タクシーのサンプル データを使用して、SQL プールの分析機能を探索します。

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>NYC タクシーのサンプル データを SQLDB1 データベースに読み込む

1. Synapse Studio で、一番上にある青いメニューの疑問符 **[?]** を選択します。
1. **[作業の開始]**  >  **[作業の開始] ハブ**を選択します。
1. **[Query sample data]\(クエリ サンプル データ\)** というラベルの付いたカードで、**SQLDB1** という名前の SQL プールを選択します。
1. **[クエリ データ]** を選択します。 "Loading sample data" (サンプル データを読み込んでいます) という通知が少しの間表示されます。 Synapse Studio の上部付近にある薄い青のステータス バーで、データを SQLDB1 に読み込み中であることが示されます。
1. ステータス バーが緑色に変わったら、それを閉じます。

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>SQL プール内の NYC タクシーのデータを探索する

1. Synapse Studio で、 **[データ]** ハブに移動します。
1. **[SQLDB1]**  >  **[テーブル]** に移動します。 複数のテーブルが読み込まれていることがわかります。
1. **dbo.Trip** テーブルを右クリックし、 **[New SQL Script]\(新しい SQL スクリプト\)**  >  **[Select TOP 100 Rows]\(上位 100 行の選択\)** を選択します。
1. 新しい SQL スクリプトが作成されて実行されるまで待ちます。
1. SQL スクリプトの上部にある **[接続先]** が自動的に **SQLDB1** という SQL プールに注目します。
1. SQL スクリプトのテキストをこのコードで置き換えて実行します。

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    このクエリは、合計乗車距離と平均乗車距離が乗客数とどのように関係しているかを示します。
1. SQL スクリプトの結果ウィンドウで、 **[ビュー]** を **[グラフ]** に変更して、結果を折れ線グラフとして視覚的に表示します。



## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Spark を使用して分析する](get-started-analyze-spark.md)

