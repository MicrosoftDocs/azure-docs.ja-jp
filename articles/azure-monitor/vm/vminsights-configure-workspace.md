---
title: VM insights の Log Analytics ワークスペースの構成
description: VM insights によって使用される Log Analytics ワークスペースを作成して構成する方法について説明します。
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.openlocfilehash: 5a0e04772cfc1c9be77c0ad8b32b0e93be8b9f54
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046722"
---
# <a name="configure-log-analytics-workspace-for-vm-insights"></a>VM insights の Log Analytics ワークスペースの構成
VM insights では、Azure Monitor 内の 1 つ以上の Log Analytics ワークスペースからデータを収集します。 エージェントをオンボードする前に、ワークスペースを作成して構成する必要があります。 この記事では、ワークスペースの要件と、それを VM insights 用に構成するための要件について説明します。

## <a name="overview"></a>概要
1 つのサブスクリプションで、要件に応じて任意の数のワークスペースを使用できます。 ワークスペースの唯一の要件は、サポートされている場所に配置され、*VMInsights* ソリューションで構成されることです。

ワークスペースが構成されたら、使用可能なオプションのいずれかを使用して、必要な各エージェントを仮想マシンや仮想マシン スケール セットにインストールしたり、それらがデータを送信するワークスペースを指定したりできます。 VM insights では、そのサブスクリプション内に構成された任意のワークスペースからデータを収集します。

> [!NOTE]
> Azure portal を使用して 1 つの仮想マシンや仮想マシン スケール セットで VM insights を有効にすると、既存のワークスペースを選択するか、新しいワークスペースを作成するオプションが表示されます。 このワークスペースには *VMInsights* ソリューションがインストールされます (まだインストールされていない場合)。 その後、このワークスペースを他のエージェントに使用できます。


## <a name="create-log-analytics-workspace"></a>Log Analytics ワークスペースの作成

>[!NOTE]
>このセクションで説明されている情報は、[Service Map ソリューション](service-map.md)にも適用できます。 

Azure portal で、 **[Log Analytics ワークスペース]** メニューから Log Analytics ワークスペースにアクセスします。

[![[Log Analytics ワークスペース]](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

新しい Log Analytics ワークスペースは、次のいずれかの方法を使用して作成できます。 環境で使用するワークスペースの数の決定や、それらのアクセス戦略を設計する方法に関するガイダンスについては、「[Azure Monitor ログのデプロイの設計](../logs/design-logs-deployment.md)」を参照してください。


* [Azure Portal](../logs/quick-create-workspace.md)
* [Azure CLI](../logs/quick-create-workspace-cli.md)
* [PowerShell](../logs/powershell-workspace-configuration.md)
* [Azure Resource Manager](../logs/resource-manager-workspace.md)

## <a name="supported-regions"></a>サポートされているリージョン
VM insights では、[Log Analytics でサポートされているすべてのリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all) (以下を除く) で Log Analytics ワークスペースをサポートしています。

- ドイツ中西部
- 韓国中部

>[!NOTE]
>Azure VM は任意のリージョンで監視できます。 VM 自体は、Log Analytics ワークスペースでサポートされているリージョンに限定されません。

## <a name="azure-role-based-access-control"></a>Azure ロールベースのアクセス制御
VM insights の機能を有効にしてアクセスするには、ワークスペースに [Log Analytics 共同作成者ロール](../logs/manage-access.md#manage-access-using-azure-permissions)が必要です。 パフォーマンス、正常性、マップ データを表示するには、Azure VM に対する[監視閲覧者ロール](../roles-permissions-security.md#built-in-monitoring-roles)が必要です。 Log Analytics ワークスペースへのアクセスを制御する方法の詳細については、「[ワークスペースを管理する](../logs/manage-access.md)」を参照してください。

## <a name="add-vminsights-solution-to-workspace"></a>ワークスペースに VMInsights ソリューションを追加する
VM insights で Log Analytics ワークスペースを使用するには、そのワークスペースに *VMInsights* ソリューションがインストールされている必要があります。 以降のセクションでは、ワークスペースを構成する方法について説明します。

> [!NOTE]
> ワークスペースに *VMInsights* ソリューションを追加すると、そのワークスペースに接続されているすべての既存の仮想マシンが InsightsMetrics にデータを送信し始めます。 その他のデータ型のデータは、そのワークスペースに接続されているこれらの既存の仮想マシンに依存関係エージェントを追加するまで収集されません。

### <a name="azure-portal"></a>Azure portal
Azure portal を使用して既存のワークスペースを構成するためのオプションは 3 つあります。 次にそれぞれについて説明します。

1 つのワークスペースを構成するには、**Azure Monitor** メニューの **[Virtual Machines]** オプションを選択し、 **[その他のオンボードオプション]** を選択してから **[ワークスペースの構成]** を選択します。 サブスクリプションとワークスペースを選択し、 **[構成]** をクリックします。

[![ワークスペースを構成する](../vm/media/vminsights-enable-policy/configure-workspace.png)](../vm/media/vminsights-enable-policy/configure-workspace.png#lightbox)

複数のワークスペースを構成するには、Azure portal の **[監視]** メニューにある **[Virtual Machines]** メニューの **[ワークスペースの構成]** タブを選択します。 既存のワークスペースの一覧を表示するには、フィルター値を設定します。 有効にする各ワークスペースの横にあるボックスを選択し、 **[選択したものを構成]** をクリックします。

[![[ワークスペースの構成]](../vm/media/vminsights-enable-policy/workspace-configuration.png)](../vm/media/vminsights-enable-policy/workspace-configuration.png#lightbox)


Azure portal を使用して 1 つの仮想マシンや仮想マシン スケール セットで VM insights を有効にすると、既存のワークスペースを選択するか、新しいワークスペースを作成するオプションが表示されます。 このワークスペースには *VMInsights* ソリューションがインストールされます (まだインストールされていない場合)。 その後、このワークスペースを他のエージェントに使用できます。

[![ポータルで 1 つの VM を有効にする](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Resource Manager テンプレート
VM insights の Azure Resource Manager テンプレートはアーカイブ ファイル (.zip) として提供され、GitHub リポジトリから[ダウンロード](https://aka.ms/VmInsightsARMTemplates)できます。 これには、VM insights 用に Log Analytics ワークスペースを構成する **ConfigureWorkspace** という名前のテンプレートが含まれています。 このテンプレートは、次のサンプルの PowerShell および CLI コマンドを含む標準の方法のいずれかを使用してデプロイします。 

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
- エージェントを VM insights に接続するには、[VM insights へのエージェントのオンボード](vminsights-enable-overview.md)に関するページをご覧ください。
- ソリューションからワークスペースに送信されるデータの量を制限するには、「[Azure Monitor での監視ソリューションのターゲット設定 (プレビュー)](../insights/solution-targeting.md)」を参照してください。
