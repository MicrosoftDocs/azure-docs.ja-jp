---
title: Azure Policy リソースをエクスポートする
description: ポリシー定義やポリシーの割り当てなど、Azure Policy のリソースを GitHub にエクスポートするやり方を学習します。
ms.date: 10/29/2020
ms.topic: how-to
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 923b063244f6f47def1c3e6a63d6e4d6b3b88083
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94919567"
---
# <a name="export-azure-policy-resources"></a>Azure Policy リソースをエクスポートする

この記事では、既存の Azure Policy リソースをエクスポートする方法について説明します。 リソースのエクスポートは、バックアップには便利で推奨されますが、クラウド ガバナンスおよび [コードとしてのポリシー](../concepts/policy-as-code.md)の扱いに関する重要な手順でもあります。 Azure Policy リソースは、 [Azure portal](#export-with-azure-portal)、 [Azure CLI](#export-with-azure-cli)、 [Azure PowerShell](#export-with-azure-powershell)、およびサポートされている各 SDK を使用してエクスポートできます。

## <a name="export-with-azure-portal"></a>Azure portal を使用したエクスポート

Azure portal からポリシー定義をエクスポートするには、次の手順を実行します。

1. Azure portal 上で **[すべてのサービス]** をクリックし、 **[ポリシー]** を検索して選択し、Azure Policy サービスを起動します。

1. Azure Policy ページの左側にある **[定義]** を選択します。

1. **[Export definitions]\(定義のエクスポート\)** ボタンを使用するか、ポリシー定義の行の省略記号を選択してから、 **[Export definition]\(定義のエクスポート\)** を選択し ます。

1. **[Sign in with GitHub]\(GitHub でサインイン\)** ボタンを選択します。 リソースをエクスポートする Azure Policy を承認するために GitHub で認証されていない場合は、表示される新しいウィンドウで [GitHub Action](https://github.com/features/actions) に必要なアクセス権を確認し、 **[Authorize AzureGitHubActions]\(AzureGitHubActions を承認する\)** を選択して、エクスポート プロセスを続行します。 完了すると、新しいウィンドウが自動的に閉じます。

1. **[基本]** タブで、次のオプションを設定し、 **[ポリシー]** タブまたは **[Next : Policies]\(次へ: ポリシー\)** ボタンがページの下部にあるので選択します。

   - **Repository filter (リポジトリ フィルター)** :自分が所有しているリポジトリのみを表示するには _[My repositories]\(マイ リポジトリ\)_ に、GitHub アクションによるアクセスを許可したものをすべてを表示するには _[All repositories]\(すべてのリポジトリ\)_ に設定します。
   - **リポジトリ**:Azure Policy リソースをエクスポートするリポジトリを設定します。
   - **ブランチ**:リポジトリにブランチを設定します。 ソース コードにさらにマージする前に更新を検証する場合は、既定以外のブランチを使用することをお勧めします。
   - **ディレクトリ**:Azure Policy リソースをエクスポートする "_ルート レベル フォルダー_"。 このディレクトリの下のサブフォルダーは、エクスポートされるリソースに基づいて作成されます。

1. **[ポリシー]** タブで、省略記号を選択し、管理グループ、サブスクリプション、またはリソース グループの組み合わせを選択して、検索範囲を設定します。
   
1. **[Add policy definition(s)]\(ポリシー定義の追加\)** ボタンを使用して、エクスポートするオブジェクトのスコープを検索します。 開いたサイド ウィンドウ内で、エクスポートする各オブジェクトを選択します。 検索ボックスまたは種類によって選択をフィルター処理します。 エクスポートするオブジェクトをすべて選択したら、ページの下部にある **[追加]** ボタンを使用します。

1. 選択した各オブジェクトについて、 _[Only Definition]\(定義のみ\)_ や _[Definition and Assignment(s)]\(定義と割り当て\)_ など、ポリシー定義の目的のエクスポート オプションを選択し ます。 次に、 **[Review + Export]\(確認 + エクスポート\)** タブまたは **[Next : Review + Export]\(次へ: 確認 + エクスポート\)** ボタンがページ下部にあるので選択します。

   > [!NOTE]
   > _[Definition and Assignment(s)]\(定義と割り当て\)_ オプションを選択した場合、ポリシー定義の追加時にフィルターによって設定されたスコープ内のポリシー割り当てのみがエクスポートされます。

1. **[Review + Export]\(確認 + エクスポート\)** タブで、詳細が一致していることを確認し、ページの下部にある **[エクスポート]** ボタンを使用します。

1. GitHub リポジトリ、ブランチ、"_ルート レベル フォルダー_" を確認して、選択したリソースがソース管理にエクスポートされていることを確認します。

Azure Policy リソースは、選択した GitHub リポジトリと _ルートレベル フォルダー_ 内の次の構造にエクスポートされ ます。

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

## <a name="export-with-azure-cli"></a>Azure CLI を使用したエクスポート

Azure Policy の定義、イニシアチブ、および割り当ては、 [Azure CLI](/cli/azure/install-azure-cli)を使用して JSON としてエクスポートできます。 これらの各コマンドは、 **name** パラメーターを使用して、JSON を取得するオブジェクトを指定します。 **name** プロパティは、多くの場合、_GUID_ であり、オブジェクトの **displayName** ではありません。

- 定義 - [az policy definition show](/cli/azure/policy/definition#az_policy_definition_show)
- イニシアチブ - [az policy set-definition show](/cli/azure/policy/set-definition#az_policy_set_definition_show)
- 割り当て - [az policy assignment show](/cli/azure/policy/assignment#az_policy_assignment_show)

_VirtualMachineStorage_ の **名前** を使用して、ポリシー定義の JSON を取得する例を次に示します。

```azurecli-interactive
az policy definition show --name 'VirtualMachineStorage'
```

## <a name="export-with-azure-powershell"></a>Azure PowerShell を使用したエクスポート

Azure Policy の定義、イニシアチブ、および割り当ては、 [Azure PowerShell](/powershell/azure/)を使用して JSON としてエクスポートできます。 これらの各コマンドレットは、 **Name** パラメーターを使用して、JSON を取得するオブジェクトを指定します。 **Name** プロパティは、多くの場合、_GUID_ であり、オブジェクトの **displayName** ではありません。

- 定義 - [Get-AzPolicyDefinition](/powershell/module/az.resources/get-azpolicydefinition)
- イニシアチブ - [Get-AzPolicySetDefinition](/powershell/module/az.resources/get-azpolicysetdefinition)
- 割り当て - [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment)

_VirtualMachineStorage_ の **名前** を使用して、ポリシー定義の JSON を取得する例を次に示します。

```azurepowershell-interactive
Get-AzPolicyDefinition -Name 'VirtualMachineStorage' | ConvertTo-Json -Depth 10
```

## <a name="next-steps"></a>次のステップ

- [Azure Policy のサンプル](../samples/index.md)を確認します。
- 「[Azure Policy の定義の構造](../concepts/definition-structure.md)」を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。
- [プログラムによってポリシーを作成する](programmatically-create.md)方法を理解します。
- [準拠していないリソースを修復する](remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。
