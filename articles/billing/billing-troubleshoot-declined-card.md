---
title: Azure へのサインアップ時にカードが拒否される問題のトラブルシューティング
description: Azure portal またはアカウント センターで Azure へのサインアップ時にクレジット カードが拒否される問題を解決します。
author: v-miegge
manager: adpick
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: banders
ms.openlocfilehash: 730238d62e4ee4aad1807a4461c9b26ee1c8485d
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656838"
---
# <a name="troubleshoot-a-declined-card-at-azure-sign-up"></a>Azure へのサインアップ時にカードが拒否される問題のトラブルシューティング

この記事では、Azure portal または Azure アカウント センターにおける Azure のサインアップでクレジット カードが拒否される問題の解決を支援します。 問題のトラブルシューティングを開始する前に、次の点を確認してください。

- Azure アカウント プロファイルに指定した情報 (連絡先のメール アドレス、住所、電話番号など) が正しいことを確認する。
- クレジット カード情報が正しいことを確認する。
- 同じ情報を持つ Microsoft アカウントをがないことを確認する。
- デビット カードは使用できません。

## <a name="issues"></a>発行

以下に、Azure のサインアップでクレジット カードが拒否される原因として考えられる一般的な問題を列挙します。

### <a name="the-credit-card-provider-is-not-accepted-for-your-country"></a>お住まいの国でクレジット カード会社がサポートされていない

カードを選択すると、選択した国で有効なカード オプションが表示されます。 ご利用の銀行またはカード発行会社に連絡して、クレジット カードが国際取引に対応しているかどうかを確認してください。 サポートされている国と通貨の詳細については、[Azure の購入に関する FAQ](https://azure.microsoft.com/pricing/faq/) を参照してください。

>[!Note]
>現在、インドでは、American Express クレジット カードが支払い方法としてサポートされていません。 支払形式として承認される時期は未定です。

### <a name="youre-using-a-virtual-or-prepaid-card"></a>バーチャル カードまたはプリペイド カードを使用している 

バーチャルまたはプリペイド式のクレジット カードやデビット カードは、Azure サブスクリプションの支払い方法として認められていません。

### <a name="your-credit-information-is-inaccurate-or-incomplete"></a>クレジットの情報が不正確または不完全である 

入力する名前、アドレス、CVV コードは、カードに印刷された内容と完全に一致している必要があります。

### <a name="the-card-is-inactive-or-blocked"></a>カードが無効であるか、ブロックされている 

ご利用の銀行に連絡して、カードが有効であることを確認してください。

サインアップに関する他の問題が生じている可能性があります。 

Azure のサインアップに関する問題をトラブルシューティングする方法について詳しくは、次のナレッジ ベース記事を参照してください。 

[Azure portal または Azure アカウント センターから Azure にサインアップできない](billing-troubleshoot-azure-sign-up.md)

### <a name="you-represent-a-business-that-doesnt-want-to-pay-by-card"></a>カードでの支払いを希望しない企業による 

法人のお客様は、Azure サブスクリプションの支払いに、小切手、オーバーナイト小切手、電信送金など、請求書による支払い方法を利用できます。 請求書で支払うようにアカウントを設定した後は、Microsoft 顧客契約を締結していて、Azure Web サイト経由で Azure にサインアップする場合を除き、別の支払オプションに変更することはできません。

請求書による支払い方法の詳細については、[Azure サブスクリプションの支払いを請求書で行うための要求を送信する方法](billing-how-to-pay-by-invoice.md)に関するページを参照してください。

### <a name="your-credit-card-information-is-outdated"></a>クレジット カードの情報が古い 

カードの変更と削除を含め、カード情報を管理する方法については、[Azure のクレジットを追加、更新、削除する方法](billing-how-to-change-credit-card.md)に関するページを参照してください。

## <a name="additional-help-resources"></a>その他のヘルプ リソース

Azure の請求とサブスクリプションに関するその他のトラブルシューティング記事

- [サインアップの問題](billing-troubleshoot-azure-sign-up.md)
- [サブスクリプションのサインインの問題](billing-troubleshoot-sign-in-issue.md)
- [サブスクリプションが見つからない](billing-no-subscriptions-found.md)
- [Enterprise コスト ビューが無効になっている](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次の手順

- [Azure の課金に関するドキュメント](index.md)
