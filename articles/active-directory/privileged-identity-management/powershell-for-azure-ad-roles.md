---
title: PIM の Azure AD ロールのための PowerShell - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で PowerShell コマンドレットを使用して Azure AD ロールを管理します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2774c63d34de3de951aa8076b56d203b976158ec
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548752"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management の Azure AD ロールのための PowerShell

この記事では、Azure Active Directory (Azure AD) PowerShell コマンドレットを使用して Privileged Identity Management (PIM) で Azure AD ロールを管理する手順について説明します。 また、Azure AD PowerShell のモジュールを使用して設定する方法についても説明します。

> [!Note]
> Microsoft の公式 PowerShell は、Azure AD Privileged Identity Management の新しいバージョンを使用している場合にのみサポートされます。 Privileged Identity Management にアクセスして、[クイック スタート] ブレードに次のバナーが表示されていることを確認してください。
> [![お使いの Privileged Identity Management のバージョンを確認する](media/pim-how-to-add-role-to-user/pim-new-version.png "[Azure AD] > [Privileged Identity Management] を選択する")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) このバナーが表示されていない場合は、更新されたエクスペリエンスを現在数週間かけて提供するプロセスが進行中のため、しばらくお待ちください。
> Privileged Identity Management PowerShell コマンドレットは、Azure AD Preview モジュールでサポートされています。 別のモジュールを使用していて、そのモジュールがエラー メッセージを返すようになった場合は、この新しいモジュールの使用を開始してください。 異なるモジュール上に構築された実稼働システムがある場合は、[pim_preview@microsoft.com](mailto:pim_preview@microsoft.com) にご連絡ください。

## <a name="installation-and-setup"></a>インストールとセットアップ

1. Azure AD Preview モジュールをインストールします。

    ```powershell
    Install-module AzureADPreview
    ```

1. 続行する前に、必要なロールのアクセス許可があることを確認してください。 ロールの割り当てやロール設定の更新などの管理タスクを実行しようとしている場合は、全体管理者ロールまたは特権ロール管理者ロールのどちらかを持っていることを確認してください。 自分自身の割り当てをアクティブ化するだけの場合は、既定のユーザー アクセス許可を超えるアクセス許可は必要ありません。

1. Azure AD に接続します。

    ```powershell
    $AzureAdCred = Get-Credential  
    Connect-AzureAD -Credential $AzureAdCred
    ```

