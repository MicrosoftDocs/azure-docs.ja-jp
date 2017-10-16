---
title: "無料アカウントの課金の回避 - Azure | Microsoft Docs"
description: "Azure 無料アカウントに対する課金の理由を理解します。 これらの課金を回避する方法について説明します。"
services: 
documentationcenter: 
author: amberbhargava
manager: amberb
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: amberb
ms.openlocfilehash: e4f7571acb72d8f099898fffc05eb43083aeabfe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="avoid-getting-charged-for-your-azure-free-account"></a>Azure 無料アカウントの課金の回避

Azure 無料アカウントは、最初の 30 日以内に使用できる 200 ドルの Azure クレジットと 12 か月の無料サービスを提供します。 詳細については、[Azure 無料アカウント](https://azure.microsoft.com/free/)と[Azure 無料アカウントを使用した無料サービスの作成](billing-create-free-services-included-free-account.md)に関する記事を参照してください。 クレジットの状態によっては、クレジットを使用できることも、Azure 無料アカウントの特典ではまかなえない使用量に対して課金されることもあります。

## <a name="you-still-have-active-azure-free-account-credit"></a>Azure 無料アカウントのアクティブなクレジットがまだ残っている場合 
有効期限が切れていない Azure クレジットがまだ残っている場合は、Azure 無料アカウントに対して課せられる課金の支払いにクレジットを使用します。

## <a name="your-credit-ran-out-or-has-expired"></a>クレジットを使い果たしたか、期限切れになった場合
クレジットを使い果たしたか、30 日目が来て期限切れになった場合、Azure はサブスクリプションを無効にします。 従量サブスクリプションにアップグレードしてサブスクリプションを有効にしない限りは、課金されません。 詳細については、[無料試用版 Azure サブスクリプションの従量課金制へのアップグレード](billing-upgrade-azure-subscription.md)に関する記事を参照してください。 サブスクリプションをアップグレードした後、Azure 無料アカウントに課せられた課金に対して従量課金レートで課金されます。 


Azure 無料アカウントに対して課金が課せられる理由のいくつかを見てみましょう。


### <a name="your-usage-exceeds-the-limits-of-free-services-included-with-azure-free-account"></a>使用量が、Azure 無料アカウントに付随する無料サービスの限度を超えた場合

Azure 無料アカウントには、月ごとの容量が制限された無料サービスが付随します。 無料容量は月末に期限切れになり、翌月に繰り越されません。 たとえば、毎月 5 GB のファイル ストレージが得られるとします。 1 か月で 2 GB だけを使用した場合、残りの 3 GB は翌月に繰り越されません。 課金されないようにするには、使用量を限度内に収めてください。 無料サービスの限度の詳細については、[Azure 無料アカウントとは](https://azure.microsoft.com/free/free-account-faq/)に関する記事を参照してください。 無料サービスの使用量を確認するには、[Azure 無料アカウントに付随する無料サービスの使用量の確認](billing-check-free-service-usage.md)に関する記事を参照してください。 

### <a name="you-are-using-services-not-included-with-azure-free-account"></a>Azure 無料アカウントに付随しないサービスを使用している場合

Azure 無料アカウントに無料で付随していないサービスを使用している場合、従量課金制で課金されます。 無料アカウントに付随しているサービスの詳細については、[Azure 無料アカウントとは](https://azure.microsoft.com/free/free-account-faq/)に関する記事を参照してください。 Azure ポータル上で、または Azure 使用状況ファイルから使用量を確認できます。 詳細については、[ポータルでの原価計算およびバーンレートの定期的チェック](billing-getting-started.md#costs)と[アカウント センターからの使用状況のダウンロード](billing-download-azure-invoice-daily-usage-date.md)に関する記事を参照してください。 

### <a name="you-have-reached-the-end-of-your-azure-free-account-benefits"></a>Azure 無料アカウントの特典の終了に至った場合

Azure 無料アカウントの特典は 12 か月目の月末に期限切れになります。 それ以降は、使用しているサービスに対して従量課金制で支払うように Azure から請求されます。 使用しないサービスのリソースは、Azure ポータルを使用して削除できます。 どの Azure サービスも使用する予定がない場合は、[サブスクリプションを取り消す](billing-how-to-cancel-azure-subscription.md)ことができます。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
