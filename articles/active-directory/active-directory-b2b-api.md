---
title: "Azure Active Directory B2B コラボレーションの API とカスタマイズ | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーションは、会社のアプリケーションにビジネス パートナーが選択的にアクセスできるようにすることで会社間のリレーションシップをサポートします"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/15/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 6f487e2e8f368240339ef5e0015c85591522e49a
ms.lasthandoff: 03/17/2017


---

# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory B2B コラボレーションの API とカスタマイズ

多くのお客様から、組織に最適な方法で招待の処理をカスタマイズできるようにしてほしいという要望がありました。 API を使用すると、そのようにすることができます。 [https://graph.microsoft.io/en-us/docs/api-reference/beta/resources/invitation](https://graph.microsoft.io/en-us/docs/api-reference/beta/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>招待 API の機能
API には次の機能が用意されています。

1. "*任意の*" 電子メール アドレスで外部ユーザーを招待できます。

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. 招待に応じたユーザーの移動先をカスタマイズできます。

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Microsoft を通じて標準的な招待メールを送信することができます。

    ```
    "sendInvitationMessage": true
    ```

  受信者へのカスタマイズ可能なメッセージを付けることができます。

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. また、このコラボレーターの招待について知らせておきたい人を cc することができます。

5. または、Azure AD を通じて通知を送信しないことを選択して、招待とオンボード ワークフローを完全にカスタマイズすることができます。

    ```
    "sendInvitationMessage": false
    ```

  この場合は、招待に応じるための URL を API から受け取り、それを電子メール テンプレート、IM、またはその他の任意の配布方法に埋め込むことができます。

6. 最後に、管理者である場合は、ユーザーをメンバーとして招待することができます。

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>承認モデル
API は、以下の承認モードで実行できます。

### <a name="app--user-mode"></a>アプリ + ユーザー モード
このモードでは、API を使用するユーザーは、B2B 招待を作成できるアクセス許可を付与されている必要があります。

### <a name="app-only-mode"></a>アプリのみモード
アプリのみのコンテキストで招待を成功させるには、アプリに User.ReadWrite.All または Directory.ReadWrite.All スコープが必要です。

詳しくは、https://graph.microsoft.io/docs/authorization/permission_scopes をご覧ください


## <a name="powershell"></a>PowerShell
PowerShell を使用して、簡単に外部ユーザーを組織に追加および招待できるようになりました。 次のコマンドレットを使用して招待を作成します。

```
New-AzureADMSInvitation
```

以下のオプションを使用できます。

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

[https://graph.microsoft.io/ja-jp/docs/api-reference/beta/resources/invitation](https://graph.microsoft.io/en-us/docs/api-reference/beta/resources/invitation) で招待 API リファレンスを確認することもできます

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-admin-add-users.md)
* [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-iw-add-users.md)
* [B2B コラボレーションの招待メールの要素](active-directory-b2b-invitation-email.md)
* [B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B コラボレーションのライセンス](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B コラボレーションのトラブルシューティング](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)](active-directory-b2b-faq.md)
* [B2B コラボレーション ユーザーの多要素認証](active-directory-b2b-mfa-instructions.md)
* [招待を使用せずに B2B コラボレーション ユーザーを追加する](active-directory-b2b-add-user-without-invite.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)

