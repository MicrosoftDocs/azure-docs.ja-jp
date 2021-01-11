---
title: 個々の Azure 請求書を確認する
description: 請求書とリソースの使用状況を理解し、個々の Azure サブスクリプションの料金を検証する方法について説明します。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: a91f3781b490ee6f724e7d28a6be9a96c853426f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684628"
---
# <a name="tutorial-review-your-individual-azure-bill"></a>チュートリアル:個々の Azure 請求書を確認する

この記事は、Azure 請求書の理解と確認に役立ちます。 請求期間ごとに、通常は電子メールで請求書を受け取ります。 請求書は、Azure の請求を記載したものです。 請求書と同じコスト情報を、Azure portal でも利用できます。 このチュートリアルでは、請求書を、詳細な毎日の使用状況ファイルおよび Azure portal でのコスト分析と比較します。

このチュートリアルは、個々のサブスクリプションを持つ Azure カスタマーだけに適用されます。 一般的な個々のサブスクリプションは、従量課金制の料金であり、Azure Web サイトから直接購入します。

想定外の料金について不明な点がある場合は、「[想定外の料金を分析する](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started#analyze-unexpected-charges)」を参照してください。 または、Azure サブスクリプションを取り消す必要がある場合は、「[Azure サブスクリプションの取り消し](../manage/cancel-azure-subscription.md)」を参照してください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 請求された料金と使用状況ファイルの比較
> * コスト分析での料金と使用状況の比較

## <a name="prerequisites"></a>前提条件

有料の "*Microsoft Online Services プログラム*" 課金アカウントが必要です。 このアカウントは、Azure Web サイトを通じて Azure にサインアップするときに作成されます。 たとえば、[従量課金制の料金のアカウント](https://azure.microsoft.com/offers/ms-azr-0003p/)を持っているか、[Visual Studio サブスクライバー](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)である場合です。

[Azure 無料アカウント](https://azure.microsoft.com/offers/ms-azr-0044p/)の請求書は、月単位のクレジット額を超過した場合にのみ作成されます。

Azure のサブスクリプションを開始してから 30 日以上経過している必要があります。 Azure では請求期間の最後に課金される。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

- Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="compare-invoiced-charges-with-usage-file"></a>請求された料金と使用状況ファイルの比較

<a name="charges"></a>

使用状況とコストを比較するための最初の手順は、請求書と使用状況ファイルをダウンロードすることです。 使用状況の詳細の CSV ファイルは、請求期間と毎日の使用状況ごとの料金を示します。 税金情報は含まれません。 ファイルをダウンロードするには、アカウント管理者であるか、所有者ロールを持っている必要があります。

Azure portal で、検索ボックスに「*サブスクリプション*」と入力し、[[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) をクリックします。

[![[サブスクリプション] に移動する](./media/review-individual-bill/navigate-subscriptions.png)](./media/review-individual-bill/navigate-subscriptions.png#lightbox)

サブスクリプションの一覧で、サブスクリプションをクリックします。

**[課金]** で **[請求書]** をクリックします。

請求書の一覧で、ダウンロードする請求書を探して、ダウンロードの記号をクリックします。 古い請求書を表示するには、期間の変更が必要になる場合があります。 使用状況の詳細ファイルと請求書の生成には、数分かかる場合があります。

![Screenshot that shows billing periods, the download option, and total charges for each billing period](./media/review-individual-bill/download-invoice.png)

[使用量と請求金額をダウンロードする] ウィンドウで、 **[csv のダウンロード]** と **[請求書のダウンロード]** をクリックします。

![請求書と使用状況のダウンロード ページを示すスクリーンショット](./media/review-individual-bill/usageandinvoice.png)

**"利用不可"** と表示される場合、使用状況の詳細または請求書が表示されない理由は、いくつかあります。

- Azure のサブスクリプションを開始してから 30 日以上経過していない。
- 請求期間での使用がない。
- 請求書がまだ生成されていない。 請求期間の終了までお待ちください。
- 請求書を表示するアクセス許可がない。 アカウント管理者でない場合は、古い請求書が表示されないことがあります。 課金情報へのアクセス権の取得に関する詳細については、[ロールを使用した Azure の課金へのアクセス管理](../manage/manage-billing-access.md)に関するページをご覧ください。
- 無料試用版を使用している場合や、毎月のクレジット額がまだ残っているサブスクリプションがある場合は、Microsoft 顧客契約を結んでいない限り、請求書を取得できません。

次に、料金を確認します。 請求書には、税金と使用料の値が表示されます。

![Azure の請求書の例](./media/review-individual-bill/invoice-usage-charge.png)

ダウンロードした CSV 使用状況ファイルを開きます。 ファイルの末尾で、"*コスト*" 列のすべての項目の値を合計します。

![コストが合計された使用状況ファイルの例](./media/review-individual-bill/usage-file-usage-charges.png)

 合計された "*コスト*" の値は、請求書の "*利用料金*" コストと正確に一致する必要があります。

使用料金は、メーター レベルで表示されます。 以下の用語は、請求書と使用状況の詳細ファイルの両方で同じ内容を意味します。 たとえば、請求書の請求サイクルは、使用状況の詳細ファイルに示された請求期間と同じです。

| 請求書 (PDF) | 使用状況の詳細 (CSV)|
| --- | --- |
|Billing cycle | BillingPeriodStartDate BillingPeriodEndDate |
|名前 |測定カテゴリ |
|Type |測定サブカテゴリ |
|リソース |MeterName |
|リージョン |MeterRegion |
|使用量 | Quantity |
|Included |含まれる量 |
|Billable |超過量 |
|料金 | EffectivePrice|
| 値 | コスト |

請求書の **[利用料金]** セクションには、請求期間中に使用された各メーターの合計値 (コスト) が示されます。 たとえば、次の画像では、"*P10 ディスク*" リソースの Azure Storage サービスの利用料金が示されています。

![請求書の使用料金](./media/review-individual-bill/invoice-usage-charges.png)

CSV 使用状況ファイルで、請求書に示されている対応リソースの *MeterName* によるフィルター処理を行います。 次に、列の項目の "*コスト*" の値を合計します。 次の例では、請求書の同じ品目に対応する測定名 (P10 ディスク) に焦点を当てています。

![MeterName の値が合計された使用状況ファイル](./media/review-individual-bill/usage-file-usage-charge-resource.png)

合計された "*コスト*" の値は、請求書で課金されている個々のリソースの "*利用料金*" コストと正確に一致する必要があります。

詳細については、[Azure の請求書の理解](understand-invoice.md)と [Azure の詳細な使用状況の理解](understand-usage.md)に関するページを参照してください。

## <a name="compare-charges-and-usage-in-cost-analysis"></a>コスト分析での料金と使用状況の比較

Azure portal でのコスト分析も、料金の検証に役立ちます。 請求された使用量と料金の概要をすばやく把握するには、Azure portal の [[サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)で、自分のサブスクリプションを選択します。 次に、 **[コスト分析]** をクリックし、ビューの一覧で **[請求書の詳細]** をクリックします。

![[請求書の詳細] の選択を示す例](./media/review-individual-bill/cost-analysis-select-invoice-details.png)

次に、日付範囲の一覧で、請求書の期間を選択します。 請求書番号に対するフィルターを追加し、自分の請求書の番号と一致する InvoiceNumber を選択します。 コスト分析によって、請求された項目のコストの詳細が示されます。

![コスト分析での請求されたコストの詳細を示す例](./media/review-individual-bill/cost-analysis-service-usage-charges.png)

コスト分析で示されるコストは、請求書で課金されている個々のリソースの "*利用料金*" コストと正確に一致する必要があります。

![請求書の使用料金](./media/review-individual-bill/invoice-usage-charges.png)

## <a name="external-services-billed-separately"></a><a name="external"></a>個別に請求される外部サービス

外部サービス (マーケットプレース) 料金は、サードパーティ ソフトウェア ベンダーによって作成されたリソースに対する金額です。 このようなリソースは Azure Marketplace から入手して利用できます。 たとえば、Barracuda Firewall はサードパーティによって提供される Azure Marketplace リソースです。 このファイアウォールと対応する測定に関するすべての料金は、外部サービス料金として表示されます。

外部サービスの料金は個別に請求されます。 これらの料金は、Azure の請求書には表示されません。 詳細については、「[Azure 外部サービスの課金について](understand-azure-marketplace-charges.md)」を参照してください。

### <a name="resources-billed-by-usage-meters"></a>使用量メーターによって請求されるリソース

Azure では、リソース コストに基づいて直接請求されることはありません。 リソースに対する料金は、1 つまたは複数のメーターを利用して計算されます。 メーターは、リソースの有効期間を通してその使用量を追跡する目的で使用されます。 これらの測定が課金の計算に使用されます。

仮想マシンなど、Azure リソースを 1 つ作成すると、1 つまたは複数のメーター インスタンスが作成されます。 メーターは、時間経過に伴ってリソースの使用状況を追跡するために使用されます。 各メーターから送られる使用状況レコードが Azure で利用され、請求金額が計算されます。

たとえば、Azure に作成された 1 つの仮想マシン (VM) に対して、使用状況を追跡するために次の測定が作成される場合があります。

- コンピューティング時間
- IP アドレス時間
- データ転送 (受信)
- データ転送 (送信)
- Standard マネージド ディスク
- Standard マネージド ディスク操作
- Standard IO - ディスク
- Standard IO - ブロック BLOB (読み取り)
- Standard IO - ブロック BLOB (書き込み)
- Standard IO - ブロック BLOB (削除)

VM が作成されると、各メーターが使用状況レコードの出力を開始します。 この使用状況とメーター料金が Azure の測定システムで追跡されます。

請求の計算に使用されたメーターは、前の例と同様、使用状況 CSV ファイルで確認できます。

## <a name="pay-your-bill"></a><a name="payment"></a>請求金額を支払う

支払い方法としてクレジット カードを設定している場合、支払いは、請求期間の終了後、10 日以内に自動的に行われます。 クレジット カードの明細には、**MSFT Azure** と表記されます。

請求されるクレジット カードを変更する方法については、「[Azure 用にクレジット カードを追加、更新、または削除する](../manage/change-credit-card.md)」を参照してください。

[請求書による支払い](../manage/pay-by-invoice.md)を利用している場合は、請求書の下部に記載されている場所に送金します。

支払い状況を確認するには、[サポート チケットを作成](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 請求された料金と使用状況ファイルの比較
> * コスト分析での料金と使用状況の比較

クイックスタートを完了して、コスト分析の使用を開始します。

> [!div class="nextstepaction"]
> [コストの分析を開始する](../costs/quick-acm-cost-analysis.md)
