---
title: Azure Active Directory でグループに割り当てられているロールを表示する | Microsoft Docs
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
ms.openlocfilehash: 3b7613fb70299a70e4389b97c2647a26cb7c3374
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475867"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Azure Active Directory でグループに割り当てられているロールを表示する

この項では、Azure AD 管理センターを使用して、グループに割り当てられているロールを表示する方法について説明します。 グループと割り当てられたロールの表示は、既定のユーザー アクセス許可で行うことができます。

1. 管理者以外の資格情報または管理者の資格情報を使用して、[Azure AD 管理センター](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)にサインインします。

1. 目的のグループを選択します。

1. **[割り当てられたロール]** を選択します。 このグループに割り当てられているすべての Azure AD ロールが表示されます。

   ![選択したグループに割り当てられているすべてのロールの表示](./media/roles-groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>PowerShell の使用

### <a name="get-object-id-of-the-group"></a>グループのオブジェクト ID を取得する

```powershell
Get-AzureADMSGroup -SearchString “Contoso_Helpdesk_Administrators”
```

### <a name="view-role-assignment-to-a-group"></a>グループへのロールの割り当てを表示する

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph API の使用

### <a name="get-object-id-of-the-group"></a>グループのオブジェクト ID を取得する

```powershell
GET https://graph.microsoft.com/beta/groups?$filter displayName eq ‘Contoso_Helpdesk_Administrator’ 
```

### <a name="get-role-assignments-to-a-group"></a>グループへのロールの割り当てを取得する

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>次のステップ

- [クラウド グループを使用してロールの割り当てを管理する](roles-groups-concept.md)
- [クラウド グループに割り当てられているロールのトラブルシューティング](roles-groups-faq-troubleshooting.md)
