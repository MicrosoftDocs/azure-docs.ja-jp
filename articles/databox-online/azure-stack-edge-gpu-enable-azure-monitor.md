---
title: Azure Stack Edge Pro GPU デバイスで Azure Monitor を有効にする
description: Azure Stack Edge Pro GPU デバイスで Azure Monitor を有効にする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: c2f66895fccd14dcffd8c5570f1d5f46933090aa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439189"
---
# <a name="enable-azure-monitor-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスで Azure Monitor を有効にする

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU デバイス上のコンテナーの監視は、複数のコンピューティング アプリケーションを実行している場合に特に重要です。 Azure Monitor を使用すると、デバイスで実行されている Kubernetes クラスターからコンテナー ログとメモリおよびプロセッサ メトリックを収集できます。

この記事では、デバイスで Azure Monitor を有効にし、Log Analytics ワークスペースでコンテナー ログを収集するために必要な手順について説明します。 現在、Azure Stack Edge Pro GPU デバイスでは、Azure Monitor メトリック ストアはサポートされていません。 


## <a name="prerequisites"></a>前提条件

開始する前に、次の条件を満たしている必要があります。

- Azure Stack Edge Pro デバイス。 [チュートリアル:デバイスのアクティブ化](azure-stack-edge-gpu-deploy-activate.md)に関する記事の手順に従って、デバイスがアクティブ化されている。
- **コンピューティングの構成** が完了している。コンピューティングの構成は、お使いのデバイスで、[チュートリアル: Azure Stack Edge Pro デバイスでのコンピューティングの構成](azure-stack-edge-gpu-deploy-configure-compute.md)に関する記事に従って構成します。 お使いのデバイスには、関連付けられた IoT Hub リソース、IoT デバイス、および IoT Edge デバイスが必要です。


## <a name="create-log-analytics-workspace"></a>Log Analytics ワークスペースの作成

Log Analytics ワークスペースを作成するには、次の手順を実行します。 Log Analytics ワークスペースは、ログ データが収集されて格納される論理ストレージ ユニットです。

1. Azure portal で **[+ リソースの作成]** を選択し、**Log Analytics ワークスペース** を検索してから **[作成]** を選択します。 
1. **[Log Analytics ワークスペースの作成]** で、次の設定を構成します。 残りの部分は既定値のままにします。

    1. **[基本]** タブで、ワークスペースのサブスクリプション、リソース グループ、名前、およびリージョンを指定します。 

        ![Log Analytics ワークスペースの [基本] タブ](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-basics-1.png)  

    1. **[価格レベル]** タブで、既定の **[従量課金制プラン]** をそのまま使用します。

        ![Log Analytics ワークスペースの [価格] タブ](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-pricing-1.png) 

    1. **[確認と作成]** タブでワークスペースの情報を確認し、 **[作成]** を選択します。

        ![Log Analytics ワークスペースの [確認と作成]](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-review-create-1.png)

詳細については、[Azure portal で Log Analytics ワークスペースを作成する](../azure-monitor/logs/quick-create-workspace.md)方法に関するページに記載されている詳細な手順を参照してください。



## <a name="enable-container-insights"></a>Container Insights を有効にする

次の手順を実行して、ワークスペースで Container Insights を有効にします。 

