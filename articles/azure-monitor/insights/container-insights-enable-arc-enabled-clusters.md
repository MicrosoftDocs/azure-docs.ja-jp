---
title: Azure Arc 対応 Kubernetes クラスターに Azure Monitor for containers を構成する | Microsoft Docs
description: この記事では、Azure Monitor for containers を使用して Azure Arc 対応 Kubernetes クラスターの監視を構成する方法を説明します。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: f8002b20f37ca5149c58ca3e29402916ebbc1333
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092883"
---
# <a name="enable-monitoring-of-azure-arc-enabled-kubernetes-cluster"></a>Azure Arc 対応 Kubernetes クラスターの監視を有効にする

Azure Monitor for containers は、Azure Kubernetes Service (AKS) と AKS エンジン クラスター用の監視エクスペリエンスを提供するものです。 この記事では、Azure の外部でホストしている Azure Arc 対応 Kubernetes クラスターの監視を有効にし、Azure と同等の監視エクスペリエンスを実現する方法について説明します。

PowerShell または Bash スクリプトを使用して、1 つ以上の既存の Kubernetes のデプロイに対してコンテナー用の Azure Monitor 有効にできます。

## <a name="supported-configurations"></a>サポートされている構成

Azure Monitor for containers では、次の機能を除き、[概要](container-insights-overview.md)記事で説明されているとおり Azure Arc 対応 Kubernetes (プレビュー) の監視をサポートしています。

- ライブ データ (プレビュー)

- クラスターノードとポッドから[メトリックを収集](container-insights-update-metrics.md)し、Azure Monitor メトリック データベースに格納する

Azure Monitor for containers では、以下が公式にサポートされています。

- Kubernetes のバージョンとサポート ポリシーについては、[AKS でサポートされているバージョン](../../aks/supported-kubernetes-versions.md)と同じです。

- 次のコンテナー ランタイムがサポートされています。Docker、Moby、および CRI と互換性のあるランタイム (CRI-O、など)。

- サポートされているマスターおよびワーカー ノードの Linux OS リリースは次のとおりです。Ubuntu (18.04 LTS と 16.04 LTS)。

## <a name="prerequisites"></a>前提条件

始める前に、必ず以下のものを用意してください。

- Log Analytics ワークスペース。

    Azure Monitor for containers では、Azure の[リージョン別の製品](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)に関するページに一覧表示されているリージョンの Log Analytics ワークスペースがサポートされます。 ワークスペースは、[Azure Resource Manager](../platform/template-workspace-configuration.md)、[PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)、[Azure portal](../learn/quick-create-workspace.md) のいずれかを使用して作成できます。

- Azure Monitor for containers の機能を有効にしてアクセスするには、少なくとも、Azure サブスクリプションの Azure *共同作成者* ロールのメンバー、および Azure Monitor for containers で構成されている Log Analytics ワークスペースの [*Log Analytics 共同作成者*](../platform/manage-access.md#manage-access-using-azure-permissions)のメンバーである必要があります。

- Azure Arc クラスター リソースの [[共同作成者]](../../role-based-access-control/built-in-roles.md#contributor) ロールのメンバーである必要があります。

- 監視データを表示するには、Azure Monitor for containers で構成されている Log Analytics ワークスペースの [*Log Analytics 閲覧者*](../platform/manage-access.md#manage-access-using-azure-permissions) ロールのアクセス権限を持つメンバーであることを確認します。

- [HELM クライアント](https://helm.sh/docs/using_helm/)。これは、特定の Kubernetes クラスターに Azure Monitor for containers のチャートをオンボードするために使用します。

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

- Powershell のスクリプト化された方法を使用して監視を有効にする場合は、[PowerShell Core](/powershell/scripting/install/installing-powershell?view=powershell-6) が必要です。

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
    wget https://aka.ms/enable-monitoring-powershell-script -outfile enable-monitoring.ps1
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
    $logAnalyticsWorkspaceResourceId = “/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>”
    ```

5. Arc 対応 Kubernetes クラスターがプロキシ サーバー経由で通信する場合は、プロキシ サーバーの URL を使用して変数 `$proxyEndpoint` を構成します。 クラスターがプロキシ サーバー経由で通信しない場合は、値を `""` に設定します。  詳細については、この記事の後半にある「[プロキシ エンドポイントを構成する](#configure-proxy-endpoint)」を参照してください。

6. 次のコマンドを実行して、監視を有効にします。

    ```powershell
    .\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
    ```

監視を有効にした後、クラスターの正常性メトリックが表示されるまで、約 15 分かかる場合があります。

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
    export logAnalyticsWorkspaceResourceId=“/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>”
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

## <a name="configure-proxy-endpoint"></a>プロキシ エンドポイントを構成する

Azure Monitor for containers のコンテナー化されたエージェントを使用して、プロキシ サーバー経由で通信できるようにプロキシ エンドポイントを構成できます。 コンテナー化されたエージェントと Azure Monitor 間の通信には HTTP または HTTPS プロキシ サーバーを使用でき、匿名と基本認証 (ユーザー名/パスワード) の両方がサポートされます。

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

- Arc 対応 Kubernetes クラスターとそこで実行中のワークロードの正常性とリソース使用率を収集するための監視を有効にしたので、Azure Monitor for containers の[使い方](container-insights-analyze.md)について学習します。

- 既定では、コンテナー化されたエージェントによって、kube-system を除くすべての名前空間で実行されているすべてのコンテナーの stdout および stderr コンテナー ログが収集されます。 特定の名前空間に固有のコンテナー ログ収集を構成するには、「[コンテナーの Azure Monitor に対するエージェントのデータ収集を構成する](container-insights-agent-config.md)」を参照して、ConfigMap 構成ファイルに必要なデータ収集設定を構成します。

- クラスターから Prometheus メトリックをスクレイピングして分析するには、「[Azure Monitor for containers で Prometheus メトリックのスクレーピングを構成する](container-insights-prometheus-integration.md)」を参照してください。

- Azure Monitor for containers で Arc 対応 Kubernetes クラスターの監視を停止する方法については、「[お使いのハイブリッド クラスターの監視を停止する方法](container-insights-optout-hybrid.md#how-to-stop-monitoring-on-arc-enabled-kubernetes)」を参照してください。
