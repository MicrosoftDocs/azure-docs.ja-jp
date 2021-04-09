---
title: 準拠していないリソースを修復する
description: このガイドでは、Azure Policy のポリシーに準拠していないリソースを修復する手順を説明します。
ms.date: 02/17/2021
ms.topic: how-to
ms.openlocfilehash: e567bedf48393a36215c1ac3f3d11f467ae7badd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742230"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Azure Policy を使って準拠していないリソースを修復する

**deployIfNotExists** や **modify** に準拠していないリソースは､**修復** を使って準拠状態にすることができます。 修復は､既存のリソースおよびサブスクリプションに割り当てられているポリシーの **deployIfNotExists** 効果や **変更操作** を実行するように Azure Policy に指示することによって実行されます。その割り当てが、管理グループ、サブスクリプション、リソース グループ、または個々のリソースのいずれに対するものかは関係ありません。 この記事では、Azure Policy による修復を理解して実行するために必要な手順を示します。

## <a name="how-remediation-security-works"></a>修復のセキュリティの仕組み

**deployIfNotExists** ポリシー定義にあるテンプレートを実行するとき､Azure Policy では [マネージド ID](../../../active-directory/managed-identities-azure-resources/overview.md) が使用されます｡
マネージド ID は Azure Policy によって各割り当てに対して作成されますが、どのようなロールをマネージド ID に付与するかについての詳細が必要です。 マネージド ID にロールが存在しない場合、そのポリシーまたはイニシアチブの割り当て中にエラーが表示されます。 ポータルを使用している場合、割り当てが開始されると、Azure Policy によって、示されているロールが自動的にマネージド ID に付与されます。 SDK を使用している場合、マネージド ID にロールを手動で付与する必要があります。 マネージド ID の "_場所_" は、Azure Policy による操作に影響を与えません。

:::image type="content" source="../media/remediate-resources/missing-role.png" alt-text="マネージド ID に対して定義されたアクセス許可がない deployIfNotExists ポリシーのスクリーンショット。" border="false":::

