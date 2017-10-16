---
title: "Azure のクレジット カードの変更 | Microsoft Docs"
description: "Azure サブスクリプションの支払いに使用するクレジット カードを変更する方法について説明します"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: genli
ms.openlocfilehash: 03764377b3ea0e17d4a192a7e05bb495ec56f331
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Azure のクレジット カードまたはデビット カードを追加、更新、または削除する

アカウント センターでは、新しいクレジット カードの追加、既存のクレジット カードの更新、または使用しないクレジット カードの削除が可能です。 クレジット カードを変更するには、[アカウント管理者](billing-subscription-transfer.md#whoisaa)になる必要があります。

**請求書による支払いに切り替えるには、どうしたらいいですか?** 「[Azure サブスクリプションの請求書による支払い](billing-how-to-pay-by-invoice.md)」をご覧ください。
 
<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card"></a>クレジット カードまたはデビット カードを追加する

1. アカウント管理者として[アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインします。
1. サブスクリプションを選択します。
1. ページの右側にある **[Manage payment methods (支払方法の管理)]** を選択します。

    ![選択した [支払い方法の管理] オプションのスクリーン ショット。](./media/billing-how-to-change-credit-card/changesub_new.png)
1. "+" を選択して、カードを追加します。

    ![支払い方法の横にある [編集] オプションを示すスクリーン ショット。](./media/billing-how-to-change-credit-card/editcard_new.png)
1. クレジット カードまたはデビット カードの詳細を入力します。
1. [ **保存**] を選択します。 

クレジット カードを追加したあとでエラーが発生する場合は、「[Azure へのサインアップ時にクレジット カード拒否される](billing-credit-card-fails-during-azure-sign-up.md)」を参照してください。

## <a name="update-existing-credit-or-debit-card"></a>既存のクレジット カードまたはデビット カードを更新する

クレジット カードを更新しても、番号が元のままである場合は、有効期限など、既存のクレジット カードの詳細情報を更新します。 カードの紛失、盗難、期限切れなどの理由でクレジット カード番号が変更された場合は、[支払い方法としてクレジット カードを追加する](#addcard)セクションの手順に従います。 CVV を更新する必要はありません。

1. アカウント管理者として [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインします。
1. カードにリンクされているサブスクリプションを選択します。
1. **[支払い方法の管理]** を選択します。
1. 更新するカードの横にある **[編集]** を選択します。
1. クレジット カードまたはデビット カードの詳細を更新します。
1. [ **保存**] を選択します。

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Azure サブスクリプションに別のクレジット カードを使用する

1. アカウント管理者として [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインします。
1. カードにリンクされているサブスクリプションを選択します。
1. ページの右側にある **[Manage payment methods (支払方法の管理)]** を選択します。
1. 使用するカードの横にある **[代替使用]** をクリックします。 これにより、このカードに現在関連付けられている他のサブスクリプションも更新されます。 

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>クレジット カードまたはデビットカードをアカウントから削除する

1. アカウント管理者として [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインします。
1. カードにリンクされているサブスクリプションを選択します。
3. ページの右側にある **[Manage payment methods (支払方法の管理)]** を選択します。
4. 削除するクレジット カードの **[削除]** をクリックします。

クレジット カードが他の有効な Microsoft サブスクリプションに関連付けられている場合は、Azure アカウントから削除できません。 すべての有効な Microsoft のサブスクリプションからクレジット カードを削除して、再試行します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>使用しているサブスクリプションが無効です。 現在、クレジット カードを削除できないのはなぜですか。

お客様のサブスクリプションが無効化または取り消された後、サブスクリプションを完全に削除するまで 90 日間待機します。 サブスクリプションを再アクティブ化する場合に備えて、保有期間中はファイル上に支払い方法を保持しています。 その後、サブスクリプションは完全に削除されます。

90 日間の保有期間が終了する前に、クレジット カードやデビット カードを削除する必要がある場合は、[Azure サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>"ログイン セッションの期限が切れています。 ここをクリックして、ログインし直してください。" という表示が消えないのはなぜですか。

既にログアウトして再ログインしている場合にも、このエラー メッセージが続けて表示される場合は、プライベート ブラウズ セッションを使用して再試行してください。

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>保有するサブスクリプションごとに異なるカードを使用するには、どうしたらいいですか。

残念ながら、お使いのサブスクリプションで既に同じカードを使用している場合は、個々のサブスクリプションで別のカードを使用することはできません。 ただし、新しいサブスクリプションにサインアップするときに、そのサブスクリプションに対して新しい支払い方法を使用するように選択することは可能です。

### <a name="how-do-i-make-payments"></a>支払いの手順を教えてください。

支払い方法としてクレジット カードまたはデビット カードを設定している場合、各請求期間の終了後にカードから自動的に支払われます。 何も行う必要はありません。

[請求書による支払](billing-how-to-pay-by-invoice.md)を行っている場合、請求書の下部に記載されている場所に請求書を送付します。

### <a name="how-do-i-make-a-one-time-payment"></a>一括での支払い方法を教えてください。

残念ながら、Azure では現在、クレジット カードまたはデビット カードの一括支払いはサポートされていません。 

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
