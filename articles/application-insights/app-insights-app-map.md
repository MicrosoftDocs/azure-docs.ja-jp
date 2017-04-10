---
title: "Azure Application Insights のアプリケーション マップ | Microsoft Docs"
description: "アプリケーション コンポーネント間の依存関係を、KPI やアラートと共に視覚的に表します。"
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 9fc7a41dcca3d9e51b8c67b86ef61443945b6bad
ms.lasthandoff: 03/16/2017


---
# <a name="application-map-in-application-insights"></a>Application Insights のアプリケーション マップ
[Azure Application Insights](app-insights-overview.md) のアプリケーション マップは、アプリケーション コンポーネントにおける依存関係のレイアウトを視覚的に表したものです。 負荷やパフォーマンス、エラー、アラートといった KPI がコンポーネントごとに表示されるので、パフォーマンスの問題やエラーの原因となっているコンポーネントを容易に検出することができます。 任意のコンポーネントをクリックして、さらに詳しい診断結果 (Application Insights イベントなど) にアクセスすることができます。 アプリで Azure サービスを使用している場合は、Azure 診断 (SQL Database アドバイザーのアドバイス情報など) をクリックすることもできます。

アプリケーション マップは他のグラフと同様、Azure ダッシュボードにピン留めし、すべての機能を利用することができます。 

## <a name="open-the-application-map"></a>アプリケーション マップを開く
アプリケーション マップは、対象アプリケーションの概要ブレードから開きます。

![open app map](./media/app-insights-app-map/01.png)

![app map](./media/app-insights-app-map/02.png)

マップには次の情報が表示されます。

* 可用性テスト
* クライアント側コンポーネント (JavaScript SDK で監視)
* サーバー側のコンポーネント
* クライアント コンポーネントとサーバー コンポーネントの依存関係

依存関係リンク グループは、展開したり折りたたんだりすることができます。

![collapse](./media/app-insights-app-map/03.png)

特定の種類 (SQL、HTTP など) の依存関係が多数存在する場合、それらがグループ化されて表示される場合があります。 

![grouped dependencies](./media/app-insights-app-map/03-2.png)

## <a name="spot-problems"></a>問題の特定
それぞれのノードには、関連するパフォーマンス指標 (対応するコンポーネントの負荷、パフォーマンス、エラー率など) が表示されます。 

問題のリスクは、警告アイコンによって強調表示されます。 オレンジ色の警告は、要求、ページ ビュー、依存関係の呼び出しにおけるエラーの存在を意味します。 赤色は、エラー率が 5% を超えていることを示します。 このしきい値を調整する場合は、[オプション] を開きます。

![failure icons](./media/app-insights-app-map/04.png)

アクティブな警告も表示されます。 

![active alerts](./media/app-insights-app-map/05.png)

SQL Azure を使用している場合、パフォーマンスを高める方法についての推奨事項が存在するときにアイコンが表示されます。 

![Azure recommendation](./media/app-insights-app-map/06.png)

アイコンをクリックすると、詳しい情報にアクセスできます。

![Azure recommendation](./media/app-insights-app-map/07.png)

## <a name="diagnostic-click-through"></a>診断のリンク
マップ上の各ノードには、診断を目的としたリンクが表示されます。 表示されるオプションは、ノードの種類によって異なります。

![server options](./media/app-insights-app-map/09.png)

Azure でホストされるコンポーネントの場合、そのコンポーネントに直接アクセスするためのリンクがオプションに含まれます。

## <a name="filters-and-time-range"></a>フィルターと時間範囲
既定では、選択した時間範囲に関して入手できるすべてのデータがマップに集約されます。 ただしフィルターを適用することで、表示内容を特定の操作名や依存関係に限定することができます。

* 操作名: ページ ビューのほか、サーバー側の要求の種類が表示対象となります。 このオプションでは、選択した操作に限定して、サーバー/クライアント側ノードの KPI がマップに表示されます。 その特定の操作のコンテキストで呼び出された依存関係が表示されます。
* 依存関係のベース名: AJAX ブラウザーの依存関係とサーバー側の依存関係が表示対象となります。 TrackDependency API を使ってカスタムの依存関係のテレメトリを報告対象にしている場合、それらもここに表示されます。 マップに表示する依存関係は選択できます。 現時点では、この選択によって、サーバー側の要求やクライアント側のページ ビューはフィルタリングされません。

![Set filters](./media/app-insights-app-map/11.png)

## <a name="save-filters"></a>フィルターの保存
適用したフィルターを保存するには、フィルタリングされたビューを [ダッシュボード](app-insights-dashboards.md)にピン留めします。

![Pin to dashboard](./media/app-insights-app-map/12.png)

## <a name="end-to-end-system-app-maps"></a>エンド ツー エンドのシステム アプリケーション マップ

アプリケーションに複数のコンポーネントがある場合 (Web アプリに加え、バックエンド サービスなど)、統合されたアプリ マップにこれらすべてを表示できます。

![Set filters](./media/app-insights-app-map/multi-component-app-map.png)

アプリ マップは、現在のリソース グループ内のすべての Application Insights リソースを探して、サーバー ノードを検出します。 また、現在のリソース グループ内の Application Insights リソースによって追跡されている依存関係の呼び出しをフォローすることでサーバー ノードを検出します。


### <a name="setting-up"></a>設定

> [!NOTE] 
> エンド ツー エンドのシステム マップ アプリはプレビュー段階です。 SDK の特別なバージョンでコンポーネントをインストルメント化する必要があり、特別な URL を使用してアプリ マップを表示する必要があります。 [エンド ツー エンドのシステム アプリ マップを設定する方法をご覧ください](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-app-map-preview.md)。

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>フィードバック
[ご意見やご感想は、ポータルのフィードバック オプションからお寄せください](app-insights-get-dev-support.md)。

![MapLink-1 image](./media/app-insights-app-map/13.png)


## <a name="next-steps"></a>次のステップ

* [Azure ポータル](https://portal.azure.com)
