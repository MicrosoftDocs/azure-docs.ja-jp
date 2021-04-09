---
title: Azure Monitor のブックベースの分析情報のトラブルシューティング
description: Azure Key Vault、Azure CosmosDB、Azure Storage、Azure Cache for Redis などのサービスに対する Azure Monitor のブックベースの分析情報のトラブルシューティング指針を示します。
services: azure-monitor
ms.author: lagayhar
author: lgayhardt
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: a823b5e2ae31e62ff6d876f0b3468953c0ed9c35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587158"
---
# <a name="troubleshooting-workbook-based-insights"></a>ブックベースの分析情報のトラブルシューティング

この記事は、Azure Monitor のブックベースの分析情報を使用するときに発生する可能性があるいくつかの一般的な問題を診断し、トラブルシューティングするのに役立ちます。


## <a name="why-can-i-only-see-200-resources"></a>200 個のリソースしか表示できないのはなぜですか

選択されているサブスクリプションの数には関係なく、選択されるリソースの数には 200 の制限があります。

## <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>ダッシュボードの最近ピン留めされたタイルをクリックするとどうなりますか

* タイル上の任意の場所をクリックすると、そのタイルがピン留めされたときのタブが表示されます。 たとえば、[概要] タブでグラフをピン留めした場合は、ダッシュボードのそのタイルをクリックすると、その既定のビューが開きます。ただし、独自の保存されたコピーからグラフをピン留めした場合は、その保存されたコピーのビューが開きます。
* タイトルの左上にあるフィルター アイコンをクリックすると、[タイル設定の構成] タブが開きます。
* 右上にある省略記号アイコンをクリックすると、[Customize title data] (タイトル データのカスタマイズ) のオプションである [カスタマイズ]、[更新]、および [ダッシュボードから削除する] が表示されます。

## <a name="what-happens-when-i-save-a-workbook"></a>ブックを保存するとどうなりますか

* ブックを保存すると、編集を使用してブックの新しいコピーを作成したり、タイトルを変更したりできるようになります。 保存してもブックは上書きされず、現在のブックは常に既定のビューになります。
* **保存されていない** ブックは、単なる既定のビューです。

## <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>ポータルに自分のすべてのサブスクリプションが表示されないのはなぜですか

ポータルには、ポータルの起動時に選択されたサブスクリプションのデータのみが表示されます。 選択されているサブスクリプションを変更するには、右上に移動し、フィルター アイコンを含むノートブックをクリックします。 このオプションにより、 **[ディレクトリ + サブスクリプション]** タブが表示されます。

![[ディレクトリ + サブスクリプション]](./media/storage-insights-overview/fqa3.png)

## <a name="what-is-time-range"></a>時間範囲とはどのようなものですか

時間範囲は、特定の期間のデータを示します。 たとえば、時間範囲が 24 時間である場合は、過去 24 時間分のデータを示しています。

## <a name="what-is-time-granularity-time-grain"></a>時間の粒度 (時間グレイン) とはどのようなものですか

時間の粒度は、2 つのデータ ポイント間の時間差です。 たとえば、時間グレインが 1 秒に設定されている場合は、メトリックが 1 秒ごとに収集されることを示します。

## <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>ブックのある部分をダッシュボードにピン留めした後の時間の粒度はどれだけですか

既定の時間の粒度は自動に設定されています。現時点で、それを変更することはできません。

## <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>ダッシュボード上のブック ステップの期間/時間範囲を変更するにはどうしたらよいですか

既定では、ダッシュボード タイルの期間/時間範囲は 24 時間に設定されています。これを変更するには、右上にある省略記号ボタンをクリックして **[タイル データをカスタマイズする]** を選択し、[override the dashboard time settings at the title level] (タイトル レベルでダッシュボードの時刻の設定をオーバーライドする) ボックスをオンにしてから、ドロップダウン メニューを使用して期間を選択します。  

![タイルの右の隅にある省略記号ボタンを選択し、[Customize this data] (このデータをカスタマイズする) を選択します。](./media/storage-insights-overview/fqa-data-settings.png)

![[タイル設定の構成] で、期間のドロップダウンを選択して期間/時間範囲を変更します。](./media/storage-insights-overview/fqa-timespan.png)

## <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>ダッシュボードにピン留めしたブックまたはブック ステップのタイトルを変更するにはどうしたらよいですか

ダッシュボードにピン留めされたブックまたはブック ステップのタイトルは、そのブックにあったものと同じ名前を保持します。 タイトルを変更するには、ブックの独自のコピーを保存する必要があります。 その後、[保存] を押す前に、ブックに名前を付けることができます。

![上部にある [保存] を選択してブックのコピーを保存し、その名前を変更します。](./media/storage-insights-overview/fqa-change-workbook-name.png)

保存されたブック内のステップの名前を変更するには、そのステップの下の [編集] を選択した後、[設定] の一番下にある歯車を選択します。

![ブック ステップの下部にある [編集] を選択して [設定] を開く](./media/storage-insights-overview/fqa-edit.png)
![[設定] の一番下にある歯車を選択すると、ステップ名を変更できる](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>次のステップ

ブックがサポートするように設計されているシナリオ、新規レポートの作成方法と既存レポートのカスタマイズ方法などの詳細については、「[Azure Monitor ブックを使用した対話型レポートの作成](../visualize/workbooks-overview.md)」で学習してください。
