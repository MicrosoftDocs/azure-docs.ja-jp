---
title: 静的なグループ メンバーシップを動的に変更する - Azure AD | Microsoft Docs
description: Azure AD 管理センターまたは PowerShell コマンドレットを使用して、既存のグループを静的から動的なメンバーシップに変換する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a46cc3f4a0f2fb25fc693103a64a319dcec0324
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860883"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Azure Active Directory で静的なグループメンバーシップを動的に変更する

Azure Active Directory (Azure AD) で、グループのメンバーシップを静的から動的 (またはその逆) に変更できます。 Azure AD は、システム内で同じグループ名と ID を維持するため、グループに対するすべての既存の参照は、引き続き有効です。 新しいグループを作成する場合は、これらの参照を更新する必要があります。 動的グループ メンバーシップは、ユーザーの追加と削除という管理オーバーヘッドを排除します。 この記事では、Azure AD 管理センターまたは PowerShell コマンドレットを使用して、既存のグループを静的から動的なメンバーシップに変換する方法について説明します。

> [!WARNING]
> 既存の静的グループを動的グループに変更すると、すべての既存のメンバーがグループから削除され、新しいメンバーを追加するためにメンバーシップ ルールが処理されます。 グループを使用してアプリまたはリソースへのアクセスを制御している場合、メンバーシップ ルールが完全に処理されるまで、元のメンバーはアクセス権を失う場合があることに注意してください。
>
> グループの新しいメンバーシップが予期したとおりのものになるように、事前に新しいメンバーシップ ルールをテストすることをお勧めします。

## <a name="change-the-membership-type-for-a-group"></a>グループのメンバーシップの種類を変更する

1. Azure AD 組織のグローバル管理者、ユーザー管理者、またはグループ管理者であるアカウントで [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
2. **[グループ]** を選びます。
3. **[すべてのグループ]** の一覧から、変更するグループを開きます。
4. **[プロパティ]** を選択します。
5. グループの **[プロパティ]** ページで、目的のメンバーシップの種類に応じて、[割り当て済み] (静的)、[動的ユーザー]、または [動的なデバイス] の **[メンバーシップの種類]** を選びますす。 動的メンバーシップの場合は、ルール ビルダーを使って簡単なルールのオプションを選んだり、メンバーシップ ルールを自分で作成したりすることができます。 

次の手順は、ユーザーのグループを静的メンバーシップから動的メンバーシップに変更する例です。

1. 選択したグループの **[プロパティ]** ページで、 **[動的ユーザー]** の **[メンバーシップの種類]** を選び、グループ メンバーシップの変更について説明するダイアログ ボックスで [はい] を選んで続行します。 
  
   ![動的ユーザーのメンバーシップの種類を選ぶ](./media/groups-change-type/select-group-to-convert.png)
  
2. **[動的クエリの追加]** を選び、ルールを指定します。
  
   ![動的なグループのルールを入力する](./media/groups-change-type/enter-rule.png)
  
3. ルールを作成した後、ページの下部にある **[クエリの追加]** を選びます。
4. グループの **[プロパティ]** ページで **[保存]** を選んで、変更を保存します。 グループの一覧でグループの **[メンバーシップの種類]** がすぐに更新されます。

> [!TIP]
> 入力したメンバーシップ ルールが正しくない場合、グループの変換が失敗する可能性があります。 ポータルの右上隅に通知が表示されます。ルールがシステムで受け付けられない理由の説明が含まれます。 それをよく読み、有効にするためにできる調整の方法を理解してください。 ルールの構文の例、メンバーシップ ルールでサポートされているプロパティ、演算子、および値の完全な一覧については、[Azure Active Directory でのグループに対する動的メンバーシップ ルール](groups-dynamic-membership.md)に関する記事を参照してください。

## <a name="change-membership-type-for-a-group-powershell"></a>グループのメンバーシップの種類を変更する (PowerShell)

> [!NOTE]
> 動的なグループ プロパティを変更するには、[Azure AD PowerShell バージョン 2](/powershell/azure/active-directory/install-adv2) の **プレビュー バージョン** のコマンドレットを使用する必要があります。 [PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureADPreview)からプレビューをインストールできます。

既存のグループにおいてメンバーシップの管理を切り替える関数を次に示します。 この例では、GroupTypes プロパティを正しく操作し、動的なメンバーシップに関係のない値を保持するようにしてください。

```powershell
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
グループを静的にするには:

```powershell
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

グループを動的にするには:

```powershell
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>次のステップ

次の記事は、Azure Active Directory のグループに関する追加情報を提供します。

* [既存のグループの表示](../fundamentals/active-directory-groups-view-azure-portal.md)
* [新しいグループの作成とメンバーの追加](../fundamentals/active-directory-groups-create-azure-portal.md)
* [グループの設定の管理](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [グループのメンバーシップの管理](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [グループ内のユーザーの動的ルールの管理](groups-dynamic-membership.md)
