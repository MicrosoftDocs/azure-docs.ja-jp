---
title: "Publisher サポートに関する一般的な問題をトラブルシューティングする方法 | Microsoft Docs"
description: "Publisher サポートに関する一般的な問題をトラブルシューティングする方法、サポートを得る方法を説明します"
services: marketplace-publishing
documentationcenter: na
author: v-jeana
manager: lakoch
editor: 
ms.assetid: f8465448-8328-41f3-8288-48c1c4098134
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2016
ms.author: v-jeana; hascipio; v-dabosl
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 11b5fb0ed1b3939d8278156fcf2c831befc5f7ff


---
# <a name="how-to-troubleshoot-common-azure-marketplace-publisher-issues"></a>Azure Marketplace Publisher に関する一般的な問題をトラブルシューティングする方法
この記事では、いくつかの一般的な Marketplace のシナリオのサポートを得たり、これに関する問題を解決したりする方法を説明します。

## <a name="credits-and-refunds"></a>クレジットと返金
状況によっては、顧客に代わるクレジットまたは返金の要求が必要になる場合があります。  Microsoft サポートでは、貴社のお客様のクレジットまたは返金の要求が検討され、やがてその対応が提供されるよう支援します。

顧客に代わりクレジットまたは返金を要求する方法を次に示します。

> [!NOTE]
> 顧客または Azure リセラーから、返金またはクレジットの要求が Microsoft 宛になされた場合、Azure サービスの提供で使用するのと同じポリシーが適用されます。 それらのポリシーで返金またはクレジットが指示される場合、弊社は事前に振り出して、発行元は Microsoft が発行元に代わって行う顧客または Azure リセラー宛ての返金またはクレジットを承認することになります。 ポリシーで返金もクレジットも指示されない場合、顧客または Azure リセラーに対して、直接発行元に連絡を取るように通知する場合があります。
> 
> 

  ![1_RequestRefund][1]

1. サポート ページ ([https://support.microsoft.com/en-us/getsupport?tenant=ClassicCommercial&locale=en-us&supportregion=en-us&pesid=15635&sd=&oaspworkflow=start_1.0.0.0&wf=0&ccsid=635562546535117884](https://support.microsoft.com/en-us/getsupport?tenant=ClassicCommercial&locale=en-us&supportregion=en-us&pesid=15635&sd=&oaspworkflow=start_1.0.0.0&wf=0&ccsid=635562546535117884)) に移動します。
2. **[問題の種類]** ボックスで、**[顧客の管理]** を選択します。
3. **[カテゴリ]** ボックスで、**[お客様への返金の承認または要求]** を選択します。
4. **[要求の開始]**をクリックします。
   
   ![2_RequestRefund][2]

1. 連絡先情報を提供する。
2. **[どんな問題ですか?]** ボックスに、次の情報を入力します。
   
   * 影響を受ける顧客のサブスクリプション ID
   * 顧客名
   * クレジットの合計金額
   * クレジットの期間
   * その期間の請求日
   * クレジットの理由

その後 24 時間以内に、情報を受け取った旨の受信確認電子メールが届きます。 要求に関する決定が下されたらすぐに、その結果が通知されます。

## <a name="customer-subscription-validation"></a>顧客サブスクリプションの検証
状況によっては、貴社のいずれかのお客様のサブスクリプション詳細を検証することが必要になる場合があります。 この支援を受けるために、Microsoft サポートに問い合わせることができます。 Microsoft サポートにより、サブスクリプション ID と請求先の住所が既知の顧客に属していることを確認できます。

  ![subscriptionvalidation][3]

* 顧客からサポートを要求する問い合わせを受けたとします。
* 貴社に、その顧客がその申し出にふさわしい顧客であるか検証する術がないとします。
* 顧客サブスクリプションの検証を要求するサポート要求をログに記録できます。
* 顧客サブスクリプション ID と請求先住所を入力する必要があります。
* マイクロソフトから、請求先住所とサブスクリプション ID がマイクロソフトで把握されているものであることを伝える連絡が届きます。

## <a name="how-do-i-update-my-publisher-profile"></a>Publisher Profile を更新する方法を教えてください。
発行者プロファイルを更新するには、Microsoft デベロッパー センターを使用して以下を実行します。

**1.Windows デベロッパー センターにサインインします**。

* 新しい Chrome Incognito または Internet Explorer InPrivate ブラウズ セッションを開き、個人用アカウントにサインインしていないことを確認します。
*  [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure)

![inprivate][4]

**2.職場または学校のアカウントを使用してサインインします** (つまり AzureStore@yourcompany.com).)

![signin][5]

**3.[アカウントの保護にご協力ください] 検証画面を完了します**。

> [!NOTE]
> 電話番号または電子メール アドレスで検証できます。
> 
> 

![protectacct][6]

**4.[アカウントの詳細] と [連絡先情報] に移動します**。

![acctdetails][7]

## <a name="how-do-i-add-or-delete-an-administrator-from-my-publishing-account"></a>発行アカウントに管理者を追加、または発行アカウントから管理者を削除する方法を教えてください。
![manageadmins][8]

1. 発行ポータルに移動します。
2. **[発行元]**を選択します。
3. **[管理者]**を選択します。
4. 追加または削除する共同管理者の電子メール アドレスを追加します。
5. [ **保存**] を選択します。

## <a name="next-steps--other-support-queries"></a>次の手順: その他のサポート クエリ
次の問題については、 [https://portal.azure.com](https://portal.azure.com)に問題を記録してください。

* 課金エラーを報告する方法を教えてください。
* 課金明細や使用状況明細に不一致があるのですが。
* 課金明細の理解に役立つサポートはありますか。

次の問題については、 [http://go.microsoft.com/fwlink/?LinkId=272975](http://go.microsoft.com/fwlink/?LinkId=272975)でサポート要求を作成してください。

* Web サービスの管理に関する問題があります。
* 自分の販売者のアカウントに関連付けられている Microsoft アカウントを変更する方法を教えてください。

[1]: ./media/marketplace-publishing-support-common-issues/requestrefund1.png
[2]: ./media/marketplace-publishing-support-common-issues/requestrefund2.png
[3]: ./media/marketplace-publishing-support-common-issues/subscriptionvalidation.png
[4]: ./media/marketplace-publishing-support-common-issues/inprivate.png
[5]: ./media/marketplace-publishing-support-common-issues/signin.png
[6]: ./media/marketplace-publishing-support-common-issues/protectacct.png
[7]: ./media/marketplace-publishing-support-common-issues/acctdetails.png
[8]: ./media/marketplace-publishing-support-common-issues/manageadmins.png



<!--HONumber=Dec16_HO2-->


