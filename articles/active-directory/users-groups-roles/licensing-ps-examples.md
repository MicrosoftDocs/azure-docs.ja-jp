---
title: ライセンス グループ用の PowerShell と Graph の例 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory のグループベースのライセンスが使用される PowerShell と Graph の例とシナリオ
services: active-directory
keywords: Azure AD のライセンス
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: f95c0596d7a2b55867cdb7ed9355006500e89242
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065498"
---
# <a name="powershell-and-graph-examples-for-group-based-licensing-in-azure-ad"></a>Azure AD でのグループベース ライセンスの PowerShell と Graph の例

グループベースのライセンスの機能はすべて [Azure ポータル](https://portal.azure.com)を通じて利用できます。現在、PowerShell と Microsoft Graph のサポートは読み取り専用操作に制限されていますが、 既存の [MSOnline PowerShell コマンドレット](https://docs.microsoft.com/powershell/msonline/v1/azureactivedirectory) と Microsoft Graph を使用して実行できる便利なタスクがいくつかあります。 このドキュメントでは、利用できる機能のいくつかの例を示します。

> [!NOTE]
> コマンドレットの実行を開始する前に、`Connect-MsolService` コマンドレットを実行して、組織に接続していることを最初に確認します。

> [!WARNING]
> このコードは、デモンストレーション用のサンプルとして提供されています。 ご利用の環境で使用する場合は、まず小規模にテストするか別のテスト テナントでテストすることを検討してください。 お使いの環境の具体的なニーズに合わせてコードの調整が必要になる場合があります。

## <a name="view-product-licenses-assigned-to-a-group"></a>グループに割り当てられた製品ライセンスの表示

[Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0) コマンドレットでは、グループ オブジェクトを取得し、*ライセンス* プロパティを確認できます (グループに現在割り当てられているすべての製品ライセンスが一覧表示されます)。

```powershell
(Get-MsolGroup -ObjectId 99c4216a-56de-42c4-a4ac-e411cd8c7c41).Licenses
| Select SkuPartNumber
```
出力:
```
SkuPartNumber
-------------
ENTERPRISEPREMIUM
EMSPREMIUM
```

> [!NOTE]
> データは、製品 (SKU) 情報に限定されます。 ライセンスで無効になっているサービス プランを一覧表示することはできません。

Microsoft Graph から、同じデータを取得するには次のサンプルを使用します。

```
GET https://graph.microsoft.com/v1.0/groups/99c4216a-56de-42c4-a4ac-e411cd8c7c41$select=assignedLicenses
```
出力:
```
HTTP/1.1 200 OK
{
  “value”: [
{
  “assignedLicenses”: [
     {
          "accountId":"f1b45b40-57df-41f7-9596-7f2313883635",
          "skuId":"c7df2760-2c81-4ef7-b578-5b5392b571df",
      "disabledPlans":[]
     },
     {
          "accountId":"f1b45b40-57df-41f7-9596-7f2313883635",
          "skuId":" b05e124f-c7cc-45a0-a6aa-8cf78c946968",
      "disabledPlans":[]
     },
  ],
}
  ]
}
```

## <a name="get-all-groups-with-licenses"></a>ライセンスを持つすべてのグループを取得する

次のコマンドを実行して、割り当てられているすべてのライセンスを持つグループをすべて検索できます。
```powershell
Get-MsolGroup | Where {$_.Licenses}
```
割り当てられている製品に関する詳細情報を表示することもできます。
```powershell
Get-MsolGroup | Where {$_.Licenses} | Select `
    ObjectId, `
    DisplayName, `
    @{Name="Licenses";Expression={$_.Licenses | Select -ExpandProperty SkuPartNumber}}
```

出力:
```
ObjectId                             DisplayName              Licenses
--------                             -----------              --------
7023a314-6148-4d7b-b33f-6c775572879a EMS E5 – Licensed users  EMSPREMIUM
cf41f428-3b45-490b-b69f-a349c8a4c38e PowerBi - Licensed users POWER\_BI\_STANDARD
962f7189-59d9-4a29-983f-556ae56f19a5 O365 E3 - Licensed users ENTERPRISEPACK
c2652d63-9161-439b-b74e-fcd8228a7074 EMSandOffice             {ENTERPRISEPREMIUM,EMSPREMIUM}
```

## <a name="get-statistics-for-groups-with-licenses"></a>ライセンスを持つグループの統計を取得する
ライセンスを持つグループの基本的な統計情報を報告できます。 次のスクリプト例は、合計ユーザー数、グループに既に割り当てられているライセンスを持つユーザーの数、およびグループにライセンスを割り当てることができなかったユーザーの数を一覧表示します。

```powershell
#get all groups with licenses
Get-MsolGroup -All | Where {$_.Licenses}  | Foreach {
    $groupId = $_.ObjectId;
    $groupName = $_.DisplayName;
    $groupLicenses = $_.Licenses | Select -ExpandProperty SkuPartNumber
    $totalCount = 0;
    $licenseAssignedCount = 0;
    $licenseErrorCount = 0;

    Get-MsolGroupMember -All -GroupObjectId $groupId |
    #get full info about each user in the group
    Get-MsolUser -ObjectId {$_.ObjectId} |     Foreach {
        $user = $_;
        $totalCount++

        #check if any licenses are assigned via this group
        if($user.Licenses | ? {$_.GroupsAssigningLicense -ieq $groupId })
        {
            $licenseAssignedCount++
        }
        #check if user has any licenses that failed to be assigned from this group
        if ($user.IndirectLicenseErrors | ? {$_.ReferencedObjectId -ieq $groupId })
        {
            $licenseErrorCount++
        }     
    }

    #aggregate results for this group
    New-Object Object |
                    Add-Member -NotePropertyName GroupName -NotePropertyValue $groupName -PassThru |
                    Add-Member -NotePropertyName GroupId -NotePropertyValue $groupId -PassThru |
                    Add-Member -NotePropertyName GroupLicenses -NotePropertyValue $groupLicenses -PassThru |
                    Add-Member -NotePropertyName TotalUserCount -NotePropertyValue $totalCount -PassThru |
                    Add-Member -NotePropertyName LicensedUserCount -NotePropertyValue $licenseAssignedCount -PassThru |
                    Add-Member -NotePropertyName LicenseErrorCount -NotePropertyValue $licenseErrorCount -PassThru

    } | Format-Table
```


出力:
```
GroupName         GroupId                              GroupLicenses       TotalUserCount LicensedUserCount LicenseErrorCount
---------         -------                              -------------       -------------- ----------------- -----------------
Dynamics Licen... 9160c903-9f91-4597-8f79-22b6c47eafbf AAD_PREMIUM_P2                   0                 0                 0
O365 E5 - base... 055dcca3-fb75-4398-a1b8-f8c6f4c24e65 ENTERPRISEPREMIUM                2                 2                 0
O365 E5 - extr... 6b14a1fe-c3a9-4786-9ee4-3a2bb54dcb8e ENTERPRISEPREMIUM                3                 3                 0
EMS E5 - all s... 7023a314-6148-4d7b-b33f-6c775572879a EMSPREMIUM                       2                 2                 0
PowerBi - Lice... cf41f428-3b45-490b-b69f-a349c8a4c38e POWER_BI_STANDARD                2                 2                 0
O365 E3 - all ... 962f7189-59d9-4a29-983f-556ae56f19a5 ENTERPRISEPACK                   2                 2                 0
O365 E5 - EXO     102fb8f4-bbe7-462b-83ff-2145e7cdd6ed ENTERPRISEPREMIUM                1                 1                 0
Access to Offi... 11151866-5419-4d93-9141-0603bbf78b42 STANDARDPACK                     4                 3                 1
```

## <a name="get-all-groups-with-license-errors"></a>ライセンス エラーがあるすべてのグループを取得する
ライセンスを割り当てることができなかったユーザーを含むグループを見つけるには、次の操作を実行します。
```powershell
Get-MsolGroup -HasLicenseErrorsOnly $true
```
出力:
```
ObjectId                             DisplayName             GroupType Description
--------                             -----------             --------- -----------
11151866-5419-4d93-9141-0603bbf78b42 Access to Office 365 E1 Security  Users who should have E1 licenses
```
Microsoft Graph から、同じデータを取得するには次を使用します
```
GET https://graph.microsoft.com/v1.0/groups?$filter=hasMembersWithLicenseErrors+eq+true
```
出力:
```
HTTP/1.1 200 OK
{
  "value":[
    {
      "odata.type": "Microsoft.DirectoryServices.Group",
      "objectType": "Group",
      "id": "11151866-5419-4d93-9141-0603bbf78b42",
      ... # other group properties.
    },
    {
      "odata.type": "Microsoft.DirectoryServices.Group",
      "objectType": "Group",
      "id": "c57cdc98-0dcd-4f90-a82f-c911b288bab9",
      ...
    },
    ... # other groups with license errors.
  ]
"odata.nextLink":"https://graph.microsoft.com/v1.0/ groups?$filter=hasMembersWithLicenseErrors+eq+true&$skipToken=<encodedPageToken>"
}
```


## <a name="get-all-users-with-license-errors-in-a-group"></a>グループ内のライセンス エラーがあるすべてのユーザーを取得する

ライセンス関連のエラーがあるグループについて、これらのエラーの影響を受けるすべてのユーザーを表示できるようになりました。 ユーザーが他のグループからのエラーを持つ場合もあります。 ただし、この例では、対象のグループに関連するエラーのみに結果を制限しています。このためには、ユーザーの各 **IndirectLicenseError** エントリの **ReferencedObjectId** プロパティを確認します。

```powershell
#a sample group with errors
$groupId = '11151866-5419-4d93-9141-0603bbf78b42'

#get all user members of the group
Get-MsolGroupMember -All -GroupObjectId $groupId |
    #get full information about user objects
    Get-MsolUser -ObjectId {$_.ObjectId} |
    #filter out users without license errors and users with license errors from other groups
    Where {$_.IndirectLicenseErrors -and $_.IndirectLicenseErrors.ReferencedObjectId -eq $groupId} |
    #display id, name and error detail. Note: we are filtering out license errors from other groups
    Select ObjectId, `
           DisplayName, `
           @{Name="LicenseError";Expression={$_.IndirectLicenseErrors | Where {$_.ReferencedObjectId -eq $groupId} | Select -ExpandProperty Error}}
```

出力:

```powershell
ObjectId                             DisplayName      License Error
--------                             -----------      ------------
6d325baf-22b7-46fa-a2fc-a2500613ca15 Catherine Gibson MutuallyExclusiveViolation
```

Microsoft Graph から、同じデータを取得するには次を使用します。

```powershell
GET https://graph.microsoft.com/v1.0/groups/11151866-5419-4d93-9141-0603bbf78b42/membersWithLicenseErrors
```

出力:
```powershell
HTTP/1.1 200 OK
{
  "value":[
    {
      "odata.type": "Microsoft.DirectoryServices.User",
      "objectType": "User",
      "id": "6d325baf-22b7-46fa-a2fc-a2500613ca15",
      ... # other user properties.
    },
    ... # other users.
  ],
  "odata.nextLink":"https://graph.microsoft.com/v1.0/groups/11151866-5419-4d93-9141-0603bbf78b42/membersWithLicenseErrors?$skipToken=<encodedPageToken>"
}

```

## <a name="get-all-users-with-license-errors-in-the-entire-tenant"></a>テナント全体のライセンス エラーがあるすべてのユーザーを取得する

次のスクリプトを使用すると、1 つ以上のグループからのライセンス エラーを持つすべてのユーザーを一覧表示できます。 このスクリプトでは、ユーザーごと、またライセンス エラーごとに 1 行を出力するので、各エラーのソースを明確に識別することができます。

> [!NOTE]
> このスクリプトではテナントのすべてのユーザーが列挙されるため、大きなテナントでは最適でない場合があります。

```powershell
Get-MsolUser -All | Where {$_.IndirectLicenseErrors } | % {   
    $user = $_;
    $user.IndirectLicenseErrors | % {
            New-Object Object |
                Add-Member -NotePropertyName UserName -NotePropertyValue $user.DisplayName -PassThru |
                Add-Member -NotePropertyName UserId -NotePropertyValue $user.ObjectId -PassThru |
                Add-Member -NotePropertyName GroupId -NotePropertyValue $_.ReferencedObjectId -PassThru |
                Add-Member -NotePropertyName LicenseError -NotePropertyValue $_.Error -PassThru
        }
    }  
```

出力:

```powershell
UserName         UserId                               GroupId                              LicenseError
--------         ------                               -------                              ------------
Anna Bergman     0d0fd16d-872d-4e87-b0fb-83c610db12bc 7946137d-b00d-4336-975e-b1b81b0666d0 MutuallyExclusiveViolation
Catherine Gibson 6d325baf-22b7-46fa-a2fc-a2500613ca15 f2503e79-0edc-4253-8bed-3e158366466b CountViolation
Catherine Gibson 6d325baf-22b7-46fa-a2fc-a2500613ca15 11151866-5419-4d93-9141-0603bbf78b42 MutuallyExclusiveViolation
Drew Fogarty     f2af28fc-db0b-4909-873d-ddd2ab1fd58c 1ebd5028-6092-41d0-9668-129a3c471332 MutuallyExclusiveViolation
```

以下は、ライセンス エラーを含むグループのみを検索するスクリプトの別バージョンの例です。 これは、問題のあるグループが少数であることが予測されるシナリオにより適しています。

```powershell
$groupIds = Get-MsolGroup -HasLicenseErrorsOnly $true
    foreach ($groupId in $groupIds) {
    Get-MsolGroupMember -All -GroupObjectId $groupId.ObjectID |
        Get-MsolUser -ObjectId {$_.ObjectId} |
        Where {$_.IndirectLicenseErrors -and $_.IndirectLicenseErrors.ReferencedObjectId -eq $groupId.ObjectID} |
        Select DisplayName, `
               ObjectId, `
               @{Name="LicenseError";Expression={$_.IndirectLicenseErrors | Where {$_.ReferencedObjectId -eq $groupId.ObjectID} | Select -ExpandProperty Error}}
 
    } 
``` 

## <a name="check-if-user-license-is-assigned-directly-or-inherited-from-a-group"></a>ユーザー ライセンスが直接割り当てられたものか、グループから継承されたものかを確認する

特定のユーザー オブジェクトについて、特定の製品ライセンスがグループから割り当てられているか、または直接割り当てられているかを確認することができます。

以下の 2 つの関数の例を使用すると、各ユーザーの割り当ての種類を分析できます。

```powershell
#Returns TRUE if the user has the license assigned directly
function UserHasLicenseAssignedDirectly
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    foreach($license in $user.Licenses)
    {
        #we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
            #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
            #This could be a group object or a user object (contrary to what the name suggests)
            #If the collection is empty, this means the license is assigned directly - this is the case for users who have never been licensed via groups in the past
            if ($license.GroupsAssigningLicense.Count -eq 0)
            {
                return $true
            }

            #If the collection contains the ID of the user object, this means the license is assigned directly
            #Note: the license may also be assigned through one or more groups in addition to being assigned directly
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
            {
                if ($assignmentSource -ieq $user.ObjectId)
                {
                    return $true
                }
            }
            return $false
        }
    }
    return $false
}
#Returns TRUE if the user is inheriting the license from a group
function UserHasLicenseAssignedFromGroup
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    foreach($license in $user.Licenses)
    {
        #we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
            #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
            #This could be a group object or a user object (contrary to what the name suggests)
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
            {
                #If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                #Note: the license may also be assigned directly in addition to being inherited
                if ($assignmentSource -ine $user.ObjectId)
                {
                    return $true
                }
            }
            return $false
        }
    }
    return $false
}
```

このスクリプトは、SKU ID を入力値として利用し、テナントの各ユーザーに関数を実行します。この例では、*Enterprise Mobility + Security* のライセンスに注目します。テナントでこれは *contoso:EMS* という ID で表されます。

```powershell
#the license SKU we are interested in. use Get-MsolAccountSku to see a list of all identifiers in your tenant
$skuId = "contoso:EMS"

