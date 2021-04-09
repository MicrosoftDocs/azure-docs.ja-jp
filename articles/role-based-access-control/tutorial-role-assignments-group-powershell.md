---
title: チュートリアル:Azure PowerShell を使用して Azure リソースへのアクセス権をグループに付与する - Azure RBAC
description: このチュートリアルでは、Azure PowerShell と Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure リソースへのアクセス権をグループに付与する方法について説明します。
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
ms.openlocfilehash: e25db6213c99afaa46732831534956b01adba0b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555226"
---
# <a name="tutorial-grant-a-group-access-to-azure-resources-using-azure-powershell"></a>チュートリアル:Azure PowerShell を使用して Azure リソースへのアクセス権をグループに付与する

[Azure ロールベースのアクセス制御 (Azure RBAC)](overview.md) は、Azure のリソースに対するアクセスを管理するための手法です。 このチュートリアルでは、Azure PowerShell を使用して、サブスクリプション内のすべてを表示し、リソース グループ内のすべてを管理するためのアクセス権をグループに付与します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * さまざまなスコープでのグループへのアクセス権の付与
> * アクセス権の表示
> * アクセス権の削除

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の要件があります。

- Azure Active Directory でグループを作成する (または既存のグループを所有する) ためのアクセス許可
- [Azure Cloud Shell](../cloud-shell/quickstart-powershell.md)

## <a name="role-assignments"></a>ロールの割り当て

Azure RBAC でアクセス権を付与するには、ロールの割り当てを作成します。 ロールの割り当ては、セキュリティ プリンシパル、ロールの定義、スコープの 3 つの要素で構成されています。 このチュートリアルで実行するロールの割り当ては次の 2 つです。

| セキュリティ プリンシパル | ロール定義 | Scope |
| --- | --- | --- |
| グループ<br>(RBAC チュートリアル グループ) | [Reader](built-in-roles.md#reader) | サブスクリプション |
| グループ<br>(RBAC チュートリアル グループ)| [Contributor](built-in-roles.md#contributor) | Resource group<br>(rbac-tutorial-resource-group) |

   ![グループのロールの割り当て](./media/tutorial-role-assignments-group-powershell/rbac-role-assignments.png)

## <a name="create-a-group"></a>グループの作成

ロールを割り当てるには、ユーザー、グループ、またはサービス プリンシパルが必要です。 グループがない場合は、作成します。

- Azure Cloud Shell で、[New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) コマンドを使用して、新しいグループを作成します。

   ```azurepowershell
   New-AzureADGroup -DisplayName "RBAC Tutorial Group" `
     -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
   ```

   ```Example
   ObjectId                             DisplayName         Description
   --------                             -----------         -----------
   11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
   ```

グループを作成するアクセス許可がない場合は、代わりに「[チュートリアル: Azure PowerShell を使用して Azure リソースへのアクセス権をユーザーに付与する](tutorial-role-assignments-user-powershell.md)」をお試しください。

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

グループにアクセス権を付与するには、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) コマンドを使用してロールを割り当てます。 セキュリティ プリンシパル、ロールの定義、およびスコープを指定する必要があります。

1. [Get-AzureADGroup](/powershell/module/azuread/new-azureadgroup) コマンドを使用して、グループのオブジェクト ID を取得します。

    ```azurepowershell
    Get-AzureADGroup -SearchString "RBAC Tutorial Group"
    ```

    ```Example
    ObjectId                             DisplayName         Description
    --------                             -----------         -----------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
    ```

1. グループのオブジェクト ID を変数に保存します。

    ```azurepowershell
    $groupId = "11111111-1111-1111-1111-111111111111"
    ```

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

1. [閲覧者](built-in-roles.md#reader)ロールをサブスクリプション スコープでグループに割り当てます。

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

1. [共同作成者](built-in-roles.md#contributor)ロールをリソース グループ スコープでグループに割り当てます。

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

## <a name="list-access"></a>アクセス権の表示

1. サブスクリプションのアクセス権を確認するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) コマンドを使用して、ロールの割り当てを一覧表示します。

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId $groupId -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    出力では、閲覧者ロールが、サブスクリプション スコープで RBAC チュートリアル グループに割り当てられていることを確認できます。

1. リソース グループのアクセス権を確認するには、[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) コマンドを使用して、ロールの割り当てを一覧表示します。

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId $groupId -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    出力では、共同作成者ロールと閲覧者ロールの両方が、RBAC チュートリアル グループに割り当てられていることを確認できます。 共同作成者ロールは rbac-tutorial-resource-group スコープです。また、閲覧者ロールは、サブスクリプション スコープに継承されます。

## <a name="optional-list-access-using-the-azure-portal"></a>(省略可能) Azure portal を使用したアクセス権の表示

1. Azure portal で、ロールの割り当てがどのように表示されるかを確認するには、サブスクリプションの **[アクセス制御 (IAM)]** ブレードを表示します。

    ![サブスクリプション スコープでのグループのロールの割り当て](./media/tutorial-role-assignments-group-powershell/role-assignments-subscription.png)

1. リソース グループの **[アクセス制御 (IAM)]** ブレードを表示します。

    ![リソース グループ スコープでのグループのロールの割り当て](./media/tutorial-role-assignments-group-powershell/role-assignments-resource-group.png)

## <a name="remove-access"></a>アクセス権の削除

ユーザー、グループ、アプリケーションのアクセス権を削除するには、[Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) を使用して、ロールの割り当てを削除します。

1. 次のコマンドを使用して、リソース グループ スコープでグループの共同作成者ロールの割り当てを削除します。

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. 次のコマンドを使用して、サブスクリプション スコープでグループの閲覧者ロールの割り当てを削除します。

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成したリソースをクリーンアップするには、リソース グループとグループを削除します。

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

1. [Remove-AzureADGroup](/powershell/module/azuread/remove-azureadgroup) コマンドを使用して、グループを削除します。

    ```azurepowershell
    Remove-AzureADGroup -ObjectId $groupId
    ```
    
    グループを削除しようしたときにエラーが発生する場合は、ポータルでもグループを削除できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure PowerShell を使用して Azure ロールを割り当てる](role-assignments-powershell.md)