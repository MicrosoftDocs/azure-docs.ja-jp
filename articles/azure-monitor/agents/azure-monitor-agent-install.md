---
title: Azure Monitor エージェントのインストール
description: Azure 仮想マシンと Azure Arc 対応サーバーに Azure Monitor エージェント (AMA) をインストールするためのオプション。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: b2f91f0036a86151588c8c138dac5421ad54e18e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586422"
---
# <a name="install-the-azure-monitor-agent-preview"></a>Azure Monitor エージェントをインストールする (プレビュー)
この記事では、Azure 仮想マシンと Azure Arc 対応サーバーの両方に [Azure Monitor エージェント](azure-monitor-agent-overview.md) をインストールするために現在使用できるさまざまなオプションについて説明し、エージェントが収集するデータを定義する[データ収集ルールとの関連付け](data-collection-rule-azure-monitor-agent.md)を作成するオプションについても説明します。

## <a name="prerequisites"></a>前提条件
Azure Monitor エージェントをインストールする前に、次の前提条件を満たす必要があります。

- [マネージド システム ID](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) が Azure 仮想マシンで有効になっている必要があります。 これは、Azure Arc 対応サーバーでは必要ありません。 [Azure portal でデータ収集ルールを作成して割り当てる](#install-with-azure-portal)プロセスの一部としてエージェントがインストールされている場合は、システム ID が自動的に有効になります。
- 仮想マシンの仮想ネットワークで [AzureResourceManager サービス タグ](../../virtual-network/service-tags-overview.md)を有効にする必要があります。

## <a name="virtual-machine-extension-details"></a>仮想マシン拡張機能の詳細
Azure Monitor エージェントは、次の表に示す詳細で [Azure VM 拡張機能](../../virtual-machines/extensions/overview.md)として実装されます。 この記事で説明されているものを含め、仮想マシン拡張機能をインストールする、どの方法でもインストールできます。

| プロパティ | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft.Azure.Monitor  | Microsoft.Azure.Monitor |
| 型      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |


## <a name="install-with-azure-portal"></a>Azure portal を使用してインストールする
Azure portal を使用して Azure Monitor エージェントをインストールするには、Azure portal で[データ収集ルールを作成する](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal)プロセスに従います。 これにより、1 つ以上の Azure 仮想マシンまたは Azure Arc 対応サーバーにデータ収集ルールを関連付けることができます。 エージェントが、まだインストールされていないあらゆる仮想マシンにインストールされます。


## <a name="install-with-resource-manager-template"></a>Resource Manager テンプレートを使用してインストールする
Resource Manager テンプレートを使用して、Azure 仮想マシンおよび Azure Arc 対応サーバーに Azure Monitor エージェントをインストールし、データ収集ルールとの関連付けを作成することができます。 関連付けを作成する前に、データ収集ルールを作成する必要があります。

エージェントをインストールし、関連付けを作成するためのサンプル テンプレートを、以下から取得します。 

- [Azure Monitor エージェント (Azure および Azure Arc) をインストールするためのテンプレート](../agents/resource-manager-agent.md#azure-monitor-agent-preview) 
- [データ収集ルールとの関連付けを作成するためのテンプレート](./resource-manager-data-collection-rules.md)

次のコマンドのような、[Resource Manager テンプレートのデプロイ方法のいずれか](../../azure-resource-manager/templates/deploy-powershell.md)を使用して、テンプレートをインストールします。

# <a name="powershell"></a>[PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[CLI](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>PowerShell を使用したインストール
仮想マシン拡張機能を追加するための PowerShell コマンドを使用して、Azure 仮想マシンと Azure Arc 対応サーバーに Azure Monitor エージェントをインストールすることができます。 

### <a name="azure-virtual-machines"></a>Azure の仮想マシン
次の PowerShell コマンドを使用して、Azure の仮想マシンに Azure Monitor エージェントをインストールします。
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0
```
---

### <a name="azure-arc-enabled-servers"></a>Azure Arc 対応サーバー
次の PowerShell コマンドを使用して Azure Monitor エージェントを Azure Arc 対応サーバーにインストールします。
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <virtual-machine-name> -Location <location>
```
---
## <a name="azure-cli"></a>Azure CLI
仮想マシン拡張機能を追加するための Azure CLI コマンドを使用して、Azure 仮想マシンと Azure Arc 対応サーバーに Azure Monitor エージェントをインストールすることができます。 

### <a name="azure-virtual-machines"></a>Azure の仮想マシン
次の CLI コマンドを使用して、Azure 仮想マシンに Azure Monitor エージェントをインストールします。
# <a name="windows"></a>[Windows](#tab/CLIWindows)
```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinux)
```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---
### <a name="azure-arc-enabled-servers"></a>Azure Arc 対応サーバー
次の CLI コマンドを使用して Azure Monitor エージェントを Azure Arc 対応サーバーにインストールします。

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---


## <a name="next-steps"></a>次のステップ

- [データ収集ルールを作成し](data-collection-rule-azure-monitor-agent.md)、エージェントからデータを収集して Azure Monitor に送信します。
