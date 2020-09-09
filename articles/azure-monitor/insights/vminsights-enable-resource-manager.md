---
title: Resource Manager テンプレートを使用して Azure Monitor for VMs を有効にする
description: この記事では、Azure PowerShell または Azure Resource Manager テンプレートを使用して、1 つ以上の Azure 仮想マシンまたは仮想マシン スケール セットで Azure Monitor for VMs を有効にする方法について説明します。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 89a9a1b762e02237a8ee08dca5d6eedefabaafbb
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87328027"
---
# <a name="enable-azure-monitor-for-vms-using-resource-manager-templates"></a>Resource Manager テンプレートを使用して Azure Monitor for VMs を有効にする
この記事では、Resource Manager テンプレートを使用して 1 つの仮想マシンまたは仮想マシン スケール セットに対して Azure Monitor for VMs を有効にする方法について説明します。 この手順は、次に対して使用できます。

- Azure 仮想マシン
- Azure 仮想マシン スケール セット
- Azure Arc に接続されているハイブリッド仮想マシン

## <a name="prerequisites"></a>前提条件

- [Log Analytics ワークスペースを作成して構成](vminsights-configure-workspace.md)します。 
- 「[サポートされるオペレーティング システム](vminsights-enable-overview.md#supported-operating-systems)」を参照して、有効にする仮想マシンまたは仮想マシン スケール セットのオペレーティング システムがサポートされていることを確認します。 

## <a name="resource-manager-templates"></a>Resource Manager テンプレート

仮想マシンまたは仮想マシン スケール セットをオンボードするための、Azure Resource Manager テンプレートの例が用意されています。 これらのテンプレートには、既存のリソースに対する監視を有効にして、監視が有効な新しいリソースを作成するために使用できるシナリオが含まれています。

>[!NOTE]
>テンプレートは、有効になっている仮想マシンまたは仮想マシン スケール セットと同じリソース グループにデプロイする必要があります。


Azure Resource Manager テンプレートはアーカイブ ファイル (.zip) として提供さ、GitHub リポジトリから[ダウンロード](https://aka.ms/VmInsightsARMTemplates)できます。 このファイルには各デプロイ シナリオを表すフォルダーが含まれ、各フォルダーにはテンプレートとパラメーター ファイルが格納されています。 実行前に、パラメーター ファイルを変更し、必須の値を指定します。 

ダウンロード ファイルには、さまざまなシナリオに対応した、以下のテンプレートが含まれています。

- **ExistingVmOnboarding** テンプレートでは、仮想マシンが既に存在する場合に Azure Monitor for VMs を有効にします。
- **NewVmOnboarding** テンプレートでは、仮想マシンを作成し、これを監視するために Azure Monitor for VMs を有効にします。
- **ExistingVmssOnboarding** テンプレートでは、仮想マシン スケール セットが既に存在する場合に Azure Monitor for VMs を有効にします。
- **NewVmssOnboarding** テンプレートでは、仮想マシン スケール セットを作成し、これを監視するために Azure Monitor for VMs を有効にします。
- **ConfigureWorkspace** テンプレートでは、Linux および Windows オペレーティング システム パフォーマンス カウンターのソリューションと収集を有効にすることで、Azure Monitor for VMs をサポートする Log Analytics ワークスペースを構成します。

>[!NOTE]
>仮想マシン スケール セットが既に存在し、アップグレード ポリシーが**手動**に設定されている場合、**ExistingVmssOnboarding** Azure Resource Manager テンプレートを実行しても、これらのインスタンスに対して Azure Monitor for VMs は既定では有効になりません。 手動でインスタンスをアップグレードする必要があります。

## <a name="deploy-templates"></a>テンプレートのデプロイ
テンプレートは、PowerShell および CLI を使用した次の例を含め、[Resource Manager テンプレートのデプロイ方法のいずれか](../../azure-resource-manager/templates/deploy-powershell.md)を利用してデプロイすることができます。

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```


```azurecli
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```



## <a name="next-steps"></a>次のステップ

これで、仮想マシンに対する監視が有効になったので、この情報を Azure Monitor for VMs での分析に使用できます。

- 検出されたアプリケーションの依存関係を表示するには、[Azure Monitor for VMs のマップの表示](vminsights-maps.md)に関する記事をご覧くださいい。

- VM のパフォーマンスでのボトルネックや全体的な使用率を識別するには、[Azure VM のパフォーマンスの表示](vminsights-performance.md)に関する記事を参照してください。
