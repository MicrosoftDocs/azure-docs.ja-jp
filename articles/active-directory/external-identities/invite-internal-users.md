---
title: 内部ユーザーを B2B コラボレーションに招待する - Azure AD
description: パートナー、販売代理店、仕入先、製造元、およびその他のゲスト用の内部ユーザー アカウントがある場合は、ユーザー独自の外部資格情報またはログインを使用してサインインするように招待することによって Azure AD B2B コラボレーションに変更できます。 PowerShell または Microsoft Graph の招待 API を使用します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 802307a21873d15242c2e387ec0defe35f50bb20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99576432"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>内部ユーザーを B2B コラボレーションに招待する

Azure AD B2B コラボレーションが利用できるようになる前は、組織は販売代理店、仕入先、製造元、およびその他のゲスト ユーザーの内部資格情報を設定することによって、これらのユーザーと共同作業を行うことができていました。 このような内部ゲスト ユーザーがいる場合、代わりに B2B コラボレーションを使用するように招待することができます。 これらの B2B ゲスト ユーザーが自分自身の ID と資格情報を使用してサインインできるようになるため、管理者がパスワードを保持し、アカウントのライフサイクルを管理する必要がなくなります。

既存の内部アカウントに招待を送信することにより、そのユーザーのオブジェクト ID、UPN、グループ メンバーシップ、およびアプリの割り当てを保持できます。 ユーザーを手動で削除して再び招待したり、リソースを再割り当てしたりする必要はありません。 ユーザーを招待するには、招待 API を使用して、内部ユーザー オブジェクトとゲスト ユーザーの電子メール アドレスの両方を招待と共に渡します。 ユーザーが招待に同意すると、B2B サービスによって既存の内部ユーザー オブジェクトが B2B ユーザーに変更されます。 これ以降、ユーザーは B2B 資格情報を使用してクラウド リソース サービスにサインインする必要があります。

## <a name="things-to-consider"></a>考慮事項

- **オンプレミスのリソースへのアクセス**:ユーザーは、B2B コラボレーションに招待された後も、内部資格情報を使用してオンプレミスのリソースにアクセスできます。 内部アカウントのパスワードをリセットまたは変更することで、これを防ぐことができます。 例外は、[電子メール ワンタイム パスコード認証](one-time-passcode.md)です。ユーザーの認証方法がワンタイム パスコードに変更された場合、そのユーザーは内部資格情報を使用できなくなります。

- **課金**:この機能では、ユーザーの UserType が変更されないため、ユーザーの課金モデルが、[External Identities の月間アクティブ ユーザー (MAU)](external-identities-pricing.md) に自動的に切り替わることはありません。 ユーザーの MAU の価格をアクティブにするには、ユーザーの UserType を `guest` に変更します。 また、MAU の課金をアクティブにするには、Azure AD テナントを [Azure サブスクリプションにリンクする](external-identities-pricing.md#link-your-azure-ad-tenant-to-a-subscription)必要があることにご注意ください。

- **招待は一方向**:B2B コラボレーションを使用するように内部ユーザーを招待することはできますが、B2B 資格情報を追加した後に削除することはできません。 ユーザーを内部専用ユーザーに戻すよう変更するには、ユーザー オブジェクトを削除して、新しいものを作成する必要があります。

- **Teams**:ユーザーが外部資格情報を使用して Teams にアクセスしたとき、初期状態では Teams テナント ピッカーでそのテナントを使用できません。 ユーザーは、テナント コンテキストを含む URL (例: `https://team.microsoft.com/?tenantId=<TenantId>`) を使用して Teams にアクセスできます。 その後は、Teams テナント ピッカーでテナントを使用できるようになります。

- **オンプレミスの同期されたユーザー**:オンプレミスとクラウドの間で同期されたユーザー アカウントの場合、B2B コラボレーションを使用するように招待された後も、オンプレミス ディレクトリが引き続き権限ソースとなります。 アカウントの無効化や削除を含む、オンプレミスのアカウントに対して行ったすべての変更は、クラウド アカウントに同期されます。 そのため、単にオンプレミスのアカウントを削除するだけでは、ユーザーがクラウド アカウントを維持しながらオンプレミスのアカウントにサインインできないようにすることはできません。 その代わりに、オンプレミスのアカウントのパスワードをランダムな GUID またはその他の不明な値に設定することができます。

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>B2B コラボレーションに内部ユーザーを招待する方法

PowerShell または招待 API を使用して、B2B 招待を内部ユーザーに送信できます。 招待に使用する電子メール アドレスが、内部ユーザー オブジェクトで外部電子メール アドレスとして設定されていることを確認します。

- 招待には、User.Mail プロパティのメール アドレスを使用必要があります。
- ユーザーの Mail プロパティのドメインは、サインインに使用しているアカウントと一致している必要があります。 そうしないと、Teams などの一部のサービスがユーザーを認証できなくなります。

既定では、招待により、招待されたことを知らせる電子メールがユーザーに送信されますが、この電子メールを使用せず、代わりに独自のものを送信することもできます。

> [!NOTE]
> 独自の電子メールまたはその他の情報を送信するには、`New-AzureADMSInvitation` に `-SendInvitationMessage:$false` を指定してユーザーをサイレントに招待し、変換されたユーザーに独自の電子メール メッセージを送信できます。 [Azure AD B2B コラボレーションの API とカスタマイズ](customize-invitation-api.md)に関する記事を参照してください。

## <a name="use-powershell-to-send-a-b2b-invitation"></a>PowerShell を使用して B2B の招待を送信する

Azure AD PowerShell モジュール バージョン 2.0.2.130 以降が必要です。 次のコマンドを使用して、最新の AzureAD PowerShell モジュールに更新し、B2B コラボレーションに内部ユーザーを招待します。

```powershell
Uninstall-Module AzureAD
Install-Module AzureAD
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
