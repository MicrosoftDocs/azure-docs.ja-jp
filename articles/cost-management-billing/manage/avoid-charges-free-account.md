---
title: Azure 無料アカウントでの課金を避ける
description: Azure 無料アカウントに対する課金の理由を理解します。 これらの課金を回避する方法について説明します。
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: f81c33f3b49352933801c8b89a2c3a1787c2356a
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682928"
---
# <a name="avoid-charges-with-your-azure-free-account"></a>Azure 無料アカウントでの課金を避ける

[Azure 無料アカウント](https://azure.microsoft.com/free/)では、最初の 30 日間に使用できる 200 ドルの Azure クレジットと 12 か月の無料サービス (数に制限あり) が提供されます。 無料サービスの限度の詳細については、「[Azure 無料アカウント FAQ](https://azure.microsoft.com/free/free-account-faq/)」を参照してください。 有効期限内のクレジットがあるか、または無料のサービスを制限の範囲内で使用する限り、料金は請求されません。

Azure 無料アカウントに対して課金が課せられる理由のいくつかを見てみましょう。

## <a name="your-credit-runs-out-or-is-expired"></a>クレジットを使い果たした、または期限切れ

クレジットの残高がなくなるか、30 日が経過して有効期限が切れると、ご利用のサブスクリプションとサービスは無効になります。 Azure サービスを引き続き使用するには、アカウントをアップグレードする必要があります。 詳細については、[Azure 無料アカウントのアップグレード](upgrade-azure-subscription.md)に関するページを参照してください。 12 か月間は、アップグレード後も無料サービスをご利用いただけます。また課金の対象となるのは、無料のサービスと数量を超えた使用分だけです。

## <a name="usage-exceeds-the-limits-of-free-services"></a>使用量が無料サービスの制限を超えている

Azure 無料アカウントには、月ごとの容量が制限された無料サービスが付随します。 無料容量は月末に期限切れになり、翌月に繰り越されません。 たとえば、毎月 5 GB のファイル ストレージが得られるとします。 1 か月で 2 GB だけを使用した場合、残りの 3 GB は翌月に繰り越されません。 課金されないようにするには、使用量を限度内に収めてください。 無料サービスの限度の詳細については、「[Azure 無料アカウント FAQ](https://azure.microsoft.com/free/free-account-faq/)」を参照してください。 無料サービスの使用量を確認するには、[Azure 無料アカウントに付随する無料サービスの使用量の確認](check-free-service-usage.md)に関する記事を参照してください。

## <a name="you-used-some-services-that-arent-free"></a>無料ではないサービスを使用した

アカウントのアップグレード後は、Azure 無料アカウントに無料で付随する以外のサービスを使用した場合、従量課金制で課金されます。 無料アカウントに付随しているサービスについては、「[Azure 無料アカウント FAQ](https://azure.microsoft.com/free/free-account-faq/)」を参照してください。 Azure portal でサービスの使用状況を確認できます。 詳細については、[ポータルでコストの内訳とバーン レートを定期的に確認する方法](getting-started.md#costs)に関するセクションを参照してください。

## <a name="you-reached-the-end-of-your-free-12-months"></a>無料の 12 か月間が終了した

無料のサービスおよび数量は、12 か月目の末日に期限切れになります。 無料サービスの有効期限は Azure portal で確認できます。

1. [Azure portal](https://portal.azure.com) にサインインする

2. 左側のナビゲーション領域で、 **[すべてのサービス]** を選択します。

3.  **[サブスクリプション]** を選択します。

4.  無料アカウントにサインアップするときに作成されたサブスクリプションを選択します。

5.  下へスクロールして、無料サービス グリッドを表示します。 グリッドの左上にあるヒントをクリックします。

![無料アカウントの特典の有効期限を確認できる場所を示すスクリーンショット](./media/avoid-charges-free-account/freeaccount-benefits-expiration-date.png)


無料のサービスおよび数量の有効期限が切れると、使用中のすべてのサービスの料金が従量課金制で課金されます。 使用しないサービスのリソースは、Azure ポータルを使用して削除できます。 どの Azure サービスも使用する予定がない場合は、[サブスクリプションを取り消す](cancel-azure-subscription.md)ことができます。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ
- [Azure 無料アカウントをアップグレードする](upgrade-azure-subscription.md)