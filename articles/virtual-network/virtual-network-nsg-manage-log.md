---
title: "NSG に関する操作、イベント、カウンターの監視 | Microsoft Docs"
description: "NGS に関するカウンター、イベント、操作のログ記録を有効にする方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: f907f388ecb897c05e822f2abd5431b3976b7987
ms.openlocfilehash: 10581234a4475d0d3b32c7891fcf97eed55f7a1d
ms.lasthandoff: 03/02/2017


---
# <a name="log-analytics-for-network-security-groups-nsgs"></a>ネットワーク セキュリティ グループ (NSG) のためのログ分析

NSG に対して、以下の診断ログ カテゴリを有効にできます。

* **イベント:** MAC アドレスに基づいて VM とインスタンス ロールに適用される NSG ルールに関するエントリが含まれます。 これらのルールの状態は 60 秒ごとに収集されます。
* **ルール カウンター:** トラフィックを拒否または許可するために各 NSG ルールが適用された回数に関するエントリが含まれます。

> [!NOTE]
> 診断ログは、Azure Resource Manager デプロイメント モデルでデプロイされた NSG についてのみ使用できます。 クラシック デプロイメント モデルでデプロイされた NSG については診断ログを有効にできません。 2 つのモデルについて理解を深めるには、[Azure デプロイメント モデルの理解](../resource-manager-deployment-model.md)に関する記事をご覧ください。

どちらの Azure デプロイメント モデルで作成された NSG に対しても、アクティビティ ログ (以前の監査ログまたは操作ログ) が既定で有効になります。 NSG で完了した操作を確認するには、アクティビティ ログで次のリソース タイプを含むエントリを探します。 

- Microsoft.ClassicNetwork/networkSecurityGroups 
- Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
- Microsoft.Network/networkSecurityGroups
- Microsoft.Network/networkSecurityGroups/securityRules 

アクティビティ ログの詳細については、「[Azure アクティビティ ログの概要](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)」をご覧ください。 

## <a name="enable-diagnostic-logging"></a>診断ログの有効化

診断ログは、データを収集する*各* NSG に対して有効にする必要があります。 診断ログに対して指定できる送信先は、「[Azure 診断ログの概要](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)」で説明しています。 既存の NSG がない場合は、[ネットワーク セキュリティ グループの作成](virtual-networks-create-nsg-arm-pportal.md)に関する記事の手順に従って NSG を作成します。 NSG の診断ログは、次のいずれかの方法で有効にできます。

### <a name="azure-portal"></a>Azure ポータル

ポータルを使用してログを有効にするには、[ポータル](https://portal.azure.com)にログインします。 **[その他のサービス]** をクリックし、*ネットワーク サービス グループ*を入力します。 ログを有効にする NSG を選択します。 コンピューティング以外のリソースに対する手順については、[ポータルでの診断ログの有効化](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#enable-diagnostic-logs-in-the-portal)に関する記事をご覧ください。 **[NetworkSecurityGroupEvent]** と **[NetworkSecurityGroupRuleCounter]** のいずれかまたは両方のログ カテゴリを選択します。

### <a name="powershell"></a>PowerShell

PowerShell を使用してログを有効にするには、[PowerShell での診断ログの有効化](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#enable-diagnostic-logs-via-powershell)に関する記事の手順に従います。 この記事のコマンドを入力する前に、次の情報を評価してください。

- `-ResourceId` パラメーターに使用する値は、次の [テキスト] を適切な値に置き換えてからコマンド `Get-AzureRmNetworkSecurityGroup -Name [nsg-name] -ResourceGroupName [resource-group-name]` を入力して調べることができます。 このコマンドからの ID の出力は、*/subscriptions/[Subscription Id]/resourceGroups/[resource-group]/providers/Microsoft.Network/networkSecurityGroups/[NSG name]* のようになります。
- ログ カテゴリのデータのみを収集する場合は、記事のコマンドの末尾に `-Categories [category]` を追加し、カテゴリには *NetworkSecurityGroupEvent* または *NetworkSecurityGroupRuleCounter* のいずれかを指定します。 `-Categories` パラメーターを使用しない場合、両方のログ カテゴリに対してデータ収集が有効になります。

### <a name="azure-command-line-interface-cli"></a>Azure コマンド ライン インターフェイス (CLI)

CLI を使用してログを有効にするには、[CLI での診断ログの有効化](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#enable-diagnostic-logs-via-cli)に関する記事の手順に従います。 この記事のコマンドを入力する前に、次の情報を評価してください。

- `-ResourceId` パラメーターに使用する値は、次の [テキスト] を適切な値に置き換えてからコマンド `azure network nsg show [resource-group-name] [nsg-name]` を入力して調べることができます。 このコマンドからの ID の出力は、*/subscriptions/[Subscription Id]/resourceGroups/[resource-group]/providers/Microsoft.Network/networkSecurityGroups/[NSG name]* のようになります。
- ログ カテゴリのデータのみを収集する場合は、記事のコマンドの末尾に `-Categories [category]` を追加し、カテゴリには *NetworkSecurityGroupEvent* または *NetworkSecurityGroupRuleCounter* のいずれかを指定します。 `-Categories` パラメーターを使用しない場合、データ収集は両方のログ カテゴリに対して有効になります。

## <a name="logged-data"></a>ログ記録されるデータ

どちらのログでも、JSON 形式のデータが書き込まれます。 各ログ タイプで書き込まれる固有のデータを以下のセクションに示します。

### <a name="event-log"></a>イベント ログ
このログには、MAC アドレスに基づいて VM とクラウド サービス ロール インスタンスに適用される NSG ルールに関する情報が含まれます。 各イベントについて次の例のようなデータがログ記録されます。

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-B791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"3389-3389",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter-log"></a>ルール カウンター ログ

このログには、リソースに適用される各ルールに関する情報が含まれます。 ルールが適用されるたびに、次の例のようなデータがログ記録されます。

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]TESTRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

## <a name="view-and-analyze-logs"></a>ログの表示と分析

アクティビティ ログ データの表示方法については、「[Azure アクティビティ ログの概要](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)」をご覧ください。 診断ログ データの表示方法については、「[Azure 診断ログの概要](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)」をご覧ください。 診断データを Log Analytics に送信すると、[Azure ネットワーク セキュリティ グループ分析](../log-analytics/log-analytics-azure-networking-analytics.md) (プレビュー) 管理ソリューションを使用してより深い洞察を得ることができます。 

