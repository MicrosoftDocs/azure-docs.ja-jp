---
title: Azure のサインアップのトラブルシューティング
description: Microsoft Azure portal アカウント センターで新しいアカウントにサインアップしようとしたときに発生する問題を解決します。
services: billing
author: v-miegge
manager: dcscontentpm
editor: v-jesits
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: f1414f96a00626b8e47decd22e4560bff1394468
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872725"
---
# <a name="troubleshoot-azure-sign-up"></a>Azure のサインアップのトラブルシューティング

Microsoft Azure portal または Azure アカウント センターで新しいアカウントにサインアップしようとすると、問題が発生する場合があります。 問題のトラブルシューティングを行う前に、まず次のことを確認してください。

- Azure アカウント プロファイルに指定した情報 (連絡先のメール アドレス、住所、電話番号など) が正しいこと。
- クレジット カード情報が正しいこと。
- 同じ情報を持つ既存の Microsoft アカウントをがないこと。

## <a name="resolutions"></a>解決策

エラーを解決するには、Azure へのサインアップを試みたときに発生した問題を選択してください。

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>エラー:"*アカウントに問題が発生したため、サインアップを続行できません。課金サポートにお問い合わせください。* "

この問題を解決するには、次の手順に従ってください。

1. アカウント管理者の資格情報を使用して、アカウント管理者として [Azure アカウント センター](https://account.azure.com/Profile)にログインします。

2. **[詳細の編集]** を選択します。

3. すべてのアドレス フィールドが完成し、有効になっていることを確認します。

4. Azure サブスクリプションにサインアップするときに、クレジット カード登録の請求先住所が銀行のレコードと一致していることを確認します。

それでもエラー メッセージが表示される場合は、別のブラウザーを使用してサインアップを試してください。

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>*[カードによる本人確認]* セクションで進行状況バーが停止する。

カードによる本人確認を完了するには、ブラウザーでサードパーティの Cookie を許可する必要があります。

![カードによる本人確認](./media/billing-troubleshoot-azure-sign-up/identify-verification-by-card.png)
 
次の手順に従って、ブラウザーの Cookie の設定を更新します。

1. Chrome を使用している場合は、 **[設定]**  >  **[詳細設定を表示]**  >  **[プライバシー]**  >  **[コンテンツの設定]** の順に選択し、 **[サードバーティの Cookie とサイト データをブロックする]** をオフにします。

2. Microsoft Edge を使用している場合は、 **[設定]**  >  **[詳細設定を表示]**  >  **[Cookie]**  >  **[Cookie をブロックしない]** の順に選択します。

3. Azure のサインアップ ページを更新し、問題が解決したかどうかを確認します。

4. 更新しても問題が解決しなかった場合は、ブラウザーを終了して再起動した後、やり直してください。

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>請求先住所がクレジット カード フォームでサポートされていない

請求先住所は、 **[自分の情報]** セクションで選択した国である必要があります。 正しい国を選択していることを確認してください。

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>サインアップ アカウントの検証中に、テキスト メッセージの送信がなく、電話もかかってこない

通常、このプロセスにさほど時間はかかりませんが、確認コードが配信されるまでに、最大で 4 分かかる場合があります。 確認のために入力した電話番号は、アカウントの連絡先の電話番号としては保存されません。

いくつかの追加のヒントを以下に示します。

- 電話確認処理に VoiP (ボイス オーバー IP) の電話番号を使用することはできません。
- ドロップダウン メニューで選択した国番号を含め、入力する電話番号を再確認してください。
- テキスト メッセージ (SMS) を受信しない電話の場合は、 **[電話で確認コードを受け取る]** をオプションを試してください。
- お使いの電話で米国の電話番号からの電話または SMS メッセージを受信できることを確認してください。

テキスト メッセージを受信するか、電話がかかってきたら、テキスト ボックスに受け取ったコードを入力してください。

### <a name="credit-card-declined-or-not-accepted"></a>クレジット カードが拒否されます

バーチャルまたはプリペイド式のクレジット カードやデビット カードは、Azure サブスクリプションの支払い方法として認められていません。 カードが拒否される他の原因を確認するには、[Azure へのサインアップ時にデビット カードまたはクレジット カードが拒否される問題](https://support.microsoft.com/help/4042960)に関するページを参照してください。

### <a name="free-trial-is-not-available"></a>無料試用版は使用できません

過去に Azure サブスクリプションを使用していませんか。 Azure 使用条件契約書では、Azure を初めて使用するユーザーのみが Azure 無料試用版をアクティブ化できるという制限を設けています。 他の種類の Azure サブスクリプションを持っている場合も、無料試用版をアクティブ化することはできません。 [従量課金制サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0003p/)へのサインアップを検討してください。

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>無料試用版アカウントに料金が表示されている

サインアップ後にお客様のクレジット カード アカウントに少額の承認保留が表示されることがあります。 これは 3 日から 5 日以内に削除されます。 コストの管理についての不安がある場合は、[予想外のコストを防ぐ](billing-getting-started.md)方法に関する記事をご覧ください。

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>MSDN、BizSpark、BizSparkPlus、MPN などの Azure 特典プランをアクティブ化できません

サインイン資格情報が正しいことを確認したうえで、 特典プログラムを使用する資格があることを確認します。

- MSDN 
  - [MSDN アカウント ページ](https://msdn.microsoft.com/subscriptions/manage/default.aspx)で資格の状態を確認します。
  - 状態を確認できない場合は、[MSDN サブスクリプション カスタマー サービス センター](https://msdn.microsoft.com/library/aa493452.aspx)にお問い合わせください。
- Microsoft for Startups
  - [Microsoft for Startups ポータル](https://startups.microsoft.com/#start-two)にサインインして、Microsoft for Startups の資格の状態を確認します。
  - 状態を確認できない場合は、[Microsoft for Startups フォーラム](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups)で質問してください。
- MPN 
  - [MPN ポータル](https://mspartner.microsoft.com/Pages/Locale.aspx)にサインインし、資格の状態を確認します。 該当する [クラウド プラットフォーム コンピテンシー](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx)をお持ちの場合、追加特典の資格を与えられることがあります。
  - 状態を確認できない場合は、[MPN サポート](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx)にお問い合わせください。

### <a name="cant-activate-new-azure-in-open-subscription"></a>新しい Azure イン オープン プラン サブスクリプションをアクティブ化できません

Azure イン オープン プランのサブスクリプションを作成するには、少なくとも 1 つの Azure イン オープン プラン トークンが関連付けられている、有効なオンライン サービスのライセンス認証 (OSA) キーが必要です。 OSA キーを持っていない場合は、[Microsoft Pinpoint](https://pinpoint.microsoft.com/) に記載されている Microsoft パートナーにお問い合わせください。

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>エラー:"*Azure サブスクリプションの資格がありません*"

この問題を解決するには、次の項目が当てはまるかどうかを再確認してください。

- Azure アカウント プロファイルに指定した情報 (連絡先のメール アドレス、住所、電話番号など) が正しいこと。
- クレジット カード情報が正しいこと。
- 同じ情報を使用する既存の Microsoft アカウントをがないこと。

### <a name="error-your-current-account-type-is-not-supported"></a>エラー:"*現在のアカウントの種類はサポートされていません*"

この問題は、[アンマネージドの Azure AD ディレクトリ](../active-directory/users-groups-roles/directory-self-service-signup.md)にアカウントが登録されているために、組織の Azure AD ディレクトリにはアカウントが存在しない場合に発生します。 

この問題を解決するには、別のアカウントを使用して Azure アカウントにサインアップするか、アンマネージド AD ディレクトリの引き継ぎを行ってください。 詳細については、「[Azure Active Directory の非管理対象ディレクトリを管理者として引き継ぐ](../active-directory/users-groups-roles/domains-admin-takeover.md)」を参照してください。
 
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
