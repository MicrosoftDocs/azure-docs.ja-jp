---
title: テキスト メッセージを検証方法として設定する - Azure AD
description: テキスト メッセージを検証方法として使用して ID を検証するように [Security info (preview)] (セキュリティ情報 (プレビュー)) ページを設定する方法。
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
ms.openlocfilehash: 0fe191ac827c76afd497896611430f6efc6283aa
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747240"
---
# <a name="set-up-text-messaging-as-your-verification-method"></a>テキスト メッセージを検証方法として設定する

次の手順に従って、2 要素認証とパスワードのリセット メソッドを追加できます。 これを初回に設定したら、 **[セキュリティ情報]** ページに戻り、セキュリティ情報を追加、更新、または削除できます。

職場または学校アカウントにサインイン後すぐにこれを設定するように求められた場合、[サインイン ページ プロンプトからセキュリティ情報を設定する](security-info-setup-signin.md)方法に関する記事の詳細な手順を参照してください。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>電話番号オプションが表示されない場合、検証にこのオプションを使用することを組織が許可していない可能性があります。 この場合、別の方法を選択するか、組織のヘルプ デスクに連絡して、さらに支援を依頼する必要があります。

## <a name="set-up-text-messages-from-the-security-info-page"></a>[セキュリティ情報] ページからテキスト メッセージを設定する

組織の設定に応じて、セキュリティ情報メソッドの 1 つとしてテキスト メッセージを使用できます。 テキスト メッセージ オプションは電話オプションの一部であるため、電話番号を使用する場合と同じ方法ですべてを設定しますが、Microsoft からの電話呼び出しではなく、テキスト メッセージを使用するように選択します。

>[!Note]
>テキスト メッセージではなく電話呼び出しを希望する場合は、「[電話を使用するようにセキュリティ情報を設定する](security-info-setup-phone-number.md)」の手順に従います。

### <a name="to-set-up-text-messages"></a>テキスト メッセージを設定するには

1. 職場または学校アカウントにサインインした後、、 https://myaccount.microsoft.com/ ページに移動します。

    ![強調表示されているセキュリティ情報リンクを示す [マイ プロファイル] ページ](media/security-info/securityinfo-myprofile-page.png)