#find all users that have the SKU license assigned
Get-MsolUser -All | where {$_.isLicensed -eq $true -and $_.Licenses.AccountSKUID -eq $skuId} | select `
    ObjectId, `
    @{Name="SkuId";Expression={$skuId}}, `
    @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly $_ $skuId)}}, `
    @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup $_ $skuId)}}
```

出力:

```powershell
ObjectId                             SkuId       AssignedDirectly AssignedFromGroup
--------                             -----       ---------------- -----------------
157870f6-e050-4b3c-ad5e-0f0a377c8f4d contoso:EMS             True             False
1f3174e2-ee9d-49e9-b917-e8d84650f895 contoso:EMS            False              True
240622ac-b9b8-4d50-94e2-dad19a3bf4b5 contoso:EMS             True              True
```

Graph には、結果を表示する簡単な方法はありませんが、この API から確認することができます。

```powershell
GET https://graph.microsoft.com/v1.0/users/e61ff361-5baf-41f0-b2fd-380a6a5e406a?$select=licenseAssignmentStates
```

出力:

```powershell
HTTP/1.1 200 OK
{
  "value":[
    {
      "odata.type": "Microsoft.DirectoryServices.User",
      "objectType": "User",
      "id": "e61ff361-5baf-41f0-b2fd-380a6a5e406a",
      "licenseAssignmentState":[
        {
          "skuId": "157870f6-e050-4b3c-ad5e-0f0a377c8f4d”,
          "disabledPlans":[],
          "assignedByGroup": null, # assigned directly.
          "state": "Active",
          "error": "None"
        },
        {
          "skuId": "1f3174e2-ee9d-49e9-b917-e8d84650f895",
          "disabledPlans":[],
          "assignedByGroup": “e61ff361-5baf-41f0-b2fd-380a6a5e406a”, # assigned by this group.
          "state": "Active",
          "error": "None"
        },
        {
          "skuId": "240622ac-b9b8-4d50-94e2-dad19a3bf4b5", 
          "disabledPlans":[
            "e61ff361-5baf-41f0-b2fd-380a6a5e406a"
          ],
          "assignedByGroup": "e61ff361-5baf-41f0-b2fd-380a6a5e406a",
          "state": "Active",
          "error": "None"
        },
        {
          "skuId": "240622ac-b9b8-4d50-94e2-dad19a3bf4b5",
          "disabledPlans":[],
          "assignedByGroup": null, # It is the same license as the previous one. It means the license is assigned directly once and inherited from group as well.
          "state": " Active ",
          "error": " None"
        }
      ],
      ...
    }
  ],
}

