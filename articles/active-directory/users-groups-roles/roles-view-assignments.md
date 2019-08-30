---
title: 管理センターで管理者ロールのアクセス許可を表示する - Azure Active Directory | Microsoft Docs
description: Azure AD 管理センターで Azure AD 管理者ロールのメンバーを表示して管理できるようになりました。
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf45d45481712e30d40bacec9a3c4d80d1ed56b6
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707432"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>Azure Active Directory でカスタム ロールの割り当てを表示する

この記事では、Azure Active Directory (Azure AD) で割り当てたカスタム ロールを表示する方法について説明します。 Azure Active Directory (Azure AD) では、ロールはディレクトリ レベルで、または単一のアプリケーションのスコープを使用して割り当てることができます。 ディレクトリ スコープでのロールの割り当ては、単一アプリケーション ロールの割り当ての一覧に追加されますが、単一アプリケーション スコープでのロールの割り当ては、ディレクトリ レベルの割り当ての一覧に追加されません。

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-the-azure-ad-portal"></a>Azure AD ポータルを使って、ディレクトリ スコープでのロールの割り当てを表示する

1. Azure AD 組織の特権ロール管理者またはグローバル管理者のアクセス許可を使用して  [Azure AD 管理センター](https://aad.portal.azure.com) にサインインします。
1.  **[Azure Active Directory]** を選択し、 **[ロールと管理者]** を選択します。次に、ロールを選択して開き、そのプロパティを表示します。
1. **[割り当て]** を選択して、そのロールの割り当てを表示します。

    ![一覧からロールを開いたときに、ロールの割り当てとアクセス許可を表示する](./media/roles-view-assignments/role-assignments.png)

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-azure-ad-powershell"></a>Azure AD PowerShell を使って、ディレクトリ スコープでロールの割り当てを表示する

Azure PowerShell を使って、Azure AD 管理者のロールをユーザーに割り当てる方法を自動化することができます。 この記事では、[Azure Active Directory PowerShell バージョン 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) モジュールを使います。

### <a name="prepare-powershell"></a>PowerShell を準備する

最初に、[Azure AD プレビュー PowerShell モジュールをダウンロード](https://www.powershellgallery.com/packages/AzureAD/)する必要があります。

Azure AD PowerShell モジュールをインストールするには、次のコマンドを使用します。

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

モジュールを使用する準備ができているかどうかを確認するには、次のコマンドを使用します。

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="view-the-assignments-of-a-role"></a>ロールの割り当てを表示する

ロールの割り当てを表示する例。

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-microsoft-graph-api"></a>Microsoft Graph API を使って、ディレクトリ スコープでロールの割り当てを表示する

特定のロールの定義に対するロールの割り当てを取得するための HTTP 要求。

GET

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Response

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview"></a>Azure AD ポータルを使って、単一アプリケーション スコープでロールの割り当てを表示する (プレビュー)

1. Azure AD 組織の特権ロール管理者またはグローバル管理者のアクセス許可を使用して  [Azure AD 管理センター](https://aad.portal.azure.com) にサインインします。
1. Azure Active Directory を選択し、 **[アプリの登録]** を選択します。次に、そのアプリの登録を選択してそのプロパティを表示します。 Azure AD 組織内のアプリ登録の完全な一覧を表示するには、 **[すべてのアプリケーション]** を選択する必要がある場合があります。

    ![[アプリの登録] ページからアプリの登録を作成または編集する](./media/roles-create-custom/appreg-all-apps.png)

1.  **[ロールと管理者]** を選択し、次にロールを選択してそのプロパティを表示します。

    ![[アプリの登録] ページから、アプリの登録のロールの割り当てを表示する](./media/roles-view-assignments/appreg-assignments.png)

1. **[割り当て]** を選択して、そのロールの割り当てを表示します。

    ![アプリの登録のプロパティから、アプリの登録のロールの割り当てを表示する](./media/roles-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>次の手順

* [Azure AD 管理ロール フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)でご意見をお寄せください。
* ロールと管理者ロールの割り当ての詳細については、[管理者ロールの割り当て](directory-assign-admin-roles.md)に関するページを参照してください。
* 既定のユーザー アクセス許可については、[既定のゲストとメンバー ユーザーのアクセス許可の比較](../fundamentals/users-default-permissions.md)を参照してください。
