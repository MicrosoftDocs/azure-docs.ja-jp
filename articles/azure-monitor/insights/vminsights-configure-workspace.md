---
title: Azure Monitor for VMs 用に Log Analytics ワークスペースを構成する
description: Azure Monitor for VMs によって使用される Log Analytics ワークスペースを作成して構成する方法について説明します。
ms.subservice: ''
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 40ff7bfe713f401a23dea0b3660be1b7728a1380
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87828559"
---
# <a name="configure-log-analytics-workspace-for-azure-monitor-for-vms"></a>Azure Monitor for VMs 用に Log Analytics ワークスペースを構成する
Azure Monitor for VMs は、Azure Monitor 内の 1 つ以上の Log Analytics ワークスペースからデータを収集します。 エージェントをオンボードする前に、ワークスペースを作成して構成する必要があります。 この記事では、ワークスペースの要件と、それを Azure Monitor for VMs 用に構成するための要件について説明します。

## <a name="overview"></a>概要
1 つのサブスクリプションでは、要件に応じて、任意の数のワークスペースを使用できます。ワークスペースの唯一の要件は、サポートされている場所に配置され、*VMInsights* ソリューションで構成されることです。

ワークスペースが構成されたら、使用可能なオプションのいずれかを使用して VM や VMMS に必要なエージェントをインストールしたり、それらがデータを送信するためのワークスペースを指定したりできます。 Azure Monitor for VMs は、そのサブスクリプション内に構成された任意のワークスペースからデータを収集します。

> [!NOTE]
> Azure portal を使用して 1 つの VM または VMMS で Azure Monitor for VMs を有効にする場合は、既存のワークスペースを選択するか、または新しいワークスペースを作成するオプションが与えられます。 このワークスペースには *VMInsights* ソリューションがインストールされます (まだインストールされていない場合)。 その後、このワークスペースを他のエージェントに使用できます。


## <a name="create-log-analytics-workspace"></a>Log Analytics ワークスペースの作成

>[!NOTE]
>このセクションで説明されている情報は、[Service Map ソリューション](service-map.md)にも適用できます。 

Azure portal で、 **[Log Analytics ワークスペース]** メニューから Log Analytics ワークスペースにアクセスします。

[![[Log Analytics ワークスペース]](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

新しい Log Analytics ワークスペースは、次のいずれかの方法を使用して作成できます。 環境で使用するワークスペースの数の決定や、それらのアクセス戦略を設計する方法に関するガイダンスについては、「[Azure Monitor ログのデプロイの設計](../platform/design-logs-deployment.md)」を参照してください。


* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

## <a name="supported-regions"></a>サポートされているリージョン

Azure Monitor for VMs では、次のリージョン内の Log Analytics ワークスペースがサポートされますが、どのリージョン内の仮想マシンでも監視できます。 仮想マシン自体は、Log Analytics ワークスペースでサポートされているリージョンに限定されません。

- 米国中西部
- 米国西部
- 米国西部 2
- 米国中南部
- 米国東部
- 米国東部 2
- 米国中部
- 米国中北部
- US Gov アリゾナ州
- US Gov バージニア州
- カナダ中部
- 英国南部
- 北ヨーロッパ
- 西ヨーロッパ
- 東アジア
- 東南アジア
- インド中部
- 東日本
- オーストラリア東部
- オーストラリア南東部

## <a name="role-based-access-control"></a>ロールベースのアクセス制御
Azure Monitor for VMs の機能を有効にしてアクセスするには、ワークスペース内の [Log Analytics 共同作成者ロール](../platform/manage-access.md#manage-access-using-azure-permissions)が必要です。 パフォーマンス、正常性、マップ データを表示するには、Azure VM に対する[監視閲覧者ロール](../platform/roles-permissions-security.md#built-in-monitoring-roles)が必要です。 Log Analytics ワークスペースへのアクセスを制御する方法の詳細については、「[ワークスペースを管理する](../platform/manage-access.md)」を参照してください。

## <a name="add-vminsights-solution-to-workspace"></a>ワークスペースに VMInsights ソリューションを追加する
Azure Monitor for VMs で Log Analytics ワークスペースを使用するには、そのワークスペースに *VMInsights* ソリューションがインストールされている必要があります。 以降のセクションでは、ワークスペースを構成する方法について説明します。

> [!NOTE]
> ワークスペースに *VMInsights* ソリューションを追加すると、そのワークスペースに接続されているすべての既存の仮想マシンが InsightsMetrics にデータを送信し始めます。 その他のデータ型のデータは、そのワークスペースに接続されているこれらの既存の仮想マシンに依存関係エージェントを追加するまで収集されません。

### <a name="azure-portal"></a>Azure portal
Azure portal から既存のワークスペースを構成するためのオプションは 3 つあります。

1 つのワークスペースを構成するには、 **[その他のオンボード オプション]** 、 **[ワークスペースの構成]** の順に選択します。 サブスクリプションとワークスペースを選択し、 **[構成]** をクリックします。

[![ワークスペースを構成する](media/vminsights-enable-at-scale-policy/configure-workspace.png)](media/vminsights-enable-at-scale-policy/configure-workspace.png#lightbox)

複数のワークスペースを構成するには、Azure portal の **[監視]** メニューにある **[Virtual Machines]** メニューの **[ワークスペースの構成]** タブを選択します。 既存のワークスペースの一覧を表示するには、フィルター値を設定します。 有効にする各ワークスペースの横にあるボックスを選択し、 **[選択したものを構成]** をクリックします。

[![[ワークスペースの構成]](media/vminsights-enable-at-scale-policy/workspace-configuration.png)](media/vminsights-enable-at-scale-policy/workspace-configuration.png#lightbox)


Azure portal を使用して 1 つの VM または VMMS で Azure Monitor for VMs を有効にする場合は、既存のワークスペースを選択するか、または新しいワークスペースを作成するオプションが与えられます。 このワークスペースには *VMInsights* ソリューションがインストールされます (まだインストールされていない場合)。 その後、このワークスペースを他のエージェントに使用できます。

[![ポータルで 1 つの VM を有効にする](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Resource Manager テンプレート
Azure Monitor for VMs 用の Azure Resource Manager テンプレートは、[GitHub リポジトリからダウンロード](https://aka.ms/VmInsightsARMTemplates)できるアーカイブ ファイル (.zip) で提供されています。 これには、Azure Monitor for VMs 用に Log Analytics ワークスペースを構成する **ConfigureWorkspace** という名前のテンプレートが含まれています。 このテンプレートは、次のサンプルの PowerShell および CLI コマンドを含む標準の方法のいずれかを使用してデプロイします。 

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az deployment group create --name ConfigureWorkspace --resource-group my-resource-group --template-file CreateWorkspace.json  --parameters workspaceResourceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace' workspaceLocation='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzResourceGroupDeployment -Name ConfigureWorkspace -ResourceGroupName my-resource-group -TemplateFile ConfigureWorkspace.json -workspaceResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace -location eastus
```

---



## <a name="next-steps"></a>次のステップ
- Azure Monitor for VMs にエージェントを接続するには、[Azure Monitor for VMs へのエージェントのオンボード](vminsights-enable-overview.md)に関するページを参照してください。
- ソリューションからワークスペースに送信されるデータの量を制限するには、「[Azure Monitor での監視ソリューションのターゲット設定 (プレビュー)](solution-targeting.md)」を参照してください。