---
title: チュートリアル:Azure PowerShell を使用して Azure リソースへのアクセス権をユーザーに付与する - Azure RBAC
description: このチュートリアルでは、Azure PowerShell と Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure リソースへのアクセス権をユーザーに付与する方法について説明します。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.openlocfilehash: 45993d617028dec13c7a8b57587c7204322965cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555187"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-azure-powershell"></a>チュートリアル:Azure PowerShell を使用して Azure リソースへのアクセス権をユーザーに付与する

[Azure ロールベースのアクセス制御 (Azure RBAC)](overview.md) は、Azure のリソースに対するアクセスを管理するための手法です。 このチュートリアルでは、Azure PowerShell を使用して、サブスクリプション内のすべてを表示し、リソース グループ内のすべてを管理するためのアクセス権をユーザーに付与します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * さまざまなスコープでのユーザーへのアクセス権の付与
> * アクセス権の表示
> * アクセス権の削除

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の要件があります。

- Azure Active Directory でユーザーを作成する (または既存のユーザーを所有する) ためのアクセス許可
- [Azure Cloud Shell](../cloud-shell/quickstart-powershell.md)

## <a name="role-assignments"></a>ロールの割り当て

Azure RBAC でアクセス権を付与するには、ロールの割り当てを作成します。 ロールの割り当ては、セキュリティ プリンシパル、ロールの定義、スコープの 3 つの要素で構成されています。 このチュートリアルで実行するロールの割り当ては次の 2 つです。

| セキュリティ プリンシパル | ロール定義 | Scope |
| --- | --- | --- |
| User<br>(RBAC チュートリアル ユーザー) | [Reader](built-in-roles.md#reader) | サブスクリプション |
| User<br>(RBAC チュートリアル ユーザー)| [Contributor](built-in-roles.md#contributor) | Resource group<br>(rbac-tutorial-resource-group) |

   ![ユーザーのロールの割り当て](./media/tutorial-role-assignments-user-powershell/rbac-role-assignments-user.png)

## <a name="create-a-user"></a>ユーザーの作成

ロールを割り当てるには、ユーザー、グループ、またはサービス プリンシパルが必要です。 ユーザーがない場合は、作成します。

1. Azure Cloud Shell で、パスワードの複雑さの要件に準拠したパスワードを作成します。

    ```azurepowershell
    $PasswordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
    $PasswordProfile.Password = "Password"
    ```

1. [New-AzureADUser](/powershell/module/azuread/new-azureaduser) コマンドを使用して、ご自身のドメインのユーザーを新しく作成します。

    ```azurepowershell
    New-AzureADUser -DisplayName "RBAC Tutorial User" -PasswordProfile $PasswordProfile `
      -UserPrincipalName "rbacuser@example.com" -AccountEnabled $true -MailNickName "rbacuser"
    ```
    
    ```Example
    ObjectId                             DisplayName        UserPrincipalName    UserType
    --------                             -----------        -----------------    --------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial User rbacuser@example.com Member
    ```

## <a name="create-a-resource-group"></a>リソース グループを作成する

リソース グループを使用して、リソース グループ スコープでロールを割り当てる方法を示すことができます。

1. [Get-AzLocation](/powershell/module/az.resources/get-azlocation) コマンドを使用して、リージョンの場所の一覧を取得します。

   ```azurepowershell
   Get-AzLocation | select Location
   ```

1. 近くの場所を選択し、変数に割り当てます。

   ```azurepowershell
   $location = "westus"
   ```

1. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドを使用して、新しいリソース グループを作成します。

   ```azurepowershell
   New-AzResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>アクセス権の付与

ユーザーにアクセス権を付与するには、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) コマンドを使用してロールを割り当てます。 セキュリティ プリンシパル、ロールの定義、およびスコープを指定する必要があります。

1. [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) コマンドを使用して、サブスクリプションの ID を取得します。

    ```azurepowershell
    Get-AzSubscription
    ```

    ```Example
    Name     : Pay-As-You-Go
    Id       : 00000000-0000-0000-0000-000000000000
    TenantId : 22222222-2222-2222-2222-222222222222
    State    : Enabled
    ```

1. サブスクリプション スコープを変数に保存します。

    ```azurepowershell
    $subScope = "/subscriptions/00000000-0000-0000-0000-000000000000"
    ```

1. [閲覧者](built-in-roles.md#reader)ロールをサブスクリプション スコープでユーザーに割り当てます。

    ```azurepowershell
    New-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

1. [共同作成者](built-in-roles.md#contributor)ロールをリソース グループ スコープでユーザーに割り当てます。

    ```azurepowershell
    New-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

## <a name="list-access"></a>アクセス権の表示

1. サブスクリプションのアクセス権を確認するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) コマンドを使用して、ロールの割り当てを一覧表示します。

    ```azurepowershell
    Get-AzRoleAssignment -SignInName rbacuser@example.com -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

    出力では、閲覧者ロールが、サブスクリプション スコープで RBAC チュートリアル ユーザーに割り当てられていることを確認できます。

1. リソース グループのアクセス権を確認するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) コマンドを使用して、ロールの割り当てを一覧表示します。

    ```azurepowershell
    Get-AzRoleAssignment -SignInName rbacuser@example.com -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

    出力では、共同作成者ロールと閲覧者ロールの両方が、RBAC チュートリアル ユーザーに割り当てられていることを確認できます。 共同作成者ロールは rbac-tutorial-resource-group スコープです。また、閲覧者ロールは、サブスクリプション スコープに継承されます。

## <a name="optional-list-access-using-the-azure-portal"></a>(省略可能) Azure portal を使用したアクセス権の表示

1. Azure portal で、ロールの割り当てがどのように表示されるかを確認するには、サブスクリプションの **[アクセス制御 (IAM)]** ブレードを表示します。

    ![サブスクリプション スコープでのユーザーのロールの割り当て](./media/tutorial-role-assignments-user-powershell/role-assignments-subscription-user.png)

1. リソース グループの **[アクセス制御 (IAM)]** ブレードを表示します。

    ![リソース グループ スコープでのユーザーのロールの割り当て](./media/tutorial-role-assignments-user-powershell/role-assignments-resource-group-user.png)

## <a name="remove-access"></a>アクセス権の削除

ユーザー、グループ、アプリケーションのアクセス権を削除するには、[Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) を使用して、ロールの割り当てを削除します。

1. 次のコマンドを使用して、リソース グループ スコープでユーザーの共同作成者ロールの割り当てを削除します。

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. 次のコマンドを使用して、サブスクリプション スコープでユーザーの閲覧者ロールの割り当てを削除します。

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成したリソースをクリーンアップするには、リソース グループとユーザーを削除します。

1. [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループを削除します。

    ```azurepowershell
    Remove-AzResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. 確認を求められたら、「**Y**」と入力します。削除には数秒かかります。

1. [Remove-AzureADUser](/powershell/module/azuread/remove-azureaduser) コマンドを使用して、ユーザーを削除します。

    ```azurepowershell
    Remove-AzureADUser -ObjectId "rbacuser@example.com"
    ```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure PowerShell を使用して Azure ロールを割り当てる](role-assignments-powershell.md)