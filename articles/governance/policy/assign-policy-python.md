---
title: クイック スタート:Python を使用した新しいポリシー割り当て
description: このクイックスタートでは、Python を使用して、準拠していないリソースを特定するための Azure Policy 割り当てを作成します。
ms.date: 03/02/2021
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: e600f97dafdd1040c22b6e4d9e333f638334b663
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742332"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-python"></a>クイック スタート:Python を使用して準拠していないリソースを特定するためのポリシー割り当てを作成する

Azure のコンプライアンスを理解する第一歩は、リソースの状態を特定することです。 このクイック スタートでは、マネージド ディスクを使用していない仮想マシンを識別するためのポリシー割り当てを作成します。 完了すると、"_準拠していない_" 仮想マシンが特定されます。

Python ライブラリを使用して、コマンド ラインまたはスクリプトで Azure リソースを管理します。 このガイドでは、Python ライブラリを使用してポリシー割り当てを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-library"></a>ポリシー ライブラリの追加

Python で Azure Policy を使用できるようにするには、ライブラリを追加する必要があります。 このライブラリは、[Windows 10 の bash](/windows/wsl/install-win10) やローカルにインストールされている場合も含め、Python を使用できる場所ならどこでも動作します。

1. 最新の Python がインストールされていることを確認します (**3.8** 以降)。 まだインストールされていない場合は、[Python.org](https://www.python.org/downloads/) でダウンロードします。

1. 最新の Azure CLI がインストールされていることを確認します (**2.5.1** 以降)。 まだインストールされていない場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

   > [!NOTE]
   > 次の例で、Python で **CLI ベースの認証** を使用できるようにするには、Azure CLI が必要です。 その他のオプションの詳細については、[Python 用 Azure 管理ライブラリを使用した認証](/azure/developer/python/azure-sdk-authenticate)に関するページを参照してください。

1. Azure CLI から認証します。

   ```azurecli
   az login
   ```

1. 選択した Python 環境で、Azure Policy に必要なライブラリをインストールします。

   ```bash
   # Add the Python library for Python
   pip install azure-mgmt-policyinsights

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > すべてのユーザーに対して Python がインストールされている場合、これらのコマンドは管理者特権のコンソールから実行する必要があります。

1. ライブラリがインストールされていることを確認します。 `azure-mgmt-policyinsights` は **0.5.0** 以上、`azure-mgmt-resource` は **9.0.0** 以上、`azure-cli-core` は **2.5.0** 以上である必要があります。

   ```bash
   # Check each installed library
   pip show azure-mgmt-policyinsights azure-mgmt-resource azure-cli-core
   ```

## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

このクイックスタートでは、ポリシー割り当てを作成し、**マネージド ディスクを使用しない監査 VM** (`06a78e20-9358-41c9-923c-fb736d382a4d`) 定義を割り当てます。 このポリシー定義では、ポリシー定義で設定されている条件に準拠していないリソースが識別されます。

次のコードを実行して、新しいポリシー割り当てを作成します。

```python
# Import specific methods and models from other libraries
from azure.common.credentials import get_azure_cli_credentials
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient
from azure.mgmt.resource.policy.models import PolicyAssignment

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Create details for the assignment
policyAssignmentDetails = PolicyAssignment(display_name="Audit VMs without managed disks Assignment", policy_definition_id="/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d", scope="{scope}", description="Shows all virtual machines not using managed disks")

# Create new policy assignment
policyAssignment = policyClient.policy_assignments.create("{scope}", "audit-vm-manageddisks", policyAssignmentDetails)

# Show results
print(policyAssignment)
```

上記のコマンドでは次の情報が使用されています。

割り当ての詳細:
- **display_name** - ポリシー割り当ての表示名。 このケースでは、"_Audit VMs without managed disks Assignment_" を使用します。
- **policy_definition_id** - 割り当てを作成する際に基礎として使用するポリシー定義パス。 ここでは、"_Managed Disks を使用していない VM の監査_" というポリシー定義の ID です。 この例では、ポリシー定義は組み込みであり、パスには管理グループまたはサブスクリプション情報は含まれていません。
- **scope** - スコープによって、ポリシー割り当てを適用するリソースまたはリソース グループが決まります。 スコープには、管理グループから個々のリソースまで指定できます。 `{scope}` は、次のパターンのいずれかに必ず置き換えてください。
  - 管理グループ: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - サブスクリプション: `/subscriptions/{subscriptionId}`
  - リソース グループ: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - リソース: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`
- **description** - ポリシーの機能またはこのスコープに割り当てる理由の詳細な説明。

割り当ての作成:

- Scope - このスコープによって、ポリシー割り当ての保存先が決まります。 割り当ての詳細で設定されたスコープは、このスコープ内に存在する必要があります。
- Name - 割り当ての実際の名前。 この例では、_audit-vm-manageddisks_ が使用されました。
- Policy assignment - 前の手順で作成された、Python の **PolicyAssignment** オブジェクト。

以上の手順で、準拠していないリソースを特定し、環境のコンプライアンスの状態を理解できるようになりました。

## <a name="identify-non-compliant-resources"></a>準拠していないリソースを特定する

以下、作成したポリシーの割り当てに準拠していないリソースを特定する方法について説明します。 次のコードを実行します。

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.policyinsights._policy_insights_client import PolicyInsightsClient
from azure.mgmt.policyinsights.models import QueryOptions

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyInsightsClient = get_client_from_cli_profile(PolicyInsightsClient)

# Set the query options
queryOptions = QueryOptions(filter="IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'",apply="groupby((ResourceId))")

# Fetch 'latest' results for the subscription
results = policyInsightsClient.policy_states.list_query_results_for_subscription(policy_states_resource="latest", subscription_id="{subscriptionId}", query_options=queryOptions)

# Show results
print(results)
```

`{subscriptionId}` は、このポリシー割り当ての準拠の結果を表示するサブスクリプションに置き換えます。 他のスコープの一覧とデータを集計する方法については、[ポリシーの状態メソッド](/python/api/azure-mgmt-policyinsights/azure.mgmt.policyinsights.operations.policystatesoperations#methods)に関する記事をご覧ください。

次のような結果が返されます。

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

この結果は、Azure portal ビュー内のポリシー割り当ての **[リソース コンプライアンス]** タブに表示される内容と同じです。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成した割り当てを削除するには、次のコマンドを使用します。

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Delete the policy assignment
policyAssignment = policyClient.policy_assignments.delete("{scope}", "audit-vm-manageddisks")

# Show results
print(policyAssignment)
```

`{scope}` は、ポリシー割り当てを作成する際に使用したものと同じスコープに置き換えます。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ポリシー定義を割り当てて、Azure 環境内で準拠していないリソースを特定しました。

新しいリソースが準拠していることを検証するためのポリシー定義の割り当てについて詳しく学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [ポリシーの作成と管理](./tutorials/create-and-manage.md)
