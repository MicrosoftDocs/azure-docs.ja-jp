---
title: 特定のラボ ポリシーに対するアクセス許可をユーザーに付与する | Microsoft Docs
description: 各ユーザーのニーズに基づいて DevTest ラボの特定のラボ ポリシーに対するアクセス許可をユーザーに付与する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 976862476d25e4e9a4933d8a5319eec9d77ca39b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92328472"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>特定のラボ ポリシーに対するアクセス許可をユーザーに付与する
## <a name="overview"></a>概要
この記事では、PowerShell を使用して、特定のラボ ポリシーに対するアクセス許可をユーザーに付与する方法を説明します。 そうすることで、アクセス許可を各ユーザーのニーズに基づいて適用できます。 たとえば、特定のユーザーに、VM ポリシー設定は変更できるがコスト ポリシーは変更できない能力を付与することができます。

## <a name="policies-as-resources"></a>リソースとしてのポリシー
[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) の記事で説明されているように、Azure RBAC を使用すると、Azure のリソースのアクセスをきめ細かく管理できます。 Azure RBAC を使用して、DevOps チーム内で職務を分け、職務に必要なアクセス権のみをユーザーに付与します。

DevTest ラボでは、ポリシーは、リソースの種類の 1 つであり、Azure RBAC の操作 **Microsoft.DevTestLab/labs/policySets/policies/** を可能にするものです。 各ラボ ポリシーはこの種類のポリシー リソースのリソースであり、Azure ロールにスコープとして割り当てることができます。

たとえば、**許可される VM サイズ** ポリシーに対する読み取り/書き込みアクセス許可をユーザーに付与するには、**Microsoft.DevTestLab/labs/policySets/policies/** アクションを扱うカスタム ロールを作成し、**Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab** スコープ内でこのカスタム ロールに適切なユーザーを割り当てます。

Azure RBAC でのカスタム ロールの詳細については、「[Azure カスタム ロール](../role-based-access-control/custom-roles.md)」を参照してください。

## <a name="creating-a-lab-custom-role-using-powershell"></a>PowerShell を使用してラボ カスタム ロールを作成する
始めるには、[Azure PowerShell をインストールする](/powershell/azure/install-az-ps)必要があります。 

Azure PowerShell コマンドレットを設定すると、次のタスクを実行できるようになります。

* リソース プロバイダーのすべての操作の一覧
* 特定のロールの操作の一覧:
* カスタム ロールの作成

次の PowerShell スクリプトは、これらのタスクを実行する方法の例を示しています。

```azurepowershell
# List all the operations/actions for a resource provider.
Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

# List actions in a particular role.
(Get-AzRoleDefinition "DevTest Labs User").Actions

# Create custom role.
$policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
$policyRoleDef.Id = $null
$policyRoleDef.Name = "Policy Contributor"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
$policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)
```

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>カスタム ロールを使用して特定のポリシーに対しユーザーにアクセス許可を割り当てる
カスタム ロールを定義すると、ユーザーにカスタム ロールを割り当てられるようになります。 カスタム ロールをユーザーに割り当てるには、まず、そのユーザーを表す **ObjectId** を取得する必要があります。 そのためには、**Get-AzADUser** コマンドレットを使用します。

次の例では、 **SomeUser** ユーザーの *ObjectId* は 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 です。

```azurepowershell
PS C:\>Get-AzADUser -SearchString "SomeUser"

DisplayName                    Type                           ObjectId
-----------                    ----                           --------
someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3
```

ユーザーの **ObjectId** とカスタム ロール名を取得したら、**New-AzRoleAssignment** コマンドレットを使用してユーザーにそのロールを割り当てることができます。

```azurepowershell
PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab
```

前の例では、 **AllowedVmSizesInLab** ポリシーを使用しました。 次のようなポリシーを使用することもできます。

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>次のステップ
特定のラボ ポリシーに対するアクセス許可をユーザーに付与した後で実行する手順として、以下のようなものがあります。

* [ラボへのアクセスをセキュリティで保護する](devtest-lab-add-devtest-user.md)
* [ラボのポリシーを設定する](devtest-lab-set-lab-policy.md)
* [ラボ テンプレートを作成する](devtest-lab-create-template.md)
* [VM のカスタム アーティファクトを作成する](devtest-lab-artifact-author.md)
* [VM をラボに追加する](devtest-lab-add-vm.md)
