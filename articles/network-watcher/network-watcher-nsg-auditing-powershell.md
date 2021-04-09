---
title: NSG 監査の自動化 - セキュリティ グループ ビュー
titleSuffix: Azure Network Watcher
description: このページでは、ネットワーク セキュリティ グループの監査を構成する方法について説明します
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 177215775c9e83286aa98872eed0ab211a8f36ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94948750"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Azure Network Watcher のセキュリティ グループ ビューを使用した NSG 監査の自動化

インフラストラクチャのセキュリティ体制の検証は、多くのお客様が直面する課題です。 この課題は Azure の VM にも同様に存在します。 適用するネットワーク セキュリティ グループ (NSG) 規則に基づいて、同様のセキュリティ プロファイルを確保することが重要です。 セキュリティ グループ ビューを使用すれば、NSG 内の VM に適用された規則の一覧を取得できます。 適切な NSG セキュリティ プロファイルを定義し、一週間ごとにセキュリティ グループ ビューを起動して、その出力を適切なプロファイルと比較してレポートを作成することができます。 この方法で、所定のセキュリティ プロファイルに準拠していないすべての VM を簡単に特定できます。

ネットワーク セキュリティ グループについてよくご存じでない場合は、「[ネットワーク セキュリティの概要](../virtual-network/network-security-groups-overview.md)」をご覧ください。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>開始する前に

このシナリオでは、仮想マシンについて返されるセキュリティ グループ ビューの結果と既知の適切な基準を比較します。

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関するページの手順を参照して、Network Watcher を作成済みであることを前提としています。 また、有効な仮想マシンがあるリソース グループを使用することも前提としています。

## <a name="scenario"></a>シナリオ

この記事で説明するシナリオでは、仮想マシンのセキュリティ グループ ビューを取得します。

このシナリオでは次のことを行います。

- 既知の適切な規則セットを取得する
- REST API を使用して仮想マシンを取得する
- 仮想マシンのセキュリティ グループ ビューの取得
- 応答を評価する

## <a name="retrieve-rule-set"></a>規則セットの取得

この例の最初の手順では、既存の基準を処理します。 次の例は、`Get-AzNetworkSecurityGroup` コマンドレットを使用して既存のネットワーク セキュリティ グループから抽出した json です。これをこの例の基準として使用します。

```json
[
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "3389",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1000,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "default-allow-rdp",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/default-allow-rdp"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "111",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1010,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "MyRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/MyRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "112",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1020,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "My2ndRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/My2ndRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "5672",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Deny",
        "Priority":  1030,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "ThisRuleNeedsToStay",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/ThisRuleNeedsToStay"
    }
]
```

## <a name="convert-rule-set-to-powershell-objects"></a>規則セットから PowerShell オブジェクトへの変換

この手順では、この例のネットワーク セキュリティ グループで想定される規則が含まれている、先ほど作成した json ファイルを読み込みます。

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Network Watcher の取得

次の手順では、Network Watcher インスタンスを取得します。 `$networkWatcher` 変数は、`AzNetworkWatcherSecurityGroupView` コマンドレットに渡されます。

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
```

## <a name="get-a-vm"></a>VM の取得

`Get-AzNetworkWatcherSecurityGroupView` コマンドレットを実行する仮想マシンが必要です。 次の例では VM オブジェクトを取得します。

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>セキュリティ グループ ビューの取得

次に、セキュリティ グループ ビューの結果を取得します。 この結果は、先ほど表示した "基準" の json と比較されます。

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>結果の分析

応答は、ネットワーク インターフェイスごとにグループ分けされます。 返された各種の規則は有効であり、既定のセキュリティ規則です。 この結果は、適用方法 (サブネットまたは仮想 NIC) によってさらに細かく分けられます。

次の PowerShell スクリプトでは、セキュリティ グループ ビューの結果を既存の NSG の出力と比較します。 次の例は、`Compare-Object` コマンドレットを使用して結果を比較する方法の単純な例です。

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

次の例は結果です。 最初の規則セットにあった 2 つの規則が比較では見つからなかったことがわかります。

```
Name                     : My2ndRuleDoNotDelete
Description              : 
Protocol                 : *
SourcePortRange          : *
DestinationPortRange     : 112
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Allow
Priority                 : 1020
Direction                : Inbound
SideIndicator            : <=

Name                     : ThisRuleNeedsToStay
Description              : 
Protocol                 : TCP
SourcePortRange          : *
DestinationPortRange     : 5672
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Deny
Priority                 : 1030
Direction                : Inbound
SideIndicator            : <=
```

## <a name="next-steps"></a>次のステップ

設定が変更されている場合は、[ネットワーク セキュリティ グループの管理](../virtual-network/manage-network-security-group.md)に関するページを参照して、問題があるネットワーク セキュリティ グループとセキュリティ規則を詳しく調べます。