---
title: リソースをテナントにデプロイする
description: Azure Resource Manager テンプレートでテナントのスコープでリソースをデプロイする方法について説明します。
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: fd5a9ae60c578a3be7f70d82baae0a15e406b9db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99491488"
---
# <a name="tenant-deployments-with-arm-templates"></a>ARM テンプレートを使用したテナントのデプロイ

組織が成熟するにつれて、[ポリシー](../../governance/policy/overview.md)または [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を定義し、Azure AD テナント全体に割り当てる必要性が生じることがあります。 テナント レベルのテンプレートを使用すると、宣言によってポリシーを適用し、グローバル レベルでロールを割り当てることができます。

## <a name="supported-resources"></a>サポートされているリソース

すべてのリソースの種類をテナント レベルにデプロイできるわけではありません。 このセクションでは、サポートされているリソースの種類を示します。

Azure ポリシーでは、以下を使用します。

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

Azure のロールベースのアクセス制御 (Azure RBAC) では、以下を使用します。

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

管理グループ、サブスクリプション、またはリソース グループにデプロイする入れ子になったテンプレートでは、以下を使用します。

* [deployments](/azure/templates/microsoft.resources/deployments)

管理グループを作成するには、以下を使用します。

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

サブスクリプションを作成するには、以下を使用します。

* [aliases](/azure/templates/microsoft.subscription/aliases)

コストを管理するには、以下を使用します。

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [instructions](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

ポータルを構成するには、以下を使用します。

* [tenantConfigurations](/azure/templates/microsoft.portal/tenantconfigurations)

## <a name="schema"></a>スキーマ

テナントのデプロイに使用するスキーマは、リソース グループのデプロイ用のスキーマとは異なります。

テンプレートの場合は、次を使用します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    ...
}
```

パラメーター ファイルのスキーマはすべてのデプロイ範囲で同じです。 パラメーター ファイルの場合は、次を使用します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="required-access"></a>必要なアクセス

テンプレートをデプロイするプリンシパルには、テナントのスコープでリソースを作成するためのアクセス許可が必要です。 このプリンシパルには、デプロイ アクション (`Microsoft.Resources/deployments/*`) を実行し、テンプレートに定義されているリソースを作成するためのアクセス許可が必要です。 たとえば、管理グループを作成するには、プリンシパルにテナントのスコープの共同作成者アクセス許可が必要です。 ロールの割り当てを作成するには、プリンシパルに所有者アクセス許可が必要です。

Azure Active Directory の全体管理者には、ロールを割り当てるアクセス許可が自動的に付与されるわけではありません。 テナントのスコープでテンプレートのデプロイを有効にするには、全体管理者が次の手順を実行する必要があります。

1. 全体管理者がロールを割り当てることができるように、アカウントのアクセス権を昇格させます。 詳細については、「[Azure のすべてのサブスクリプションと管理グループを管理する目的でアクセス権限を昇格させる](../../role-based-access-control/elevate-access-global-admin.md)」を参照してください。

1. テンプレートをデプロイする必要があるプリンシパルに、所有者または共同作成者を割り当てます。

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

これで、テンプレートをデプロイするために必要なアクセス許可がプリンシパルに付与されました。

## <a name="deployment-commands"></a>デプロイ コマンド

テナントのデプロイ用のコマンドは、リソース グループのデプロイ用のコマンドとは異なります。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI の場合は、[az deployment tenant create](/cli/azure/deployment/tenant#az-deployment-tenant-create) を使用します。

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell の場合は [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment) を使用します。

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

---

ARM テンプレートをデプロイするためのデプロイ コマンドとオプションの詳細については、以下を参照してください。

* [ARM テンプレートと Azure portal でリソースをデプロイする](deploy-portal.md)
* [ARM テンプレートと Azure CLI でリソースをデプロイする](deploy-cli.md)
* [ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](deploy-powershell.md)
* [ARM テンプレートと Azure Resource Manager REST API を使用してリソースをデプロイする](deploy-rest.md)
* [デプロイ ボタンを使用して GitHub リポジトリからテンプレートをデプロイする](deploy-to-azure-button.md)
* [Cloud Shell から ARM テンプレートをデプロイする](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>デプロイの場所と名前

テナント レベルのデプロイの場合は、デプロイの場所を指定する必要があります。 デプロイの場所は、デプロイするリソースの場所とは異なります。 デプロイの場所では、デプロイ データを格納する場所を指定します。 [サブスクリプション](deploy-to-subscription.md)および[管理グループ](deploy-to-management-group.md)のデプロイにも場所が必要です。 [リソース グループ](deploy-to-resource-group.md)のデプロイの場合、リソース グループの場所を使用してデプロイ データを格納します。

デプロイ名を指定することも、既定のデプロイ名を使用することもできます。 既定の名前は、テンプレート ファイルの名前です。 たとえば、_azuredeploy.json_ という名前のテンプレートをデプロイすると、既定のデプロイ名として **azuredeploy** が作成されます。

デプロイ名ごとに、場所を変更することはできません。 ある場所にデプロイを作成しようとしても、別の場所に同じ名前の既存のデプロイがあると、作成することはできません。 たとえば、**centralus** で **deployment1** という名前のテナントのデプロイを作成した場合、後で **deployment1** という名前の別のデプロイを **westus** の場所に作成することはできません。 エラー コード `InvalidDeploymentLocation` が表示された場合は、別の名前を使用するか、その名前の以前のデプロイと同じ場所を使用してください。

## <a name="deployment-scopes"></a>デプロイのスコープ

テナントにデプロイする際には、リソースを以下にデプロイできます。

* テナント
* テナント内の管理グループ
* subscriptions
* リソース グループ

[拡張リソース](scope-extension-resources.md)は、デプロイ ターゲットとは異なるターゲットにスコープ設定できます。

テンプレートをデプロイするユーザーは、特定のスコープにアクセスできる必要があります。

このセクションでは、異なるスコープを指定する方法について説明します。 これらの異なるスコープを 1 つのテンプレートで結合することができます。

### <a name="scope-to-tenant"></a>テナントへのスコープ

テンプレートのリソース セクション内で定義されたリソースは、テナントに適用されます。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

### <a name="scope-to-management-group"></a>管理グループへのスコープ

テナント内で管理グループを対象にするには、入れ子になったデプロイを追加し、`scope` プロパティを指定します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>サブスクリプションへのスコープ

また、テナント内のサブスクリプションを対象にすることもできます。 テンプレートをデプロイするユーザーは、特定のスコープにアクセスできる必要があります。

テナント内のサブスクリプションを対象にするには、入れ子になったデプロイと `subscriptionId` プロパティを使用します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>リソース グループへのスコープ

また、テナント内のリソース グループを対象にすることもできます。 テンプレートをデプロイするユーザーは、特定のスコープにアクセスできる必要があります。

そのテナント内のリソース グループを対象にするには、入れ子になったデプロイを使用します。 `subscriptionId` と `resourceGroup` プロパティを設定します。 入れ子になったデプロイの場所はリソース グループの場所にデプロイされているため、設定しないでください。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-rg.json" highlight="9,10,18":::

## <a name="create-management-group"></a>管理グループの作成

次のテンプレートを使うと、管理グループを作成できます。

:::code language="json" source="~/quickstart-templates/tenant-deployments/new-mg/azuredeploy.json":::

テナントにデプロイするためのアクセス許可がアカウントにない場合でも、別のスコープにデプロイすることによって管理グループを作成できます。 詳細については、「[管理グループ](deploy-to-management-group.md#management-group)」を参照してください。

## <a name="assign-role"></a>ロールを割り当てる

次のテンプレートを使うと、テナントのスコープでロールを割り当てることができます。

:::code language="json" source="~/quickstart-templates/tenant-deployments/tenant-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>次のステップ

* ロールの割り当ての詳細については、「[Azure Resource Manager テンプレートを使用して Azure でのロールの割り当てを追加する](../../role-based-access-control/role-assignments-template.md)」を参照してください。
* また、[サブスクリプション レベル](deploy-to-subscription.md)または[管理グループ レベル](deploy-to-management-group.md)でテンプレートをデプロイすることもできます。