```

## <a name="remove-direct-licenses-for-users-with-group-licenses"></a>グループ ライセンスを持つユーザーの直接付与されたライセンスを削除する

このスクリプトの目的は、グループから既に同じライセンスを継承している (例: [グループベースのライセンスへの移行](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-migration-azure-portal)の一環として) ユーザーの不要な直接ライセンスを削除することです。
> [!NOTE]
> まず、削除する直接ライセンスで、継承されたライセンスより多い数のサービス機能が有効にされないことを検証することが重要です。 そうしないと、直接付与されたライセンスを削除したときに、ユーザーのサービスおよびデータへのアクセスが無効になる可能性があります。 現在、継承されたライセンスまたは直接ライセンスによって有効になっているサービスを PowerShell で確認することはできません。 このスクリプトでは、グループから継承されることがわかっているサービスの最小レベルを指定し、それに照らして確認を行うことで、サービスに対するユーザーのアクセス権が予期せず失われないようにしています。

```powershell
#BEGIN: Helper functions used by the script

#Returns TRUE if the user has the license assigned directly
function UserHasLicenseAssignedDirectly
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    $license = GetUserLicense $user $skuId

    if ($license -ne $null)
    {
        #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
        #This could be a group object or a user object (contrary to what the name suggests)
        #If the collection is empty, this means the license is assigned directly - this is the case for users who have never been licensed via groups in the past
        if ($license.GroupsAssigningLicense.Count -eq 0)
        {
            return $true
        }

        #If the collection contains the ID of the user object, this means the license is assigned directly
        #Note: the license may also be assigned through one or more groups in addition to being assigned directly
        foreach ($assignmentSource in $license.GroupsAssigningLicense)
        {
            if ($assignmentSource -ieq $user.ObjectId)
            {
                return $true
            }
        }
        return $false
    }
    return $false
}
#Returns TRUE if the user is inheriting the license from a specific group
function UserHasLicenseAssignedFromThisGroup
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [Guid]$groupId)

    $license = GetUserLicense $user $skuId

    if ($license -ne $null)
    {
        #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
        #This could be a group object or a user object (contrary to what the name suggests)
        foreach ($assignmentSource in $license.GroupsAssigningLicense)
        {
            #If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
            #Note: the license may also be assigned directly in addition to being inherited
            if ($assignmentSource -ieq $groupId)
            {
                return $true
            }
        }
        return $false
    }
    return $false
}

