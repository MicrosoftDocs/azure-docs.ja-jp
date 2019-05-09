---
title: Visual Studio での傾向の分析 | Microsoft Docs
description: Visual Studio で、Application Insights テレメトリの傾向を分析、視覚化、調査します。
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: carmonm
ms.assetid: 3150c6fc-2691-44f6-a290-fc5cd68e692a
ms.service: application-insights
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/17/2017
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 898f0974a6a29abde5c84d611adc8d50c3873141
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121073"
---
# <a name="analyzing-trends-in-visual-studio"></a>Visual Studio での傾向の分析
Application Insights Trends ツールを使用すると、Web アプリケーションの重要なテレメトリ イベントが時間の経過に伴ってどのように変化しているかを視覚化できます。これにより、問題と異常を迅速に特定できるようになります。 より詳細な診断情報が得られるようになるため、Trends は、アプリのパフォーマンスの向上、例外の原因の追跡、カスタム イベントからの知見の獲得に役立ちます。

![Example Trends window](./media/visual-studio-trends/app-insights-trends-hero-750.png)

## <a name="configure-your-web-app-for-application-insights"></a>Application Insights 向けの Web アプリの構成

これがまだ完了していない場合は、[Web アプリを Application Insights 向けに構成します](../../azure-monitor/app/app-insights-overview.md)。 これにより、Application Insights ポータルにテレメトリを送信できます。 Trends ツールはそこからテレメトリを読み取ります。

Application Insights Trends は、Visual Studio 2015 Update 3 以降で利用できます。

## <a name="open-application-insights-trends"></a>Application Insights Trends を開く方法
[Application Insights の傾向] ウィンドウを開くには、次のいずれかを行います。

* Application Insights のツール バー ボタンから、 **[テレメトリの傾向を調べる]** を選択する。
* プロジェクトのコンテキスト メニューから、**[Application Insights]、[テレメトリの傾向を調べる]** の順に選択する。
* Visual Studio のメニュー バーから、**[表示]、[その他のウィンドウ]、[Application Insights の傾向]** を選択する。

リソースの選択を求めるメッセージが表示される場合があります。 **[リソースの選択]** をクリックし、Azure サブスクリプションでサインインしてから、テレメトリの傾向の分析対象とする Application Insights リソースを一覧から選択します。

## <a name="choose-a-trend-analysis"></a>傾向分析の選択
![Menu of common types of trend analysis](./media/visual-studio-trends/app-insights-trends-1-750.png)

5 種類の一般的な傾向分析からいずれかを選択して開始します。各傾向分析では、過去 24 時間のデータが分析されます。

* **[サーバー要求のパフォーマンスの問題を調査します。]** - 対象のサービスに対して行われた要求が、応答時間ごとにグループ化されます。
* **[サーバー要求におけるエラーを分析します。]** - 対象のサービスに対して行われた要求が、HTTP 応答コードごとにグループ化されます。
* **[アプリケーションの例外を確認します。]** - サービスからの例外が、例外の種類ごとにグループ化されます。
* **[アプリケーションの依存関係のパフォーマンスを確認します。]** - 対象のサービスに呼び出されたサービスが、応答時間ごとにグループ化されます。
* **[カスタム イベントを検査します。]** - サービスについて設定してあるカスタム イベントが、イベントの種類ごとにグループ化されます。

事前構築済みのこれらの分析は、Trends ウィンドウの左上隅にある **[一般的な種類のテレメトリの解析を表示する]** ボタンから後で利用できます。

## <a name="visualize-trends-in-your-application"></a>アプリケーションでの傾向の視覚化
Application Insights Trends では、アプリのテレメトリが時系列で視覚化されます。 時系列の視覚化それぞれで、1 種類のテレメトリを、そのいずれかのプロパティに基づいてグループ化したうえで、一定期間を対象に表示できます。 たとえば、送信元の国別にグループ分けして、過去 24 時間のサーバー要求を確認する必要があるとします。 この例では、視覚化上の各バブルは、ある国/地域から 1 時間の間に行われたサーバー要求の数を表します。

ウィンドウの上部にあるコントロールを使用して、表示するテレメトリの種類を調整します。 まず、関心のあるテレメトリの種類を選択します。

* **テレメトリの種類** - サーバー要求、例外、依存関係、またはカスタム イベント
* **時間範囲** - 過去 3 日前から 30 分前までの任意の範囲。
* **グループ化** - 例外の種類、問題 ID、国/地域など。

その後、 **[テレメトリの分析]** をクリックしてクエリを実行します。

視覚化のバブル間で移動するには、次の手順に従います。

