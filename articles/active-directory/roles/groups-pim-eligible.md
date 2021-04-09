---
title: Azure AD の Privileged Identity Management を使用してグループにロールを割り当てる | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) を使用して、グループに Azure Active Directory (Azure AD) ロールを割り当てる方法について説明します。
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
ms.openlocfilehash: 02cd3f54823b80ae201316fee29c02616b9d8502
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012039"
---
# <a name="assign-a-role-to-a-group-using-privileged-identity-management"></a>Privileged Identity Management を使用してグループにロールを割り当てる

この記事では、Azure AD Privileged Identity Management (PIM) を使用して、グループに Azure Active Directory (Azure AD) ロールを割り当てる方法について説明します。

> [!NOTE]
> Privileged Identity Management を使用して Azure AD ロールにグループを割り当てることができるようにするには、更新されたバージョンの PIM を使用する必要があります。 Azure AD 組織で Privileged Identity Management API を活用している場合、PIM は以前のバージョンである可能性があります。 その場合、エイリアス pim_preview@microsoft.com に連絡して、組織を移動し、API を更新してください。 詳細については、[PIM の Azure AD ロールと機能](../privileged-identity-management/azure-ad-roles-features.md)に関する記事を参照してください。

## <a name="using-azure-ad-admin-center"></a>Azure AD 管理センターの使用

1. 組織の特権ロール管理者またはグローバル管理者として、[Azure AD Privileged Identity Management](https://ms.portal.azure.com/?Microsoft_AAD_IAM_GroupRoles=true&Microsoft_AAD_IAM_userRolesV2=true&Microsoft_AAD_IAM_enablePimIntegration=true#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) にサインインします。

1. **[Privileged Identity Management]**  >  **[Azure AD ロール]**  >  **[ロール]**  >  **[割り当ての追加]** の順に選択します。

1. ロールを選択し、グループを選択します。 すべてのグループではなく、ロールの割り当て (ロールの割り当てが可能なグループ) の対象となるグループのみが表示されます。

    ![[ロールの選択] および [メンバーの選択] セクションが強調表示されている [割り当ての追加] ページを示すスクリーンショット。](./media/groups-pim-eligible/select-member.png)

1. 目的のメンバーシップ設定を選択します。 アクティブ化が必要なロールについては、 **[対象]** を選択します。 既定では、ユーザーは永続的に有資格となりますが、ユーザーの資格の開始時刻と終了時刻を設定することもできます。 完了したら、[保存] をクリックし、[追加] をクリックして、ロールの割り当てを完了します。

    ![ロールの割り当て先となるユーザーを選択する](./media/groups-pim-eligible/set-assignment-settings.png)

## <a name="using-powershell"></a>PowerShell の使用

### <a name="download-the-azure-ad-preview-powershell-module"></a>Azure AD プレビュー PowerShell モジュールをダウンロードする

Azure AD #PowerShell モジュールをインストールするには、次のコマンドレットを使用します。

```powershell
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
```

モジュールを使用する準備ができているかどうかを確認するには、次のコマンドレットを使用します。

```powershell
Get-Module -Name AzureADPreview
```

### <a name="assign-a-group-as-an-eligible-member-of-a-role"></a>ロールの有資格メンバーとしてグループを割り当てる

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = "2019-04-26T20:49:11.770Z"
$schedule.endDateTime = "2019-07-25T20:49:11.770Z"
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId aadRoles -Schedule $schedule -ResourceId "[YOUR TENANT ID]" -RoleDefinitionId "9f8c1837-f885-4dfd-9a75-990f9222b21d" -SubjectId "[YOUR GROUP ID]" -AssignmentState "Eligible" -Type "AdminAdd"
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph API の使用

```http
POST
https://graph.microsoft.com/beta/privilegedAccess/aadroles/roleAssignmentRequests
{
 "roleDefinitionId": {roleDefinitionId},
 "resourceId": {tenantId},
 "subjectId": {GroupId},
 "assignmentState": "Eligible",
 "type": "AdminAdd",
 "reason": "reason string",
 "schedule": {
   "startDateTime": {DateTime},
   "endDateTime": {DateTime},
   "type": "Once"
 }
}
```

## <a name="next-steps"></a>次のステップ

- [クラウド グループを使用してロールの割り当てを管理する](groups-concept.md)
- [クラウド グループに割り当てられているロールのトラブルシューティング](groups-faq-troubleshooting.md)
- [Privileged Identity Management で Azure AD 管理者ロールの設定を構成する](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [Privileged Identity Management で Azure リソース ロールを割り当てる](../privileged-identity-management/pim-resource-roles-assign-roles.md)