#Returns the license object corresponding to the skuId. Returns NULL if not found
function GetUserLicense
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [Guid]$groupId)
    #we look for the specific license SKU in all licenses assigned to the user
    foreach($license in $user.Licenses)
    {
        if ($license.AccountSkuId -ieq $skuId)
        {
            return $license
        }
    }
    return $null
}

#produces a list of disabled service plan names for a set of plans we want to leave enabled
function GetDisabledPlansForSKU
{
    Param([string]$skuId, [string[]]$enabledPlans)

    $allPlans = Get-MsolAccountSku | where {$_.AccountSkuId -ieq $skuId} | Select -ExpandProperty ServiceStatus | Where {$_.ProvisioningStatus -ine "PendingActivation" -and $_.ServicePlan.TargetClass -ieq "User"} | Select -ExpandProperty ServicePlan | Select -ExpandProperty ServiceName
    $disabledPlans = $allPlans | Where {$enabledPlans -inotcontains $_}

    return $disabledPlans
}

function GetUnexpectedEnabledPlansForUser
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [string[]]$expectedDisabledPlans)

    $license = GetUserLicense $user $skuId

    $extraPlans = @();

    if($license -ne $null)
    {
        $userDisabledPlans = $license.ServiceStatus | where {$_.ProvisioningStatus -ieq "Disabled"} | Select -ExpandProperty ServicePlan | Select -ExpandProperty ServiceName

        $extraPlans = $expectedDisabledPlans | where {$userDisabledPlans -notcontains $_}
    }
    return $extraPlans
}
#END: helper functions