> [!IMPORTANT]
> 次のシナリオでは、割り当てのマネージド ID に[手動でアクセスを許可](#manually-configure-the-managed-identity)しないと、修復デプロイが失敗します。
>
> - 割り当てが SDK によって作成された場合
> - **deployIfNotExists** または **modify** によって変更されたリソースがポリシー割り当ての範囲外である場合
> - テンプレートがポリシー割り当ての範囲外のリソースのプロパティにアクセスする場合

## <a name="configure-policy-definition"></a>ポリシー定義を設定する

最初の手順は、含まれているテンプレートのコンテンツを **deployIfNotExists** と **modify** が正しくデプロイするために必要なロールを、ポリシー定義に定義することです。 **details (詳細)** プロパティで **roleDefinitionIds** プロパティを追加します。 このプロパティは、環境にあるロールに合致する一連の文字列です。 [deployIfNotExists の例](../concepts/effects.md#deployifnotexists-example)または [modify の例](../concepts/effects.md#modify-examples)に示す例を参照してください。

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

**roleDefinitionIds** プロパティは完全なリソース識別子を使用し、ロールの短い **roleName** は使用しません。 環境にある「共同作成者」ロールの ID を取得するには、次のコードを使用します。

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>管理対象 ID を手動で設定します。

ポータルを使用して割り当てを作成する際、Azure Policy によって、マネージド ID の生成と **roleDefinitionIds** に定義されているロールのマネージド ID への付与の両方が実行されます。 次の条件では、管理対象 ID を作成し、アクセス許可を割り当てる手順を手動で行う必要があります:

- SDK (Azure PowerShell など) を使用している場合
- 割り当てスコープの範囲外のリソースがテンプレートによって変更される場合
- 割り当てスコープの範囲外のリソースがテンプレートによって読み取られる場合

### <a name="create-managed-identity-with-powershell"></a>PowerShell で管理対象 ID を作成する

ポリシーの割り当て時に管理対象 ID を作成するには､**場所** を定義して､**AssignIdentity** を使用する必要があります｡ 次の例では、組み込みポリシー **Deploy SQL DB transparent data encryption** の定義を取得して､ターゲット リソース グループを設定し、割り当てを作成しています。

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

この結果､`$assignment` 変数には管理対象 ID のプリンシパル ID とともに､ポリシー割り当てを作成するときに返される標準値が含まれます｡ この変数には `$assignment.Identity.PrincipalId` を使ってアクセスできます｡

### <a name="grant-defined-roles-with-powershell"></a>定義したロールを PowerShell を使って付与する

新しい管理対象 ID に必要なロールを付与するには､Azure Active Directory からレプリケーションを完了しておく必要があります。 次の例では、レプリケーションを完了した後､**roleDefinitionIds** に対して `$policyDef` でポリシー定義を反復処理し､[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を使用して､新しい管理対象 ID にロールを付与します｡

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>定義したロールをポータルから付与する

ポータルを使用して、定義したロールを割り当てのマネージド ID に付与するには、**アクセス制御 (IAM)** を使用する方法と、ポリシーまたはイニシアチブの割り当てを編集して **[保存]** を選択する方法の 2 つがあります。

割り当ての管理対象 ID にロールを追加するには、次の手順に従います｡

1. Azure portal で **[すべてのサービス]** を選択し、「**Policy**」を検索して選択することで、Azure Policy サービスを起動します。

1. Azure Policy ページの左側にある **[割り当て]** を選択します。

1. マネージド ID がある割り当てを見つけて、その名前を選択します。

1. 編集ページで **割り当て ID** を見つけます｡ 割り当て ID は以下のような ID です。

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   管理対象 ID の名前は、割り当てリソース ID の最後の部分です｡この例では `2802056bfc094dfb95d4d7a5` です。 割り当てリソース ID のこの部分をコピーします。

1. ロールの定義で手動で追加する必要があるリソース､またはリソースの親コンテナー (リソース グループ、サブスクリプション、管理グループ) に移動します。

1. リソース ページにある **アクセス制御 (IAM)** のリンクを選択して、アクセス制御ページの上部にある **[+ Add role assignment]\(+ ロール割り当ての追加\)** を選択します。

1. ポリシー定義から､ **roleDefinitionIds** に一致するロールを選択します｡
   **Assign access to** は ''Azure AD user, group, or application という既定値の設定のままにします｡ **[選択]** ボックスで、前の手順で見つけた割り当てリソース ID の部分を貼り付けるか､入力します。 検索が完了したら、同じ名前のオブジェクトを選択することで ID を選択し､ **[保存]** を選択します。

## <a name="create-a-remediation-task"></a>修復タスクを作成する

### <a name="create-a-remediation-task-through-portal"></a>ポータルを通じて修復タスクを作成する

評価では､**deployIfNotExists** または **modify** 効果を持つポリシー割り当てによって､準拠していないリソースまたはサブスクリプションがあるかどうかが判定されます。 準拠していないリソースまたはサブスクリプションが見つかった場合は、**修復** ページにその詳細が表示されます。 準拠していないリソースまたはサブスクリプションがあるポリシーの一覧には、**修復タスク** をトリガーするオプションがあります｡
このオプションは､**deployIfNotExists** テンプレートまたは **modify** 操作からデプロイを作成するものです。

**修復タスク** を作成するには､次の手順に従います。

1. Azure portal で **[すべてのサービス]** を選択し、「**Policy**」を検索して選択することで、Azure Policy サービスを起動します。

   :::image type="content" source="../media/remediate-resources/search-policy.png" alt-text="[すべてのサービス] で「Policy」 を検索している様子を示すスクリーンショット。" border="false":::

1. Azure Policy ページの左側にある **[修復]** を選択します。

   :::image type="content" source="../media/remediate-resources/select-remediation.png" alt-text="[Policy] ページの修復ノードのスクリーンショット。" border="false":::

1. **deployIfNotExists** および **modify** のポリシー割り当てのうち、準拠していないリソースがあるものはすべて､ **[修復するポリシー]** タブとデータ テーブルに含まれます。 準拠していないリソースがあるポリシーを選択します。 **新しい修復タスク** ページが開きます。

   > [!NOTE]
   > **[修復タスク]** ページは、 **[コンプライアンス]** ページからポリシーを見つけて選択し、 **[修復タスクの作成]** ボタンを選択して開くこともできます。

1. **[New remediation task]\(新しい修復タスク\)** ページで **[Scope]\(スコープ\)** 省略記号ボタンを使って、ポリシーが割り当てられている子リソースを選択することで、修復するリソースをフィルター処理します (個々のリソース オブジェクトまでフィルター可能)。 また、 **[場所]** ドロップダウンを使って､リソースをさらにフィルター処理することもできます｡ 表に示されたリソースのみ修復されます。

   :::image type="content" source="../media/remediate-resources/select-resources.png" alt-text="修復ノードと、修復するリソースのグリッドのスクリーンショット。" border="false":::

1. リソースのフィルター処理を終えたら、 **[修復]** を選択して、修復タスクを開始します。 **[修復タスク]** タブに対するポリシー コンプライアンス ページが開いて、タスクの進行状況が表示されます。 修復タスクによって作成されたデプロイが、すぐに開始されます。

   :::image type="content" source="../media/remediate-resources/task-progress.png" alt-text="[修復タスク] タブと、既存の修復タスクの進行状況を示すスクリーンショット。" border="false":::

1. ポリシー コンプライアンス ページの **[修復タスク]** を選択すると､進行状況の詳細が表示されます｡ 修復されているリソースの一覧と共に、タスクに使用されたフィルターが表示されます。

1. **[修復タスク]** ページで、リソースを右クリックして、修復タスクのデプロイまたはリソースのいずれかを表示します。 行の末尾にある **[関連イベント]** を選択すると､エラー メッセージなどの詳細が表示されます｡

   :::image type="content" source="../media/remediate-resources/resource-task-context-menu.png" alt-text="[修復タスク] タブのリソースのコンテキスト メニューを示すスクリーンショット。" border="false":::

**[修復タスク]** を使ってデプロイされたリソースは、ポリシー コンプライアンス ページの **[デプロイされたリソース]** タブに追加されます。

### <a name="create-a-remediation-task-through-azure-cli"></a>Azure CLI を通じて修復タスクを作成する

Azure CLI を使用して **修復タスク** を作成するには、`az policy remediation` コマンドを使用します。 `{subscriptionId}` をサブスクリプション ID に置き換え、`{myAssignmentId}` を **deployIfNotExists** または **modify** のポリシー割り当て ID に置き換えます。

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

その他の修復コマンドや例については、[az ポリシーの修復](/cli/azure/policy/remediation) コマンドを参照してください。

### <a name="create-a-remediation-task-through-azure-powershell"></a>Azure PowerShell を通じて修復タスクを作成する

Azure PowerShell を使用して **修復タスク** を作成するには、`Start-AzPolicyRemediation` コマンドを使用します。 `{subscriptionId}` をサブスクリプション ID に置き換え、`{myAssignmentId}` を **deployIfNotExists** または **modify** のポリシー割り当て ID に置き換えます。

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

その他の修復コマンドレットや例については、[Az.PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) モジュールを参照してください。

### <a name="create-a-remediation-task-during-policy-assignment-in-the-azure-portal"></a>Azure portal でポリシー割り当て時に修復タスクを作成する

修復タスクを簡単に作成する方法は、ポリシーの割り当て時に Azure portal から行うことです。 割り当てるポリシー定義が **deployIfNotExists** または **Modify** 効果である場合、 **[修復]** タブのウィザードによって _[修復タスクを作成する]_ オプションが提供されます。 このオプションを選択すると、ポリシーの割り当てと同時に修復タスクが作成されます。

## <a name="next-steps"></a>次のステップ

- [Azure Policy のサンプル](../samples/index.md)を確認します。
- 「[Azure Policy の定義の構造](../concepts/definition-structure.md)」を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。
- [プログラムによってポリシーを作成する](programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](get-compliance-data.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。
