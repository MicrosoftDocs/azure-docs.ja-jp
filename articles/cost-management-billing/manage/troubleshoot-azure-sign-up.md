---
title: Azure portal または Azure アカウント センターで新しいアカウントをサインアップするときの問題をトラブルシューティングする
description: Microsoft Azure portal アカウント センターで新しいアカウントにサインアップしようとしたときに発生する問題を解決します。
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: v-miegge
ms.openlocfilehash: 86f9f92810cff241f816dadfc6e1e8ca120f1f4a
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686651"
---
# <a name="troubleshoot-issues-when-you-sign-up-for-a-new-account-in-azure-portal-or-azure-account-center"></a>Azure portal または Azure アカウント センターで新しいアカウントをサインアップするときの問題をトラブルシューティングする

Microsoft Azure portal または Azure アカウント センターで新しいアカウントにサインアップしようとすると、問題が発生する場合があります。 この短いガイドでは、サインアップ プロセスの手順と、各ステップでの一般的な問題について説明します。

> [!NOTE]
> 既にアカウントがあって、サインインの問題をトラブルシューティングするためのガイダンスをお探しの場合は、「[Azure サブスクリプションのサインインに関する問題のトラブルシューティング](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue)」を参照してください。

## <a name="before-you-begin"></a>開始する前に

サインアップを始める前に、次の項目を確認します。

- Azure アカウント プロファイルの情報 (連絡先のメール アドレス、住所、電話番号など) が正しいこと。
- クレジット カード情報が正しいこと。
- 同じ情報を持つ既存の Microsoft アカウントをがないこと。

## <a name="guided-walkthrough-of-azure-sign-up"></a>Azure サインアップのガイド付きチュートリアル

Azure のサインアップ エクスペリエンスは、次の 4 つのセクションで構成されています。

- 自分の情報
- 電話による本人確認
- カードによる本人確認
- 契約

このチュートリアルでは、Azure アカウントにサインアップするための正しい情報の例を示します。 各セクションでは、一般的な問題とその解決方法も含まれます。

## <a name="about-you"></a>自分の情報

![自分の情報](./media/troubleshoot-azure-sign-up/1.png)
 
### <a name="common-issues-and-solutions"></a>一般的な問題と解決方法

#### <a name="you-see-the-message-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>次のようなメッセージが表示される: "アカウントに問題が発生したため、サインアップを続行できません。 課金サポートにお問い合わせください"

このエラーを解決するには、次の手順のようにします。

