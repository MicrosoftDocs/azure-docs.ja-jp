---
title: セキュリティ キーを検証方法として設定する - Azure AD
description: Fast Identity Online (FIDO2) セキュリティ キーを検証方法として使用して ID を検証するように [Security info (preview)] (セキュリティ情報 (プレビュー)) ページを設定する方法。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: c056e439deac71417ff14dcfc3f2c3c95db41946
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88797668"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>セキュリティ キーを検証方法として設定する

セキュリティ キーは、組織内でパスワードなしのサインイン方法として使用できます。 セキュリティ キーは、職場または学校アカウントにサインインするために一意の PIN と共に使用される物理デバイスです。 セキュリティ キーを使用するには物理デバイスと自分だけが知っている何かが必要なため、それはユーザー名とパスワードより強力な検証方法と見なされています。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> セキュリティ キー オプションが表示されない場合、検証に電話呼び出しを使用することを組織が許可していない可能性があります。 この場合、別の方法を選択するか、組織のヘルプ デスクに連絡して、さらに支援を依頼する必要があります。

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

## <a name="what-is-a-security-key"></a>セキュリティ キーとは

現在、[Fast Identity Online (FIDO)](https://fidoalliance.org/fido2/) (FIDO2) というパスワードなしの認証プロトコルを使用するセキュリティ キーの設計とプロバイダーが複数サポートされています。 これらのキーを使用すると、サポートされているデバイスと Web ブラウザーで職場または学校アカウントにサインインして、組織のクラウドベースのリソースにアクセスできます。

職場または学校アカウントにセキュリティ キーが必要な場合は、管理者または組織から提供されます。 使用できるセキュリティ キーには、デバイスに差し込む USB キーや NFC リーダーでタップする NFC キーなど、さまざまな種類があります。 ご使用のセキュリティ キーの詳細 (種類など) については、製造元のドキュメントを参照してください。

> [!Note]
> FIDO2 セキュリティ キーを使用できない場合は、Microsoft Authenticator アプリや Windows Hello など、パスワードなしの他の検証方法を使用できます。 Microsoft Authenticator アプリの詳細については、「[Microsoft Authenticator アプリとは](user-help-auth-app-overview.md)」を参照してください Windows Hello の詳細については、[Windows Hello の概要](https://www.microsoft.com/windows/windows-hello)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

セキュリティ キーを登録する前に、次の条件を満たす必要があります。

- 管理者がこの機能を組織内で使用できる状態にしている。

- Windows 10 2019 年 5 月の更新プログラムを実行しているデバイスで、サポートされているブラウザーを使用している。

- 管理者または組織によって承認された物理的なセキュリティ キーがある。 このセキュリティ キーは FIDO2 と Microsoft の両方に準拠している必要があります。 セキュリティ キーやその互換性について不明な点がある場合は、所属組織のヘルプ デスクにお問い合わせください。

## <a name="register-your-security-key"></a>セキュリティ キーの登録

セキュリティ キーを使用して職場または学校アカウントにサインインするには、事前にセキュリティ キーを作成し、一意の PIN を与える必要があります。 お使いのアカウントには、最大で 10 個のキーを登録できます。 

1. [https://myprofile.microsoft.com](https://myaccount.microsoft.com ) で **[マイ プロファイル]** ページにアクセスし、まだサインインしていない場合はサインインします。

2. **[セキュリティ情報]** 、 **[方法の追加]** の順に選択し、 **[方法を追加します]** ドロップダウン リストで **[セキュリティ キー]** を選択します。

    ![セキュリティ キーが選択された [方法の追加] ボックス](media/security-info/security-info-security-key-add-method.png)

3. **[追加]** を選択し、所有しているセキュリティ キーの種類 ( **[USB デバイス]** または **[NFC デバイス]** ) を選択します。

    ![所有しているセキュリティ キーの種類として USB または NFC を選択](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > 所有しているセキュリティ キーの種類がわからない場合は、製造元のドキュメントを参照してください。 製造元が不明な場合は、所属組織のヘルプ デスクにお問い合わせください。

4. セキュリティ キーを手元に準備し、 **[セキュリティ キー]** ボックスで **[次へ]** を選択します。

    ![セキュリティ キーの登録を開始するボックス](media/security-info/security-info-security-key-start-setup.png)

    新しいサインイン方法を設定するための新たなボックスが表示されます。

5. **[新しいサインイン方法の設定]** ボックスで **[次へ]** を選択し、次の操作を行います。

    - セキュリティ キーが USB デバイスの場合は、セキュリティ キーをデバイスの USB ポートに挿入します。

    - セキュリティ キーが NFC デバイスの場合は、リーダーに対してセキュリティ キーをタップします。

6. **[Windows セキュリティ]** ボックスに一意のセキュリティ キー PIN を入力し、 **[OK]** を選択します。

    **[新しいサインイン方法の設定]** ボックスに戻ります。

7. **[次へ]** を選択します。

8. **[セキュリティ情報]** ページに戻り、新しいセキュリティ キーに対して後で識別するための名前を入力し、 **[次へ]** を選択します。

    ![[セキュリティ情報] ページでセキュリティ キーに名前を付ける](media/security-info/security-info-security-key-name.png)

    セキュリティ キーが登録され、職場または学校アカウントへのサインインに使用できるようになります。

9. **[完了]** を選択して **[セキュリティ キー]** ボックスを閉じます。

    お使いのセキュリティ キー情報で **[セキュリティ情報]** ページが更新されます。

    ![登録した方法がすべて表示されている [セキュリティ情報] ページ](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>セキュリティ情報からのセキュリティ キーの削除

紛失したセキュリティ キーや、不要となったセキュリティ キーは、セキュリティ情報から削除できます。 これにより職場または学校アカウントでセキュリティ キーが使用されなくなりますが、セキュリティ キーには自分のデータと資格情報が格納されたままとなります。 セキュリティ キー自体から自分のデータと資格情報を削除するには、この記事の [Microsoft と互換性のあるセキュリティ キーのリセット](#reset-your-security-key)に関するセクションの手順に従う必要があります。

1. 削除するセキュリティ キーの **[削除]** リンクを選択します。

2. **[Delete security key]\(セキュリティ キーの削除\)** ボックスで **[OK]** を選択します。

    セキュリティ キーが削除され、職場または学校アカウントへのサインインに使用できなくなります。

>[!Important]
>セキュリティ キーを誤って削除した場合は、この記事の[セキュリティ キーの登録方法](#register-your-security-key)に関するセクションの手順に従って、もう一度登録できます。

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Windows 設定からのセキュリティ キー設定の管理

**Windows 設定** アプリからセキュリティ キーの設定を管理することができます (セキュリティ キーのリセットやセキュリティ キー PIN の新規作成など)。

### <a name="reset-your-security-key"></a>セキュリティ キーのリセット

物理的なセキュリティ キーに格納されているアカウント情報をすべて削除する場合は、キーを出荷時の既定値に戻す必要があります。 セキュリティ キーをリセットすると、キーからすべてのデータが削除されて、最初からやり直すことができます。

>[!IMPORTANT]
>セキュリティ キーをリセットすると、キーからすべてのデータが削除されて出荷時の既定値にリセットされます。
>
> **すべてのデータと資格情報が消去されます。**

#### <a name="to-reset-your-security-key"></a>セキュリティ キーをリセットするには

1. Windows 設定アプリを開き、 **[アカウント]** 、 **[サインイン オプション]** 、 **[セキュリティ キー]** 、 **[管理]** の順に選択します。

2. セキュリティ キーを USB ポートに挿入するか、NFC リーダーをタップして本人確認を行います。

3. ご使用のセキュリティ キーの製造元に応じて、画面の指示に従います。 ご使用のキーの製造元が画面の指示に表示されない場合は、製造元のサイトで詳細情報を確認してください。

4. **[閉じる]** を選択して **[管理]** 画面を閉じます。

### <a name="create-a-new-security-key-pin"></a>新しいセキュリティ キー PIN の作成

セキュリティ キーに新しいセキュリティ キー PIN を作成することができます。

#### <a name="to-create-a-new-security-key-pin"></a>新しいセキュリティ キー PIN を作成するには

1. Windows 設定アプリを開き、 **[アカウント]** 、 **[サインイン オプション]** 、 **[セキュリティ キー]** 、 **[管理]** の順に選択します。

2. セキュリティ キーを USB ポートに挿入するか、NFC リーダーをタップして本人確認を行います。
3. **[Security Key PIN]\(セキュリティ キー PIN\)** 領域で **[追加]** を選択し、新しいセキュリティ キー PIN を入力して確認してから **[OK]** を選択します。

     セキュリティ キーが新しいセキュリティ キー PIN で更新されて、職場または学校アカウントで使用できるようになります。 PIN を再度変更する場合は、 **[変更]** を選択します。
4. **[閉じる]** を選択して **[管理]** 画面を閉じます。

## <a name="additional-security-info-methods"></a>追加のセキュリティ情報メソッド

セキュリティ キーを登録するには、少なくとも 1 つの追加のセキュリティ検証方法を登録しておく必要があります。 詳細については、[概要](./security-info-setup-auth-app.md)に関するページを参照してください。 

## <a name="next-steps"></a>次のステップ

- パスワードなしの検証方法の詳細については、[Microsoft の Azure AD での FIDO2 セキュリティ キーのパブリック プレビューの開始 (パスワードなしのログインの有効化)](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) に関するブログ、または「[Microsoft Authenticator アプリとは](user-help-auth-app-overview.md)」および [Windows Hello の概要](https://www.microsoft.com/windows/windows-hello)に関する記事を参照してください。

- [Microsoft と互換性のあるセキュリティ キー](/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key)に関する詳細情報を確認する。

- [パスワード リセット ポータル](https://passwordreset.microsoftonline.com/)を使用するか、「[職場または学校のパスワードをリセットする](active-directory-passwords-update-your-own-password.md)」の手順に従って、パスワードをリセットする (パスワードをなくしたか忘れた場合)。

- 「[Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)」の記事でサインインの問題に関するトラブルシューティングのヒントやヘルプを入手する。