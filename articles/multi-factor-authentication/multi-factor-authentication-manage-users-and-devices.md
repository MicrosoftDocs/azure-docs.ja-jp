---
title: "管理者によるユーザーとデバイスの管理 - Azure MFA | Microsoft Docs"
description: "ここでは、追加のセキュリティ確認を強制するなど、ユーザー設定を変更する方法について説明します。"
documentationcenter: 
services: multi-factor-authentication
author: kgremban
manager: femila
editor: yossib
ms.assetid: aac3b922-7cc1-428c-9044-273579aa7b5a
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 847a8bdcf880b56f587f6759058825fd1965d29e
ms.openlocfilehash: 43ab735b91bf3f3f1e9631067827f2c456dd7b72
ms.lasthandoff: 03/01/2017


---
# <a name="managing-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>クラウドでの Azure Multi-factor Authentication によるユーザー設定の管理
管理者は、ユーザーとデバイスの次の設定を管理できます。

* 選択したユーザーについて連絡方法の再指定を必須にする
* ユーザーの既存のアプリ パスワードを削除する
* ユーザーのすべての停止中のデバイスで MFA を復元する

## <a name="require-selected-users-to-provide-contact-methods-again"></a>選択したユーザーについて連絡方法の再指定を必須にする
この設定は、ユーザーのサインイン時に、もう一度登録プロセスを完了することを強制します。 ブラウザー以外のアプリは、ユーザーがアプリ パスワードを持っている場合に引き続き動作しますので注意してください。  ユーザー アプリ パスワードを削除するには、 **[選択したユーザーが生成したすべての既存のアプリ パスワードを削除する]**を選択することもできます。

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>連絡方法をもう一度提供するようユーザーに要求する方法
1. Azure クラシック ポータルにサインインします。
2. 左側の [Active Directory] をクリックします。
3. [ディレクトリ] の下で、連絡方法の再提供を必要とするユーザーのディレクトリをクリックします。
4. 上部の [ユーザー] をクリックします。
5. ページ下部の [Multi-Factor Authentication の管理] をクリックします。 [多要素認証] ページが開きます。
6. 管理するユーザーを見つけ、その名前の横にあるチェック ボックスをオンにします。 上部でビューを変更することが必要になる場合があります。
7. これにより、右側に **[ユーザー設定の管理]** リンクが表示されます。 このボタンをクリックします。
8. **[選択したユーザーについて連絡方法の再指定を必須にする]**チェック ボックスをオンにします。
   ![連絡方法を提供する](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
9. [保存] をクリックします。
10. [閉じる] をクリックします。

## <a name="delete-users-existing-app-passwords"></a>ユーザーの既存のアプリ パスワードを削除する
ユーザーが作成したすべてのアプリ パスワードを削除します。 これらのアプリ パスワードに関連付けられているブラウザー以外のアプリは、新しいアプリ パスワードが作成されるまで機能しなくなります。

### <a name="how-to-delete-users-existing-app-passwords"></a>ユーザーの既存のアプリ パスワードを削除する方法
1. Azure クラシック ポータルにサインインします。
2. 左側の [Active Directory] をクリックします。
3. [ディレクトリ] の下で、アプリ パスワードを削除するユーザーのディレクトリをクリックします。
4. 上部の [ユーザー] をクリックします。
5. ページ下部の [Multi-Factor Authentication の管理] をクリックします。 [多要素認証] ページが開きます。
6. 管理するユーザーを見つけ、その名前の横にあるチェック ボックスをオンにします。 上部でビューを変更することが必要になる場合があります。
7. これにより、右側に **[ユーザー設定の管理]** リンクが表示されます。 このボタンをクリックします。
8. **[選択したユーザーが生成したすべての既存のアプリケーション パスワードを削除する]**チェック ボックスをオンにします。
   ![アプリ パスワードを削除する](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
9. [保存] をクリックします。
10. [閉じる] をクリックします。

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>ユーザーの記憶されたすべてのデバイスで MFA を復元する
Azure Multi-Factor Authentication の構成可能な機能の&1; つに、デバイスを信頼済みとしてマークすることをユーザーが選択できる機能があります。 詳細については、「[Azure Multi-Factor Authentication の設定を構成する](multi-factor-authentication-whats-next.md#remember-multi-factor-authentication-for-devices-that-users-trust)」をご覧ください。

ユーザーは通常使用しているデバイスで、構成可能な日数の間、2 段階認証を省略することを選択できます。 アカウントが侵害されたり、信頼済みデバイスを紛失したりした場合、信頼済みの状態を削除し、2 段階認証を再び要求できる必要があります。

**[記憶されているすべてのデバイスに多要素認証を復元]** 設定は、ユーザーがデバイスを信頼済みとしてマークしているかどうかに関係なく、次回のサインイン時に&2; 段階認証の実行を要求することを意味します。 

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>ユーザーの中断されたすべてのデバイスで MFA を復元する方法
1. Azure クラシック ポータルにサインインします。
2. 左側の [Active Directory] をクリックします。
3. [ディレクトリ] の下で、MFA を復元するユーザーのディレクトリをクリックします。
4. 上部の [ユーザー] をクリックします。
5. ページ下部の [Multi-Factor Authentication の管理] をクリックします。 [多要素認証] ページが開きます。
6. 管理するユーザーを見つけ、その名前の横にあるチェック ボックスをオンにします。 上部でビューを変更することが必要になる場合があります。
7. これにより、右側に **[ユーザー設定の管理]** リンクが表示されます。 それをクリックします。
8. **[記憶されているすべてのデバイスに多要素認証を復元]** チェック ボックスをオンにします。
   ![アプリ パスワードの削除](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. [保存] をクリックします。
10. [閉じる] をクリックします。

