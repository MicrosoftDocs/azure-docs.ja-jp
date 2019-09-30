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
ms.date: 09/04/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 923c828a3d7e9e10d79fcf079839b84c2f0e0f77
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772385"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>Azure Active Directory でカスタム ロールの割り当てを表示する

この記事では、Azure Active Directory (Azure AD) で割り当てたカスタム ロールを表示する方法について説明します。 Azure Active Directory (Azure AD) では、ロールを組織全体のスコープまたは単一アプリケーションのスコープで割り当てることができます。

- 組織全体のスコープでのロールの割り当ては、単一アプリケーションのロールの割り当ての一覧に追加され、そこで確認することができます。
- 単一アプリケーションのスコープでのロールの割り当ては、組織全体のスコープでの割り当ての一覧に追加されることはなく、そこで確認することもできません。

## <a name="view-role-assignments-in-the-azure-portal"></a>Azure portal でロールの割り当てを表示する

この手順では、組織全体のスコープを使用してロールの割り当てを表示する方法について説明します。

1. Azure AD 組織の特権ロール管理者またはグローバル管理者のアクセス許可を使用して  [Azure AD 管理センター](https://aad.portal.azure.com) にサインインします。
1.  **[Azure Active Directory]** を選択し、 **[ロールと管理者]** を選択します。次に、ロールを選択して開き、そのプロパティを表示します。
1. **[割り当て]** を選択して、そのロールの割り当てを表示します。

    ![一覧からロールを開いたときに、ロールの割り当てとアクセス許可を表示する](./media/roles-view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>Azure AD PowerShell を使用してロールの割り当てを表示する

このセクションでは、組織全体のスコープを使用してロールの割り当てを表示する方法について説明します。 この記事では、[Azure Active Directory PowerShell バージョン 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) モジュールを使います。 PowerShell を使用して単一アプリケーションのスコープを表示するには、[PowerShell を使用したカスタム ロールの割り当て](roles-assign-powershell.md)に関するページのコマンドレットを使用できます。

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

## <a name="view-role-assignments-using-microsoft-graph-api"></a>Microsoft Graph API を使用してロールの割り当てを表示する

このセクションでは、組織全体のスコープを使用してロールの割り当てを表示する方法について説明します。  Graph API を使用して単一アプリケーションのスコープの割り当てを表示するには、[Graph API を使用したカスタム ロールの割り当て](roles-assign-graph.md)に関するページの操作を使用できます。

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

## <a name="view-assignments-of-single-application-scope"></a>単一アプリケーションのスコープの割り当てを表示する

このセクションでは、単一アプリケーションのスコープのロールの割り当てを表示する方法について説明します。 現在、この機能はパブリック プレビュー段階にあります。

1. Azure AD 組織の特権ロール管理者またはグローバル管理者のアクセス許可を使用して  [Azure AD 管理センター](https://aad.portal.azure.com) にサインインします。
1. **[アプリの登録]** を選択し、次にそのアプリの登録を選択してそのプロパティを表示します。 Azure AD 組織内のアプリ登録の完全な一覧を表示するには、 **[すべてのアプリケーション]** を選択する必要がある場合があります。

    ![[アプリの登録] ページからアプリの登録を作成または編集する](./media/roles-create-custom/appreg-all-apps.png)

1. アプリの登録で、 **[ロールと管理者]** を選択し、次にロールを選択してそのプロパティを表示します。

    ![[アプリの登録] ページから、アプリの登録のロールの割り当てを表示する](./media/roles-view-assignments/appreg-assignments.png)

1. **[割り当て]** を選択して、そのロールの割り当てを表示します。 アプリの登録内から [割り当て] ビューを開くと、この Azure AD リソースをスコープとした割り当てが表示されます。

    ![アプリの登録のプロパティから、アプリの登録のロールの割り当てを表示する](./media/roles-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>次の手順

* [Azure AD 管理ロール フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)でご意見をお寄せください。
* ロールと管理者ロールの割り当ての詳細については、[管理者ロールの割り当て](directory-assign-admin-roles.md)に関するページを参照してください。
* 既定のユーザー アクセス許可については、[既定のゲストとメンバー ユーザーのアクセス許可の比較](../fundamentals/users-default-permissions.md)を参照してください。
