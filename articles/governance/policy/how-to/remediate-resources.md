---
title: 準拠していないリソースを修復する
description: このガイドでは、Azure Policy のポリシーに準拠していないリソースを修復する手順を説明します。
ms.date: 09/09/2019
ms.topic: how-to
ms.openlocfilehash: 341a325aa7a82c8b1f6366c3a674848c60a8fb5e
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873048"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Azure Policy を使って準拠していないリソースを修復する

**deployIfNotExists** や **modify** に準拠していないリソースは､**修復**を使って準拠状態にすることができます。 修復は､既存のリソースに割り当てられているポリシーの **deployIfNotExists** 効果やタグ**操作**を実行するように Azure Policy に指示することによって実行されます。 この記事では、Azure Policy による修復を理解して実行するために必要な手順を示します。

## <a name="how-remediation-security-works"></a>修復のセキュリティの仕組み

**deployIfNotExists** ポリシー定義にあるテンプレートを実行するとき､Azure Policy では[マネージド ID](../../../active-directory/managed-identities-azure-resources/overview.md) が使用されます｡
マネージド ID は Azure Policy によって各割り当てに対して作成されますが、どのようなロールをマネージド ID に付与するかについての詳細が必要です。 管理対象 ID にロールが存在しない場合、そのポリシーまたはイニシアチブの割り当て中にこのエラーが表示されます。 ポータルを使用している場合、割り当てが開始されると、Azure Policy によって、示されているロールのすべてが自動的にマネージド ID に付与されます。

