---
title: Microsoft 顧客契約の請求書の料金を理解する - Azure
description: 請求書の料金を読み取り、理解する方法について説明します。
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: ee250589133abb1944ff17e39dc650cbae4279c6
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490671"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>Microsoft 顧客契約の請求書の料金を理解する

個々のトランザクションを分析することで、請求書の料金を理解できます。 Microsoft 顧客契約の請求先アカウントでは、課金プロファイルごとに毎月請求書が生成されます。 請求書には、前月からのすべての料金が含まれます。 Azure portal で請求書を表示できます。 詳細については、[Microsoft 顧客契約の請求書のダウンロード](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement)に関するページを参照してください。

この記事では、Microsoft 顧客契約の請求先アカウントについて説明します。 [Microsoft の顧客契約にアクセスできるかどうかを確認してください](#check-access-to-a-microsoft-customer-agreement)。

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Azure portal で請求書のトランザクションを表示する

1. [Azure Portal](https://www.azure.com) にサインインします。

2. "**コストの管理と請求**" を検索します。

    ![Azure portal でのコストの管理と請求の検索を示すスクリーンショット](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. ページの左側から **[すべてのトランザクション]** を選択します。 アクセス方法によっては、請求先アカウント、課金プロファイル、または請求書セクションを選択してから、 **[すべてのトランザクション]** を選択することが必要な場合があります。

4. [すべてのトランザクション] ページには次の情報が表示されます。

    ![課金対象トランザクションの一覧を示したスクリーンショット](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |列  |定義  |
    |---------|---------|
    |Date     | トランザクションの日付  |
    |請求書 ID     | トランザクションが課金される請求書の識別子。 サポート要求を送信する場合、ID を Azure サポートに知らせると、サポート要求が迅速に処理されます |
    |トランザクションの種類     |  購入、キャンセル、利用料金などのトランザクションの種類  |
    |製品ファミリ     | 仮想マシン用のコンピューターや Azure SQL データベース用のデータベースなどの製品のカテゴリ|
    |製品 SKU     | お使いの製品のインスタンスを識別する一意のコード |
    |金額     |  トランザクションの金額      |
    |請求書セクション     | トランザクションは、課金プロファイルの請求書のこのセクションに表示されます |
    |請求プロファイル     | トランザクションは、この課金プロファイルの請求書に表示されます |

5. 請求書 ID で検索して、請求書のトランザクションをフィルター処理します。

### <a name="view-transactions-by-invoice-sections"></a>請求書セクションでトランザクションを表示する

請求書セクションは、課金プロファイルの請求書のコストを整理するのに役立ちます。 詳細については、[請求書セクションの理解](billing-mca-overview.md#invoice-sections)に関するページを参照してください。 請求書が生成されると、課金プロファイル内のすべてのセクションの料金が請求書に表示されます。

次の画像には、請求書サンプルでの会計部門の請求書セクションの料金が示されています。

![請求書セクション情報別に詳細を表示した画像例](./media/billing-understand-your-bill-mca/invoicesection-details.png)

請求書セクションの料金を確認したら、Azure portal でトランザクションを表示して料金を把握することができます。

1. Azure portal の [すべてのトランザクション] ページに移動して、請求書のトランザクションを表示します。 詳細については、「[Azure portal で請求書のトランザクションを表示する](#view-transactions-for-an-invoice-in-the-azure-portal)」を参照してください。

2. トランザクションを表示する請求書セクション名でフィルター処理します。

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>保留中の料金を確認して次の請求書を見積もる

Microsoft 顧客契約の請求先アカウントでは、請求が行われるまで、料金は見積もられたもので保留中であると見なされます。 Azure portal で保留中の料金を確認して次の請求書を見積もることができます。 保留中の料金は見積もられたもので、税は含まれていません。 次の請求書の実際の料金は、保留中の料金とは異なるものになります。

### <a name="view-summary-of-pending-charges"></a>保留中の料金の概要を表示する

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. "**コストの管理と請求**" を検索します。

   ![Azure portal でのコストの管理と請求の検索を示すスクリーンショット](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 課金プロファイルを選択します。 アクセス方法によっては、請求先アカウントを選択することが必要な場合があります。 請求先アカウントから **[課金プロファイル]** を選択し、課金プロファイルを選択します。

4. 画面上部の **[概要]** タブを選択します。

5. 料金セクションでは、月度累計と先月の請求金額が表示されます。

   ![Azure portal でのコストの管理と請求の検索を示すスクリーンショット](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

月度累計請求金額は、当月の保留中の料金であり、当月の請求書が作成されるときに請求されます。 先月の請求書がまた生成されていない場合、先月の料金も保留中であり、次の請求書に表示されます。

### <a name="view-pending-transactions"></a>保留中のトランザクションを表示する

保留中の料金を確認するときに、その料金を発生させた個々のトランザクションを分析することで、料金のことを理解できます。 この時点では、[すべてのトランザクション] ページに保留中の利用料金は表示されません。 [Azure サブスクリプション] ページで、保留中の利用料金を確認できます。 詳細については、「[保留中の利用料金を表示する](#view-pending-usage-charges)」を参照してください

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. "**コストの管理と請求**" を検索します。

   ![Azure portal でのコストの管理と請求の検索を示すスクリーンショット](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 課金プロファイルを選択します。 アクセス方法によっては、請求先アカウントを選択することが必要な場合があります。 請求先アカウントから **[課金プロファイル]** を選択し、課金プロファイルを選択します。

4. ページの左側から **[すべてのトランザクション]** を選択します。

5. *保留中*を検索します。 **Timespan** フィルターを使用して、当月または先月の保留中の料金を表示します。

   ![保留中トランザクションの一覧を示したスクリーンショット](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>保留中の利用料金を表示する

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. "*コストの管理と請求*" を検索します。

   ![Azure portal でのコストの管理と請求の検索を示すスクリーンショット](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 課金プロファイルを選択します。 アクセス方法によっては、請求先アカウントを選択することが必要な場合があります。 請求先アカウントから **[課金プロファイル]** を選択し、課金プロファイルを選択します。

4. ページの左側の **[すべてのサブスクリプション]** を選択します。

5. [Azure サブスクリプション] ページに、課金プロファイルのサブスクリプションごとに当月および先月の料金が表示されます。 月度累計請求金額は、当月の保留中の料金であり、当月の請求書が作成されるときに請求されます。 先月の請求書がまた生成されていない場合、先月の料金も保留中です。

    ![課金プロファイルの Azure サブスクリプションの一覧を表示したスクリーンショット](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Azure 利用料金を分析する

Azure の使用状況と料金の CSV ファイルを使用して、使用状況ベースの料金を分析します。 請求書または保留中料金のファイルをダウンロードできます。 詳細については、[Azure の請求書と毎日の使用状況データの取得](billing-download-azure-invoice-daily-usage-date.md)に関するページを参照してください。

### <a name="view-detailed-usage-by-invoice-section"></a>請求書セクションで詳細な使用状況を表示する

Azure の使用状況および料金のファイルをフィルター処理して、請求書セクションの利用料金を調整できます。

次の手順では、会計部門の請求書セクションのコンピューティング料金の調整について説明します。

![請求書セクション情報別に詳細を表示した画像例](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | 請求書 PDF | Azure の使用状況および料金の CSV |
 | --- | --- |
 |会計部門 |invoiceSectionName |
 |利用料金 - Microsoft Azure プラン |productOrderName |
 |Compute |serviceFamily |

1. CSV ファイル内の **invoiceSectionName** 列を **会計部門**にフィルター処理します。
2. CSV ファイル内の **productOrderName** 列を **Microsoft Azure プラン**にフィルター処理します。
3. CSV ファイル内の **serviceFamily** 列を **Microsoft.Compute** にフィルター処理します。

![請求書セクションでフィルター処理された使用状況および料金を示したスクリーンショット](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>サブスクリプション別に詳細な使用状況を表示する

Azure の使用状況および料金の CSV ファイルをフィルター処理して、サブスクリプションの利用料金を照合確認できます。 課金プロファイルのすべてのサブスクリプションを表示するには、「[保留中の利用料金を表示する](#view-pending-usage-charges)」を参照してください。

特定のサブスクリプションの料金を確認するときには、Azure の使用状況と料金の csv ファイルを使用して料金を分析します。

次の画像には、Azure portal でのサブスクリプションの一覧が表示されています。

![課金プロファイルの Azure サブスクリプションの一覧を表示したスクリーンショット](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

Azure の使用状況および料金の CSV ファイル内の **subscriptionName** 列を **WA_Subscription** にフィルター処理して、WA_Subscription の詳細な利用料金を表示します。

![サブスクリプションでフィルター処理された使用状況および料金を示したスクリーンショット](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>請求金額を支払う

請求金額を支払う指示が、請求書の底部に表示されます。 [支払方法を確認してください](billing-mca-understand-your-invoice.md#how-to-pay)。

請求金額を既に支払っている場合は、Azure portal の [請求書] ページで支払いの状態を確認できます。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次の手順

請求書および詳細な使用状況の詳細については、以下を参照してください。

- [Azure の請求書と毎日の使用状況データを取得する方法](billing-download-azure-invoice-daily-usage-date.md)
- [Microsoft 顧客契約の請求書の用語を理解する](billing-mca-understand-your-invoice.md)
- [Microsoft 顧客契約の使用状況 CSV の用語を理解する](billing-mca-understand-your-usage.md)
