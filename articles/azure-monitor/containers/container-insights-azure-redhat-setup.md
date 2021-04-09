---
title: コンテナー分析情報を使用して Azure Red Hat OpenShift v3.x を構成する | Microsoft Docs
description: この記事では、Azure Red Hat OpenShift バージョン 3 以降でホストされている Azure Monitor を使用して Kubernetes クラスターの監視を構成する方法を説明します。
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: b46dfda0bdb0f3b582aa751786187a4d74524f75
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708375"
---
# <a name="configure-azure-red-hat-openshift-v3-with-container-insights"></a>コンテナー分析情報を使用して Azure Red Hat OpenShift v3 を構成する

>[!IMPORTANT]
> Azure Red Hat OpenShift 3.11 は、2022 年 6 月に廃止されます。
>
> 2020 年 10 月の時点で、新しい 3.11 クラスターを作成することはできなくなります。
> 既存の 3.11 クラスターは、2022 年 6 月までは引き続き動作しますが、その日以降はサポートされなくなります。
>
> このガイドに従って、[Azure Red Hat OpenShift 4 クラスターを作成](../../openshift/tutorial-create-cluster.md)します。
> ご質問がある場合は、[お問い合わせください](mailto:aro-feedback@microsoft.com)。

コンテナー分析情報は、Azure Kubernetes Service (AKS) と AKS エンジン クラスターに対する充実した監視エクスペリエンスを提供します。 この記事では、[Azure Red Hat OpenShift](../../openshift/intro-openshift.md) バージョン 3 およびバージョン 3 のサポートされている最新バージョンでホストされている Kubernetes クラスターの監視を有効にし、類似した監視エクスペリエンスを実現する方法について説明します。

>[!NOTE]
>現時点では、Azure Red Hat OpenShift のサポートはパブリック プレビューの機能です。
>

次のサポートされている方法を使用して、新規または 1 つ以上の既存の Azure Red Hat OpenShift のデプロイに対してコンテナー分析情報を有効にできます。

