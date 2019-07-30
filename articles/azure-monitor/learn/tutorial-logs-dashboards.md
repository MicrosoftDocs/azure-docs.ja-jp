---
title: Azure Log Analytics データのダッシュボードを作成して共有する | Microsoft Docs
description: このチュートリアルでは、保存されているすべてのログ クエリを Log Analytics ダッシュボードに可視化して、環境をわかりやすく表示できるようにする方法について説明します。
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 93cda8680bc665055d449e86c24d6565f6fc525f
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296387"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Log Analytics データのダッシュボードを作成して共有する

Log Analytics ダッシュボードは、保存されているすべてのログ クエリを可視化して、組織の IT 運用データを検索、関連付け、および共有できるようにします。  このチュートリアルでは、IT 運用サポート チームがアクセスする共有ダッシュボードをサポートするために使用されるログ クエリの作成について説明します。  学習内容は次のとおりです。

> [!div class="checklist"]
> * Azure ポータルで共有ダッシュボードを作成する
> * パフォーマンス ログ クエリを視覚化する 
> * ログ クエリを共有ダッシュボードに追加する 
> * 共有ダッシュボードのタイルをカスタマイズする

このチュートリアルの例を完了するには、[Log Analytics ワークスペースに接続された](quick-collect-azurevm.md)既存の仮想マシンが必要です。  
 
## <a name="sign-in-to-azure-portal"></a>Azure Portal にサインインする
Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。 

## <a name="create-a-shared-dashboard"></a>共有ダッシュボードを作成する
**[ダッシュボード]** を選択して、既定の[ダッシュボード](../../azure-portal/azure-portal-dashboards.md)を開きます。 ダッシュ ボードの外観は次の例とは異なります。

![Azure Portal ダッシュボード](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

すべての Azure リソースの IT にとって最も重要な運用データをここにまとめて表示でき、Log Analytics のテレメトリも表示できます。  ログ クエリの可視化を始める前に、ダッシュボードを作成して共有しましょう。  その後、折れ線グラフとして表示される、パフォーマンス ログ クエリの例に焦点を当てます。これはダッシュボードに追加できます。  

ダッシュボードを作成するには、現在のダッシュボードの名前の隣にある **[新しいダッシュボード]** ボタンを選択します。

![Azure portal で新しいダッシュボードを作成する](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

この操作を実行すると、新しい空のプライベート ダッシュボードが作成され、カスタマイズ モードに切り替わります。カスタマイズ モードでは、ダッシュボードに名前を付けられるほか、タイルを追加したり並べ替えたりできます。 ダッシュボードの名前を編集して、このチュートリアル用の名前 "*サンプル ダッシュボード*" を指定し、 **[カスタマイズの完了]** を選択します。<br><br> ![カスタマイズした Azure ダッシュボードを保存する](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

作成したダッシュボードは既定ではプライベートです。つまり、自分だけがこのダッシュボードを見ることができます。 他のユーザーもこのダッシュボードを表示できるようにするには、ダッシュボードの他のコマンドと共に表示されている **[共有]** ボタンを使用します。

![Azure portal で新しいダッシュボードを共有する](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

ダッシュボードの発行先となるサブスクリプションとリソース グループを選択するよう求められます。 便宜上、ポータルの発行機能によって、 **dashboards**という名前のリソース グループにダッシュボードを配置するように案内されます。  選択したサブスクリプションを確認し、 **[発行]** をクリックします。  ダッシュボードに表示される情報へのアクセスは、[Azure リソース ベースのアクセスの制御](../../role-based-access-control/role-assignments-portal.md)によって制御されます。   

## <a name="visualize-a-log-query"></a>ログ クエリを可視化する
[Log Analytics](../log-query/get-started-portal.md) は、ログ クエリとその結果を処理するために使用する専用ポータルです。 たとえば、複数行のクエリを編集したり、コードを選択的に実行したりできます。また、状況依存の Intellisense、スマート分析などの機能もあります。 このチュートリアルでは、Log Analytics を使用して、グラフィカル形式でパフォーマンス ビューを作成し、今後の検索のためにそれを保存し、事前に作成した共有ダッシュボードにそれをピン留めします。

[Azure Monitor] メニューの **[ログ]** を選択して Log Analytics を開きます。 新しい空のクエリから開始されます。

![ホーム ページ](media/tutorial-logs-dashboards/homepage.png)

Windows と Linux の両方のコンピューターでのプロセッサ使用率のレコードを返す次のクエリを入力します。このクエリは、Computer と TimeGenerated 別にレコードをグループ化してビジュアル グラフに表示します。 **[実行]** をクリックしてクエリを実行し、結果のグラフを確認します。

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

ページの上部にある **[保存]** を選択して、クエリを保存します。

![クエリを保存する](media/tutorial-logs-dashboards/save-query.png)

**[クエリの保存]** コントロール パネルで、名前 ("*Azure VM - プロセッサ使用率*" など) とカテゴリ ("*ダッシュボード*" など) を指定し、 **[保存]** をクリックします。  このようにすることで、一般的なクエリのライブラリを作成し、使用したり変更したりできます。  最後に、ページの右上隅にある **[ピン留めする]** を選択し、ダッシュボード名を選択することで、事前に作成した共有ダッシュボードにこれをピン留めします。

ダッシュボードにクエリがピン留めされ、一般的なタイトルとその下にコメントが表示されます。

![Azure ダッシュボードの例](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 見た人が簡単に理解できる意味のある名前に変更する必要があります。  [編集] をクリックして、タイルのタイトルとサブタイトルをカスタマイズし、 **[更新]** をクリックします。  変更を発行するか破棄するかを確認するバナーが表示されます。  **[コピーの保存]** をクリックします。  

![構成が完了したサンプル ダッシュボード](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure portal でダッシュボードを作成して、ログ クエリを追加する方法について学習しました。  次のチュートリアルに進んで、ログ クエリの結果に基づいて実装できる異なる応答について学習します。  

> [!div class="nextstepaction"]
> [Log Analytics のアラートを使用してイベントに応答する](tutorial-response.md)
