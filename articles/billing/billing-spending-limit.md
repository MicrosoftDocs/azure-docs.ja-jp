---
title: "Azure 使用制限を理解する | Microsoft Docs"
description: "Azure の使用制限のしくみと削除方法を説明します。"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: genli
ms.openlocfilehash: 9313f3bd09b24db8e33b1f89eb5aebea871605b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Azure の使用制限のしくみと削除方法を理解する

Azure の使用制限は、信用取引金額を超過する支出を防ぐために存在します。 試用版プラン、または複数月にわたるクレジットが含まれるプランにサインアップする新規のすべてのお客様に対して、既定で使用制限が有効化されます。 使用制限は $0 です。 変更することはできません。 使用制限は、従量課金制サブスクリプションやコミットメント プランなどのサブスクリプションの種類では利用できません。 [Azure プランの完全な一覧および使用制限の可用性](https://azure.microsoft.com/support/legal/offer-details/)についてご覧ください。

**課金アラートお探しの場合:**  「[Azure サブスクリプションの課金アラートまたはクレジット アラートの設定](billing-set-up-alerts.md)」をご覧ください。

## <a name="what-happens-when-i-reach-the-spending-limit"></a>使用制限に達するとどうなるか?

使用量が、サブスクリプションに含まれている月額料金を使い果たす額に到達した場合、デプロイしたサービスは、請求の残りの期間、無効になります。 

たとえば、サブスクリプションに含まれているすべてのクレジットを使用した場合は、デプロイした Cloud Services は運用環境から削除され、Azure 仮想マシンは停止し、割り当てが解除されます。 ストレージ アカウントおよびデータベース内のデータは、読み取り専用で使用できます。

翌請求期間の開始時点で、サブスクリプションに複数月のクレジットが含まれる場合、サブスクリプションは自動的に再び有効化されます。 この後、Cloud Services を再デプロイし、ストレージ アカウントおよびデータベースに完全にアクセスできるようになります。

サブスクリプションの使用制限に達した場合は、電子メール通知が送信されます。 [アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインして、使用制限に到達したサブスクリプションに関する通知を確認します。

無料試用版で使用制限に到達している場合、[従量課金制にアップグレード](billing-upgrade-azure-subscription.md)して使用制限を削除し、自動的にサブスクリプションを再有効化できます。

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>アカウント センターでの使用制限の削除

サブスクリプションに関連付けられた有効な支払方法がある限り、使用制限はいつでも削除することができます。 複数月にわたるクレジットを含むプランの場合、翌請求期間の開始時に使用制限を再度有効化することもできます。

使用制限を削除するには、次の手順に従ってください。

1. [アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインします。
1. サブスクリプションを選択します。
1. 使用制限に達したためにサブスクリプションが無効にされている場合は、"サブスクリプションの使用制限に達したので、課金されないように無効にされました。" という通知をクリックします。 それ以外の場合は、**[サブスクリプションの状態]** 領域の **[使用制限の削除]** をクリックします。
1. 適切なオプションを選択します。

|オプション|効果|
|-------|-----|
|使用制限を無期限に削除|次の請求期間の開始時に使用制限を自動的に有効にすることなく、使用制限を削除します。|
|現在の請求期間の使用制限を削除|使用制限を削除し、次の請求期間の開始時に使用制限を自動的に有効に戻します。|

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="why-would-i-want-to-remove-the-spending-limit"></a>使用制限を削除しようと考えるときの理由は何ですか。

使用制限により、特定のサードパーティおよび Microsoft サービスのデプロイまたは使用ができなくなる場合があります。 サブスクリプションの使用制限を削除する必要があるシナリオを次に示します。

* Oracle のようなファースト パーティ イメージや Visual Studio Team Services などのサービスのデプロイを計画しています。 このシナリオは、ほぼ即座に使用制限に到達し、サブスクリプションが無効化される原因となります。
* 中断することができないサービスがあります。
* 仮想 IP アドレスなど、中断することのできない設定があるサービスおよびリソースを利用しています。 これらの設定は、サービスおよびリソースの割り当てが解除されると、失われます。

### <a name="how-do-i-turn-on-the-spending-limit-after-removing-it"></a>削除後、使用制限を有効にするにはどうしたらいいですか。

この機能は使用制限が無期限に削除された場合にのみ使うことができます。 次の請求期間の開始時に自動的にオンになるように変更します。

1. [アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインします。
1. 使用制限オプションを変更するには、黄色のバナーをクリックします。
1. **[次の請求期間に使用制限を有効にする \<請求期間の開始日\>]** を選びます。

### <a name="how-do-i-set-a-custom-spending-limit"></a>カスタムの使用制限を設定するにはどうしたらいいですか。

現在、カスタムの使用制限はありません。 ただし、[課金アラートを使用して支出を制御する](billing-set-up-alerts.md)ことができます。

### <a name="does-the-spending-limit-prevent-all-charges-from-azure"></a>使用制限は、Azure からのすべての料金を防止しますか。

一部の [Microsoft サービスとサードパーティ ブランド サービス](billing-understand-your-azure-marketplace-charges.md)では、使用制限が設定されている場合でも、支払い方法 (CC) の下、料金が発生する可能性があります。 Visual Studio のライセンス、Azure Active Directory Premium、[Azure Marketplace](https://azure.microsoft.com/marketplace/) を通じて販売されたサポート プランとほとんどのサード パーティ ブランド サービスなどがその例として挙げられます。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
