---
title: Azure の支払いに関する問題のトラブルシューティング
description: Microsoft Azure portal またはアカウント センターで支払い情報アカウントを更新しようとしたときに発生する問題を解決します。
author: v-miegge
manager: na
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: jaserano
ms.openlocfilehash: 45f6f181ddbf7e76662f76c433d8ff68c18f0de1
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "69656846"
---
# <a name="troubleshoot-azure-payment-issues"></a>Azure の支払いに関する問題のトラブルシューティング

Microsoft Azure portal または Azure アカウント センターで支払い情報アカウントを更新しようとしたときに問題が発生する場合があります。 問題のトラブルシューティングを行う前に、次のガイドラインを考慮してください。

- Azure アカウント プロファイルに指定した情報 (連絡先メール アドレス、住所、電話番号など) が正しいことを確認する。
- クレジット カード情報が正しいことを確認する。
- 同じ情報を持つ Microsoft アカウントをがないことを確認する。

## <a name="issues"></a>発行

エラーを解決するには、Azure へのサインアップを試みたときに発生した問題を選択してください。

### <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>保存された請求支払い方法からクレジット カードを削除できない

仕様により、アクティブなサブスクリプションからクレジット カードを削除することはできません。

既存のカードを削除する必要がある場合は、以前の支払い方法を正常に削除できるよう新しいカードをサブスクリプションに追加するか、またはサブスクリプションをキャンセルする必要があります。 これにより、サブスクリプションが完全に削除されてカードが削除されます。

### <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>新しい支払い方法の追加後、以前の支払い方法を削除できない

新しい支払い方法がサブスクリプションに関連付けられていない可能性があります。 支払い方法をサブスクリプションに関連付ける方法については、[Azure 用にクレジット カードまたはデビット カードを追加、更新、または削除する方法](billing-how-to-change-credit-card.md)に関するページを参照してください。

カードが拒否される問題をトラブルシューティングするには、[Azure のサインアップ時に拒否されたカードをトラブルシューティングする方法](billing-troubleshoot-declined-card.md)に関するページを参照してください。

### <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>"*支払い方法を削除できない*" というエラーが発生して支払い方法を削除できない

これは、未払い残高が原因で発生します。 支払い方法を削除する前に、未払い残高をすべて清算してください。

### <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>支払い方法を更新するサブスクリプションがマイ アカウントに表示されない

対象のサブスクリプション用とは違うメール ID が使用されている可能性があります。

この問題をトラブルシューティングするには、[Azure portal または Azure アカウント センターにおける "サブスクリプションが見つかりません" のサインイン エラー](billing-no-subscriptions-found.md)に関するページを参照してください。

### <a name="unable-to-make-payment-for-a-subscription"></a>サブスクリプションに対する支払いができない

"*お支払い期限を過ぎています。お客様のお支払い方法に問題があります*" または "*申し訳ございませんが、情報を保存できません。ブラウザーを閉じて、もう一度やり直してください。* " というエラー メッセージが表示された場合、カードが金融機関によって拒否されたために、保留中の支払いがカードにあります。

支払いに必要な残高がクレジット カードにあることを確認してください。 ない場合は、支払いに他のカードを使用するか、または金融機関に問い合わせて問題を解決してください。

次の問題については、ご利用の銀行にお問い合わせください。

- 国際取引が有効になっていない。
- カードに与信限度額があり、なおかつ残高を決済する必要がある。
- カードで定期的な支払いが有効になっている。

### <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>ブラウザーの問題 (ブラウザーが応答しない、読み込みをしない、など) が原因で支払い方法を変更できない

アクティブな Azure セッションからすべてログアウトした後、[「Microsoft Edge で InPrivate ブラウズを使う」という記事](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate)の手順に従って、Microsoft Edge または Internet Explorer の InPrivate セッションを開始します。

プライベート セッションで、[クレジット カードの変更方法](billing-how-to-change-credit-card.md)に関するページの手順に従い、クレジット カード情報を更新または変更します。

また、次の操作も試してください。

- ブラウザーを最新の情報に更新する
- 別のブラウザーを使用する
- キャッシュされた Cookie を削除する

### <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>支払い方法を更新した後もサブスクリプションが無効のままである

この問題は、未払い残高が原因で発生します。 支払い方法を削除する前に、未払い残高をすべて清算してください。

### <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>XML エラー応答ページが原因で支払い方法を変更できない

このメッセージは、[Azure portal](https://portal.azure.com/) を使用して新しいクレジット カードを追加しようとすると発生します。

カードの詳細を追加するには、アカウント管理者のメール アドレスを使用して、Azure アカウント ポータルにサインインしてください。

## <a name="additional-help-resources"></a>その他のヘルプ リソース

Azure の請求とサブスクリプションに関するその他のトラブルシューティング記事

- [拒否されたカード](billing-troubleshoot-declined-card.md)
- [サブスクリプションのサインインの問題](billing-troubleshoot-sign-in-issue.md)
- [サブスクリプションが見つからない](billing-no-subscriptions-found.md)
- [Enterprise コスト ビューが無効になっている](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次の手順

- [Azure の課金に関するドキュメント](index.md)
