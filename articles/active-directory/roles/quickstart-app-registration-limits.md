---
title: アプリ登録の作成への制限を削除する - Azure AD | Microsoft Docs
description: Azure AD Active Directory に無制限のアプリの登録を許可するカスタム ロールを割り当てる
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: quickstart
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, devx-track-azurepowershell
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48740c52e6713735957e1ebf00e8996800eabec2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015779"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>クイック スタート:無制限のアプリの登録を作成するアクセス許可を付与する

このクイック スタート ガイドでは、無制限の数のアプリの登録を作成するアクセス許可があるカスタム ロールを作成した後、そのロールをユーザーに割り当てます。 その後、割り当てられたユーザーは、Azure AD ポータル、Azure AD PowerShell、または Microsoft Graph API を使用して、アプリの登録を作成できます。 組み込みのアプリケーション開発者ロールとは異なり、このカスタム ロールには、無制限の数のアプリの登録を作成する権限が付与されます。 アプリケーション開発者ロールにもこの権限は付与されますが、[ディレクトリ全体のオブジェクトのクォータ](../enterprise-users/directory-service-limits-restrictions.md)に達することがないように、作成されるオブジェクトの合計数は 250 に制限されています。 Azure AD カスタム ロールの作成と割り当てに必要な最小限の特権を持つロールは、特権ロール管理者です。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="create-a-custom-role-using-the-azure-ad-portal"></a>Azure AD ポータルを使用してカスタム ロールを作成する

1. Azure AD 組織の特権ロール管理者またはグローバル管理者のアクセス許可を使用して [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. **[Azure Active Directory]** を選択し、**[ロールと管理者]** を選択してから、**[新しいカスタム ロール]** を選択します。

    ![[ロールと管理者] ページでロールを作成または編集する](./media/quickstart-app-registration-limits/new-custom-role.png)

1. **[基本]** タブで、ロールの [名前] として「Application Registration Creator」(アプリケーション登録作成者) を指定し、[ロールの説明] に「Can create an unlimited number of application registrations」(無制限の数のアプリケーションの登録を作成できる) と入力して、**[次へ]** を選択します。

    ![[基本] タブでカスタム ロールの名前と説明を指定する](./media/quickstart-app-registration-limits/basics-tab.png)

1. **[アクセス許可]** タブで、検索ボックスに「microsoft.directory/applications/create」と入力し、必要なアクセス許可の横にあるチェックボックスをオンにした後、**[次へ]** を選択します。

    ![[アクセス許可] タブでカスタム ロールのアクセス許可を選択する](./media/quickstart-app-registration-limits/permissions-tab.png)

1. **[確認と作成]** タブでアクセス許可を確認し、 **[作成]** を選択します。

### <a name="assign-the-role-in-the-azure-ad-portal"></a>Azure AD ポータルでロールを割り当てる

1. Azure AD 組織の特権ロール管理者またはグローバル管理者のアクセス許可を使用して [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. **[Azure Active Directory]** を選択し、次に **[ロールと管理者]** を選択します。
1. [アプリケーション登録作成者] ロールを選択し、**[割り当ての追加]** を選択します。
1. 目的のユーザーを選択し、**[選択]** をクリックして、ユーザーをロールに追加します。

これで完了です。 このクイックスタートでは、無制限の数のアプリの登録を作成するアクセス許可を持つカスタム ロールを作成した後、そのロールをユーザーに割り当てることに成功しました。

> [!TIP]
> Azure AD ポータルを使用してアプリケーションにロールを割り当てるには、[割り当て] ページの [検索] ボックスにアプリケーションの名前を入力します。 既定では、アプリケーションは一覧に表示されませんが、検索結果には返されます。

### <a name="app-registration-permissions"></a>アプリの登録とアクセス許可

アプリケーションの登録を作成する権限を付与するために使用できるアクセス許可は 2 つあり、それぞれ動作が異なります。

- microsoft.directory/applications/createAsOwner:このアクセス許可を割り当てると、作成者は、作成されたアプリの登録の最初の所有者として追加され、作成されたアプリの登録は、その作成者の 250 という作成オブジェクト クォータのカウント対象になります。
- microsoft.directory/applications/create:このアクセス許可を割り当てると、作成者は、作成されたアプリの登録の最初の所有者としては追加されず、作成されたアプリの登録は、その作成者の 250 という作成オブジェクト クォータのカウント対象になりません。 担当者がディレクトリ レベルのクォータに達するまでアプリの登録を作成できないようにするものはないため、このアクセス許可は慎重に使用してください。 両方のアクセス許可が割り当てられている場合は、このアクセス許可が優先されます。

## <a name="create-a-custom-role-in-azure-ad-powershell"></a>Azure AD PowerShell でカスタム ロールを作成する

### <a name="prepare-powershell"></a>PowerShell を準備する

最初に、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)から Azure AD PowerShell モジュールをインストールします。 その後、次のコマンドを使用して、Azure AD PowerShell プレビュー モジュールをインポートします。

```powershell
Import-Module -Name AzureADPreview
```

モジュールが使用できる状態であることを確認するには、次のコマンドによって返されたバージョンを次に示されているものと照合します。

```powershell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role-in-azure-ad-powershell"></a>Azure AD PowerShell でカスタム ロールを作成する

次の PowerShell スクリプトを実行して、新しいロールを作成します。

```powershell

# Basic role information
$displayName = "Application Registration Creator"
$description = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
    "microsoft.directory/applications/createAsOwner"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customRole = New-AzureAdMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-role-in-azure-ad-powershell"></a>Azure AD PowerShell でロールを割り当てる

次の PowerShell スクリプトを使用してロールを割り当てます。

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'Adam@contoso.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Registration Creator'"

# Get resource scope for assignment
$resourceScope = '/'

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-custom-role-in-the-microsoft-graph-api"></a>Microsoft Graph API でカスタム ロールを作成する

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
                    "microsoft.directory/applications/createAsOwner"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-role-in-the-microsoft-graph-api"></a>Microsoft Graph API でロールを割り当てる

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

## <a name="next-steps"></a>次のステップ

- [Azure AD 管理ロール フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)でご意見をお寄せください。
- Azure AD ロールの割り当てについて詳しくは、[管理者ロールの割り当て](permissions-reference.md)に関するページを参照してください。
- 既定のユーザー アクセス許可について詳しくは、[ゲスト ユーザーとメンバー ユーザーの既定のアクセス許可の比較](../fundamentals/users-default-permissions.md)に関するページを参照してください。
