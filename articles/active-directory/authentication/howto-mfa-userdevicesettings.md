---
title: Azure AD Multi-Factor Authentication の認証方法を管理する - Azure Active Directory
description: Azure AD Multi-Factor Authentication の Azure Active Directory ユーザー設定を構成する方法について説明します。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/04/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc0be7e50c5cce511fafd7d8b407626bd57659bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98183134"
---
# <a name="manage-user-authentication-methods-for-azure-ad-multi-factor-authentication"></a>Azure AD Multi-factor Authentication のユーザー認証方法を管理する

Azure AD のユーザーには、2 つの異なる連絡先情報のセットがあります。  

- パブリック プロファイルの連絡先情報。これはユーザー プロファイルで管理され、自分の組織のメンバーに表示されます。 オンプレミスの Active Directory から同期したユーザーの場合、この情報はオンプレミスの Windows Server Active Directory Domain Services で管理されます。
- 認証方法。これは常に非公開で、多要素認証 (MFA) を含む認証にのみ使用されます。 管理者はユーザーの認証方法ブレードでこれらの方法を管理でき、ユーザーは MyAccount の [セキュリティ情報] ページで自分の方法を管理できます。

ユーザーの Azure AD Multi-Factor Authentication 方法を管理する場合、認証管理者は次のことができます。 

1. MFA に使用される電話番号など、特定のユーザーの認証方法を追加します。
1. ユーザーのパスワードをリセットします。
1. ユーザーに MFA の再登録を要求します。
1. 既存の MFA セッションを取り消します。
1. ユーザーの既存のアプリ パスワードを削除します。  

## <a name="add-authentication-methods-for-a-user"></a>ユーザーの認証方法を追加する 

Azure portal または Microsoft Graph を使用して、ユーザーの認証方法を追加できます。  

> [!NOTE]
> セキュリティ上の理由から、パブリック ユーザーの連絡先情報フィールドを使用して MFA を実行しないでください。 代わりに、ユーザーは、MFA に使用する自分の認証方法番号を入力する必要があります。  

:::image type="content" source="media/howto-mfa-userdevicesettings/add-authentication-method-detail.png" alt-text="Azure portal から認証方法を追加する":::

Azure portal 経由でユーザーの認証方法を追加するには:  

1. **Azure Portal** にサインインします。 
1. **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** の順に移動します。 
1. 認証方法を追加する対象のユーザーを選択し、 **[認証方法]** を選択します。  
1. ウィンドウの上部にある **[+ 認証方法の追加]** を選択します。
   1. 方法 (電話番号または電子メール) を選択します。 電子メールは、自己パスワードのリセットに使用できますが、認証には使用できません。 電話番号を追加する場合は、電話の種類を選択し、有効な形式で電話番号を入力します (例: +1 4255551234)。
   1. **[追加]** を選択します。

> [!NOTE]
> プレビュー エクスペリエンスでは、管理者はユーザーが利用可能なすべての認証方法を追加できますが、元のエクスペリエンスでは、電話と代替の電話方法の更新のみが可能です。

### <a name="manage-methods-using-powershell"></a>PowerShell を使用して方法を管理する:  

次のコマンドを使用して、Microsoft.Graph.Identity.Signins PowerShell モジュールをインストールします。 

```powershell
Install-module Microsoft.Graph.Identity.Signins
Connect-MgGraph -Scopes UserAuthenticationMethod.ReadWrite.All
Select-MgProfile -Name beta
```

特定のユーザーについての電話ベースの認証方法を一覧表示します。

```powershell
Get-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com
```

特定のユーザーについての携帯電話認証方法を作成します。

```powershell
New-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -phoneType "mobile" -phoneNumber "+1 7748933135"
```

ユーザーの特定の電話方法を削除します

```powershell
Remove-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -PhoneAuthenticationMethodId 3179e48a-750b-4051-897c-87b9720928f7
```

認証方法は Microsoft Graph API を使用して管理することもできます。詳細については、「[Azure AD 認証方法 API の概要](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)」のドキュメントを参照してください。

## <a name="manage-user-authentication-options"></a>ユーザー認証オプションを管理する

*認証管理者* ロールが割り当てられている場合、パスワードのリセット、MFA の再登録、またはユーザー オブジェクトからの既存の MFA セッションの取り消しをユーザーに要求できます。 ユーザー設定を管理するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側の **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 操作の実行対象のユーザーを選択し、 **[認証方法]** を選択します。 ウィンドウの上部で、ユーザーに対して次のいずれかのオプションを選択します。
   - **[パスワードのリセット]** では、ユーザーのパスワードがリセットされ、次のサインイン時に変更する必要がある一時パスワードが割り当てられます。
   - **[MFA の再登録が必要]** では、ユーザーが次回サインインするときに、新しい MFA 認証方法を設定するように要求されます。
   
      > [!NOTE]
      > ユーザーの現在登録されている認証方法は、管理者が MFA を再登録する必要がある場合は削除されません。 ユーザーが MFA を再登録した後、セキュリティ情報を確認し、使用できなくなった以前に登録された認証方法を削除することをお勧めします。
   
   - **[MFA セッションの取り消し]** では、ユーザーの記憶済み MFA セッションがクリアされ、デバイス上のポリシーによって次回要求されたときに MFA を実行するように要求されます。
   
    :::image type="content" source="media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png" alt-text="Azure portal から認証方法を管理する":::

## <a name="delete-users-existing-app-passwords"></a>ユーザーの既存のアプリ パスワードを削除する

アプリ パスワードを定義したユーザーの場合、管理者はこれらのパスワードを削除して、これらのアプリケーションのレガシ認証が失敗するようにすることも選択できます。 これらのアクションは、ユーザーを支援する必要がある場合や認証方法をリセットする必要がある場合に必要になることがあります。 これらのアプリ パスワードに関連付けられているブラウザー以外のアプリは、新しいアプリ パスワードが作成されるまで機能しなくなります。 

ユーザーのアプリ パスワードを削除するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側の **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. **[Multi-Factor Authentication]** を選択します。 このメニュー オプションを表示するには、必要に応じて右にスクロールします。 次のスクリーンショット例を選択すると、完全な Azure portal ウィンドウとメニューの場所が表示されます。[![Azure AD の [ユーザー] ウィンドウから [Multi-Factor Authentication] を選択する](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. ユーザーまたは管理するユーザーの横にあるチェック ボックスをオンにします。 クイック ステップのオプションの一覧が右側に表示されます。
1. 次の例に示すように、 **[ユーザー設定の管理]** を選択し、 **[選択したユーザーが生成したすべての既存のアプリケーション パスワードを削除する]** チェックボックスをオンにします。![すべての既存のアプリ パスワードを削除する](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. **[保存]** 、 **[閉じる]** の順に選択します。

## <a name="next-steps"></a>次のステップ

この記事では、個々のユーザー設定を構成する方法について説明しました。 Azure AD Multi-Factor Authentication サービスの全体的な設定を構成するには、「[Azure AD Multi-Factor Authentication の設定を構成する](howto-mfa-mfasettings.md)」を参照してください。

ユーザーがヘルプを必要とする場合は、[Azure AD Multi-Factor Authentication のユーザー ガイド](../user-help/multi-factor-authentication-end-user-first-time.md)を参照してください。
