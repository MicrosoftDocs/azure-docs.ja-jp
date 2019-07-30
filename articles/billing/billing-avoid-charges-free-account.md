---
title: Azure 無料アカウントでの課金を避ける
description: Azure 無料アカウントに対する課金の理由を理解します。 これらの課金を回避する方法について説明します。
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: ac5bcf0a4dd9c7ee593c70a63509a21f28ba1456
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491476"
---
# <a name="avoid-charges-with-your-azure-free-account"></a>Azure 無料アカウントでの課金を避ける

Azure 無料アカウントでは、最初の 30 日間に使用できる 200 ドルの Azure クレジットと 12 か月の無料サービス (数に制限あり) が提供されます。 詳細については、[Azure 無料アカウント](https://azure.microsoft.com/free/)に関するページをご覧ください。 クレジットの状態に応じて、クレジットを使用できる場合もあれば、無料のサービスおよび数量の限度を超えた使用分に対して課金される場合もあります。

## <a name="azure-free-account-might-use-account-credit"></a>Azure 無料アカウントはアカウント クレジットを使用する可能性がある
有効期限が切れていない Azure クレジットがまだ残っている場合は、無料のサービスおよび数量の限度を超えた使用分の支払いにクレジットが使用されます。

## <a name="your-credit-runs-out-or-is-expired"></a>クレジットを使い果たした、または期限切れ
クレジットを使い果たしたか、30 日目が来て期限切れになった場合、Azure はサブスクリプションを無効にします。 Azure サービスを引き続き使用するには、従量課金制料金を使用する個別のサブスクリプションにアップグレードする必要があります。 詳細については、[無料試用版 Azure サブスクリプションのアップグレード](billing-upgrade-azure-subscription.md)に関する記事を参照してください。 サブスクリプションのアップグレード後も、12 か月間の無料サービスを利用できます。 無料のサービスおよび数量の限度を超えた使用分にのみ課金されます。

Azure 無料アカウントに対して課金が課せられる理由のいくつかを見てみましょう。

### <a name="usage-exceeds-the-limits-of-free-services"></a>使用量が無料サービスの制限を超えている

Azure 無料アカウントには、月ごとの容量が制限された無料サービスが付随します。 無料容量は月末に期限切れになり、翌月に繰り越されません。 たとえば、毎月 5 GB のファイル ストレージが得られるとします。 1 か月で 2 GB だけを使用した場合、残りの 3 GB は翌月に繰り越されません。 課金されないようにするには、使用量を限度内に収めてください。 無料サービスの限度の詳細については、「[Azure 無料アカウント FAQ](https://azure.microsoft.com/free/free-account-faq/)」を参照してください。 無料サービスの使用量を確認するには、[Azure 無料アカウントに付随する無料サービスの使用量の確認](billing-check-free-service-usage.md)に関する記事を参照してください。

### <a name="some-services-are-not-free"></a>一部のサービスは無料ではない

Azure 無料アカウントに無料で付随していないサービスを使用している場合、従量課金制で課金されます。 無料アカウントに付随しているサービスの詳細については、「[Azure 無料アカウント FAQ](https://azure.microsoft.com/free/free-account-faq/)」を参照してください。 Azure portal または Azure 使用状況ファイルでサービスの使用状況を確認できます。 詳細については、[ポータルでの原価計算およびバーンレートの定期的チェック](billing-getting-started.md#costs)と[アカウント センターからの使用状況のダウンロード](billing-download-azure-invoice-daily-usage-date.md)に関する記事を参照してください。

### <a name="you-reached-the-end-of-your-free-12-months"></a>無料の 12 か月間が終了した

無料のサービスおよび数量は、12 か月目の末日に期限切れになります。 無料サービスの有効期限は Azure portal で確認できます。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. 左側のナビゲーション領域で、 **[すべてのサービス]** を選択します。

3.  **[サブスクリプション]** を選択します。

4.  無料アカウントにサインアップしたときに作成したサブスクリプションを選択します。

5.  下へスクロールして、無料サービス グリッドを表示します。 グリッドの左上にあるヒントをクリックします。

![無料アカウントの特典の有効期限を確認できる場所を示すスクリーンショット](./media/billing-avoid-charges-free-account/freeaccount-benefits-expiration-date.png)


無料のサービスおよび数量の有効期限が切れると、使用中のすべてのサービスに対して、従量課金制で課金されます。 使用しないサービスのリソースは、Azure ポータルを使用して削除できます。 どの Azure サービスも使用する予定がない場合は、[サブスクリプションを取り消す](billing-how-to-cancel-azure-subscription.md)ことができます。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次の手順
- [無料試用版 Azure サブスクリプションをアップグレードする](billing-upgrade-azure-subscription.md)
