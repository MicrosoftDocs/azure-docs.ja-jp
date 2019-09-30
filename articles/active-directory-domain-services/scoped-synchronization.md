---
title: Azure AD Domain Services の範囲指定された同期 | Microsoft Docs
description: Azure AD から Azure Active Directory Domain Services マネージド ドメインへの範囲指定された同期を構成する方法について説明します。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 09/06/2019
ms.author: iainfou
ms.openlocfilehash: 5fe19d3800883782187ae15c0a6fc0cd9709f0e9
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842672"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Azure AD から Azure Active Directory Domain Services への範囲指定された同期を構成する

Azure Active Directory Domain Services (Azure AD DS) は、認証サービスを提供するため、Azure AD からユーザーとグループを同期します。 ハイブリッド環境では、最初にオンプレミスの Active Directory Domain Services (AD DS) 環境のユーザーとグループが Azure AD Connect を使用して Azure AD に同期された後、Azure AD DS に同期されます。 既定では、Azure AD ディレクトリのすべてのユーザーとグループが Azure AD DS マネージド ドメインに同期されます。 特定のニーズがある場合は、代わりに、定義したユーザー セットのみを同期することを選択できます。

この記事では、範囲指定された同期を使用する Azure AD DS マネージド ドメインを作成した後、範囲指定されたユーザー セットを変更したり無効にしたりする方法について説明します。

## <a name="scoped-synchronization-overview"></a>範囲指定された同期の概要

既定では、Azure AD ディレクトリのすべてのユーザーとグループが Azure AD DS マネージド ドメインに同期されます。 マネージド ドメインにアクセスする必要のあるユーザーが少数しかいない場合は、それらのユーザー アカウントのみを同期することができます。 この範囲指定された同期はグループベースです。 グループベースの範囲指定された同期を構成した場合、指定したグループに属するユーザー アカウントのみが Azure AD DS マネージド ドメインに同期されます。

次の表では、範囲指定された同期の使用方法を概説します。

| 現在の状態 | 期待される状態 | 必要な構成 |
| --- | --- | --- |
| 既存のマネージド ドメインはすべてのユーザー アカウントとグループを同期するように構成されています。 | 特定のグループに属するユーザー アカウントのみを同期することを希望しています。 | すべてのユーザーを同期している状態から、範囲指定された同期を使用するように変更することはできません。 [既存のマネージド ドメインを削除](delete-aadds.md)してから、この記事の手順に従い、範囲指定された同期が構成されている Azure AD DS マネージド ドメインを再作成してください。 |
| 既存のマネージド ドメインはありません。 | 新しいマネージド ドメインを作成し、特定のグループに属するユーザー アカウントのみを同期します。 | この記事の手順に従い、範囲指定された同期が構成されている Azure AD DS マネージド ドメインを作成します。 |
| 既存のマネージド ドメインは、特定のグループに属するアカウントのみを同期するように構成されています。 | Azure AD DS マネージド ドメインにユーザーが同期されるグループの一覧を変更することを希望しています。 | この記事の手順に従い、範囲指定された同期を変更します。 |

範囲指定された同期の設定を構成するには、Azure portal または PowerShell を使用します。

