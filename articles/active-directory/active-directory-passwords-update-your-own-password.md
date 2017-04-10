---
title: "Azure Active Directory を使用して自分のパスワードを更新する方法 | Microsoft Docs"
description: "パスワードのリセットのための登録方法、パスワードの変更方法、パスワードを忘れた場合のリセット方法について説明します。"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 47bd039ce61351b18e19408679b1d847cc70eef3
ms.lasthandoff: 03/28/2017


---
# <a name="update-your-own-password"></a>自分のパスワードを更新する
ここでは、職場または学校アカウントのパスワードを管理する方法について説明します。 パスワードのリセットのための登録方法、パスワードの変更方法、パスワードのリセット方法、およびアカウントのロックの解除方法について説明します。

## <a name="dont-lose-access-to-your-account"></a>引き続きアカウントにアクセスできるようにする

> [!IMPORTANT]
> **このメッセージが表示されるのはなぜですか。** リンクをたどってこのページにアクセスした場合、このメッセージが表示される可能性があります。これは、アプリにアクセスできるように、管理者によってパスワード リセットの登録が要求されるためです。 電話または電子メールの情報を求められたり、セキュリティの質問を設定するよう求められたりすることがあります。 心配はありません。 いただいた情報はお客様のアカウントのセキュリティを強化する目的でのみ使用されます。 必要な作業は、ここに記載した手順に従って実行できます。
>

