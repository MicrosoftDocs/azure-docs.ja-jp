---
title: Azure サブスクリプション オファーの変更
description: Azure アカウント センターを使用して Azure サブスクリプションを変更し、別のオファーに切り替える方法について説明します。
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 8e87bcb9f7d05fd5041623693c96f8c737758c32
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490979"
---
# <a name="change-your-azure-subscription-to-a-different-offer"></a>Azure サブスクリプションを別のオファーに変更する

[従量課金制料金の個別サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0003p/)のお客様は、[アカウント センター](https://account.windowsazure.com/Subscriptions)で Azure サブスクリプションを別のオファーに切り替えることができます。 たとえば、この機能を使えば、[Visual Studio サブスクライバー向けの月単位のクレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効活用できます。

**無料試用版からアップグレードしたいだけの場合:** [サブスクリプションのアップグレード](billing-upgrade-azure-subscription.md)に関するページを参照してください。

## <a name="whats-supported"></a>サポート対象:

従量課金制料金の個別サブスクリプションを以下に切り替えることができます。

- [開発テスト用の従量課金制プラン](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)

> [!NOTE]
> その他のプランの変更については、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
>
>

## <a name="switch-subscription-offer"></a>サブスクリプション オファーの切り替え

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインします。
1. 従量課金制料金の個別サブスクリプションを選択します。
1. **[別のプランに切り替える]** をクリックします。 このオプションは、従量課金制料金の個別サブスクリプションで、最初の請求期間が完了している場合にのみ使用できます。

   ![ページの右側にあるプランの切り替えボタンに注目](./media/billing-how-to-switch-azure-offer/switchbutton.png)
1. 現在のサブスクリプションから移行可能なオファーの一覧から、**目的のオファーを選択します**。 この一覧の内容はアカウントが関連付けられているメンバーシップに基づいて決まります。 何も表示されない場合は、[移行可能なプランの一覧](#whats-supported)をチェックして、適切なメンバーシップを持っていることを確認してください。

   ![切り替え先のプランの選択](./media/billing-how-to-switch-azure-offer/selectoffer.png)
1. 切り替え先のプランによっては、切り替えによって生じる影響について注意が表示される場合があります。 先に進む前に、その内容をよく確認し、記載されている指示に従ってください。

   ![注意事項の確認](./media/billing-how-to-switch-azure-offer/thingstonote.png)
1. サブスクリプションの名前を変更できます。 既定では、サブスクリプションは新しいプラン名に設定されます。 **[プランの切り替え]** をクリックしてプロセスを完了します。

   ![緑色のボタンをクリック](./media/billing-how-to-switch-azure-offer/confirmpage.png)
1. 成功です。 これでサブスクリプションは新しいオファーに切り替わりました。

## <a name="frequently-asked-questions"></a>よく寄せられる質問
次のセクションでは、よく寄せられる質問に回答します。

### <a name="what-is-an-azure-offer"></a>Azure プランとは

Azure オファーとは、ご利用の Azure サブスクリプションの*種類*です。 たとえば、[従量課金制のサブスクリプション](https://azure.microsoft.com/offers/ms-azr-0003p/)、[Azure イン オープン プラン](https://azure.microsoft.com/offers/ms-azr-0111p/)、および [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) はすべて Azure プランです。 各サービスは異なる[使用条件](https://azure.microsoft.com/support/legal/offer-details/)を有し、特別な利点を持っています。 ご利用のサブスクリプションのオファーは、アカウント センターのサブスクリプション ページで確認できます。 プラン名をクリックすると、詳細が表示されます。

   ![アカウント センターでプランのリンクをクリックして、詳細を確認する](./media/billing-how-to-switch-azure-offer/offerlink.png)

### <a name="why-dont-i-see-the-button"></a>ボタンが表示されないのはなぜですか。

次の場合、 **[別のプランに切り替える]** オプションは表示されません。

* [従量課金制料金のサブスクリプション](https://azure.microsoft.com/offers/ms-azr-0003p/)を持たない場合。 現時点では、別のプランに切り替えられるのは従量課金制料金のサブスクリプションのみです。
  * [無料試用版](https://azure.microsoft.com/free/)をご利用の場合は、[従量課金制にアップグレードする](billing-upgrade-azure-subscription.md)方法を確認してください。
  * 別のサブスクリプションからオファーを切り替えるには、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
* 最初の請求期間中である場合、プランを切り替えるには、最初の請求期間が終わるまで待つ必要があります。

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>次の場合、"現在、お客様がお住まいのリージョンまたは国で利用できるプランはありません。" と表示されるのはなぜですか。

* プラン切り替えの対象ではない可能性があります。 [切り替え可能なプランの一覧](#whats-supported)をチェックして、Visual Studio または Bizspark で適切に特典をアクティブ化していることを確認してください。
* 国や地域によっては一部のプランをご利用いただけません。

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Azure のプランを切り替えると、サービスと請求はどうなりますか?

以下で、アカウント センターで Azure のプランを切り替えるとどうなるかを詳しく説明します。

#### <a name="no-service-downtime"></a>サービスのダウンタイムは発生しない

サブスクリプションに関連付けられているユーザーにサービスのダウンタイムは生じません。 ただし、切り替え先のプランによっては、制限がある場合があります。 たとえば、一部のオファーでは運用環境での使用が禁止されているため、該当する場合は、運用環境のリソースを別のサブスクリプションに移動する必要があります。

#### <a name="quota-increases-are-reset"></a>クォータの増加はリセットされる

プランを切り替えると、[既定の制限を上回る制限またはクォータの増加](../azure-supportability/resource-manager-core-quotas-request.md)がリセットされます。 既定の制限を超えるリソースがあっても、サービスのダウンタイムはありません。 たとえば、サブスクリプションで 200 コアを使用しているとき、オファーを切り替えるとコア クォータが既定の 20 コアにリセットされます。 200 コアを使用している VM は影響を受けず、実行を継続します。 ただし、クォータ増加をもう一度要求しないのであれば、これ以上はコアをプロビジョニングできません。

#### <a name="billing"></a>課金

切り替えを行った日に、すべての未処理料金の請求書が生成されます。 その後、サブスクリプションは新しいオファーの価格条件に従って課金されます。 サブスクリプションの請求日はオファーを変更した日付に変更されます。 プラン変更前の使用状況と課金データは保存されないので、切り替え前にコピーをダウンロードしておくことをお勧めします。

### <a name="can-i-migrate-from-a-subscription-with-pay-as-you-go-rates-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>従量課金制料金のサブスクリプションから [Cloud Solution Provider](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) または [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA) に移行することはできますか?

* CSP に移行するには、[Azure の従量課金制サブスクリプションから CSP への移行](https://docs.microsoft.com/azure/cloud-solution-provider/migration/migration-from-payg-to-csp)に関する記事をご覧ください。
* EA に移行するには、登録管理者に依頼してアカウントを EA に追加してもらう必要があります。 登録案内メールの指示に従って、サブスクリプションを EA 加入契約に移行します。 詳細については、EA ポータルの「[既存のアカウントの関連付け](https://ea.azure.com/helpdocs/associateExistingAccount)」を参照してください。

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>データとサービスを新しいサブスクリプションに移行できますか?

* リソースを直接、新しいサブスクリプションに移行することができます。「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/resource-group-move-resources.md)」を参照してください。
* Azure サブスクリプションとそこに含まれるすべての所有権を別のユーザーに譲渡するには、「[Transferring ownership of an Azure subscription](billing-subscription-transfer.md)」 (Azure サブスクリプションの所有権を譲渡する) を参照してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次の手順
- [コストの分析を開始する](../cost-management/quick-acm-cost-analysis.md)
