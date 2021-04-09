---
title: ネットワーク セキュリティ グループの診断リソース ログ
titlesuffix: Azure Virtual Network
description: Azure ネットワーク セキュリティ グループのイベントとルール カウンター診断リソース ログを有効にする方法を説明します。
services: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: bb078b9738e995a1c507f7934a7dd64f075d5fe0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100596528"
---
# <a name="resource-logging-for-a-network-security-group"></a>ネットワーク セキュリティ グループのリソース ログ

ネットワーク セキュリティ グループ (NSG) には、仮想ネットワーク サブネットとネットワーク インターフェイスのどちらか一方または両方へのトラフィックを許可または拒否するルールが含まれています。 

NSG に対するログを有効にすると、次の種類のリソース ログ情報を収集できます。

* **イベント:** MAC アドレスに基づいて、VM に適用される NSG ルールに関するエントリがログに記録されます。
* **ルール カウンター:** トラフィックを拒否または許可するために各 NSG ルールが適用された回数に関するエントリが含まれます。 これらのルールの状態は、300 秒ごとに収集されます。

リソース ログは、Azure Resource Manager デプロイ モデルでデプロイされた NSG についてのみ使用できます。 クラシック デプロイ モデルでデプロイされた NSG についてはリソース ログを有効にできません。 2 つのモデルについて理解を深めるには、[Azure デプロイ モデルの理解](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。

リソース ログは、診断データの収集対象とする "*個々の*" NSG に対して個別に有効にします。 アクティビティ (操作) ログに関心がある場合は、Azure の [アクティビティ ログ](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページをご覧ください。

## <a name="enable-logging"></a>ログの有効化

[Azure portal](#azure-portal)、[PowerShell](#powershell)、または [Azure CLI](#azure-cli) を使用して、リソース ログを有効にすることができます。

### <a name="azure-portal"></a>Azure portal

1. [ポータル](https://portal.azure.com)にサインインします。
2. **[すべてのサービス]** を選び、「*ネットワーク セキュリティ グループ*」と入力します。 検索結果に **[ネットワーク セキュリティ グループ]** が表示されたら、それを選びます。
3. ログを有効にする NSG を選択します。
4. 次の図に示すように、 **[監視]** で **[診断ログ]** を選び、 **[診断をオンにする]** を選びます。

   ![診断の有効化](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. **[診断設定]** で、次の情報を入力するか選んだ後、 **[保存]** を選びます。

    | 設定                                                                                     | 値                                                          |
    | ---------                                                                                   |---------                                                       |
    | 名前                                                                                        | 任意の名前です。  例: *myNsgDiagnostics*      |
    | **ストレージ アカウントへのアーカイブ**、**イベント ハブへのストリーム**、**Log Analytics への送信** | 必要なだけいくつでも保存先を選択できます。 それぞれについて詳しくは、「[ログの保存先](#log-destinations)」をご覧ください。                                                                                                                                           |
    | LOG                                                                                         | いずれか一方または両方のログ カテゴリを選びます。 各カテゴリでログに記録されるデータについて詳しくは、「[ログのカテゴリ](#log-categories)」をご覧ください。                                                                                                                                             |
6. ログを表示して分析します。 詳しくは、「[ログの表示と分析](#view-and-analyze-logs)」をご覧ください。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下のコマンドは、[Azure Cloud Shell](https://shell.azure.com/powershell) で、またはコンピューターから PowerShell を実行することで実行できます。 Azure Cloud Shell は無料の対話型シェルです。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 お使いのコンピューターから PowerShell を実行する場合は、Azure PowerShell モジュール、バージョン 1.0.0 以降が必要です。 コンピューターで `Get-Module -ListAvailable Az` を実行して、インストールされているバージョンを確認してください。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、[必要なアクセス許可](virtual-network-network-interface.md#permissions)を持つアカウントで `Connect-AzAccount` を実行して Azure にサインインする必要もあります。

リソース ログを有効にするには、既存の NSG の ID が必要です。 既存の NSG がない場合は、[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) を実行して作成できます。

[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) を使用して、リソース ログを有効にするネットワーク セキュリティ グループを取得します。 たとえば、*myResourceGroup* という名前のリソース グループに存在する *myNsg* という名前の NSG を取得するには、次のコマンドを入力します。

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

3 種類の保存先にリソース ログを書き込むことができます。 詳しくは、「[ログの保存先](#log-destinations)」をご覧ください。 この記事では、例として *Log Analytics* の保存先にログを送信します。 [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) を使用して、既存の Log Analytics ワークスペースを取得します。 たとえば、*myWorkspaces* という名前のリソース グループに存在する *myWorkspace* という名前の既存のワークスペースを取得するには、次のコマンドを入力します。

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

既存のワークスペースがない場合は、[New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) を使用して作成できます。

2 つのカテゴリのログを有効にすることができます。 詳しくは、「[ログのカテゴリ](#log-categories)」をご覧ください。 [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) を使用して、NSG に対するリソース ログを有効にします。 次の例では、NSG の ID と前に取得したワークスペース を使って、NSG のイベントとカウンター カテゴリ データの両方をワークスペースに記録します。

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

両方ではなくどちらか一方のカテゴリのデータだけを記録したい場合は、前のコマンドに `-Categories` オプションを追加し、続けて *NetworkSecurityGroupEvent* または  *NetworkSecurityGroupRuleCounter* を指定します。 Log Analytics ワークスペース以外の[保存先](#log-destinations)にログを記録したい場合は、Azure [ストレージ アカウント](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)または [Event Hub](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs) の適切なパラメーターを使います。

ログを表示して分析します。 詳しくは、「[ログの表示と分析](#view-and-analyze-logs)」をご覧ください。

### <a name="azure-cli"></a>Azure CLI

以下のコマンドは、[Azure Cloud Shell](https://shell.azure.com/bash) で、またはコンピューターから Azure CLI を実行することで実行できます。 Azure Cloud Shell は無料の対話型シェルです。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 コンピューターから CLI を実行する場合は、バージョン 2.0.38 以降が必要です。 コンピューターで `az --version` を実行して、インストールされているバージョンを確認してください。 アップグレードする必要がある場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。 CLI をローカルで実行している場合、[必要なアクセス許可](virtual-network-network-interface.md#permissions)を持つアカウントで `az login` を実行して Azure にサインインする必要もあります。

リソース ログを有効にするには、既存の NSG の ID が必要です。 既存の NSG がない場合は、[az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) を使用して作成できます。

[az network nsg show](/cli/azure/network/nsg#az-network-nsg-show) を使用して、リソース ログを有効にするネットワーク セキュリティ グループを取得します。 たとえば、*myResourceGroup* という名前のリソース グループに存在する *myNsg* という名前の NSG を取得するには、次のコマンドを入力します。

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

3 種類の保存先にリソース ログを書き込むことができます。 詳しくは、「[ログの保存先](#log-destinations)」をご覧ください。 この記事では、例として *Log Analytics* の保存先にログを送信します。 詳しくは、「[ログのカテゴリ](#log-categories)」をご覧ください。

[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) を使用して、NSG に対するリソース ログを有効にします。 次の例では、*myWorkspaces* というリソース グループ内に存在する *myWorkspace* という既存のワークスペースに対するイベントとカウンター カテゴリ データの両方と、以前に取得した NSG の ID をログに記録します。

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

既存のワークスペースがない場合は、[Azure portal](../azure-monitor/logs/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [PowerShell](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) を使って作成できます。 2 つのカテゴリのログを有効にすることができます。

あるカテゴリまたは他のカテゴリのデータのみをログに記録する場合は、前のコマンドでデータをログに記録しないカテゴリを削除します。 Log Analytics ワークスペース以外の[保存先](#log-destinations)にログを記録したい場合は、Azure [ストレージ アカウント](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)または [Event Hub](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs) の適切なパラメーターを使います。

ログを表示して分析します。 詳しくは、「[ログの表示と分析](#view-and-analyze-logs)」をご覧ください。

## <a name="log-destinations"></a>ログの保存先

診断データは次のようにすることができます。
- 監査や手動での検査に使用するために、[Azure ストレージ アカウントに書き込みます](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)。 リソース診断設定を使用して、リテンション期間 (日数) を指定できます。
- サード パーティーのサービスや PowerBI などのカスタム分析ソリューションで取り込むために、[Event ハブにストリーミングします](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)。
- [Azure Monitor ログに書き込みます](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)。

## <a name="log-categories"></a>ログのカテゴリ

次のログ カテゴリの JSON 形式データが書き込まれます。

### <a name="event"></a>Event

イベント ログには、MAC アドレスに基づいて、VM に適用される NSG ルールに関する情報が含まれます。 各イベントについて、次のようなデータがログに記録されます。 次の例では、IP アドレスが 192.168.1.4 で MAC アドレスが 00-0D-3A-92-6A-7C の仮想マシンのデータが記録されます。

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "priority":"[PRIORITY-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "conditions":{
            "protocols":"[PROTOCOLS-SPECIFIED-IN-RULE]",
            "destinationPortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourcePortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourceIP":"[SOURCE-IP-OR-RANGE-SPECIFIED-IN-RULE]",
            "destinationIP":"[DESTINATION-IP-OR-RANGE-SPECIFIED-IN-RULE]"
            }
        }
}
```

### <a name="rule-counter"></a>ルール カウンター

ルール カウンター ログには、リソースに適用される各ルールに関する情報が含まれます。 ルールが適用されるたびに、次の例のようなデータが記録されます。 次の例では、IP アドレスが 192.168.1.4 で MAC アドレスが 00-0D-3A-92-6A-7C の仮想マシンのデータが記録されます。

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> 通信の送信元 IP アドレスは記録されません。 ただし、NSG の [NSG フロー ログ](../network-watcher/network-watcher-nsg-flow-logging-portal.md)を有効にすることができます。このログでは、すべてのルール カウンター情報だけでなく、通信を開始した送信元 IP アドレスも記録されます。 NSG フロー ログ データは Azure Storage アカウントに書き込まれます。 Azure Network Watcher の [トラフィック分析](../network-watcher/traffic-analytics.md)機能を使って、データを分析することができます。

## <a name="view-and-analyze-logs"></a>ログの表示と分析

リソース ログのデータを表示する方法については、[Azure プラットフォーム ログの概要](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページを参照してください。 診断データの送信先に応じて、次のようになります。
- **Azure Monitor ログ**: [ネットワーク セキュリティ グループ分析](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
)ソリューションを使って、詳細な分析情報を取得できます。 このソリューションは、仮想マシン内のネットワーク インターフェイスのトラフィックを MAC アドレスに従って許可または拒否する NSG ルールの視覚化を提供します。
- **Microsoft Azure Storage アカウント**:データは PT1H.json ファイルに書き込まれます。 各ログは次のパスで見つかります。
  - イベント ログ: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - ルール カウンター ログ: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>次のステップ

- [アクティビティ ログ](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)の詳細について説明します。 どちらの Azure デプロイ モデルで作成された NSG に対しても、アクティビティ ログが既定で有効になります。 NSG で完了した操作を確認するには、アクティビティ ログで次のリソース タイプを含むエントリを探します。
  - Microsoft.ClassicNetwork/networkSecurityGroups
  - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft.Network/networkSecurityGroups
  - Microsoft.Network/networkSecurityGroups/securityRules
- 診断情報に各フローの送信元 IP アドレスを含める方法については、[NSG フロー ログ](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページをご覧ください。
