---
title: "Azure の課金内容を確認する | Microsoft Docs"
description: "Azure サブスクリプションの使用状況と課金内容を確認して理解する方法について説明します"
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: tonguyen
ms.translationtype: HT
ms.sourcegitcommit: 07e5e15f4f4c4281a93c8c3267c0225b1d79af45
ms.openlocfilehash: 2bf75dc55d190522597eb8958d378aa1469027b7
ms.contentlocale: ja-jp
ms.lasthandoff: 08/31/2017

---
# <a name="understand-your-bill-for-microsoft-azure"></a>Microsoft Azure の課金内容の確認
Azure の課金内容を確認するには、詳細な毎日の使用状況ファイルおよび Azure ポータルのコスト管理レポートと、請求書を比較します。

請求書の PDF および詳細な毎日の使用状況ファイルの CSV ダウンロードのコピーを取得するには、[Azure 課金の請求書および毎日の使用状況データの取得](billing-download-azure-invoice-daily-usage-date.md)に関する記事をご覧ください。 

請求書および詳細な毎日の使用状況ファイルの詳しい用語と説明については、[Microsoft Azure 請求書の用語の確認](billing-understand-your-invoice.md)に関する記事と「[Microsoft Azure の詳細な利用料金の用語を理解する](billing-understand-your-usage.md)」をご覧ください。 

コスト管理レポートの詳細については、[Azure ポータルのコスト管理](https://docs.microsoft.com/en-us/azure/billing/billing-getting-started)に関する記事をご覧ください。

## <a name="charges"></a>請求書の料金が正しいことを確認する方法
<div style="padding-top: 56.25%; position: relative; width: 100%;">
<iframe style="position: absolute;top: 0;left: 0;right: 0;bottom: 0;" width="100%" height="100%" src="https://www.youtube.com/embed/3YegFD769Pk" frameborder="0" allowfullscreen></iframe>
</div>

請求書上に詳細を知りたい料金がある場合は、次の 2 つの選択肢があります。

### <a name="option-1-review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>オプション 1: 請求書を確認し、使用状況およびコストを使用状況の詳細の CSV ファイルと比較する

使用状況の詳細の CSV ファイルは、請求期間と毎日の使用状況ごとの料金を示します。 使用状況の詳細の CSV ファイルを取得するには、[Azure 課金の請求書および毎日の使用状況データの取得](https://docs.microsoft.com/en-us/azure/billing/billing-download-azure-invoice-daily-usage-date)に関する記事をご覧ください。

使用料金は、メーター レベルで表示されます。 以下の用語は、請求書と使用状況の詳細ファイルの両方で同じ内容を意味します。 たとえば、請求書の請求サイクルは、使用状況の詳細ファイルに示された請求期間と同じです。

 | 請求書 (PDF) | 使用状況の詳細 (CSV)|
 | --- | --- |
|請求サイクル | 請求期間 |
 |名前 |測定カテゴリ |
 |種類 |測定サブカテゴリ |
 |リソース |測定名 |
 |リージョン |測定リージョン |
 |消費量 |消費量 |
 |あり |含まれる量 |
 |課金対象 |超過量 |

請求書の **[使用料金]** セクションには、請求期間中に使用された各メーターの合計値が含まれています。 たとえば、次のスクリーン ショットでは、Azure Scheduler サービスの使用料金が表示されます。

![請求書の使用料金](./media/billing-understand-your-bill/1.png)

使用状況の詳細の CSV の **[明細書]** セクションに同じ料金が表示されます。 "*[消費量]*" と "*[料金]*" は請求書と一致します。

![CSV の使用料金](./media/billing-understand-your-bill/2.png)

この料金の日々の明細を確認するには、CSV の **[毎日の使用状況]** セクションに移動します。 "*[メーター カテゴリ]*" の [Scheduler] をフィルター処理することで、メーターが使用された日とその日の消費量を確認できます。 "*[リソース]*" と "*[リソース グループ]*" の情報も、比較用に一覧表示されます。 "*[消費量]*" の合計値が請求書に表示される値になります。

![CSV の [毎日の使用状況] セクション](./media/billing-understand-your-bill/3.png)

1 日あたりのコストを取得するには、**[明細書]** セクションの "*[消費量]*" を "*[レート]*" で乗算します。

請求書の詳細については、[Azure 請求書の確認](billing-understand-your-invoice.md)に関する記事を参照してください。

CSV の各列については、[Azure の詳細な利用状況の確認](billing-understand-your-invoice.md)に関する記事を参照してください。

### <a name="option-2-review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>オプション 2: 請求書を確認し、Azure ポータルの使用状況およびコストと比較する

Azure Portal で料金を確認することもできます。Azure Portal は、請求書の使用状況と料金の概要を示すコスト管理グラフを提供します。

上記の例から続行するには、[[サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)に移動してサブスクリプションを選択し、**[コスト分析]** を選択します。 そこから、期間を指定して、Azure Scheduler サービスの使用料金を確認できます。

![Azure ポータルでのコスト分析ビュー](./media/billing-understand-your-bill/4.png)

**[コスト履歴]** の毎日のコストの明細を表示するには、行をクリックします。

![Azure ポータルでのコスト履歴ビュー](./media/billing-understand-your-bill/5.png)

詳細については、「[Azure の課金とコスト管理で予想外のコストを防ぐ](billing-getting-started.md#costs)」をご覧ください。

## <a name="external"></a>外部サービスの料金について
外部サービス (Azure Marketplace 注文とも呼ばれます) は、独立系サービス ベンダーによって提供され、別途課金されます。 これらの料金は、Azure の請求書には表示されません。 詳細については、「[Azure 外部サービスの課金について](billing-understand-your-azure-marketplace-charges.md)」を参照してください。

## <a name="payment"></a>支払い方法

支払い方法としてクレジット カードまたはデビット カードを設定している場合、支払いは、請求期間の終了後、10 日以内に自動的に行われます。 クレジット カードの明細には、**MSFT Azure** と表記されます。

[請求書による支払い](billing-how-to-pay-by-invoice.md)を利用している場合は、請求書の下部に記載されている場所に送金します。 詳しくは、 [サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>クレジット カードによる支払い状況を確認するにはどうすればよいですか。

[サポート チケットを作成](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)して、支払い状況の確認に関するサポートを要求してください。 

## <a name="tips-for-cost-management"></a>コスト管理に関するヒント
- [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)や[総保有コスト計算ツール](https://aka.ms/azure-tco-calculator)を使用してコストを推定し、[各サービスの詳細な料金情報](https://azure.microsoft.com/en-us/pricing/)を取得します。
- [課金アラートを設定](billing-set-up-alerts.md)します。
- [Azure ポータルで使用状況とコストを定期的に確認します](billing-getting-started.md#costs)。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

