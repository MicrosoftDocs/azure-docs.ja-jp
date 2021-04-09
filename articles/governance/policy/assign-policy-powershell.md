---
title: 'クイック スタート: PowerShell を使用した新しいポリシー割り当て'
description: このクイックスタートでは、Azure PowerShell を使用して、Azure Policy の割り当てを作成し、準拠していないリソースを特定します。
ms.date: 08/17/2020
ms.topic: quickstart
ms.openlocfilehash: e941b74101308af703f243197fb4043f8f32d233
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "88548414"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>クイックスタート: Azure PowerShell を使用して準拠していないリソースを識別するためのポリシー割り当てを作成する

Azure のコンプライアンスを理解する第一歩は、リソースの状態を特定することです。 このクイック スタートでは、マネージド ディスクを使用していない仮想マシンを識別するためのポリシー割り当てを作成します。 完了すると、"_準拠していない_" 仮想マシンが特定されます。

Azure PowerShell モジュールは、コマンド ラインやスクリプトで Azure リソースを管理するために使用します。
このガイドでは、Az モジュールを使用してポリシーの割り当てを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

- 開始する前に、最新バージョンの Azure PowerShell がインストールされていることを確認してください。 詳細については、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

- Azure PowerShell を使用して Azure Policy Insights リソース プロバイダーを登録します。 リソース プロバイダーを登録すると、そのリソース プロバイダーで、ご利用のサブスクリプションを確実に動作させることができます。 リソース プロバイダーを登録するには、リソース プロバイダーの登録操作のためのアクセス許可が必要です。 この操作は、共同作成者ロールと所有者ロールに含まれます。 リソース プロバイダーを登録する以下のコマンドを実行します。

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  リソース プロバイダーの登録と表示の詳細については、「[リソース プロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

このクイック スタートでは、"_マネージド ディスクを使用していない VM を監査_" 定義のためのポリシー割り当てを作成します。 このポリシー定義では、マネージド ディスクを使用していない仮想マシンを識別します。

ポリシーの割り当てを新たに作成するには、次のコマンドを実行します。

```azurepowershell-interactive
# Get a reference to the resource group that is the scope of the assignment
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'

# Get a reference to the built-in policy definition to assign
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }

# Create the policy assignment with the built-in definition against your resource group
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

上記のコマンドでは次の情報が使用されています。

- **Name** - 割り当ての実際の名前。 この例では、_audit-vm-manageddisks_ が使用されました。
- **DisplayName** - ポリシーの割り当てに使用する表示名。 このケースでは、"_Audit VMs without managed disks Assignment_" を使用します。
- **Definition** - 割り当ての作成に使用するポリシー定義。 ここでは、"_Managed Disks を使用していない VM の監査_" というポリシー定義の ID です。
- **Scope** - スコープによって、ポリシーの割り当てを強制するリソースまたはリソースのグループが決まります。 サブスクリプションからリソース グループまで、適用対象は多岐にわたります。 &lt;scope&gt; は、実際のリソース グループの名前に置き換えてください。

以上の手順で、準拠していないリソースを特定し、環境のコンプライアンスの状態を理解できるようになりました。

## <a name="identify-non-compliant-resources"></a>準拠していないリソースを特定する

以下、作成したポリシーの割り当てに準拠していないリソースを特定する方法について説明します。 次のコマンドを実行します。

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

ポリシーの状態を取得する方法の詳細については、「[Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState)」を参照してください。

次のような結果が返されます。

```output
Timestamp                   : 3/9/19 9:21:29 PM
ResourceId                  : /subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmId}
PolicyAssignmentId          : /subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/audit-vm-manageddisks
PolicyDefinitionId          : /providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d
IsCompliant                 : False
SubscriptionId              : {subscriptionId}
ResourceType                : /Microsoft.Compute/virtualMachines
ResourceTags                : tbd
PolicyAssignmentName        : audit-vm-manageddisks
PolicyAssignmentOwner       : tbd
PolicyAssignmentScope       : /subscriptions/{subscriptionId}
PolicyDefinitionName        : 06a78e20-9358-41c9-923c-fb736d382a4d
PolicyDefinitionAction      : audit
PolicyDefinitionCategory    : Compute
ManagementGroupIds          : {managementGroupId}
```

この結果は、Azure portal ビュー内のポリシー割り当ての **[リソース コンプライアンス]** タブに表示される内容と同じです。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成した割り当てを削除するには、次のコマンドを使用します。

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ポリシー定義を割り当てて、Azure 環境内で準拠していないリソースを特定しました。

新しいリソースが準拠していることを検証するためのポリシーの割り当てについて詳しく学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [ポリシーの作成と管理](./tutorials/create-and-manage.md)