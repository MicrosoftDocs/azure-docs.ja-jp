---
title: PowerShell を使用した Azure AD Domain Services の範囲指定された同期 | Microsoft Docs
description: Azure AD PowerShell を使用して、Azure AD から Azure Active Directory Domain Services マネージド ドメインへの範囲指定された同期を構成する方法について説明します
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: justinha
ms.openlocfilehash: f877a631fd3c89d74b9e3b47cf205bbcf173ebc0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453412"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-azure-ad-powershell"></a>Azure AD PowerShell を使用して、Azure AD から Azure Active Directory Domain Services への範囲指定された同期を構成する

Azure Active Directory Domain Services (Azure AD DS) は、認証サービスを提供するため、Azure AD からユーザーとグループを同期します。 ハイブリッド環境では、最初にオンプレミスの Active Directory Domain Services (AD DS) 環境のユーザーとグループが Azure AD Connect を使用して Azure AD に同期された後、Azure AD DS に同期されます。

既定では、Azure AD ディレクトリのすべてのユーザーとグループが Azure AD DS マネージド ドメインに同期されます。 特定のニーズがある場合は、代わりに、定義したユーザー セットのみを同期することを選択できます。

この記事では、範囲指定された同期を使用するマネージド ドメインを作成した後、Azure AD PowerShell を使用して、範囲指定されたユーザー セットを変更したり、無効にしたりする方法について説明します。 [これらの手順は、Azure portal を使用して完了][scoped-sync]することもできます。

## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要に応じて、[Azure Active Directory Domain Services マネージド ドメインを作成して構成する][tutorial-create-instance]チュートリアルを完了します。
* Azure AD DS 同期スコープを変更するには、Azure AD テナントでの "*全体管理者*" 特権が必要です。

## <a name="scoped-synchronization-overview"></a>範囲指定された同期の概要

既定では、Azure AD ディレクトリのすべてのユーザーとグループがマネージド ドメインに同期されます。 マネージド ドメインにアクセスする必要のあるユーザーが少数しかいない場合は、それらのユーザー アカウントのみを同期することができます。 この範囲指定された同期はグループベースです。 グループベースの範囲指定された同期を構成した場合、指定したグループに属するユーザー アカウントのみがマネージド ドメインに同期されます。 入れ子になったグループは同期されません。選択した特定のグループのみが同期されます。

同期スコープは、マネージド ドメインを作成する前または後に変更できます。 同期のスコープは、アプリケーション識別子 2565bd9d-da50-47d4-8b85-4c97f669dc36 を使用してサービス プリンシパルによって定義されます。 スコープが失われないようにするには、サービス プリンシパルを削除または変更しないでください。 誤って削除された場合、同期スコープを復旧できません。 

同期スコープを変更する場合は、次の点にご注意ください。

- 完全同期が行われます。
- マネージド ドメインで不要になったオブジェクトは削除されます。 新しいオブジェクトは、マネージド ドメインに作成されます。

同期プロセスの詳細については、[Azure AD Domain Services での同期の理解][concepts-sync]に関する記事を参照してください。

## <a name="powershell-script-for-scoped-synchronization"></a>範囲指定された同期に関する PowerShell スクリプト

範囲指定された同期を PowerShell を使用して構成するには、まず、次のスクリプトを `Select-GroupsToSync.ps1` という名前のファイルに保存します。

このスクリプトでは、Azure AD から選択されたグループを同期するよう Azure AD DS を構成します。 指定されたグループに属しているユーザー アカウントはすべて、マネージド ドメインに同期されます。

このスクリプトは、この記事の追加の手順で使用します。

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

## <a name="enable-scoped-synchronization"></a>範囲指定された同期を有効にする

マネージド ドメインのグループベースの範囲指定された同期を有効にするには、次の手順を実行します。

1. 最初に Azure AD DS リソースに *"filteredSync" = "Enabled"* を設定してから、マネージド ドメインを更新します。

    プロンプトが表示されたら、"*全体管理者*" の資格情報を指定し、次の [Connect-AzureAD][Connect-AzureAD] コマンドレットを使用して Azure AD テナントにサインインします。

    ```powershell
    # Connect to your Azure AD tenant
    Connect-AzureAD

    # Retrieve the Azure AD DS resource.
    $DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

    # Enable group-based scoped synchronization.
    $enableScopedSync = @{"filteredSync" = "Enabled"}

    # Update the Azure AD DS resource
    Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $enableScopedSync
    ```

1. 次に、マネージド ドメインにユーザーを同期するグループの一覧を指定します。

    `Select-GroupsToSync.ps1` スクリプトを実行し、同期するグループの一覧を指定します。次の例で、同期するグループは *GroupName1* と *GroupName2* です。

    > [!WARNING]
    > 範囲指定された同期用のグループの一覧には、*AAD DC Administrators* グループを含める必要があります。 このグループを含めないと、マネージド ドメインは使用できません。

    ```powershell
    .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
    ```

同期のスコープを変更すると、マネージド ドメインですべてのデータが再同期されます。 マネージド ドメインで不要になったオブジェクトは削除されます。また、再同期が完了するまでには時間がかかる場合があります。

## <a name="modify-scoped-synchronization"></a>範囲指定された同期を変更する

マネージド ドメインにユーザーを同期するグループの一覧を変更するには、`Select-GroupsToSync.ps1` スクリプトを実行し、同期する新しいグループの一覧を指定します。

次の例では、同期対象のグループに *GroupName2* が含まれなくなり、*GroupName3* が含まれるようになりました。

> [!WARNING]
> 範囲指定された同期用のグループの一覧には、*AAD DC Administrators* グループを含める必要があります。 このグループを含めないと、マネージド ドメインは使用できません。

プロンプトが表示されたら、"*全体管理者*" の資格情報を指定し、次の [Connect-AzureAD][Connect-AzureAD] コマンドレットを使用して Azure AD テナントにサインインします。

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

同期のスコープを変更すると、マネージド ドメインですべてのデータが再同期されます。 マネージド ドメインで不要になったオブジェクトは削除されます。また、再同期が完了するまでには時間がかかる場合があります。

## <a name="disable-scoped-synchronization"></a>範囲指定された同期を無効にする

マネージド ドメインのグループベースの範囲指定された同期を無効にするには、Azure AD DS リソース上で *"filteredSync" = "Disabled"* を設定してから、マネージド ドメインを更新します。 完了すると、すべてのユーザーとグループが Azure AD から同期されるように設定されます。

プロンプトが表示されたら、"*全体管理者*" の資格情報を指定し、次の [Connect-AzureAD][Connect-AzureAD] コマンドレットを使用して Azure AD テナントにサインインします。

```powershell
# Connect to your Azure AD tenant
Connect-AzureAD

# Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

# Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

# Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

同期のスコープを変更すると、マネージド ドメインですべてのデータが再同期されます。 マネージド ドメインで不要になったオブジェクトは削除されます。また、再同期が完了するまでには時間がかかる場合があります。

## <a name="next-steps"></a>次のステップ

同期プロセスの詳細については、[Azure AD Domain Services での同期の理解](synchronization.md)に関する記事を参照してください。

<!-- INTERNAL LINKS -->
[scoped-sync]: scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md

<!-- EXTERNAL LINKS -->
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
