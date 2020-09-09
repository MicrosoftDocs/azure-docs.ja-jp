---
title: 管理単位のグループを追加、削除、一覧表示する (プレビュー) - Azure Active Directory | Microsoft Docs
description: Azure Active Directory で管理単位のグループとそのロールのアクセス許可を管理します
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 06/23/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76026313eea8c8fbb2f3e55321e2e4ebbe5dcfc7
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85850911"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Azure Active Directory で管理単位にグループを追加して管理する

Azure Active Directory (Azure AD) では、きめ細かい管理スコープで制御するために、グループを管理単位 (AU) に追加できます。

管理単位の管理に PowerShell や Microsoft Graph を使用するよう準備する手順については、[作業の開始](roles-admin-units-manage.md#get-started)に関するページを参照してください。

## <a name="add-groups-to-an-au"></a>AU にグループを追加する

### <a name="azure-portal"></a>Azure portal

プレビューでは、グループを管理単位に個別に割り当てることだけができます。 グループを管理単位に一括割り当てする選択肢はありません。 ポータルでは、次の 2 つの方法のいずれかで、グループを管理単位に割り当てることができます。

1. **Azure AD > [グループ]** ページから

    Azure AD の [グループ] の概要ページを開き、管理単位に割り当てる必要のあるグループを選択します。 左側で、 **[管理単位]** を選択して、グループが割り当てられている管理単位を一覧表示します。 上部に [管理単位に割り当てる] オプションが表示され、それをクリックすると、管理単位を選択するためのパネルが右側に表示されます。

    ![グループを管理単位に個別に割り当てる](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. **Azure AD > [管理単位] > [すべてのグループ]** ページから

    Azure AD > [管理単位] で [すべてのグループ] ブレードを開きます。 管理単位に既に割り当てられているグループがある場合は、右側に表示されます。 上部にある **[追加]** を選択すると、右側のパネルがスライドして開き、Azure AD 組織で利用可能なグループが一覧表示されます。 管理単位に割り当てるグループを 1 つまたは複数選択します。

    ![管理単位を選択して、[メンバーの追加] を選択する](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId
```

この例では、コマンドレット Add-AzureADAdministrativeUnitMember を使用して、グループを管理単位に追加します。 管理単位のオブジェクト ID と追加するグループのオブジェクト ID が引数として受け取られます。 強調表示されたセクションは、それぞれの環境で必要に応じて変更される場合があります。

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref

Request body
{
"@odata.id":"https://graph.microsoft.com/beta/groups/{id}"
}
```

例:

```http
{
"@odata.id":"https://graph.microsoft.com/beta/groups/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="list-groups-in-an-au"></a>AU 内のグループを一覧表示する

### <a name="azure-portal"></a>Azure portal

ポータルで、**Azure AD > [管理単位]** の順にアクセスします。 ユーザーを一覧表示する管理単位を選択します。 既定では、左側のパネルで **[すべてのユーザー]** が既に選択されています。 **[すべての グループ]** を選択すると、選択した管理単位のメンバーであるグループの一覧が右側に表示されます。

![管理単位のグループを一覧表示する](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId
```

これは、管理単位のすべてのメンバーを取得する場合に役立ちます。 管理単位のメンバーであるすべてのグループを表示する場合は、次のコード スニペットを使用できます。

```http
foreach ($member in (Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```
### <a name="microsoft-graph"></a>Microsoft Graph

```http
HTTP request
GET /administrativeUnits/{Admin id}/members/$/microsoft.graph.group
Request body
{}
```

## <a name="list-aus-for-a-group"></a>グループの AU を一覧表示する

### <a name="azure-portal"></a>Azure portal

Azure AD ポータルでは、 **[グループ]** を開いて、グループの詳細を開くことができます。 グループを選択して、そのグループのプロファイルを開きます。 **[管理単位]** を選択すると、そのグループがメンバーとなっているすべての管理単位が一覧表示されます。

![グループの管理単位を一覧表示する](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-au"></a>AU からグループを削除する

### <a name="azure-portal"></a>Azure portal

Azure portal で管理単位からグループを削除する方法は 2 つあります。

**[Azure AD]**  >  **[グループ]** の順に開き、管理単位から削除するグループのプロファイルを開きます。 左側のパネルで **[管理単位]** を選択して、そのグループがメンバーとなっているすべての管理単位を一覧表示します。 グループが削除される管理単位を選択してから、 **[Remove from administrative unit] (管理単位から削除する)** を選択します。

![管理単位からグループを削除する](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

もう 1 つの方法として、 **[Azure AD]**  >  **[管理単位]** の順にアクセスし、グループがメンバーとなっている管理単位を選択できます。 左側のパネルで **[グループ]** を選択して、メンバーのグループを一覧表示します。 管理単位から削除するグループを選択し、 **[グループの削除]** を選択します。

![管理単位のグループを一覧表示する](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref
```

## <a name="next-steps"></a>次のステップ

- [管理単位にロールを割り当てる](roles-admin-units-assign-roles.md)
- [管理単位のユーザーを管理する](roles-admin-units-add-manage-users.md)