* バブルをクリックして選択します。これにより、ウィンドウの下部にあるフィルターが更新され、特定の期間に発生したイベントだけが要約されます。
* バブルをダブルクリックして検索ツールに移動し、この期間に発生した個別のテレメトリ イベントをすべて表示します
* Ctrl キーを押しながらバブルをクリックして、視覚化のバブルの選択を解除します。

> [!TIP]
> Trends ツールと検索ツールを併用すれば、何千ものテレメトリ イベントの中から、対象のサービスに関する問題の原因を特定できます。 たとえばある日の午後に、対象のアプリの反応が悪いことに顧客が気付いたら、Trends ツールの使用を開始します。 応答時間ごとにグループ化して、過去数時間の間に対象のサービスに対して行われた要求を分析します。 応答に時間がかかっている要求が存在しないか確認します。 次に、そのバブルをダブルクリックして検索ツールに移動し、それらの要求イベントでフィルター処理します。 検索ツールから、これらの要求の内容を調査し、問題の解決にかかわるコードに移動できます。
> 
> 

## <a name="filter"></a>filter
ウィンドウの下部にあるフィルター コントロールを使用して、より具体的な傾向を特定します。 フィルターを適用するには、その名前をクリックします。 各種フィルターをすばやく切り替えて、テレメトリの特定のディメンションに隠れている可能性のある傾向を突き止めることができます。 例外の種類など、いずれかのディメンションのフィルターを適用すると、それ以外のディメンションのフィルターは淡色表示されますが、クリックできます。フィルターの適用を解除するには、もう一度クリックします。 Ctrl キーを押しながらクリックして、同じディメンションの複数のフィルターを選択します。

![Trend filters](./media/visual-studio-trends/TrendsFiltering-750.png)

複数のフィルターを適用するには 

1. 最初のフィルターを適用します。 
2. 最初のフィルターのディメンション名のそばにある、 **[選択したフィルターを適用し、もう一度クエリを実行します]** ボタンをクリックします。 これにより、最初のフィルターに一致するイベントのみを対象に、テレメトリのクエリが再び実行されます。 
3. 2 番目のフィルターを適用します。 
4. この手順を繰り返して、テレメトリの特定のサブセットに潜む傾向を突き止めます。 たとえば、"GET Home/Index" *と*いうサーバー要求、ドイツが送信元であるサーバー要求、そして応答コードが 500 であったサーバー要求*と*いうぐあいです。 

これらのフィルターの適用を解除するには、ディメンションの **[選択したフィルターを削除し、もう一度クエリを実行します]** ボタンをクリックします。

![Multiple filters](./media/visual-studio-trends/TrendsFiltering2-750.png)

## <a name="find-anomalies"></a>異常の検出
Trends ツールでは、同じ時系列の他のバブルと比べて異常なイベントのバブルを強調表示できます。 [ビューの種類] ボックスの一覧の **[タイム バケット内の数 (異常を強調表示)]** または **[タイム バケット内の割合 (異常を強調表示)]** を選択します。 赤いバブルが異常なバブルです。 異常なバブルは、数/割合が過去の 2 つの期間で発生した数/割合の標準偏差の 2.1 倍を超えているバブルと定義されています (過去 24 時間を表示する場合は 48 時間)。

![Colored dots indicate anomalies](./media/visual-studio-trends/TrendsAnomalies-750.png)

> [!TIP]
> 異常の強調表示は特に、時系列で並んだ小さなバブルの外れ値を見つけるのに役立ちます。強調表示がない場合、これらのバブルは同じような大きさに見える可能性があります。  
> 
> 

## <a name="next"></a>次のステップ
|  |  |
| --- | --- |
| **[Visual Studio での Application Insights の操作](../../azure-monitor/app/visual-studio.md)**<br/>テレメトリの検索、CodeLens でのデータの確認、Application Insights の構成。 いずれも Visual Studio で行うことができます。 |![プロジェクトを右クリックし、[Application Insights]、[検索] を選択する](./media/visual-studio-trends/34.png) |
| **[データの追加](../../azure-monitor/app/asp-net-more.md)**<br/>使用状況、可用性、依存関係、例外の監視。 ログ記録フレームワークからのトレースを統合します。 カスタム テレメトリを記述します。 |![Visual studio](./media/visual-studio-trends/64.png) |
| **[Application Insights ポータルの操作](../../azure-monitor/app/app-insights-dashboards.md)**<br/>ダッシュボード、強力な診断および分析ツール、アラート、アプリケーションのリアルタイム依存関係マップ、テレメトリのエクスポート。 |![Visual studio](./media/visual-studio-trends/62.png) |

