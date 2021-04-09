---
title: Azure Active Directory でグループに割り当てられているロールを表示する | Microsoft Docs
description: Azure AD 管理センターを使用して、グループに割り当てられているロールを表示する方法について説明します。 グループと割り当てられたロールの表示は、既定のユーザー アクセス許可で行うことができます。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7dac23897f8a17c9adb4ae78736a6a8afa85a18b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012005"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Azure Active Directory でグループに割り当てられているロールを表示する

この項では、Azure AD 管理センターを使用して、グループに割り当てられているロールを表示する方法について説明します。 グループと割り当てられたロールの表示は、既定のユーザー アクセス許可で行うことができます。

1. 管理者以外の資格情報または管理者の資格情報を使用して、[Azure AD 管理センター](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)にサインインします。

1. 目的のグループを選択します。

1. **[割り当てられたロール]** を選択します。 このグループに割り当てられているすべての Azure AD ロールが表示されます。

   ![選択したグループに割り当てられているすべてのロールの表示](./media/groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>PowerShell の使用

### <a name="get-object-id-of-the-group"></a>グループのオブジェクト ID を取得する

```powershell
Get-AzureADMSGroup -SearchString "Contoso_Helpdesk_Administrators"
```

### <a name="view-role-assignment-to-a-group"></a>グループへのロールの割り当てを表示する

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph API の使用

### <a name="get-object-id-of-the-group"></a>グループのオブジェクト ID を取得する

```http
GET https://graph.microsoft.com/beta/groups?$filter=displayName+eq+'Contoso_Helpdesk_Administrator'
```

### <a name="get-role-assignments-to-a-group"></a>グループへのロールの割り当てを取得する

```http
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>次のステップ

- [クラウド グループを使用してロールの割り当てを管理する](groups-concept.md)
- [クラウド グループに割り当てられているロールのトラブルシューティング](groups-faq-troubleshooting.md)
