---
title: Azure Monitor for VMs (プレビュー) をデプロイする | Microsoft Docs
description: この記事では、分散アプリケーションがどのように実行されているか、および確認されている正常性の問題について理解できるように、Azure Monitor for VMs をデプロイして構成する方法について説明します。
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
ms.date: 03/13/2019
ms.author: magoedte
ms.openlocfilehash: 1a4bfae22477e345176971bd40b0afa91c8867fb
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58885827"
---
# <a name="deploy-azure-monitor-for-vms-preview"></a>Azure Monitor for VMs (プレビュー) をデプロイする

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

この記事では、Azure Monitor for VMs の設定方法について説明します。 お使いの Azure 仮想マシン (VM) と仮想マシン スケール セットおよび環境内の仮想マシンのオペレーティング システムの正常性を監視するサービスです。 この監視には、それらでホストされている可能性があるアプリケーションの依存関係の検出とマッピングが含まれます。 

Azure Monitor for VMs を有効にするには、次のいずれかの方法を使用します。

* VM から直接 **Insights (プレビュー)** を選択することにより、単一の Azure 仮想マシンを有効にします。
* Azure Policy を使用して、複数の Azure VM を有効にします。 この方法を使用すると、既存 VM および新規 VM の必要な依存関係がインストールされて、適切に構成されます。 準拠していない VM が報告されるので、それらを有効にするかどうか、およびそれらを修復する方法を決めることができます。
* PowerShell を使用して、指定したサブスクリプションまたはリソース グループ全体の複数の Azure VM または仮想マシン スケール セットを有効にします。

各方法についての他の情報は、記事の後半で提供します。

## <a name="prerequisites"></a>前提条件
始める前に、次のセクションの情報を理解しておいてださい。

### <a name="log-analytics"></a>Log Analytics

Azure Monitor for VMs は、次のリージョンで Log Analytics ワークスペースをサポートします。

- 米国中西部
- 米国東部
- カナダ中部<sup>1</sup>
- 英国南部<sup>1</sup>
- 西ヨーロッパ
- 東南アジア<sup>1</sup>

<sup>1</sup> このリージョンでは、Azure Monitor for VMs の正常性の機能はサポートされていません。

>[!NOTE]
>Azure 仮想マシンは、任意のリージョンからデプロイできます。Log Analytics ワークスペースのサポートされるリージョンに制限されることはありません。
>

