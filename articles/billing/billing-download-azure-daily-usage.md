---
title: Azure の使用状況と料金の表示とダウンロード
description: Azure の毎日の使用量と料金をダウンロードまたは表示する方法について説明します。
keywords: 使用量の請求,利用料金,使用量のダウンロード,使用量の表示,Azure 請求書, Azure 使用量
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 7d2d7be562eaaa7dd21e63735f5697ffe5a62f8a
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491442"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Azure の使用量と料金の表示とダウンロード

EA のお客様である場合や [Microsoft 顧客契約](#check-your-access-to-a-microsoft-customer-agreement)を結んでいる場合は、[Azure portal](https://portal.azure.com/) で Azure の使用量と料金をダウンロードできます。 その他のサブスクリプションの場合は、[Azure アカウント センター](https://account.azure.com/Subscriptions)にアクセスして使用量をダウンロードします。

Azure 使用状況情報を取得するアクセス許可を持つのは、特定のロール (アカウント管理者やエンタープライズ管理者など) だけです。 課金情報へのアクセス権の取得に関する詳細については、[ロールを使用した Azure の課金へのアクセス管理](billing-manage-access.md)に関するページをご覧ください。

[Microsoft 顧客契約](#check-your-access-to-a-microsoft-customer-agreement)を結んでいる場合、Azure の使用量と料金を表示するには、課金プロファイルの所有者、共同作成者、閲覧者、または請求書管理者である必要があります。 Microsoft 顧客契約の課金ロールの詳細については、「[課金プロファイルのロールとタスク](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)」を参照してください。

## <a name="download-usage-from-the-account-center-csv"></a>アカウント センターから使用状況をダウンロードする (.csv)

1. アカウント管理者として [Azure アカウント センター](https://account.windowsazure.com/subscriptions)にサインインします。

2. 請求書と使用状況を確認するサブスクリプションを選択します。

3. **[請求履歴]** を選択します。

    ![Screenshot that shows billing history option](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. 過去 6 回の請求期間と、現行の未請求期間の明細も確認できます。

    ![Screenshot that shows billing periods, options to download invoice and daily usage, and total charges for each billing period](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. 見積もりを作成した時点での料金の見積もりを表示するには、 **[現在の明細書の表示]** を選択します。 この情報は毎日更新されますが、すべての累計使用量が含まれていない可能性があります。 その月の実際の請求書は、この見積もりとは異なる場合があります。

    ![Screenshot that shows the View Current Statement option](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Screenshot that shows the estimate of current charges](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. CSV ファイル形式で毎日の使用状況データをダウンロードするには、 **[利用状況のダウンロード]** を選択します。 バージョンが 2 つある場合は、バージョン 2 をダウンロードします。

    ![Screenshot that shows the Download Usage option](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Azure アカウント センターにアクセスできるのは、アカウント管理者のみです。 所有者などの他の課金管理者は、[課金 API](billing-usage-rate-card-overview.md) を使用して使用状況の情報を取得できます。

毎日の使用状況の詳細については、「[Microsoft Azure の課金内容の確認](billing-understand-your-bill.md)」をご覧ください。 コスト管理に関するヘルプについては、[Azure の課金とコスト管理で想定外の料金を防ぐ](billing-getting-started.md)方法に関するページを参照してください。

## <a name="download-usage-for-ea-customers"></a>EA のお客様の使用量のダウンロード

EA のお客様として使用量データを表示およびダウンロードするには、料金表示ポリシーが有効になっているエンタープライズ管理者、アカウント所有者、または部門管理者である必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. "*コスト管理 + 請求*" を検索します。

    ![Azure portal の検索を表示するスクリーンショット](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. **[使用量 + 請求金額]** を選択します。
1. ダウンロードしたい月で、 **[ダウンロード]** を選択します。

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Microsoft 顧客契約に関する使用量のダウンロード

Microsoft 顧客契約を結んでいる場合は、自分の請求プロファイルの Azure の使用量と料金をダウンロードできます。 Azure の使用量と料金の CSV ファイルをダウンロードするには、課金プロファイルの所有者、共同作成者、閲覧者、または請求書管理者である必要があります。

### <a name="download-usage-for-billed-charges"></a>課金された料金の使用量のダウンロード

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. "*コスト管理 + 請求*" を検索します。
3. 課金プロファイルを選択します。 アクセス方法によっては、最初に請求先アカウントを選択することが必要な場合があります。
4. **[請求書]** を選択します。
5. 請求書グリッドで、ダウンロードする使用量に対応する請求書の行を探します。
6. 行の末尾にある省略記号 (`...`) をクリックします。

    ![行の末尾にある省略記号を示すスクリーンショット](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. ダウンロードのコンテキスト メニューで [**Azure の利用状況と請求金額]** を選択します。

     ![選択された Azure の使用量と料金のスクリーンショット](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>保留中の料金の使用量のダウンロード

現在の請求期間について、当月の累計使用量をダウンロードすることもできます。 これらの料金はまだ請求されていません。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. "*コスト管理 + 請求*" を検索します。
3. 課金プロファイルを選択します。 お持ちのアクセス権によっては、最初に請求先アカウントを選択することが必要な場合があります。
4. **[概要]** 領域で、月度累計請求金額の下にあるダウンロード リンクを見つけます。
5. **[Azure の利用状況と請求金額]** を選択します。

    ![概要からのダウンロードを示すスクリーンショット](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次の手順

請求書および使用状況の詳細については、以下を参照してください。

- [Microsoft Azure の詳細な使用状況の用語を理解する](billing-understand-your-usage.md)
- [Microsoft Azure の課金内容の確認](billing-understand-your-bill.md)
- [Microsoft Azure の請求書の表示とダウンロード](billing-download-azure-invoice.md)
- [組織の Azure の価格の表示とダウンロード](billing-ea-pricing.md)

Microsoft 顧客契約を結んでいる場合は、次のページを参照してください。

- [Microsoft 顧客契約での Azure の詳細な使用状況の用語を理解する](billing-mca-understand-your-usage.md)
- [Microsoft 顧客契約の請求書での料金を理解する](billing-mca-understand-your-bill.md)
- [Microsoft Azure の請求書の表示とダウンロード](billing-download-azure-invoice.md)
- [Microsoft 顧客契約に関する税務書類の表示とダウンロード](billing-mca-download-tax-document.md)
- [組織の Azure の価格の表示とダウンロード](billing-ea-pricing.md)
