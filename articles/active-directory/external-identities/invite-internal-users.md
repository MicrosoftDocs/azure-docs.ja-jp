---
title: 内部ユーザーを B2B コラボレーションに招待する - Azure AD
description: パートナー、販売代理店、仕入先、製造元、およびその他のゲスト用の内部ユーザー アカウントがある場合は、ユーザー独自の外部資格情報またはログインを使用してサインインするように招待することによって Azure AD B2B コラボレーションに変更できます。 PowerShell または Microsoft Graph の招待 API を使用します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: c03c2c55988df04cc45ef4a1d66d959513c1626d
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87907452"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>内部ユーザーを B2B コラボレーションに招待する

> [!NOTE]
> B2B コラボレーションを使用するための内部ユーザーの招待は、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

Azure AD B2B コラボレーションが利用できるようになる前は、組織は販売代理店、仕入先、製造元、およびその他のゲスト ユーザーの内部資格情報を設定することによって、これらのユーザーと共同作業を行うことができていました。 このような内部ゲスト ユーザーがいる場合は、これらのユーザーを B2B コラボレーションを使用するように招待して、Azure AD B2B のベネフィットを利用することができます。 B2B ゲスト ユーザーが独自の ID と資格情報を使用してサインインできるようになるため、パスワードやアカウントのライフサイクルを管理する必要がなくなります。

既存の内部アカウントに招待を送信することにより、そのユーザーのオブジェクト ID、UPN、グループ メンバーシップ、およびアプリの割り当てを保持できます。 ユーザーを手動で削除して再び招待したり、リソースを再割り当てしたりする必要はありません。 ユーザーを招待するには、招待 API を使用して、内部ユーザー オブジェクトとゲスト ユーザーの電子メール アドレスの両方を招待と共に渡します。 ユーザーが招待に同意すると、B2B サービスによって既存の内部ユーザー オブジェクトが B2B ユーザーに変更されます。 これ以降、ユーザーは B2B 資格情報を使用してクラウド リソース サービスにサインインする必要があります。 引き続き内部資格情報を使用してオンプレミスのリソースにアクセスすることもできますが、内部アカウントのパスワードをリセットまたは変更することでこれを防ぐことができます。

> [!NOTE]
> 招待は一方向の操作です。 B2B コラボレーションを使用するように内部ユーザーを招待することはできますが、B2B 資格情報を追加した後に削除することはできません。 ユーザーを内部専用ユーザーに戻すよう変更するには、ユーザー オブジェクトを削除して、新しいものを作成する必要があります。

パブリック プレビュー中は、この記事で説明されている B2B コラボレーションへの内部ユーザーの招待方法は、次の場合には使用できません。

- 内部ユーザーに Exchange ライセンスが既に割り当てられている。
- ユーザーが、ディレクトリ内で直接フェデレーション用に設定されているドメインに属している。
- 内部ユーザーがクラウド専用アカウントであり、そのメイン アカウントが Azure AD 内に存在しない。

これらの場合、内部ユーザーを B2B ユーザーに変更する必要があるときは、内部アカウントを削除し、B2B コラボレーションの招待をユーザーに送信する必要があります。

**オンプレミスの同期されたユーザー**:オンプレミスとクラウドの間で同期されたユーザー アカウントの場合、B2B コラボレーションを使用するように招待された後も、オンプレミス ディレクトリが引き続き権限ソースとなります。 アカウントの無効化や削除を含む、オンプレミスのアカウントに対して行ったすべての変更は、クラウド アカウントに同期されます。 そのため、単にオンプレミスのアカウントを削除するだけでは、ユーザーがクラウド アカウントを維持しながらオンプレミスのアカウントにサインインできないようにすることはできません。 その代わりに、オンプレミスのアカウントのパスワードをランダムな GUID またはその他の不明な値に設定することができます。

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>B2B コラボレーションに内部ユーザーを招待する方法

PowerShell または招待 API を使用して、B2B 招待を内部ユーザーに送信できます。 招待に使用する電子メール アドレスが、内部ユーザー オブジェクトで外部電子メール アドレスとして設定されていることを確認します。

- クラウド専用ユーザーの場合は、招待の User.OtherMails プロパティの電子メール アドレスを使用します。
- オンプレミスの同期されたユーザーについては、招待の User.Mail プロパティの値を使用する必要があります。
- ユーザーの Mail プロパティのドメインは、サインインに使用しているアカウントと一致している必要があります。 そうしないと、Teams などの一部のサービスがユーザーを認証できなくなります。

既定では、招待により、招待されたことを知らせる電子メールがユーザーに送信されますが、この電子メールを使用せず、代わりに独自のものを送信することもできます。

> [!NOTE]
> 独自の電子メールまたはその他の通信を送信するには、-SendInvitationMessage:$false を指定した New-AzureADMSInvitation を使用してユーザーをサイレントに招待し、変換されたユーザーに独自の電子メール メッセージを送信できます。 [Azure AD B2B コラボレーションの API とカスタマイズ](customize-invitation-api.md)に関する記事を参照してください。

## <a name="use-powershell-to-send-a-b2b-invitation"></a>PowerShell を使用して B2B の招待を送信する

次のコマンドを使用して、B2B コラボレーションにユーザーを招待します。

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
$ADGraphUser = Get-AzureADUser -objectID "UPN of Internal User"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>招待 API を使用して B2B の招待を送信する

次のサンプルは、招待 API を呼び出して、内部ユーザーを B2B ユーザーとして招待する方法を示しています。

```json
POST https://graph.microsoft.com/v1.0/invitations
Authorization: Bearer eyJ0eX...
ContentType: application/json
{
    "invitedUserEmailAddress": "<<external email>>",
    "sendInvitationMessage": true,
    "invitedUserMessageInfo": {
        "messageLanguage": "en-US",
        "ccRecipients": [
            {
                "emailAddress": {
                    "name": null,
                    "address": "<<optional additional notification email>>"
                }
            }
        ],
        "customizedMessageBody": "<<custom message>>"
    },
    "inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",
    "invitedUser": {
        "id": "<<ID for the user you want to convert>>"
    }
}
```

API への応答は、新しいゲストユーザーをディレクトリに招待したときと同じ応答になります。

## <a name="next-steps"></a>次のステップ

- [B2B コラボレーションの招待の利用](redemption-experience.md)