| Action | | |
|--|--|--|
| Azure AD DS マネージド ドメインを作成し、範囲指定された同期を構成する | [Azure Portal](#enable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#enable-scoped-synchronization-using-powershell) |
| 範囲指定された同期を変更する | [Azure Portal](#modify-scoped-synchronization-using-the-azure-portal) | [PowerShell](#modify-scoped-synchronization-using-powershell) |
| 範囲指定された同期を無効にする | [Azure Portal](#disable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> 同期のスコープを変更すると、Azure AD DS マネージド ドメインですべてのデータが再同期されます。
> 
>  * Azure AD DS マネージド ドメインの同期スコープを変更すると、完全な再同期が発生します。
>  * Azure AD DS マネージド ドメインで不要になったオブジェクトは削除されます。 新しいオブジェクトは、マネージド ドメインに作成されます。
>  * 再同期が完了するまでには時間がかかる場合があります。 同期時間は、Azure AD DS マネージド ドメイン内や Azure AD ディレクトリ内のユーザー、グループ、グループ メンバーシップなどのオブジェクトの数によって異なります。 数十万のオブジェクトを含む大規模なディレクトリについては、再同期に数日かかる場合があります。

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>範囲指定された同期を Azure portal を使用して有効にする

1. [Azure AD DS インスタンスを作成して構成するためのチュートリアル](tutorial-create-instance.md)に従います。 同期スコープ以外のすべての前提条件とデプロイ手順を完了します。
1. 同期の手順で **[範囲指定]** を選択してから、Azure AD DS インスタンスに同期する Azure AD グループを選択します。

Azure AD DS マネージド ドメインのデプロイが完了するまで最大 1 時間かかる場合があります。 Azure portal では、お使いの Azure AD DS マネージド ドメインの **[概要]** ページに、このデプロイ ステージ全体の現在の状態が表示されます。

Azure AD DS マネージド ドメインがプロビジョニングを完了したことが Azure portal に示されたら、次のタスクを完了する必要があります。

* 仮想マシンがマネージド ドメインを検出してドメイン参加または認証を行うことができるように、仮想ネットワークの DNS 設定を更新します。
    * DNS を構成するには、ポータルで Azure AD DS のマネージド ドメインを選択します。 **[概要]** ウィンドウで、これらの DNS 設定を自動的に構成するように求められます。
* [Azure AD Domain Services とのパスワード同期を有効にして](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)、エンド ユーザーが会社の資格情報を使用してマネージド ドメインにサインインできるようにします。

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>範囲指定された同期を Azure portal を使用して変更する

Azure AD DS マネージド ドメインにユーザーが同期されるグループの一覧を変更するには、次の手順を実行します。

1. Azure portal で、*contoso.com* などのお使いの Azure AD DS インスタンスを選択します。
1. 左側のメニューで、 **[同期]** を選択します。
1. グループを追加するには、上部にある **[+ グループの選択]** を選択し、追加するグループを選択します。
1. 同期スコープからグループを削除するには、現在同期されているグループの一覧からそのグループを選択し、 **[グループの削除]** を選択します。
1. すべての変更が行われたら、 **[同期スコープを保存します]** を選択します。

同期のスコープを変更すると、Azure AD DS マネージド ドメインですべてのデータが再同期されます。 Azure AD DS マネージド ドメインで不要になったオブジェクトは削除されます。また、再同期が完了するまでには時間がかかる場合があります。

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>範囲指定された同期を Azure portal を使用して無効にする

Azure AD DS マネージド ドメインのグループベースの範囲指定された同期を無効にするには、次の手順を実行します。

1. Azure portal で、*contoso.com* などのお使いの Azure AD DS インスタンスを選択します。
1. 左側のメニューで、 **[同期]** を選択します。
1. 同期スコープを **[範囲指定]** から **[すべて]** に設定してから、 **[同期スコープを保存します]** を選択します。

同期のスコープを変更すると、Azure AD DS マネージド ドメインですべてのデータが再同期されます。 Azure AD DS マネージド ドメインで不要になったオブジェクトは削除されます。また、再同期が完了するまでには時間がかかる場合があります。

## <a name="powershell-script-for-scoped-synchronization"></a>範囲指定された同期に関する PowerShell スクリプト

範囲指定された同期を PowerShell を使用して構成するには、まず、次のスクリプトを `Select-GroupsToSync.ps1` という名前のファイルに保存します。 このスクリプトでは、Azure AD から選択されたグループを同期するよう Azure AD DS を構成します。 指定されたグループに属しているユーザー アカウントはすべて、Azure AD DS マネージド ドメインに同期されます。

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

## <a name="enable-scoped-synchronization-using-powershell"></a>範囲指定された同期を PowerShell を使用して有効にする

この一連の手順を完了するには、PowerShell を使用します。 [PowerShell を使用した Azure Active Directory Domain Services の有効化](powershell-create-instance.md)の手順を参照してください。 この記事の手順のいくつかは、スコープ付きの同期を構成するために若干変更されます。

1. この記事の次のタスクを実行して、PowerShell を使用して Azure AD DS を有効にします。 マネージド ドメインを実際に作成する手順で停止します。 範囲指定された同期は、Azure AD DS マネージド ドメインの作成時に構成します。

   * [必要な PowerShell モジュールをインストールする](powershell-create-instance.md#prerequisites)。
   * [管理アクセスのために必要なサービス プリンシパルと Azure AD グループを作成する](powershell-create-instance.md#create-required-azure-ad-resources)。
   * [仮想ネットワークやサブネットなどのサポート対象の Azure リソースを作成する](powershell-create-instance.md#create-supporting-azure-resources)。

1. Azure AD から同期対象として含めるグループとユーザーを決定します。 Azure AD DS に同期するグループの表示名の一覧を作成します。

1. [前のセクションのスクリプト](#powershell-script-for-scoped-synchronization)を実行し、 *-groupsToAdd* パラメーターを使用して、同期するグループの一覧を渡します。

   > [!WARNING]
   > 範囲指定された同期用のグループの一覧には、*AAD DC Administrators* グループを含める必要があります。 このグループを含めないと、Azure AD DS マネージド ドメインは使用できません。

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. ここで、Azure AD DS マネージド ドメインを作成し、グループベースの範囲指定された同期を有効にします。 *-Properties* パラメーターに *"filteredSync" = "Enabled"* を含めます。

    お使いの Azure サブスクリプション ID を設定し、マネージド ドメインの名前 (*contoso.com* など) を指定します。 お使いのサブスクリプション ID は、[Get-AzSubscription][Get-AzSubscription] コマンドレットを使用して取得できます。 サポート対象の Azure リソースを作成するには、リソース グループ名、仮想ネットワーク名、およびリージョンを前の手順で使用した値に設定します。

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso.com"
   $ResourceGroupName = "myResourceGroup"
   $VnetName = "myVnet"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -Force -Verbose
   ```

リソースが作成され、PowerShell プロンプトに制御が戻るまで数分かかります。 Azure AD DS マネージド ドメインは引き続きバックグラウンドでプロビジョニングされ、デプロイが完了するまでには最大 1 時間かかる場合があります。 Azure portal では、お使いの Azure AD DS マネージド ドメインの **[概要]** ページに、このデプロイ ステージ全体の現在の状態が表示されます。

Azure AD DS マネージド ドメインがプロビジョニングを完了したことが Azure portal に示されたら、次のタスクを完了する必要があります。

* 仮想マシンがマネージド ドメインを検出してドメイン参加または認証を行うことができるように、仮想ネットワークの DNS 設定を更新します。
    * DNS を構成するには、ポータルで Azure AD DS のマネージド ドメインを選択します。 **[概要]** ウィンドウで、これらの DNS 設定を自動的に構成するように求められます。
* [Azure AD Domain Services とのパスワード同期を有効にして](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)、エンド ユーザーが会社の資格情報を使用してマネージド ドメインにサインインできるようにします。

## <a name="modify-scoped-synchronization-using-powershell"></a>範囲指定された同期を PowerShell を使用して変更する

Azure AD DS マネージド ドメインにユーザーが同期されるグループの一覧を変更するには、[PowerShell スクリプト](#powershell-script-for-scoped-synchronization)を再実行し、新しいグループの一覧を指定します。 次の例では、同期対象のグループに *GroupName2* が含まれなくなり、*GroupName3* が含まれるようになりました。

> [!WARNING]
> 範囲指定された同期用のグループの一覧には、*AAD DC Administrators* グループを含める必要があります。 このグループを含めないと、Azure AD DS マネージド ドメインは使用できません。

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

同期のスコープを変更すると、Azure AD DS マネージド ドメインですべてのデータが再同期されます。 Azure AD DS マネージド ドメインで不要になったオブジェクトは削除されます。また、再同期が完了するまでには時間がかかる場合があります。

## <a name="disable-scoped-synchronization-using-powershell"></a>範囲指定された同期を PowerShell を使用して無効にする

Azure AD DS マネージド ドメインのグループベースの範囲指定された同期を無効にするには、Azure AD DS リソース上で *"filteredSync" = "Disabled"* を設定してから、マネージド ドメインを更新します。 完了すると、すべてのユーザーとグループが Azure AD から同期されるように設定されます。

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

同期のスコープを変更すると、Azure AD DS マネージド ドメインですべてのデータが再同期されます。 Azure AD DS マネージド ドメインで不要になったオブジェクトは削除されます。また、再同期が完了するまでには時間がかかる場合があります。

## <a name="next-steps"></a>次の手順

同期プロセスの詳細については、[Azure AD Domain Services での同期の理解](synchronization.md)に関する記事を参照してください。

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
