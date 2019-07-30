---
title: メトリックを使用した Azure Data Explorer のパフォーマンス、正常性、および使用状況の監視
description: Azure Data Explorer メトリックを使用して、クラスターのパフォーマンス、正常性、および使用状況を監視する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: cb59fa0fe9094943dfc942d1d6e664891996c9e3
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67569293"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>メトリックを使用した Azure Data Explorer のパフォーマンス、正常性、および使用状況の監視

Azure Data Explorer は、アプリケーション、Web サイト、IoT デバイスなどからの大量のデータ ストリーミングをリアルタイムに分析するためのフル マネージドのデータ分析サービスです。 Azure Data Explorer を使用するには、最初にクラスターを作成し、そのクラスター内に 1 つまたは複数のデータベースを作成します。 その後、クエリを実行できるように、データをデータベースに取り込み (読み込み) ます。 Azure Data Explorer メトリックは、クラスター リソースの正常性とパフォーマンスに関する主要な指標を提供します。 この記事で詳細に説明するメトリックを使用して、特定のシナリオでの Azure Data Explorer クラスターの正常性とパフォーマンスをスタンドアロン メトリックとして監視します。 また、[Azure ダッシュボード](/azure/azure-portal/azure-portal-dashboards) と [Azure アラート](/azure/azure-monitor/platform/alerts-metric-overview) の操作の基本としてメトリックを使用できます。

## <a name="prerequisites"></a>前提条件

* Azure のサブスクリプションがない場合は、[Azure の無料アカウント](https://azure.microsoft.com/free/)を作成します。

* [クラスターとデータベース](create-cluster-database-portal.md)を作成します。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインします

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="using-metrics"></a>メトリックの使用

Azure Data Explorer クラスターで、 **[メトリック]** を選択して [メトリック] ウィンドウを開き、クラスターの分析を開始します。

![メトリックを選択する](media/using-metrics/select-metrics.png)

[メトリック] ウィンドウでの操作:

![[メトリック] ウィンドウ](media/using-metrics/metrics-pane.png)

1. メトリック グラフを作成するには、**メトリック**名と、関連するメトリックあたりの**集計** (以下で詳述) を選択します。 Azure Data Explorer クラスターの **[リソース]** と **[メトリック名前空間]** は事前に選択されています。

    **メトリック** | **単位** | **集計** | **メトリックの説明**
    |---|---|---|---|
    | キャッシュ使用率 | Percent | Avg、Max、Min | クラスターで現在使用中の割り当てられたキャッシュ リソースの割合。 キャッシュとは、定義されているキャッシュ ポリシーに従ってユーザー アクティビティに割り当てられた SSD のサイズのことです。 80% 以下の平均キャッシュ使用率が、クラスターにとって持続可能な状態です。 平均キャッシュ使用率が 80% を超える場合、ストレージ最適化価格レベルにクラスターを[スケールアップ](manage-cluster-vertical-scaling.md)するか、より多くのインスタンスを[スケールアウト](manage-cluster-horizontal-scaling.md)します。 あるいは、キャッシュ ポリシーを調整します (キャッシュに格納する日数を削減)。 キャッシュ使用率が 100% を超える場合、キャッシュ ポリシーに基づいてキャッシュされるデータのサイズが、クラスター上の合計キャッシュ サイズを超えています。 |
    | CPU | Percent | Avg、Max、Min | クラスター内のマシンで現在使用中の割り当てられたコンピューティング リソースの割合。 80% 以下の平均 CPU 使用率が、クラスターにとって持続可能です。 CPU の最大値は 100% であり、これは、データを処理する計算リソースがそれ以上ないことを示します。 クラスターのパフォーマンスが良くない場合は、特定の CPU がブロックされていないか判別するために、CPU の最大値を確認してください。 |
    | (Event Hubs の) 処理されたイベント | Count | Max、Min、Sum | イベント ハブから読み取られ、クラスターによって処理されたイベントの数。 イベントは、クラスター エンジンによって拒否されたイベントと受け入れられたイベントに分類されます。 |
    | インジェストの待ち時間 | Seconds | Avg、Max、Min | クラスターでデータが受信された時点からクエリー用に準備できるまでの、取り込まれたデータの待ち時間。 インジェストの待ち時間の長さは、インジェストのシナリオに応じて異なります。 |
    | インジェストの結果 | Count | Count | 失敗および成功したインジェスト操作の合計数。 **[Apply Splitting]\(分割の適用\)** を使用して、成功および失敗した結果のバケットを作成し、ディメンションを分析します ( **[値]**  >  **[状態]** )。|
    | インジェストの使用率 | Percent | Avg、Max、Min | 割り当てられた合計リソースからのデータのインジェストに使用された実際のリソースと、インジェストを実行するために容量ポリシーに割り当てられた合計リソースの間の比率。 既定の容量ポリシーでは、同時実行のインジェスト操作は 512 以下、またはインジェストに使用されるクラスター リソースは 75% 以下です。 80% 以下の平均インジェスト使用率が、クラスターにとって持続可能な状態です。 インジェスト使用率の最大値は 100% であり、これは、すべてのクラスター インジェスト機能が使用され、インジェストがキューに入れられる結果となる可能性を示します。 |
    | インジェストの量 (MB 単位) | Count | Max、Min、Sum | 圧縮前にクラスターにインジェストされたデータの合計サイズ (MB 単位)。 |
    | キープ アライブ | Count | Avg | クラスターの応答性を追跡します。 応答性の高いクラスターは 1 の値を返し、ブロックまたは切断されたクラスターは 0 を返します。 |
    | クエリ実行時間 | Seconds | Count、Avg、Min、Max、Sum | クエリ結果を受け取るまでの合計時間 (ネットワーク待ち時間は含まれません)。 |
    | | | |

    [サポートされる Azure Data Explorer クラスター メトリック](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)に関する追加情報

2. 同じグラフにプロットされた複数のメトリックを表示する場合は、 **[メトリックの追加]** ボタンを選択します。
3. 1 つのビューに複数のグラフを表示する場合は、 **[+ New chart]\(+ 新規グラフ\)** ボタンを選択します。
4. 時間範囲を変更する場合は、時刻の選択ツールを使用します (既定: 過去 24 時間)。
5. ディメンションを持つメトリックには、[ **[フィルターの追加]** および **[Apply Splitting]\(分割の適用\)** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) を使用します。
6. グラフ構成をダッシュボードに追加して、再度表示できるようにする場合は、 **[ダッシュボードにピン留めする]** を選択します。
7. 設定した条件を使用してメトリックを視覚化するには、 **[新しいアラート ルール]** を設定します。 新しいアラート ルールにはターゲット リソース、メトリック、分割、およびグラフからのフィルター ディメンションが含まれます。 これらの設定は、[アラート ルールの作成ウィンドウ](/azure/azure-monitor/platform/metrics-charts#create-alert-rules)で変更します。

[メトリックス エクスプローラー](/azure/azure-monitor/platform/metrics-getting-started)の使用に関する追加情報。


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [クイック スタート:Azure Data Explorer でデータのクエリを実行する](web-query-data.md)
