---
title: 双方向 SMS がサポートされなくなりました - Azure Active Directory
description: 現在も双方向 SMS を使用しているユーザーに対して、別の方法を有効にする方法を説明します。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/02/2021
ms.author: justinha
author: rhicock
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d25ed1e46823ec6d820addf3944c96c97fcabcb8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101689030"
---
# <a name="two-way-sms-unsupported"></a>双方向 SMS のサポート終了

Azure AD Multi-Factor Authentication (MFA) Server の双方向 SMS は、元は 2018 年に非推奨とされていたもので、2021 年 2 月 24 日以降はサポートされなくなりました。 管理者は、現在も双方向 SMS を使用しているユーザーに対して、別の方法を有効にする必要があります。

影響を受ける管理者に対して、2020 年 12 月 8 日と 2021 年 1 月 28 日に、電子メール通知と Azure portal Service Health 通知 （ポータル トースト） が送信されました。 アラートは、サブスクリプションに関連付けられている、所有者、共同所有者、管理者、およびサービス管理者の各 RBAC の役割に送信されました。 次の手順を既に完了している場合は、必要なアクションはありません。

## <a name="required-actions"></a>必要なアクション

1. まだ行っていない場合は、ユーザーのモバイル アプリを有効にします。 詳細については、[MFA Server でのモバイル アプリ認証の有効化](howto-mfaserver-deploy-mobileapp.md)に関する記事を参照してください。
1. エンド ユーザーに対して、モバイル アプリをアクティブにするために MFA Server [ユーザー ポータル](howto-mfaserver-deploy-userportal.md)にアクセスするよう通知します。 [Microsoft Authenticator アプリ](https://www.microsoft.com/en-us/account/authenticator)は、双方向 SMS よりも安全性が高いため、推奨される認証オプションです。 詳細については、「[認証のための電話転送から脱却する時がきました](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752)」を参照してください。
1. 既定の方法として、ユーザー設定を双方向テキスト メッセージからモバイル アプリに変更します。

## <a name="faq"></a>よく寄せられる質問

### <a name="what-if-i-dont-change-the-default-method-from-two-way-sms-to-the-mobile-app"></a>既定の方法を双方向 SMS からモバイル アプリに変更しない場合、どうなりますか?
双方向 SMS は、2021 年 2 月 24 日以降は失敗します。 ユーザーがサインインして MFA を通過しようとすると、エラーが表示されます。

### <a name="how-do-i-change-the-user-settings-from-two-way-text-message-to-mobile-app"></a>ユーザー設定を双方向テキスト メッセージからモバイル アプリに変更するには、どうすればよいですか?

これらの手順に従ってユーザー設定を変更する必要があります。

1. MFA Server で、双方向テキスト メッセージのユーザー一覧をフィルター処理します。
1. [すべてのユーザー] を選択します。
1. [ユーザーの編集] ダイアログが開きます。
1. ユーザーを [テキスト メッセージ] から [モバイル アプリ] に変更します。

   ![エンド ユーザーのスクリーンショット](media/how-to-authentication-two-way-sms-unsupported/end-users.png)

### <a name="do-my-users-need-to-take-any-action-if-yes-how"></a>ユーザーは何らかのアクションをとる必要がありますか? 「はい」の場合、どうすればよいですか?
はい。 エンド ユーザーは、特定の MFA Server ユーザー ポータルにアクセスして、モバイル アプリをアクティブにする必要があります (まだ行っていない場合)。 手順 3 を完了すると、ユーザー ポータルにアクセスしてモバイル アプリを設定しなかったユーザーは、ユーザー ポータルにアクセスして再登録するまでサインインに失敗するようになります。

### <a name="what-if-my-users-cant-install-the-mobile-app-what-other-options-do-they-have"></a>ユーザーがモバイル アプリをインストールできない場合はどうすればよいですか? 他にどのようなオプションがありますか?
双方向 SMS やモバイル アプリの代替手段となるのは、電話呼び出しです。 ただし、推奨される認証方法は Microsoft Authenticator アプリです。

### <a name="will-one-way-sms-be-deprecated-as-well"></a>一方向 SMS も同様に非推奨になりますか?
いいえ。非推奨になるのは双方向 SMS のみです。 MFA Server の場合、一方向 SMS は一部のシナリオで機能します。

- AD FS アダプター
- IIS 認証 (ユーザー ポータルと構成が必要です)
- RADIUS (RADIUS クライアントがアクセス チャレンジをサポートしていることと、PAP プロトコルが使用されていることが必要です)

一方向 SMS を使用できる場合には制限があります。そのため、確認コードのプロンプトを必要としないモバイル アプリがより優れた代替手段になります。
一部のシナリオで引き続き一方向 SMS を使用する場合は、これらのチェックをオンのままにしておくことができますが、 **[会社の設定]** セクションの **[全般]** タブの **[User Defaults Text Message]\(ユーザーの既定のテキスト メッセージ\)** は、 **[双方向]** ではなく **[一方向]** に変更してください。 最後に、既定で SMS に設定されているディレクトリ同期を使用する場合は、それを [双方向] ではなく [一方向] に変更する必要があります。

### <a name="how-can-i-check-which-users-are-still-using-two-way-sms"></a>現在も双方向 SMS を使用しているユーザーを確認するにはどうすればよいですか?
これらのユーザーを一覧表示するには、 **[MFA Server]** を起動し、 **[ユーザー]** セクションを選択してから、 **[ユーザー一覧のフィルター]** をクリックし、 **[Text Message Two-Way]\(テキスト メッセージ双方向\)** でフィルター処理します。

### <a name="how-do-we-hide-two-way-sms-as-an-option-in-the-mfa-portal-to-prevent-users-from-selecting-it-in-the-future"></a>MFA ポータルでオプションとして双方向 SMS を非表示にして、ユーザーが将来選択できないようにするにはどうすればよいですか?
MFA Server ユーザー ポータルで **[設定]** をクリックし、 **[テキスト メッセージ]** をクリアすることで、使用できないようにすることができます。 ユーザー登録に AD FS を使用している場合は **AD FS** セクションでも同じことが当てはまります。

