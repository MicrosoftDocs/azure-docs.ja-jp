---
title: 管理単位のグループを追加、削除、一覧表示する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory で管理単位のグループとそのロールのアクセス許可を管理します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 03/10/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: f88a0818d93c33b6265cc8c695479d2a42678ada
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103011036"
---
# <a name="add-and-manage-groups-in-an-administrative-unit-in-azure-active-directory"></a>Azure Active Directory で管理単位のグループを追加して管理する

Azure Active Directory (Azure AD) では、きめ細かい管理スコープで制御するために、グループを管理単位に追加できます。

管理単位の管理に PowerShell や Microsoft Graph を使用するよう準備するには、「[はじめに](admin-units-manage.md#get-started)」を参照してください。

## <a name="add-groups-to-an-administrative-unit"></a>管理単位にグループを追加する

管理単位にグループを追加するには、Azure portal、PowerShell、または Microsoft Graph を使用します。

### <a name="use-the-azure-portal"></a>Azure ポータルの使用

管理単位には、グループを個別で割り当てることしかできません。 グループを一括操作として割り当てるオプションはありません。 Azure portal では、次の 2 つの方法のいずれかを使用して、管理単位にグループを割り当てることができます。

* **[グループ]** ウィンドウで、次を行います。

  1. Azure portal で、**Azure AD** に移動します。
  1. **[グループ]** を選択し、管理単位に割り当てるグループを選択します。 
  1. 左側のウィンドウで、 **[管理単位]** を選択して、グループが割り当てられている管理単位を一覧表示します。 

     ![[管理単位] ウィンドウの [Assign to administrative unit]\(管理単位に割り当てる\) リンクのスクリーンショット。](./media/admin-units-add-manage-groups/assign-to-group-1.png)

  1. **[Assign to administrative unit]\(管理単位に割り当てる\)** を選択します。
  1. 右側のウィンドウで、管理単位を選択します。

* **[管理単位]**  >  **[すべてのグループ]** ウィンドウで次を行います。

  1. Azure portal で、**Azure AD** に移動します。
  
  1. 左側のウィンドウで、 **[管理単位]** を選択してから **[すべてのグループ]** を選択します。 
     管理単位に既に割り当てられているグループは、右側のウィンドウに表示されます。 

  1. **[グループ]** ウィンドウで、 **[追加]** を選択します。
    右側のウィンドウには、Azure AD 組織内の使用可能なすべてのグループが一覧表示されます。 

     ![管理単位にグループを追加するための [追加] ボタンのスクリーンショット。](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

  1. 管理単位に割り当てる 1 つまたは複数のグループを選択し、 **[選択]** ボタンを選択します。

### <a name="use-powershell"></a>PowerShell の使用

次の例では、`Add-AzureADMSAdministrativeUnitMember` コマンドレットを使用して、グループを管理単位に追加します。 管理単位のオブジェクト ID と追加するグループのオブジェクト ID を引数として指定できます。 実際の環境に合わせて、必要に応じて強調表示されているセクションを変更します。


```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitObj.ObjectId -RefObjectId $GroupObj.ObjectId
```

### <a name="use-microsoft-graph"></a>Microsoft Graph の使用

次のコマンドを実行します。

要求

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

Body

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/{group-id}"
}
```

例

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="view-a-list-of-groups-in-an-administrative-unit"></a>管理単位内のグループの一覧を表示する

### <a name="use-the-azure-portal"></a>Azure ポータルの使用

1. Azure portal で、**Azure AD** に移動します。

1. 左側のウィンドウで **[管理単位]** を選択し、グループを表示する管理単位を選択します。 既定では、左側のウィンドウで **[すべてのユーザー]** が選択されています。 

1. 左側のウィンドウで、 **[グループ]** を選択します。 右側のウィンドウには、選択した管理単位のメンバーであるグループの一覧が表示されます。

   ![[グループ] ウィンドウのスクリーンショット。管理単位内のグループの一覧が示されています。](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>PowerShell の使用

管理単位のすべてのメンバーの一覧を表示するには、次のコマンドを実行します。 

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitObj.ObjectId
```

管理単位のメンバーであるすべてのグループを表示するには、次のコード スニペットを使用します。

```powershell
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitObj.ObjectId)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="use-microsoft-graph"></a>Microsoft Graph の使用

次のコマンドを実行します。

要求

```http
GET /directory/administrativeUnits/{admin-unit-id}/members/$/microsoft.graph.group
```

Body

```http
{}
```

## <a name="view-a-list-of-administrative-units-for-a-group"></a>グループの管理単位の一覧を表示する

### <a name="use-the-azure-portal"></a>Azure ポータルの使用

1. Azure portal で、**Azure AD** に移動します。

1. 左側のウィンドウで、 **[グループ]** を選択して、グループの一覧を表示します。

1. グループを選択して、そのグループのプロファイルを開きます。 

1. 左側のウィンドウで **[管理単位]** を選択して、そのグループがメンバーとなっているすべての管理単位を一覧表示します。

   ![[管理単位] ウィンドウのスクリーンショット。グループが割り当てられている管理単位の一覧が表示されています。](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="use-powershell"></a>PowerShell の使用

次のコマンドを実行します。

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="use-microsoft-graph"></a>Microsoft Graph の使用

次のコマンドを実行します。

```http
https://graph.microsoft.com/v1.0/groups/{group-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-administrative-unit"></a>管理単位からグループを削除する

### <a name="use-the-azure-portal"></a>Azure ポータルの使用

次の 2 つの方法のいずれかを使用して、Azure portal で管理単位からグループを削除できます。

- グループ概要から削除する:

  1. Azure portal で、**Azure AD** に移動します。
  1. 左側のウィンドウで **[グループ]** を選択し、管理単位から削除するグループのプロファイルを開きます。
  1. 左側のウィンドウで **[管理単位]** を選択して、そのグループが割り当てられているすべての管理単位を一覧表示します。 
  1. グループが削除される管理単位を選択してから、 **[Remove from administrative unit] (管理単位から削除する)** を選択します。

     ![[管理単位] ウィンドウのスクリーンショット。選択した管理単位に割り当てられているグループの一覧が表示されています。](./media/admin-units-add-manage-groups/group-au-remove.png)

- 管理単位から削除する:

  1. Azure portal で、**Azure AD** に移動します。
  1. 左側のウィンドウで **[管理単位]** を選択し、グループが割り当てられている管理単位を選択します。
  1. 左側のウィンドウで **[グループ]** を選択して、管理単位に割り当てられているすべてのグループを一覧表示します。
  1. 削除するグループを選択し、 **[グループを削除する]** を選択します。

    ![[グループ] ウィンドウのスクリーンショット。管理単位内のグループの一覧が示されています。](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>PowerShell の使用

次のコマンドを実行します。

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitId -MemberId $memberGroupObjId
```

### <a name="use-microsoft-graph"></a>Microsoft Graph の使用

次のコマンドを実行します。

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{group-id}/$ref
```

## <a name="next-steps"></a>次のステップ

- [管理単位にロールを割り当てる](admin-units-assign-roles.md)
- [管理単位のユーザーを管理する](admin-units-add-manage-users.md)