2. 左側のナビゲーション ウィンドウから、または **[セキュリティ情報]** ブロックのリンクから **[セキュリティ情報]** を選択した後、 **[セキュリティ情報]** ページで **[メソッドの追加]** を選択します。

    ![[メソッドの追加] オプションが強調表示されている [セキュリティ情報] ページ](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. **[メソッドの追加]** ページで、ドロップダウン リストから **[電話番号]** を選択し、 **[追加]** を選択します。

    ![[電話番号] が選択された [メソッドの追加] ボックス](media/security-info/securityinfo-myprofile-addphonetext.png)

4. **[電話番号]** ページで、モバイル デバイスの電話番号を入力し、 **[コードを SMS 送信する]** を選択して、 **[次へ]** を選択します。

    ![電話番号を追加し、テキスト メッセージを選択する](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. モバイル デバイスにテキスト メッセージで送信されたコードを入力し、 **[次へ]** を選択します。

    ![電話番号を追加し、テキスト メッセージを選択する](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    ページが変わり、成功したことが示されます。

    ![成功通知、電話番号への接続、テキスト メッセージを受け取る選択、ユーザーのアカウント](media/security-info/securityinfo-myprofile-phonetext-success.png)

    セキュリティ情報が更新され、2 段階認証またはパスワードのリセットを使用するときに、テキスト メッセージを使用して ID を確認できるようになります。 テキスト メッセージを既定のメソッドにしたい場合は、この記事の「[既定のセキュリティ情報メソッドを変更する](#change-your-default-security-info-method)」をご覧ください。

## <a name="delete-text-messaging-from-your-security-info-methods"></a>セキュリティ情報メソッドからテキスト メッセージを削除する

セキュリティ情報メソッドとしてテキスト メッセージを使用したくなくなった場合は、 **[セキュリティ情報]** ページから削除できます。

>[!Important]
>テキスト メッセージを誤って削除した場合、元に戻す方法はありません。 この記事の「[テキスト メッセージを設定するには](#set-up-text-messages-from-the-security-info-page)」セクションの手順に従って、メソッドを再度追加する必要があります。

### <a name="to-delete-text-messaging"></a>テキスト メッセージを削除するには

1. **[セキュリティ情報]** ページで、 **[電話番号]** オプションの横にある **[削除]** リンクを選択します。

    ![セキュリティ情報から電話番号とテキスト メッセージ メソッドを削除するためのリンク](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. 確認ボックスで **[はい]** を選択して、**電話**番号を削除します。 電話番号を削除すると、セキュリティ情報から削除され、 **[セキュリティ情報]** ページから消去されます。 **電話番号**が既定のメソッドである場合、既定値は、別の使用可能なメソッドに変更されます。

## <a name="change-your-default-security-info-method"></a>既定のセキュリティ情報メソッドを変更する

2 要素検証を使用して職場または学校アカウントにサインインする場合、またはパスワード リセット要求に、テキスト メッセージを既定のメソッドとして使用する場合は、 **[セキュリティ情報]** ページから設定できます。

### <a name="to-change-your-default-security-info-method"></a>既定のセキュリティ情報メソッドを変更するには

1. **[セキュリティ情報]** ページで、 **[Default sign-in method]\(既定のサインイン方法\)** 情報の横の **[変更]** リンクを選択します。

    ![既定のサインイン方法の変更リンク](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. 利用可能なメソッドのドロップダウン リストから **[Phone - text (*_your_phone_number_*)]\(電話 - テキスト (<ユーザーの電話番号>)\)** を選択して、 **[確認]** を選択します。

    ![既定のサインインのメソッドを選択する](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    サインインに使用される既定のメソッドが、 **[Phone - text (*_your_phone_number_*)]\(電話 - テキスト (<ユーザーの電話番号>)\)** に変更されます。

## <a name="additional-security-info-methods"></a>追加のセキュリティ情報メソッド

操作内容に基づき、本人確認のために組織から連絡が届きますが、その方法には追加の選択肢があります。 選択肢は次のようになっています。

- **認証アプリ。** 認証アプリをダウンロードして使用する場合、2 段階認証やパスワード リセットのために承認通知かランダムに生成された承認コードを取得できます。 Microsoft Authenticator アプリの設定方法と使用方法に関する段階的な説明が必要な場合は、「[認証アプリを使用するようにセキュリティ情報を設定する](security-info-setup-auth-app.md)」を参照してください。

- **モバイル デバイスまたは職場の電話の呼び出し。** モバイル デバイスの番号を入力し、電話の呼び出しで 2 段階認証やパスワード リセットを行います。 電話番号による本人確認方法に関する段階的説明が必要な場合、「[電話を使用するようにセキュリティ情報を設定する](security-info-setup-phone-number.md)」を参照してください。

- **セキュリティ キー。** Microsoft と互換性のあるセキュリティ キーを登録し、それを PIN と一緒に使用して、2 段階認証またはパスワードのリセットを行います。 セキュリティ キーによる本人確認方法に関する段階的説明が必要な場合、[セキュリティ キーを使用するようにセキュリティ情報を設定する](security-info-setup-security-key.md)方法に関するページを参照してください。

- **メール アドレス。** 職場または学校のメール アドレスを入力して、パスワードをリセットするためのメールを受け取ります。 このオプションは、2 段階認証用には使用できません。 メールの設定方法に関する段階的説明が必要な場合、「[Set up security info to use email (メールを使用するようにセキュリティ情報を設定する)](security-info-setup-email.md)」を参照してください。

- **セキュリティの質問。** 管理者が組織のために作成したいくつかのセキュリティの質問に答えます。 この選択肢はパスワード リセットの場合にのみ選択できます。2 段階認証には選択できません。 セキュリティの質問の設定方法に関する段階的な説明が必要な場合は、「[Set up security info to use security questions ](security-info-setup-questions.md)」(セキュリティの質問を使用するようにセキュリティ情報を設定する) を参照してください。

    >[!Note]
    >一部の選択肢が表示されない場合、おそらく、組織がその方法を許可していません。 その場合、選択できる方法を選択するか、管理者に支援を要請する必要があります。

## <a name="next-steps"></a>次のステップ

- [パスワード リセット ポータル](https://passwordreset.microsoftonline.com/)を使用するか、「[職場または学校のパスワードをリセットする](active-directory-passwords-update-your-own-password.md)」の手順に従って、パスワードをリセットする (パスワードをなくしたか忘れた場合)。

- 「[Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)」の記事でサインインの問題に関するトラブルシューティングのヒントやヘルプを入手する。
