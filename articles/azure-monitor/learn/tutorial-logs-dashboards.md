---
title: Azure Log Analytics データのダッシュボードを作成して共有する | Microsoft Docs
description: このチュートリアルでは、保存されているすべてのログ検索を Log Analytics ダッシュボードに可視化して、環境をわかりやすく表示できるようにする方法について説明します。
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
ms.date: 09/14/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 5ed0cfba9abaed1f1fdbacc8fcf28918403b82f5
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53186618"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Log Analytics データのダッシュボードを作成して共有する

Log Analytics ダッシュボードは、保存されているすべてのログ検索を可視化して、組織の IT 運用データを検索、関連付け、および共有できるようにします。  このチュートリアルでは、IT 運用サポート チームがアクセスする共有ダッシュボードをサポートするために使用されるログ検索の作成について説明します。  学習内容は次のとおりです。

> [!div class="checklist"]
> * Azure ポータルで共有ダッシュボードを作成する
> * パフォーマンス ログ検索を視覚化する 
> * ログ検索を共有ダッシュボードに追加する 
> * 共有ダッシュボードのタイルをカスタマイズする

このチュートリアルの例を完了するには、[Log Analytics ワークスペースに接続された](../../azure-monitor/learn/quick-collect-azurevm.md)既存の仮想マシンが必要です。  
 
## <a name="log-in-to-azure-portal"></a>Azure Portal へのログイン
Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にログインします。 

## <a name="create-a-shared-dashboard"></a>共有ダッシュボードを作成する

Microsoft Azure ポータルにサインインすると、[ダッシュボード](../../azure-portal/azure-portal-dashboards.md)が最初に表示されます。<br> ![Azure ポータル ダッシュボード](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

すべての Azure リソースの IT にとって最も重要な運用データをここにまとめて表示でき、Log Analytics のテレメトリも表示できます。  ログ検索の可視化を始める前に、ダッシュボードを作成して共有しましょう。  これにより、折れ線グラフで表示されるパフォーマンス ログ検索のサンプルを取得した後、それをダッシュボードに追加できます。  

ダッシュボードを作成するには、現在のダッシュボードの名前の隣にある **[新しいダッシュボード]** ボタンを選択します。<br> ![Azure ポータルで新しいダッシュボードを作成する](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

この操作を実行すると、新しい空のプライベート ダッシュボードが作成され、カスタマイズ モードに切り替わります。カスタマイズ モードでは、ダッシュボードに名前を付けられるほか、タイルを追加したり並べ替えたりできます。 ダッシュボードの名前を編集して、このチュートリアル用の名前 "*サンプル ダッシュボード*" を指定し、**[カスタマイズの完了]** を選択します。<br><br> ![カスタマイズした Azure ダッシュボードを保存する](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

作成したダッシュボードは既定ではプライベートです。つまり、自分だけがこのダッシュボードを見ることができます。 他のユーザーもこのダッシュボードを表示できるようにするには、ダッシュボードの他のコマンドと共に表示されている **[共有]** ボタンを使用します。<br> ![Azure ポータルで新しいダッシュボードを共有する](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

ダッシュボードの発行先となるサブスクリプションとリソース グループを選択するよう求められます。 便宜上、ポータルの発行機能によって、 **dashboards**という名前のリソース グループにダッシュボードを配置するように案内されます。  選択したサブスクリプションを確認し、**[発行]** をクリックします。  ダッシュボードに表示される情報へのアクセスは、[Azure リソース ベースのアクセスの制御](../../role-based-access-control/role-assignments-portal.md)によって制御されます。   

## <a name="visualize-a-log-search"></a>ログ検索を可視化する

Azure ポータルのログ検索ポータルで、1 行の基本的なクエリを作成できます。 ログ検索ポータルは、外部ポータルを起動しなくても使用できます。また、このポータルを使用して、警告ルールの作成、コンピューター グループの作成、クエリ結果のエクスポートなど、さまざまな機能をログ検索で実行することができます。 

[Log Analytics ポータル](../../azure-monitor/log-query/get-started-portal.md)は、ログ検索ポータルで使用できない高度な機能を提供する専用ポータルです。 たとえば、複数行のクエリを編集したり、コードを選択的に実行したりできます。また、状況依存の Intellisense、スマート分析などの機能もあります。 高度な分析ポータルで、グラフィカルな形式で表示されるパフォーマンス ビューを作成し、今後の検索のために保存し、先ほど作成した共有ダッシュボードにピン留めします。   

高度な分析ポータルは、ログ検索ポータル内のリンクから起動します。<br> ![高度な分析ポータルを起動する](media/tutorial-logs-dashboards/log-analytics-advancedportal-01.png)

分析ポータルで、Windows と Linux の両方のコンピューターでのプロセッサ使用率のレコードのみを返す次のクエリを入力します。このクエリは、Computer と TimeGenerated 別にレコードをグループ化してビジュアル グラフに表示します。

```
Perf | where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1m), Computer | render timechart
```

右上の **[クエリの保存]** を選択して、クエリを保存します。<br> ![高度な分析ポータルでクエリを保存する](media/tutorial-logs-dashboards/log-analytics-advancedportal-02.png)<br><br> **[クエリの保存]** コントロール パネルで、名前 ("*Azure VM - プロセッサ使用率*" など) を指定し、**[保存]** をクリックします。  この方法で、検索するための一般的なクエリのライブラリを作成したり、全体を書き直すことなくクエリを変更したりできます。  最後に、ページの右上中央にあ る **[Pin chart to your Azure dashboard]\(グラフを Azure ダッシュボードにピン留め\)** を選択して、先ほど作成した共有ダッシュボードにピン留めします。  

ダッシュボードにクエリがピン留めされ、一般的なタイトルとその下にコメントが表示されます。<br> ![Azure ダッシュボードの例](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)<br><br>  見た人が簡単に理解できる意味のある名前に変更する必要があります。  タイルを右クリックし、**[タイルの編集]** を選択します。  タイルのタイトルとサブタイトルのカスタマイズが完了したら、**[更新]** をクリックします。  変更を発行するか破棄するかを確認するバナーが表示されます。  **[変更の発行]** をクリックした後、**[タイルの編集]** コントロール ウィンドウを閉じます。  

![構成が完了したサンプル ダッシュボード](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure ポータルでダッシュボードを作成して、ログ検索を追加する方法について学習しました。  次のチュートリアルに進んで、ログ検索の結果に基づいて実装できる異なる応答について学習してください。  

> [!div class="nextstepaction"]
> [Log Analytics のアラートを使用してイベントに応答する](tutorial-response.md)
