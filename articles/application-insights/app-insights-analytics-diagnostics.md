---
title: "Azure Application Insights における Web アプリのパフォーマンス変動のスマート診断 | Microsoft Docs"
description: "Web アプリのパフォーマンス テレメトリにおける急上昇や段階的変化の自動診断。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: cfreeman
ms.openlocfilehash: 5e53bc714d89bf6204681349e7890e0b8fbc7046
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="diagnose-sudden-changes-in-your-app-telemetry"></a>アプリのテレメトリにおける急激な変化を診断する

"*この機能はプレビュー段階にあります。*"

Web アプリのパフォーマンスや使用状況における急激な変化をクリック 1 回で診断します。 スマート診断機能は、[Application Insights](app-insights-overview.md) の [Analytics](app-insights-analytics.md) で時間グラフを作成するとすぐに利用できます。 分析結果の傾向に急激な上昇や低下などの異常な変化がある場合は、変化を説明できるディメンションや関連する値のパターンがスマート診断によって特定されます。 これにより、問題をすばやく診断できるようになります。 

次の例では、スマート診断によって変化に関連するプロパティ値のパターンが特定され、そのパターンの有無による結果の違いが強調されています。

![分析例の診断結果](./media/app-insights-analytics-diagnostics/analytics-result.png)
 

## <a name="diagnose-data-changes"></a>データの変化を診断する

1.  Analytics でクエリを実行し、それを時間グラフとしてレンダリングします。 
2.  強調されているピーク ポイントがある場合は、それをクリックします。
 
    ![ピーク ポイント](./media/app-insights-analytics-diagnostics/peak.png)

    診断によってパターンを検出するまでに数秒かかります。

3. [Diagnostics Results (診断結果)] タブに、データの不連続性を説明できるパターンが表示されます。

    ![result](./media/app-insights-analytics-diagnostics/result.png)
 
    表示されるテキストは、変化との相関関係があると思われるディメンション値を示しています。 この例でのディメンション値は、特定の要求とブラウザーのバージョンに関連しています。

    また、このグラフは true および false フィルターによって 2 つの成分に分けられています。 false 成分は変化のない傾向を示しています。 つまり、診断によって特定された問題のあるディメンションの組み合わせを除外した場合、テレメトリの結果には変化がないことになります。 一方で、その組み合わせの範囲内での結果は、強調表示されている調査領域内で大幅な変化を示しています。 このことから、変化を説明できるプロパティの組み合わせが診断によって判明したことがわかります。

4.  パターンが複雑な場合は、**[すべて表示]** の上にポインターを置いてディメンションを表示する必要があります。

    ![すべて表示](./media/app-insights-analytics-diagnostics/show-all.png)
 
5.  通知すべき重要なパターンが診断で見つからなかった場合は、"結果なし" ページが表示されます。 この時点で、クエリを変更することができます。 たとえば、Analytics クエリで時間や値の範囲を絞り込むことにより、さらに詳しく分析したり、良好な結果が得られる可能性を高めたりできます。

Web サイトの特定のページに問題があり、特定のブラウザーでその問題が発生するということが把握できれば、すぐに問題のページを開いて最近の変更箇所を調べることができます。

## <a name="try-the-demo"></a>デモの試用

サンプル データによるデモを確認するには、[こちら](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA3VSTY%2FTQAy991dYPXWlLf0QIO2KIiGWA3duiMPsxEnMzhe2p6WIH48nVUsuGylRNPOe3%2FOzN5vFZgPfRhL4VZHPIGM%2BCdgHdESgpMjOKx0RnsgNKYuSF%2BjRaWUE7xKMGIoBgTpMSv2Z0jBxOWc1QBWEPjM4EMUCP2uc0A3x8E5HKMi%2BEQNC7oHRbIgKdJWdUk5vmr9PvdkArildit%2Fcrk0lBDjnyhBzk%2FKVxdTy0QhNY6RhDPYqdlCy9XMV96NjBZc68IH8y6Tzuf01iZxeIZ%2FI5DqMOYmaQQRXNUdz6qGb5WOdSKEXnOozHtEFK%2Bh0qnq5YQzGF9DcoinoqbcigkO0NOZRNGOZaaBkMuat5xznFOtULKhG%2BdrGlVDhy%2B8SMlsETV8dD6gTd0YrbsBrFq6U1v%2Filv4C%2FsJpRJuwUrQTZ0P7eIDOHLeD1X67e7%2Fe7dbbB9htH%2Ffbu4vQDfvhFez%2B8a1h%2F1f3VSy%2BJ4Ol1oN8X4qN0qMZWv44HJanzKFLeJIltKcRpcbomP7gbHNkdV2Xe1uqO3g%2BwzOl1c3PvbmMlC7KjKlry2GX0w4s%2FgFoo5%2BhBAMAAA%3D%3D&timespan=PT24H)をクリックしてください。

## <a name="how-it-works"></a>動作のしくみ

スマート診断では、[DiffPatterns](app-insights-analytics-reference.md) 操作に基づく教師なしの高度な機械学習アルゴリズムを使用します。 データの変化を説明できるような候補となるパターンを探し、 メトリックに対する各候補の影響を分析して、変化との相関関係が最大となるパターンを示します。

## <a name="no-diagnostic-points"></a>診断ポイントがない場合

スマート診断は、以下の条件が満たされている場合にのみ機能します。

 * スマート診断の設定がオンになっている。 Analytics で [設定] アイコンの下を確認してください。
 * Analytics 設定の [Smart Diagnostics (スマート診断)] オプションが選択されている。 
 * 時間軸: グラフの X 軸の種類は `datetime` になっている必要があります。
 * 折れ線または面グラフ: 診断はこれらの種類のグラフでのみ機能します。 クエリの末尾に `| render timechart` または `| render areachart` を使用するか、ドロップダウン セレクターから折れ線グラフまたは面グラフを選択してください。
 * 不連続性: データ内に著しい不連続性があることが必要です。
 * 分析対象として十分なポイント数。
 * クエリ内に複数の summarize 句がないこと。
 * 名前の定義が含まれる project 句が summarize 句の前にないこと。

 
 ## <a name="related-articles"></a>関連記事

 * [Analytics のチュートリアル](app-insights-analytics-tour.md)
 * [スマート検出](app-insights-proactive-diagnostics.md)では、パフォーマンスの問題に関するアラートを自動的に通知します。