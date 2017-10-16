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
ms.author: bwren
ms.openlocfilehash: 207526b7a675f92134d045ebefb9a372749bce92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
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

## <a name="error-pane"></a>エラー ウィンドウ
マップ内でノードをクリックすると、そのノードのエラーの概要を示すエラー ウィンドウが右側に表示されます。 エラーは、まず操作 ID でグループ化された後、問題 ID でグループ化されます。

![エラー ウィンドウ](./media/app-insights-app-map/error-pane.png)

エラーをクリックすると、そのエラーの最新のインスタンスが表示されます。

## <a name="resource-health"></a>リソース ヘルス
リソースの種類によっては、エラー ウィンドウの上部にリソースの正常性が表示されます。 たとえば、SQL ノードをクリックすると、データベースの正常性と発生したアラートが表示されます。

![リソース ヘルス](./media/app-insights-app-map/resource-health.png)

リソース名をクリックすると、そのリソースの標準の概要メトリックを表示できます。

## <a name="end-to-end-system-app-maps"></a>エンド ツー エンドのシステム アプリケーション マップ

*SDK バージョン 2.3 以降が必要*

アプリケーションに複数のコンポーネントがある場合 (Web アプリに加え、バックエンド サービスなど)、統合されたアプリ マップにこれらすべてを表示できます。

![Set filters](./media/app-insights-app-map/multi-component-app-map.png)

アプリ マップでは、Application Insights SDK がインストールされているサーバー間での HTTP 依存関係呼び出しに従ってサーバー ノードを検索します。 各 Application Insights リソースには、1 つのサーバーが含まれていると見なされます。

### <a name="multi-role-app-map-preview"></a>マルチロール アプリ マップ (プレビュー)

マルチロール アプリ マップ プレビュー機能では、同じ Application Insights リソースやインストルメンテーション キーにデータを送信する複数のサーバーでアプリ マップを使用できます。 マップ内のサーバーは、テレメトリ項目の cloud_RoleName プロパティに基づいてセグメント化されています。 この構成を有効にするには、[プレビュー] ブレードで *[Multi-role Application Map (マルチロール アプリケーション マップ)]* を *[オン]* に設定します。

この方法は、Micro-Service アプリケーションや、1 つの Application Insights リソース内の複数のサーバー間でイベントを関連付ける他のシナリオで必要になる場合があります。

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>フィードバック
ご意見やご感想は、ポータルのフィードバック オプションからお寄せください。

![MapLink-1 image](./media/app-insights-app-map/13.png)


## <a name="next-steps"></a>次のステップ

* [Azure ポータル](https://portal.azure.com)
