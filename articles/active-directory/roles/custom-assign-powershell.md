---
title: Azure AD PowerShell を使用してカスタム ロールを割り当てる - Azure AD | Microsoft Docs
description: Azure AD PowerShell を使用して Azure AD 管理者のカスタム ロールのメンバーを管理します。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f0fb81a4daa57b473e8b2b4b937426eafbf903d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014538"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Azure Active Directory で PowerShell を使用してリソース スコープのカスタム ロールを割り当てる

この記事では、Azure Active Directory (Azure AD) でロールの割り当てを組織全体のスコープで作成する方法について説明します。 組織全体のスコープでロールを割り当てると、Azure AD 組織全体のアクセス権が付与されます。 1 つの Azure AD リソースのスコープでロールの割り当てを作成する場合は、[カスタム ロールを作成してリソース スコープで割り当てる方法](custom-create.md)に関する記事を参照してください。 この記事では、[Azure Active Directory PowerShell バージョン 2](/powershell/module/azuread/#directory_roles) モジュールを使います。

Azure AD 管理者ロールの詳細については、[Azure Active Directory での管理者ロールの割り当て](permissions-reference.md)に関する記事を参照してください。

## <a name="required-permissions"></a>必要なアクセス許可

ロールの割り当てまたは削除を行うには、グローバル管理者アカウントを使って Azure AD 組織に接続します。

## <a name="prepare-powershell"></a>PowerShell を準備する

[PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureADPreview)から Azure AD PowerShell モジュールをインストールします。 その後、次のコマンドを使用して、Azure AD PowerShell プレビュー モジュールをインポートします。

``` PowerShell
Import-Module -Name AzureADPreview
```

モジュールが使用できる状態であることを確認するには、次のコマンドによって返されたバージョンを次に示されているものと照合します。

``` PowerShell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADMSAdministrati...}
```

これで、モジュールのコマンドレットの使用を開始できます。 Azure AD モジュールのコマンドレットの詳細については、[Azure AD プレビュー モジュール](https://www.powershellgallery.com/packages/AzureADPreview)のオンライン リファレンス ドキュメントをご覧ください。

## <a name="assign-a-directory-role-to-a-user-or-service-principal-with-resource-scope"></a>リソース スコープでユーザーまたはサービス プリンシパルにディレクトリ ロールを割り当てる

1. Azure AD PowerShell (プレビュー) モジュールを読み込みます。
1. `Connect-AzureAD` コマンドを実行してサインインします。
1. 次の PowerShell スクリプトを実行して、新しいロールを作成します。

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

ユーザーではなくサービス プリンシパルにロールを割り当てるには、[Get-AzureADMSServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) コマンドレットを使用します。

## <a name="role-definitions"></a>ロールの定義

ロール定義オブジェクトには、組み込みロールまたはカスタム ロールの定義と共に、そのロールの割り当てによって付与されるアクセス許可が含まれます。 このリソースでは、カスタム ロールの定義と組み込みディレクトリ ロール (roleDefinition と同等の形式で表示されます) の両方が表示されます。 現在、Azure AD 組織では、最大 30 個の一意のカスタム ロール定義を定義できます。

### <a name="create-a-role-definition"></a>ロール定義を作成する

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to include
$rolePermissions = @{
    "allowedResourceActions" = @(
        "microsoft.directory/applications/standard/read",
        "microsoft.directory/applications/credentials/update"
    )
}

# Create new custom directory role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-and-list-role-definitions"></a>ロール定義を読み取るおよび一覧表示する

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by ID
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-a-role-definition"></a>ロール定義を更新する

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-a-role-definition"></a>ロール定義を削除する

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="role-assignments"></a>ロールの割り当て

ロールの割り当てには、特定のセキュリティ プリンシパル (ユーザーまたはアプリケーションのサービス プリンシパル) をロールの定義にリンクする情報が含まれます。 必要に応じて、割り当てられたアクセス許可に対して 1 つの Azure AD リソースのスコープを追加できます。  ロール割り当てのスコープの制限は、組み込みおよびカスタム ロールでサポートされています。

### <a name="create-a-role-assignment"></a>役割の割り当ての作成

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

### <a name="read-and-list-role-assignments"></a>ロールの割り当てを読み取るおよび一覧表示する

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-a-role-assignment"></a>ロールの割り当てを削除する

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>次のステップ

- [Azure AD 管理ロール フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)でご意見をお寄せください。
- ロールと Azure AD 管理者ロールの割り当ての詳細については、「[管理者ロールを割り当てる](permissions-reference.md)」を参照してください。
- 既定のユーザー アクセス許可については、[アクセス許可に関する既定のゲストとメンバー ユーザーの比較](../fundamentals/users-default-permissions.md)に関する記事を参照してください。
