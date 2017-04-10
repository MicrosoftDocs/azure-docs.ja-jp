---
title: "Azure Log Analytics でのカスタム ダッシュボードの作成| Microsoft Docs"
description: "このガイドでは、環境をわかりやすく表示できるように、Log Analytics ダッシュボードが保存済みのすべてのログ検索を視覚化するしくみについて説明します。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a0c8af30fbed064001c3fd393bf0440aa1cb2835
ms.openlocfilehash: a8c9766bf066a7f0dfd28ebb4e41bf0eaf3f05bd
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-custom-dashboard-for-use-in-log-analytics"></a>Log Analytics での使用するカスタム ダッシュボードの作成
このガイドでは、環境をわかりやすく表示できるように、Log Analytics ダッシュボードが保存済みのすべてのログ検索を視覚化するしくみについて説明します。

![サンプルのダッシュ ボード](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

OMS ポータルで作成するカスタム ダッシュボードはすべて、OMS Mobile App でも利用できます。 アプリの詳細については、以下のページを参照してください。

* [Microsoft ストアの OMS モバイル アプリ](http://www.windowsphone.com/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)
* [Apple iTunes の OMS モバイル アプリ](https://itunes.apple.com/app/microsoft-operations-management/id1042424859?mt=8)

![モバイル ダッシュボード](./media/log-analytics-dashboards/oms-search-mobile.png)

## <a name="how-do-i-create-my-dashboard"></a>ダッシュ ボードの作成方法。
まず OMS の概要ページに移動します。 左側に **[マイ ダッシュボード]** タイルが表示されます。 クリックしてダッシュボードへドリルダウンします。

![概要](./media/log-analytics-dashboards/oms-dashboards-overview.png)

## <a name="adding-a-tile"></a>タイルの追加
ダッシュ ボードでは、タイルは、保存済みのログ検索が提供します。 OMS には事前に作成された保存済みの多くのログ検索が付属しているので、すぐに開始できます。 以降の手順で基本的な使い方を説明します。

[マイ ダッシュボード] ビューの **[カスタマイズ]** をクリックしてカスタマイズ モードにします。

![図](./media/log-analytics-dashboards/oms-dashboards-pictorial01.png)

 ページの右側に表示されたパネルに、ワークスペースが保存したすべてのログ検索が表示されます。 保存済みのログ検索をタイルとして視覚化するには、保存した検索条件にマウス ポインターを合わせ、**正符号**アイコンをクリックします。

![タイル 1 の追加](./media/log-analytics-dashboards/oms-dashboards-pictorial02.png)

**正符号**アイコンをクリックすると、[マイ ダッシュボード] ビューに新しいタイルが表示されます。

![タイル 2 の追加](./media/log-analytics-dashboards/oms-dashboards-pictorial03.png)

## <a name="edit-a-tile"></a>タイルを編集する
[マイ ダッシュボード] ビューの **[カスタマイズ]** をクリックしてカスタマイズ モードにします。 編集するタイルをクリックします。 右側のパネルで編集し、オプションを選択します。

![タイルの編集](./media/log-analytics-dashboards/oms-dashboards-pictorial04.png)

![タイルの編集](./media/log-analytics-dashboards/oms-dashboards-pictorial05.png)

### <a name="tile-visualizations"></a>タイルの視覚化
タイルの視覚化は、次の&3; 種類から選択できます。

| グラフの種類 | 実行内容 |
| --- | --- |
| ![横棒グラフ](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png) |保存済みのログ検索の結果のタイムラインを棒グラフとして表示します。または、ログ検索の結果がフィールドごとに集計されているかどうかに応じて、フィールドごとに結果の一覧を表示します。 |
| ![メトリック](./media/log-analytics-dashboards/oms-dashboards-metric.png) |ログ検索結果の合計ヒット数を数値としてタイルに表示します。 メトリックのタイルでは、しきい値に達したときに、タイルを強調表示するしきい値を設定することができます。 |
| ![line](./media/log-analytics-dashboards/oms-dashboards-line.png) |保存されたログ検索結果のヒット数のタイムラインを折れ線グラフとして表示します。 |

### <a name="threshold"></a>しきい値
メトリックの視覚エフェクトを使用して、タイルのしきい値を作成できます。 タイルにしきい値の値を作成するを選択します。 値が選択したしきい値未満、または以上の場合に強調表示するかどうかを選択し、しきい値を設定します。

## <a name="organizing-the-dashboard"></a>ダッシュ ボードの整理
ダッシュボードを整理するには、[マイ ダッシュボード] ビューに移動し、**[カスタマイズ]** をクリックしてカスタマイズ モードにします。 移動するタイルをクリックしてドラッグし、タイルを任意の場所に移動します。

![ダッシュ ボードの整理](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## <a name="remove-a-tile"></a>タイルの削除
タイルを削除するには、[マイ ダッシュボード] ビューに移動し、**[カスタマイズ]** をクリックしてカスタマイズ モードにします。 削除するタイルを選択し、右側のパネルで **[タイルの削除]**を選択します。

![タイルの削除](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## <a name="next-steps"></a>次のステップ
* Log Analytics で[アラート](log-analytics-alerts.md)を作成して、通知を生成し、問題を修正します。

