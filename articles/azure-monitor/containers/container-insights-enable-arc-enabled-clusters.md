---
title: Container insights を使用して Azure Arc 対応 Kubernetes クラスターを構成する | Microsoft Docs
description: この記事では、Container insights を使用して Azure Arc 対応 Kubernetes クラスターの監視を構成する方法について説明します。
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 307f9d9928042410dc9b4443aba5c019c592980c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711299"
---
# <a name="enable-monitoring-of-azure-arc-enabled-kubernetes-cluster"></a>Azure Arc 対応 Kubernetes クラスターの監視を有効にする

Container insights によって、Azure Kubernetes Service (AKS) と AKS エンジン クラスターに対する充実した監視エクスペリエンスが提供されます。 この記事では、Azure の外部でホストしている Azure Arc 対応 Kubernetes クラスターの監視を有効にし、Azure と同等の監視エクスペリエンスを実現する方法について説明します。

PowerShell または Bash スクリプトを使用して、1 つ以上の既存の Kubernetes のデプロイに対して Container insights を有効にできます。

## <a name="supported-configurations"></a>サポートされている構成

Container insights では、次の機能を除き、[概要](container-insights-overview.md)に関する記事で説明されている、Azure Arc 対応 Kubernetes の監視 (プレビュー) がサポートされています。

- ライブ データ (プレビュー)

Container insights では、以下が正式にサポートされています。

- Kubernetes のバージョンとサポート ポリシーについては、[AKS でサポートされているバージョン](../../aks/supported-kubernetes-versions.md)と同じです。

- 次のコンテナー ランタイムがサポートされています。Docker、Moby、および CRI と互換性のあるランタイム (CRI-O、など)。

- サポートされているマスターおよびワーカー ノードの Linux OS リリースは次のとおりです。Ubuntu (18.04 LTS と 16.04 LTS)。

## <a name="prerequisites"></a>前提条件

始める前に、必ず以下のものを用意してください。

- Log Analytics ワークスペース。

    Container insights では、Azure の[リージョン別の製品](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)に関するページに一覧表示されているリージョンの Log Analytics ワークスペースがサポートされます。 ワークスペースは、[Azure Resource Manager](../logs/resource-manager-workspace.md)、[PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)、[Azure portal](../logs/quick-create-workspace.md) のいずれかを使用して作成できます。

- Container insights を有効にしてその機能にアクセスするには、少なくとも Azure サブスクリプションの Azure "*共同作成者*" ロールのメンバー、および Container insights を使用して構成されている Log Analytics ワークスペースの "[*Log Analytics 共同作成者*](../logs/manage-access.md#manage-access-using-azure-permissions)" ロールのメンバーである必要があります。