![管理対象 ID - ロールが存在しない](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> **deployIfNotExists** や **modify** で変更されたリソースがポリシー割り当てのスコープの範囲外の場合､あるいはテンプレートがポリシー割り当てのスコープの範囲外にあるリソース上のプロパティにアクセスした場合､その割り当ての管理対象 ID には[手動でアクセス権を付与](#manually-configure-the-managed-identity)する必要があります。さもないと､修復デプロイは失敗します。

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

> [!NOTE]
> 現在この機能をサポートしている SDK は Azure PowerShell と .NET だけです｡

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

ポータルを使用して､割り当ての管理対象 ID に定義したロールを付与する方法は 2 通りあります｡1 つは **アクセス制御 (IAM)** を利用する方法､もう 1 つはポリシーまたはイニシアティブ割り当てを編集する方法です(最後に **保存** をクリック)。

割り当ての管理対象 ID にロールを追加するには、次の手順に従います｡

1. Azure portal 上で **[すべてのサービス]** をクリックし、 **[ポリシー]** を検索して選択し、Azure Policy サービスを起動します。

1. Azure Policy ページの左側にある **[割り当て]** を選択します。

1. 管理対象 ID がある割り当てを見つけて、その名前をクリックします。

1. 編集ページで**割り当て ID** を見つけます｡ 割り当て ID は以下のような ID です。

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   管理対象 ID の名前は、割り当てリソース ID の最後の部分です｡この例では `2802056bfc094dfb95d4d7a5` です。 割り当てリソース ID のこの部分をコピーします。

1. ロールの定義で手動で追加する必要があるリソース､またはリソースの親コンテナー (リソース グループ、サブスクリプション、管理グループ) に移動します。

1. リソース ページにある **アクセス制御 (IAM)** のリンクをクリックして、アクセス制御ページの上部にある **[+ Add role assignment]\(+ ロール割り当ての追加\)** をクリックします。

1. ポリシー定義から､ **roleDefinitionIds** に一致するロールを選択します｡
   **Assign access to** は ''Azure AD user, group, or application という既定値の設定のままにします｡ **[選択]** ボックスで、前の手順で見つけた割り当てリソース ID の部分を貼り付けるか､入力します。 検索が完了したら、同じ名前のオブジェクトをクリックすることで ID を選択し､ **[保存]** をクリックします。

## <a name="create-a-remediation-task"></a>修復タスクを作成する

### <a name="create-a-remediation-task-through-portal"></a>ポータルを通じて修復タスクを作成する

評価では､**deployIfNotExists** または **modify** 効果を持つポリシー割り当てによって､準拠していないリソースがあるかどうかが判定されます。 準拠していないリソースが見つかった場合は、**修復**ページにその詳細が表示されます。 準拠していないリソースがあるポリシーの一覧には、**修復タスク** をトリガーするオプションがあります｡ このオプションは､**deployIfNotExists** テンプレートまたは **modify** 操作からデプロイを作成するものです。

**修復タスク** を作成するには､次の手順に従います。

1. Azure portal 上で **[すべてのサービス]** をクリックし、 **[ポリシー]** を検索して選択し、Azure Policy サービスを起動します。

   ![[すべてのサービス] で [ポリシー] を検索する](../media/remediate-resources/search-policy.png)

1. Azure Policy ページの左側にある **[修復]** を選択します。

   ![[ポリシー] ページで [修復] を選択する](../media/remediate-resources/select-remediation.png)

1. **deployIfNotExists** および **modify** のポリシー割り当てのうち、準拠していないリソースがあるものはすべて､ **[修復するポリシー]** タブとデータ テーブルに含まれます。 準拠していないリソースがあるポリシーをクリックします。 **新しい修復タスク**ページが開きます。

   > [!NOTE]
   > **修復タスク**ページは、**コンプライアンス** ページから問題のポリシーを見つけて､クリックし、ページの [] をクリックし、**修復タスクの作成**ボタンをクリックしても開くことができます。

1. **[New remediation task]\(新しい修復タスク\)** ページで **[Scope]\(スコープ\)** 省略記号ボタンを使って、ポリシーが割り当てられている子リソースを選択することで、修復するリソースをフィルター処理します (個々のリソース オブジェクトまでフィルター可能)。 また、 **[場所]** ドロップダウンを使って､リソースをさらにフィルター処理することもできます｡ 表に示されたリソースのみ修復されます。

   ![修復 - 修復するリソースを選択する](../media/remediate-resources/select-resources.png)

1. リソースのフィルター処理を終えたら、 **[修復]** をクリックして、修復タスクを開始します。 **[修復タスク]** タブに対するポリシー コンプライアンス ページが開いて、タスクの進行状況が表示されます｡

   ![修復 - 修復タスクの進行状況](../media/remediate-resources/task-progress.png)

1. ポリシー コンプライアンス ページの **[修復タスク]** をクリックすると､進行状況の詳細が表示されます｡ 修復されているリソースの一覧と共に、タスクに使用されたフィルターが表示されます。

1. **[修復タスク]** ページで、修復タスクを右クリックすると、その修復タスクのデプロイまたはリソースのいずれかが表示されます。 行の末尾にあると **[関連イベント]** をクリックすると､エラー メッセージなどの詳細が表示されます｡

   ![修復 - リソース タスクのコンテキスト メニュー](../media/remediate-resources/resource-task-context-menu.png)

**[修復タスク]** を使ってデプロイされたリソースは、ポリシー コンプライアンス ページの **[デプロイされたリソース]** タブに追加されます。

### <a name="create-a-remediation-task-through-azure-cli"></a>Azure CLI を通じて修復タスクを作成する

Azure CLI を使用して**修復タスク** を作成するには、`az policy remediation` コマンドを使用します。 `{subscriptionId}` をサブスクリプション ID に置き換え、`{myAssignmentId}` を **deployIfNotExists** または **modify** のポリシー割り当て ID に置き換えます。

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

その他の修復コマンドや例については、[az ポリシーの修復](/cli/azure/policy/remediation) コマンドを参照してください。

### <a name="create-a-remediation-task-through-azure-powershell"></a>Azure PowerShell を通じて修復タスクを作成する

Azure PowerShell を使用して**修復タスク** を作成するには、`Start-AzPolicyRemediation` コマンドを使用します。 `{subscriptionId}` をサブスクリプション ID に置き換え、`{myAssignmentId}` を **deployIfNotExists** または **modify** のポリシー割り当て ID に置き換えます。

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

その他の修復コマンドレットや例については、[Az.PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) モジュールを参照してください。

## <a name="next-steps"></a>次の手順

- [Azure Policy のサンプル](../samples/index.md)を確認します。
- 「[Azure Policy の定義の構造](../concepts/definition-structure.md)」を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。
- [プログラムによってポリシーを作成する](programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](get-compliance-data.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。
