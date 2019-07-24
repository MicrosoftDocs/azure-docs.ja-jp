---
title: Azure Active Directory Domain Services:範囲指定された同期 | Microsoft Docs
description: Azure AD からマネージド ドメインまで範囲指定された同期を構成する
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: ergreenl
ms.openlocfilehash: ac11244b87c87285722b4922da69530fab98c299
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58117610"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>Azure AD からマネージド ドメインまで範囲指定された同期を構成する
この記事では、Azure AD ディレクトリから Azure AD Domain Services のマネージド ドメインに同期される特定のユーザー アカウントのみを構成する方法を示します。


## <a name="group-based-scoped-synchronization"></a>グループ ベースのスコープを持つ同期
既定では、すべてのユーザーと Azure AD ディレクトリ内のグループがマネージド ドメインに同期されます。 マネージド ドメインを少数のユーザーしか使用していない場合は、ユーザー アカウントのみを同期することができます。 グループ ベースのスコープを持つ同期によって、そうすることができます。 構成すると、指定したグループに属しているユーザー アカウントのみがマネージド ドメインに同期されます。

次の表は、スコープ付きの同期を使用する方法を決定するのに役立ちます。

| **現在の状態** | **必要な状態** | **必要な構成** |
| --- | --- | --- |
| 既存のマネージド ドメインはすべてのユーザー アカウントとグループを同期するように構成されています。 | 特定のグループに属するユーザー アカウントのみをマネージド ドメインに同期できます。 | [既存のマネージド ドメインを削除](active-directory-ds-disable-aadds.md)します。 この記事の手順に従い、構成したスコープ付き同期で再作成します。 |
| 既存のマネージド ドメインがありません。 | 新しいマネージド ドメインを作成し、特定のグループに属するユーザー アカウントのみを同期します。 | 次に、この記事の手順に従い、新しいマネージド ドメインを、構成したスコープ付き同期で作成します。 |
| 既存のマネージド ドメインは、特定のグループに属するユーザー アカウントのみを同期するように構成されています。 | マネージド ドメインにユーザーを同期するグループの一覧を変更します。 | スコープ付き同期を変更するのには、この記事の手順に従います。 |

> [!WARNING]
> **同期のスコープを変更すると、マネージド ドメインが再同期されます。**
> 
>  * マネージド ドメインの同期スコープを変更すると、完全な再同期が発生します。
>  * マネージド ドメインで不要になったオブジェクトは削除されます。 新しいオブジェクトは、マネージド ドメインに作成されます。
>  * 再同期は、マネージド ドメインと Azure AD ディレクトリ内のオブジェクト (ユーザー、グループ、およびグループ メンバーシップ) の数によっては、完了までに時間がかかる場合があります。 数十万のオブジェクトを含む大規模なディレクトリについては、再同期に数日かかる場合があります。


## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-azure-portal"></a>Azure portal を使用して新しいマネージド ドメインを作成し、グループ ベースのスコープ付き同期を有効にする

1. [ファースト ステップ ガイド](active-directory-ds-getting-started.md)に従って、マネージド ドメインを作成します。
2. Azure AD Domain Services 作成ウィザードで同期スタイルを選択するときに **[スコープ付き]** を選択します。

## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-powershell"></a>PowerShell を使用して新しいマネージド ドメインを作成し、グループ ベースのスコープ付き同期を有効にする
この一連の手順を完了するには、PowerShell を使用します。 [PowerShell を使用した Azure Active Directory Domain Services の有効化](active-directory-ds-enable-using-powershell.md)の手順を参照してください。 この記事の手順のいくつかは、スコープ付きの同期を構成するために若干変更されます。

グループ ベースのスコープ付きの同期をマネージド ドメインに対して構成するには、次の手順を完了します。

