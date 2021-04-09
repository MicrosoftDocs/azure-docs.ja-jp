---
title: Azure AD ロールの割り当てを一覧表示する
description: Azure Active Directory 管理センターで Azure Active Directory 管理者ロールのメンバーを表示して管理できるようになりました。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: de546ef091b1a8e996f286b0c9af45e93488b5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467657"
---
# <a name="list-azure-ad-role-assignments"></a>Azure AD ロールの割り当てを一覧表示する

この記事では、Azure Active Directory (Azure AD) で割り当てたロールを一覧表示する方法について説明します。 Azure Active Directory (Azure AD) では、ロールを組織全体のスコープまたは単一アプリケーションのスコープで割り当てることができます。

- 組織全体のスコープでのロールの割り当ては、単一アプリケーションのロールの割り当ての一覧に追加され、そこで確認することができます。
- 単一アプリケーションのスコープでのロールの割り当ては、組織全体のスコープでの割り当ての一覧に追加されることはなく、そこで確認することもできません。

## <a name="list-role-assignments-in-the-azure-portal"></a>Azure portal でロールの割り当てを一覧表示する

この手順では、組織全体のスコープでロールの割り当てを一覧表示する方法について説明します。

1. Azure AD 組織の特権ロール管理者またはグローバル管理者のアクセス許可を使用して [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. **[Azure Active Directory]** を選択し、**[ロールと管理者]** を選択します。次に、ロールを選択して開き、そのプロパティを表示します。
1. ロールの割り当てを一覧表示するには、 **[割り当て]** を選択します。

    ![一覧からロールを開いたときに、ロールの割り当てとアクセス許可を一覧表示する](./media/view-assignments/role-assignments.png)

## <a name="list-my-role-assignments"></a>自分のロールの割り当てを一覧表示する

自分のアクセス許可も簡単に一覧表示することができます。 **[ロールと管理者]** ページの **[ロール]** を選択すると、現在自分に割り当てられているロールが表示されます。

## <a name="download-role-assignments"></a>ロールの割り当てのダウンロード

特定のロールのすべての割り当てをダウンロードするには、 **[ロールと管理者]** ページでロールを選択し、 **[ロールの割り当てのダウンロード]** を選択します。 そのロールのすべてのスコープでの割り当てを一覧表示する CSV ファイルがダウンロードされます。

![ロールのすべての割り当てをダウンロードする](./media/view-assignments/download-role-assignments.png)

## <a name="list-role-assignments-using-azure-ad-powershell"></a>Azure AD PowerShell を使用してロールの割り当てを一覧表示する

このセクションでは、組織全体のスコープを使用してロールの割り当てを表示する方法について説明します。 この記事では、[Azure Active Directory PowerShell バージョン 2](/powershell/module/azuread/#directory_roles) モジュールを使います。 PowerShell を使用して単一アプリケーションのスコープを表示するには、[PowerShell を使用したカスタム ロールの割り当て](custom-assign-powershell.md)に関するページのコマンドレットを使用できます。

### <a name="prepare-powershell"></a>PowerShell を準備する

最初に、[Azure AD プレビュー PowerShell モジュールをダウンロード](https://www.powershellgallery.com/packages/AzureAD/)する必要があります。

Azure AD PowerShell モジュールをインストールするには、次のコマンドを使用します。

``` PowerShell
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
```

モジュールを使用する準備ができているかどうかを確認するには、次のコマンドを使用します。

``` PowerShell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...}
```

### <a name="list-role-assignments"></a>ロールの割り当ての一覧表示

ロールの割り当てを一覧表示する例。

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="list-role-assignments-using-microsoft-graph-api"></a>Microsoft Graph API を使用してロールの割り当てを一覧表示する

このセクションでは、組織全体のスコープでロールの割り当てを一覧表示する方法について説明します。  Graph API を使用して単一アプリケーションのスコープのロールの割り当てを一覧表示するには、[Graph API を使用したカスタム ロールの割り当て](custom-assign-graph.md)に関するページの操作を使用できます。

特定のロールの定義に対するロールの割り当てを取得するための HTTP 要求。

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
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

## <a name="list-role-assignments-with-single-application-scope"></a>単一アプリケーションのスコープでロールの割り当てを一覧表示する

このセクションでは、単一アプリケーションのスコープでロールの割り当てを一覧表示する方法について説明します。 現在、この機能はパブリック プレビュー段階にあります。

1. Azure AD 組織の特権ロール管理者またはグローバル管理者のアクセス許可を使用して [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. **[アプリの登録]** を選択し、次にそのアプリの登録を選択してそのプロパティを表示します。 Azure AD 組織内のアプリ登録の完全な一覧を表示するには、 **[すべてのアプリケーション]** を選択する必要がある場合があります。

    ![[アプリの登録] ページからアプリの登録を作成または編集する](./media/view-assignments/app-reg-all-apps.png)

1. アプリの登録で、**[ロールと管理者]** を選択し、次にロールを選択してそのプロパティを表示します。

    ![[アプリの登録] ページから、アプリの登録のロールの割り当てを一覧表示する](./media/view-assignments/app-reg-assignments.png)

1. ロールの割り当てを一覧表示するには、 **[割り当て]** を選択します。 アプリの登録内から [割り当て] ページを開くと、この Azure AD リソースをスコープとしたロールの割り当てが表示されます。

    ![アプリの登録のプロパティから、アプリの登録のロールの割り当てを一覧表示する](./media/view-assignments/app-reg-assignments-2.png)

## <a name="next-steps"></a>次のステップ

* [Azure AD 管理ロール フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)でご意見をお寄せください。
* ロールと管理者ロールの割り当ての詳細については、[管理者ロールの割り当て](permissions-reference.md)に関するページを参照してください。
* 既定のユーザー アクセス許可については、[既定のゲストとメンバー ユーザーのアクセス許可の比較](../fundamentals/users-default-permissions.md)を参照してください。
