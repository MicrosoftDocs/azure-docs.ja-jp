---
title: Azure 外部サービスの課金について | Microsoft Docs
description: Azure 外部サービス (旧称 Marketplace) の課金について説明します。
services: ''
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ae9c2c975bf49725be1858ad02a1c4b90ef58a7f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57871264"
---
# <a name="understand-your-azure-billing-for-external-service-charges"></a>外部サービスの課金に対する Azure での請求について
外部サービスは、Azure Marketplace でサード パーティ ソフトウェア ベンダーによって公開されています。 たとえば、SendGrid は、Azure で購入できる外部サービスですが、Microsoft が公開したものではありません。

新しい外部サービスまたはリソースをプロビジョニングすると、警告が表示されます。

![Marketplace 購入警告](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> 外部サービスは Microsoft 以外の会社によって公開されますが、Microsoft 製品が外部サービスとして分類される場合があります。
> 
> 

## <a name="how-external-services-are-billed"></a>外部サービスの請求方法
- 外部サービスは個別に請求されます。 外部サービスは、Azure サブスクリプションの中で個別の注文として扱われます。 各サービスの請求期間は、サービスの購入時に設定されます。 サービスの請求期間とサービスを購入するために使用したサブスクリプションの請求期間を混同しないでください。 また、請求書は個別に発行され、クレジット カードの決済も個別に行われます。
- 外部サービスの課金モデルはサービスごとに異なります。 一部のサービスは従量課金制で請求され、一部のサービスは月単位で課金されます。 Azure 外部サービスにはクレジット カードが必要であり、請求書による支払いで外部サービスを購入することはできません。
- 外部サービスでは月々の無料クレジットは使用できません。 [無料クレジット](https://azure.microsoft.com/pricing/spending-limits/)付きの Azure サブスクリプションを使用している場合でも、外部サービスの請求に対して無料クレジットを適用することはできません。 外部サービスを購入する際は、クレジット カードをご使用ください。

## <a name="view-external-service-spending-and-history-in-the-azure-portal"></a>Azure Portal で外部サービスの支払い履歴を表示する
サブスクライブ中の外部サービスの一覧を [Azure ポータル](https://portal.azure.com/)に表示できます。 

1. [Azure Portal](https://portal.azure.com/) にアカウント管理者としてサインインします。
2. ハブ メニューで、**[サブスクリプション]** を選択します。
   
    ![ハブ メニューで [サブスクリプション] を選択します](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. **[サブスクリプション]** ブレードで、表示するサブスクリプションを選択し、**[External services (外部サービス)]** を選択します。
   
    ![[課金] ブレードでサブスクリプションを選択](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. 外部サービスについて、公開元の名前、購入したサービス レベル、リソースに付けた名前、および現在の注文状態が注文ごとに表示されます。 過去の課金情報を見るには、外部サービスを選びます。
   
    ![外部サービスを選択](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. ここから、過去の請求金額について、税金を含む内訳を表示できます。
   
    ![外部サービスの請求履歴の表示](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>Enterprise Agreement (EA) のお客様の外部サービスの支払いを表示する
EA のお客様は、EA ポータルで外部サービスの支払いを表示し、レポートをダウンロードすることができます。 最初に、[EA お客様向け Azure Marketplace](https://ea.azure.com/helpdocs/azureMarketplace) をご覧ください。

## <a name="manage-payment-methods-for-external-service-orders"></a>外部サービスに対する支払い方法を管理する
外部サービスに対する支払い方法の更新は[アカウント センター](https://account.windowsazure.com/)で行ってください。

> [!NOTE]
> 職場または学校のアカウントを使ってサブスクリプションを購入しているときに支払い方法を変更する場合は、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
> 
> 

1. [アカウント センター](https://account.windowsazure.com/)にサインインし、[**[marketplace]** タブに移動](https://account.windowsazure.com/Store)します。
   
    ![アカウント センターで [marketplace] を選択](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. 管理する外部サービスを選択します。
   
    ![管理する外部サービスを選択](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. ページの右側にある **[支払い方法の変更]** をクリックします。 このリンクをクリックすると、支払い方法を管理する別のポータルに移動します。
   
    ![Order summary](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. **[情報の編集]** をクリックし、指示に従って支払い情報を更新します。
   
    ![[情報の編集] を選択](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)

## <a name="cancel-an-external-service-order"></a>外部サービスの注文をキャンセルする
外部サービスの注文をキャンセルする場合は、[Azure Portal](https://portal.azure.com) でリソースを削除します。

![リソースを削除](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

