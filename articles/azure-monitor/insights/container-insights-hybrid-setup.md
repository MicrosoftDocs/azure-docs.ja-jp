---
title: ハイブリッド Kubernetes クラスターに Azure Monitor for containers を構成する | Microsoft Docs
description: この記事では、Azure Stack などの環境でホストしている Kubernetes クラスターを監視することを目的として Azure Monitor for containers を構成する方法を説明します。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 12/04/2019
ms.openlocfilehash: 0d6615d832059a8b58c0d5d52533b8c8c962640d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841576"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>ハイブリッド Kubernetes クラスターに Azure Monitor for containers を構成する

Azure Monitor for containers は、Azure Kubernetes Service (AKS) と Azure でホストしている AKS エンジン クラスター用の監視エクスペリエンスを提供するものです。 この記事では、Azure の外部でホストしている Kubernetes クラスターの監視を有効にし、Azure と同等の監視エクスペリエンスを実現する方法について説明します。

## <a name="prerequisites"></a>前提条件

始める前に、必ず以下のものを用意してください。

* Log Analytics ワークスペース。

    Azure Monitor for containers では、Azure の[リージョン別の製品](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)に関するページに一覧表示されているリージョンの Log Analytics ワークスペースがサポートされます。 ワークスペースは、[Azure Resource Manager](../platform/template-workspace-configuration.md)、[PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)、[Azure portal](../learn/quick-create-workspace.md) のいずれかを使用して作成できます。

    >[!NOTE]
    >同一の Log Analytics ワークスペースに名称が同じクラスターが複数ある場合には、そのワークスペースに対してクラスターの監視を有効にすることはできません。 クラスター名は一意になっている必要があります。
    >

* コンテナーの監視を有効にするために、**Log Analytics 共同作成者ロール**のメンバーである必要があります。 Log Analytics ワークスペースへのアクセスを制御する方法の詳細については、[ワークスペースとログ データに対するアクセスの管理](../platform/manage-access.md)に関するページを参照してください。

* [HELM クライアント](https://helm.sh/docs/using_helm/)。これは、特定の Kubernetes クラスターに Azure Monitor for containers のチャートをオンボードするために使用します。

* コンテナー化されたバージョンの Linux 用 Log Analytics エージェントが Azure Monitor と通信するためには、プロキシとファイアウォールに関する次の構成情報が必要です。

    |エージェントのリソース|Port |
    |------|---------|   
    |*.ods.opinsights.azure.com |ポート 443 |  
    |*.oms.opinsights.azure.com |ポート 443 |  
    |*.blob.core.windows.net |ポート 443 |  
    |*.dc.services.visualstudio.com |ポート 443 | 

* コンテナー化されたエージェントがパフォーマンスに関するメトリックを収集できるように、クラスター内のすべてのノードで `cAdvisor port: 10255` を開いておく必要があります。

* コンテナー化されたエージェントがクラスター内の Kubernetes API サービスとやり取りしてインベントリ データを収集するために、コンテナーに `KUBERNETES_SERVICE_HOST` と `KUBERNETES_PORT_443_TCP_PORT` の 2 つの環境変数を指定する必要があります。 

>[!IMPORTANT]
>ハイブリッド Kubernetes クラスターの監視は、ciprod10182019 以降のバージョンのエージェントに限りサポートされます。 

## <a name="supported-configurations"></a>サポートされている構成

Azure Monitor for containers では、以下が公式にサポートされています。

- 環境:オンプレミスの Kubernetes、Azure 上の AKS エンジン、Azure Stack。 詳細については、[Azure Stack 上の AKS エンジン](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)に関するページを参照してください。
- Kubernetes のバージョンとサポート ポリシーについては、[AKS でサポートされているバージョン](../../aks/supported-kubernetes-versions.md)と同じです。 
- コンテナー ランタイム:Docker と Moby
- マスター ノードとワーカー ノード用の Linux OS のリリース:Ubuntu (18.04 LTS と 16.04 LTS)
- サポートされているアクセス制御:Kubernetes の RBAC と非 RBAC

## <a name="enable-monitoring"></a>監視を有効にする

Hybrid Kubernetes クラスターに対して Azure Monitor for containers を有効にするには、次の手順を順番に実施する必要があります。

1. Log Analytics ワークスペースに Container Insights ソリューションを構成する。

2. Log Analytics ワークスペースで Azure Monitor for containers の HELM チャートを有効にする。

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Azure Monitor のコンテナー ソリューションを追加する方法

既製の Azure Resource Manager テンプレートを使ってソリューションをデプロイする方法には、Azure PowerShell コマンドレット `New-AzResourceGroupDeployment` を使用する方法と、Azure CLI を使用する方法の 2 種類があります。

テンプレートを使用するリソースのデプロイの概念について馴染みがない場合は、以下を参照してください。

* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../../azure-resource-manager/resource-group-template-deploy.md)

