---
title: Azure からの支払期限を過ぎた未払い額に関する電子メール
description: Azure サブスクリプションに支払期限を過ぎた未払い額がある場合の支払い方法について説明します
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 8c792ae1b6ac43ead15fd4f27d2468c517153354
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684815"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>支払期限を過ぎた Azure サブスクリプション未払い額の支払い

この記事は、クレジット カードを使用してオンラインで Azure にサインアップし、Microsoft Online Services プログラムの課金アカウントを持っているお客様に適用されます。 [課金アカウントの種類を確認する](#check-the-type-of-your-account)方法をご覧ください。 Microsoft 顧客契約の課金アカウントをお持ちの場合は、「[Microsoft Azure の請求書の支払い](../understand/pay-bill.md)」を参照してください。

お客様の支払いが届いていない場合や、お客様の支払いを処理できない場合、サブスクリプションの期限が過ぎていることを通知するメールが届き、アラートが Azure portal に表示されます。 既定の支払い方法がクレジット カードの場合、[アカウント管理者](billing-subscription-transfer.md#whoisaa)は Azure portal で未払い料金を清算できます。 請求書による支払い (小切手、電信送金) を利用する場合は、請求書の下部に記載されている場所に送金してください。

> [!IMPORTANT]
> * 同じクレジット カードを使用する複数のサブスクリプションがあり、それらがすべて支払期限を過ぎている場合は、未払い残高全体を一度に支払う必要があります。
> * 未払い料金の清算に使用するクレジット カードは、失敗した支払い方法を使用していたすべてのサブスクリプションの新しい既定の支払い方法になります。

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Azure portal で期限切れの残高を清算する

1. [Azure portal](https://portal.azure.com) にアカウント管理者としてサインインします。
1. "**コスト管理 + 請求**" を検索します。
1. **[概要]** ページで、期限が過ぎているサブスクリプションを選択します。
1. **[サブスクリプションの概要]** ページで、期限超過を示す赤いバナーをクリックして、残高を決済します。
    > [!NOTE]
    > アカウント管理者でない場合は、残高を決済することはできません。
1. 新しい **[残高の清算]** ページで、 **[支払い方法の選択]** をクリックします。
    ![支払い方法の選択のリンクを示すスクリーン ショット](./media/resolve-past-due-balance/settle-balance-screen.png)

1. 右側の新しいブレードで、クレジット カードをドロップダウンから選択するか、青い **[新しい支払い方法の追加]** リンクをクリックして新しく追加します。 このクレジット カードは、失敗した支払い方法を現在使用しているすべてのサブスクリプションのアクティブな支払い方法になります。
     > [!NOTE]
     > * 合計未払い残高には、失敗した支払い方法を使用するすべての Microsoft サービスの未払い料金が反映されます。
     > * 選択した支払い方法にも Microsoft サービスの未払い料金がある場合は、これが合計未払い残高に反映されます。 それらの未払い料金も支払う必要があります。
1. **[Pay]\(支払う\)** をクリックします。

## <a name="troubleshoot-declined-credit-card"></a>拒否されたクレジット カードのトラブルシューティング

クレジット カードによる支払いが金融機関によって拒否された場合は、金融機関に問い合わせてこの問題を解決してください。 お使いの銀行で、以下を確認してください。
- カードの国際取引が有効になっている。
- カードに、残高を清算するのに十分な信用限度または資金がある。
- カードの定期的な支払いが有効になっている。

## <a name="not-getting-billing-email-notifications"></a>支払いに関する電子メール通知が届きません。

アカウント管理者である場合は、[通知に使用されている電子メール アドレスを確認](change-azure-account-profile.md)します。 定期的にチェックする電子メール アドレスを使用することをお勧めします。 電子メールが正しい場合は、迷惑メール フォルダーを確認します。

## <a name="if-i-forget-to-pay-what-happens"></a>支払いを忘れた場合

サービスが取り消され、リソースは使用できなくなります。 サービス停止の 90 日後に、すべての Azure データが削除されます。 詳しくは、[Microsoft Trust Center の Microsoft によるデータの管理方法](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)に関するページをご覧ください。

支払い処理が完了しているのに、サブスクリプションが無効のままになっている場合は、[Azure サポート](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)までご連絡ください。

## <a name="check-the-type-of-your-account"></a>アカウントの種類を確認する
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
