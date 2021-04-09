---
title: Azure Policy を使用した AKS の監視アドオンの有効化
description: Azure カスタム ポリシーを使用して AKS の監視アドオンを有効にする方法について説明します。
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 2163527cc83e70913e9a6e11bf2e22f9ed9c6690
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713900"
---
# <a name="enable-aks-monitoring-addon-using-azure-policy"></a>Azure Policy を使用した AKS の監視アドオンの有効化
この記事では、Azure カスタム ポリシーを使用して AKS の監視アドオンを有効にする方法について説明します。 監視アドオン カスタム ポリシーは、サブスクリプションまたはリソース グループのスコープで割り当てることができます。 Azure Log Analytics ワークスペースと AKS クラスターが異なるサブスクリプションに存在する場合、ポリシーの割り当てに使用されるマネージド ID は、両方のサブスクリプションで、または少なくとも Log Analytics ワークスペースのリソース上で、必要なロールのアクセス許可を持っている必要があります。 同様に、ポリシーのスコープがリソース グループの場合は、選択したリソース グループのスコープ内に Log Analytics ワークスペースがない場合、そのマネージド ID にはワークスペースに対する必要なロールのアクセス許可が必要です。

監視アドオンには、Azure Policy によって使用されるマネージド ID に対して次のロールが必要です。

 - [azure-kubernetes-service-contributor-role](../../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role)
 - [log-analytics-contributor](../../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="create-and-assign-policy-definition-using-azure-portal"></a>Azure portal を使用してポリシー定義を作成し割り当てる

### <a name="create-policy-definition"></a>ポリシー定義の作成

1. Azure カスタム ポリシー定義をダウンロードして、AKS の監視アドオンを有効にします。
 
    ``` sh
    curl -o azurepolicy.json -L https://aka.ms/aks-enable-monitoring-custom-policy
    ```

3. https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions に移動し、[ポリシー定義の作成] ダイアログ ボックスで次の詳細を使用してポリシー定義を作成します。
 
    - **[定義の場所]** :ポリシー定義を格納する Azure サブスクリプションを選択します。
    - **[名前]** : " *(プレビュー) AKS の監視アドオン*"
    - **[説明]** :"*指定されたスコープの Azure Kubernetes クラスターの監視アドオンを有効にする Azure カスタム ポリシー*"
    - **カテゴリ**: *[既存のものを使用]* を選択し、ドロップダウンから *[Kubernetes]* を選択します。
    - **[ポリシー規則]** :既存のサンプル ルールを削除し、上記の手順 #1 でダウンロードした *azurepolicy.json* の内容をコピーします。

### <a name="assign-policy-definition-to-specified-scope"></a>指定されたスコープにポリシー定義を割り当てる

> [!NOTE]
>  マネージド ID は自動的に作成され、指定されたロールがポリシー定義に割り当てられます。

1. 作成したばかりのポリシー定義 *[(プレビュー) AKS の監視アドオン]* を選択します。
4. *[割り当て**]* をクリックし、ポリシーを割り当てる **[スコープ]** を指定します。 
5. **[次へ]** をクリックし、Azure Log Analytics ワークスペースのリソース ID を指定します。 リソース ID の形式は、`/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>` です。
6. 選択したスコープ内の既存の AKS クラスターにポリシーを適用する場合に備えて、修復タスクを作成します。
7. **[確認と作成]** オプションをクリックして、ポリシーの割り当てを作成します。
   
## <a name="create-and-assign-policy-definition-using-azure-cli"></a>Azure CLI を使用してポリシー定義を作成し割り当てる

### <a name="create-policy-definition"></a>ポリシー定義の作成

1. 次のコマンドを使用して、Azure カスタム ポリシー定義のルールとパラメーター ファイルをダウンロードします。

    ``` sh
    curl -o azurepolicy.rules.json -L https://aka.ms/aks-enable-monitoring-custom-policy-rules
    curl -o azurepolicy.parameters.json -L https://aka.ms/aks-enable-monitoring-custom-policy-parameters
    ```

2. 次のコマンドを使用して、ポリシー定義を作成します。

    ``` sh
    az cloud set -n <AzureCloud | AzureChinaCloud | AzureUSGovernment> # set the Azure cloud
    az login # login to cloud environment 
    az account set -s <subscriptionId>
    az policy definition create --name "(Preview)AKS-Monitoring-Addon" --display-name "(Preview)AKS-Monitoring-Addon" --mode Indexed --metadata version=1.0.0 category=Kubernetes --rules azurepolicy.rules.json --params azurepolicy.parameters.json
    ```

### <a name="assign-policy-definition-to-specified-scope"></a>指定されたスコープにポリシー定義を割り当てる

- 次のコマンドを使用して、ポリシー割り当てを作成します。

    ``` sh
    az policy assignment create --name aks-monitoring-addon --policy "(Preview)AKS-Monitoring-Addon" --assign-identity --identity-scope /subscriptions/<subscriptionId> --role Contributor --scope /subscriptions/<subscriptionId> --location <locatio> --role Contributor --scope /subscriptions/<subscriptionId> -p "{ \"workspaceResourceId\": { \"value\":  \"/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>\" } }"
    ```

## <a name="next-steps"></a>次のステップ

- [Azure Policy](../../governance/policy/overview.md) の詳細を確認します。
- [修復のセキュリティの仕組み](../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works)を確認します。
- [Container insights](./container-insights-overview.md) について詳しく学習します。
- [Azure CLI](/cli/azure/install-azure-cli) をインストールします。