* [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLI を使用する場合は、まず、ローカルに CLI をインストールして使用する必要があります。 Azure CLI バージョン 2.0.59 以降を実行している必要があります。 ご利用のバージョンを識別するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)に関するページを参照してください。 

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
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
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

7. 値を編集します。**workspaceResourceId** には手順 3 でコピーした値を使用し、**workspaceRegion** には Azure CLI コマンド [az monitor log-analytics workspace show](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list) を実行して得られる**リージョン**の値をコピーします。

8. このファイルを containerSolutionParams.json という名前でローカル フォルダーに保存します。

9. これでこのテンプレートをデプロイする準備が整いました。 

   * Azure PowerShell を使用してデプロイするには、そのテンプレートを含むフォルダーで次のコマンドを使用します。

       ```powershell
       # configure and login to the cloud of log analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of log analytics workspace>
       ```
       
       ```powershell
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of log analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```
       
       設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが表示されます。

       ```powershell
       provisioningState       : Succeeded
       ```

   * Azure CLI を使用してデプロイするには、次のコマンドを実行します。
    
       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az group deployment create --resource-group <resource group of log analytics workspace> --template-file ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが表示されます。

       ```azurecli
       provisioningState       : Succeeded
       ```
     
       監視を有効にした後、クラスターの正常性メトリックが表示されるまで、約 15 分かかる場合があります。 

## <a name="install-the-chart"></a>チャートをインストールする

HELM チャートを有効にする手順は次のとおりです。

1. 次のコマンドを実行して、ローカルのリストに Azure のチャート リポジトリを追加します。

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. 次のコマンドを実行してチャートをインストールします。

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Log Analytics ワークスペースが Azure China にある場合には、次のコマンドを実行します。

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers 
    ```

    Log Analytics ワークスペースが Azure US Government にある場合には、次のコマンドを実行します。

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>データ収集を構成する

バージョン 1.0.0 以降のチャートでは、エージェントによるデータ収集の設定を ConfigMap からコントロールします。 エージェントによるデータ収集の設定に関する詳細については、[こちら](container-insights-agent-config.md)のドキュメントを参照してください。 

チャートを正常にデプロイした後は、Azure Monitor for containers 内のハイブリッド Kubernetes クラスターに関するデータを Azure portal から確認できるようになります。  

>[!NOTE]
>インジェストの待ち時間 (Azure Log Analytics ワークスペースでエージェントがコミットを完了するまで) は、5 から 10 分程度です。 必要な監視データのすべてが Azure Monitor で利用できるようになるまでの間は、クラスターの状態として **[データなし]** と **[不明]** のいずれかの値が表示されます。 

## <a name="troubleshooting"></a>トラブルシューティング

ハイブリッド Kubernetes クラスターの監視を有効にしようとしているときにエラーが発生した場合は、PowerShell スクリプト [TroubleshootError_nonAzureK8s.ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) をコピーしてコンピューターのフォルダーに保存します。 このスクリプトは、発生した問題を検出して修正するために提供されています。 次の問題を修正を検出して修正を試みるように設計されています：

* 指定された Azure Monitor Log Analytics ワークスペースが有効 
* Log Analytics ワークスペースが Azure Monitor for Containers ソリューションで構成されています。 そうでなければ、ワークスペースを構成します。
* OmsAgent replicaset ポッドを実行中
* OmsAgent daemonset ポッドを実行中
* OmsAgent ヘルス サービスを実行中 
* Log Analytics ワークスペース ID とコンテナー化されたエージェントで構成されたキーは、Insight が構成されているワークスペースと一致します。
* すべての Linux ワーカー ノードに `kubernetes.io/role=agent` のラベルがあることを確認して、rs ポッドをスケジュールします。 存在しなければ、追加してください。
* クラスター内のすべてのノードで `cAdvisor port: 10255` が開かれていることを検証します。

Azure PowerShell を使用して実行するには、そのスクリプトを含むフォルダーで、次のコマンドを使用します：

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile>
```

## <a name="next-steps"></a>次の手順

ハイブリッド Kubernetes クラスターとそこで実行中のワークロードの正常性とリソース使用率を収集するための監視を有効にしたので、Azure Monitor for containers の[使い方](container-insights-analyze.md)について学習します。
