---
title: コマーシャル マーケットプレース分析の [Marketplace の分析情報] ダッシュボード
description: パートナー センターのマーケットプレース Web 分析の概要にアクセスすることにより、Microsoft AppSource と Azure Marketplace での顧客エンゲージメントを測定できます。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: a8a53a54d30df9a2f7079d705f754e554328c3f7
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553937"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>コマーシャル マーケットプレース分析の [Marketplace の分析情報] ダッシュボード

この記事では、パートナー センターの [Marketplace の分析情報] ダッシュボードについて説明します。 このダッシュボードには、コマーシャル マーケットプレース Web 分析の概要が表示されます。これにより公開元は、次のコマーシャル マーケットプレースのオンライン ストアに掲載されている、個々の製品詳細ページの顧客エンゲージメントを測定できます: Microsoft AppSource と Azure Marketplace。

パートナー センターの **[Marketplace の分析情報]** ダッシュボードにアクセスするには、コマーシャル マーケットプレースで **[[分析]](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **[Marketplace の分析情報]** を選択します。

分析の用語の詳細な定義については、「[コマーシャル マーケットプレース分析の用語とよく寄せられる質問](./analytics-faq.md)」を参照してください。

## <a name="marketplace-insights-dashboard"></a>[Marketplace の分析情報] ダッシュボード

[Marketplace の分析情報] ダッシュボードには、Azure Marketplace および AppSource のオファーのビジネス パフォーマンスの概要が示されます。 このダッシュボードは、次のことについて大まかに説明しています。

- ページ アクセス数の傾向
- Call to Action の傾向
- オファー、参照ドメイン、キャンペーン ID に対するページ アクセス数および Call to Action
- 地域ごとの Marketplace の分析情報
- Marketplace の分析情報の詳細テーブル

Marketplace の分析情報ダッシュボードにはクリックストリーム データ データがありますが、これをリードのターゲット エンドポイントで生成されたリードに相関付けることはできません。

> [!NOTE]
> Azure Marketplace または AppSource のオファーにユーザーがアクセスしてからパートナー センターで報告されるまでの最大待機時間は 48 時間です。

## <a name="elements-of-the-marketplace-insights-dashboard"></a>[Marketplace の分析情報] ダッシュボードの要素

[Marketplace の分析情報] ダッシュボードでは、2 つのタブに Azure Marketplace および AppSource の Web テレメトリの詳細が表示されます。 次のセクションでは、[Marketplace の分析情報] ダッシュボードの使用方法とデータの読み取り方法について説明します。

### <a name="month-range"></a>月範囲

各ページの右上隅には、月範囲の選択が表示されます。 過去 6 か月または 12 か月の月範囲を選択することで、あるいは 12 か月を最大期間とするカスタムの月範囲を選択することで、 **[Marketplace の分析情報]** ページのグラフの出力をカスタマイズします。 既定の月範囲 (計算期間) は 6 か月です。

:::image type="content" source="./media/insights-dashboard/month-filters.png" alt-text="[Marketplace の分析情報] ダッシュボードの月フィルターの画像。":::

> [!NOTE]
> 視覚化ウィジェットおよびエクスポート レポートのすべてのメトリックでは、ユーザーが選択した計算期間が優先されます。

### <a name="page-visits-trends"></a>ページ アクセス数の傾向

[Marketplace の分析情報] の **[ビジター]** グラフには、選択した計算期間の _ページ アクセス_ および _ユニーク ビジター_ の数が表示されます。

**ページ アクセス**: この数値は、選択した計算期間について、オファーのリスト ページ (製品詳細ページ) の個別のユーザー セッション数を表します。 赤と緑の割合インジケーターは、ページ アクセスの増加率を表します。 傾向グラフは、月単位のページ アクセス数を表します。

**ユニーク ビジター**: この数値は、Azure Marketplace および AppSource のオファーの、選択した計算期間における個別のビジター数を表します。 1 つまたは複数の製品詳細ページにアクセスしたビジターは、1 人のユニーク ビジターとしてカウントされます。

[![[Marketplace の分析情報] ダッシュボードのビジター グラフの画像。](./media/insights-dashboard/visitors.png)](./media/insights-dashboard/visitors.png#lightbox)

### <a name="call-to-actions-trend"></a>Call to Action の傾向

この数値は、オファーのリスト ページ (製品詳細ページ) で **[Call to Action]** のボタンがクリックが実行された回数を表します。 _Calls to Action_ は、ユーザーが **[今すぐ入手 (無料)]** 、 **[無料試用版]** 、 **[問い合わせ]** 、 **[体験版]** の各ボタンを選択したときにカウントされます。

[![[Marketplace の分析情報] ダッシュボードの Call to Action グラフの画像。](./media/insights-dashboard/call-to-actions-trend.png)](./media/insights-dashboard/call-to-actions-trend.png#lightbox)

### <a name="page-visits-and-call-to-actions-against-offers-referral-domains-and-campaign-ids"></a>オファー、参照ドメイン、キャンペーン ID に対するページ アクセス数および Call to Action

**参照ドメイン**: 特定の参照ドメインを選択すると、右側のグラフに、ページ アクセスと Call to Action の月ごとの傾向が表示されます。

:::image type="content" source="./media/insights-dashboard/referral-domain.png" alt-text="[Marketplace の分析情報] ダッシュボードの参照ドメイン グラフの画像。":::

**Offers**:特定のオファーを選択すると、右側のグラフに、ページ アクセスと Call to Action の月ごとの傾向が表示されます。

:::image type="content" source="./media/insights-dashboard/offer-alias.png" alt-text="[Marketplace の分析情報] ダッシュボードのオファーの別名グラフの画像。":::

**キャンペーン ID**: 特定のキャンペーン ID を選択すると、キャンペーンの成功を理解できるようになります。 右側のグラフに、各キャンペーンのページ アクセスと Call to Action の月ごとの傾向が表示されるはずです。

:::image type="content" source="./media/insights-dashboard/campaign.png" alt-text="[Marketplace の分析情報] ダッシュボードのキャンペーン グラフの画像。":::

### <a name="marketplace-insights-by-geography"></a>地域ごとの Marketplace の分析情報

ヒートマップには、選択した計算期間のページ アクセス、ユニーク ビジター、および Call to Action (CTA) の数が表示されます。 マップ上の明るい色はユニーク ビジターが少ないことを表し、暗い色はユニーク ビジターが多いことを表します。 テーブル内のレコードを選択すると、1 つの国や地域が拡大されます。

:::image type="content" source="./media/insights-dashboard/geographical-spread.png" alt-text="[Marketplace の分析情報] ダッシュボードの地理的分布グラフの画像。":::

次のことを考慮してください。

- マップを動かして正確な場所を見ることができます。
- 特定の場所にズーム インできます。
- ヒートマップには、特定の場所での顧客数、注文数、正規化された使用量の詳細を表示するための補助グリッドがあります。
- グリッドで国またはリージョンを検索して選択すると、マップ内の場所にズームできます。 地図内の **[ホーム]** ボタンを選択すると、元のビューに戻ります。

### <a name="marketplace-insights-details-table"></a>Marketplace の分析情報の詳細テーブル

このテーブルには、選択したオファーのページのページ アクセスと Call to Action の一覧が日付順に表示されます。

- レコード数が 1,000 未満の場合、.TSV または .CSV ファイルにデータを抽出できます。
- レコード数が 1,000 を超える場合、エクスポートされたデータは今後 30 日間、ダウンロード ページに非同期で配置されます。
- オファー名およびキャンペーン名でデータをフィルター処理して、関心のあるデータを表示します。

> [!TIP]
> ウィジェットの右上隅にあるダウンロード アイコンを使用して、データをダウンロードできます。 "サムズアップ" アイコンまたは "サムズダウン" アイコンをクリックすることで、各ウィジェットに関するフィードバックを提供できます。

| 列名<br>ユーザー インターフェイス | 属性名 | 定義 | プログラムによるアクセス<br>レポートの列名 |
| ------------ | ------------- | ------------- | ------------- |
| Date | アクセスした日 | Azure Marketplace や AppSource のプランのページにアクセスした日付、またはそのページで CTA クリック イベントが生成された日付。 | Date |
| プラン名 | プラン名 | コマーシャル マーケットプレース オファリングの名前。 | OfferName |
| 参照ドメイン | 参照ドメイン | ページ アクセスが発生した参照ドメインの名前。 ページ アクセス用にキャプチャされた参照ドメインがない場合、対応するエントリは "参照ドメインが存在しません" です。 |  ReferralDomain |
| 国名 | 国名 | ページ アクセスが発生した国の名前。 | CountryName |
| ページ アクセス | ページ アクセス | 特定の日付のプラン名に関連付けられているページ アクセスの数。 | PageVisits |
| 今すぐ入手する | 今すぐ入手する | 特定の日付について、プランのページの "今すぐ入手する" CTA のクリック回数。 | GetItNow |
| お問い合わせ | お問い合わせ | 特定の日付について、プランのページの "お問い合わせ" CTA のクリック回数。 | ContactMe |
| 体験版 | 体験版 | 特定の日付について、プランのページの "体験版" CTA のクリック回数。 | TestDrive |
| 無料試用版 | 無料試用版 | 特定の日付について、プランのページの "無料試用版" CTA のクリック回数。 | FreeTrial |
|||||

## <a name="next-steps"></a>次のステップ

- コマーシャル マーケットプレースで利用可能な分析レポートの概要については、「[パートナー センターでのコマーシャル マーケットプレース向け分析レポートにアクセスする](./partner-center-portal/analytics.md)」を参照してください。
- グラフィカルでダウンロード可能な形式での注文の詳細については、「[コマーシャル マーケットプレース分析の注文ダッシュボード](./orders-dashboard.md)」を参照してください。
- 仮想マシン (VM) プランの使用量と従量制課金メトリックについては、「[コマーシャル マーケットプレース分析の使用量ダッシュボード](./usage-dashboard.md)」を参照してください。
- 成長傾向など、顧客の詳細については、「[コマーシャル マーケットプレース分析の顧客ダッシュボード](./customer-dashboard.md)」を参照してください。
- 過去 30 日間のダウンロード要求の一覧については、「[コマーシャル マーケットプレース分析のダウンロード ダッシュボード](./partner-center-portal/downloads-dashboard.md)」を参照してください。
- Azure Marketplace と AppSource でのオファーに関する顧客からのフィードバックを統合して表示する方法については、「[パートナー センターの評価とレビューの分析ダッシュボード](./partner-center-portal/ratings-reviews.md)」を参照してください。
- コマーシャル マーケットプレース分析についてよく寄せられる質問と、データ用語の包括的な辞書については、「[コマーシャル マーケットプレース分析の用語とよく寄せられる質問](./analytics-faq.md)」を参照してください。