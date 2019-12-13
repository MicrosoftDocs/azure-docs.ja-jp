---
title: セルフサービスによるパスワード リセットのカスタマイズ - Azure Active Directory
description: Azure AD のセルフサービスによるパスワード リセットのカスタマイズ オプション
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 039b514fe70da0e300e74bbc98a3a0f4e9ea342c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848597"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>セルフ サービスによるパスワードのリセットのための Azure AD 機能のカスタマイズ

Azure Active directory (Azure AD) のセルフサービスによるパスワードのリセット (SSPR) をデプロイする IT プロフェッショナルは、ユーザーのニーズに合わせてエクスペリエンスをカスタマイズできます。

## <a name="customize-the-contact-your-administrator-link"></a>"管理者に問い合わせてください" リンクのカスタマイズ

パスワード リセットのセルフサービス ユーザーは、パスワード リセット ポータルで [管理者に問い合わせてください] リンクを使用できます。 ユーザーがこのリンクを選択すると、次の 2 つの操作のいずれかが実行されます。

* 既定の状態のままである場合:
   * メールが管理者に送信され、ユーザーのパスワードの変更についての支援が求められます。 後の「[電子メールのサンプル](#sample-email)」をご覧ください。
* カスタマイズされている場合:
   * ユーザーは、支援のために管理者によって指定された Web ページまたはメール アドレスに送られます。

> [!TIP]
> これをカスタマイズする場合は、ユーザーが既にサポートで慣れているものに設定することをお勧めします

> [!WARNING]
> この設定をパスワード リセットが必要なメール アドレスとアカウントでカスタマイズした場合、ユーザーは支援を求めることができない可能性があります。

### <a name="sample-email"></a>電子メールのサンプル

![管理者に送信されるリセット依頼メールのサンプル][Contact]

この連絡先メールは、次の受信者に次の順序で送信されます。

1. **パスワード管理者**ロールが割り当てられている場合は、このロールを持つ管理者が通知を受け取ります。
2. パスワード管理者が割り当てられていない場合は、**ユーザー管理者**ロールを持つ管理者が通知を受け取ります。
3. 上記のどちらのロールも割り当てられていない場合は、**グローバル管理者**が通知を受け取ります。

どの場合も、最大 100 人の受信者が通知を受け取ります。

さまざまな管理者ロールと、それらの管理者ロールを割り当てる方法の詳細については、「[Azure Active Directory での管理者ロールの割り当て](../users-groups-roles/directory-assign-admin-roles.md)」を参照してください。

### <a name="disable-contact-your-administrator-emails"></a>"管理者に問い合わせてください" メールを無効にする

組織でパスワードのリセット要求を管理者に通知しないようにする場合は、以下の構成を有効にできます。

* すべてのエンド ユーザーに対してセルフ サービスによるパスワードのリセットを有効にします。 このオプションは **[パスワード リセット]**  >  **[プロパティ]** の下にあります。 ユーザーに自分のパスワードをリセットさせたくない場合は、アクセスの対象を空のグループにすることができます。 *このオプションは推奨されません。*
* ヘルプデスクのリンクをカスタマイズして、ユーザーがヘルプを得るために使用できる Web URL または mailto: アドレスを指定します。 このオプションは **[パスワード リセット]**  >  **[カスタマイズ]**  >  **[カスタム ヘルプデスクの電子メールまたは URL]** の下にあります。

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

**[Azure Active Directory]**  >  **[プロパティ]** で、ディレクトリ名属性を変更できます。 ポータルと自動通信で表示される組織のフレンドリ名を表示できます。 このオプションは、以下の形式の自動メールで最も多く表示されます。

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
