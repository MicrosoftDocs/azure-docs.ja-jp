---
title: ダウンタイム、SLA、および停止のブック - Application Insights
description: Application Insights リソースと Azure サブスクリプションにわたる 1 つのウィンドウで、Web テストの SLA を計算してレポートします。
ms.topic: conceptual
ms.date: 02/8/2021
ms.openlocfilehash: d225627a27bffd9088956e5aee37ca543e528d4a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714053"
---
# <a name="downtime-sla-and-outages-workbook"></a>ダウンタイム、SLA、および停止のブック

Application Insights リソースと Azure サブスクリプションにわたる 1 つのウィンドウで、Web テストの SLA (サービス レベル アグリーメント) を簡単に計算してレポートする方法を紹介します。 ダウンタイムと停止レポートには、顧客の接続、一般的なアプリケーションの応答時間、および発生したダウンタイムについて理解を深められるように、強力な構築済みのクエリとデータ視覚化が用意されています。

SLA ブック テンプレートには、Application Insights リソースのブック ギャラリーを介して、または上部にある **[SLA Reports]\(SLA レポート\)** を選択して可用性タブを介してアクセスできます。
:::image type="content" source="./media/sla-report/availability.png" alt-text="[SLA Reports]\(SLA レポート\) が強調表示されている可用性タブのスクリーンショット。" lightbox="./media/sla-report/availability.png":::

:::image type="content" source="./media/sla-report/workbook-gallery.png" alt-text="ダウンタイムと停止のブックが強調表示されているブック ギャラリーのスクリーンショット。" lightbox ="./media/sla-report/workbook-gallery.png":::

## <a name="parameter-flexibility"></a>パラメーターの柔軟性

ブックに設定されているパラメーターは、レポートの残りの部分に影響します。

:::image type="content" source="./media/sla-report/outages.png" alt-text="ダウンタイムと停止のブックの [outage/maintenance parameters]\(停止およびメンテナンス パラメーター\) タブのスクリーンショット。" lightbox ="./media/sla-report/outages.png":::

`Subscriptions`、`App Insights Resources`、および `Web Test` パラメーターによって、大まかなリソースのオプションが決まります。 これらのパラメーターは、Log Analytics クエリに基づいており、すべてのレポート クエリで使用されます。

`Failure Threshold` と `Outage Window` を使用すると、サービス停止に関する独自の条件を決定できます。たとえば、選択された期間における、失敗した場所のカウンターに基づく App Insights の可用性アラートに関する条件などです。 一般的なしきい値は、5 分間に 3 つの場所となります。

`Maintenance Period` を使用すると、一般的なメンテナンス頻度を選択できます。メンテナンス期間の例の `Maintenance Window` は datetime セレクターです。 特定の期間に発生したすべてのデータは、結果では無視されます。

`Availability Target 9s` では、ツー ナインからファイブ ナインの Target 9s の目標を指定します。

## <a name="overview-page"></a>[概要] ページ

[概要] ページには、SLA の合計 (定義されている場合はメンテナンス期間を除く)、エンドツーエンドの停止インスタンス、およびアプリケーションのダウンタイムに関する概要情報が含まれています。 停止インスタンスは、停止パラメーターに基づき、テストが失敗するようになってから成功するまでの時間で定義されます。 テストが午前 8 時に失敗するようになり、午前 10 時に再び成功した場合は、データのその期間全体が同じ停止と見なされます。

:::image type="content" source="./media/sla-report/overview.gif" alt-text=" テスト別の概要テーブルを示す [概要] ページの GIF。" lightbox="./media/sla-report/overview.gif":::

また、報告期間中に発生した最長の停止を調査することもできます。

一部のテストはリンクをたどって Application Insights リソースに戻り、さらに調査できますが、それが可能なのは[ワークスペースベースの Application Insights リソース](create-workspace-resource.md)でのみとなります。

## <a name="downtime-outages-and-failures"></a>ダウンタイム、障害、および失敗

**[Outages and Downtime]\(停止とダウンタイム\)** タブには、停止インスタンスの合計と、テスト別に分類されたダウンタイムの合計に関する情報があります。 **[Failures by Location]\(場所別の失敗\)** タブには、問題がある可能性のある接続領域を特定するのに役立つ、テストに失敗した場所の geo マップがあります。

:::image type="content" source="./media/sla-report/outages-failures.gif" alt-text=" ダウンタイムと停止のブックにある [Outages and Downtime]\(停止とダウンタイム\) タブおよび [Failures by Location]\(場所別の失敗\) タブの GIF。" lightbox="./media/sla-report/outages-failures.gif":::

## <a name="edit-the-report"></a>レポートの編集

レポートは、他の [Azure Monitor ブック](../visualize/workbooks-overview.md)と同様に編集できます。 チームのニーズに基づいて、クエリや視覚化をカスタマイズできます。

:::image type="content" source="./media/sla-report/edit.gif" alt-text=" [編集] ボタンを選択し、視覚化を円グラフに変更している状態の GIF。" lightbox="./media/sla-report/edit.gif":::

### <a name="log-analytics"></a>Log Analytics

クエリはすべて [Log Analytics](../logs/log-analytics-overview.md) で実行し、他のレポートまたはダッシュボードで使用することができます。 パラメーターの制限を解除して、コア クエリを再利用してください。

:::image type="content" source="./media/sla-report/logs.gif" alt-text=" ログ クエリの GIF。" lightbox="./media/sla-report/logs.gif":::

## <a name="access-and-sharing"></a>アクセスと共有

レポートは、チームやリーダーと共有したり、ダッシュボードにピン留めしてさらに使用したりすることができます。 ユーザーは、実際のブックが格納されている Application Insights リソースに対する読み取りアクセス許可とアクセス権を持っている必要があります。

:::image type="content" source="./media/sla-report/share.png" alt-text=" このテンプレートを共有することを示すスクリーンショット。" lightbox= "./media/sla-report/share.png":::

## <a name="next-steps"></a>次のステップ

- [Log Analytics のクエリ最適化のヒント](../logs/query-optimization.md)。
- [ブックでグラフを作成する](../visualize/workbooks-chart-visualizations.md)方法について学習する。
- [可用性テスト](monitor-web-app-availability.md)で Web サイトを監視する方法について学習する。