#BEGIN: executing the script
#the group to be processed
$groupId = "48ca647b-7e4d-41e5-aa66-40cab1e19101"

#license to be removed - Office 365 E3
$skuId = "contoso:ENTERPRISEPACK"

#minimum set of service plans we know are inherited from groups - we want to make sure that there aren't any users who have more services enabled
#which could mean that they may lose access after we remove direct licenses
$servicePlansFromGroups = ("EXCHANGE_S_ENTERPRISE", "SHAREPOINTENTERPRISE", "OFFICESUBSCRIPTION")

$expectedDisabledPlans = GetDisabledPlansForSKU $skuId $servicePlansFromGroups

#process all members in the group and get full info about each user in the group looping through group members. 
Get-MsolGroupMember -All -GroupObjectId $groupId | Get-MsolUser -ObjectId {$_.ObjectId} | Foreach {
        $user = $_;
        $operationResult = "";

        #check if Direct license exists on the user
        if (UserHasLicenseAssignedDirectly $user $skuId)
        {
            #check if the license is assigned from this group, as expected
            if (UserHasLicenseAssignedFromThisGroup $user $skuId $groupId)
            {
                #check if there are any extra plans we didn't expect - we are being extra careful not to remove unexpected services
                $extraPlans = GetUnexpectedEnabledPlansForUser $user $skuId $expectedDisabledPlans
                if ($extraPlans.Count -gt 0)
                {
                    $operationResult = "User has extra plans that may be lost - license removal was skipped. Extra plans: $extraPlans"
                }
                else
                {
                    #remove the direct license from user
                    Set-MsolUserLicense -ObjectId $user.ObjectId -RemoveLicenses $skuId
                    $operationResult = "Removed direct license from user."   
                }

            }
            else
            {
                $operationResult = "User does not inherit this license from this group. License removal was skipped."
            }
        }
        else
        {
            $operationResult = "User has no direct license to remove. Skipping."
        }

        #format output
        New-Object Object |
                    Add-Member -NotePropertyName UserId -NotePropertyValue $user.ObjectId -PassThru |
                    Add-Member -NotePropertyName OperationResult -NotePropertyValue $operationResult -PassThru
    } | Format-Table