- Azure portal または Azure Resource Manager テンプレートを使用した既存のクラスター向け。
- Azure Resource Manager テンプレートを使用している新しいクラスター向け、または [Azure CLI](/cli/azure/openshift#az-openshift-create) を使用して新しいクラスターを作成する場合。

## <a name="supported-and-unsupported-features"></a>サポートされている機能とサポートされていない機能

コンテナー分析情報では、次の機能を除き、[概要](container-insights-overview.md)に関する記事で説明されている通り Azure Red Hat OpenShift の監視をサポートしています。

- ライブ データ (プレビュー)
- クラスターノードとポッドから[メトリックを収集](container-insights-update-metrics.md)し、Azure Monitor メトリック データベースに格納する

## <a name="prerequisites"></a>前提条件

- [Log Analytics ワークスペース。](../logs/design-logs-deployment.md)

    Container insights では、Azure の[リージョン別の製品](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)に関するページに一覧表示されているリージョンの Log Analytics ワークスペースがサポートされます。 ワークスペースは、[Azure Resource Manager](../logs/resource-manager-workspace.md)、[PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)、[Azure portal](../logs/quick-create-workspace.md) のいずれかを使用して作成できます。

- コンテナー分析情報の機能を有効にしてアクセスするには、少なくとも、Azure サブスクリプションの Azure "*共同作成者*" ロールのメンバー、およびコンテナー分析情報で構成されている Log Analytics ワークスペースの "[*Log Analytics 共同作成者*](../logs/manage-access.md#manage-access-using-azure-permissions)" ロールのメンバーである必要があります。

- 監視データを表示するには、コンテナー分析情報で構成されている Log Analytics ワークスペースの "[*Log Analytics 閲覧者*](../logs/manage-access.md#manage-access-using-azure-permissions)" ロールのアクセス権限を持つメンバーであることを確認します。

## <a name="identify-your-log-analytics-workspace-id"></a>Log Analytics ワークスペース ID を識別する

 既存の Log Analytics ワークスペースと統合するには、まず、Log Analytics ワークスペースの完全なリソース ID を特定します。 Azure Resource Manager テンプレート メソッドを使用して監視を有効にする場合、パラメーター `workspaceResourceId` にはワークスペースのリソース ID が必要です。

1. 次のコマンドを実行して、アクセス権のあるすべてのサブスクリプションを一覧表示します。

    ```azurecli
    az account list --all -o table
    ```

    出力は次のようになります。

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. **SubscriptionId** の値をコピーします。

1. 次のコマンドを実行して、Log Analytics ワークスペースをホストするサブスクリプションに切り替えます。

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. 次のコマンドを実行して、サブスクリプション内のワークスペースのリストを既定の JSON 形式で表示します。

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. 出力でワークスペース名を探し、その Log Analytics ワークスペースについてフィールド **ID** にある完全なリソース ID をコピーします。

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用している新しいクラスターで有効にする

監視が有効になっている Azure Red Hat OpenShift クラスターをデプロイするには、次の手順を実行します。 先に進む前に、チュートリアル「[Azure Red Hat OpenShift クラスターを作成する](../../openshift/tutorial-create-cluster.md)」を参照して、環境が正しく設定されるよう、構成する必要がある依存関係を理解してください。

この方法には、2 つの JSON テンプレートが含まれます。 1 つのテンプレートでは監視が有効になっているクラスターをデプロイする構成が指定され、もう 1 つのテンプレートには、次を指定するために構成するパラメーター値が含まれています。

- Azure Red Hat OpenShift クラスター リソース ID

- クラスターが展開されているリソース グループ。

- 作成手順を実行した後にメモされた、または既存の [Azure Active Directory テナント ID](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant)。

- 作成手順を実行した後にメモされた、または既存の [Azure Active Directory クライアント アプリケーション ID](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration)。

- 作成手順を実行した後にメモされた、または既存の [Azure Active Directory クライアント シークレット](../../openshift/howto-aad-app-configuration.md#create-a-client-secret)。

- 作成手順を実行した後にメモされた、または既存の [Azure AD セキュリティ グループ](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group)。

- 既存の Log Analytics ワークスペースのリソース ID。 この情報を取得する方法については、「[Log Analytics ワークスペース ID を識別する](#identify-your-log-analytics-workspace-id)」を参照してください。

- クラスターで作成するマスター ノードの数。

- エージェント プール プロファイル内の計算ノードの数。

- エージェント プール プロファイル内のインフラストラクチャ ノードの数。

テンプレートを使用するリソースのデプロイの概念について馴染みがない場合は、以下を参照してください。

- [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../../azure-resource-manager/templates/deploy-powershell.md)

- [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../../azure-resource-manager/templates/deploy-cli.md)

Azure CLI を使用する場合は、まず、ローカルに CLI をインストールして使用する必要があります。 Azure CLI バージョン 2.0.65 以降を実行している必要があります。 ご利用のバージョンを識別するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

1. 次のコマンドを使用して、監視アドオンが追加されたクラスターを作成するには、Azure Resource Manager テンプレートおよびパラメーター ファイルをダウンロードしてローカル フォルダーに保存します。

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Azure へのサインイン

    ```azurecli
    az login
    ```

    複数のサブスクリプションへのアクセス権がある場合は、`az account set -s {subscription ID}` を実行して、`{subscription ID}` を、使用するサブスクリプションに置き換えます。

3. クラスターのリソース グループをまだ作成していない場合は、作成します。 Azure 上の OpenShift がサポートされる Azure リージョンの一覧については、[サポートされるリージョン](../../openshift/supported-resources.md#azure-regions)に関する記事を参照してください。

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. JSON パラメーター ファイル **newClusterWithMonitoringParam.json** を編集し、次の値を更新します。

    - *location*
    - *clusterName*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. 次の手順では、Azure CLI を使用して、監視が有効になっているクラスターをデプロイします。

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    出力結果は、以下のようになります。

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>既存のクラスターでの有効化

Azure にデプロイされた Azure Red Hat OpenShift クラスターの監視を有効にするには、次の手順を実行します。 これは、Azure portal から、または提供されているテンプレートを使用して行うことができます。

### <a name="from-the-azure-portal"></a>Azure portal から

1. [Azure portal](https://portal.azure.com) にサインインします。

2. Azure portal メニュー上または **[ホーム]** ページから、[Azure Monitor] を選択します。 **[分析情報]** セクションで **[コンテナー]** を選択します。

3. **[モニター - コンテナー]** ページで、 **[Non-monitored clusters] (監視対象外のクラスター)** を選択します。

4. 監視対象外のクラスターの一覧でクラスターを検索し、 **[有効にする]** をクリックします。 一覧の結果を確認するには、 **[クラスターの種類]** 列から **ARO** を探します。

5. **[Onboarding to Container insights]\(コンテナー分析情報のオンボード\)** ページにクラスターと同じサブスクリプションの既存の Log Analytics ワークスペースが存在する場合は、ドロップダウン リストからそれを選択します。  
    このリストでは、サブスクリプションでクラスターがデプロイされている既定のワークスペースと場所が事前に選択されています。

    ![監視対象外のクラスターの監視を有効にする](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >クラスターからの監視データを格納するための新しい Log Analytics ワークスペースを作成する場合は、「[Log Analytics ワークスペースの作成](../logs/quick-create-workspace.md)」の手順に従います。 必ず、RedHat OpenShift クラスターがデプロイされるのと同じサブスクリプションでワークスペースを作成してください。

監視を有効にした後、クラスターの正常性メトリックが表示されるまで、約 15 分かかる場合があります。

### <a name="enable-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して有効にする

この方法には、2 つの JSON テンプレートが含まれます。 1 つのテンプレートでは監視を有効にする構成が指定され、もう 1 つのテンプレートには、次を指定するために構成するパラメーター値が含まれています。

- Azure Red Hat OpenShift クラスター リソース ID。

- クラスターが展開されているリソース グループ。

- Log Analytics ワークスペース。 この情報を取得する方法については、「[Log Analytics ワークスペース ID を識別する](#identify-your-log-analytics-workspace-id)」を参照してください。

テンプレートを使用するリソースのデプロイの概念について馴染みがない場合は、以下を参照してください。

- [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../../azure-resource-manager/templates/deploy-powershell.md)

- [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../../azure-resource-manager/templates/deploy-cli.md)

Azure CLI を使用する場合は、まず、ローカルに CLI をインストールして使用する必要があります。 Azure CLI バージョン 2.0.65 以降を実行している必要があります。 ご利用のバージョンを識別するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

1. 次のコマンドを使用して、監視アドオンでクラスターを更新するためのテンプレートとパラメーター ファイルをダウンロードします。

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Azure へのサインイン

    ```azurecli
    az login
    ```

    複数のサブスクリプションへのアクセス権がある場合は、`az account set -s {subscription ID}` を実行して、`{subscription ID}` を、使用するサブスクリプションに置き換えます。

3. Azure RedHat OpenShift クラスターのサブスクリプションを指定します。

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. 次のコマンドを実行して、クラスターの場所とリソース ID を指定します。

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. JSON パラメーター ファイル **existingClusterParam.json** を編集し、*aroResourceId* および *aroResourceLocation* の値を更新します。 **workspaceResourceId** の値は、ワークスペース名を含む Log Analytics ワークスペースの完全なリソース ID です。

6. Azure CLI を使用してデプロイするには、次のコマンドを実行します。

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    出力結果は、以下のようになります。

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>次のステップ

- RedHat OpenShift クラスターと実行中のワークロードの正常性とリソース使用率を収集するための監視を有効にしたうえで、コンテナー分析情報を[使用する方法](container-insights-analyze.md)について学習します。

- 既定では、コンテナー化されたエージェントによって、kube-system を除くすべての名前空間で実行されているすべてのコンテナーの stdout および stderr コンテナー ログが収集されます。 特定の名前空間に固有のコンテナー ログ収集を構成するには、「[コンテナーの Azure Monitor に対するエージェントのデータ収集を構成する](container-insights-agent-config.md)」を参照して、ConfigMap 構成ファイルに必要なデータ収集設定を構成します。

- クラスターから Prometheus メトリックをスクレイピングして分析するには、「[Azure Monitor for containers で Prometheus メトリックのスクレーピングを構成する](container-insights-prometheus-integration.md)」を参照してください。

- コンテナー分析情報によるクラスターの監視を停止する方法については、「[お使いの Azure Red Hat OpenShift クラスターの監視を停止する方法](./container-insights-optout-openshift-v3.md)」を参照してください。