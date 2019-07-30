---
title: Azure PowerShell または Resource Manager テンプレートを使用して Azure Monitor for VMs (プレビュー) を有効にする | Microsoft Docs
description: この記事では、Azure PowerShell または Azure Resource Manager テンプレートを使用して、1 つ以上の Azure 仮想マシンまたは仮想マシン スケール セットで Azure Monitor for VMs を有効にする方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: ff284ea0adf6021ace84cd6a41f0a0e4e987a9c8
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144246"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-templates"></a>Azure PowerShell または Resource Manager テンプレートを使用して Azure Monitor for VMs (プレビュー) を有効にする

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

この記事では、Azure PowerShell または Azure Resource Manager テンプレートを使用して、Azure 仮想マシンまたは仮想マシン スケール セットで Azure Monitor for VMs (プレビュー) を有効にする方法について説明します。 このプロセスを完了すると、すべての仮想マシンの監視を正常に開始し、パフォーマンスや可用性の問題が発生していないかどうかを確認できるようになります。

## <a name="set-up-a-log-analytics-workspace"></a>Log Analytics ワークスペースを設定する 

Log Analytics ワークスペースがない場合は、作成する必要があります。 構成する手順を続行する前に、「[前提条件](vminsights-enable-overview.md#log-analytics)」のセクションで提案されている方法を確認してください。 次に、Azure Resource Manager テンプレート メソッドを使用して、Azure Monitor for VMs のデプロイを完了できます。

### <a name="enable-performance-counters"></a>パフォーマンス カウンターを有効にする

ソリューションによって参照されている Log Analytics ワークスペースが、ソリューションで必要なパフォーマンス カウンターを収集するようにまだ構成されていない場合は、カウンターを有効にする必要があります。 次の 2 つの方法のいずれかでこれを行うことができます。
* 「[Log Analytics での Windows および Linux のパフォーマンス データ ソース](../../azure-monitor/platform/data-sources-performance-counters.md)」の説明に従って、手動で行います
* [Azure PowerShell ギャラリー](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)で入手できる PowerShell スクリプトをダウンロードして実行します

### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>ServiceMap および InfrastructureInsights ソリューションをインストールする
この方法には、Log Analytics ワークスペースでソリューション コンポーネントを有効にするための構成を指定する JSON テンプレートが含まれています。

テンプレートを使用してリソースをデプロイする方法がわからない場合は、以下を参照してください。
* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLI を使用するには、まず、ローカルに CLI をインストールして使用する必要があります。 Azure CLI バージョン 2.0.27 以降を実行する必要があります。 ご利用のバージョンを識別するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードするには、「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。

1. 以下の JSON 構文をコピーして、ファイルに貼り付けます。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. このファイルを *installsolutionsforvminsights.json* としてローカル フォルダーに保存します。

1. *WorkspaceName*、*ResourceGroupName*、および *WorkspaceLocation* の値を取り込みます。 *WorkspaceName* の値は Log Analytics ワークスペースの名前です。 *WorkspaceLocation* の値は、ワークスペースが定義されているリージョンです。

1. これでこのテンプレートをデプロイする準備が整いました。
 
    * テンプレートが含まれているフォルダーで、次の PowerShell コマンドを使用します。

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        設定の変更が完了するまで数分かかる場合があります。 完了すると、次のような結果を含むメッセージが表示されます。

        ```powershell
        provisioningState       : Succeeded
        ```

    * Azure CLI を使用して次のコマンドを実行するには、次の手順を実行します。
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        設定の変更が完了するまで数分かかる場合があります。 完了すると、次のような結果を含むメッセージが表示されます。

        ```azurecli
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して有効にする
仮想マシンまたは仮想マシン スケール セットをオンボードするための、Azure Resource Manager テンプレートの例が用意されています。 これらのテンプレートには、既存のリソースに対する監視を有効にして、監視が有効な新しいリソースを作成するために使用できるシナリオが含まれています。

>[!NOTE]
>テンプレートは、オンボードするリソースと同じリソース グループ内にデプロイする必要があります。

テンプレートを使用してリソースをデプロイする方法がわからない場合は、以下を参照してください。
* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLI を使用するには、まず、ローカルに CLI をインストールして使用する必要があります。 Azure CLI バージョン 2.0.27 以降を実行する必要があります。 ご利用のバージョンを識別するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードするには、「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。

### <a name="download-templates"></a>テンプレートをダウンロードする

Azure Resource Manager テンプレートはアーカイブ ファイル (.zip) として提供さ、GitHub リポジトリから[ダウンロード](https://aka.ms/VmInsightsARMTemplates)できます。 このファイルには各デプロイ シナリオを表すフォルダーが含まれ、各フォルダーにはテンプレートとパラメーター ファイルが格納されています。 実行前に、パラメーター ファイルを変更し、必須の値を指定します。 特定の要件をサポートするためにテンプレート ファイルをカスタマイズする必要がない場合は、変更しないでください。 パラメーター ファイルを変更したら、この記事で後の方で説明されている方法を使用してデプロイできます。 

ダウンロード ファイルには、さまざまなシナリオに対応した、以下のテンプレートが含まれています。

- **ExistingVmOnboarding** テンプレートでは、仮想マシンが既に存在する場合に Azure Monitor for VMs を有効にします。
- **NewVmOnboarding** テンプレートでは、仮想マシンを作成し、これを監視するために Azure Monitor for VMs を有効にします。
- **ExistingVmssOnboarding** テンプレートでは、仮想マシン スケール セットが既に存在する場合に Azure Monitor for VMs を有効にします。
- **NewVmssOnboarding** テンプレートでは、仮想マシン スケール セットを作成し、これを監視するために Azure Monitor for VMs を有効にします。
- **ConfigureWorksapce** テンプレートでは、Linux および Windows オペレーティング システム パフォーマンス カウンターのソリューションと収集を有効にすることで、Azure Monitor for VMs をサポートする Log Analytics ワークスペースを構成します。

>[!NOTE]
>仮想マシン スケール セットが既に存在し、アップグレード ポリシーが**手動**に設定されている場合、**ExistingVmssOnboarding** Azure Resource Manager テンプレートを実行しても、これらのインスタンスに対して Azure Monitor for VMs は既定では有効になりません。 手動でインスタンスをアップグレードする必要があります。

### <a name="deploy-by-using-azure-powershell"></a>Azure PowerShell を使用したデプロイ

Azure PowerShell を使用して監視を有効にするには、次の手順のようにします。

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
設定の変更が完了するまで数分かかる場合があります。 完了すると、次のような結果を含むメッセージが表示されます。

```powershell
provisioningState       : Succeeded
```
### <a name="deploy-by-using-the-azure-cli"></a>Azure CLI を使用したデプロイ

Azure CLI を使用して監視を有効にするには、次の手順のようにします。

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

出力結果は、以下のようになります。

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>PowerShell を使用した有効化

複数の VM または仮想マシン スケール セットに対して Azure Monitor for VMs を有効にするには、PowerShell スクリプト [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0) を使用します。 これは Azure PowerShell ギャラリーから入手できます。 このスクリプトでは以下について反復処理が実行されます。

- サブスクリプションに設定されているすべての仮想マシンと仮想マシン スケール セット。
- *ResourceGroup* で指定されたスコープ付きリソース グループ。 
- *Name* で指定された 1 つの VM または仮想マシン スケール セット。

このスクリプトでは、各 VM または仮想マシン スケール セットに対して、VM 拡張機能が既にインストールされているかどうかが確認されます。 VM 拡張機能がインストールされていない場合、スクリプトで再インストールが試みられます。 VM 拡張機能がインストールされている場合、スクリプトでは Log Analytics と Dependency Agent の VM 拡張機能がインストールされます。

このスクリプトには、Azure PowerShell モジュール Az バージョン 1.0.0 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

スクリプトの引数の詳細と使用例の一覧を取得するには、`Get-Help` を実行します。

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or reconfigures the following on VMs and virtual machine scale sets:
    - Log Analytics VM extension configured to supplied Log Analytics workspace
    - Dependency agent VM extension

    Can be applied to:
    - Subscription
    - Resource group in a subscription
    - Specific VM or virtual machine scale set
    - Compliance results of a policy for a VM or VM extension

    Script will show you a list of VMs or virtual machine scale sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed, they will not install again.
    Use -ReInstall switch if you need to, for example, update the workspace.

    Use -WhatIf if you want to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a resource group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example, to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source and to reinstall (move to a new workspace)
```

次の例では、フォルダー内で PowerShell コマンドを使用して Azure Monitor for VMs を有効化して、想定される出力を解釈する方法を示しています。

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or virtual machine scale sets matching criteria specified

VMs or virtual machine scale sets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on the previous two VMs or virtual machine scale sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example, to update workspace.

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="next-steps"></a>次の手順

これで、仮想マシンに対する監視が有効になったので、この情報を Azure Monitor for VMs での分析に使用できます。
 
- 正常性機能の使用方法については、[Azure Monitor for VMs の正常性の表示](vminsights-health.md)に関する記事をご覧ください。 
- 検出されたアプリケーションの依存関係を表示するには、[Azure Monitor for VMs のマップの表示](vminsights-maps.md)に関する記事をご覧くださいい。 
- VM のパフォーマンスでのボトルネックや全体的な使用率を識別するには、[Azure VM のパフォーマンスの表示](vminsights-performance.md)に関する記事を参照してください。 
- 検出されたアプリケーションの依存関係を表示するには、[Azure Monitor for VMs のマップの表示](vminsights-maps.md)に関する記事をご覧くださいい。