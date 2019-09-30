---
title: Azure サブスクリプションのサインインに関する問題のトラブルシューティング
description: Azure portal または Azure アカウント センターにサインインできない問題の解決を支援します。
author: v-miegge
manager: na
editor: na
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: ca641813e8b01a39d31a56e3730424b0fa1d6436
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "69656834"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Azure サブスクリプションのサインインに関する問題のトラブルシューティング

このガイドは、Azure portal または Azure アカウント センターにサインインできない問題の解決を支援します。 

## <a name="issues"></a>発行

### <a name="page-hangs-in-the-loading-status"></a>ページの読み込み中にハングする

インターネット ブラウザーのページがハングする場合、Azure portal にアクセスできるまで、次の各手順を試してみてください。

- ページを更新します。
- 別のインターネット ブラウザーを使用します。
- お使いのブラウザーのプライベート ブラウズ モードを使用します。 Internet Explorer の場合、 **[ツール]**  >  **[安全性]**  >  **[InPrivate ブラウズ]** をクリックし、[Azure portal](https://portal.azure.com/) または [Azure アカウント センター](https://account.azure.com/Subscriptions)にアクセスしてサインインします。

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>サインイン時に自動で別のユーザーになる

この問題は、インターネット ブラウザーで複数のユーザー アカウントを使用している場合に発生する可能性があります。

この問題を解決するには、次の方法のいずれかを試してください。

- キャッシュをクリアし、インターネット Cookie を削除する。 Internet Explorer で、 **[ツール]**  >  **[インターネット オプション]**  >  **[削除]** の順にクリックします。 一時ファイル、Cookie、パスワード、閲覧履歴の各チェック ボックスをオンにして [削除] をクリックします。
- Internet Explorer の設定をリセットして、構成済みの個人設定を元に戻す。 **[ツール]**  >  **[インターネット オプション]**  >  **[詳細設定]** の順にクリックし、 **[個人設定を削除する]** ボックス、 **[リセット]** の順に選択します。
- お使いのブラウザーのプライベート ブラウズ モードを使用します。 Internet Explorer の場合、 **[ツール]**  >  **[安全性]**  >  **[InPrivate ブラウズ]** をクリックし、[Azure portal](https://portal.azure.com/) または [Azure アカウント センター](https://account.azure.com/Subscriptions)にアクセスしてサインインします。

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>サインインすることはできるが、"*サブスクリプション見つかりませんでした*" と表示される

この問題は、誤ったディレクトリで選択した場合、または、自分のアカウントに十分なアクセス許可がない場合に発生します。

**シナリオ 1:** [Azure portal](https://portal.azure.com/) でエラー メッセージが表示される

この問題を解決するには、次の手順に従います。

- 右上にある自分のアカウントをクリックして、適切な Azure ディレクトリが選択されていることを確認します。
- 適切な Azure ディレクトリが選択されていてもエラー メッセージが表示される場合は、[アカウントを所有者として追加](billing-add-change-azure-subscription-administrator.md)します。

**シナリオ 2:** [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)でエラー メッセージが表示される

使用しているアカウントがアカウント管理者であるかどうかを確認します。 どのアカウントがアカウント管理者であるかを確認するには、以下の手順に従ってください。

1. Azure Portal の[サブスクリプション ビュー](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にサインインします。

2. 確認するサブスクリプションを選択し、 **[設定]** を調べます。

3. **[プロパティ]** を選択します。 サブスクリプションのアカウント管理者が、 **[アカウント管理者]** ボックスに表示されます。

## <a name="additional-help-resources"></a>その他のヘルプ リソース

Azure の請求とサブスクリプションに関するその他のトラブルシューティング記事

- [拒否されたカード](billing-troubleshoot-declined-card.md)
- [サブスクリプションのサインアップの問題](billing-troubleshoot-azure-sign-up.md)
- [サブスクリプションが見つからない](billing-no-subscriptions-found.md)
- [Enterprise コスト ビューが無効になっている](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次の手順

- [Azure の課金に関するドキュメント](index.md)
