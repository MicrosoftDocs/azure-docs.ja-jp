---
title: Azure Monitor のメトリック グラフの例
description: Azure Monitor データの視覚化について学習します。
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 9b2ab664f319de07fd70bd1a22b1ba6d64ac208f
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320257"
---
# <a name="metric-chart-examples"></a>メトリック グラフの例 

Azure プラットフォームでは [1,000 個を超えるメトリック](./metrics-supported.md)が提供されます。その多くはディメンションを備えています。 [ディメンション フィルター](./metrics-charts.md)の使用、[分割](./metrics-charts.md)の適用、グラフの種類の制御、グラフ設定の調整を通じて、お客様のインフラストラクチャとアプリケーションの正常性に関する分析情報を提供する強力な診断ビューとダッシュボードを作成できます。 この記事では、お客様が[メトリックス エクスプローラー](./metrics-charts.md)を使用して作成できるグラフの例をいくつか紹介し、これらのそれぞれのグラフを構成するために必要な手順を説明します。

お持ちの優れたグラフのサンプルを世間に公表しませんか。 GitHub のこのページに投稿して、ご自分のグラフのサンプルをそこに公表しましょう。

## <a name="website-cpu-utilization-by-server-instances"></a>サーバー インスタンスごとの Web サイトの CPU 使用率

このグラフは、App Service の CPU が許容範囲内にあるかどうかを示し、負荷が正しく分散されたかどうかを判断するためにインスタンスごとの内訳を表示します。 このグラフを見ると、午前 6 時まで単一のサーバー インスタンスでアプリが実行された後、別のインスタンスが追加されてスケールアップされたことがわかります。

![サーバー インスタンスごとの平均 CPU 使用率の折れ線グラフ](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>このグラフを構成する方法

お客様の App Service リソースを選択し、 **[CPU の割合]** メトリックを見つけます。 次に、 **[Apply splitting]\(分割の適用\)** をクリックし、 **[インスタンス]** ディメンションを選択します。

## <a name="application-availability-by-region"></a>リージョンごとのアプリケーションの可用性

リージョンごとのアプリケーションの可用性を確認して、どの地理的な場所に問題があるかを特定します。 このグラフには、Application Insights の可用性メトリックが表示されています。 監視対象のアプリケーションでは、米国東部のデータセンターからの可用性に問題はありませんが、米国西部と東アジアからの可用性については部分的な問題が発生していることがわかります。

![場所ごとの平均可用性のグラフ](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>このグラフを構成する方法

最初に、お客様の Web サイトに対して [Application Insights の可用性](../app/monitor-web-app-availability.md)の監視をオンにする必要があります。 その後、お客様の Application Insights リソースを選択し、可用性メトリックを選択します。 **[実行場所]** ディメンションで分割を適用します。

## <a name="volume-of-storage-account-transactions-by-api-name"></a>API 名ごとのストレージ アカウントのトランザクション数

ストレージ アカウント リソースで過剰な量のトランザクションが発生しています。 トランザクション メトリックを使用すると、過剰な負荷の原因となっている API を特定できます。 次のグラフでは、関心のある API 呼び出しのみにビューを絞り込むためにフィルタリングと分割で同じディメンション (API 名) が構成されていることに注意してください。

![API トランザクションの棒グラフ](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>このグラフを構成する方法

メトリックの選択ツールで、お客様のストレージ アカウントと **[トランザクション]** メトリックを選択します。 グラフの種類を**棒グラフ**に切り替えます。 **[Apply splitting]\(分割の適用\)** をクリックし、 **[API 名]** ディメンションを選択します。 次に、 **[フィルターの追加]** をクリックし、もう一度 **[API 名]** ディメンションを選択します。 フィルター ダイアログで、グラフにプロットしたい API を選択します。

## <a name="next-steps"></a>次のステップ

* Azure Monitor [Workbooks](./workbooks-overview.md) について学習します
* [メトリックス エクスプローラー](metrics-charts.md)について学習します

