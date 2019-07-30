---
title: 請求書にセクションを作成してコストを整理する - Azure
description: 請求書セクションによるコストの整理について説明します。
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.topic: conceptual
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: eadaf34dc5bdd93af532362e8f8542de3f17f414
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490774"
---
# <a name="create-sections-on-your-invoice-to-organize-your-costs"></a>請求書にセクションを作成してコストを整理する

部門別、開発環境別、または組織のニーズに基づいてコストを整理するために、請求書にセクションを作成します。 次に、そのセクションに対して課金する Azure サブスクリプションを作成する権限を他のユーザーに付与します。 サブスクリプションのすべての利用料金や購入がそのセクションに対して課金されるようになります。 請求書のセクションの合計料金を Azure portal で表示するか、Azure コスト分析で確認することができます。 詳細については、「[請求書セクションでトランザクションを表示する](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections)」をご覧ください。

この記事は、Microsoft 顧客契約の請求先アカウントに適用されます。 [Microsoft 顧客契約にアクセスできるかどうかを確認してください](#check-access-to-a-microsoft-customer-agreement)。

## <a name="create-an-invoice-section-in-the-azure-portal"></a>Azure portal で請求書セクションを作成する

請求書セクションを作成するには、**課金プロファイルの所有者**または**課金プロファイルの共同作成者**である必要があります。 詳細については、「[課金プロファイルの請求書セクションを管理する](billing-understand-mca-roles.md#manage-invoice-sections-for-billing-profile)」をご覧ください。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. "**コスト管理 + 請求**" を検索します。

   ![Azure portal の検索を表示するスクリーンショット](./media/billing-mca-section-invoice/billing-search-cost-management-billing.png)

3. 左側のペインから **[請求書セクション]** を選びます。 アクセス方法によっては、課金プロファイルまたは請求先アカウントを選択してから、 **[請求書セクション]** を選択することが必要な場合があります。

   ![請求書セクション一覧を示すスクリーン ショット](./media/billing-mca-section-invoice/mca-select-invoice-sections.png)

4. ページの最上部から **[追加]** を選びます。

5. 請求書セクションの名前を入力し、課金プロファイルを選択します。 このセクションはこの課金プロファイルの請求書に表示され、各サブスクリプションの使用量およびそのセクションに割り当てられている購入を反映します。 

   ![請求書セクションの作成ページを示すスクリーン ショット](./media/billing-mca-section-invoice/mca-create-invoice-section.png)

6. **作成** を選択します。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="next-steps"></a>次の手順

- [Microsoft 顧客契約用に追加の Azure サブスクリプションを作成する](billing-mca-create-subscription.md)
- [Azure サブスクリプションを作成する権限を他のユーザーに付与する](billing-mca-create-subscription.md#give-others-permission)
- [その他の請求先アカウント内のユーザーから Azure サブスクリプションの課金所有権を取得する](billing-mca-request-billing-ownership.md)