- Azure Arc クラスター リソースの [[共同作成者]](../../role-based-access-control/built-in-roles.md#contributor) ロールのメンバーである必要があります。

- 監視データを表示するには、Container insights を使用して構成されている Log Analytics ワークスペースの "[*Log Analytics 閲覧者*](../logs/manage-access.md#manage-access-using-azure-permissions)" ロールのアクセス許可を持つメンバーである必要があります。

- [HELM クライアント](https://helm.sh/docs/using_helm/)。特定の Kubernetes クラスターに Container insights のグラフをオンボードするために使用されます。

- コンテナー化されたバージョンの Linux 用 Log Analytics エージェントが Azure Monitor と通信するためには、プロキシとファイアウォールに関する次の構成情報が必要です。

    |エージェントのリソース|Port |
    |------|---------|
    |`*.ods.opinsights.azure.com` |ポート 443 |
    |`*.oms.opinsights.azure.com` |ポート 443 |
    |`*.dc.services.visualstudio.com` |ポート 443 |

- コンテナー化されたエージェントがパフォーマンスに関するメトリックを収集できるように、クラスター内のすべてのノードで Kubelet の `cAdvisor secure port: 10250` または `unsecure port :10255` を開いておく必要があります。 Kubelet の cAdvisor がまだ構成されていない場合は、`secure port: 10250` を構成することをお勧めします。

- コンテナー化されたエージェントがクラスター内の Kubernetes API サービスとやり取りしてインベントリ データを収集するために、コンテナーに `KUBERNETES_SERVICE_HOST` と `KUBERNETES_PORT_443_TCP_PORT` の 2 つの環境変数を指定する必要があります。

    >[!IMPORTANT]
    >Azure Arc 対応 Kubernetes クラスターの監視は、ciprod04162020 以降のバージョンのエージェントに限りサポートされます。

- Powershell のスクリプト化された方法を使用して監視を有効にする場合は、[PowerShell Core](/powershell/scripting/install/installing-powershell?view=powershell-6&preserve-view=true) が必要です。

- Bash のスクリプト化された方法を使用して監視を有効にする場合は、[Bash バージョン 4](https://www.gnu.org/software/bash/) が必要です。

## <a name="identify-workspace-resource-id"></a>ワークスペースのリソース ID の特定

先ほどダウンロードした PowerShell または Bash スクリプトを使用してクラスターの監視を有効にし、既存の Log Analytics ワークスペースと統合するには、次の手順を実行して、最初に Log Analytics ワークスペースの完全なリソース ID を特定します。 これは、コマンドを実行して、指定されたワークスペースに対して監視アドオンを有効にするとき、`workspaceResourceId` パラメーターに必要です。 指定するワークスペースがない場合は、`workspaceResourceId` パラメーターを含めてスキップし、スクリプトを使用して新しいワークスペースの作成を自動実行します。

1. 次のコマンドを使用して、アクセス権のあるすべてのサブスクリプションを一覧表示します。

    ```azurecli
    az account list --all -o table
    ```

    出力は次のようになります。

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    **SubscriptionId** の値をコピーします。

2. 次のコマンドを使用して、Log Analytics ワークスペースをホストしているサブスクリプションに切り替えます。

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. 次の例では、既定の JSON 形式で、サブスクリプション内のワークスペースの一覧が表示されます。

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    出力でワークスペース名を探し、その Log Analytics ワークスペースについてフィールド **ID** にある完全なリソース ID をコピーします。

## <a name="enable-monitoring-using-powershell"></a>PowerShell を使用して監視を有効にする

1. 次のコマンドを使用し、監視アドオンを使用してクラスターを構成するスクリプトをローカル フォルダーにダウンロードし、保存します。

    ```powershell
    Invoke-WebRequest https://aka.ms/enable-monitoring-powershell-script -OutFile enable-monitoring.ps1
    ```

2. Azure Arc 対応 Kubernetes クラスター リソースのリソース ID を表す `subscriptionId`、`resourceGroupName`、`clusterName` の対応する値を設定することによって、`$azureArcClusterResourceId` 変数を構成します。

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. コマンド `kubectl config get-contexts` を実行することによって、クラスターの **kube-context** で `$kubeContext` 変数を構成します。 現在のコンテキストを使用する場合は、この値を `""` に設定します。

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. 既存の Azure Monitor Log Analytics ワークスペースを使用する場合は、ワークスペースのリソース ID を表す対応する値を使用して `$logAnalyticsWorkspaceResourceId` 変数を構成します。 そうでない場合、変数を `""` に設定すると、リージョンにワークスペースがまだ存在しない場合、クラスター サブスクリプションの既定のリソース グループにスクリプトが既定のワークスペースを作成します。 作成される既定のワークスペースは、*DefaultWorkspace-\<SubscriptionID>-\<Region>* のような形式になります。

    ```powershell
    $logAnalyticsWorkspaceResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Arc 対応 Kubernetes クラスターがプロキシ サーバー経由で通信する場合は、プロキシ サーバーの URL を使用して変数 `$proxyEndpoint` を構成します。 クラスターがプロキシ サーバー経由で通信しない場合は、値を `""` に設定します。  詳細については、この記事の後半にある「[プロキシ エンドポイントを構成する](#configure-proxy-endpoint)」を参照してください。

6. 次のコマンドを実行して、監視を有効にします。

    ```powershell
    .\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
    ```

監視を有効にした後、クラスターの正常性メトリックが表示されるまで、約 15 分かかる場合があります。

### <a name="using-service-principal"></a>サービス プリンシパルを使用する
スクリプト *enable-monitoring.ps1* では、対話型デバイス ログインが使用されます。 非対話型のログインを望む場合、既存のサービス プリンシパルを使用するか、「[前提条件](#prerequisites)」の説明にある必須のアクセス許可が与えられたサービス プリンシパルを新規作成できます。 サービス プリンシパルを使用するには、$servicePrincipalClientId、$servicePrincipalClientSecret、$tenantId パラメーターを、*enable-monitoring.ps1* スクリプトに使用するサービス プリンシパルの値と共に渡す必要があります。

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"
```

以下のロールの割り当ては、Arc K8s Connected Cluster リソース以外の Azure サブスクリプションで既存の Log Analytics ワークスペースを使用している場合にのみ該当します。

```powershell
$logAnalyticsWorkspaceResourceId = "<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
New-AzRoleAssignment -RoleDefinitionName 'Log Analytics Contributor'  -ObjectId $servicePrincipal.Id -Scope  $logAnalyticsWorkspaceResourceId

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

次に例を示します。

```powershell
.\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
```



## <a name="enable-using-bash-script"></a>Bash スクリプトの使用を有効にする

提供された Bash スクリプトを使用して監視を有効にするには、次の手順を実行します。

1. 次のコマンドを使用し、監視アドオンを使用してクラスターを構成するスクリプトをローカル フォルダーにダウンロードし、保存します。

    ```bash
    curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script
    ```

2. Azure Arc 対応 Kubernetes クラスター リソースのリソース ID を表す `subscriptionId`、`resourceGroupName`、`clusterName` の対応する値を設定することによって、`azureArcClusterResourceId` 変数を構成します。

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. コマンド `kubectl config get-contexts` を実行することによって、クラスターの **kube-context** で `kubeContext` 変数を構成します。 現在のコンテキストを使用する場合は、この値を `""` に設定します。

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. 既存の Azure Monitor Log Analytics ワークスペースを使用する場合は、ワークスペースのリソース ID を表す対応する値を使用して `logAnalyticsWorkspaceResourceId` 変数を構成します。 そうでない場合、変数を `""` に設定すると、リージョンにワークスペースがまだ存在しない場合、クラスター サブスクリプションの既定のリソース グループにスクリプトが既定のワークスペースを作成します。 作成される既定のワークスペースは、*DefaultWorkspace-\<SubscriptionID>-\<Region>* のような形式になります。

    ```bash
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Arc 対応 Kubernetes クラスターがプロキシ サーバー経由で通信する場合は、プロキシ サーバーの URL を使用して変数 `proxyEndpoint` を構成します。 クラスターがプロキシ サーバー経由で通信しない場合は、値を `""` に設定します。 詳細については、この記事の後半にある「[プロキシ エンドポイントを構成する](#configure-proxy-endpoint)」を参照してください。

6. クラスターの監視を有効にするには、デプロイ シナリオに応じてさまざまなコマンドが用意されています。

    次のコマンドを実行し、既定のオプションで監視を有効にします。たとえば、現在の kube コンテキストの使用、既定の Log Analytics ワークスペースの作成、プロキシ サーバーを指定しないなどです。

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    次のコマンドを実行して、既定の Log Analytics ワークスペースを作成し、プロキシ サーバーは指定しません。

    ```bash
   bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

    次のコマンドを実行して、既存の Log Analytics ワークスペースを使用し、プロキシ サーバーは指定しません。

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId
    ```

    次のコマンドを実行して、既存の Log Analytics ワークスペースを使用し、プロキシ サーバーを指定します。

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
    ```

監視を有効にした後、クラスターの正常性メトリックが表示されるまで、約 15 分かかる場合があります。

### <a name="using-service-principal"></a>サービス プリンシパルを使用する
Bash スクリプト *enable-monitoring.sh* では、対話型デバイス ログインが使用されます。 非対話型のログインを望む場合、既存のサービス プリンシパルを使用するか、「[前提条件](#prerequisites)」の説明にある必須のアクセス許可が与えられたサービス プリンシパルを新規作成できます。 サービス プリンシパルを使用するには、*enable-monitoring.sh* Bash スクリプトに使用するサービス プリンシパルの --client-id、--client-secret、--tenant-id 値を渡す必要があります。

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')
```

以下のロールの割り当ては、Arc K8s Connected Cluster リソース以外の Azure サブスクリプションで既存の Log Analytics ワークスペースを使用している場合にのみ該当します。

```bash
logAnalyticsWorkspaceResourceId="<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
az role assignment create --role 'Log Analytics Contributor' --assignee $servicePrincipalClientId --scope $logAnalyticsWorkspaceResourceId

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

次に例を示します。

```bash
bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
```

## <a name="configure-proxy-endpoint"></a>プロキシ エンドポイントを構成する

Container insights のコンテナー化されたエージェントを使用して、プロキシ サーバー経由で通信できるようにプロキシ エンドポイントを構成できます。 コンテナー化されたエージェントと Azure Monitor 間の通信には HTTP または HTTPS プロキシ サーバーを使用でき、匿名と基本認証 (ユーザー名/パスワード) の両方がサポートされます。

プロキシ構成の値には次の構文があります。`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>プロキシ サーバーで認証が必要ない場合でも、疑似ユーザー名/パスワードを指定する必要があります。 これは、どのようなユーザー名とパスワードでもかまいません。

|プロパティ| 説明 |
|--------|-------------|
|Protocol | http または https |
|user | プロキシ認証のオプションのユーザー名 |
|password | プロキシ認証のオプションのパスワード |
|proxyhost | プロキシ サーバーのアドレスまたは FQDN |
|port | プロキシ サーバーのオプションのポート番号 |

例: `http://user01:password@proxy01.contoso.com:3128`

プロトコルを **http** として指定すると、SSL/TLS のセキュリティで保護された接続を使用して HTTP 要求が作成されます。 プロキシ サーバーは、SSL/TLS プロトコルをサポートしている必要があります。

### <a name="configure-using-powershell"></a>PowerShell を使用した構成

プロキシ サーバーのユーザー名とパスワード、IP アドレスまたは FQDN、およびポート番号を指定します。 次に例を示します。

```powershell
$proxyEndpoint = https://<user>:<password>@<proxyhost>:<port>
```

### <a name="configure-using-bash"></a>Bash を使用して構成する

プロキシ サーバーのユーザー名とパスワード、IP アドレスまたは FQDN、およびポート番号を指定します。 次に例を示します。

```bash
export proxyEndpoint=https://<user>:<password>@<proxyhost>:<port>
```

## <a name="next-steps"></a>次のステップ

- Arc 対応 Kubernetes クラスターとそこで実行されるワークロードの正常性とリソース使用率を収集するための監視を有効にしたので、Container insights の[使い方](container-insights-analyze.md)を確認します。

- 既定では、コンテナー化されたエージェントによって、kube-system を除くすべての名前空間で実行されているすべてのコンテナーの stdout および stderr コンテナー ログが収集されます。 特定の名前空間に固有のコンテナー ログ収集を構成するには、「[コンテナーの Azure Monitor に対するエージェントのデータ収集を構成する](container-insights-agent-config.md)」を参照して、ConfigMap 構成ファイルに必要なデータ収集設定を構成します。

- クラスターから Prometheus メトリックをスクレイピングして分析するには、「[Azure Monitor for containers で Prometheus メトリックのスクレーピングを構成する](container-insights-prometheus-integration.md)」を参照してください。

- Container insights を使用した Arc 対応 Kubernetes クラスターの監視を停止する方法については、「[お使いのハイブリッド クラスターの監視を停止する方法](container-insights-optout-hybrid.md#how-to-stop-monitoring-on-arc-enabled-kubernetes)」を参照してください。