---
title: ゲスト ユーザーのアクセス許可を制限する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory で Azure portal、PowerShell、または Microsoft Graph を使用して、ゲスト ユーザーのアクセス許可を制限します
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 01/14/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf2d0d3335468147575eb53a99940866baa18375
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222523"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>Azure Active Directory でゲストのアクセス許可を制限する (プレビュー)

Azure Active Directory (Azure AD) を使用すると、Azure AD 内の組織で外部のゲスト ユーザーに表示される内容を制限することができます。 既定では、ゲスト ユーザーは Azure AD で制限されたアクセス許可レベルに設定されますが、メンバー ユーザーの既定値は、既定のユーザー アクセス許可の完全なセットになります。 これは、Azure AD 組織の外部コラボレーション設定における新しいゲスト ユーザーのアクセス許可レベルのプレビューであり、さらに制限されたアクセスが可能になります。そのため、ゲスト アクセスの選択肢は次のようになります。

アクセス許可レベル         | アクセス レベル
----------------         | ------------
メンバー ユーザーと同じ     | ゲストは、Azure AD リソースに対してメンバー ユーザーと同じアクセス権を持っています
制限付きアクセス (既定) | ゲストは、非表示でないすべてのグループのメンバーシップを表示できます
**制限付きアクセス (新規)**  | **ゲストは、どのグループのメンバーシップも表示できません**

ゲスト アクセスが制限されている場合、ゲストは自分のユーザー プロファイルのみを表示できます。 ゲストがユーザー プリンシパル名または objectId で検索している場合でも、他のユーザーを表示するアクセス許可は許可されません。 制限付きアクセスでは、ゲスト ユーザーが所属しているグループのメンバーシップも表示できないように制限されます。 ゲスト ユーザーのアクセス許可を含めた全体的な既定のユーザー アクセス許可については、「[Azure Active Directory の既定のユーザー アクセス許可とは](../fundamentals/users-default-permissions.md)」をご覧ください。

## <a name="permissions-and-licenses"></a>アクセス許可とライセンス

外部コラボレーション設定を構成するには、全体管理者ロールである必要があります。 ゲスト アクセスを制限するための追加のライセンス要件はありません。

## <a name="update-in-the-azure-portal"></a>Azure portal で更新する

ゲスト ユーザーのアクセス許可に対して既存の Azure portal コントロールを変更しました。

1. 全体管理者アクセス許可を使用して [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. 組織の **Azure Active Directory** 概要ページで、 **[ユーザー設定]** を選択します。
1. **[外部ユーザー]** で、 **[Manage external collaboration settings (外部コラボレーション設定の管理)]** を選択します。
1. **[外部コラボレーションの設定]** ページで、 **[Guest user access is restricted to properties and memberships of their own directory objects] (ゲスト ユーザーのアクセスを、自分のディレクトリ オブジェクトのプロパティとメンバーシップに制限する)** オプションを選択します。

    ![Azure AD の外部コラボレーション設定ページ](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. **[保存]** を選択します。 変更は、ゲスト ユーザーに対して有効になるまでに最大 15 分かかることがあります。

## <a name="update-with-the-microsoft-graph-api"></a>Microsoft Graph API を使用して更新する

Azure AD 組織のゲスト アクセス許可を構成するための新しい Microsoft Graph API が追加されました。 次の API 呼び出しを実行して、任意のアクセス許可レベルを割り当てることができます。 ここで使用する guestUserRoleId の値は、最も制限の厳しいゲスト ユーザー設定を示すためのものです。 Microsoft Graph を使用してゲストのアクセス許可を設定する方法の詳細については、[authorizationPolicy リソースの種類](/graph/api/resources/authorizationpolicy)に関するページを参照してください。

### <a name="configuring-for-the-first-time"></a>初回の構成

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

応答は Success 204 です。

### <a name="updating-the-existing-value"></a>既存の値の更新

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

応答は Success 204 です。

### <a name="view-the-current-value"></a>現在の値を表示する

````PowerShell
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
````

応答の例:

````PowerShell
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authorizationPolicy/$entity",
    "id": "authorizationPolicy",
    "displayName": "Authorization Policy",
    "description": "Used to manage authorization related settings across the company.",
    "enabledPreviewFeatures": [],
    "guestUserRoleId": "10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "permissionGrantPolicyIdsAssignedToDefaultUserRole": [
        "user-default-legacy"
    ]
}
````

## <a name="update-with-powershell-cmdlets"></a>PowerShell コマンドレットで更新する

この機能では、PowerShell v2 コマンドレットを使用して制限されたアクセス許可を構成できるようにしました。 PowerShell コマンドレットの Get と Set は、バージョン 2.0.2.85 で公開されています。

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Get コマンド:Get-AzureADMSAuthorizationPolicy

例:

````PowerShell
PS C:\WINDOWS\system32> Get-AzureADMSAuthorizationPolicy

Id                                                : authorizationPolicy
OdataType                                         :
Description                                       : Used to manage authorization related settings across the company.
DisplayName                                       : Authorization Policy
EnabledPreviewFeatures                            : {}
GuestUserRoleId                                   : 10dae51f-b6af-4016-8d66-8c2a99b929b3
PermissionGrantPolicyIdsAssignedToDefaultUserRole : {user-default-legacy}
````

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Set コマンド:Set-AzureADMSAuthorizationPolicy

