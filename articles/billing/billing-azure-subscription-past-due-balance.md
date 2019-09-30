---
title: Azure からの支払期限を過ぎた未払い額に関する電子メール | Microsoft Docs
description: Azure サブスクリプションに支払期限を過ぎた未払い額がある場合の支払い方法について説明します
services: ''
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.assetid: d0b88c92-fb9d-4d12-931b-c26104ad63e9
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: banders
ms.openlocfilehash: f58969b167b6c0f0a66d46731ad76c1f6e9acc41
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491449"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>支払期限を過ぎた Azure サブスクリプション未払い額の支払い

お客様の支払いが届いていない場合や、お客様の支払いを処理できない場合、メールが届いたり、Azure portal またはアカウント センターにアラートが表示されたりすることがあります。
自分が[アカウント管理者](billing-subscription-transfer.md#whoisaa)である場合は、[Azure portal](https://portal.azure.com) で未払い料金を清算できます。 請求書モードの支払いを利用している場合、請求書の下部に記載されている場所に支払いを送付します。

> [!IMPORTANT]
> * 同じクレジット カードを使用する複数のサブスクリプションがあり、それらがすべて支払期限を過ぎている場合は、未払い残高全体を一度に支払う必要があります。
> * 未払い料金の清算に使用する支払い方法は、失敗した支払い方法を使用していたすべてのサブスクリプションの新しいアクティブな支払い方法になります。

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Azure portal で期限切れの残高を清算する

1. [Azure portal](https://portal.azure.com) にアカウント管理者としてサインインします。
1. "**コスト管理 + 請求**" を検索します。
1. [概要] ページにサブスクリプションの一覧が表示されます。 サブスクリプションの状態が期限切れの場合は、 **[Settle balance]\(残高の清算\)** リンクをクリックします。
    ![[Settle balance]\(残高の清算\) リンクを示すスクリーン ショット](./media/billing-azure-subscription-past-due-balance/settle-balance-entry-point.png)
1. 合計未払い残高には、失敗した支払い方法を使用するすべての Microsoft サービスの未払い料金が反映されます。
1. 残高を支払うための支払い方法を選択します。 この支払い方法は、現在、失敗した支払い方法を使用しているすべてのサブスクリプションのアクティブな支払い方法になります。
    ![支払い方法の選択のリンクを示すスクリーン ショット](./media/billing-azure-subscription-past-due-balance/settle-balance-screen.png)
1. 選択した支払い方法にも Microsoft サービスの未払い料金がある場合は、これが合計未払い残高に反映されます。 それらの未払い料金も支払う必要があります。
1. **[Pay]\(支払う\)** をクリックします。

## <a name="not-getting-billing-email-notifications"></a>支払いに関する電子メール通知が届きません。

アカウント管理者である場合は、[通知に使用されている電子メール アドレスを確認](billing-how-to-change-azure-account-profile.md)します。 定期的にチェックする電子メール アドレスを使用することをお勧めします。 電子メールが正しい場合は、迷惑メール フォルダーを確認します。

## <a name="if-i-forget-to-pay-what-happens"></a>支払いを忘れた場合

サービスが取り消され、リソースは使用できなくなります。 サービス停止の 90 日後に、すべての Azure データが削除されます。 詳しくは、[Microsoft Trust Center の Microsoft によるデータの管理方法](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)に関するページをご覧ください。

支払い処理が完了しているのに、サブスクリプションが無効のままになっている場合は、[Azure サポート](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)までご連絡ください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
