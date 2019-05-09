---
title: Azure AD セルフサービスによるパスワード リセットをカスタマイズする - Azure Active Directory
description: Azure AD のセルフサービスによるパスワード リセットのカスタマイズ オプション
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: d38d93a1c9716cc3a71d904b7b1a46fb8b1c2ee0
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369226"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>セルフ サービスによるパスワードのリセットのための Azure AD 機能のカスタマイズ

Azure Active directory (Azure AD) のセルフサービスによるパスワードのリセット (SSPR) をデプロイする IT プロフェッショナルは、ユーザーのニーズに合わせてエクスペリエンスをカスタマイズできます。

## <a name="customize-the-contact-your-administrator-link"></a>"管理者に問い合わせてください" リンクのカスタマイズ

SSPR が有効になっていない場合でも、ユーザーにはパスワード リセット ポータルに "管理者に問い合わせてください" リンクが表示されます。 ユーザーがこのリンクを選択した場合、次のいずれかが実行されます:

* メールが管理者に送信され、ユーザーのパスワードの変更についての支援が求められます。
* 支援のために指定した URL がユーザーに送信されます。

この連絡先は、ユーザーがサポートのために既に使用しているメール アドレスや Web サイトなどに設定することをお勧めします。

![管理者に送信されるリセット依頼メールのサンプル][Contact]

この連絡先メールは、次の受信者に次の順序で送信されます。

1. **パスワード管理者**ロールが割り当てられている場合は、このロールを持つ管理者が通知を受け取ります。
2. パスワード管理者が割り当てられていない場合は、**ユーザー管理者**ロールを持つ管理者が通知を受け取ります。
3. 上記のどちらのロールも割り当てられていない場合は、**グローバル管理者**が通知を受け取ります。

どの場合も、最大 100 人の受信者が通知を受け取ります。

さまざまな管理者ロールと、それらの管理者ロールを割り当てる方法の詳細については、「[Azure Active Directory での管理者ロールの割り当て](../users-groups-roles/directory-assign-admin-roles.md)」を参照してください。

### <a name="disable-contact-your-administrator-emails"></a>"管理者に問い合わせてください" メールを無効にする

組織でパスワードのリセット要求を管理者に通知しないようにする場合は、以下の構成を有効にできます。

* すべてのエンド ユーザーに対してセルフ サービスによるパスワードのリセットを有効にします。 このオプションは **[パスワード リセット]** > **[プロパティ]** の下にあります。 ユーザーに自分のパスワードをリセットさせたくない場合は、アクセスの対象を空のグループにすることができます。 *このオプションは推奨されません。*
* ヘルプデスクのリンクをカスタマイズして、ユーザーがヘルプを得るために使用できる Web URL または mailto: アドレスを指定します。 このオプションは **[パスワード リセット]** > **[カスタマイズ]** > **[カスタム ヘルプデスクの電子メールまたは URL]** の下にあります。

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>SSPR の AD FS サインイン ページをカスタマイズする

Active Directory Federation Services (AD FS) 管理者は、[サインイン ページの説明の追加](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description)の記事のガイダンスを参考にして、サインイン ページへのリンクを追加できます。

AD FS サインイン ページにリンクを追加するには、AD FS サーバーで次のコマンドを使用します。 ユーザーは、このページを使用して、SSPR ワークフローを入力できます。

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>サインイン ページとアクセス パネルの外観のカスタマイズ

サインイン ページをカスタマイズできます。 会社のブランドに合ったイメージと一緒に表示されるロゴを追加できます。

選択したグラフィックスは、次の状況で表示されます。

* ユーザーが自分のユーザー名を入力した後
* ユーザーがカスタマイズされた URL にアクセスした場合:
   * パスワード リセット ページに `whr` パラメーターを渡した場合 (例: `https://login.microsoftonline.com/?whr=contoso.com`)
   * パスワード リセット ページに `username` パラメーターを渡した場合 (例: `https://login.microsoftonline.com/?username=admin@contoso.com`)

会社のブランドを構成する方法の詳細については、[Azure AD のサインイン ページへの会社のブランドの追加](../fundamentals/customize-branding.md)に関する記事を参照してください。

### <a name="directory-name"></a>ディレクトリ名

**[Azure Active Directory]** > **[プロパティ]** で、ディレクトリ名属性を変更できます。 ポータルと自動通信で表示される組織のフレンドリ名を表示できます。 このオプションは、以下の形式の自動メールで最も多く表示されます。

* メールのフレンドリ名 (例: "Microsoft on behalf of CONTOSO demo")
* メールの件名行 (例: "CONTOSO demo account email verification code")

## <a name="next-steps"></a>次の手順

* [SSPR のロールアウトを正常に完了する方法](howto-sspr-deployment.md)
* [パスワードのリセットまたは変更](../user-help/active-directory-passwords-update-your-own-password.md)
* [セルフサービスのパスワード リセットのための登録](../user-help/active-directory-passwords-reset-register.md)
* [ライセンスに関する質問](concept-sspr-licensing.md)
* [SSPR が使用するデータと、ユーザー用に設定するデータ。](howto-sspr-authenticationdata.md)
* [ユーザーが使用できる認証方法](concept-sspr-howitworks.md#authentication-methods)
* [SSPR のポリシー オプション](concept-sspr-policy.md)
* [パスワード ライトバックの概要とその必要性](howto-sspr-writeback.md)
* [SSPR でアクティビティをレポートする方法](howto-sspr-reporting.md)
* [SSPR のすべてのオプションとその意味](concept-sspr-howitworks.md)
* [不具合が発生していると思われるSSPR のトラブルシューティング方法](active-directory-passwords-troubleshoot.md)
* [質問したい内容に関する説明がどこにもない。](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "管理者に連絡してパスワードのリセットを依頼するメールの例"
