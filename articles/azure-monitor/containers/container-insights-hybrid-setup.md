---
title: Container insights を使用してハイブリッド Kubernetes クラスターを構成する | Microsoft Docs
description: この記事では、Azure Stack またはその他の環境でホストされている Kubernetes クラスターを監視するように Container insights を構成する方法について説明します。
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: d2692b4a634d60ef62339f68277591d711260712
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711248"
---
# <a name="configure-hybrid-kubernetes-clusters-with-container-insights"></a>Container insights を使用してハイブリッド Kubernetes クラスターを構成する

Container insights は、Azure Kubernetes Service (AKS) と、Azure でホストされている自己管理型 Kubernetes クラスターである [Azure 上の AKS エンジン](https://github.com/Azure/aks-engine)用の監視エクスペリエンスを提供するものです。 この記事では、Azure の外部でホストしている Kubernetes クラスターの監視を有効にし、Azure と同等の監視エクスペリエンスを実現する方法について説明します。

## <a name="supported-configurations"></a>サポートされている構成

次の構成は、Container insights で正式にサポートされています。 Kubernetes とオペレーティング システムのバージョンが異なる場合は、askcoin@microsoft.com 宛にメールを送信してください。

- 環境:

    - オンプレミスの Kubernetes
    - Azure と Azure Stack 上の AKS エンジン。 詳細については、[Azure Stack 上の AKS エンジン](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)に関するページを参照してください
    - [OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) バージョン 4 以降、オンプレミスまたはその他のクラウド環境。

- Kubernetes のバージョンとサポート ポリシーについては、[AKS でサポートされているバージョン](../../aks/supported-kubernetes-versions.md)と同じです。

- 次のコンテナー ランタイムがサポートされています。Docker、Moby、および CRI と互換性のあるランタイム (CRI-O、など)。

- サポートされているマスターおよびワーカー ノードの Linux OS リリースは次のとおりです。Ubuntu (18.04 LTS と 16.04 LTS)、および Red Hat Enterprise Linux CoreOS 43.81。

- サポートされているアクセス制御:Kubernetes の RBAC と非 RBAC

## <a name="prerequisites"></a>前提条件

始める前に、必ず以下のものを用意してください。

- [Log Analytics ワークスペース。](../logs/design-logs-deployment.md)

    Container insights では、Azure の[リージョン別の製品](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)に関するページに一覧表示されているリージョンの Log Analytics ワークスペースがサポートされます。 ワークスペースは、[Azure Resource Manager](../logs/resource-manager-workspace.md)、[PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)、[Azure portal](../logs/quick-create-workspace.md) のいずれかを使用して作成できます。

    >[!NOTE]
    >同一の Log Analytics ワークスペースに名称が同じクラスターが複数ある場合には、そのワークスペースに対してクラスターの監視を有効にすることはできません。 クラスター名は一意になっている必要があります。
    >

- コンテナーの監視を有効にするために、**Log Analytics 共同作成者ロール** のメンバーである必要があります。 Log Analytics ワークスペースへのアクセスを制御する方法の詳細については、[ワークスペースとログ データに対するアクセスの管理](../logs/manage-access.md)に関するページを参照してください。

- 監視データを表示するには、Container insightsで構成された Log Analytics ワークスペースの "[*Log Analytics 閲覧者*](../logs/manage-access.md#manage-access-using-azure-permissions)" ロールを持っている必要があります。

- [HELM クライアント](https://helm.sh/docs/using_helm/)。特定の Kubernetes クラスターに Container insights のグラフをオンボードするために使用されます。

- コンテナー化されたバージョンの Linux 用 Log Analytics エージェントが Azure Monitor と通信するためには、プロキシとファイアウォールに関する次の構成情報が必要です。

    |エージェントのリソース|Port |
    |------|---------|
    |*.ods.opinsights.azure.com |ポート 443 |
    |*.oms.opinsights.azure.com |ポート 443 |
    |*.dc.services.visualstudio.com |ポート 443 |

- コンテナー化されたエージェントがパフォーマンスに関するメトリックを収集できるように、クラスター内のすべてのノードで Kubelet の `cAdvisor secure port: 10250` または `unsecure port :10255` を開いておく必要があります。 Kubelet の cAdvisor がまだ構成されていない場合は、`secure port: 10250` を構成することをお勧めします。

- コンテナー化されたエージェントがクラスター内の Kubernetes API サービスとやり取りしてインベントリ データを収集するために、コンテナーに `KUBERNETES_SERVICE_HOST` と `KUBERNETES_PORT_443_TCP_PORT` の 2 つの環境変数を指定する必要があります。

>[!IMPORTANT]
>ハイブリッド Kubernetes クラスターの監視は、ciprod10182019 以降のバージョンのエージェントに限りサポートされます。

## <a name="enable-monitoring"></a>監視を有効にする

Hybrid Kubernetes クラスターに対して Container insights を有効にするには、次の手順を順番に実施する必要があります。

1. Log Analytics ワークスペースに Container Insights ソリューションを構成する。   

2. Log Analytics ワークスペースで Container insights HELM Chart を有効にします。

Azure Monitor での監視ソリューションの詳細については、[こちら](../../azure-monitor/insights/solutions.md)をご覧ください。

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Azure Monitor のコンテナー ソリューションを追加する方法

既製の Azure Resource Manager テンプレートを使ってソリューションをデプロイする方法には、Azure PowerShell コマンドレット `New-AzResourceGroupDeployment` を使用する方法と、Azure CLI を使用する方法の 2 種類があります。

テンプレートを使用するリソースのデプロイの概念について馴染みがない場合は、以下を参照してください。

- [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../../azure-resource-manager/templates/deploy-powershell.md)

- [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../../azure-resource-manager/templates/deploy-cli.md)

Azure CLI を使用する場合は、まず、ローカルに CLI をインストールして使用する必要があります。 Azure CLI バージョン 2.0.59 以降を実行している必要があります。 ご利用のバージョンを識別するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

この方法には、2 つの JSON テンプレートが含まれます。 1 つのテンプレートでは監視を有効にする構成が指定され、もう 1 つのテンプレートには、次を指定するために構成するパラメーター値が含まれています。

- **workspaceResourceId** - Log Analytics ワークスペースの完全なリソース ID です。
- **workspaceRegion** - ワークスペースの作成先となるリージョンです (Azure portal から確認したときには、ワークスペースの **[場所]** というプロパティとして表示されるものです)。

まずは、Log Analytics ワークスペースの完全なリソース ID を特定します。これは **containerSolutionParams.json** ファイル内の `workspaceResourceId` パラメーターの値として必要になる情報で、次の手順に従って確認します。リソース ID が特定できたら、PowerShell コマンドレットまたは Azure CLI コマンドを実行し、ソリューションを追加します。

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

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    出力でワークスペース名を探し、その Log Analytics ワークスペースについてフィールド **ID** にある完全なリソース ID をコピーします。

4. 以下の JSON 構文をコピーして、ファイルに貼り付けます。

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
   }
    ```

5. このファイルを containerSolution.json という名前でローカル フォルダーに保存します。

6. 以下の JSON 構文をファイルに貼り付けます。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. 値を編集します。**workspaceResourceId** には手順 3 でコピーした値を使用し、**workspaceRegion** には Azure CLI コマンド [az monitor log-analytics workspace show](/cli/azure/monitor/log-analytics/workspace#az-monitor-log-analytics-workspace-list&preserve-view=true) を実行して得られる **リージョン** の値をコピーします。

8. このファイルを containerSolutionParams.json という名前でローカル フォルダーに保存します。

9. これでこのテンプレートをデプロイする準備が整いました。

   - Azure PowerShell を使用してデプロイするには、そのテンプレートを含むフォルダーで次のコマンドを使用します。

       ```powershell
       # configure and login to the cloud of Log Analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of Log Analytics workspace>
       ```

       ```powershell
       # execute deployment command to add Container Insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of Log Analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが表示されます。

       ```powershell
       provisioningState       : Succeeded
       ```

   - Azure CLI を使用してデプロイするには、次のコマンドを実行します。

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが表示されます。

       ```azurecli
       provisioningState       : Succeeded
       ```

       監視を有効にした後、クラスターの正常性メトリックが表示されるまで、約 15 分かかる場合があります。

## <a name="install-the-helm-chart"></a>HELM Chart をインストールする

このセクションでは、Container insights のコンテナー化されたエージェントをインストールします。 次に進む前に、`omsagent.secret.wsid` パラメーターに必要なワークスペース ID と、`omsagent.secret.key` パラメーターに必要な主キーを特定する必要があります。 この情報を特定するには、次の手順を実行してから、Helm Chart を使用してエージェントをインストールするコマンドを実行します。

1. 次のコマンドを実行して、ワークスペース ID を特定します。

    `az monitor log-analytics workspace list --resource-group <resourceGroupName>`

    出力で、**name** フィールドの下のワークスペース名を見つけ、**customerID** フィールドの下にあるその Log Analytics ワークスペースのワークスペース ID をコピーします。

2. 次のコマンドを実行して、ワークスペースの主キーを特定します。

    `az monitor log-analytics workspace get-shared-keys --resource-group <resourceGroupName> --workspace-name <logAnalyticsWorkspaceName>`

    出力で、**primarySharedKey** フィールドの下の主キーを見つけ、その値をコピーします。

>[!NOTE]
>次のコマンドは、Helm バージョン 2 にのみ適用できます。 `--name` パラメーターの使用は、Helm バージョン 3 には適用されません。 

>[!NOTE]
>Kubernetes クラスターがプロキシ サーバー経由で通信する場合は、プロキシ サーバーの URL を使用して `omsagent.proxy` パラメーターを構成します。 クラスターがプロキシ サーバー経由で通信しない場合は、このパラメーターを指定する必要はありません。 詳細については、この記事の後半にある「[プロキシ エンドポイントを構成する](#configure-proxy-endpoint)」を参照してください。

3. 次のコマンドを実行して、ローカルのリストに Azure のチャート リポジトリを追加します。

    ```
    helm repo add microsoft https://microsoft.github.io/charts/repo
    ````

4. 次のコマンドを実行してチャートをインストールします。

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<my_prod_cluster> microsoft/azuremonitor-containers
    ```

    Log Analytics ワークスペースが Azure China 21Vianet にある場合には、次のコマンドを実行します。

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Log Analytics ワークスペースが Azure US Government にある場合には、次のコマンドを実行します。

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

### <a name="enable-the-helm-chart-using-the-api-model"></a>API モデルを使用して、Helm グラフを有効にする

AKS エンジン クラスター仕様の JSON ファイル (API モデルとも呼ばれます) で、アドオンを指定できます。 このアドオンでは、収集した監視データが格納される Log Analytics ワークスペースの、base64 でエンコードされたバージョンの `WorkspaceGUID` と `WorkspaceKey` を指定します。 `WorkspaceGUID` と `WorkspaceKey` は、前のセクションの手順 1 と 2 を使用して検出できます。

Azure Stack Hub クラスターに対してサポートされる API 定義については、[kubernetes-container-monitoring_existing_workspace_id_and_key.json](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json) の例をご覧ください。 具体的には、**kubernetesConfig** の **addons** プロパティを探します。

```json
"orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Id in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="configure-agent-data-collection"></a>データ収集を構成する

バージョン 1.0.0 以降のチャートでは、エージェントによるデータ収集の設定を ConfigMap からコントロールします。 エージェントによるデータ収集の設定に関する詳細については、[こちら](container-insights-agent-config.md)のドキュメントを参照してください。

チャートを正常にデプロイした後は、Container insights 内のハイブリッド Kubernetes クラスターに関するデータを Azure portal から確認できるようになります。  

>[!NOTE]
>インジェストの待ち時間 (Azure Log Analytics ワークスペースでエージェントがコミットを完了するまで) は、5 から 10 分程度です。 必要な監視データのすべてが Azure Monitor で利用できるようになるまでの間は、クラスターの状態として **[データなし]** と **[不明]** のいずれかの値が表示されます。

## <a name="configure-proxy-endpoint"></a>プロキシ エンドポイントを構成する

チャートのバージョン 2.7.1 以降、チャートでは、`omsagent.proxy` チャート パラメーターを使用したプロキシ エンドポイントの指定がサポートされます。 これにより、プロキシ サーバー経由で通信することができます。 Container insights エージェントと Azure Monitor 間の通信には HTTP または HTTPS プロキシ サーバーを使用でき、匿名と基本認証 (ユーザー名/パスワード) の両方がサポートされます。

プロキシ構成の値には、次の構文があります: `[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>プロキシ サーバーで認証が必要ない場合でも、疑似ユーザー名/パスワードを指定する必要があります。 これは、どのようなユーザー名とパスワードでもかまいません。

|プロパティ| 説明 |
|--------|-------------|
|Protocol | http または https |
|user | プロキシ認証のオプションのユーザー名 |
|password | プロキシ認証のオプションのパスワード |
|proxyhost | プロキシ サーバーのアドレスまたは FQDN |
|port | プロキシ サーバーのオプションのポート番号 |

例: `omsagent.proxy=http://user01:password@proxy01.contoso.com:8080`

プロトコルを **http** として指定すると、SSL/TLS のセキュリティで保護された接続を使用して HTTP 要求が作成されます。 プロキシ サーバーは、SSL/TLS プロトコルをサポートしている必要があります。

## <a name="troubleshooting"></a>トラブルシューティング

ハイブリッド Kubernetes クラスターの監視を有効にしようとしているときにエラーが発生した場合は、PowerShell スクリプト [TroubleshootError_nonAzureK8s.ps1](https://aka.ms/troubleshoot-non-azure-k8s) をコピーしてコンピューターのフォルダーに保存します。 このスクリプトは、発生した問題を検出して修正するために提供されています。 次の問題を修正を検出して修正を試みるように設計されています：

- 指定された Azure Monitor Log Analytics ワークスペースが有効
- Log Analytics ワークスペースは、Container insights ソリューションを使用して構成されています。 そうでなければ、ワークスペースを構成します。
- OmsAgent replicaset ポッドを実行中
- OmsAgent daemonset ポッドを実行中
- OmsAgent ヘルス サービスを実行中
- Log Analytics ワークスペース ID とコンテナー化されたエージェントで構成されたキーは、Insight が構成されているワークスペースと一致します。
- すべての Linux ワーカー ノードに `kubernetes.io/role=agent` のラベルがあることを確認して、rs ポッドをスケジュールします。 存在しなければ、追加してください。
- クラスター内のすべてのノードで `cAdvisor secure port:10250` または `unsecure port: 10255` が開かれていることを検証します。

Azure PowerShell を使用して実行するには、そのスクリプトを含むフォルダーで、次のコマンドを使用します：

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>次のステップ

ハイブリッド Kubernetes クラスターとそこで実行されるワークロードの正常性とリソース使用率を収集するための監視を有効にしたので、Container insights の[使い方](container-insights-analyze.md)を確認します。