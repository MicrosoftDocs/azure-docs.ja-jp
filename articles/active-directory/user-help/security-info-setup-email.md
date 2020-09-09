---
title: メール アドレスを検証方法として設定する - Azure AD
description: メール アドレスを検証方法として使用して ID を検証するように [Security info (preview)] (セキュリティ情報 (プレビュー)) ページを設定する方法。
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
ms.openlocfilehash: 4f0ea55149fc8cfeeb6ef719d67346019b81078a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744463"
---
# <a name="set-up-an-email-address-as-your-verification-method"></a>メール アドレスを検証方法として設定する

次の手順に従って、パスワードのリセット メソッドを追加できます。 これを初回に設定したら、 **[セキュリティ情報]** ページに戻り、セキュリティ情報を追加、更新、または削除できます。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>メール オプションが表示されない場合は、組織がパスワード リセット方法にこのオプションの使用を許可していない可能性があります。 この場合、別の方法を選択するか、組織のヘルプ デスクに連絡して、さらに支援を依頼する必要があります。

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

## <a name="set-up-your-email-address-from-the-security-info-page"></a>[セキュリティ情報] ページからメール アドレスを設定する

組織の設定に応じて、セキュリティ情報メソッドの 1 つとしてメール アドレスを使用できます。

>[!Note]
>アクセスにネットワーク パスワードを要求しないメール アドレスの使用をお勧めします。 電子メール オプションが表示されない場合、検証に電子メールを使用することを組織が許可していない可能性があります。 その場合、別の方法を選択するか、管理者に支援を要請する必要があります。

### <a name="to-set-up-your-email-address"></a>メール アドレスを設定するには

1. 職場または学校アカウントにサインインした後、、 https://myaccount.microsoft.com/ ページに移動します。

    ![強調表示されているセキュリティ情報リンクを示す [マイ プロファイル] ページ](media/security-info/securityinfo-myprofile-page.png)

2. 左側のナビゲーション ウィンドウから、または **[セキュリティ情報]** ブロックのリンクから **[セキュリティ情報]** を選択した後、 **[セキュリティ情報]** ページで **[メソッドの追加]** を選択します。

    ![[メソッドの追加] オプションが強調表示されている [セキュリティ情報] ページ](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. **[メソッドの追加]** ページで、ドロップダウン リストから **[電子メール]** を選択し、 **[追加]** を選択します。

    ![[電子メール] が選択された [メソッドの追加] ボックス](media/security-info/securityinfo-myprofile-addemail.png)

4. **[電子メール]** ページで、メール アドレス (alain@gmail.com など) を入力し、 **[次へ]** を選択します。

    ![電話番号を追加し、電話呼び出しを選択する](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >このメール アドレスには、職場または学校の電子メールを選択できません。

5. 指定したメール アドレスに送信されたコードを入力し、 **[次へ]** を選択します。

    ![電話番号を追加し、テキスト メッセージを選択する](media/security-info/securityinfo-myprofile-emailcode.png)

    セキュリティ情報が更新され、パスワードのリセットを使用するときに、自分のメール アドレスを使用して本人確認をできるようになります。

## <a name="delete-your-email-address-from-your-security-info-methods"></a>セキュリティ情報メソッドからメール アドレスを削除する

セキュリティ情報メソッドとしてメール アドレスを使用しなくなった場合は、 **[セキュリティ情報]** ページから削除できます。

>[!Important]
>メール アドレスを誤って削除した場合、元に戻す方法はありません。 この記事の「[メール アドレスを設定するには](#set-up-your-email-address-from-the-security-info-page)」セクションの手順に従って、メソッドを再度追加する必要があります。

### <a name="to-delete-your-email-address"></a>メール アドレスを削除するには

1. **[セキュリティ情報]** ページで、 **[電子メール]** オプションの横にある **[削除]** リンクを選択します。

    ![セキュリティ情報から電話番号メソッドを削除するためのリンク](media/security-info/securityinfo-myprofile-emaildelete.png)

2. 確認ボックスで **[はい]** を選択して、**メール アドレス**を削除します。 電子メール アカウントを削除すると、セキュリティ情報から削除され、 **[セキュリティ情報]** ページから消去されます。

## <a name="additional-security-info-methods"></a>追加のセキュリティ情報メソッド

操作内容に基づき、本人確認のために組織から連絡が届きますが、その方法には追加の選択肢があります。 選択肢は次のようになっています。

- **認証アプリ。** 認証アプリをダウンロードして使用する場合、2 段階認証やパスワード リセットのために承認通知かランダムに生成された承認コードを取得できます。 Microsoft Authenticator アプリの設定方法と使用方法に関する段階的な説明が必要な場合は、「[認証アプリを使用するようにセキュリティ情報を設定する](security-info-setup-auth-app.md)」を参照してください。

- **モバイル デバイスのテキスト。** モバイル デバイスの番号を入力し、2 段階認証やパスワード リセットに使用するテキスト コードを取得します。 テキスト メッセージ (SMS) による本人確認方法に関する段階的な説明が必要な場合は、「[Set up security info to use text messaging (SMS)](security-info-setup-text-msg.md)」(テキスト メッセージ (SMS) を使用するようにセキュリティ情報を設定する) を参照してください。

- **モバイル デバイスまたは職場の電話の呼び出し。** モバイル デバイスの番号を入力し、電話の呼び出しで 2 段階認証やパスワード リセットを行います。 電話番号による本人確認方法に関する段階的説明が必要な場合、「[電話を使用するようにセキュリティ情報を設定する](security-info-setup-phone-number.md)」を参照してください。

- **セキュリティ キー。** Microsoft と互換性のあるセキュリティ キーを登録し、それを PIN と一緒に使用して、2 段階認証またはパスワードのリセットを行います。 セキュリティ キーによる本人確認方法に関する段階的説明が必要な場合、[セキュリティ キーを使用するようにセキュリティ情報を設定する](security-info-setup-security-key.md)方法に関するページを参照してください。

- **セキュリティの質問。** 管理者が組織のために作成したいくつかのセキュリティの質問に答えます。 この選択肢はパスワード リセットの場合にのみ選択できます。2 段階認証には選択できません。 セキュリティの質問の設定方法に関する段階的な説明が必要な場合は、「[Set up security info to use security questions ](security-info-setup-questions.md)」(セキュリティの質問を使用するようにセキュリティ情報を設定する) を参照してください。

    >[!Note]
    >一部の選択肢が表示されない場合、おそらく、組織がその方法を許可していません。 その場合、選択できる方法を選択するか、管理者に支援を要請する必要があります。

## <a name="next-steps"></a>次のステップ

- [パスワード リセット ポータル](https://passwordreset.microsoftonline.com/)を使用するか、「[職場または学校のパスワードをリセットする](active-directory-passwords-update-your-own-password.md)」の手順に従って、パスワードをリセットする (パスワードをなくしたか忘れた場合)。

- 「[Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)」の記事でサインインの問題に関するトラブルシューティングのヒントやヘルプを入手する。
