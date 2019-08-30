---
title: Azure PowerShell を使用してリソース スコープのカスタム ロールを割り当てる - Azure Active Directory | Microsoft Docs
description: Azure PowerShell を使用して Azure AD 管理者のカスタム ロールのメンバーを管理します。
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/05/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd3f590659017d2bb79c7445f6896817b8432f41
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880730"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Azure Active Directory で PowerShell を使用してリソース スコープのカスタム ロールを割り当てる

この記事では、Azure Active Directory (Azure AD) でロールの割り当てを組織全体のスコープで作成する方法について説明します。 組織全体のスコープでロールを割り当てると、Azure AD 組織全体のアクセス権が付与されます。 1 つの Azure AD リソースのスコープでロールの割り当てを作成する場合は、[カスタム ロールを作成してリソース スコープで割り当てる方法](roles-create-custom.md)に関する記事を参照してください。この記事では、[Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) モジュールを使用します。

Azure AD 管理者ロールについて詳しくは、 [Azure Active Directory での管理者ロールの割り当て](directory-assign-admin-roles.md)に関する記事をご覧ください。

## <a name="required-permissions"></a>必要なアクセス許可

ロールの割り当てまたは削除を行うには、グローバル管理者アカウントを使って Azure AD テナントに接続します。

## <a name="prepare-powershell"></a>PowerShell を準備する

[PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)から Azure AD PowerShell モジュールをインストールします。 その後、次のコマンドを使用して、Azure AD PowerShell プレビュー モジュールをインポートします。

``` PowerShell
import-module azureadpreview
```

モジュールが使用できる状態であることを確認するには、次のコマンドによって返されたバージョンを次に示されているものと照合します。

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

これで、モジュールのコマンドレットの使用を開始できます。 Azure AD モジュールのコマンドレットの詳細については、[Azure AD プレビュー モジュール](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)のオンライン リファレンス ドキュメントをご覧ください。

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>リソース スコープでユーザーまたはサービス プリンシパルにロールを割り当てる

1. Azure AD プレビュー PowerShell モジュールを開きます。
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

ユーザーではなくサービス プリンシパルにロールを割り当てるには、[Get-AzureADMSServicePrincipal cmdlet](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) コマンドレットを使用します。

## <a name="operations-on-roledefinition"></a>RoleDefinition に対する操作

ロール定義オブジェクトには、組み込みロールまたはカスタム ロールの定義と共に、そのロールの割り当てによって付与されるアクセス許可が含まれます。 このリソースでは、カスタム ロールの定義と組み込み directoryRole (roleDefinition と同等の形式で表示されます) の両方が表示されます。 現在、Azure AD 組織では、最大 30 個の一意のカスタム RoleDefinition を定義できます。

### <a name="create-operations-on-roledefinition"></a>RoleDefinition に対する操作を作成する

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/standard/read",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-operations-on-roledefinition"></a>RoleDefinition に対する操作を読み取る

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-operations-on-roledefinition"></a>RoleDefinition に対する操作を更新する

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>RoleDefinition に対する操作を削除する

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="operations-on-roleassignment"></a>RoleAssignment に対する操作

ロールの割り当てには、特定のセキュリティ プリンシパル (ユーザーまたはアプリケーションのサービス プリンシパル) をロールの定義にリンクする情報が含まれます。 必要に応じて、割り当てられたアクセス許可に対して 1 つの Azure AD リソースのスコープを追加できます。  アクセス許可のスコープの制限は、組み込みロールとカスタム ロールでサポートされています。

### <a name="create-operations-on-roleassignment"></a>RoleAssignment に対する操作を作成する

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

### <a name="read-operations-on-roleassignment"></a>RoleAssignment に対する操作を読み取る

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>RoleAssignment に対する操作を削除する

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>次の手順

- [Azure AD 管理ロール フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)でご意見をお寄せください。
- ロールと Azure AD 管理者ロールの割り当ての詳細については、[管理者ロールの割り当て](directory-assign-admin-roles.md)に関する記事を参照してください。
- 既定のユーザー アクセス許可については、[既定のゲストとメンバー ユーザーのアクセス許可の比較](../fundamentals/users-default-permissions.md)を参照してください。