パスワード リセットの登録の最も簡単な方法は、[パスワード リセット登録ページ](http://aka.ms/ssprsetup)にアクセスすることです。  

1. [パスワード リセット登録ページ](http://aka.ms/ssprsetup)に移動します。
2. ユーザー名とパスワードを入力します。
3. **[今すぐセットアップ]** をクリックして、登録オプションを選択します。 この例では、**認証用電話**を使用した登録方法を示しています。 管理者が複数のオプションの登録を求めている場合もあります。 

   ![セットアップ オプション][101]
4. ドロップダウン リストから自分の国コードを選択し、市外局番を含む完全な電話番号を入力して、**[テキスト メッセージを送信する]** または **[電話で確認コードを受け取る]** を選択します。 いずれかのオプションで、コードが提供されます。 

   ![電話検証][102]
5. 受け取ったコードを入力し、**[確認]** をクリックします。 

   ![確認コードのサンプル][103]
6. **[完了]** をクリックして、パスワードのリセットを完了します。 これで、[[ID を入力] ページ](https://passwordreset.microsoftonline.com)に移動し、登録したものを使用していつでもパスワードをリセットできるようになりました。

   ![回復情報][104]

> [!IMPORTANT]
> 管理者が複数のオプションの登録を許可している場合、携帯電話を紛失した場合や電子メールにアクセスできない場合に備えて、バックアップのオプションを登録することを強くお勧めします。
   >
   >

## <a name="change-your-password-in-office-365"></a>Office 365 でパスワードを変更する
Office 365 で職場または学校アカウントのパスワードを変更するには、次の手順に従います。 パスワードを忘れてしまって、それをリセットしたい場合は、「[パスワードをリセットする](#how-to-reset-your-password)」の手順に従います。

1. 職場または学校のアカウントを使用して Office 365 にサインインします。
2. **[設定]** > **[Office 365 設定]** > **[パスワード]** > **[パスワードの変更]** の順に進みます。
3. 古いパスワードを入力した後、新しいパスワードを入力して、それを確認します。
4. **[Save]**をクリックします。

Office 365 でのパスワードの変更の詳細については、[Office 365 ドキュメント センター](https://support.office.com/article/Change-my-password-in-Office-365-for-business-d1efbaee-63a7-4c08-ab1d-71bf932bbb5d)を参照してください。

## <a name="change-your-password-from-the-access-panel"></a>アクセス パネルからパスワードを変更する
[[アクセス パネル]](https://myapps.microsoft.com) から職場または学校アカウントのパスワードを変更するには、次の手順に従います。 パスワードを忘れてしまって、それをリセットしたい場合は、「[パスワードをリセットする](#how-to-reset-your-password)」の手順に従います。

1. 職場または学校アカウントを使用して https://myapps.microsoft.com にサインインします。
2. **[プロファイル]** タブをクリックします。 
3. **[パスワードの変更]** をクリックします。
4. 古いパスワードを入力した後、新しいパスワードを入力して、それを確認します。
5. **[Submit]**をクリックします。

パスワードの変更で問題が発生した場合は、 「[一般的な問題とその解決方法](#common-problems-and-their-solutions)」を参照してください。

## <a name="reset-your-password"></a>パスワードをリセットする
職場または学校アカウントのサインイン ページから、職場または学校アカウントのパスワードをリセットするには、次の手順に従います。

> [!IMPORTANT]
> この機能は、管理者が有効にしている場合にのみ使用できます。 有効になっていない場合は、この機能がアカウントで有効になっていないというメッセージが表示されます。 **[管理者に問い合わせてください]** のリンクを使用して、アカウントのロックを解除するよう管理者に連絡してください。
>
> 管理者がアカウントに対してこの機能を有効にしている場合は、この機能を使用する前に、サインアップする必要があります。 この操作は、[パスワード リセット登録ページ](http://aka.ms/ssprsetup)で行うことができます。
>
>

1. 任意の職場または学校アカウントのサインイン ページで、**[アカウントにアクセスできない場合]** リンクをクリックするか、[[ID を入力] ページ](https://passwordreset.microsoftonline.com)に移動します。

   ![アカウントのサインイン][110]

2. 職場または学校アカウントの ID を入力し、CAPTCHA チャレンジをクリアしてロボットでないことを証明します。 

   ![ID の確認][111]

3. **[次へ]**をクリックします。
4. パスワードをリセットするオプションを選択します。 管理者によるシステムの構成に応じて、次の 1 つ以上の選択肢が表示されます。

   * **[連絡用電子メール アドレスにメールを送信]**: **連絡用メール アドレス**または**認証用メール アドレス** (選択した方) に 6 桁のコードが記載されたメールが送信されます。
   * **[携帯電話に SMS 送信]**: **携帯電話**または**認証用メール アドレス** (選択した方) に 6 桁のコードが SMS 送信されます。
   * **[携帯電話に発信]**: **携帯電話**または**認証用電話** (選択した方) に呼び出しが行われます。 発信を確認するには、*#* キーを押します。
   * **[会社電話に発信]**: **会社電話**に発信されます。 発信を確認するには、*#* キーを押します。
   * **[セキュリティの質問に回答する]**: 事前に登録済みのセキュリティの質問を表示し、回答を求めます。

   ![パスワード リセットのオプション][109]

5. この例では、**[携帯電話に SMS 送信]** オプションを選択した場合を示しています。 電話番号ベースのオプションでは、完全な電話番号を入力し、番号が正しいことを確認するために **[テキスト]** をクリックします。 テキストと 6 桁のコードが、電話に送信されます。

6. テキストの受信後は、送信されたコードの番号ではなく、メッセージ本文の確認コードを使用するようにしてください。 テキストの受信には数分かかる場合があります。

7. 受け取った 6 桁のコードを入力し、**[次へ]** をクリックします。

8. 管理者から追加の確認手順が要求される場合があります。 その場合は、別のオプションを選択して手順 4. を繰り返します。

9. **[新しいパスワードの選択]** ページで、新しいパスワードを入力して選択内容を確認し、**[完了]** をクリックします。

   ![新しいパスワードの確認][107]

10. パスワードが受け入れられると、新しいパスワードでサインインできるようになります。

    ![パスワード リセットの確認][108]

パスワードのリセットで問題が発生した場合は、 「[一般的な問題とその解決方法](#common-problems-and-their-solutions)」を参照してください。

## <a name="unlock-your-account"></a>自分のアカウントのロックを解除する
職場または学校アカウントのサインイン ページから、ローカル アカウントのロックを解除するには、次の手順に従います。 

> [!NOTE]
> アカウントのロックを解除できるのは、オンプレミスでロックされている場合のみです。

> [!IMPORTANT]
> この機能は、管理者が有効にしている場合にのみ使用できます。 有効になっていない場合は、この機能がアカウントで有効になっていないというメッセージが表示されます。 **[管理者に問い合わせてください]** のリンクを使用して、アカウントのロックを解除するよう管理者に連絡してください。
>
> 管理者がアカウントに対してこの機能を有効にしている場合は、この機能を使用する前に、サインアップする必要があります。 この操作は、[パスワード リセット登録ページ](http://aka.ms/ssprsetup)で行うことができます。
>
>

1. 任意の職場または学校アカウントのサインイン ページで、**[アカウントにアクセスできない場合]** リンクをクリックするか、[[ID を入力] ページ](https://passwordreset.microsoftonline.com)に移動します。

   ![アカウントのサインイン][110]
2. **[ID を入力]** ページで職場または学校アカウントの ID を入力し、CAPTCHA チャレンジをクリアしてロボットでないことを証明します。 
3. **[次へ]**をクリックします。

   ![ID の確認][111]
4. 自分のアカウントをロック解除するオプションを選択します。 管理者によるシステムの構成に応じて、次の 1 つ以上の選択肢が表示されます。

   * **[連絡用電子メール アドレスにメールを送信]**: **連絡用メール アドレス**または**認証用メール アドレス** (選択した方) に 6 桁のコードが記載されたメールが送信されます。
   * **[携帯電話に SMS 送信]**: **携帯電話**または**認証用メール アドレス** (選択した方) に 6 桁のコードが SMS 送信されます。
   * **[携帯電話に発信]**: **携帯電話**または**認証用電話** (選択した方) に呼び出しが行われます。 発信を確認するには、*#* キーを押します。
   * **[会社電話に発信]**: **会社電話**に発信されます。 発信を確認するには、*#* キーを押します。
   * **[セキュリティの質問に回答する]**: 事前に登録済みのセキュリティの質問を表示し、回答を求めます。

   ![ID の確認のオプション][112]
5. この例では、**[セキュリティの質問に回答する]** オプションが選択されている状態を示しています。 セキュリティの質問に対する回答を入力し、**[次へ]** をクリックして身元を確認します。

6. 管理者から追加の確認手順が要求される場合があります。 その場合は、別のオプションを選択して手順 4. を繰り返します。
7. 成功のページが表示されたら、オンプレミスのアカウントがロック解除されたので、サインインできます。

   ![ロックを解除されたアカウントのサインイン][113]

> [!IMPORTANT]
> 必ずすべてのデバイスを新しいパスワードに更新してください。 最初にアカウントがロックアウトされた原因は、古いパスワードを使用する悪意のあるアプリ (携帯電話の電子メール クライアントなど) であることがよくあります。
   >
   >


## <a name="common-problems-and-their-solutions"></a>一般的な問題とその解決方法
次に、一般にエラーになる場合とその解決方法を示します。

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>エラー ケース</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>表示されるエラー</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>解決策</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>ユーザー ID を入力した後に **[管理者に連絡してください]** ページが表示されます。</p>
            </td>
            <td>
              <p>管理者に連絡してください。 <br><br>お客様のユーザー アカウントのパスワードは Microsoft によって管理されていません。 そのため、パスワードを自動的にリセットすることはできません。 <br><br>詳細については、お客様の管理者またはヘルプデスクにお問い合わせください。 </p>
            </td>
            <td>
              <p>このメッセージが表示されるのは、管理者がオンプレミス環境でパスワードを管理していて、<b>[アカウントにアクセスできません]</b> リンクからパスワードをリセットすることが許可されていないためです。 <br><br> パスワードをリセットするには、管理者に直接依頼してください。 Office 365 でパスワードをリセットしたいことを管理者に知らせ、この機能を有効にしてもらいます。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>ユーザー ID の入力後、"アカウントは、パスワード リセットが有効になっていません" エラーが表示されます。</p>
            </td>
            <td>
              <p>アカウントは、パスワード リセットが有効になっていません。<br><br>アカウントがこのサービスを使用できるようにセットアップされていません。<br><br> ご希望の場合は、こちらで組織の管理者に連絡してパスワードのリセットを依頼することもできます。</p>
            </td>
            <td>
              <p>このメッセージは、管理者が <b>[アカウントにアクセスできません]</b> リンクからのパスワードのリセットを有効にしていないため、または機能を使用するライセンスをユーザーに付与していないために表示されます。 <br><br> パスワードをリセットするには、<b>[管理者に連絡してください]</b> リンクをクリックし、組織の管理者に電子メールを送信します。 Office 365 でパスワードをリセットしたいことを管理者に知らせ、この機能を有効にしてもらいます。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>ユーザー ID の入力後、"お客様のアカウントを確認できませんでした" エラーが表示されます。</p>
            </td>
            <td>
              <p>お客様のアカウントを確認できませんでした。<br><br>ご希望の場合は、こちらで組織の管理者に連絡してパスワードのリセットを依頼することもできます。 </p>
            </td>
            <td>
              <p>このメッセージは、パスワードのリセットは有効になっていても、サービスを使用するための登録を行っていない場合に表示されます。 パスワードのリセットを登録するには、アカウントに再アクセスできるようになった後、[パスワード リセット登録ページ](http://aka.ms/ssprsetup)に移動します。 <br><br> パスワードをリセットするには、<b>[管理者に連絡してください]</b> リンクをクリックし、組織の管理者に電子メールを送信します。</p>
            </td>
          </tr>
        </tbody></table>

## <a name="next-steps"></a>次のステップ
セルフサービスによるパスワード リセット (SSPR) についてさらに質問がある場合は、管理者に問い合わせるか、以下のリンクを参照してください。

* [SSPR の情報を登録する必要がある場合](http://aka.ms/ssprsetup)
* [アカウントにアクセスできない場合](https://passwordreset.microsoftonline.com)
* [Office 365 のパスワード リセットの情報](https://support.office.com/en-us/article/Reset-user-passwords-in-Office-365-3254c031-04c9-44f1-8fda-2563847a6b31?ui=en-US&rs=en-US&ad=US)
* [アクセス パネル](https://myapps.microsoft.com)

[101]: ./media/active-directory-passwords-update-your-own-password/password-1-dont-lose-access.png "password-1-dont-lose-access.png"
[102]: ./media/active-directory-passwords-update-your-own-password/password-2-verification-response.png "password-2-verification-response.png"
[103]: ./media/active-directory-passwords-update-your-own-password/password-2-verification-text.png "password-2-verification-text.png"
[104]: ./media/active-directory-passwords-update-your-own-password/password-3-registration-complete.png "password-3-registration-complete.png"
[105]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-cant-access.png "password-4-reset-cant-access.png"
[106]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-captcha.png "password-4-reset-captcha.png"
[107]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-change.png "password-4-reset-change.png"
[108]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-finished.png "password-4-reset-finished.png"
[109]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-verification.png "password-4-reset-verification.png"
[110]: ./media/active-directory-passwords-update-your-own-password/password-5-unlock-cant-access.png "password-5-unlock-cant-access.png"
[111]: ./media/active-directory-passwords-update-your-own-password/password-5-unlock-captcha.png "password-5-unlock-captcha.png"
[112]: ./media/active-directory-passwords-update-your-own-password/password-5-unlock-verification.png "password-5-unlock-verification.png"
[113]: ./media/active-directory-passwords-update-your-own-password/password-5-unlock-finished.png "password-5-unlock-finished.png"

