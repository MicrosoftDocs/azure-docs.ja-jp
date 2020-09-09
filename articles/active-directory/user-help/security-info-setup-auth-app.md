---
title: Microsoft Authenticator アプリを検証方法として設定する - Azure AD
description: Microsoft Authenticator アプリを検証方法として使用して ID を検証するように [Security info (preview)] (セキュリティ情報 (プレビュー)) ページを設定する方法。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: 133e6b533a3d87ef519d0f586ea59db1bbce28b9
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744478"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>Microsoft Authenticator アプリを検証方法として設定する

次の手順に従って、2 要素認証とパスワードのリセット メソッドを追加できます。 これを初回に設定したら、 **[セキュリティ情報]** ページに戻り、セキュリティ情報を追加、更新、または削除できます。

職場または学校アカウントにサインイン後すぐにこれを設定するように求められた場合、[サインイン ページ プロンプトからセキュリティ情報を設定する](security-info-setup-signin.md)方法に関する記事の詳細な手順を参照してください。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
> 認証アプリ オプションが表示されない場合、検証にこのオプションを使用することを組織が許可していない可能性があります。 この場合、別の方法を選択するか、組織のヘルプ デスクに連絡して、さらに支援を依頼する必要があります。

## <a name="security-vs-password-reset-verification"></a>セキュリティとパスワード リセットの検証

セキュリティ情報の方法は、2 要素セキュリティ認証とパスワードのリセットの両方に使用されます。 ただし、すべてのメソッドを両方に使用できるわけではありません。

| Method | 使用目的 |
| ------ | -------- |
| Authenticator アプリ | 2 要素認証とパスワードのリセット認証。 |
| テキスト メッセージ | 2 要素認証とパスワードのリセット認証。 |
| 電話 | 2 要素認証とパスワードのリセット認証。 |
| セキュリティ キー | 2 要素認証とパスワードのリセット認証。 |
| 電子メール アカウント | パスワードのリセット認証のみ。 2 要素認証には別のメソッドを選択する必要があります。 |
| セキュリティの質問 | パスワードのリセット認証のみ。 2 要素認証には別のメソッドを選択する必要があります。 |

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>[セキュリティ情報] ページから Microsoft Authenticator アプリを設定する

組織の設定に応じて、セキュリティ情報メソッドの 1 つとして認証アプリを使用できます。 Microsoft Authenticator アプリを使用することは必須ではなく、設定プロセス時に別のアプリを選択することもできます。 ただし、この記事では、Microsoft Authenticator アプリを使用します。

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリを設定するには

1. 職場または学校アカウントにサインインした後、、 https://myaccount.microsoft.com/ ページに移動します。

    ![強調表示されているセキュリティ情報リンクを示す [マイ プロファイル] ページ](media/security-info/securityinfo-myprofile-page.png)

