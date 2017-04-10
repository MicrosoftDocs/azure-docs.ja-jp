---
title: "Azure で使用するクレジット カードの変更 | Microsoft Docs"
description: "Azure サブスクリプションの支払いに使用するクレジット カードを変更する方法について説明します"
services: 
documentationcenter: 
author: genlin
manager: narmstr
editor: 
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 09c18ca6a967c2930ddd2b16d37f0bc606712ea1
ms.openlocfilehash: 2a4751351561728024417d50aeefba0e5b14a438


---
# <a name="change-the-credit-card-used-to-pay-for-an-azure-subscription"></a>Azure サブスクリプションの支払いに使用するクレジット カードを変更する

この記事では、Azure アカウントに関連付けられているクレジット カードを追加、変更、または削除する方法と支払方法について説明します。

<a id="addcard"></a>
## <a name="add-a-credit-card-as-a-payment-method"></a>支払方法としてクレジット カードを追加する

クレジット カードをアカウントに追加するには、次の手順を実行します。

1. アカウント管理者として [Azure アカウント センター](https://account.windowsazure.com/Subscriptions) にログオンします。
2. **[詳細および使用量を表示するにはサブスクリプションをクリックします]** で、支払方法を追加するサブスクリプションを選択します。
3. ページの右側にある **[Manage payment methods (支払方法の管理)]**を選択します。
4. **[支払方法の管理]** ページで、[+] を選択してクレジット カードを追加します。
5. クレジット カードの種類を選択し、必要なすべての情報を入力します。
6. **[次へ]** を選択して、**[Manage payment methods (支払方法の管理)]** ページに戻ります。 追加したカードが有効な場合、使用できる支払方法の一覧に追加されます。

> [!NOTE]
>クレジット カードの情報を送信した後にエラー メッセージが表示される場合は、[デビット カードまたはクレジット カードが拒否される、または受け付けられない](billing-credit-card-fails-during-azure-sign-up.md)場合に関する記事を参照してください。
>
>

## <a name="edit-payment-information-for-an-existing-credit-card"></a>既存のクレジット カードの支払い情報を編集する

支払方法を確認し、既存のクレジット カードの詳細を更新するには、次の手順を実行します。

1. アカウント管理者として [Azure アカウント センター](https://account.windowsazure.com/Subscriptions) にログオンします。

   > [!NOTE]
   > アカウント センターへのアクセス権があるのはアカウント管理者だけです。 アカウント管理者の詳細については、「[Azure 管理者ロールを追加または変更する方法](billing-add-change-azure-subscription-administrator.md)」を参照してください。
   >
   >
2. **[詳細および使用量を表示するにはサブスクリプションをクリックします]** で、クレジット カードにリンクされているサブスクリプションを選択します。</br> ![selectsub](./media/billing-how-to-change-credit-card/selectsub.png)
3. ページの右側にある **[Manage payment methods (支払方法の管理)]**を選択します。</br> ![changesub](./media/billing-how-to-change-credit-card/changesub_new.png)

  現在の支払い方法が一覧表示されます。 支払い方法を選択し、必要な情報を確認します。

  **既存のクレジット カードの詳細を更新する方法**

  クレジット カード番号が変更されない更新の場合は、有効期限など、既存のクレジット カードの詳細情報を更新するだけです。 ただし、カードの紛失、盗難、期限切れなどによりクレジット カード番号が変更された場合は、[クレジット カードをアカウントに追加](#addcard)する必要があります。 これは、クレジット カード番号が変更されると、カード上の CVV セキュリティ コードが変わるためです。

4. **[Manage payment methods (支払方法の管理)]** ページで、クレジット カード番号の横の **[編集]** を選択します。</br> ![changesub](./media/billing-how-to-change-credit-card/editcard_new.png)
5. **[編集]** ページで、正しいクレジット カードの種類とカード番号を選択していることを確認します。
6. 必要に応じてカードの詳細を変更し、**[次へ]** を選択して **[支払方法の選択]** ページに移動します。

## <a name="change-the-credit-card-that-you-use-to-pay-an-azure-bill"></a>Azure の請求書の支払いに使用するクレジット カードを変更する

1. アカウント管理者として [Azure アカウント センター](https://account.windowsazure.com/Subscriptions) にログオンします。
2. **[詳細および使用量を表示するにはサブスクリプションをクリックします]**で、支払方法を更新するサブスクリプションを選択します。
3. ページの右側にある **[Manage payment methods (支払方法の管理)]**を選択します。
4. **[支払方法の管理]** ページで、**[代替使用]** をクリックし、使用するクレジット カードを選択します。

## <a name="remove-a-credit-card-from-the-account"></a>クレジット カードをアカウントから削除する
1. アカウント管理者として [Azure アカウント センター](https://account.windowsazure.com/Subscriptions) にログオンします。
2. **[詳細および使用量を表示するにはサブスクリプションをクリックします]**で、支払方法を更新するサブスクリプションを選択します。
3. ページの右側にある **[Manage payment methods (支払方法の管理)]**を選択します。
4. **[Manage payment methods (支払方法の管理)]** ページで、**[削除]** をクリックし、削除するクレジット カードを選択します。

   > [!NOTE]
   > クレジット カードは、他のアクティブな Microsoft サブスクリプションに関連付けられている場合に削除できません。 カードが使用中であるというエラーが発生した場合は、Microsoft のアクティブなサブスクリプションすべてとの関連付けが解除されていることを確認してください。
   >
   >

##  <a name="how-to-make-payments"></a>支払い方法

支払い方法としてクレジット カードやデビット カードを設定している場合、支払いは自動的に行われます。
[支払い方法として請求書](https://azure.microsoft.com/pricing/invoicing/)を利用している場合、請求書の下部に記載されている場所に支払いを送付します。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。



<!--HONumber=Feb17_HO2-->