#END: executing the script
```

出力:

```powershell
UserId                               OperationResult
------                               ---------------
7c7f860f-700a-462a-826c-f50633931362 Removed direct license from user.
0ddacdd5-0364-477d-9e4b-07eb6cdbc8ea User has extra plans that may be lost - license removal was skipped. Extra plans: SHAREPOINTWAC
aadbe4da-c4b5-4d84-800a-9400f31d7371 User has no direct license to remove. Skipping.
```
> [!NOTE]
> 上記のスクリプトを実行する前に、テスト環境による直接付与されたライセンスの削除の対象になっている変数 `$skuId` と `$groupId` の値を更新してください。 

## <a name="next-steps"></a>次の手順

グループを使用したライセンス管理の機能セットについては、以下の記事をご覧ください。

* [What is group-based licensing in Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md) (Azure Active Directory のグループベースのライセンスとは)
* [Assigning licenses to a group in Azure Active Directory](licensing-groups-assign.md) (Azure Active Directory でのグループへのライセンス割り当て)
* [Azure Active Directory のグループのライセンスに関する問題の特定と解決](licensing-groups-resolve-problems.md)
* [Azure Active Directory で個別にライセンスを付与されたユーザーをグループベースのライセンスに移行する方法](licensing-groups-migrate-users.md)
* [Azure Active Directory のグループベースのライセンスを使用して製品ライセンス間でユーザーを移行する方法](../users-groups-roles/licensing-groups-change-licenses.md)
* [Azure Active Directory グループベース ライセンスのその他のシナリオ](licensing-group-advanced.md)
* [Azure Active Directory のグループベースのライセンスの PowerShell の例](../users-groups-roles/licensing-ps-examples.md)