2. 左側のナビゲーション ウィンドウから、または **[セキュリティ情報]** ブロックのリンクから **[セキュリティ情報]** を選択した後、 **[セキュリティ情報]** ページで **[メソッドの追加]** を選択します。

    ![[メソッドの追加] オプションが強調表示されている [セキュリティ情報] ページ](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. **[メソッドの追加]** ページで、ドロップダウン リストから **[Authenticator アプリ]** を選択して、 **[追加]** を選択します。

    ![Authenticator アプリが選択されている [メソッドの追加] ボックス](media/security-info/securityinfo-myprofile-addauthapp.png)

4. **[Start by getting the app]\(アプリを取得して起動\)** ページで、 **[今すぐダウンロード]** を選択し、Microsoft Authenticator アプリをダウンロードしてモバイル デバイスにインストールし、 **[次へ]** を選択します。

    アプリをダウンロードして、インストールする方法の詳細については、「[Microsoft Authenticator アプリのダウンロードとインストール](user-help-auth-app-download-install.md)」を参照してください。

    ![[Start by getting the app]\(アプリを取得して起動\) ページ](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Microsoft Authenticator アプリ以外の認証アプリを使用する場合は、 **[別の認証アプリを使用します]** リンクを選択します。
   >
   > 組織で、認証アプリのほかに別の方法を選択できる場合は、 **[別の方法を設定します]** リンクを選択できます。

5. モバイル デバイスで Microsoft Authenticator アプリを設定している間は、 **[アカウントのセットアップ]** ページに留まります。

    ![認証アプリのセットアップ ページ](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Microsoft Authenticator アプリを開き、通知を許可するように選択し (メッセージが表示された場合)、右上の **[Customize and control]\(カスタマイズと制御\)** アイコンから **[アカウントの追加]** を選択して、 **[職場または学校アカウント]** を選択します。

    >[!Note]
    >初めて Microsoft Authenticator アプリを設定する場合は、アプリによるカメラへのアクセスを許可するか (iOS)、またはアプリによる画像の撮影とビデオの録画を許可するか (Android) を確認するメッセージが表示されることがあります。 認証アプリがカメラにアクセスして、次の手順で QR コードを撮影できるようにするには、 **[許可]** を選択する必要があります。 カメラを許可しない場合でも、認証アプリを設定できますが、手動でコード情報を追加する必要があります。 コードを手動で追加する方法の詳細については、「[アプリにアカウントを手動で追加する](user-help-auth-app-add-account-manual.md)」を参照してください。

7. コンピューターで **[アカウントのセットアップ]** ページに戻り、 **[次へ]** を選択します。

    **[QR コードをスキャンします]** ページが表示されます。

    ![認証アプリを使用して QR コードをスキャンする](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. 手順 6 で職場または学校アカウントを作成した後に、モバイル デバイスに表示された、提供されたコードを Microsoft Authenticator アプリの QR コード リーダーでスキャンします。

    認証アプリによって、ユーザーからの追加情報を必要とせずに、職場または学校アカウントが正常に追加されるはずです。 ただし、QR コード リーダーで、コードを読み取れない場合は、 **[QR コードをスキャンできませんか?]** リンクを選択し、コードと URL を Microsoft Authenticator アプリに手動で入力します。 コードを手動で追加する方法の詳細については、「[アプリにアカウントを手動で追加する](user-help-auth-app-add-account-manual.md)」を参照してください。

9. コンピューターの **[QR コードをスキャンします]** ページで、 **[次へ]** を選択します。

    モバイル デバイスの Microsoft Authenticator アプリに、アカウントをテストするための通知が送信されます。

    ![認証アプリでアカウントをテストする](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Microsoft Authenticator アプリで通知を承認し、 **[次へ]** を選択します。

     ![アプリとアカウントの接続の成功通知](media/security-info/securityinfo-myprofile-successauthapp.png)

     2 段階認証またはパスワードのリセットを使用するときに、既定で Microsoft Authenticator アプリを使用して本人確認をするようにセキュリティ情報が更新されます。

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>セキュリティ情報メソッドから認証アプリを削除する

セキュリティ情報メソッドとして認証 アプリを使用しなくなった場合は、 **[セキュリティ情報]** ページから削除できます。 これは、Microsoft Authenticator アプリだけでなく、すべての認証アプリに対して機能します。 アプリの削除後、モバイル デバイスの認証アプリに移動し、アカウントを削除する必要があります。

>[!Important]
>認証アプリを誤って削除した場合、元に戻す方法はありません。 この記事の[認証アプリのセットアップ](#set-up-the-microsoft-authenticator-app-from-the-security-info-page)のセクションの手順に従って、認証アプリを再度追加する必要があります。

### <a name="to-delete-the-authenticator-app"></a>認証アプリを削除するには

1. **[セキュリティ情報]** ページで、認証アプリの横にある **[削除]** リンクを選択します。

    ![セキュリティ情報から認証アプリを削除するリンク](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. 確認ボックスから、 **[はい]** を選択して、認証アプリを削除します。 認証アプリを削除すると、セキュリティ情報から削除され、 **[セキュリティ情報]** ページから消去されます。 認証アプリが、既定の方法である場合、既定値は、別の使用可能な方法に変更されます。

3. モバイル デバイスの認証アプリを開き、 **[アカウントの編集]** を選択し、認証アプリから職場または学校アカウントを削除します。

    アカウントは 2 要素検証およびパスワード リセット要求用の認証アプリから完全に削除されます。

## <a name="change-your-default-security-info-method"></a>既定のセキュリティ情報メソッドを変更する

2 要素検証を使用して職場または学校アカウントにサインインする場合、またはパスワード リセット要求に、認証アプリを既定の方法として使用する場合は、 **[セキュリティ情報]** ページから設定できます。

### <a name="to-change-your-default-security-info-method"></a>既定のセキュリティ情報メソッドを変更するには

1. **[セキュリティ情報]** ページで、 **[Default sign-in method]\(既定のサインイン方法\)** 情報の横の **[変更]** リンクを選択します。

    ![既定のサインイン方法の変更リンク](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. 使用可能な方法のドロップダウン リストから、 **[Microsoft Authenticator - notification]\(Microsoft Authenticator - 通知\)** を選択します。 Microsoft Authenticator アプリを使用していない場合は、 **[Authenticator app or hardware token]\(認証アプリまたはハードウェア トークン\)** オプションを選択します。

    ![既定のサインインのメソッドを選択する](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. **[確認]** を選択します。

    サインインに使用される既定の方法が Microsoft Authenticator アプリに変更されます。

## <a name="additional-security-info-methods"></a>追加のセキュリティ情報メソッド

操作内容に基づき、本人確認のために組織から連絡が届きますが、その方法には追加の選択肢があります。 選択肢は次のようになっています。

- **モバイル デバイスのテキスト。** モバイル デバイスの番号を入力し、2 段階認証やパスワード リセットに使用するテキスト コードを取得します。 テキスト メッセージ (SMS) による本人確認方法に関する段階的な説明が必要な場合は、「[Set up security info to use text messaging (SMS)](security-info-setup-text-msg.md)」(テキスト メッセージ (SMS) を使用するようにセキュリティ情報を設定する) を参照してください。

- **モバイル デバイスまたは職場の電話の呼び出し。** モバイル デバイスの番号を入力し、電話の呼び出しで 2 段階認証やパスワード リセットを行います。 電話番号による本人確認方法に関する段階的説明が必要な場合、「[電話を使用するようにセキュリティ情報を設定する](security-info-setup-phone-number.md)」を参照してください。

- **セキュリティ キー。** Microsoft と互換性のあるセキュリティ キーを登録し、それを PIN と一緒に使用して、2 段階認証またはパスワードのリセットを行います。 セキュリティ キーによる本人確認方法に関する段階的説明が必要な場合、[セキュリティ キーを使用するようにセキュリティ情報を設定する](security-info-setup-security-key.md)方法に関するページを参照してください。

- **メール アドレス。** 職場または学校のメール アドレスを入力して、パスワードをリセットするためのメールを受け取ります。 このオプションは、2 段階認証用には使用できません。 メールの設定方法に関する段階的説明が必要な場合、「[Set up security info to use email (メールを使用するようにセキュリティ情報を設定する)](security-info-setup-email.md)」を参照してください。

- **セキュリティの質問。** 管理者が組織のために作成したいくつかのセキュリティの質問に答えます。 この選択肢はパスワード リセットの場合にのみ選択できます。2 段階認証には選択できません。 セキュリティの質問の設定方法に関する段階的な説明が必要な場合は、「[Set up security info to use security questions ](security-info-setup-questions.md)」(セキュリティの質問を使用するようにセキュリティ情報を設定する) を参照してください。

    >[!Note]
    >一部の選択肢が表示されない場合、おそらく、組織がその方法を許可していません。 その場合、選択できる方法を選択するか、管理者に支援を要請する必要があります。

## <a name="next-steps"></a>次のステップ

- 「[2 段階認証またはセキュリティ情報を使ったサインイン方法](security-info-setup-signin.md)」の手順に従って、Microsoft Authenticator アプリを使用してサインインします。

- [パスワード リセット ポータル](https://passwordreset.microsoftonline.com/)を使用するか、「[職場または学校のパスワードをリセットする](active-directory-passwords-update-your-own-password.md)」の手順に従って、パスワードをリセットする (パスワードをなくしたか忘れた場合)。

- 「[Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)」の記事でサインインの問題に関するトラブルシューティングのヒントやヘルプを入手する。