1. Azure AD 組織のテナント ID を検索するには、 **[Azure Active Directory]**  >  **[プロパティ]**  >  **[ディレクトリ ID]** に移動します。 [コマンドレット] セクションで resourceId を指定する必要がある場合は、常にこの ID を使用します。

    ![Azure AD 組織のプロパティで組織 ID を検索する](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> 次のセクションでは、使用を開始するのに役立つ簡単な例を紹介しています。 次のコマンドレットに関する詳細なドキュメントについては、[https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#privileged_role_management](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#privileged_role_management) を参照してください。 ただし、providerID パラメーターの "azureResources" を "aadRoles" に置き換える必要があります。 また、Azure AD 組織のテナント ID を resourceId パラメーターとして使用する必要もあります。

## <a name="retrieving-role-definitions"></a>ロール定義の取得

次のコマンドレットを使用して、Azure AD 組織内のすべての組み込みおよびカスタムの Azure AD ロールを取得します。 この重要な手順では、ロール名と roleDefinitionId の間のマッピングが提供されます。 特定のロールを参照するために、これらのコマンドレット全体で roleDefinitionId が使用されます。

この roleDefinitionId は Azure AD 組織に固有のものであり、ロール管理 API によって返される roleDefinitionId とは異なります。

```powershell
Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26
```

結果:

![Azure AD 組織のすべてのロールを取得する](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>ロールの割り当ての取得

Azure AD 組織内のすべてのロールの割り当てを取得するには、次のコマンドレットを使用します。

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"
```

特定のユーザーに対するすべてのロールの割り当てを取得するには、次のコマンドレットを使用します。 この一覧は、Azure AD ポータルでは [自分のロール] とも呼ばれています。 ここでの唯一の違いは、サブジェクト ID にフィルターを追加したことです。 このコンテキストでのサブジェクト ID は、ユーザー ID またはグループ ID です。

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 
```

特定のロールに対するすべてのロールの割り当てを取得するには、次のコマンドレットを使用します。 ここでの roleDefinitionId は、前のコマンドレットによって返される ID です。

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"
```

コマンドレットを実行すると、次に示すロールの割り当てオブジェクトの一覧が表示されます。 サブジェクト ID は、ロールが割り当てられているユーザーのユーザー ID です。 割り当ての状態は、アクティブまたは有資格のどちらかになります。 ユーザーがアクティブで、LinkedEligibleRoleAssignmentId フィールドに ID がある場合は、ロールが現在アクティブ化されていることを意味します。

結果:

![Azure AD 組織のすべてのロールの割り当てを取得する](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>ロールの割り当て

資格のある割り当てを作成するには、次のコマンドレットを使用します。

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 
```

スケジュールは、割り当ての開始時刻と終了時刻を定義するオブジェクトで、次の例のように作成できます。

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
$schedule.endDateTime = "2020-07-25T20:49:11.770Z"
```
> [!Note]
> endDateTime の値が null に設定されている場合、永続的な割り当てを示します。

## <a name="activate-a-role-assignment"></a>ロールの割り当てのアクティブ化

資格のある割り当てをアクティブ化するには、次のコマンドレットを使用します。

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas"
``` 

このコマンドレットは、ロールの割り当てを作成するためのコマンドレットとほぼ同じです。 コマンドレット間の主な違いは、-Type パラメーターがアクティブ化の場合には "adminAdd" ではなく "userAdd" であることです。 もう 1 つの違いは、-AssignmentState パラメーターが "Eligible" ではなく "Active" であることです。

> [!Note]
> PowerShell を使用したロールのアクティブ化には、制限されるシナリオが 2 つあります。
> 1. ロール設定にチケット システムまたはチケット番号が必要な場合は、それらをパラメーターとして指定することはできません。 このため、Azure portal を超えてロールをアクティブ化することはできません。 この機能は、今後数か月の間に PowerShell に提供されます。
> 1. ロールのアクティブ化に多要素認証が必要な場合は、現在、ユーザーがロールをアクティブ化するときに PowerShell によりユーザーをチャレンジする方法はありません。 代わりに、ユーザーが Azure AD に接続するときに MFA チャレンジをトリガーする必要があります。これを行うには、Microsoft のエンジニアによる[ブログ記事](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html)に従ってください。 PIM 用のアプリを開発している場合、考えられる 1 つの実装は、ユーザーをチャレンジして、"MfaRule" エラーが発生した後にユーザーをモジュールに再接続することです。

## <a name="retrieving-and-updating-role-settings"></a>ロール設定の取得と更新

Azure AD 組織内のすべてのロール設定を取得するには、次のコマンドレットを使用します。

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'"
```

この設定には、4 つの主要なオブジェクトがあります。 PIM によって現在使用されているのは、これらのオブジェクトのうち 3 つだけです。 UserMemberSettings はアクティブ化の設定、AdminEligibleSettings は有資格な割り当ての割り当て設定、AdminmemberSettings はアクティブな割り当ての割り当て設定です。

[![ロール設定を取得して更新します。](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png)](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

ロールの設定を更新するには、特定のロールの既存の設定オブジェクトを取得し、それに変更を加える必要があります。

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq 'tenant id' and RoleDefinitionId eq 'role id'"
$settinga = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting
$settinga.RuleIdentifier = "JustificationRule"
$settinga.Setting = '{"required":false}'
```

その後、次に示すように、特定のロールのオブジェクトのいずれかに設定を適用できます。 ここでの ID は、リスト ロール設定コマンドレットの結果から取得できるロール設定 ID です。

```powershell
Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $settinga 
```

## <a name="next-steps"></a>次のステップ

- [Azure AD のカスタム ロールを割り当てる](azure-ad-custom-roles-assign.md)
- [Azure AD のカスタム ロールの割り当てを削除または更新する](azure-ad-custom-roles-update-remove.md)
- [Azure AD のカスタム ロールの割り当てを構成する](azure-ad-custom-roles-configure.md)
- [Azure AD のロールの定義](../roles/permissions-reference.md)