1. 「[Azure Monitor のコンテナー ソリューションを追加する方法](../azure-monitor/containers/container-insights-hybrid-setup.md#how-to-add-the-azure-monitor-containers-solution)」の詳細な手順に従います。 次のテンプレート ファイル (`containerSolution.json`) を使用します。

    ```yml
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

1. リソース ID と場所を取得します。 `Your Log Analytics workspace > General > Properties` にアクセスします。 次の情報をコピーします。

    - **リソース ID**。これは、Azure Log Analytics ワークスペースの完全修飾 Azure リソース ID です。 
    - **場所**。これは、Azure リージョンです。

    ![Log Analytics ワークスペースのプロパティ](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-properties-1.png) 

1. 次のパラメーター ファイル (`containerSolutionParams.json`) を使用します。 `workspaceResourceId` をリソース ID に、`workspaceRegion` を前の手順でコピーした場所に置き換えます。

    ```yaml
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "workspaceResourceId": {
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
        },
        "workspaceRegion": {
        "value": "westus"
        }
        }
    }
    ```
    
    Container Insights を有効にした Log Analytics ワークスペースの出力例を次に示します。
    
    ```powershell
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...
    MOTD: Switch to Bash from PowerShell: bash
    VERBOSE: Authenticating to Azure ...
    VERBOSE: Building your Azure drive ...
    
    PS /home/alpa> az account set -s fa68082f-8ff7-4a25-95c7-ce9da541242f
    PS /home/alpa> ls
    clouddrive  containerSolution.json
    PS /home/alpa> ls
    clouddrive  containerSolution.json  containerSolutionParams.json
    PS /home/alpa> az deployment group create --resource-group myaserg --name Testdeployment1 --template-file containerSolution.json --parameters containerSolutionParams.json
    {- Finished ..
        "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.Resources/deployments/Testdeployment1",
        "location": null,
        "name": "Testdeployment1",
        "properties": {
        "correlationId": "3a9045fe-2de0-428c-b17b-057508a8c575",
        "debugSetting": null,
        "dependencies": [],
        "duration": "PT11.1588316S",
        "error": null,
        "mode": "Incremental",
        "onErrorDeployment": null,
        "outputResources": [
            {
            "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.OperationsManagement/solutions/ContainerInsights(myaseloganalyticsws)",
            "resourceGroup": "myaserg"
            }
        ],
        "outputs": null,
        "parameters": {
            "workspaceRegion": {
            "type": "String",
            "value": "westus"
            },
            "workspaceResourceId": {
            "type": "String",
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
            }
        },
        "parametersLink": null,
        "providers": [
            {
            "id": null,
            "namespace": "Microsoft.Resources",
            "registrationPolicy": null,
            "registrationState": null,
            "resourceTypes": [
                {
                "aliases": null,
                "apiProfiles": null,
                "apiVersions": null,
                "capabilities": null,
                "defaultApiVersion": null,
                "locations": [
                    null
                ],
                "properties": null,
                "resourceType": "deployments"
                }
            ]
            }
        ],
        "provisioningState": "Succeeded",
        "templateHash": "10500027184662969395",
        "templateLink": null,
        "timestamp": "2020-11-06T22:09:56.908983+00:00",
        "validatedResources": null
        },
        "resourceGroup": "myaserg",
        "tags": null,
        "type": "Microsoft.Resources/deployments"
    }
    PS /home/alpa>
    ```

## <a name="configure-azure-monitor-on-your-device"></a>デバイスで Azure Monitor を構成する

1. 新しく作成された Log Analytics リソースにアクセスし、**ワークスペース ID** と **主キー** (ワークスペース キー) をコピーします。

    ![Log Analytics ワークスペースでエージェントを管理する](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-agents-management-1.png)

    この情報は後の手順で使用するので、保存しておいてください。

1. [デバイスの PowerShell インターフェイスに接続します](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。 
    
1. Log analytics ワークスペース ID とワークスペース キーを使用して、次のコマンドレットを実行します。

    `Set-HcsKubernetesAzureMonitorConfiguration -WorkspaceId <> -WorkspaceKey <>`

1. Azure Monitor が有効になると、Log Analytics ワークスペースにログが表示されます。 デバイスにデプロイされている Kubernetes クラスターの状態を表示するには、 **[Azure Monitor] > [分析情報] > [コンテナー]** にアクセスします。 [環境] オプションで、 **[すべて]** を選択します。 

    ![Log Analytics ワークスペースのメトリック](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-metrics-1.png)

## <a name="next-steps"></a>次の手順

- [Kubernetes ダッシュボードを使用して Kubernetes ワークロードを監視する方法](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)を確認します。
- [デバイス イベントのアラート通知を管理する](azure-stack-edge-gpu-manage-device-event-alert-notifications.md)方法について説明します。 