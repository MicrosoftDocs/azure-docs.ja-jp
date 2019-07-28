---
title: サンプル - アプリケーションが Linux VM 内にインストールされていないかどうかを監査する
description: このサンプル Policy ゲスト構成のイニシアチブと定義は、指定したアプリケーションが Linux 仮想マシン内にインストールされていないかどうかを監査します。
author: dcthegeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 05/02/2019
ms.author: dacoulte
ms.openlocfilehash: eda5a2a6d2dae58f8da72deccbb89a34c7f21dae
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204011"
---
# <a name="sample---audit-if-specified-applications-arent-installed-inside-linux-vms"></a>サンプル - 指定したアプリケーションが Linux VM 内にインストールされていないかどうかを監査する

この Policy ゲスト構成のイニシアチブでは、指定したアプリケーションが Linux 仮想マシン内にインストールされていない場合に監査イベントを作成します。 このビルトイン イニシアチブの ID は `/providers/Microsoft.Authorization/policySetDefinitions/c937dcb4-4398-4b39-8d63-4a6be432252e` です。

> [!IMPORTANT]
> すべてのゲスト構成イニシアチブは、**audit** と **deployIfNotExists** というポリシー定義から成ります。 どちらか一方のポリシー定義しか割り当てなかった場合、ゲスト構成が正しく機能しなくなります。

このサンプルは、次の方法で割り当てることができます。

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="components-of-the-initiative"></a>イニシアチブのコンポーネント

この[ゲスト構成](../concepts/guest-configuration.md)イニシアチブは、次のポリシーから成ります。

