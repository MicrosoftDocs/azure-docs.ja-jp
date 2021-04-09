---
title: Resource Manager テンプレートを使用して VM insights を有効にする
description: この記事では、Azure PowerShell または Azure Resource Manager テンプレートを使用して、1 つ以上の Azure 仮想マシンまたは仮想マシン スケール セットで VM insights を有効にする方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: fc0c304a3fea81f44e01d3e815f34e44728ea42e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031874"
---
# <a name="enable-vm-insights-using-resource-manager-templates"></a>Resource Manager テンプレートを使用して VM insights を有効にする
この記事では、Resource Manager テンプレートを使用して 1 つの仮想マシンまたは仮想マシン スケール セットに対して VM insights を有効にする方法について説明します。 この手順は、次に対して使用できます。

- Azure 仮想マシン
- Azure 仮想マシン スケール セット
- Azure Arc に接続されたハイブリッド仮想マシン

## <a name="prerequisites"></a>前提条件

- [Log Analytics ワークスペースを作成して構成](./vminsights-configure-workspace.md)します。 
- 「[サポートされるオペレーティング システム](./vminsights-enable-overview.md#supported-operating-systems)」を参照して、有効にする仮想マシンまたは仮想マシン スケール セットのオペレーティング システムがサポートされていることを確認します。 

## <a name="resource-manager-templates"></a>Resource Manager テンプレート

仮想マシンまたは仮想マシン スケール セットをオンボードするための、Azure Resource Manager テンプレートの例が用意されています。 これらのテンプレートには、既存のリソースに対する監視を有効にして、監視が有効な新しいリソースを作成するために使用できるシナリオが含まれています。

>[!NOTE]
>テンプレートは、有効になっている仮想マシンまたは仮想マシン スケール セットと同じリソース グループにデプロイする必要があります。


Azure Resource Manager テンプレートはアーカイブ ファイル (.zip) として提供さ、GitHub リポジトリから[ダウンロード](https://aka.ms/VmInsightsARMTemplates)できます。 このファイルには各デプロイ シナリオを表すフォルダーが含まれ、各フォルダーにはテンプレートとパラメーター ファイルが格納されています。 実行前に、パラメーター ファイルを変更し、必須の値を指定します。 

ダウンロード ファイルには、さまざまなシナリオに対応した、以下のテンプレートが含まれています。

- **ExistingVmOnboarding** テンプレートを使用すると、仮想マシンが既に存在する場合に VM insights が有効になります。
- **NewVmOnboarding** テンプレートを使用すると、仮想マシンを作成し、VM insights によって監視できるようになります。
- **ExistingVmssOnboarding** テンプレートを使用すると、仮想マシン スケール セットが既に存在する場合に VM insights が有効になります。
- **NewVmssOnboarding** テンプレートを使用すると、仮想マシン スケール セットを作成し、それらを VM insights によって監視できるようになります。
- **ConfigureWorkspace** テンプレートを使用すると、Linux および Windows オペレーティング システム パフォーマンス カウンターのソリューションと収集が有効になり、VM insights をサポートする Log Analytics ワークスペースを構成することができます。

>[!NOTE]
>仮想マシン スケール セットが既に存在し、アップグレード ポリシーが **手動** に設定されている場合、**ExistingVmssOnboarding** Azure Resource Manager テンプレートを実行しても、これらのインスタンスに対して VM insights は既定では有効になりません。 手動でインスタンスをアップグレードする必要があります。

## <a name="deploy-templates"></a>テンプレートのデプロイ
テンプレートは、PowerShell および CLI を使用した次の例を含め、[Resource Manager テンプレートのデプロイ方法のいずれか](../../azure-resource-manager/templates/deploy-powershell.md)を利用してデプロイすることができます。

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```


```azurecli
az deployment group create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```



## <a name="next-steps"></a>次のステップ

これで、仮想マシンに対する監視が有効になったので、この情報を VM insights での分析に使用できます。

- 検出されたアプリケーションの依存関係を表示するには、[VM insights マップの表示](vminsights-maps.md)に関する記事を参照してください。

- VM のパフォーマンスでのボトルネックや全体的な使用率を識別するには、[Azure VM のパフォーマンスの表示](vminsights-performance.md)に関する記事を参照してください。