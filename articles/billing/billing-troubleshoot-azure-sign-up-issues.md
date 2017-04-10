---
title: "Azure のサインアップに関する問題のトラブルシューティング | Microsoft Docs"
description: "Azure のサインアップに関する一般的な問題をトラブルシューティングする方法について説明します。"
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: a0907da1-cb2d-41d1-a97f-43841fabe355
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: cjiang
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: a50b4fb4b19acf270aaf94fea613e745ec2af649
ms.lasthandoff: 03/22/2017

---
# <a name="troubleshoot-sign-up-issues-for-azure"></a>Azure のサインアップに関する問題のトラブルシューティング
Azure にサインアップできない場合は、この記事の情報を使用して、一般的な問題のトラブルシューティングを行ってください。 サインアップ時にクレジット カードに関する問題が発生した場合は、「[Azure へのサインアップ時にデビット カードまたはクレジット カードが拒否される](billing-credit-card-fails-during-azure-sign-up.md)」を参照してください。 ご自身の Azure アカウントでサインインできない場合は、「[Azure サブスクリプションにサインインして管理することができない](billing-cannot-login-subscription.md)」を参照してください。

## <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>[カードによる本人確認] セクションで進行状況バーが停止する

カードによる本人確認を完了するには、ブラウザーでサードパーティの Cookie を許可する必要があります。

![サインアップ中に停止した [カードによる本人確認] セクションのスクリーンショット](./media/billing-troubleshoot-azure-sign-up-issues/identity-verification-hangs.PNG)

次の手順に従って、ブラウザーの Cookie の設定を更新します。

1. Chrome を使用している場合は、**[設定]**  >  **[詳細設定を表示]**  >  **[プライバシー]**  >  **[コンテンツの設定]** の順に選択し、 **[サードバーティの Cookie とサイト データをブロックする]** をオフにします。
2. Edge を使用している場合は、**[設定]**  >  **[詳細設定を表示]**  >  **[Cookie]** の順に選択し、 **[Cookie をブロックしない]** を選択します。
3. Azure のサインアップ ページを更新し、問題が解決したかどうかを確認します。
4. 更新しても問題が解決しなかった場合は、ブラウザーを終了して再起動した後、やり直してください。

## <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>サインアップ アカウントの検証中に、テキスト メッセージの送信がなく、電話もかかってこない
**[テキスト メッセージを受信]** を選択した場合、テキスト コードが電話に届くまでに最大で 4 分かかる場合があります。 言うまでもありませんが、本人確認のために、SMS メッセージを受信できる、または電話を受けることができる (**[電話で確認コードを受け取る]** オプションを選択した場合) 電話番号を入力します。 入力した電話番号は本人確認の目的でのみ使用されます。アカウントの連絡先の電話番号としては保存されません。

いくつかのヒントを次に示します。
* 電話確認処理に VoIP 電話番号を使用することはできません。
* ドロップダウン メニューで選択した国コードを含め、入力する電話番号を再確認してください。
* テキスト メッセージ (SMS) を受信しない電話の場合は、**[電話で確認コードを受け取る]** をオプションを使用します。
* **[テキスト メッセージを受信]** と **[電話で確認コードを受け取る]** のどちらを使っても本人確認ができなかった場合は、別の電話番号を使用してください。

テキスト メッセージを受信した場合、または電話がかかってきたら、テキスト ボックスに受け取ったコードを入力してください。

## <a name="credit-card-declined-or-not-accepted"></a>クレジット カードが拒否されます
バーチャルまたはプリペイド式のクレジット カードやデビット カードは、Azure サブスクリプションの支払い方法として認められていません。 カードが拒否される他の原因を確認するには、「[Azure へのサインアップ時にデビット カードまたはクレジット カードが拒否される](billing-credit-card-fails-during-azure-sign-up.md)」を参照してください。

## <a name="free-trial-is-not-available"></a>"無料試用版は使用できません"
過去に Azure サブスクリプションを使用していませんか。 Azure 使用条件契約書では、Azure を初めて使用するユーザーのみが Azure 無料試用版をアクティブ化できるという制限を設けています。 他の種類の Azure サブスクリプションを持っている場合も、無料試用版をアクティブ化することはできません。 [従量課金のサブスクリプション](https://azure.microsoft.com/offers/ms-azr-0003p/)へのサインアップを検討してください。

## <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>MSDN、BizSpark、BizSparkPlus、MPN などの Azure 特典プランをアクティブ化できません
サインイン資格情報が正しいことを確認したうえで、 特典プログラムを使用する資格があることを確認します。 

* MSDN
  * [MSDN アカウント ページ](https://msdn.microsoft.com/subscriptions/manage/default.aspx)で資格の状態を確認します。
  * 状態を確認できない場合は、[MSDN サブスクリプション カスタマー サービス センター](https://msdn.microsoft.com/subscriptions/contactus.aspx)にお問い合わせください
* BizSpark
  * [BizSpark ポータル](https://www.microsoft.com/bizspark/default.aspx#start-two) にサインインし、BizSpark と BizSpark Plus の資格の状態を確認します。
  * 状態を確認できない場合は、[BizSpark チームにお問い合わせ](mailto:bizspark@microsoft.com?subject=BizSpark%20Support&body=Thank%20you%20for%20contacting%20BizSpark.%20Please%20provide%20as%20much%20of%20the%20following%20information%20as%20possible,%20as%20it%20will%20help%20expedite%20our%20response%20to%20you.%0aContact%20name:%0aStartup%20name:%0aMicrosoft%20Account/Live%20ID:%0aSpecific%20description%20of%20issue%20experienced%20or%20question:%0a%0aThank%20you,%0a%0aThe%20BizSpark%20Team)ください
* MPN
  * [MPN ポータル](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx) にサインインし、資格の状態を確認します。 該当する [クラウド プラットフォーム コンピテンシー](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx)をお持ちの場合、追加特典の資格を与えられることがあります。
  * 状態を確認できない場合は、[MPN サポート](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx)にお問い合わせください。

## <a name="cant-activate-new-azure-in-open-subscription"></a>新しい Azure イン オープン プラン サブスクリプションをアクティブ化できません
Azure イン オープン プランのサブスクリプションを作成するには、少なくとも 1 つの Azure イン オープン プラン トークンが関連付けられている、有効なオンライン サービスのライセンス認証 (OSA) キーが必要です。 OSA キーを持っていない場合は、[Microsoft Pinpoint](http://pinpoint.microsoft.com/) に記載されている Microsoft パートナーにお問い合わせください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

