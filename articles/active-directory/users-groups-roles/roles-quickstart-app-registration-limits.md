---
title: アプリの登録に関する制限を超える個別のアクセス許可を付与する - Azure Active Directory | Microsoft Docs
description: Azure AD Active Directory に無制限のアプリの登録を許可するカスタム ロールを割り当てます。
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
ms.openlocfilehash: f883741577de22f66cd7a9bfaf733aa3c59b879b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707472"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>クイック スタート:無制限のアプリの登録を作成するアクセス許可を付与する

このクイックスタートでは、無制限の数のアプリの登録を作成するアクセス許可を持つカスタム ロールを作成した後、そのロールをユーザーに割り当てます。 その後、割り当てられたユーザーは、Azure AD ポータル、Azure AD PowerShell、Azure AD Graph API、または Microsoft Graph API を使用して、アプリの登録を作成できます。 組み込みのアプリケーション開発者ロールとは異なり、このカスタム ロールには、無制限の数のアプリの登録を作成する権限が付与されます。 アプリケーション開発者ロールにもこの権限は付与されますが、[ディレクトリ全体のオブジェクトのクォータ](directory-service-limits-restrictions.md)に達することがないように、作成されるオブジェクトの合計数は 250 に制限されています。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisite"></a>前提条件

Azure AD カスタム ロールの作成と割り当てに必要な最小限の特権を持つロールは、特権ロール管理者です。

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Azure AD ポータルを使用して新しいカスタム ロールを作成する

1. Azure AD 組織の特権ロール管理者またはグローバル管理者のアクセス許可を使用して、 [Azure AD 管理センター](https://aad.portal.azure.com) にサインインします。
1.  **[Azure Active Directory]** を選択します。その後、 **[ロールと管理者]** を選択し、 **[新しいカスタム ロール]** を選択します。

    ![[ロールと管理者] ページでロールを作成または編集する](./media/roles-create-custom/new-custom-role.png)

1. **[基本]** タブで、ロールの [名前] として「Application Registration Creator」(アプリケーション登録作成者) を指定し、[ロールの説明] に「Can create an unlimited number of application registrations」(無制限の数のアプリケーションの登録を作成できる) と入力して、 **[次へ]** を選択します。

    ![[基本] タブでカスタム ロールの名前と説明を指定する](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. **[アクセス許可]** タブで、検索ボックスに「microsoft.directory/applications/create」と入力し、必要なアクセス許可の横にあるチェックボックスをオンにした後、 **[次へ]** を選択します。

    ![[アクセス許可] タブでカスタム ロールのアクセス許可を選択する](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. **[確認と作成]** タブでアクセス許可を確認し、 **[作成]** を選択します。

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Azure AD ポータルを使用してユーザーにロールを割り当てる

1. Azure AD 組織の特権ロール管理者またはグローバル管理者のアクセス許可を使用して、 [Azure AD 管理センター](https://aad.portal.azure.com) にサインインします。
1.  **[Azure Active Directory]** を選択し、 **[ロールと管理者]** を選択します。
1. [アプリケーション登録作成者] ロールを選択し、 **[割り当ての追加]** を選択します。
1. 目的のユーザーを選択し、 **[選択]** をクリックして、ユーザーをロールに追加します。

これで完了です。 このクイックスタートでは、無制限の数のアプリの登録を作成するアクセス許可を持つカスタム ロールを作成した後、そのロールをユーザーに割り当てることに成功しました。

> [!TIP]
> Azure AD ポータルを使用してアプリケーションにロールを割り当てるには、[割り当て] ページの [検索] ボックスにアプリケーションの名前を入力します。 既定では、アプリケーションは一覧に表示されませんが、検索結果には返されます。

### <a name="app-registration-permissions"></a>アプリの登録とアクセス許可

アプリケーションの登録を作成する権限を付与するために使用できるアクセス許可は 2 つあり、それぞれ動作が異なります。

- microsoft.directory/applications/createAsOwner:このアクセス許可を割り当てると、作成者は、作成されたアプリの登録の最初の所有者として追加され、作成されたアプリの登録は、その作成者の 250 という作成オブジェクト クォータのカウント対象になります。
- microsoft.directory/applicationPolicies/create:このアクセス許可を割り当てると、作成者は、作成されたアプリの登録の最初の所有者としては追加されず、作成されたアプリの登録は、その作成者の 250 という作成オブジェクト クォータのカウント対象になりません。 作成者がディレクトリ レベルのクォータに達するまでアプリの登録の作成を防ぐものは何もないため、このアクセス許可は慎重に使用してください。 両方のアクセス許可が割り当てられている場合は、このアクセス許可が優先されます。

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Azure AD PowerShell を使用してカスタム ロールを作成する

次の PowerShell スクリプトを実行して、新しいロールを作成します。

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/createAsOwner"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Azure AD PowerShell を使用してカスタム ロールを割り当てる

#### <a name="prepare-powershell"></a>PowerShell を準備する

最初に、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)から Azure AD PowerShell モジュールをインストールします。 その後、次のコマンドを使用して、Azure AD PowerShell プレビュー モジュールを開きます。

```powershell
import-module azureadpreview
```

モジュールが使用できる状態であることを確認するには、次のコマンドによって返されたバージョンを次に示されているものと照合します。

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>カスタム ロールを割り当てる

次の PowerShell スクリプトを使用して、ロールを割り当てます。

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>Microsoft Graph API を使用してカスタム ロールを作成する

カスタム ロールを作成するための HTTP 要求。

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Body

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Microsoft Graph API を使用してカスタム ロールを割り当てる

ロールの割り当てでは、セキュリティ プリンシパル ID (ユーザーでもサービス プリンシパルでも可)、ロール定義 (ロール) ID、および Azure AD リソース スコープを組み合わせます。

カスタム ロールを割り当てるための HTTP 要求。

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Body

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>次の手順

- [Azure AD 管理ロール フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)でご意見をお寄せください。
- ロールと管理者ロールの割り当ての詳細については、[管理者ロールの割り当て](directory-assign-admin-roles.md)に関するページを参照してください。
- 既定のユーザー アクセス許可については、[既定のゲストとメンバー ユーザーのアクセス許可の比較](../fundamentals/users-default-permissions.md)を参照してください。
