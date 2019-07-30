---
title: Azure の使用制限 | Microsoft Docs
description: この記事では、Azure の使用制限のしくみと削除方法を説明します。
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: fb43f29827309fc8986ee6b4653f5edf303cc21d
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490613"
---
# <a name="azure-spending-limit"></a>Azure の使用制限

Azure の使用制限により、クレジット額を超過する支出が防止されます。 Azure 試用版プラン、または複数月にわたるクレジットが含まれるプランにサインアップする新規のすべてのお客様に対して、既定で使用制限が有効化されます。 使用制限は $0 です。 変更することはできません。 使用制限は、コミットメント プランや従量課金制料金のプランなどの一部のプランのサブスクリプションでは利用できません。 [Azure プランの完全な一覧および使用制限の可用性](https://azure.microsoft.com/support/legal/offer-details/)についてご覧ください。

## <a name="reaching-a-spending-limit"></a>使用制限への到達

使用量が、Azure サブスクリプションに含まれている月額料金を使い果たす額に到達した場合、デプロイしたサービスは、請求の残りの期間、無効になります。

たとえば、サブスクリプションに含まれているすべてのクレジットを使用した場合は、デプロイした Azure リソースは運用環境から削除され、Azure 仮想マシンは停止し、割り当てが解除されます。 ストレージ アカウントのデータは、読み取り専用として使用できます。

翌請求期間の開始時点で、サブスクリプション オファーに複数月のクレジットが含まれる場合、サブスクリプションは自動的に再び有効化されます。 この後、Azure リソースを再デプロイし、ストレージ アカウントおよびデータベースに完全にアクセスできるようになります。

サブスクリプションの使用制限に達した場合は、Azure により電子メール通知が送信されます。 [アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインして、使用制限に到達したサブスクリプションに関する通知を確認します。

無料試用版のサブスクリプションで使用制限に到達している場合、[従量課金制](billing-upgrade-azure-subscription.md)料金のプランにアップグレードすることで、使用制限を削除し、サブスクリプションを自動的に有効化できます。

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>アカウント センターでの使用制限の削除

Azure サブスクリプションに関連付けられた有効な支払方法がある限り、使用制限はいつでも削除できます。 複数月にわたるクレジットを含むプランの場合、翌請求期間の開始時に使用制限を有効化することもできます。

使用制限を削除するには、次の手順に従ってください。

1. [アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインします。
1. サブスクリプションを選択します。 使用制限に達したためにサブスクリプションが無効にされている場合は、次の通知をクリックします:**サブスクリプションの使用制限に達したので、課金されないように無効にされました。** それ以外の場合は、 **[サブスクリプションの状態]** 領域の **[使用制限の削除]** をクリックします。
1. 適切なオプションを選択します。

![使用制限を削除するためのオプションを選択](./media/billing-spending-limit/remove-spending-limit.PNG)

| オプション | 効果 |
| --- | --- |
| 使用制限を無期限に削除 | 次の請求期間の開始時に使用制限を自動的に有効にすることなく、使用制限を削除します。 |
| 現在の請求期間の使用制限を削除 | 使用制限を削除し、次の請求期間の開始時に使用制限を自動的に有効に戻します。 |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>使用制限を削除しようと考えるときの理由は何ですか

使用制限により、特定のサードパーティおよび Microsoft サービスのデプロイまたは使用ができなくなる場合があります。 サブスクリプションの使用制限を削除する必要がある状況を次に示します。

-  Oracle のようなファースト パーティ イメージや Azure DevOps Services などのサービスのデプロイを計画しています。 この状況は、ほぼ即座に使用制限に到達し、サブスクリプションが無効化される原因となります。
- 中断したくないサービスがあります。
- 仮想 IP アドレスなど、中断することのできない設定があるサービスおよびリソースを利用しています。 これらの設定は、サービスおよびリソースの割り当てが解除されると、失われます。

## <a name="turn-on-the-spending-limit-after-removing"></a>削除後に使用制限を有効にする

この機能は使用制限が無期限に削除された場合にのみ使うことができます。 次の請求期間の開始時に自動的にオンになるように変更します。

1. [アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインします。
1. 使用制限オプションを変更するには、黄色のバナーをクリックします。
1. **[次の請求期間に使用制限を有効にする \<請求期間の開始日\>]** を選びます。

## <a name="custom-spending-limit"></a>カスタムの使用制限

カスタムの使用制限は使用できません。

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>すべての料金が使用制限で防止されるわけではない

[Azure Marketplace で公開されている一部の外部サービス](billing-understand-your-azure-marketplace-charges.md)には、サブスクリプション クレジットを使用できません。また、使用制限が設定されている場合でも、別に料金を請求される場合があります。 Visual Studio のライセンス、Azure Active Directory Premium、サポート プランとほとんどのサード パーティ ブランド サービスなどがその例として挙げられます。 新しい外部サービスをプロビジョニングすると、警告が表示され、サービス料が別に請求されることをお知らせします。

![Marketplace 購入警告](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次の手順
- [従量課金制](billing-upgrade-azure-subscription.md)料金のプランにアップグレードする。
