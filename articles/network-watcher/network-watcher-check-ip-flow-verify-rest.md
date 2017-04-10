---
title: "Azure Network Watcher の IP フロー検証を使用したトラフィックの検証 - REST | Microsoft Docs"
description: "この記事では、仮想マシンから送受信されるトラフィックが許可されているか拒否されているかを確認する方法について説明します"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 3307a79f-03be-46a0-aaaf-b2079cb5f3b2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 18b98300ee103e3f3118f6db4e1c1a0e169eecad
ms.lasthandoff: 03/04/2017


---
# <a name="check-if-traffic-is-allowed-or-denied-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Azure Network Watcher のコンポーネントである IP フロー検証を使用して、トラフィックが許可されているか拒否されているかを確認する

> [!div class="op_single_selector"]
> - [Azure ポータル](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST API](network-watcher-check-ip-flow-verify-rest.md)

IP フロー検証は Network Watcher の機能であり、仮想マシンから送受信されるトラフィックが許可されているかどうかを確認できます。 検証は、受信または送信トラフィックに対して実行できます。 このシナリオは仮想マシンが現在、外部リソースまたはバックエンドと通信可能な状態にあるかを確認する際に役立ちます。 IP フロー検証を使用すると、ネットワーク セキュリティ グループ (NSG) 規則が適切に構成されているかを検証し、NSG 規則によってブロックされているフローのトラブルシューティングを行うことができます。 ブロック対象のトラフィックが NSG により適切にブロックされているかどうかを確認することも、IP フロー検証を使用する別の理由として挙げられます。

## <a name="before-you-begin"></a>開始する前に

PowerShell を使用している REST API を呼び出すには、ARMClient を使用します。 ARMClient は、[Chocolatey 上の ARMClient](https://chocolatey.org/packages/ARMClient) に関するページの chocolatey 上にあります。

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関するページの手順に従って、Network Watcher を作成済みであることを前提としています。

## <a name="scenario"></a>シナリオ

このシナリオでは、IP フロー検証を使用して、仮想マシンがポート 443 で別のマシンと通信可能かどうかを確認します。 トラフィックが拒否されている場合は、そのトラフィックを拒否するセキュリティ規則が返されます。 IP フロー検証の詳細については、[IP フロー検証の概要](network-watcher-ip-flow-verify-overview.md)をご覧ください。

このシナリオでは次のことを行います。

* 仮想マシンの取得
* IP フロー検証の呼び出し
* 検証結果

## <a name="log-in-with-armclient"></a>ARMClient でログインする

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>仮想マシンの取得

次のスクリプトを実行して、仮想マシンを返します。 次のコードには変数の値が必要です。

* **subscriptionId**: 使用するサブスクリプション ID。
* **resourceGroupName** - 仮想マシンを含むリソース グループの名前。

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

必要な情報は型 `Microsoft.Compute/virtualMachines` の後に続く ID です。 結果は次のコード サンプルのようになります。

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft
.Network/networkInterfaces/contosovm842"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Com
pute/virtualMachines/ContosoVM/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="call-ip-flow-verify"></a>IP フロー検証の呼び出し

次の例では、指定された仮想マシンのトラフィックを検証する要求を作成します。 トラフィックが許可または拒否されているかが応答により返されます。 トラフィックが拒否されている場合は、どの規則がトラフィックをブロックしているかが返されます。

> [!NOTE]
> IP フロー検証では、VM リソースを割り当てる必要があります。

スクリプトには仮想マシンのリソース ID と仮想マシン上のネットワーク インターフェイス カードのリソース ID が必要です。 これらの値は前述の出力で指定されています。

> [!Important]
> Network Watcher REST の呼び出しでは、要求 URI のリソース グループ名は Network Watcher インスタンスを含むもので、診断アクションを実行しているリソースではありません。

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$vmName = "<vm name>"
$vmNICName = "<vm NIC name>"
$direction = "<direction of traffic>" # Examples are: Inbound or Outbound
$localIP = "<source IP>"
$localPort = "<source Port>"
$remoteIP = "<destination IP>"
$remotePort = "<destination Port>" # Examples are: 80, or 80-120
$protocol = "<UDP, TCP or *>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.compute/virtualMachine/${vmName}
$targetNic = "<uri of target nic resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkInterfaces/${vmNICName}

$requestBody = @"
{
    'targetResourceId':  '$targetUri',
    'direction':  '$direction',
    'protocol':  '$protocol',
    'localPort':  '$localPort',
    'remotePort':  '$remotePort',
    'localIPAddress':  '$localIP',
    'remoteIPAddress':  '$remoteIP',
    'targetNICResourceId':  '$targetNic'
}
"@
        
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/ipFlowVerify?api-version=2016-12-01" $requestBody -verbose
```

## <a name="understanding-the-results"></a>結果について

取得した応答から、トラフィックが許可されたか拒否されたかがわかります。 応答は次の例のいずれかのようになります。

**許可**

```json
{
  "access": "Allow",
  "ruleName": "defaultSecurityRules/AllowInternetOutBound"
}
```

**拒否**

```json
{
  "access": "Deny",
  "ruleName": "defaultSecurityRules/DefaultInboundDenyAll"
}
```

## <a name="next-steps"></a>次のステップ

ブロックされるべきでないトラフィックがブロックされている場合は、[ネットワーク セキュリティ グループの管理](../virtual-network/virtual-network-manage-nsg-arm-portal.md)に関する記事を参照し、Network Security Groups について確認してください。













