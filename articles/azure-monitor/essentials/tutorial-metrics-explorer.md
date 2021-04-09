---
title: チュートリアル - Azure Monitor でのメトリック グラフの作成
description: Azure メトリックス エクスプローラーを使用し、メトリック グラフを作成する方法について説明します。
author: bwren
ms.author: bwren
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 9923bfcbd439ef5e20e44e9c397199fe06f40b43
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102032910"
---
# <a name="tutorial-create-a-metrics-chart-in-azure-monitor"></a>チュートリアル:Azure Monitor でのメトリック グラフの作成
メトリックス エクスプローラーは、Azure portal 内の Azure Monitor の 1 機能であり、メトリック値からグラフを作成したり、傾向を視覚的に関連付けたり、メトリック値の急上昇または急降下を調査したりすることができます。 メトリックス エクスプローラーを使用して、ご利用の Azure リソースの正常性と使用率を調べ、カスタム メトリックに基づいてグラフをプロットします。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * グラフをプロットするメトリックを選択する
> * メトリック値のさまざまな集計を実行する
> * グラフの時間の範囲と粒度を変更する

次のビデオでは、この記事で概説している手順よりも詳細なシナリオが示されています。 メトリックを初めて使用する場合は、まずこの記事を読んでから、ビデオを見て詳細を確認することをお勧めします。 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、監視する Azure リソースが必要です。 メトリックをサポートする Azure サブスクリプションの任意のリソースを使用できます。 リソースがメトリックをサポートしているかどうかを判断するには、Azure portal のメニューにアクセスし、メニューの **[監視]** セクションに **[メトリック]** オプションがあることを確認します。


## <a name="log-in-to-azure"></a>Azure にログインする
Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にログインします。

## <a name="open-metrics-explorer-and-select-a-scope"></a>メトリックス エクスプローラーを開いてスコープを選択する
メトリックス エクスプローラーは、[Azure Monitor] メニューから、または Azure portal のリソースのメニューから開くことができます。 使用するオプションに関係なく、すべてのリソースのメトリックを使用できます。 

1. **[Azure Monitor]** メニューまたはリソースのメニューの **[監視]** セクションから **メトリック** を選択します。

1. メトリックを表示するリソースである **スコープ** を選択します。 リソースのメニューからメトリックス エクスプローラーを開いた場合は、スコープに既に値が設定されています。 リソース スコープ ピッカーの各種機能の詳細については、[こちらの記事](../essentials/metrics-charts.md#resource-scope-picker)を参照してください。

    ![スコープを選択する](media/tutorial-metrics-explorer/scope-picker.png)

2. スコープに複数の名前空間がある場合は、**名前空間** を選択します。 名前空間は、メトリックを簡単に見つけることができるように整理するために用意された手段です。 たとえば、ストレージ アカウントには、ファイル、テーブル、BLOB、およびキューのメトリックを格納するための個別の名前空間があります。 多くの種類のリソースは、名前空間を 1 つ備えているだけです。

3. 選択したスコープと名前空間の利用可能なメトリックの一覧からメトリックを 1 つ選択します。

    ![メトリックを選択する](media/tutorial-metrics-explorer/metric-picker.png)

4. 必要に応じて、メトリックの **集計** を変更することができます。 これにより、グラフの時間粒度全体でメトリック値がどのように集計されるかが定義されます。 たとえば、時間の粒度を 15 分に設定し、集計を合計に設定した場合、グラフ内の各ポイントは、15 分セグメントごとに収集されたすべての値の合計になります。

    ![スクリーンショットには、Sum Ingress for contosoretailweb というタイトルのグラフが表示されています。](media/tutorial-metrics-explorer/chart.png)

5. 同じグラフに複数のメトリックをプロットして表示する場合は、 **[メトリックの追加]** ボタンを使用して上記の手順を繰り返します。 1 つのビューに複数のグラフを表示する場合は、 **[+ New chart]\(+ 新規グラフ\)** ボタンを選択します。

## <a name="select-a-time-range-and-granularity"></a>時間の範囲と粒度を選択する

既定では、直近の 24 時間のメトリック データがグラフに表示されます。 時間の選択ツールを使用して、グラフの **時間の範囲** を変更するか、各データ ポイントの時間の範囲を定義する **時間の粒度** を変更します。 グラフでは、指定された集計を使用して、指定された時間粒度でサンプリングされたすべての値が計算されます。

![[時間の範囲] パネルを変更する](media/tutorial-metrics-explorer/time-picker.png)


グラフ内で関心のある領域 (急上昇または急降下) を調査するには、**時間ブラシ** を使用します。 領域の先頭にマウス ポインターを置き、マウスの左ボタンを押したまま領域のもう一方の側にドラッグしてボタンを離します。 その時間範囲のグラフが拡大されます。 

![時間ブラシ](media/tutorial-metrics-explorer/time-brush.png)

## <a name="apply-dimension-filters-and-splitting"></a>ディメンションのフィルターと分割を適用する
メトリックに対して追加の分析を実行したり、データの潜在的な外れ値を特定したりできる高度な機能については、次のリファレンスを参照してください。

- [フィルター処理](../essentials/metrics-charts.md#filters)を使用すると、グラフに表示するディメンション値を選択できます。 たとえば、"*サーバー応答時間*" メトリックのグラフを作成する場合は、成功した要求を表示します。 

- [分割](../essentials/metrics-charts.md#apply-splitting)を使用すると、グラフ内でディメンションの値ごとに個別のラインを表示するか、それとも値を集計して 1 本のラインとして表示するかを制御できます。 たとえば、すべてのサーバー インスタンスの応答時間を平均した 1 本のラインを表示することも、サーバーごとに個別のラインを表示することもできます。 

フィルター処理と分割が適用されたグラフの例については、[こちら](../essentials/metric-chart-samples.md)を参照してください。

## <a name="advanced-chart-settings"></a>グラフの詳細設定

グラフのスタイルおよびタイトルをカスタマイズして、グラフの詳細設定を変更することができます。 カスタマイズが完了したら、ダッシュボードにピン留めして作業内容を保存します。 メトリック アラートを構成することもできます。 Azure Monitor メトリックス エクスプローラーの上記のような高度な機能を学習するには、「[Azure メトリックス エクスプローラーの高度な機能](../essentials/metrics-charts.md#locking the-range-of-the-y-axis)」をご覧ください。


## <a name="next-steps"></a>次のステップ
これで、Azure Monitor でメトリックを使用する方法を学習できました。次は、メトリックを使用してプロアクティブなアラートを送信する方法について説明します。

> [!div class="nextstepaction"]
> [Azure Monitor を使用してメトリック アラートを作成、表示、管理する](../essentials/metrics-charts.md#alert-rules)

