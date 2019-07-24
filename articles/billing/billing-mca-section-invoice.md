---
title: 請求書にセクションを作成してコストを整理する - Azure | Microsoft Docs
description: 請求書セクションによるコストの整理について説明します。
services: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.topic: conceptual
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 21d6c1671c57341d785c002f360c05cc5c610657
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551621"
---
# <a name="create-sections-on-your-invoice-to-organize-your-costs"></a>請求書にセクションを作成してコストを整理する

部門別、開発環境別、または組織のニーズに基づいてコストを整理するために、請求書にセクションを作成します。 次に、セクションに対して Azure サブスクリプションを作成する権限を他のユーザーに付与します。 サブスクリプションのすべての利用料金や購入が請求書セクションに反映されます。 請求書のセクションの合計料金を Azure portal で表示するか、Azure コスト分析で確認します。 詳細については、「[請求書セクションでトランザクションを表示する](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections)」をご覧ください。

この記事は、Microsoft 顧客契約の請求先アカウントに適用されます。 [Microsoft 顧客契約にアクセスできるかどうかを確認してください](#check-access-to-a-microsoft-customer-agreement)。

## <a name="create-an-invoice-section-in-the-azure-portal"></a>Azure portal で請求書セクションを作成する

請求書セクションを作成するには、**課金プロファイルの所有者**または**課金プロファイルの共同作成者**である必要があります。 詳細については、「[課金プロファイルの請求書セクションを管理する](billing-understand-mca-roles.md#manage-invoice-sections-for-billing-profile)」をご覧ください。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. **[コストの管理と請求]** で検索します。

   ![Azure portal の検索を表示するスクリーンショット](./media/billing-mca-section-invoice/billing-search-cost-management-billing.png)

3. 左側のペインから **[請求書セクション]** を選びます。 アクセス方法によっては、課金プロファイルまたは請求先アカウントを選択してから、**[請求書セクション]** を選択することが必要な場合があります。

   ![Azure portal の検索を表示するスクリーンショット](./media/billing-mca-section-invoice/billing-mca-list-invoice-sections.png)

4. ページの最上部から **[追加]** を選びます。

5. 請求書セクションの名前を入力します。

   ![請求書セクションの作成ページを示すスクリーン ショット](./media/billing-mca-section-invoice/mca-create-invoice-section.png)

6. 課金プロファイルを選択します。 請求書セクションのすべての利用料金や購入が、課金プロファイルの請求書に反映されます。

7. **作成**を選択します。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>次の手順

- [Microsoft 顧客契約用に追加の Azure サブスクリプションを作成する](billing-mca-create-subscription.md)
- [Azure サブスクリプションを作成する権限を他のユーザーに付与する](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions)
- [その他の請求先アカウント内のユーザーから Azure サブスクリプションの課金所有権を取得する](billing-mca-request-billing-ownership.md)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
