---
title: パスワードのリセット - Azure Active Directory | Microsoft Docs
description: セルフサービスによるパスワードのリセットを使用して職場または学校のユーザー アカウントへのアクセスを回復する
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.custom: end-user
ms.collection: M365-identity-device-management
ms.openlocfilehash: d16b54df5b8392a294036978e0b917ecdb3ce1ca
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56184246"
---
# <a name="reset-your-work-or-school-password"></a>職場または学校のパスワードをリセットする

パスワードを忘れた場合や、会社のサポート部門からパスワードを受け取っていない場合、アカウントからロックアウトされた場合、パスワードを変更する場合は、Microsoft がお手伝いします。 パスワードがわかっていて、単にパスワードの変更が必要な場合は、「[パスワードを変更する](#change-my-password)」セクションに進んでください。

   > [!NOTE]
   > Xbox、hotmail.com、outlook.com などの個人アカウントを回復しようとしている場合は、「[Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)」の記事内の提案事項を試してください。
   >

## <a name="reset-or-unlock-my-password-for-a-work-or-school-account"></a>職場または学校アカウントのパスワードをリセットまたはロック解除する

次のいずれかの理由で、ご自分の Azure Active Directory (Azure AD) アカウントにアクセスできない場合があります。

* パスワードが機能しておらず、リセットしたい。
* パスワードはわかっているが、アカウントがロックされており、ロックを解除したい。

Azure AD のセルフサービスによるパスワードのリセット (SSPR) にアクセスしてご自分のアカウントを回復するには、次の手順を使用します。

1. 職場または学校の **[サインイン]** ページから **[アカウントにアクセスできませんか?]** を選択し、**[職場または学校アカウント]** を選択するか、[パスワードのリセット用ページ](https://passwordreset.microsoftonline.com/)に直接アクセスします。

    ![アカウントにアクセスできない場合][Login]

2. 職場または学校の**ユーザー ID** を入力し、画面に表示された文字を入力して自分がロボットではないことを証明し、**[次へ]** を選択します。

   > [!NOTE]
   > この機能が IT スタッフによって有効にされていない場合は、IT スタッフがメールや自分の Web ポータルを通じてサポートできるように、[管理者に連絡してください] というリンクが表示されます。
   >
   > アカウントのロックを解除する必要がある場合は、この時点で、**[パスワードは覚えていますが、サインインできません]** オプションを選択します。
   >

3. IT スタッフによる SSPR の構成内容に応じて、以下の認証方法のうち 1 つ以上が表示されます。 自分または IT スタッフが、「[セルフサービスによるパスワードのリセットを登録する](active-directory-passwords-reset-register.md)」の記事で示されている手順に従って、この情報の一部を設定しているはずです。

   * **連絡用電子メール アドレスにメールを送信**
   * **携帯電話に SMS 送信**
   * **携帯電話に発信**
   * **会社電話に発信**
   * **セキュリティの質問に回答する**

   オプションを選択し、正しい回答を入力してから、**[次へ]** を選択します。

   ![認証データを確認する][Verification]

4. IT スタッフが追加の確認を求めている場合もあります。その場合は、別のオプションを選択して手順 3. を繰り返す必要があります。
5. **[新しいパスワードの選択]** ページで、新しいパスワードを入力し、パスワードを確認してから、**[完了]** を選択します。 職場または学校のパスワードは、特定の要件に従う必要があります。 8 - 16 文字の、大文字と小文字、数字、特殊文字が含まれているパスワードにすることをお勧めします。
6. **[パスワードがリセットされました]** というメッセージが表示されたら、新しいパスワードでサインインできます。

    ![パスワードがリセットされました][Complete]

これで、アカウントにアクセスできるようになりました。 アカウントにアクセスできない場合は、ご自分の組織の IT スタッフに連絡してサポートを依頼してください。

"Microsoft (\<組織> の代理)" などのアカウントから送信された確認メールを受け取ることがあります。 セルフサービスによるパスワードのリセットを利用してアカウントへのアクセスを回復した覚えがないのに、このようなメールが届いた場合は、ご自分の組織の IT スタッフに問い合わせてください。

## <a name="change-my-password"></a>パスワードを変更する

パスワードを既に知っており、変更する必要がある場合は、以下の手順に従います。

### <a name="change-your-password-from-the-office-365-portal"></a>Office 365 ポータルからパスワードを変更する

通常は Office ポータルを使用してアプリケーションにアクセスしている場合は、次の方法を使用します。

1. 既存のパスワードを使用して [Office 365 アカウント](https://www.office.com)にサインインします。
2. 右上にある自分のプロファイルを選択してから、**[アカウントを表示]** を選択します。
3. **[セキュリティとプライバシー]** > **[パスワード]** の順に選択します。
4. 古いパスワードを入力し、新しいパスワードを設定して、そのパスワードを確認します。その後、**[送信]** を選択します。

### <a name="change-your-password-from-the-azure-access-panel"></a>Azure アクセス パネルからパスワードを変更する

通常は Azure アクセス パネル (MyApps) を使用してアプリケーションにアクセスしている場合は、次の方法を使用します。

1. 既存のパスワードを使用して [Azure アクセス パネル](https://myapps.microsoft.com/)にサインインします。
2. 右上にある自分のプロファイルを選択してから、**[プロファイル]** を選択します。
3. **[パスワードの変更]** を選択します。
4. 古いパスワードを入力し、新しいパスワードを設定して、そのパスワードを確認します。その後、**[送信]** を選択します。

## <a name="reset-password-at-sign-in"></a>サインイン時にパスワードをリセットする

管理者が機能を有効にしている場合、Windows 10 Fall Creators Update のサインイン画面に **[パスワードのリセット]** へのリンクが表示されるようになりました。

![サインイン画面][LoginScreen]

**[パスワードのリセット]** を選択すると、サインイン画面に SSPR エクスペリエンスが開き、サインインして通常の Web ベースのエクスペリエンスにアクセスしなくても、パスワードをリセットできます。

1. ユーザー ID を確認して **[次へ]** を選択します。
2. 確認用の連絡方法を選択して確定します。 IT スタッフが追加の確認を求めている場合もあります。その場合は、別のオプションを選択してこの手順を繰り返す必要があります。

   ![連絡方法][ContactMethod]

3. **[新しいパスワードの作成]** ページで、新しいパスワードを入力し、パスワードを確認してから、**[次へ]** を選択します。 大文字と小文字、数字、特殊文字を組み合わせた 8 - 16 文字のパスワードにすることをお勧めします。

   ![パスワードのリセット][ResetPassword]

4. **[パスワードがリセットされました]** というメッセージが表示されたら、**[完了]** を選択します。

これで、アカウントにアクセスできるようになりました。 できない場合は、ご自分の組織の IT スタッフに連絡してサポートを依頼してください。

## <a name="common-problems-and-their-solutions"></a>一般的な問題とその解決方法

 次に、一般にエラーになる場合とその解決方法を示します。

| エラー ケース| 表示されるエラー| 解決策 |
| --- | --- | --- |
| パスワードを変更しようとするとエラーが表示されます。 | 残念ながら、パスワードを簡単に推測できる単語、語句、またはパターンが含まれています。 別のパスワードで再実行してください。 | 推測されにくいパスワードを選択してください。 |
| ユーザー ID を入力した後に [管理者にお問い合わせください] というページが表示されます | 管理者にお問い合わせください。 <br> <br> お客様のユーザー アカウントのパスワードは Microsoft によって管理されていません。 そのため、パスワードを自動的にリセットすることはできません。 <br> <br> IT スタッフに問い合わせてサポートを受ける必要があります。 | IT スタッフがお客様のパスワードをオンプレミス環境で管理しているため、このメッセージが表示されます。 [アカウントにアクセスできません] というリンクから、パスワードをリセットできます。 <br> <br> 自分のパスワードをリセットするには、IT スタッフに直接連絡して支援を依頼し、パスワードをリセットしたいことを伝え、この機能を有効にしてもらう必要があります。|
| ユーザー ID の入力後に [アカウントは、パスワード リセットが有効になっていません。] というエラーが表示されます | アカウントは、パスワード リセットが有効になっていません。 <br> <br> 申し訳ありませんが、IT スタッフはお客様のアカウントでこのサービスを使用できるように設定していません。 <br> <br> ご希望の場合は、こちらで組織の管理者に連絡してパスワードをリセットできます。 | これは IT スタッフが [アカウントにアクセスできません] というリンクからのパスワードのリセットを有効にしていないため、またはその機能を使用するライセンスを付与していないために表示されます。 <br> <br> 自分のパスワードをリセットするには、[contact an administrator]\(管理者に連絡\) を選択して会社の IT スタッフにメールを送信し、パスワードをリセットしたいことを伝え、この機能を有効にしてもらう必要があります。 |
| ユーザー ID の入力後に [お客様のアカウントを確認できませんでした] というエラーが表示されます | お客様のアカウントを確認できませんでした。 <br> <br> ご希望の場合は、こちらで組織の管理者に連絡してパスワードをリセットできます。 | このメッセージは、パスワードのリセットは有効になっていても、サービスを使用するための登録を行っていない場合に表示されます。 パスワードのリセットを登録するには、アカウントに再アクセスできるようになった後に https://aka.ms/ssprsetup に進みます。 <br> <br> 自分のパスワードをリセットするには、[contact an administrator]\(管理者に連絡\) を選択し、会社の IT スタッフにメールを送信します。 |

## <a name="next-steps"></a>次の手順

* [セルフサービスによるパスワードのリセットを使用するための登録方法](active-directory-passwords-reset-register.md)
* [パスワードのリセット登録ページ](https://aka.ms/ssprsetup)
* [パスワードのリセット用ポータル](https://passwordreset.microsoftonline.com/)
* [Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "ログイン ページ: アカウントにアクセスできませんか?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "認証データを確認する"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "パスワードを変更する"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "パスワードがリセットされました"
[LoginScreen]: ./media/active-directory-passwords-update-your-own-password/login-screen.png "Windows 10 Fall Creators Update のログイン画面の [パスワードのリセット] リンク"
[ContactMethod]: ./media/active-directory-passwords-update-your-own-password/reset-contact-method-screen.png "認証データを確認する"
[ResetPassword]: ./media/active-directory-passwords-update-your-own-password/reset-password-screen.png "パスワードを変更する"
