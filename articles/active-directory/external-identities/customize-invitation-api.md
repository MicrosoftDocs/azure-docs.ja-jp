---
title: B2B コラボレーションの API とカスタマイズ - Azure Active Directory
description: Azure Active Directory B2B コラボレーションは、会社のアプリケーションにビジネス パートナーが選択的にアクセスできるようにすることで会社間のリレーションシップをサポートします。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8160859bb782ee8ffc4fef5ee03b61b6f54be1bb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99548663"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory B2B コラボレーションの API とカスタマイズ

多くのお客様から、組織に最適な方法で招待の処理をカスタマイズできるようにしてほしいという要望がありました。 API を使用すると、そのようにすることができます。 [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](/graph/api/resources/invitation)

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

## <a name="determine-if-a-user-was-already-invited-to-your-directory"></a>ユーザーが既にディレクトリに招待されたかどうかを判断する

招待 API を使用して、ユーザーがリソース テナントに既に存在するかどうかを判断できます。 これは、招待 API を使用してユーザーを招待するアプリを開発している場合に便利です。 既にリソース ディレクトリに存在する場合、ユーザーは招待を受信しません。したがって、最初にクエリを実行して、メールが UPN またはその他のサインイン プロパティとして既に存在するかどうかを確認できます。

1. ユーザーのメール ドメインが、リソース テナントの確認済みドメインに含まれていないことを確認します。
2. リソース テナントで、次のユーザーの取得クエリを使用します。ここで、{0} は、招待するメール アドレスです。

   ```
   “userPrincipalName eq '{0}' or mail eq '{0}' or proxyAddresses/any(x:x eq 'SMTP:{0}') or signInNames/any(x:x eq '{0}') or otherMails/any(x:x eq '{0}')"
   ```

## <a name="authorization-model"></a>承認モデル

API は、以下の承認モードで実行できます。

### <a name="app--user-mode"></a>アプリ + ユーザー モード

このモードでは、API を使用するユーザーは、B2B 招待を作成できるアクセス許可を付与されている必要があります。

### <a name="app-only-mode"></a>アプリのみモード

アプリのみのコンテキストで招待を成功させるには、アプリに User.Invite.All スコープが必要です。

詳しくは、https://developer.microsoft.com/graph/docs/authorization/permission_scopes をご覧ください


## <a name="powershell"></a>PowerShell

PowerShell を使用して、簡単に外部ユーザーを組織に追加および招待できます。 次のコマンドレットを使用して招待を作成します。

```powershell
New-AzureADMSInvitation
```

以下のオプションを使用できます。

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>招待の状態

外部ユーザーに招待を送信した後、**Get-AzureADUser** コマンドレットを使用して、招待が受け取られたかどうかを確認できます。 外部ユーザーに招待が送信されると、Get-AzureADUser の次のプロパティが入力されます。

* **UserState** は、招待が **PendingAcceptance** であるか **Accepted** であるかを示します。
* **UserStateChangedOn** は、**UserState** プロパティに対する最新の変更のタイムスタンプを示します。

**Filter** オプションを使用して、**UserState** で結果をフィルター処理できます。 次の例では、保留中の招待を持っているユーザーのみを表示するように結果をフィルター処理する方法を示しています。 表示するプロパティを指定するための **Format-List** オプションも示しています。 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> AzureAD PowerShell モジュールまたは AzureADPreview PowerShell モジュールが最新バージョンであることを確認してください。 

## <a name="see-also"></a>関連項目

招待 API のリファレンスは、[https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](/graph/api/resources/invitation) で確認できます。

## <a name="next-steps"></a>次のステップ

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)
- [B2B コラボレーションの招待メールの要素](invitation-email-elements.md)
- [B2B コラボレーションの招待の利用](redemption-experience.md)
- [招待を使用せずに B2B コラボレーション ユーザーを追加する](add-user-without-invite.md)