- [audit](#audit-definition) - 指定したアプリケーションが Linux VM 内にインストールされていない場合に監査を行う
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/fee5cb2b-9d9b-410e-afe3-2902d90d0004`
- [deployIfNotExists](#deployIfNotExists-definition) - Linux VM 内にアプリケーションがインストールされていない場合に監査を行うための VM 拡張機能をデプロイする
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/4d1c04de-2172-403f-901b-90608c35c721`

### <a name="initiative-definition"></a>イニシアチブ定義

イニシアチブは、**audit** と **deployIfNotExists** の 2 つの定義と[イニシアチブ パラメーター](#initiative-parameters)とを組み合わせることによって作成します。 この定義の JSON を次に示します。

[!code-json[initiative-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/azurepolicyset.json "Initiative definition (JSON)")]

### <a name="initiative-parameters"></a>イニシアチブ パラメーター

|Name |Type |説明 |
|---|---|---|
|applicationName |String |アプリケーション名。 たとえば、"python"、"powershell"、コンマ区切りリスト ("python,powershell" など) を指定します。 ワイルドカード検索には、"power\*" のように \* を使用できます。 |

PowerShell または Azure CLI を使って割り当てを作成するときは、パラメーターの値を JSON として渡すことができます。JSON として渡すパラメーターは、文字列で渡すか、または `-PolicyParameter` (PowerShell) あるいは `--params` (Azure CLI) を使用してファイルで渡します。
PowerShell では、`-PolicyParameterObject` もサポートされます。この場合は、コマンドレットに Name/Value ハッシュ テーブルを渡す必要があります。**Name** にはパラメーターの名前を、**Value** には、割り当て時に渡す値の配列または単一値を指定します。

この例のパラメーターでは、アプリケーション _python_ と _powershell_ のインストールが監査されます。

```json
{
    "applicationName": {
        "value": "python,powershell"
    }
}
```

イニシアチブ パラメーターを使用できるポリシー定義は、**deployIfNotExists** だけです。

### <a name="audit-definition"></a>audit の定義

次に示したのは、**audit** ポリシー定義のルールを定義する JSON です。

[!code-json[audit-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json "audit policy rules (JSON)")]

### <a name="deployifnotexists-definition"></a>deployIfNotExists の定義

次に示したのは、**deployIfNotExists** ポリシー定義のルールを定義する JSON です。

[!code-json[deployIfNotExists-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json "deployIfNotExists policy rules (JSON)")]

**deployIfNotExists** ポリシー定義は、そのポリシーの検証対象となった Azure イメージを定義します。

|Publisher |プラン |SKU |
|-|-|-|
|OpenLogic |CentOS\* |6 を除くすべて\* |
|RedHat |RHEL |6 を除くすべて\* |
|RedHat |osa | All |
|credativ |Debian | 7 を除くすべて\* |
|SUSE |SLES\* |11 を除くすべて\* |
|Canonical| UbuntuServer |12 を除くすべて\* |
|microsoft-dsvm |linux-data-science-vm-ubuntu |All |
|microsoft-dsvm |azureml |All |
|cloudera |cloudera-centos-os |6 を除くすべて\* |
|cloudera |cloudera-altus-centos-os |All |
|microsoft-ads |linux\* |All |
|microsoft-aks |All |All |
|AzureDatabricks |All |All |
|qubole-inc |All |All |
|datastax |All |All |
|couchbase |All |All |
|scalegrid |All |All |
|checkpoint |All |All |
|paloaltonetworks |All |All |

このルールの **deployment** 部分では、仮想マシン上のゲスト構成エージェントに _installedApplication_ パラメーターを渡しています。 エージェントはこの構成によって検証を実行し、**audit** ポリシー定義を通じてコンプライアンス レポートを返すことができます。

## <a name="azure-portal"></a>Azure ポータル

ポータルで **audit** と **deployIfNotExists** の定義を作成したら、割り当て用の[イニシアチブ](../concepts/definition-structure.md#initiatives)としてそれらをグループ化することをお勧めします。

### <a name="create-copy-of-audit-definition"></a>audit 定義のコピーを作成する

[![ポリシーのサンプルを Azure にデプロイする](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)
[![ポリシーのサンプルを Azure Gov にデプロイする](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)

これらのボタンを使用してポータル経由でデプロイすると、**audit** ポリシー定義のコピーが作成されます。
対になる **deployIfNotExists** ポリシー定義がないと、ゲスト構成が正しく機能しません。

### <a name="create-copy-of-deployifnotexists-definition"></a>deployIfNotExists 定義のコピーを作成する

[![ポリシーのサンプルを Azure にデプロイする](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)
[![ポリシーのサンプルを Azure Gov にデプロイする](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)

これらのボタンを使用してポータル経由でデプロイすると、**deployIfNotExists** ポリシー定義のコピーが作成されます。 対になる **audit** ポリシー定義がないと、ゲスト構成が正しく機能しません。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Azure PowerShell でのデプロイ

#### <a name="copy-and-assign-the-initiative"></a>イニシアチブをコピーして割り当てる

次の各ステップでは、**audit** と **deployIfNotExists** の両方のビルトイン ポリシーを含んだイニシアチブのコピーを作成し、そのイニシアチブをリソース グループに割り当てます。

```azurepowershell-interactive
# Create the policy initiative (Subscription scope)
$initDef = New-AzPolicySetDefinition -Name 'guestconfig-installed-application-linux' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This initiative will both deploy the policy requirements and audit that the specified application is installed inside Linux virtual machines.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.definitions.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the initiative parameter (JSON format)
$initParam = '{ "applicationName": { "value": "python,powershell" } }'

# Create the initiative assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicySetDefinition $initDef -PolicyParameter $initParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

以前の割り当てと定義を削除するには、次のコマンドを実行します。

```azurepowershell-interactive
# Remove the initiative assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the initiative definition
Remove-AzPolicySetDefinition -Id $initDef
```

#### <a name="copy-and-assign-the-audit-definition"></a>audit 定義をコピーして割り当てる

次の各ステップでは、**audit** 定義のコピーを作成し、それをリソース グループに割り当てます。 この定義は、対になる **deployIfNotExists** 定義も割り当てられていないと正しく機能しません。

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-audit' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This policy audits that the specified application is installed inside Linux virtual machines. This policy should only be used along with its corresponding deploy policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-audit-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicyDefinition $definition
```

以前の割り当てと定義を削除するには、次のコマンドを実行します。

```azurepowershell-interactive
# Remove the policy definition
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

#### <a name="copy-and-assign-the-deployifnotexists-definition"></a>deployIfNotExists 定義をコピーして割り当てる

次の各ステップでは、**deployIfNotExists** 定義のコピーを作成し、それをリソース グループに割り当てます。
この定義は、対になる **audit** 定義も割り当てられていないと正しく機能しません。

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-deployIfNotExists' -DisplayName 'GuestConfig - Deploy VM extension to audit that an application is installed inside Linux VMs' -description 'Include this rule to deploy the VM extension for Microsoft Guest Configuration, the VM extension for Microsoft Azure Managed Service Identity, and the content required to audit that an application is installed inside Linux virtual machines. This policy should only be used along with its corresponding audit policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the definition parameter (JSON format)
$policyParam  = '{ "applicationName": { "value": "python,powershell" } }'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-deployIfNotExists-assignment' -DisplayName 'GuestConfig - Deploy VM extension to audit that Python and PowerShell are installed inside Linux VMs' -Scope $scope.ResourceID -PolicyDefinition $definition -PolicyParameter $policyParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

以前の割り当てと定義を削除するには、次のコマンドを実行します。

```azurepowershell-interactive
# Remove the policy assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

### <a name="azure-powershell-explanation"></a>Azure PowerShell の説明

デプロイおよび削除のスクリプトには、次のコマンドが使用されています。 以下の表の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzPolicySetDefinition](/powershell/module/az.resources/New-AzPolicySetDefinition) | Azure Policy のイニシアチブを作成します。 |
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-AzPolicyDefinition) | Azure Policy の定義を作成します。 |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup) | 単一のリソース グループを取得します。 |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-AzPolicyAssignment) | イニシアチブまたは定義に使用する新しい Azure Policy の割り当てを作成します。 |
| [New-AzRoleAssignment](/powershell/module/az.resources/New-AzRoleAssignment) | 特定のプリンシパルに既存のロールの割り当てを付与します。 |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-AzPolicyAssignment) | 既存の Azure Policy の割り当てを削除します。 |
| [Remove-AzPolicySetDefinition](/powershell/module/az.resources/Remove-AzPolicySetDefinition) | イニシアチブを削除します。 |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-AzPolicyDefinition) | 定義を削除します。 |

## <a name="next-steps"></a>次の手順

- その他の [Azure Policy サンプル](index.md)を確認する。
- [Azure Policy ゲスト構成](../concepts/guest-configuration.md)について詳しく知る。
- [Azure Policy の定義の構造](../concepts/definition-structure.md)を確認する。
