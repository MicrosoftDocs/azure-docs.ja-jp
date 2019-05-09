---
title: Azure Application Insights による Web アプリケーションのユーザー リテンション期間分析 | Microsoft Docs
description: アプリに戻るユーザーの数を分析します。
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/03/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: bda79520dd86cc14161f6f22cd24feb2e35849ab
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121364"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Azure Application Insights による Web アプリケーションのユーザー リテンション期間分析

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) のリテンション期間機能を使用すると、アプリに戻るユーザーの数、およびそのユーザーが特定のタスクを実行したり目標を達成したりする頻度を分析できます。 たとえば、ゲームのサイトを運営する場合は、ゲームに負けた後にサイトに戻るユーザーの数と、勝った後に戻るユーザーの数を比較できます。 このデータによって、ユーザー エクスペリエンスとビジネス戦略の両方の強化に役立ちます。

## <a name="get-started"></a>作業開始

Application Insights ポータルのリテンション期間ツールにデータがまだ表示されていない場合は、[利用状況ツールの使用方法を確認](usage-overview.md)してください。

## <a name="the-retention-tool"></a>リテンション期間ツール

![リテンション期間ツール](./media/usage-retention/retention.png)

1. 新しいリテンション期間レポートの作成、既にあるリテンション期間レポートの表示、現在のリテンション期間レポートの保存 (または名前を付けて保存)、保存したレポートへの変更の取り消し、レポート データの更新、メールや直接リンクを介したレポートの共有、ドキュメント ページへのアクセスなどの操作は、ツール バーから行うことができます。 
2. 既定では、特定の期間に何かを実行して再び戻ってきた後、さらに何か他のことを実行したすべてのユーザーが表示されます。 各種のイベントを組み合わせることで、特定のユーザーのアクティビティを絞り込むことができます。
3. プロパティに対して 1 つ以上のフィルターを選択します。 たとえば、特定の国またはリージョンのユーザーに注目することができます。 フィルターの設定が完了したら、**[更新]** をクリックします。 
4. "保有期間全体" のグラフには、選択された期間におけるユーザーのリテンションのサマリーが表示されます。 
5. このグリッドには、2 のクエリ ビルダーに従って維持されたユーザー数が表示されます。 各行は、表示された期間に選択したいずれかのイベントを実行したユーザーのコーホートを表します。 行の各セルは、そのコーホートの中で、その後の期間に 1 回以上戻ったユーザーの数を示しています。 複数の期間にユーザーが戻る可能性があります。 
6. リテンション期間レポートを把握しやすいよう、開始イベントの上位 5 件と返されたイベントの上位 5 件が洞察カードに表示されます。 

![リテンション期間のマウス ホバー](./media/usage-retention/hover.png)

リテンション期間ツールのセルにポインタを合わせると、分析ボタンと、セルの意味を説明するヒントが表示されます。 分析ボタンを使用すると、そのセルからユーザーを生成するようにあらかじめ設定されている分析ツールに移動します。 

## <a name="use-business-events-to-track-retention"></a>ビジネス イベントを使用してリテンション期間を追跡する

最も役立つリテンション期間分析を行うには、重要なビジネス アクティビティを表すイベントを測定します。 

たとえば、多くのユーザーは、アプリでページを開いても、そこに表示されるゲームをプレイしない可能性があります。 そのため、ページ ビューだけを追跡すると、前回ゲームをプレイし、再度プレイするために戻ったユーザーの正確な推定数を得ることができません。 リピート プレイヤーを明確に把握するには、ユーザーが実際にプレイするときに、アプリからカスタム イベントを送信する必要があります。  

重要なビジネス アクションを表すカスタム イベントをコーディングして、それらをリテンション期間分析に使用することをお勧めします。 ゲームの結果をキャプチャするには、カスタム イベントを Application Insights に送信するためのコード行を記述する必要があります。 Web ページ コードまたは Node.JS で記述した場合のコード行は次のようになります。

```JavaScript
    appinsights.trackEvent("won game");
```

また、ASP.NET サーバー コードでは、次のようになります。

```csharp
   telemetry.TrackEvent("won game");
```

カスタム イベントの記述について詳しくは、[こちら](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)をご覧ください。


## <a name="next-steps"></a>次の手順
- 使用状況を把握できるようにするには、[カスタム イベント](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent)または[ページ ビュー](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)の送信を開始します。
- カスタム イベントまたはページ ビューを既に送信した場合は、使用状況ツールを見て、自分のサービスがユーザーにどのように使用されているかを把握します。
    - [ユーザー、セッション、イベント](usage-segmentation.md)
    - [ファネル](usage-funnels.md)
    - [ユーザー フロー](usage-flows.md)
    - [ブック](../../azure-monitor/app/usage-workbooks.md)
    - [ユーザー コンテキストの追加](usage-send-user-context.md)


