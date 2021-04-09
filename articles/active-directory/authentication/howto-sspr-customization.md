---
title: セルフサービス パスワード リセットのカスタマイズ - Azure Active Directory
description: Azure AD のセルフサービス パスワード リセットに対するユーザー表示とエクスペリエンスのオプションをカスタマイズする方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3766db0d5070f15216ac612353e2c25737ad092a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96741628"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>Azure Active Directory のセルフサービス パスワード リセットのユーザー エクスペリエンスをカスタマイズする

セルフサービス パスワード リセット (SSPR) により、Azure Active Directory (Azure AD) のユーザーは、管理者やヘルプ デスクに頼らなくても、自分のパスワードを変更またはリセットできるようになります。 ユーザーはアカウントがロックされた場合やパスワードを忘れた場合でも、画面の指示に従って自分自身のブロックを解除して、作業に戻ることができます。 この機能により、ユーザーが自分のデバイスやアプリケーションにサインインできなくなった場合のヘルプ デスクの問い合わせが減り、生産性の喪失も軽減されます。

ユーザーに対する SSPR のエクスペリエンスを向上させるには、パスワード リセット ページ、メール通知、またはサインイン ページのルック アンド フィールをカスタマイズできます。 これらのカスタマイズ オプションを使用することで、ユーザーが正しい場所にいることを明確に示し、会社のリソースにアクセスしていることをユーザーに確信させることができます。
    
この記事では、ユーザーに対する SSPR の電子メール リンク、会社のブランド、AD FS サインイン ページのリンクをカスタマイズする方法について説明します。

## <a name="customize-the-contact-your-administrator-link"></a>"管理者に問い合わせてください" リンクのカスタマイズ

セルフサービス パスワード リセットについてユーザーが支援を受けられるよう、パスワード リセット ポータルには [管理者にお問い合わせください] というリンクが表示されます。 ユーザーがこのリンクを選択すると、次の 2 つのいずれかが実行されます。

* この連絡先リンクが既定の状態のままになっている場合は、メールが管理者に送信され、ユーザーのパスワードの変更についての支援が求められます。 次の電子メールの例では、この既定の電子メール メッセージが示されています。

    ![管理者に送信されるリセット依頼メールのサンプル](./media/howto-sspr-customization/sspr-contact-admin.png)

* カスタマイズされている場合は、ユーザーは支援のために管理者によって指定された Web ページまたはメール アドレスに送られます。
    * これをカスタマイズする場合は、ユーザーが既にサポートで慣れているものに設定することをお勧めします。

    > [!WARNING]
    > この設定をパスワード リセットが必要なメール アドレスとアカウントでカスタマイズした場合、ユーザーは支援を求めることができない可能性があります。

### <a name="default-email-behavior"></a>既定の電子メールの動作

既定の連絡先メールは、次の順序で受信者に送信されます。

1. *ヘルプデスク管理者* ロールまたは *パスワード管理者* ロールが割り当てられている場合は、これらのロールを持つ管理者が通知を受け取ります。
1. ヘルプデスク管理者またはパスワード管理者が割り当てられていない場合は、"*ユーザー管理者*" ロールを持つ管理者が通知を受け取ります。
1. 上記のどのロールも割り当てられていない場合は、*グローバル管理者* が通知を受け取ります。

どの場合も、最大 100 人の受信者が通知を受け取ります。

さまざまな管理者ロールと、それらの管理者ロールを割り当てる方法の詳細については、「[Azure Active Directory での管理者ロールの割り当て](../roles/permissions-reference.md)」を参照してください。

### <a name="disable-contact-your-administrator-emails"></a>"管理者に問い合わせてください" メールを無効にする

組織でパスワードのリセット要求を管理者に通知しないようにする場合は、以下の構成オプションを使用できます。

* ヘルプデスクのリンクをカスタマイズして、ユーザーがヘルプを得るために使用できる Web URL または mailto: アドレスを指定します。 このオプションは **[パスワード リセット]**  >  **[カスタマイズ]**  >  **[カスタム ヘルプデスクの電子メールまたは URL]** の下にあります。
* すべてのユーザーに対してセルフサービス パスワード リセットを有効にします。 このオプションは **[パスワード リセット]**  >  **[プロパティ]** の下にあります。 ユーザーに自分のパスワードをリセットさせたくない場合は、アクセスの対象を空のグループにすることができます。 *このオプションは推奨されません。*

## <a name="customize-the-sign-in-page-and-access-panel"></a>サインイン ページとアクセス パネルをカスタマイズする

会社のブランドに合ったイメージと一緒に表示されるロゴの追加などで、サインイン ページをカスタマイズできます。 会社のブランドを構成する方法の詳細については、[Azure AD のサインイン ページへの会社のブランドの追加](../fundamentals/customize-branding.md)に関する記事を参照してください。

選択したグラフィックスは、次の状況で表示されます。

* ユーザーが自分のユーザー名を入力した後
* ユーザーがカスタマイズされた URL にアクセスした場合:
   * パスワード リセット ページに `whr` パラメーターを渡した場合 (例: `https://login.microsoftonline.com/?whr=contoso.com`)
   * パスワード リセット ページに `username` パラメーターを渡した場合 (例: `https://login.microsoftonline.com/?username=admin@contoso.com`)

### <a name="directory-name"></a>ディレクトリ名

ユーザーにわかりやすくするには、ポータルおよび自動通信の組織名を変更することができます。 Azure portal でディレクトリ名属性を変更するには、 **[Azure Active Directory]**  >  **[プロパティ]** に移動します。 このわかりやすい組織名オプションは、次の例のように、自動化された電子メールでよく見られるものです。

* メールのフレンドリ名 (例: "*CONTOSO の代理としての Microsoft のデモ*")
* メールの件名行 (例: "*CONTOSO デモ アカウントのメール確認コード*")

## <a name="customize-the-ad-fs-sign-in-page"></a>AD FS サインイン ページをカスタマイズする

ユーザー サインイン イベントに Active Directory フェデレーション サービス (AD FS) を使用する場合は、「[サインイン ページの説明を追加する](/windows-server/identity/ad-fs/operations/add-sign-in-page-description)」のガイダンスを参考にして、サインイン ページへのリンクを追加できます。

SSPR ワークフローに入るためのページへのリンクをユーザーに提供します (例: *https://passwordreset.microsoftonline.com* )。 AD FS サインイン ページにリンクを追加するには、AD FS サーバーで次のコマンドを使用します。

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>次のステップ

環境内での SSPR の使用状況を把握するには、「[Azure AD のパスワード管理に関するレポート オプション](howto-sspr-reporting.md)」を参照してください。

SSPR で問題が発生した場合は、「[セルフサービス パスワード リセットのトラブルシューティング](./troubleshoot-sspr.md)」を参照してください