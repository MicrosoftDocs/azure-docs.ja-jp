---
title: Marketplace の分析情報 - Microsoft コマーシャル マーケットプレース、Microsoft AppSource、および Azure Marketplace
description: マーケットプレース Web 分析の概要にアクセスすることにより、Microsoft AppSource と Azure Marketplace での顧客エンゲージメントを測定できます。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/22/2019
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: cc950c4915a57dd949941fbb9802c51a47c9a00d
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607145"
---
# <a name="marketplace-insights-dashboard-in-partner-center"></a>パートナー センターの [Marketplace の分析情報] ダッシュボード

この記事では、パートナー センターの [Marketplace の分析情報] ダッシュボードについて説明します。 このダッシュボードには、マーケットプレース Web 分析の概要が表示されます。これにより公開元は、次のコマーシャル マーケットプレースのオンライン ストアに掲載されている、個々の製品詳細ページの顧客エンゲージメントを測定できます: Microsoft AppSource と Azure Marketplace。

## <a name="marketplace-insights-dashboard"></a>[Marketplace の分析情報] ダッシュボード

パートナー センターの  **[Marketplace の分析情報] ダッシュボード**にアクセスするには、コマーシャル マーケットプレースの **[[分析] タブ](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** を開きます。

次の項目のグラフィカル表示を見ることができます。  

- [Marketplace の分析情報の概要](#marketplace-insights-summary)
- [地域別のページ アクセス](#page-visits-by-geography)  
- [ページ アクセスとユニーク ビジターの傾向](#page-visits-versus-unique-visitors-trend)
- [Call to Action (CTA) と CTA を使用したユニーク ビジター](#call-to-action-versus-unique-visitors-with-ctas)
- [オファー別のページ アクセスと Call to Action](#page-visits-and-calls-to-action-by-offers)
- [Call to Action の割合の傾向](#call-to-action-percentage-trend)
- [参照ドメイン別のページ アクセスと Call to Action](#page-visits-and-calls-to-action-by-referral-domains)
- [Marketplace の分析情報の詳細テーブル](#marketplace-insights-details-table)

Azure Marketplace または AppSource のオファーにユーザーがアクセスしてからパートナー センターで報告されるまでの最大待機時間は 48 時間です。

>[!NOTE]
> 分析の用語の詳細な定義については、「[コマーシャル マーケットプレース分析に関するよく寄せられる質問と用語](./faq-terminology.md)」を参照してください。

### <a name="insights-dashboard-layout"></a>[分析情報] ダッシュボードのレイアウト

さまざまな方法でコマーシャル マーケットプレースのメトリックを表示できます。

- オンライン ストアのタブ
- ページ フィルター
- 日付フィルター

**オンライン ストアのタブ**: AppSource と Azure Marketplace のタブで別々にオファーのメトリックを表示できます。 右側のオファーのドロップダウン リストからオファーを選択すると、選択したオファーのメトリックが視覚的に表示されます。 既定では、すべてのオファーが選択されています。

![パートナー センターの [分析情報] ダッシュボードにあるオファーのドロップダウンリスト](./media/insights-offer-dropdown.png)

**[分析情報] ページのフィルター**: これらのフィルターは、オファー (製品詳細ページ) レベルで適用されます。 表示する条件に対して複数のフィルターを選択できます。 このフィルターは、グラフや詳細を含む [Marketplace の分析情報] セクション全体に適用されます。

![パートナー センターの [分析情報] ダッシュボード ページのフィルター](./media/insights-page-filter.png)

- オファー名は、選択した日付範囲内にページ アクセスがあったオファーに対してのみ一覧表示されます。  
- 既定では、各フィルター オプションに対して [すべて] が選択されています。
- 適用されたフィルターにより、選択された項目の数が示されます。 既定の [すべて] の選択では、適用されたフィルターは表示されません。

![パートナー センターの適用された分析情報フィルター](./media/insights-page-filter-two.png)

**分析情報の日付フィルター**: このフィルターは、[Marketplace の分析情報] セクション全体に適用されます。 フィルターには、事前に決定された日付範囲またはカスタムの日付範囲を含めることができます。

![パートナー センター分析情報の日付フィルター](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>Marketplace の分析情報の概要

Marketplace の分析情報の概要セクションには、選択した日付範囲の**ページ アクセス**、**Call to Action**、**ユニーク ビジター**の数が表示されます。

### <a name="page-visits"></a>ページ アクセス

この数値は、選択した日付範囲について、オファー ページ (製品詳細ページ) の個別のユーザー セッション数を表します。 赤または緑の割合インジケーターは、ページ アクセスの増加率を表します。 傾向グラフは、月単位のページ アクセス数を表します。

### <a name="unique-visitors"></a>Unique visitors (ユニーク ビジター)

この数値は、ページ フィルターで選択されたオファーの、選択した日付範囲における個別のビジター数を表します。 1 つまたは複数の製品詳細ページにアクセスしたビジターは、1 人のユニーク ビジターとしてカウントされます。

### <a name="call-to-action"></a>行動への呼び掛け

この数値は、オファー ページ (製品詳細ページ) で **[Call to Action]** のボタンがクリックが実行された回数を表します。 **Calls to Action** は、ユーザーが **[今すぐ入手 (無料)]** 、 **[無料試用版]** 、 **[問い合わせ]** 、 **[体験版]** の各ボタンを選択したときにカウントされます。

![パートナー センター分析情報の Call to Action の概要](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>地域別のページ アクセス

次のヒートマップは、**ページ アクセス**、**Call to Action**、**顧客の国別のユニーク ビジター**の数を示しています。 ページ アクセス数が多いほど濃いマップの色で表され、ページ アクセス数が少ないほど薄いマップの色で表されています。

![パートナー センター分析情報の地理的分布](./media/insights-geography.png)

ヒートマップには以下の機能が含まれています。

- ヒートマップには、特定の場所における**ページ アクセス**、**Call to Action**、**ユニーク ビジター**の詳細を表示するための補助グリッドがあり、必要に応じて特定の場所をズームできます。  
- **[Countries/regions spread]\(展開している国および地域\)** は、選択した日付範囲内に顧客のページ アクセスがレポートされたすべての国および地域の数です。
- グリッドで国を検索して選択すると、マップ内の場所にズームできます。 マップの **[ホーム]** を選択すると、元のビューに戻ります。

## <a name="page-visits-versus-unique-visitors-trend"></a>ページ アクセスとユニーク ビジターの傾向

以下の縦棒グラフは、月ごとのページ アクセスの数を表しています。これは、Y 軸 (グラフの左側の軸) に表示されます。 傾向線は、次のオンライン ストアで公開されているオファーについて、ユニーク ビジターの月ごとの傾向を表しており、第 2 の Y 軸 (グラフの右側の軸) に表示されます: Azure Marketplace と AppSource。

![パートナー センター分析情報のページ アクセスとユニーク ビジターの傾向](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>Call to Action と CTA を使用したユニーク ビジター

積み上げ縦棒グラフには、月ごとの Call to Action (CTA) が表されており、CTA の種類 (**今すぐ入手する**、**問い合わせ**、**無料試用版**) によって分類され、Y 軸 (ページの左側の軸) にプロットされます。 傾向線は、CTA を使用したユニーク ビジターの月ごとの傾向を表しており、Azure Marketplace と AppSource で公開したオファーについて、第 2 の Y 軸 (グラフの右側の軸) に表示されます。

![パートナー センター分析情報の Call to Action と CTA を使用したユニーク ビジター](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>オファー別のページ アクセスと Call to Action

外側の円グラフは、マーケットプレースで公開済みであり、フィルターで選択したオファーに基づいて、**ページ アクセス**の内訳を表します。 内側のグラフは、同じオファーについての **Call to Action** の内訳を表します。

![パートナー センター分析情報のオファー別のページ アクセスと Call to Action](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>Call to Action の割合の傾向

**[Call to Action の割合の傾向]** には、マーケットプレースに公開したオファーの CTA の割合が表示されます。 CTA % = (CTA/ページ アクセス) * 100 です。

![パートナー センター分析情報の [Call to Action の割合の傾向]](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>参照ドメイン別のページ アクセスと Call to Action

以下のグラフは、上位 50 の参照ドメインを示しています。 特定の参照ドメインを選択すると、右側のグラフに、ページ アクセスと Call to Action の月ごとの傾向が表示されます。

![パートナー センター分析情報の参照ドメインおよびキャンペーン別のページ アクセスと Call to Action](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>Marketplace の分析情報の詳細テーブル

このテーブルには、選択したオファーのページ アクセスと Call to Action の一覧が日付順に表示されます。

![パートナー センター分析情報の詳細テーブル](./media/insights-details-page.png)

- レコード数が 1000 未満の場合、CSV ファイルにデータを抽出できます。
- レコード数が 1000 を超える場合、エクスポートされたデータは今後 30 日間、ダウンロード ページに非同期で配置されます。
- オファー名およびキャンペーン名でデータをフィルター処理して、関心のあるデータを表示します。

## <a name="next-steps"></a>次のステップ

- パートナー センターのコマーシャル マーケットプレースで利用可能な分析レポートの概要については、「[パートナー センターでのコマーシャル マーケットプレース向け分析](./analytics.md)」を参照してください。
- オファーのマーケットプレース アクティビティを要約した集計データのグラフ、傾向、値については、「[コマーシャル マーケットプレース分析の概要ダッシュボード](./summary-dashboard.md)」を参照してください。
- グラフィカルでダウンロード可能な形式での注文の詳細については、「[コマーシャル マーケットプレース分析の注文ダッシュボード](./orders-dashboard.md)」を参照してください。
- 仮想マシン (VM) プランの使用量と従量制課金メトリックについては、「[コマーシャル マーケットプレース分析の使用量ダッシュボード](./usage-dashboard.md)」を参照してください。
- 成長傾向など、顧客の詳細については、「[コマーシャル マーケットプレース分析の顧客ダッシュボード](./customer-dashboard.md)」を参照してください。
- 過去 30 日間のダウンロード要求の一覧については、「[コマーシャル マーケットプレース分析のダウンロード ダッシュボード](./downloads-dashboard.md)」を参照してください。
- Azure Marketplace と AppSource でのオファーに関する顧客からのフィードバックを統合して表示する方法については、「[コマーシャル マーケットプレース分析の [評価とレビュー] ダッシュボード](./ratings-reviews.md)」を参照してください。
- コマーシャル マーケットプレース分析についてよく寄せられる質問と、データ用語の包括的な辞書については、「[コマーシャル マーケットプレース分析に関するよく寄せられる質問と用語](./faq-terminology.md)」を参照してください。