例:

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> 要求された場合は、authorizationPolicy を ID として入力する必要があります。

## <a name="supported-microsoft-365-services"></a>サポートされている Microsoft 365 サービス

### <a name="supported-services"></a>サポートされているサービス

サポートされているということは、エクスペリエンスが期待どおりであるということです。具体的には、現在のゲスト エクスペリエンスと同じです。

- Teams
- Outlook (OWA)
- SharePoint
- Teams 内の Planner
- Planner Web アプリ

### <a name="services-currently-not-supported"></a>現時点ではサポートされていないサービス

現在サポートされていないサービスには、新しいゲスト制限設定に関する互換性の問題がある可能性があります。

- フォーム
- Planner モバイル アプリ
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>よく寄せられる質問 (FAQ)

Question | Answer
-------- | ------
これらのアクセス許可はどこに適用されますか。 | これらのディレクトリ レベルのアクセス許可は、Microsoft Graph、PowerShell v2、Azure portal、マイ アプリ ポータルなどの Azure AD サービスとポータルに適用されます。 コラボレーション シナリオに Microsoft 365 グループを利用する Microsoft 365 サービスも、特に Outlook、Microsoft Teams、および SharePoint が影響を受けます。
制限付きアクセス許可は、ゲストが表示できるグループにどのように影響しますか。 | 既定または制限付きのゲスト アクセス許可に関係なく、ゲストはグループまたはユーザーの一覧を列挙できません。 ゲストは、アクセス許可に応じて、Azure portal とマイ アプリ ポータルの両方で自身がメンバーであるグループを表示できます。<li>**既定のアクセス許可**:ゲストが Azure portal でメンバーであるグループを見つけるには、 **[すべてのユーザー]** 一覧でオブジェクト ID を検索し、 **[グループ]** を選択する必要があります。 ここでは、名前やメール アドレスなど、グループのすべての詳細を含め、メンバーであるグループの一覧を確認できます。 マイ アプリ ポータルでは、所有しているグループとメンバーであるグループの一覧を確認できます。</li><li>**制限付きのゲスト アクセス許可**:Azure portal では、[すべてのユーザー] 一覧でオブジェクト ID を検索し、[グループ] を選択することで、メンバーであるグループの一覧を確認できます。 グループについて確認できるのは、ごく限られた詳細 (特にオブジェクト ID) のみです。 仕様として、[名前] と [メール] の列は空白であり、[グループの種類] は "認識不可" です。 マイ アプリ ポータルでは、所有しているグループまたはメンバーであるグループの一覧にアクセスできません。</li><br>Graph API から取得するディレクトリ アクセス許可の詳細な比較については、[既定のユーザー アクセス許可](../fundamentals/users-default-permissions.md#member-and-guest-users)に関するページを参照してください。
この機能によって影響を受けるのは、マイ アプリ ポータルのどの部分ですか。 | マイ アプリ ポータルのグループ機能では、これらの新しいアクセス許可が優先されます。 これには、マイ アプリのグループ一覧とグループ メンバーシップを表示するためのすべてのパスが含まれます。 グループ タイルの可用性に変更は加えられていません。 グループ タイルの可用性は、Azure portal の既存のグループ設定によって引き続き制御されます。
これらのアクセス許可は、SharePoint または Microsoft Teams のゲスト設定をオーバーライドしますか。 | いいえ。 これらの既存の設定は、引き続きこれらのアプリケーションのエクスペリエンスとアクセスを制御します。 たとえば、SharePoint で問題が発生した場合は、外部共有の設定を再確認してください。
Planner と Yammer には、互換性に関する既知の問題がありますか。 | <li>アクセス許可が "制限" に設定されている場合、Planner モバイル アプリにサインインしたゲストは、プランやタスクにアクセスすることができません。<li>アクセス許可が "制限" に設定されている場合、Yammer にサインインしたゲストはグループから脱退できません。
テナントの既存のゲスト アクセス許可は変更されますか。 | 現在の設定に変更は加えられていません。 既存の設定との下位互換性が維持されています。 変更を行うタイミングを決定してください。
これらのアクセス許可は既定で設定されますか。 | いいえ。 既存の既定のアクセス許可は変更されていません。 必要に応じて、より制限の厳しいアクセス許可を設定することもできます。
この機能のライセンス要件はありますか。 | いいえ。この機能には新しいライセンスの要件はありません。

## <a name="next-steps"></a>次のステップ

- Azure AD での既存のゲスト アクセス許可の詳細については、「[Azure Active Directory の既定のユーザー アクセス許可とは](../fundamentals/users-default-permissions.md)」を参照してください。
- ゲスト アクセスを制限するための Microsoft Graph API のメソッドについては、[authorizationPolicy リソースの種類](/graph/api/resources/authorizationpolicy)に関するページを参照してください。
- ユーザーのすべてのアクセス権を取り消すには、[Azure AD でのユーザー アクセスの取り消し](users-revoke-access.md)に関するページを参照してください。