---
title: Azure の支払いに関する問題のトラブルシューティング
description: Microsoft Azure portal またはアカウント センターで支払い情報アカウントを更新しようとしたときに発生する問題を解決します。
author: v-miegge
ms.reviewerr: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: jaserano
ms.openlocfilehash: adc3874ba211de270589d8ea36a04b9431e9e12a
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684917"
---
# <a name="troubleshoot-azure-payment-issues"></a>Azure の支払いに関する問題のトラブルシューティング

Microsoft Azure portal または Azure アカウント センターで、支払い情報アカウントを更新しようとしたときに、問題またはエラーが発生する場合があります。

この問題を解決するには、以下のトピックの中から、発生しているエラーに最も近いものを選択してください。

## <a name="my-credit-card-was-declined-when-i-tried-to-sign-up-for-azure"></a>Azure にサインアップしようとするとクレジット カードが拒否される

カードが拒否される問題をトラブルシューティングするには、「[Azure へのサインアップ時にカードが拒否される問題のトラブルシューティング](troubleshoot-declined-card.md)」を参照してください。

## <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>支払い方法を更新するサブスクリプションがマイ アカウントに表示されない

対象のサブスクリプション用とは違うメール ID が使用されている可能性があります。

この問題をトラブルシューティングするには、[Azure portal または Azure アカウント センターにおける "サブスクリプションが見つかりません" のサインイン エラー](no-subscriptions-found.md)に関するページを参照してください。

## <a name="unable-to-use-a-virtual-or-prepaid-credit-or-debit-card-as-a-payment-method"></a>バーチャルまたはプリペイド式のクレジット カードやデビット カードを支払い方法として使用することはできません。

*   バーチャルまたはプリペイド式のクレジット カードは、Azure サブスクリプションの支払い方法として認められていません。
*   デビット カードは、Azure サブスクリプションの支払い方法として認められていません。

詳細については、「[Azure へのサインアップ時にカードが拒否される問題のトラブルシューティング](troubleshoot-declined-card.md)」を参照してください。

## <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>保存された請求支払い方法からクレジット カードを削除できない

仕様により、アクティブなサブスクリプションからクレジット カードを削除することはできません。

既存のカードを削除する必要がある場合は、以前の支払い方法を正常に削除できるよう新しいカードをサブスクリプションに追加するか、またはサブスクリプションをキャンセルする必要があります。 これにより、サブスクリプションが完全に削除されてカードが削除されます。

## <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>新しい支払い方法の追加後、以前の支払い方法を削除できない

新しい支払い方法がサブスクリプションに関連付けられていない可能性があります。 支払い方法をサブスクリプションに関連付ける方法については、「[Azure 用にクレジット カードを追加、更新、または削除する](change-credit-card.md)」を参照してください。

## <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>"*支払い方法を削除できない*" というエラーが発生して支払い方法を削除できない

これは、未払い残高が原因で発生します。 支払い方法を削除する前に、未払い残高をすべて清算してください。

## <a name="unable-to-make-payment-for-a-subscription"></a>サブスクリプションに対する支払いができない

次のようなエラー メッセージが表示されることがあります。"*お支払い期限を過ぎています。お客様のお支払い方法に問題があります*" または "*申し訳ございませんが、情報を保存できません。ブラウザーを閉じて、もう一度やり直してください*"。この場合、カードが金融機関によって拒否されたため、カードに保留中の支払いがあります。

支払いに必要な残高がクレジット カードにあることを確認してください。 ない場合は、支払いに他のカードを使用するか、または金融機関に問い合わせて問題を解決してください。

次の問題については、ご利用の銀行にお問い合わせください。

- 国際取引が有効になっていない。
- カードに与信限度額があり、なおかつ残高を決済する必要がある。
- カードで定期的な支払いが有効になっている。

## <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>ブラウザーの問題 (ブラウザーが応答しない、読み込みをしない、など) が原因で支払い方法を変更できない

アクティブな Azure セッションからすべてログアウトした後、[「Microsoft Edge で InPrivate ブラウズを使う」という記事](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate)の手順に従って、Microsoft Edge または Internet Explorer の InPrivate セッションを開始します。

プライベート セッションで、[クレジット カードの変更方法](change-credit-card.md)に関するページの手順に従い、クレジット カード情報を更新または変更します。

また、次の操作も試してください。

- ブラウザーを最新の情報に更新する
- 別のブラウザーを使用する
- キャッシュされた Cookie を削除する

## <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>支払い方法を更新した後もサブスクリプションが無効のままである

この問題は、未払い残高が原因で発生します。 支払い方法を削除する前に、未払い残高をすべて清算してください。

## <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>XML エラー応答ページが原因で支払い方法を変更できない

このメッセージは、[Azure portal](https://portal.azure.com/) を使用して新しいクレジット カードを追加しようとすると発生します。

カードの詳細を追加するには、アカウント管理者のメール アドレスを使用して、Azure アカウント ポータルにサインインしてください。

## <a name="additional-help-resources"></a>その他のヘルプ リソース

Azure の請求とサブスクリプションに関するその他のトラブルシューティング記事

- [拒否されたカード](troubleshoot-declined-card.md)
- [サブスクリプションのサインインの問題](troubleshoot-sign-in-issue.md)
- [サブスクリプションが見つからない](no-subscriptions-found.md)
- [Enterprise コスト ビューが無効になっている](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次のステップ

- [Azure の課金に関するドキュメント](../../billing/index.md)
