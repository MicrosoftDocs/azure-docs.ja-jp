---
title: セキュリティの質問を使用するようにセキュリティ情報 (プレビュー) を設定する - Azure Active Directory | Microsoft Docs
description: あらかじめ定義されたセキュリティの質問を使用して本人確認をするようにセキュリティ情報を設定する方法です。
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e5d1546c658631911f25c43e94275f00c7a5140
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901884"
---
# <a name="set-up-security-info-preview-to-use-security-questions"></a>セキュリティの質問を使用するようにセキュリティ情報 (プレビュー) を設定する
次の手順に従って、パスワードのリセット メソッドを追加できます。 これを初回に設定したら、**[セキュリティ情報]** ページに戻り、セキュリティ情報を追加、更新、または削除できます。

パスワードのリセット メソッドを設定した後、[Authenticator アプリ](security-info-setup-auth-app.md)、[テキスト メッセージ](security-info-setup-text-msg.md)、または[電話呼び出し](security-info-setup-phone-number.md)を使用して、2 要素認証メソッドも設定する必要があります。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>[セキュリティ情報] ページでセキュリティの質問を設定する
組織の設定によっては、セキュリティ情報メソッドの 1 つとして、いくつかのセキュリティの質問を選択して回答することができる場合があります。 管理者は、選択および回答する必要があるセキュリティの質問の数を設定します。

セキュリティの質問を使用する場合、別の方法と併用することをお勧めします。 セキュリティの質問は、他の人が自分の質問に対する回答を知っている可能性があるため、他の方法に比べて安全性が低い可能性があります。

> [!Note]
> セキュリティの質問は、ディレクトリ内のユーザー オブジェクトに非公開かつ安全に保存され、登録時にユーザーだけが回答できます。 管理者がユーザーの質問または回答を読み取ったり変更したりする方法はありません。
> 
> セキュリティの質問オプションが表示されない場合、検証にセキュリティの質問を使用することを組織が許可していない可能性があります。 その場合、別の方法を選択するか、管理者に支援を要請する必要があります。
> 
> 管理者アカウントでは、パスワードのリセット方法としてセキュリティの質問を使用することはできません。 管理者レベルのアカウントとしてログインしている場合、これらのオプションは表示されません。

### <a name="to-set-up-your-security-questions"></a>セキュリティの質問を設定するには

1. 職場または学校アカウントにサインインした後、、 https://myprofile.microsoft.com/ ページに移動します。

    ![強調表示されているセキュリティ情報リンクを示す [マイ プロファイル] ページ](media/security-info/securityinfo-myprofile-page.png)

2. 左側のナビゲーション ウィンドウから、または **[セキュリティ情報]** ブロックのリンクから **[セキュリティ情報]** を選択した後、**[セキュリティ情報]** ページで **[メソッドの追加]** を選択します。

    ![[メソッドの追加] オプションが強調表示されている [セキュリティ情報] ページ](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. **[メソッドの追加]** ページで、ドロップダウン リストから **[秘密の質問]** を選択し、**[追加]** を選択します。

    ![セキュリティの質問が選択された [メソッドの追加] ボックス](media/security-info/securityinfo-myprofile-addquestions.png)

4. **[秘密の質問]** ページで、セキュリティの質問を選択して回答し、**[保存]** を選択します。

    ![電話番号を追加し、電話呼び出しを選択する](media/security-info/securityinfo-myprofile-securityquestions.png)

    セキュリティ情報が更新され、パスワードのリセットを使用するときに、自分のセキュリティの質問を使用して本人確認をできるようになります。

## <a name="delete-security-questions-from-your-security-info-methods"></a>セキュリティ情報メソッドからセキュリティの質問を削除する
セキュリティ情報メソッドとしてセキュリティの質問を使用しなくなった場合は、**[セキュリティ情報]** ページから削除できます。

>[!Important]
>セキュリティの質問を誤って削除した場合、元に戻す方法はありません。 この記事の「[セキュリティの質問を設定するには](#set-up-your-security-questions-from-the-security-info-page)」セクションの手順に従って、メソッドを再度追加する必要があります。

### <a name="to-delete-your-security-questions"></a>セキュリティの質問を削除するには

1. **[セキュリティ情報]** ページで、**[秘密の質問]** オプションの横にある **[削除]** リンクを選択します。

    ![セキュリティ情報から電話番号メソッドを削除するためのリンク](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. 確認ボックスで **[はい]** を選択して、**セキュリティの質問**を削除します。 セキュリティの質問を削除すると、メソッドがセキュリティ情報から削除され、**[セキュリティ情報]** ページから消去されます。

## <a name="additional-security-info-methods"></a>追加のセキュリティ情報メソッド
操作内容に基づき、本人確認のために組織から連絡が届きますが、その方法には追加の選択肢があります。 選択肢は次のようになっています。

- **認証アプリ。** 認証アプリをダウンロードして使用する場合、2 段階認証やパスワード リセットのために承認通知かランダムに生成された承認コードを取得できます。 Microsoft Authenticator アプリの設定方法と使用方法に関する段階的な説明が必要な場合は、「[Set up security info to use an authenticator app](security-info-setup-auth-app.md)」(認証アプリを使用するようにセキュリティ情報を設定する) を参照してください。

- **モバイル デバイスのテキスト。** モバイル デバイスの番号を入力し、2 段階認証やパスワード リセットに使用するテキスト コードを取得します。 テキスト メッセージ (SMS) による本人確認方法に関する段階的な説明が必要な場合は、「[Set up security info to use text messaging (SMS)](security-info-setup-text-msg.md)」(テキスト メッセージ (SMS) を使用するようにセキュリティ情報を設定する) を参照してください。

- **モバイル デバイスまたは職場の電話の呼び出し。** モバイル デバイスの番号を入力し、電話の呼び出しで 2 段階認証やパスワード リセットを行います。 電話番号による本人確認方法に関する段階的説明が必要な場合、「[電話を使用するようにセキュリティ情報を設定する](security-info-setup-phone-number.md)」を参照してください。

- **メール アドレス。** 職場または学校のメール アドレスを入力して、パスワードをリセットするためのメールを受け取ります。 このオプションは、2 段階認証用には使用できません。 メールの設定方法に関する段階的説明が必要な場合、「[Set up security info to use email (メールを使用するようにセキュリティ情報を設定する)](security-info-setup-email.md)」を参照してください。
   
    >[!Note]
    >一部の選択肢が表示されない場合、おそらく、組織がその方法を許可していません。 その場合、選択できる方法を選択するか、管理者に支援を要請する必要があります。

## <a name="next-steps"></a>次の手順

- [パスワード リセット ポータル](https://passwordreset.microsoftonline.com/)を使用するか、「[職場または学校のパスワードをリセットする](user-help-reset-password.md)」の手順に従って、パスワードをリセットする (パスワードをなくしたか忘れた場合)。

- 「[Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)」を参照して、サインイン問題の解決のヒントやヘルプを確認する。
