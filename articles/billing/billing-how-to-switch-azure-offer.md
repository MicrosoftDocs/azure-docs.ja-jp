---
title: "Azure サブスクリプションのプランの変更 | Microsoft Docs"
description: "サブスクリプション管理ポータルを使用して Azure サブスクリプションを変更し、別のプランに切り替える方法について説明します。"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: aae227b3-6d64-4550-a5b6-d359f53f0a59
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 9c4c872d03a3927d02d17de6cc2aae6e684f1fad
ms.openlocfilehash: b6a1e6175833ea0cdc785b18c88b43e06a7ad57c


---
# <a name="switch-your-azure-subscription-to-another-offer"></a>別のプランへの Azure サブスクリプションの切り替え
[従量課金](https://azure.microsoft.com/offers/ms-azr-0003p/)のお客様は、[アカウント センター](https://account.windowsazure.com/Subscriptions)で Azure サブスクリプションを別のプランに切り替えることができます。 たとえば、この機能を使えば、[Visual Studio サブスクライバー向けの月単位のクレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効活用できます。 [無料試用版](https://azure.microsoft.com/free/)をご利用の場合は、[従量課金制にアップグレードする](billing-upgrade-azure-subscription.md)方法を確認してください。

#### <a name="whats-supported"></a>サポート対象:
| ファイル | To |
| --- | --- |
| [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) |[開発テスト用の従量課金制プラン](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) |[MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [!NOTE]
> その他のプランの変更については、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
> 
> 

## <a name="switch-subscription-offer"></a>サブスクリプション プランの切り替え
> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
> 
> 

1. [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインします。
2. 自分の従量課金のサブスクリプションを選択します。
3. **[別のプランに切り替える]**をクリックします。 このボタンは、従量課金制を利用しており、最初の請求期間が終了している場合にのみクリックできます。
   
   ![ページの右側にあるプランの切り替えボタンに注目](./media/billing-how-to-switch-azure-offer/switchbutton.png)
4. 現在のサブスクリプションから移行可能なプランの一覧から、**目的のプランを選択します**。 この一覧の内容はアカウントが関連付けられているメンバーシップに基づいて決まります。 何も表示されない場合は、[移行可能なプランの一覧](#whats-supported)をチェックして、適切なメンバーシップを持っていることを確認してください。 
   
   ![切り替え先のプランの選択](./media/billing-how-to-switch-azure-offer/selectoffer.png)
5. 切り替え先のプランによっては、切り替えによって生じる影響について注意が表示される場合があります。 先に進む前に、その内容をよく確認し、記載されている指示に従ってください。
   
   ![注意事項の確認](./media/billing-how-to-switch-azure-offer/thingstonote.png)
6. サブスクリプションの名前を変更できます。 既定では、サブスクリプションは新しいプラン名に設定されます。 **[プランの切り替え]** をクリックしてプロセスを完了します。
   
   ![緑色のボタンをクリック](./media/billing-how-to-switch-azure-offer/confirmpage.png)
7. 成功です。 これでサブスクリプションは新しいプランに切り替わりました。

## <a name="what-is-an-azure-offer"></a>Azure プランとは
Azure プランとは、ご利用の Azure サブスクリプションの*種類*です。 たとえば、[従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)、[Azure イン オープン プラン](https://azure.microsoft.com/offers/ms-azr-0111p/)、および [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) はすべて Azure プランです。 各サービスは異なる[使用条件](https://azure.microsoft.com/support/legal/offer-details/)を有し、特別な利点を持っています。 ご利用のサブスクリプションのプランは、アカウント センターのサブスクリプション ページで確認できます。 プラン名をクリックすると、詳細が表示されます。

   ![アカウント センターでプランのリンクをクリックして、詳細を確認する](./media/billing-how-to-switch-azure-offer/offerlink.png)

## <a name="why-cant-i-switch-offers"></a>プランを切り替えられないのですが、なぜでしょうか?
次の場合、**[別のプランに切り替える]** ボタンは表示されません。

* [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)を利用していない。 現時点では、別のプランに切り替えられるのは従量課金のサブスクリプションのみです。
  
  * [無料試用版](https://azure.microsoft.com/free/)をご利用の場合は、[従量課金制にアップグレードする](billing-upgrade-azure-subscription.md)方法を確認してください。
  * 別のサブスクリプションからプランを切り替えるには、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
* 最初の請求期間中である場合。プランを切り替えるには、最初の請求期間が終わるまで待つ必要があります。

次の場合、**"現在、お客様がお住まいのリージョンまたは国で利用できるプランはありません。"** と表示される可能性があります。

* プラン切り替えの対象ではない場合。 [切り替え可能なプランの一覧](#whats-supported)をご覧ください。

## <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Azure のプランを切り替えると、サービスと請求はどうなりますか?
以下で、アカウント センターで Azure のプランを切り替えるとどうなるかを詳しく説明します。

### <a name="access-to-services"></a>サービスへのアクセス
サブスクリプションに関連付けられているユーザーにサービスのダウンタイムは生じません。 ただし、切り替え先のプランによっては、制限がある場合があります。 たとえば、一部のプランでは運用環境での使用が禁止されているため、該当する場合は、運用環境のリソースを別のサブスクリプションに移動する必要があります。

### <a name="billing"></a>課金
切り替えを行った日に、すべての未処理料金の請求書が生成されます。 その後、サブスクリプションは新しいプランの価格条件に従って課金されます。 サブスクリプションの請求日はプランを変更した日付に変更されます。 プラン変更前の使用状況と課金データは保存されないので、切り替え前にコピーをダウンロードしておくことをお勧めします。

> [!NOTE]
> 請求関連の制約により、サブスクリプションの作成後、最初の請求サイクル内にプランを切り替えることはできません。
> 
> 

## <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>従量課金制から [Cloud Solution Provider](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) または [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA) に移行することはできますか?

- CSP に移行するには、「[Azure Subscription Migration to CSP (CSP への Azure サブスクリプションの移行)](https://blogs.technet.microsoft.com/hybridcloudbp/2016/08/26/azure-subscription-migration-to-csp/)」を参照してください。

- EA に移行するには、登録管理者に依頼してアカウントを EA に追加してもらう必要があります。 登録案内メールの指示に従って、サブスクリプションを EA 加入契約に移行します。 詳細については、EA ポータルの「[既存のアカウントの関連付け](https://ea.azure.com/helpdocs/associateExistingAccount)」を参照してください。

## <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>データとサービスを新しいサブスクリプションに移行できますか?

- リソースを直接、新しいサブスクリプションに移行することができます。「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/resource-group-move-resources.md)」を参照してください。

- Azure サブスクリプションとそこに含まれるすべての所有権を別のユーザーに譲渡するには、「[Transferring ownership of an Azure subscription](billing-subscription-transfer.md)」 (Azure サブスクリプションの所有権を譲渡する) を参照してください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。




<!--HONumber=Feb17_HO2-->


