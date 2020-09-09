---
title: 電話番号を検証方法として設定する - Azure AD
description: 電話番号とモバイル デバイスを検証方法として使用して ID を検証するように [Security info (preview)] (セキュリティ情報 (プレビュー)) ページを設定する方法。
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
ms.openlocfilehash: 7500b6b146a627dcdebacd9d2e2c7c61bc43c105
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744447"
---
# <a name="set-up-a-phone-number-as-your-verification-method"></a>電話番号を検証方法として設定する

次の手順に従って、2 要素認証とパスワードのリセット メソッドを追加できます。 これを初回に設定したら、 **[セキュリティ情報]** ページに戻り、セキュリティ情報を追加、更新、または削除できます。

職場または学校アカウントにサインイン後すぐにこれを設定するように求められた場合、[サインイン ページ プロンプトからセキュリティ情報を設定する](security-info-setup-signin.md)方法に関する記事の詳細な手順を参照してください。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

> [!Note]
> セキュリティ情報では、電話の内線番号の使用はサポートされません。 電話番号が "+1 4255551234X12345" の適切な形式であっても、電話がかけられる前に内線番号は削除されます。
>
> 電話番号オプションが表示されない場合、検証にこのオプションを使用することを組織が許可していない可能性があります。 この場合、別の方法を選択するか、組織のヘルプ デスクに連絡して、さらに支援を依頼する必要があります。

## <a name="security-verification-versus-password-reset-authentication"></a>セキュリティ認証とパスワードのリセット認証

セキュリティ情報の方法は、2 要素セキュリティ認証とパスワードのリセットの両方に使用されます。 ただし、すべてのメソッドを両方に使用できるわけではありません。

| Method | 使用目的 |
| ------ | -------- |
| Authenticator アプリ | 2 要素認証とパスワードのリセット認証。 |
| テキスト メッセージ | 2 要素認証とパスワードのリセット認証。 |
| 電話 | 2 要素認証とパスワードのリセット認証。 |
| セキュリティ キー | 2 要素認証とパスワードのリセット認証。 |
| 電子メール アカウント | パスワードのリセット認証のみ。 2 要素認証には別のメソッドを選択する必要があります。 |
| セキュリティの質問 | パスワードのリセット認証のみ。 2 要素認証には別のメソッドを選択する必要があります。 |

## <a name="set-up-phone-calls-from-the-security-info-page"></a>[セキュリティ情報] ページから電話呼び出しを設定する

組織の設定に応じて、セキュリティ情報メソッドの 1 つとして電話呼び出しを使用できます。

>[!Note]
>電話呼び出しではなくテキスト メッセージの受信を希望する場合は、「[テキスト メッセージを使用するようにセキュリティ情報を設定する (プレビュー)](security-info-setup-text-msg.md)」の手順のようにします。

### <a name="to-set-up-phone-calls"></a>電話呼び出しを設定するには

1. 職場または学校アカウントにサインインした後、、 https://myaccount.microsoft.com/ ページに移動します。

    ![強調表示されているセキュリティ情報リンクを示す [マイ プロファイル] ページ](media/security-info/securityinfo-myprofile-page.png)