1.  アカウント管理者の資格情報を使用して、アカウント管理者として [Azure アカウント センター](https://account.azure.com/Profile)にログインします。
1.  **[詳細の編集]** を選択します。
1.  すべてのアドレス フィールドが完成し、有効になっていることを確認します。
1.  Azure サブスクリプションにサインアップするときに、クレジット カード登録の請求先住所が銀行の登録情報と一致していることを確認します。

それでもメッセージが表示される場合は、別のブラウザーを使用してサインアップを試してください。

InPrivate ブラウズはどうですか。

#### <a name="free-trial-is-not-available"></a>無料試用版が利用できない

過去に Azure サブスクリプションを使用していませんか。 Azure 使用条件契約書では、Azure を初めて使用するユーザーのみが Azure 無料試用版をアクティブ化できるという制限を設けています。 他の種類の Azure サブスクリプションを持っている場合も、無料試用版をアクティブ化することはできません。 [従量課金制サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0003p/)へのサインアップを検討してください。

#### <a name="you-see-the-message-you-are-not-eligible-for-an-azure-subscription"></a>次のようなメッセージが表示される: "Azure サブスクリプションの資格がありません"

この問題を解決するには、次の項目が当てはまるかどうかを再確認してください。

- Azure アカウント プロファイルに指定した情報 (連絡先のメール アドレス、住所、電話番号など) が正しいこと。
- クレジット カード情報が正しいこと。
- 同じ情報を使用する既存の Microsoft アカウントがないこと。

#### <a name="you-see-the-message-your-current-account-type-is-not-supported"></a>次のようなメッセージが表示される: "現在のアカウントの種類はサポートされていません"

この問題は、[アンマネージドの Azure AD ディレクトリ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup)にアカウントが登録されているために、組織の Azure AD ディレクトリにはアカウントが存在しない場合に発生します。
この問題を解決するには、別のアカウントを使用して Azure アカウントにサインアップするか、アンマネージド AD ディレクトリの引き継ぎを行ってください。 詳細については、「[Azure Active Directory の非管理対象ディレクトリを管理者として引き継ぐ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover)」を参照してください。

## <a name="identity-verification-by-phone"></a>電話による本人確認

![電話による本人確認](./media/troubleshoot-azure-sign-up/2.png)
 
テキスト メッセージを受信するか、電話がかかってきたら、テキスト ボックスに受け取ったコードを入力してください。

### <a name="common-issues-and-solutions"></a>一般的な問題と解決方法

#### <a name="no-verification-text-message-or-phone-call"></a>確認のためのテキスト メッセージまたは電話がない

通常、サインアップ確認プロセスにさほど時間はかかりませんが、確認コードが配信されるまでに、最大で 4 分かかる場合があります。

いくつかの追加のヒントを以下に示します。

- 要件を満たしている限り、任意の電話番号を確認に使用できます。 確認のために入力した電話番号は、アカウントの連絡先の電話番号としては保存されません。
  - 電話確認処理に VoiP (ボイス オーバー IP) の電話番号を使用することはできません。
  - お使いの電話で米国の電話番号からの電話または SMS メッセージを受信できることを確認してください。
- ドロップダウン メニューで選択した国番号を含め、入力する電話番号を再確認してください。
- テキスト メッセージ (SMS) を受信しない電話の場合は、 **[電話で確認コードを受け取る]** をオプションを試してください。

## <a name="identity-verification-by-card"></a>カードによる本人確認

![カードによる本人確認](./media/troubleshoot-azure-sign-up/3.png)
 
### <a name="common-issues-and-solutions"></a>一般的な問題と解決方法

#### <a name="credit-card-declined-or-not-accepted"></a>クレジット カードが拒否されます

バーチャルまたはプリペイド式のクレジット カードやデビット カードは、Azure サブスクリプションの支払い方法として認められていません。 カードが拒否される他の原因を確認するには、「[Azure へのサインアップ時にカードが拒否される問題のトラブルシューティング](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)」を参照してください。

#### <a name="credit-card-form-doesnt-support-my-billing-address"></a>請求先住所がクレジット カード フォームでサポートされていない

請求先住所は、 **[自分の情報]** セクションで選択した国である必要があります。 正しい国を選択したことを確認します。

#### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>カードによる本人確認セクションで進行状況バーが停止する

カードによる本人確認を完了するには、ブラウザーでサードパーティの Cookie を許可する必要があります。

次の手順に従って、ブラウザーの Cookie の設定を更新します。

1. Cookie の設定を更新します。
   - **Chrome** を使用している場合:
     - **[設定]**  >  **[詳細設定を表示]**  >  **[プライバシー]**  >  **[コンテンツの設定]** を選択します。 **[サードバーティの Cookie とサイト データをブロックする]** をオフにします。

   - **Microsoft Edge** を使用している場合:
     - **[設定]**  >  **[詳細設定を表示]**  >  **[Cookie]**  >  **[Cookie をブロックしない]** を選択します。

1. Azure のサインアップ ページを更新し、問題が解決したかどうかを確認します。
1. 更新しても問題が解決しなかった場合は、ブラウザーを終了して再起動した後、やり直してください。

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>無料試用版アカウントに料金が表示されている

サインアップ後にお客様のクレジット カード アカウントに少額の承認保留が一時的に表示されることがあります。 この保留は 3 日から 5 日以内に削除されます。 コストの管理についての不安がある場合は、[予想外のコストを防ぐ](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started)方法に関する記事をご覧ください。

## <a name="agreement"></a>契約

契約を完了します。

## <a name="other-issues"></a>その他の問題

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>MSDN、BizSpark、BizSparkPlus、MPN などの Azure 特典プランをアクティブ化できません

サインイン資格情報が正しいことを確認します。 次に、特典プログラムを使用する資格があることを確認します。
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

## <a name="additional-help-resources"></a>その他のヘルプ リソース

Azure の請求とサブスクリプションに関するその他のトラブルシューティング記事

- [拒否されたカード](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [サブスクリプションのサインインの問題](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue)
- [サブスクリプションが見つからない](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [Enterprise コスト ビューが無効になっている](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)

## <a name="contact-us-for-help"></a>お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="find-out-more-about-azure-cost-management"></a>Azure Cost Management の詳細を探す

- [Azure Cost Management および Billing のドキュメント](https://docs.microsoft.com/azure/cost-management-billing)
