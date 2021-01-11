---
title: Azure Active Directory でグループからロールの割り当てを削除する | Microsoft Docs
description: ID 管理を委任するためのカスタム Azure AD ロールをプレビューします。 Azure portal、PowerShell、または Graph API で Azure ロールを管理します。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15312159bc0487f7d03c06fa947f69b1f6f9600c
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513402"
---
# <a name="remove-role-assignments-from-a-group-in-azure-active-directory"></a>Azure Active Directory でグループからロールの割り当てを削除する

この記事では、グループに割り当てられている Azure AD ロールを IT 管理者が削除する方法について説明します。 Azure portal で、ユーザーに対する直接的と間接的なロールの割り当ての両方を削除できるようになりました。 ユーザーにグループ メンバーシップによってロールが割り当てられている場合は、そのユーザーをグループから削除してロールの割り当てを削除します。

## <a name="using-azure-admin-center"></a>Azure 管理センターの使用

1. Azure AD 組織の特権ロール管理者またはグローバル管理者のアクセス許可を使用して [Azure AD 管理センター](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)にサインインします。

1. **[ロールと管理者]**  > ***<ロール名>*** の順に選択します。

1. ロールの割り当てを削除するグループを選択し、 **[割り当ての削除]** を選択します。

   ![選択したグループからロールの割り当てを削除します。](./media/roles-groups-remove-assignment/remove-assignment.png)

1. アクションを確認するよう求められたら、 **[はい]** を選択します。

## <a name="using-powershell"></a>PowerShell の使用

### <a name="create-a-group-that-can-be-assigned-to-role"></a>ロールに割り当てることができるグループを作成する

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

### <a name="get-the-role-definition-you-want-to-assign-the-group-to"></a>グループを割り当てるロールの定義を取得する

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'"
```

### <a name="create-a-role-assignment"></a>役割の割り当ての作成

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.objectId
```

### <a name="remove-the-role-assignment"></a>ロールの割り当てを削除する

```powershell
Remove-AzureAdMSRoleAssignment -Id $roleAssignment.Id 
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph API の使用

### <a name="create-a-group-that-can-be-assigned-an-azure-ad-role"></a>Azure AD ロールを割り当てることができるグループを作成する

```powershell
POST https://graph.microsoft.com/beta/groups

{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>ロールの定義を取得する

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>ロールの割り当てを作成する

```powershell
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<Id of role definition>",
"directoryScopeId":"/"
}
```

### <a name="delete-role-assignment"></a>ロール割り当ての削除

```powershell
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/<Id of role assignment>
```

## <a name="next-steps"></a>次のステップ

- [クラウド グループを使用してロールの割り当てを管理する](roles-groups-concept.md)
- [クラウド グループに割り当てられているロールのトラブルシューティング](roles-groups-faq-troubleshooting.md)