2. 左側のナビゲーション ウィンドウから、または **[セキュリティ情報]** ブロックのリンクから **[セキュリティ情報]** を選択した後、 **[セキュリティ情報]** ページで **[メソッドの追加]** を選択します。

    ![[メソッドの追加] オプションが強調表示されている [セキュリティ情報] ページ](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. **[メソッドの追加]** ページで、ドロップダウン リストから **[電話番号]** を選択し、 **[追加]** を選択します。

    ![[電話番号] が選択された [メソッドの追加] ボックス](media/security-info/securityinfo-myprofile-addphonetext.png)

4. **[電話番号]** ページで、モバイル デバイスの電話番号を入力し、 **[電話する]** を選択して、 **[次へ]** を選択します。

    ![電話番号を追加し、電話呼び出しを選択する](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. 入力した電話番号にかかってくる確認電話の呼び出しに応答し、指示に従います。

    ページが変わり、成功したことが示されます。

    ![成功通知、電話番号への接続、電話呼び出しを受け取る選択、ユーザーのアカウント](media/security-info/securityinfo-myprofile-phonetext-success.png)

    セキュリティ情報が更新され、2 段階認証またはパスワードのリセットを使用するときに、電話呼び出しを使用して ID を確認できるようになります。 電話呼び出しを既定のメソッドにしたい場合は、この記事の「[既定のセキュリティ情報メソッドを変更する](#change-your-default-security-info-method)」をご覧ください。

## <a name="delete-phone-calls-from-your-security-info-methods"></a>セキュリティ情報メソッドから電話呼び出しを削除する

セキュリティ情報メソッドとして電話呼び出しを使用したくなくなった場合は、 **[セキュリティ情報]** ページから削除できます。

>[!Important]
>電話呼び出しを誤って削除した場合、元に戻す方法はありません。 この記事の「[電話呼び出しを設定するには](#set-up-phone-calls-from-the-security-info-page)」セクションの手順に従って、メソッドを再度追加する必要があります。

### <a name="to-delete-phone-calls"></a>電話呼び出しを削除するには

1. **[セキュリティ情報]** ページで、 **[電話番号]** オプションの横にある **[削除]** リンクを選択します。

    ![セキュリティ情報から電話番号メソッドを削除するためのリンク](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. 確認ボックスで **[はい]** を選択して、**電話**番号を削除します。 電話番号を削除すると、セキュリティ情報から削除され、 **[セキュリティ情報]** ページから消去されます。 **電話番号**が既定のメソッドである場合、既定値は、別の使用可能なメソッドに変更されます。

## <a name="change-your-default-security-info-method"></a>既定のセキュリティ情報メソッドを変更する

2 要素検証を使用して職場または学校アカウントにサインインする場合、またはパスワード リセット要求に、電話呼び出しを既定のメソッドとして使用する場合は、 **[セキュリティ情報]** ページから設定できます。

### <a name="to-change-your-default-security-info-method"></a>既定のセキュリティ情報メソッドを変更するには

1. **[セキュリティ情報]** ページで、 **[Default sign-in method]\(既定のサインイン方法\)** 情報の横の **[変更]** リンクを選択します。

    ![既定のサインイン方法の変更リンク](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. 利用可能なメソッドのドロップダウン リストから **[Phone - call (*_your_phone_number_*)]\(電話 - 呼び出し (<ユーザーの電話番号>)\)** を選択して、 **[確認]** を選択します。

    ![既定のサインインのメソッドを選択する](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    サインインに使用される既定のメソッドが、 **[Phone - call (*_your_phone_number_*)]\(電話 - 呼び出し (<ユーザーの電話番号>)\)** に変更されます。

## <a name="additional-security-info-methods"></a>追加のセキュリティ情報メソッド

操作内容に基づき、本人確認のために組織から連絡が届きますが、その方法には追加の選択肢があります。 選択肢は次のようになっています。

- **認証アプリ。** 認証アプリをダウンロードして使用する場合、2 段階認証やパスワード リセットのために承認通知かランダムに生成された承認コードを取得できます。 Microsoft Authenticator アプリの設定方法と使用方法に関する段階的な説明が必要な場合は、「[認証アプリを使用するようにセキュリティ情報を設定する](security-info-setup-auth-app.md)」を参照してください。

- **モバイル デバイスのテキスト。** モバイル デバイスの番号を入力し、2 段階認証やパスワード リセットに使用するテキスト コードを取得します。 テキスト メッセージ (SMS) による本人確認方法に関する段階的な説明が必要な場合は、「[Set up security info to use text messaging (SMS)](security-info-setup-text-msg.md)」(テキスト メッセージ (SMS) を使用するようにセキュリティ情報を設定する) を参照してください。

- **セキュリティ キー。** Microsoft と互換性のあるセキュリティ キーを登録し、それを PIN と一緒に使用して、2 段階認証またはパスワードのリセットを行います。 セキュリティ キーによる本人確認方法に関する段階的説明が必要な場合、[セキュリティ キーを使用するようにセキュリティ情報を設定する](security-info-setup-security-key.md)方法に関するページを参照してください。

- **メール アドレス。** 職場または学校のメール アドレスを入力して、パスワードをリセットするためのメールを受け取ります。 このオプションは、2 段階認証用には使用できません。 メールの設定方法に関する段階的説明が必要な場合、「[Set up security info to use email (メールを使用するようにセキュリティ情報を設定する)](security-info-setup-email.md)」を参照してください。

- **セキュリティの質問。** 管理者が組織のために作成したいくつかのセキュリティの質問に答えます。 この選択肢はパスワード リセットの場合にのみ選択できます。2 段階認証には選択できません。 セキュリティの質問の設定方法に関する段階的な説明が必要な場合は、「[Set up security info to use security questions ](security-info-setup-questions.md)」(セキュリティの質問を使用するようにセキュリティ情報を設定する) を参照してください。

    >[!Note]
    >一部の選択肢が表示されない場合、おそらく、組織がその方法を許可していません。 その場合、選択できる方法を選択するか、管理者に支援を要請する必要があります。

## <a name="next-steps"></a>次のステップ

- [パスワード リセット ポータル](https://passwordreset.microsoftonline.com/)を使用するか、「[職場または学校のパスワードをリセットする](active-directory-passwords-update-your-own-password.md)」の手順に従って、パスワードをリセットする (パスワードをなくしたか忘れた場合)。

- 「[Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)」の記事でサインインの問題に関するトラブルシューティングのヒントやヘルプを入手する。
