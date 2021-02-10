---
title: 無効な Azure サブスクリプションを再度有効にする
description: Azure サブスクリプションが無効になっているときに再度有効にする方法について説明します。
keywords: azure サブスクリプションの無効化
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/19/2021
ms.author: banders
ms.openlocfilehash: d7b5f1ae6db633bd1af10b1a0de1392c2a1fbcef
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685538"
---
# <a name="reactivate-a-disabled-azure-subscription"></a>無効な Azure サブスクリプションを再度有効にする

Azure サブスクリプションは、クレジットの有効期限が切れている、使用制限に達している、請求の支払い期限が過ぎている、請求がクレジット カードの制限を超えている、アカウント管理者によってサブスクリプションが取り消されているという理由で無効にされることがあります。 どの問題に該当するかを調べ、この記事の手順を参照してサブスクリプションを再度有効にできます。

## <a name="your-credit-is-expired"></a>クレジットの有効期限が切れている

Azure 無料アカウントにサインアップすると、30 日間試用できる 200 ドルの Azure クレジットと 12 か月の無料サービスを提供する無料試用版のサブスクリプションを入手できます。 30 日目の終了時点で、Azure はサブスクリプションを無効にします。 サブスクリプションに含まれるクレジットと無料サービスを超えた使用による意図しない料金の発生を防止するために、お使いのサブスクリプションは無効になります。 Azure サービスを引き続き使用するには、[サブスクリプションをアップグレードする](upgrade-azure-subscription.md)必要があります。 サブスクリプションのアップグレード後も、12 か月間の無料サービスを利用できます。 無料サービスの数量の限度を超えた使用分にのみ課金されます。

## <a name="you-reached-your-spending-limit"></a>使用制限に達している

無料試用版などのクレジットを利用した Azure サブスクリプションと Visual Studio Enterprise では、使用制限を設定しています。 含まれているクレジットの範囲でしかサービスを使用できません。 使用率が使用制限に達すると、Azure では、該当の残りの請求期間中、お使いのサブスクリプションを無効にします。 サブスクリプションに含まれるクレジットを超えた使用による意図しない料金の発生を防止するために、お使いのサブスクリプションは無効になります。 使用制限の解除については、[Azure portal での使用制限の解除](spending-limit.md#remove)に関する記事を参照してください。

> [!NOTE]
> 無料試用版サブスクリプションを使用しているときに使用制限を削除すると、ご利用のサブスクリプションは、無料試用の終了時に従量課金制の個別サブスクリプションに変換されます。 残りのクレジットは、サブスクリプションの作成後、丸 30 日間保持されます。 また、12 か月間の無料サービスにアクセスすることもできます。

Azure の課金アクティビティの監視および管理については、「[Azure のコストの管理を計画する](../understand/plan-manage-costs.md)」を参照してください。

## <a name="your-bill-is-past-due"></a>請求の支払い期限が過ぎている

未払い額を支払うには、次の記事のいずれかを参照してください。

- 従量課金制を含む Microsoft オンライン サブスクリプション プログラム サブスクリプションの場合は、[Azure からのメール通知を受けて、支払期限を過ぎた Azure サブスクリプションの未払い額を支払う方法](resolve-past-due-balance.md)に関するページを参照してください。
- Microsoft 顧客契約サブスクリプションの場合は、「[Microsoft Azure の請求書の支払い方法](../understand/pay-bill.md)」を参照してください。

## <a name="the-bill-exceeds-your-credit-card-limit"></a>請求がクレジット カードの制限を超えている

この問題を解決するには、[別のクレジット カードに切り替えます](change-credit-card.md)。 ご自身が会社を代表するユーザーである場合は、[請求書による支払いに切り替える](pay-by-invoice.md)ことができます。

## <a name="the-subscription-was-accidentally-canceled"></a>サブスクリプションが誤って取り消された

アカウント管理者として、誤って従量課金制サブスクリプションを取り消した場合、Azure portal で再度有効にできます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. [サブスクリプション] に移動し、取り消したサブスクリプションを選択します。
1. **[Reactivate]\(再度有効にする\)** を選択します。
1. **[OK]** を選択して、再度有効にする処理を確定します。  
    :::image type="content" source="./media/subscription-disabled/reactivate-sub.png" alt-text="[再アクティブ化の確認] を示すスクリーンショット" :::

その他のサブスクリプションの種類については、[サポートに連絡して](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)、サブスクリプションを再度有効にしてもらいます。

## <a name="after-reactivation"></a>再度有効にした後

サブスクリプションを再度有効にした後、リソースの作成または管理に遅延が発生する可能性があります。 遅延が 30 分を超えた場合は、[Azure の課金サポート](https://go.microsoft.com/fwlink/?linkid=2083458)にお問い合わせください。 ほとんどの Azure リソースは自動的に再開され、対処は必要ありません。 しかし、Azure サービス リソースを確認し、自動的に再開されていないものは再起動することをお勧めします。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ
- [Azure のコストの管理を計画する](../understand/plan-manage-costs.md)方法について説明します。