1. 次のタスクを実行してください。
   * [タスク 1 :必要な PowerShell モジュールをインストールする](active-directory-ds-enable-using-powershell.md#task-1-install-the-required-powershell-modules)。
   * [タスク 2 :Azure AD ディレクトリに必要なサービス プリンシパルを作成する](active-directory-ds-enable-using-powershell.md#task-2-create-the-required-service-principal-in-your-azure-ad-directory)。
   * [タスク 3 :"AAD DC 管理者" グループを作成して構成する](active-directory-ds-enable-using-powershell.md#task-3-create-and-configure-the-aad-dc-administrators-group)。
   * [タスク 4 :Azure AD Domain Services のリソース プロバイダーを登録する](active-directory-ds-enable-using-powershell.md#task-4-register-the-azure-ad-domain-services-resource-provider)。
   * [タスク 5 :リソース グループを作成する](active-directory-ds-enable-using-powershell.md#task-5-create-a-resource-group)。
   * [タスク 6 :仮想ネットワークを作成して構成する](active-directory-ds-enable-using-powershell.md#task-6-create-and-configure-the-virtual-network)。

2. 同期するグループを選択し、マネージド ドメインに同期するグループの表示名を指定します。

3. [以下のセクションのスクリプト](active-directory-ds-scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1)を ```Select-GroupsToSync.ps1``` というファイルに保存します。 次のようなスクリプトを実行します。

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

   > [!WARNING]
   > **"AAD DC Administrators" グループを含めることを忘れないでください。**
   >
   > スコープを持つ同期用に構成されたグループの一覧に、"AAD DC Administrators" グループを含める必要があります。 このグループを含めない場合、マネージド ドメインは使用できません。
   >

4. ここで、マネージド ドメインを作成し、マネージド ドメインのグループ ベースのスコープを持つ同期を有効にします。 ```Properties``` パラメーターに ```"filteredSync" = "Enabled"``` プロパティ を含めます。 たとえば、「[タスク 7:Azure AD Domain Services のマネージド ドメインをプロビジョニングする](active-directory-ds-enable-using-powershell.md#task-7-provision-the-azure-ad-domain-services-managed-domain)」からコピーした次のスクリプト フラグメントを参照してください。

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso100.com"
   $ResourceGroupName = "ContosoAaddsRg"
   $VnetName = "DomainServicesVNet_WUS"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -ApiVersion 2017-06-01 -Force -Verbose
   ```

   > [!TIP]
   > スコープを持つ同期がマネージド ドメインに有効となるように ```-Properties``` パラメーターに ```"filteredSync" = "Enabled"``` を含めることを忘れないでください。


## <a name="script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1"></a>マネージド ドメインに同期するグループを選択するスクリプト (Select-GroupsToSync.ps1)
次のスクリプトを (```Select-GroupsToSync.ps1```) に保存します。 このスクリプトでは、Azure AD Domain Services を構成して選択されているグループをマネージド ドメインに同期します。 指定したグループに属しているすべてのユーザー アカウントは、マネージド ドメインに同期されます。

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```


## <a name="modify-group-based-scoped-synchronization"></a>グループ ベースのスコープを持つ同期を変更する
マネージド ドメインにユーザーを同期するグループの一覧を変更するには、[PowerShell スクリプト](active-directory-ds-scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1)を再実行し、新しいグループの一覧を指定します。 この一覧に必ず "AAD DC Administrators" グループを指定してください。

> [!WARNING]
> **"AAD DC Administrators" グループを含めることを忘れないでください。**
>
> スコープを持つ同期用に構成されたグループの一覧に、"AAD DC Administrators" グループを含める必要があります。 このグループを含めない場合、マネージド ドメインは使用できません。
>


## <a name="disable-group-based-scoped-synchronization"></a>グループ ベースのスコープを持つ同期を無効にする
グループ ベースのスコープ付きの同期をマネージド ドメインに対して無効にするには、次の PowerShell スクリプトを使用します。

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

## <a name="next-steps"></a>次の手順
* [Azure AD Domain Services での同期を理解する](active-directory-ds-synchronization.md)
* [PowerShell を使用した Azure Active Directory Domain Services の有効化](active-directory-ds-enable-using-powershell.md)