ワークスペースがない場合は、次のいずれかの方法で作成できます。
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure ポータル](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Azure portal で単一の Azure VM に対する監視を有効にする場合は、このプロセスの間にワークスペースを作成できます。

大規模シナリオに対するソリューションを有効にするには、最初に Log Analytics ワークスペースで以下を構成します。

* ServiceMap および InfrastructureInsights ソリューションをインストールします。 この記事で提供されている Azure Resource Manager テンプレートを使用することによってのみ、このインストールを完了できます。
* パフォーマンス カウンターを収集するように Log Analytics ワークスペースを構成します。

大規模なシナリオ向けにワークスペースを構成するには、大規模なデプロイのための Log Analytics ワークスペースの設定に関するセクションを参照してください。

### <a name="supported-operating-systems"></a>サポートされているオペレーティング システム

次の表は、VM 用 Azure Monitor でサポートされている Windows および Linux オペレーティング システムの一覧を示しています。 メジャーおよびマイナーの Linux OS リリースとサポートされているカーネルのバージョンの詳細な一覧は、このセクションの後の方で示されます。

|OS バージョン |パフォーマンス |マップ |Health |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 6、7| X | X| X |
|Ubuntu 14.04、16.04、18.04 | X | X | X |
|CentOS Linux 6、7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 11、12 | X | X | X |
|Debian 8、9.4 | X<sup>1</sup> | | X |

<sup>1</sup> Azure Monitor for VMs のパフォーマンス機能は、Azure Monitor からのみ使用できます。 Azure VM の左側のウィンドウから直接アクセスしたときには使用できません。

>[!NOTE]
>次の情報は、Linux オペレーティング システムのサポートに適用されます。
> - 既定と SMP Linux のカーネル リリースのみがサポートされています。
> - Physical Address Extension (PAE) や Xen などの非標準のカーネル リリースは、どの Linux ディストリビューションでもサポートされていません。 たとえば、リリースの文字列が *2.6.16.21-0.8-xen* であるシステムはサポートされていません。
> - カスタム カーネル (標準カーネルの再コンパイルを含む) はサポートされていません。
> - CentOSPlus カーネルはサポートされています。

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| OS バージョン | カーネル バージョン |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| OS バージョン | カーネル バージョン |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

### <a name="centosplus"></a>CentOSPlus
| OS バージョン | カーネル バージョン |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| OS バージョン | カーネル バージョン |
|:--|:--|
| Ubuntu 18.04 | カーネル 4.15.* |
| Ubuntu 16.04.3 | カーネル 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| OS バージョン | カーネル バージョン
|:--|:--|
|11 SP4 | 3.0.* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS バージョン | カーネル バージョン
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent
Azure Monitor for VMs のマップ機能では、Microsoft Dependency Agent からデータが取得されます。 Dependency Agent は、Log Analytics への接続に関して Log Analytics エージェントに依存しています。 したがって、お使いのシステムに Log Analytics エージェントをインストールし、Dependency Agent を使用して構成する必要があります。

単一の Azure VM に対して Azure Monitor for VMs を有効にするか、または大規模なデプロイ方法を使用するかにかかわらず、Azure VM Dependency Agent 拡張機能を使用し、エクスペリエンスの一部としてエージェントをインストールする必要があります。

ハイブリッド環境では、次の 2 つの方法のいずれかで Dependency Agent をダウンロードしてインストールできます。手動で行うか、または Azure の外部でホストされている仮想マシンに対する自動デプロイ方法を使用します。

次の表では、ハイブリッド環境でマップ機能がサポートしている接続先ソースについて説明します。

| 接続先ソース | サポートされています | 説明 |
|:--|:--|:--|
| Windows エージェント | はい | [Windows の Log Analytics エージェント](../../azure-monitor/platform/log-analytics-agent.md)に加えて、Windows エージェントには Microsoft Dependency Agent が必要です。 オペレーティング システムのバージョンの完全な一覧については、「[サポートされているオペレーティング システム](#supported-operating-systems)」をご覧ください。 |
| Linux エージェント | はい | [Linux の Log Analytics エージェント](../../azure-monitor/platform/log-analytics-agent.md)に加えて、Linux エージェントには Microsoft Dependency Agent が必要です。 オペレーティング システムのバージョンの完全な一覧については、「[サポートされているオペレーティング システム](#supported-operating-systems)」をご覧ください。 |
| System Center Operations Manager 管理グループ | いいえ  | |

Dependency Agent は、次の場所からダウンロードできます。

| ファイル | OS | バージョン | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) |  Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |

## <a name="role-based-access-control"></a>ロールベースのアクセス制御
Azure Monitor for VMs の機能を有効にしてアクセスするには、次のアクセス ロールが割り当てられている必要があります。

- ソリューションを有効にするには、"*Log Analytics 共同作成者*" ロールが必要です。

- パフォーマンス、正常性、マップのデータを表示するには、Azure VM に対する "*監視閲覧者*" ロールが必要です。 Azure Monitor for VMs 用に Log Analytics ワークスペースを構成する必要があります。

Log Analytics ワークスペースへのアクセスを制御する方法の詳細については、「[ワークスペースを管理する](../../azure-monitor/platform/manage-access.md)」を参照してください。

## <a name="enable-monitoring-in-the-azure-portal"></a>Azure Portal で監視を有効にする
Azure portal でお使いの Azure VM の監視を有効にするには、次の操作を行います。

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. **[仮想マシン]** を選択します。

1. 一覧から VM を選択します。

1. [VM] ページの **[監視]** セクションで、**[Insights (プレビュー)]** を選択します。

1. **[Insights (プレビュー)]** ページで、**[今すぐ試す]** を選択します。

    ![VM に対して Azure Monitor for VMs を有効にする](./media/vminsights-onboard/enable-vminsights-vm-portal-01.png)
1. **[Azure Monitor Insights Onboarding]\(Azure Monitor Insights の配布準備\)** ページで、同じサブスクリプションに既存の Log Analytics ワークスペースがある場合は、ドロップダウン リストでそれを選択します。  
    このリストでは、サブスクリプションで仮想マシンがデプロイされている既定のワークスペースと場所が事前に選択されています。 

    >[!NOTE]
    >VM からの監視データを格納するための新しい Log Analytics ワークスペースを作成する場合は、前に示されているサポートされるリージョンのいずれかにある「[Log Analytics ワークスペースの作成](../../azure-monitor/learn/quick-create-workspace.md)」の手順に従ってください。

監視を有効にした後、仮想マシンの正常性メトリックが表示されるまで、約 10 分かかる場合があります。

![Azure Monitor for VMs の監視デプロイ プロセスを有効にする](./media/vminsights-onboard/onboard-vminsights-vm-portal-status.png)


## <a name="deploy-at-scale"></a>大規模にデプロイする
このセクションでは、Azure Policy または Azure PowerShell を使用して大規模に Azure Monitor for VMs をデプロイします。

VM をデプロイする前に、以下のようにして Log Analytics ワークスペースを事前構成します。

1. ワークスペースがまだない場合は、Azure Monitor for VMs をサポートできるワークスペースを作成します。  
    先に進む前に「[ワークスペースを管理する](../../log-analytics/log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json)」を参照して、コスト、管理、およびコンプライアンスに関する考慮事項を理解してください。

1. Azure Monitor for VMs をサポートするために使用できる新しいワークスペースがまだ存在しない場合は作成します。 新しいワークスペースを作成する前に「[ワークスペースを管理する](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json)」を読み、コスト、管理、およびコンプライアンスの考慮事項を理解してから次に進んでください。

1. Linux および Windows VM 上のコレクションのために、ワークスペースのパフォーマンス カウンターを有効にします。

1. ワークスペースに ServiceMap および InfrastructureInsights ソリューションをインストールして有効にします。

### <a name="set-up-a-log-analytics-workspace"></a>Log Analytics ワークスペースを設定する
Log Analytics ワークスペースがない場合は、「[前提条件](#log-analytics)」セクションで推奨されている方法を確認してワークスペースを作成します。

#### <a name="enable-performance-counters"></a>パフォーマンス カウンターを有効にする
ソリューションによって参照されている Log Analytics ワークスペースが、ソリューションで必要なパフォーマンス カウンターを収集するようにまだ構成されていない場合は、カウンターを有効にする必要があります。 次の 2 つの方法のいずれかで行うことができます。
* 「[Log Analytics での Windows および Linux のパフォーマンス データ ソース](../../azure-monitor/platform/data-sources-performance-counters.md)」の説明に従って、手動で行います
* [Azure PowerShell ギャラリー](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)で入手できる PowerShell スクリプトをダウンロードして実行します

#### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>ServiceMap および InfrastructureInsights ソリューションをインストールする
この方法には、Log Analytics ワークスペースでソリューション コンポーネントを有効にするための構成を指定する JSON テンプレートが含まれています。

テンプレートを使用してリソースをデプロイすることに慣れていない場合は、以下をご覧ください。
* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLI を使用する場合は、まず、ローカルに CLI をインストールして使用する必要があります。 Azure CLI バージョン 2.0.27 以降を実行する必要があります。 ご利用のバージョンを識別するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)に関するページを参照してください。

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

        設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが表示されます。

        ```powershell
        provisioningState       : Succeeded
        ```

    * Azure CLI を使用して次のコマンドを実行するには、次の手順を実行します。
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが表示されます。

        ```azurecli
        provisioningState       : Succeeded

### Enable by using Azure Policy
To enable Azure Monitor for VMs at scale in a way that helps ensure consistent compliance and the automatic enabling of the newly provisioned VMs, we recommend [Azure Policy](../../governance/policy/overview.md). These policies:

* Deploy the Log Analytics agent and the Dependency agent.
* Report on compliance results.
* Remediate for non-compliant VMs.

To enable Azure Monitor for VMs by using Azure Policy in your tenant:

- Assign the initiative to a scope: management group, subscription, or resource group
- Review and remediate compliance results

For more information about assigning Azure Policy, see [Azure Policy overview](../../governance/policy/overview.md#policy-assignment) and review the [overview of management groups](../../governance/management-groups/index.md) before you continue.

The policy definitions are listed in the following table:

|Name |Description |Type |
|-----|------------|-----|
|[Preview]: Enable Azure Monitor for VMs |Enable Azure Monitor for the Virtual Machines (VMs) in the specified scope (management group, subscription, or resource group). Takes Log Analytics workspace as a parameter. |Initiative |
|[Preview]: Audit Dependency Agent Deployment – VM Image (OS) unlisted |Reports VMs as non-compliant if the VM Image (OS) isn't defined in the list and the agent isn't installed. |Policy |
|[Preview]: Audit Log Analytics Agent Deployment – VM Image (OS) unlisted |Reports VMs as non-compliant if the VM Image (OS) isn't defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Dependency Agent for Linux VMs |Deploy Dependency Agent for Linux VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Dependency Agent for Windows VMs |Deploy Dependency Agent for Windows VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Log Analytics Agent for Linux VMs |Deploy Log Analytics Agent for Linux VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Log Analytics Agent for Windows VMs |Deploy Log Analytics Agent for Windows VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |

Standalone policy (not included with the initiative) is described here:

|Name |Description |Type |
|-----|------------|-----|
|[Preview]: Audit Log Analytics Workspace for VM - Report Mismatch |Report VMs as non-compliant if they aren't logging to the Log Analytics workspace specified in the policy/initiative assignment. |Policy |

#### Assign the Azure Monitor initiative
With this initial release, you can create the policy assignment only in the Azure portal. To understand how to complete these steps, see [Create a policy assignment from the Azure portal](../../governance/policy/assign-policy-portal.md).

1. To launch the Azure Policy service in the Azure portal, select **All services**, and then search for and select **Policy**.

1. In the left pane of the Azure Policy page, select **Assignments**.  
    An assignment is a policy that has been assigned to take place within a specific scope.
    
1. At the top of the **Policy - Assignments** page, select **Assign Initiative**.

1. On the **Assign Initiative** page, select the **Scope** by clicking the ellipsis (...), and select a management group or subscription.  
    In our example, a scope limits the policy assignment to a grouping of virtual machines for enforcement.
    
1. At the bottom of the **Scope** page, save your changes by selecting **Select**.

1. (Optional) To remove one or more resources from the scope, select **Exclusions**.

1. Select the **Initiative definition** ellipsis (...) to display the list of available definitions, select **[Preview] Enable Azure Monitor for VMs**, and then select **Select**.  
    The **Assignment name** box is automatically populated with the initiative name you selected, but you can change it. You can also add an optional description. The **Assigned by** box is automatically populated based on who is logged in, and this value is optional.
    
1. In the **Log Analytics workspace** drop-down list for the supported region, select a workspace.

   > [!NOTE]
   > If the workspace is beyond the scope of the assignment, grant *Log Analytics Contributor* permissions to the policy assignment's Principal ID. If you don't do this, you might see a deployment failure such as: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`
   > To grant access, review [how to manually configure the managed identity](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  The **Managed Identity** check box is selected, because the initiative being assigned includes a policy with the *deployIfNotExists* effect.
    
1. In the **Manage Identity location** drop-down list, select the appropriate region.

1. Select **Assign**.

#### Review and remediate the compliance results

You can learn how to review compliance results by reading [identify non-compliance results](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). In the left pane, select **Compliance**, and then locate the **[Preview] Enable Azure Monitor for VMs** initiative for VMs that aren't compliant according to the assignment you created.

![Policy compliance for Azure VMs](./media/vminsights-onboard/policy-view-compliance-01.png)

Based on the results of the policies included with the initiative, VMs are reported as non-compliant in the following scenarios:

* Log Analytics or the Dependency agent isn't deployed.  
    This scenario is typical for a scope with existing VMs. To mitigate it, deploy the required agents by [creating remediation tasks](../../governance/policy/how-to/remediate-resources.md) on a non-compliant policy.  
    - [Preview]: Deploy Dependency Agent for Linux VMs
    - [Preview]: Deploy Dependency Agent for Windows VMs
    - [Preview]: Deploy Log Analytics Agent for Linux VMs
    - [Preview]: Deploy Log Analytics Agent for Windows VMs

* VM Image (OS) isn't identified in the policy definition.  
    The criteria of the deployment policy include only VMs that are deployed from well-known Azure VM images. Check the documentation to see whether the VM OS is supported. If it isn't supported, duplicate the deployment policy and update or modify it to make the image compliant.  
    - [Preview]: Audit Dependency Agent Deployment – VM Image (OS) unlisted
    - [Preview]: Audit Log Analytics Agent Deployment – VM Image (OS) unlisted

* VMs aren't logging in to the specified Log Analytics workspace.  
    It's possible that some VMs in the initiative scope are logging in to a Log Analytics workspace other than the one that's specified in the policy assignment. This policy is a tool to identify which VMs are reporting to a non-compliant workspace.  
    - [Preview]: Audit Log Analytics Workspace for VM - Report Mismatch

### Enable with PowerShell
To enable Azure Monitor for VMs for multiple VMs or virtual machine scale sets, you can use the PowerShell script [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0), available from the Azure PowerShell Gallery. This script iterates through every virtual machine and virtual machine scale set in your subscription, in the scoped resource group that's specified by *ResourceGroup*, or to a single VM or virtual machine scale set that's specified by *Name*. For each VM or virtual machine scale set, the script verifies whether the VM extension is already installed. If the VM extension is not installed, the script tries to reinstall it. If the VM extension is installed, the script installs the Log Analytics and Dependency agent VM extensions.

This script requires Azure PowerShell module Az version 1.0.0 or later. Run `Get-Module -ListAvailable Az` to find the version. If you need to upgrade, see [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps). If you're running PowerShell locally, you also need to run `Connect-AzAccount` to create a connection with Azure.

To get a list of the script's argument details and example usage, run `Get-Help`.

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VMs and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VMs/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


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

    Install for all VMs in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to reinstall (move to a new workspace)
```

次の例では、フォルダー内で PowerShell コマンドを使用して Azure Monitor for VMs を有効化して、想定される出力を解釈する方法を示しています。

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or VM ScaleSets matching criteria specified

VMs or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on above 2 VMs or VM Scale Sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example to update workspace

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

Already Onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="enable-for-a-hybrid-environment"></a>ハイブリッド環境対応にする
このセクションでは、Azure Monitor for VMs による監視のために、データ センターやその他のクラウド環境でホストされている仮想マシンまたは物理コンピューターをデプロイする方法について説明します。

Azure Monitor for VMs マップの Dependency Agent でデータ自体が送信されることはないため、ファイアウォールやポートを変更する必要はありません。 マップ データは、Log Analytics エージェントによって常に Azure Monitor サービスに直接送信されます。または、ご利用の IT セキュリティ ポリシーでネットワーク上のコンピューターがインターネットに接続することが許可されていない場合には、[OMS ゲートウェイ](../../azure-monitor/platform/gateway.md)を経由して送信されます。

[Log Analytics Linux および Windows エージェント](../../log-analytics/log-analytics-agent-overview.md)の要件とデプロイ方法を確認します。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

手順は次のとおりです。

1. Windows または Linux 用の Log Analytics エージェントをインストールします。

1. [Windows](https://aka.ms/dependencyagentwindows) または [Linux](https://aka.ms/dependencyagentlinux) 用の Azure Monitor for VMs Map Dependency Agent をダウンロードしてインストールします。

1. パフォーマンス カウンターの収集を有効にします。

1. Azure Monitor for VMs をデプロイします。

### <a name="install-the-dependency-agent-on-windows"></a>Windows に Dependency Agent をインストールする
`InstallDependencyAgent-Windows.exe` を実行することで、Dependency Agent を Windows コンピューターに手動でインストールできます。 オプションを指定せずにこの実行可能ファイルを実行すると、セットアップ ウィザードが起動します。ここで、指示に従って対話形式でエージェントをインストールできます。

>[!NOTE]
>エージェントをインストールまたはアンインストールするには、"*管理者*" 特権が必要です。

次の表に、コマンド ラインからのエージェントのセットアップでサポートされているパラメーターを示します。

| パラメーター | 説明 |
|:--|:--|
| /? | コマンド ライン オプションの一覧を返します。 |
| /S | ユーザーの操作を必要とせずに、サイレント インストールを実行します。 |

たとえば、`/?` パラメーターを使用してインストール プログラムを実行するには、「**InstallDependencyAgent-Windows.exe /?**」と入力します。

Windows Dependency Agent のファイルは、既定で *C:\Program Files\Microsoft Dependency Agent* にインストールされます。 セットアップの完了後に Dependency Agent が起動しない場合は、詳細なエラー情報のログを確認します。 ログ ディレクトリは、*%Programfiles%\Microsoft Dependency Agent\logs* にあります。

### <a name="install-the-dependency-agent-on-linux"></a>Linux に Dependency Agent をインストールする
Dependency Agent は、*InstallDependencyAgent-Linux64.bin* (自己解凍バイナリ ファイルを含むシェル スクリプト) から Linux サーバーにインストールされます。 `sh` を使用してファイルを実行するか、実行アクセス許可をファイル自体に追加します。

>[!NOTE]
> エージェントをインストールまたは構成するには、ルート アクセスが必要です。
>

| パラメーター | 説明 |
|:--|:--|
| -help | コマンド ライン オプションの一覧を取得します。 |
| -s | ユーザー プロンプトを表示せずにサイレント インストールを実行します。 |
| --check | アクセス許可とオペレーティング システムを確認しますが、エージェントはインストールしません。 |

たとえば、`-help` パラメーターを使用してインストール プログラムを実行するには、「**InstallDependencyAgent-Linux64.bin -help**」と入力します。

コマンド `sh InstallDependencyAgent-Linux64.bin` を実行し、Linux Dependency Agent を root としてインストールします。

Dependency Agent が起動しない場合は、詳細なエラー情報のログを確認します。 Linux エージェントのログ ディレクトリは、*/var/opt/microsoft/dependency-agent/log* にあります。

Dependency Agent のファイルは、次のディレクトリに保存されます。

| ファイル | Location |
|:--|:--|
| コア ファイル | /opt/microsoft/dependency-agent |
| ログ ファイル | /var/opt/microsoft/dependency-agent/log |
| 構成ファイル | /etc/opt/microsoft/dependency-agent/config |
| サービス実行可能ファイル | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| バイナリ ストレージ ファイル | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>パフォーマンス カウンターを有効にする
ソリューションによって参照されている Log Analytics ワークスペースが、ソリューションで必要なパフォーマンス カウンターを収集するようにまだ構成されていない場合は、カウンターを有効にする必要があります。 次の 2 つの方法のいずれかで行うことができます。
* 「[Log Analytics での Windows および Linux のパフォーマンス データ ソース](../../azure-monitor/platform/data-sources-performance-counters.md)」の説明に従って、手動で行います
* [Azure PowerShell ギャラリー](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)で入手できる PowerShell スクリプトをダウンロードして実行します

### <a name="deploy-azure-monitor-for-vms"></a>Azure Monitor for VMs をデプロイする
この方法には、Log Analytics ワークスペースでソリューション コンポーネントを有効にするための構成を指定する JSON テンプレートが含まれています。

テンプレートを使用してリソースをデプロイすることに慣れていない場合は、以下をご覧ください。
* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLI を使用する場合は、まず、ローカルに CLI をインストールして使用する必要があります。 Azure CLI バージョン 2.0.27 以降を実行する必要があります。 ご利用のバージョンを識別するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)に関するページを参照してください。

#### <a name="create-and-execute-a-template"></a>テンプレートを作成して実行する

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

1. *WorkspaceName*、*ResourceGroupName*、および *WorkspaceLocation* の値を編集します。 *WorkspaceName* の値は、ワークスペース名を含む Log Analytics ワークスペースの完全なリソース ID です。 *WorkspaceLocation* の値は、ワークスペースが定義されているリージョンです。

1. これで、次の PowerShell コマンドを使用して、このテンプレートをデプロイする準備ができました。

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが表示されます。

    ```powershell
    provisioningState       : Succeeded
    ```
   監視を有効にしてから、ハイブリッド コンピューターの正常性の状態とメトリックが表示されるまで、約 10 分かかる場合があります。

## <a name="performance-counters-enabled"></a>パフォーマンス カウンターの有効化
Azure Monitor for VMs では、ソリューションによって使用されるパフォーマンス カウンターを収集するため、Log Analytics ワークスペースを構成します。 次の表に、60 秒ごとに収集される、ソリューションによって構成されるオブジェクトとカウンターを示します。

### <a name="windows-performance-counters"></a>Windows パフォーマンス カウンター

|オブジェクト名 |カウンター名 |
|------------|-------------|
|LogicalDisk |% Free Space |
|LogicalDisk |Avg.Disk sec/Read |
|LogicalDisk |Avg.Disk sec/Transfer |
|LogicalDisk |Avg.Disk sec/Write |
|LogicalDisk |Disk Bytes/sec |
|LogicalDisk |Disk Read Bytes/sec  |
|LogicalDisk |Disk Reads/sec  |
|LogicalDisk |Disk Transfers/sec |
|LogicalDisk |Disk Write Bytes/sec |
|LogicalDisk |Disk Writes/sec |
|LogicalDisk |Free Megabytes |
|メモリ |Available MBytes |
|ネットワーク アダプター |Bytes Received/sec |
|ネットワーク アダプター |Bytes Sent/sec |
|プロセッサ |% Processor Time |

### <a name="linux-performance-counters"></a>Linux パフォーマンス カウンター

|オブジェクト名 |カウンター名 |
|------------|-------------|
|論理ディスク |% Used Space |
|論理ディスク |Disk Read Bytes/sec  |
|論理ディスク |Disk Reads/sec  |
|論理ディスク |Disk Transfers/sec |
|論理ディスク |Disk Write Bytes/sec |
|論理ディスク |Disk Writes/sec |
|論理ディスク |Free Megabytes |
|論理ディスク |Logical Disk Bytes/sec |
|メモリ |Available MBytes Memory |
|ネットワーク |Total Bytes Received |
|ネットワーク |Total Bytes Transmitted |
|プロセッサ |% Processor Time |

## <a name="diagnostic-and-usage-data"></a>診断と使用状況データ
Microsoft は、お客様による Azure Monitor サービスの使用を通して、使用状況とパフォーマンス データを自動的に収集します。 Microsoft はこのデータを使用して、提供するサービスの品質、セキュリティ、整合性の向上に努めています。 正確で効果的なトラブルシューティング機能を提供するために、マップ機能からのデータにはソフトウェアの構成に関する情報が含まれています。たとえば、オペレーティング システムとバージョン、IP アドレス、DNS 名、ワークステーション名などです。 名前や住所などの連絡先情報は収集されません。

データの収集と使用の詳細については、「[Microsoft オンライン サービスのプライバシーに関する声明](https://go.microsoft.com/fwlink/?LinkId=512132)」を参照してください。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]
## <a name="next-steps"></a>次の手順

仮想マシンの監視が有効になったので、Azure Monitor for VMs での分析にこの情報を使用できます。 正常性機能の使用方法については、[Azure Monitor for VMs の正常性の表示](vminsights-health.md)に関する記事をご覧ください。 検出されたアプリケーションの依存関係を表示するには、[Azure Monitor for VMs のマップの表示](vminsights-maps.md)に関する記事をご覧くださいい。
