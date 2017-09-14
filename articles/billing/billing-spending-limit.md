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
ms.topic: article
ms.date: 04/12/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: a2743ef34bde0faabb3afd2ace27acddd59d3d70
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Azure の使用制限のしくみと削除方法を理解する

Azure の使用制限とは、Azure サブスクリプションを使用できる限度です。 試用版プラン、または複数月にわたるクレジットが含まれるプランにサインアップする新規のすべてのお客様に対して、既定で使用制限が有効化されます。 使用制限は $0 です。 変更することはできません。 使用制限は、従量課金制サブスクリプションやコミットメント プランなどのサブスクリプションの種類では利用できません。 [Azure プランの完全な一覧および使用制限の可用性](https://azure.microsoft.com/support/legal/offer-details/)についてご覧ください。

## <a name="what-happens-when-i-reach-the-spending-limit"></a>使用制限に達するとどうなるか?

使用量が、プランに含まれている月額料金を使い果たす額に到達した場合、デプロイしたサービスは、請求月の残りの期間、無効になります。 たとえば、デプロイした Cloud Services は運用環境から削除され、Azure 仮想マシンは停止し、割り当てが解除されます。 サービスを無効にしないようにするために、使用制限を削除することができます。 サービスが無効化されると、ストレージ アカウントおよびデータベースのデータは、読み取り専用として管理者のみが利用できるようになります。 翌請求月の開始時点で、プランに複数月のクレジットが含まれる場合、サブスクリプションは再び有効化されます。 この後、Cloud Services を再デプロイし、ストレージ アカウントおよびデータベースに完全にアクセスできるようになります。

無料試用版が使用制限に達した場合は、90 日以内であればサブスクリプションを再度有効にして、自動的に[標準の従量課金制プランにアップグレード](billing-upgrade-azure-subscription.md)することができます。

プランの使用制限に達した場合は、その旨が通知されます。 [Azure アカウント センター](https://account.windowsazure.com)にログオンし、**[アカウント]** をクリックしてから**サブスクリプション**をクリックします。 使用制限に達したサブスクリプションに関する通知が表示されます。

## <a name="things-you-are-charged-for-even-if-you-have-a-spending-limit-enabled"></a>使用制限を有効化している場合でも課金対象となるもの

一部の Azure サービスと [Marketplace での購入](https://azure.microsoft.com/marketplace/)では、使用制限が設定されている場合でも、支払方法 (CC) の下、料金が発生する可能性があります。 Visual Studio のライセンス、Azure Active Directory Premium、Marketplace を通じて販売されたサポート プランとほとんどのサード パーティ ブランド サービスなどがその例として挙げられます。


## <a name="when-not-to-use-the-spending-limit"></a>使用制限を使用すべきでない場合

使用制限により、特定のマーケットプレースおよび Microsoft サービスのデプロイまたは使用ができなくなる場合があります。 サブスクリプションの使用制限を削除する必要があるシナリオを次に示します。

- Oracle のようなファースト パーティ イメージや Visual Studio Team Services などのサービスのデプロイを計画しています。 このシナリオは、ほぼ即座に使用制限に到達し、サブスクリプションが無効化される原因となります。

- 中断することができないサービスがあります。

- 仮想 IP アドレスなど、中断することのできない設定があるサービスおよびリソースを利用しています。 これらの設定は、サービスおよびリソースの割り当てが解除されると、失われます。


## <a name="remove-the-spending-limit"></a>使用制限の削除

サブスクリプションに関連付けられた有効な支払方法がある限り、使用制限はいつでも削除することができます。 複数月にわたるクレジットを含むプランの場合、翌請求サイクルの開始時に使用制限を再度有効化することもできます。

使用制限を削除するには、次の手順に従ってください。

1. [Azure アカウント センター](https://account.windowsazure.com)にログインします。

2. サブスクリプションを選択します。

3. 使用制限に達したためにサブスクリプションが無効にされている場合は、"サブスクリプションの使用制限に達したので、課金されないように無効にされました。" という通知をクリックします。 それ以外の場合は、**[サブスクリプションの状態]** 領域の **[使用制限の削除]** をクリックします。

4. 適切なオプションを選択します。

|オプション|効果|
|-------|-----|
|使用制限を無期限に削除|次の請求期間の開始時に使用制限を自動的に有効にすることなく、使用制限を削除します。|
|現在の請求期間の使用制限を削除|使用制限を削除し、次の請求期間の開始時に使用制限を自動的に有効に戻します。|

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

