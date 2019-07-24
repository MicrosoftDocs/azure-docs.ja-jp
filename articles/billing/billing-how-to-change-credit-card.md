---
title: Azure のクレジット カードの変更 | Microsoft Docs
description: Azure サブスクリプションの支払いに使用するクレジット カードを変更する方法について説明します
services: ''
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/13/2019
ms.author: banders
ms.openlocfilehash: b910cb3061b1451ea80b9843e2aa4047a784548f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57876869"
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Azure のクレジット カードまたはデビット カードを追加、更新、または削除する

Azure portal では、新しいクレジット カードの追加、既存のクレジット カードの更新、または使用しないクレジット カードの削除が可能です。 クレジット カードを変更するには、[アカウント管理者](billing-subscription-transfer.md#whoisaa)になる必要があります。

<!-- If your Angola, Belize, Algeria, Vietnam, or Virgin Islands. -->

**請求書による支払いに切り替えるには、どうしたらいいですか?** 「[Azure サブスクリプションの請求書による支払い](billing-how-to-pay-by-invoice.md)」をご覧ください。

<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card-to-an-azure-subscription"></a>Azure サブスクリプションに新しいクレジット カードまたはデビット カードを追加する

1. [Azure Portal](https://portal.azure.com) にアカウント管理者としてサインインします。
1. **[コストの管理と請求]** で検索します。

    ![検索を示すスクリーンショット](./media/billing-how-to-change-credit-card/search.png)

1. クレジット カードまたはデビット カードを追加するサブスクリプションを選択します。
1. **[支払い方法]** を選択します。

    ![選択した [支払い方法の管理] オプションのスクリーン ショット。](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. 左上隅にある [+] を選択してカードを追加します。 クレジット カードのフォームが右側に表示されます。
1. クレジット カードまたはデビット カードの詳細を入力します。

    ![新しいカードの追加を示すスクリーンショット。](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. このカードをアクティブな支払い方法にするには、フォームの上にある **[これをアクティブな支払い方法にする]** の横のチェック ボックスをオンにします。 選択したサブスクリプションと同じカードを使用しているすべてのサブスクリプションで、このカードがアクティブな支払い方法になります。

1. **[次へ]** を選択します。

クレジット カードを追加したあとでエラーが発生する場合は、「[Azure へのサインアップ時にクレジット カード拒否される](billing-credit-card-fails-during-azure-sign-up.md)」を参照してください。

## <a name="update-existing-credit-or-debit-card"></a>既存のクレジット カードまたはデビット カードを更新する

クレジット カードを更新しても、番号が元のままである場合は、有効期限など、既存のクレジット カードの詳細情報を更新します。 カードの紛失、盗難、期限切れなどの理由でクレジット カード番号が変更された場合は、[支払い方法としてクレジット カードを追加する](#addcard)セクションの手順に従います。 CVV を更新する必要はありません。

1. [Azure Portal](https://portal.azure.com) にアカウント管理者としてサインインします。
1. **[コストの管理と請求]** で検索します。

    ![検索を示すスクリーンショット](./media/billing-how-to-change-credit-card/search.png)

1. **[支払い方法]** を選択します。

    ![選択した [支払い方法の管理] オプションのスクリーン ショット。](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. 編集するデビット カードまたはクレジット カードをクリックします。 クレジット カードのフォームが右側に表示されます。

    ![選択されているクレジット カードまたはデビット カードを示すスクリーンショット。](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. クレジット カードまたはデビット カードの詳細を更新します。
1. **[保存]** を選択します。

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Azure サブスクリプションに別のクレジット カードを使用する

複数のサブスクリプションに同じアクティブな支払い方法が指定されている場合は、これらのうちのいずれかのサブスクリプションのアクティブな支払い方法を変更すると、それ以外のサブスクリプションのアクティブな支払い方法も更新されます。

1. [Azure Portal](https://portal.azure.com) にアカウント管理者としてサインインします。
1. **[コストの管理と請求]** で検索します。

    ![検索を示すスクリーンショット](./media/billing-how-to-change-credit-card/search.png)

1. クレジット カードまたはデビット カードを追加するサブスクリプションを選択します。
1. **[支払い方法]** を選択します。

    ![選択した [支払い方法の管理] オプションのスクリーン ショット。](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. アクティブな支払い方法を作成するカードの横にあるチェック ボックスをオンにします。
1. **[アクティブに設定]** をクリックします。
    ![選択されているクレジット カードまたはデビット カードと [アクティブに設定] を示すスクリーンショット](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>クレジット カードまたはデビットカードをアカウントから削除する

1. [Azure Portal](https://portal.azure.com) にアカウント管理者としてサインインします。
1. ページの左側にある **[コスト管理と請求]** を選択します。

    ![検索を示すスクリーンショット](./media/billing-how-to-change-credit-card/search.png)

1. **[請求]** で、**[支払方法]** を選択します。

    ![選択した [支払い方法の管理] オプションのスクリーン ショット。](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. 削除するカードの横にあるチェック ボックスをオンにします。
1. **[削除]** をクリックします。

クレジット カードがいずれかの Microsoft サブスクリプションのアクティブな支払い方法である場合は、Azure アカウントから削除できません。 このクレジット カードまたはデビット カードにリンクされているすべてのサブスクリプションのアクティブな支払い方法を変更し、再度お試しください。
<!-- # Add, update, or remove a credit or debit card for Azure

In the Account Center, you can add a new credit card, update an existing credit card, or delete a credit card that you don't use. You must be an [Account Administrator](billing-subscription-transfer.md#whoisaa) to make these changes.

**Want to switch to pay by invoice?** See [Pay for Azure subscriptions by invoice](billing-how-to-pay-by-invoice.md).
 
<a id="addcard"></a>

## Add a new credit or debit card

1. Sign in to the [Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select a subscription.
1. On the right side of the page, select **Manage payment methods**.

    ![Screenshot that shows Manage payment methods option selected.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Select “+” to add a card.

    ![Screenshot that shows the edit option next to the payment method.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Enter credit or debit card details.
1. Select **Save**. 

If you get an error after you add the credit card, see [Credit card declined at Azure sign-up](billing-credit-card-fails-during-azure-sign-up.md).

## Update existing credit or debit card

If your credit card gets renewed and the number remains the same, update the existing credit card details like the expiration date. If your credit card number changes because the card is lost, stolen, or expired, follow the steps in the [Add a credit card as a payment method](#addcard) section. You don't need to update the CVV.

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. Select **Manage payment methods**.
1. Select **Edit** next to the card you want to update.
1. Update the credit or debit card details.
1. Select **Save**.

## Use a different credit card for the Azure subscription

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. On the right side of the page, select **Manage payment methods**.
1. Click **Use Instead** next to the card that you want to use. This also updates any other subscriptions currently associated with this card. 

## Remove a credit or debit card from the account

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
3. On the right side of the page, select **Manage payment methods**.
4. Click **Delete** for the credit card that you want to delete.

If your credit card is associated with other active Microsoft subscriptions, you can't remove it from your Azure account. Remove the credit card from all active subscriptions that you have with Microsoft and try again. -->
## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>使用しているサブスクリプションが無効です。 現在、クレジット カードを削除できないのはなぜですか。

お客様のサブスクリプションが無効化または取り消された後、サブスクリプションを完全に削除するまで 90 日間待機します。 サブスクリプションを再アクティブ化する場合に備えて、保有期間中はファイル上に支払い方法を保持しています。 その後、サブスクリプションは永久に削除されます。

90 日間の保有期間が終了する前に、クレジット カードやデビット カードを削除する必要がある場合は、[サブスクリプションを再アクティブ化してください](billing-subscription-become-disable.md)。 再アクティブ化することはできない場合は、[Azure サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>"ログイン セッションの期限が切れています。 ここをクリックして、ログインし直してください。" という表示が消えないのはなぜですか。

既にログアウトして再ログインしている場合にも、このエラー メッセージが続けて表示される場合は、プライベート ブラウズ セッションを使用して再試行してください。

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>保有するサブスクリプションごとに異なるカードを使用するには、どうしたらいいですか。

残念ながら、お使いのサブスクリプションで既に同じカードを使用している場合は、個々のサブスクリプションで別のカードを使用することはできません。 ただし、新しいサブスクリプションにサインアップするときに、そのサブスクリプションに対して新しい支払い方法を使用するように選択することは可能です。

### <a name="how-do-i-make-payments"></a>支払いの手順を教えてください。

支払い方法としてクレジット カードまたはデビット カードを設定している場合、各請求期間の終了後にカードから自動的に支払われます。 何も行う必要はありません。

[請求書による支払](billing-how-to-pay-by-invoice.md)を行っている場合、請求書の下部に記載されている場所に請求書を送付します。

### <a name="how-do-i-make-a-one-time-payment"></a>一括での支払い方法を教えてください。

残念ながら、Azure では現在、クレジット カードまたはデビット カードの一括支払いはサポートされていません。 

### <a name="how-do-i-change-the-tax-id"></a>税 ID を変更する方法を教えてください。

税 ID を追加または更新するには、[Azure アカウント センターの **[プロファイル]**](https://account.azure.com/Profile) にアクセスし、**[納税記録]** を選択してください。 この税 ID は、免税の計算に使用され、請求書に表示されます。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
