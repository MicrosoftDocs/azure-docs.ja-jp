---
title: 組織の Azure の価格の表示とダウンロード
description: 組織の価格について、価格を表示またはダウンロードしたりコストを見積もったりする方法について説明します。
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: b4905b1aba11a2f8c457d4785897cbe70dd31f76
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88687418"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>組織の Azure の価格の表示とダウンロード

Azure Enterprise Agreement (EA)、Microsoft 顧客契約 (MCA)、Microsoft Partner Agreement (MPA) のいずれかを結んでいる Azure カスタマーは、その価格を Azure portal で表示してダウンロードすることができます。 [課金アカウントの種類を確認する方法をご覧ください](#check-your-billing-account-type)。

## <a name="download-pricing-for-an-enterprise-agreement"></a>マイクロソフト エンタープライズ契約の価格をダウンロードする

組織のエンタープライズ管理者が設定したポリシーに基づきますが、特定の管理者ロールのみが組織の EA 価格情報へのアクセスを提供します。 詳細については、「[Understand Azure Enterprise Agreement administrative roles in Azure](understand-ea-roles.md)」(Azure の Azure Enterprise Agreement 管理者ロールを理解する) を参照してください。

1. エンタープライズ管理者として [Azure portal](https://portal.azure.com/) にサインインします。
1. "*コスト管理 + 請求*" を検索します。

   ![Azure portal の検索を表示するスクリーンショット](./media/ea-pricing/portal-cm-billing-search.png)

1. 課金アカウントの下で、 **[使用量 + 請求金額]** を選択します。

   ![[請求] で使用量と請求金額を確認できることを示すスクリーンショット](./media/ea-pricing/ea-pricing-usage-charges-nav.png)

1. その月の ![[Azure portal の検索を表示するスクリーンショット]](./media/ea-pricing/download-icon.png) を選択し、 **[ダウンロード]** を選択します。

1. **[価格シート]** の下で **[CSV のダウンロード]** を選択します。

   ![価格シートの [CSV のダウンロード] ボタンを示すスクリーンショット](./media/ea-pricing/download-ea-price-sheet.png)

## <a name="download-pricing-for-an-mca-or-mpa-account"></a>MCA または MPA アカウントの価格をダウンロードする

MCA を結んでいる場合、価格を表示してダウンロードするためには、課金プロファイルの所有者、共同作成者、閲覧者、請求書管理者のいずれかである必要があります。 MPA を結んでいる場合、価格を表示してダウンロードするためには、取引先組織の全体管理者ロールおよび管理者エージェント ロールが割り当てられている必要があります。

### <a name="download-price-sheets-for-billed-charges"></a>課金された料金の価格シートのダウンロード

1. [Azure ポータル](https://portal.azure.com)にサインインします。
1. "*コスト管理 + 請求*" を検索します。
1. 課金プロファイルを選択します。 お持ちのアクセス権によっては、最初に請求先アカウントを選択することが必要な場合があります。
1. **[請求書]** を選択します。
1. 請求書グリッドで、ダウンロードする価格シートに対応する請求書の行を探します。
1. 行の末尾にある省略記号 (`...`) をクリックします。
![選択されている省略記号を示すスクリーンショット](./media/ea-pricing/billingprofile-invoicegrid-new.png)

1. 選択した請求書のサービスの価格を表示する場合は、 **[請求書価格シート]** を選択してください。
1. 特定の請求期間のすべての Azure サービスの価格を表示する場合は、 **[Azure 価格シート]** を選択してください。

![コンテキスト メニューと価格シートを示すスクリーンショット](./media/ea-pricing/contextmenu-pricesheet01.png)

### <a name="download-price-sheets-for-the-current-billing-period"></a>現在の請求期間の価格シートのダウンロード

MCA を結んでいる場合、現在の請求期間の価格をダウンロードすることができます。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
1. "*コスト管理 + 請求*" を検索します。
1. 課金プロファイルを選択します。 お持ちのアクセス権によっては、最初に請求先アカウントを選択することが必要な場合があります。
1. **[概要]** 領域で、月度累計請求金額の下にあるダウンロード リンクを見つけます。
1. **[Azure 価格シート]** を選択します。
![概要からのダウンロードを示すスクリーンショット](./media/ea-pricing/open-pricing01.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Azure 料金計算ツールを利用してコストを見積もる

組織の価格と Azure 料金計算ツールを利用してコストを見積もることもできます。

1. [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator)に移動します。
1. 右上にある **[サインイン]** を選択します。
1. **[Programs and Offer]\(プログラムとオファー\)**  >  の **[ライセンス プログラム]** の下で、 **[Enterprise Agreement (EA)]** を選択します。
1. **[Programs and Offer]\(プログラムとオファー\)**  >  の **[選択された契約]** の下で、 **[選択されていません]** を選択します。

    ![価格シートの [CSV のダウンロード] ボタンを示すスクリーンショット](./media/ea-pricing/ea-pricing-calculator-estimate.png)

1. 組織を選択します。
1. **[適用]** を選択します。
1. 製品を検索して見積もりに追加します。
1. 表示された見積価格は、選択した組織に対する価格に基づきます。

## <a name="check-your-billing-account-type"></a>課金アカウントの種類を確認する
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>次のステップ

EA をご使用の場合、次を参照してください。

- [Azure の EA 課金情報へのアクセスの管理](manage-billing-access.md)
- [Microsoft Azure の請求書の表示とダウンロード](../understand/download-azure-invoice.md)
- [Microsoft Azure の利用状況と料金の表示とダウンロード](../understand/download-azure-daily-usage.md)
- [Enterprise Ageement の顧客の課金内容の確認](../understand/review-enterprise-agreement-bill.md)

Microsoft 顧客契約を結んでいる場合は、以下を参照してください。

- [Microsoft 顧客契約の価格シートの用語を理解する](mca-understand-pricesheet.md)
- [Microsoft Azure の請求書の表示とダウンロード](../understand/download-azure-invoice.md)
- [Microsoft Azure の利用状況と料金の表示とダウンロード](../understand/download-azure-daily-usage.md)
- [課金プロファイルの税務書類を表示およびダウンロードする](../understand/mca-download-tax-document.md)
- [課金プロファイルの請求書の料金を理解する](../understand/review-customer-agreement-bill.md)
