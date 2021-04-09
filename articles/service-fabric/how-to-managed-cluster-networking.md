---
title: Service Fabric マネージド クラスターのネットワーク設定を構成する (プレビュー)
description: NSG ルール、RDP ポート アクセス、負荷分散規則などに関して Service Fabric マネージド クラスターを構成する方法について説明します。
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: e17251523c0720665c4c6f5b7811304eebc9923e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743960"
---
# <a name="configure-network-settings-for-service-fabric-managed-clusters-preview"></a>Service Fabric マネージド クラスターのネットワーク設定を構成する (プレビュー)

Service Fabric マネージド クラスターは、既定のネットワーク構成を使用して作成されます。 この構成は、重要なクラスター機能に関する必須の規則と、お客様による構成を容易にするためのいくつかのオプションの規則で構成されています。

既定のネットワーク構成以外に、シナリオのニーズに合わせて変更できるネットワーク規則があります。

## <a name="nsg-rules-guidance"></a>NSG ルールのガイダンス

マネージド クラスター用に新しい NSG ルールを作成するときは、次の点に注意してください。

* NSG ルールの 0 から 999 の優先順位範囲は、Service Fabric マネージド クラスターにより必須機能用に予約されています。 優先順位の値が 1000 未満のカスタム NSG ルールは作成できません。
* 3001 から 4000 の優先順位範囲は、Service Fabric マネージド クラスターによりオプションの NSG ルールの作成用に予約されています。 これらのルールは、すばやく簡単に構成できるように自動的に追加されます。 1000 から 3000 の優先順位範囲でカスタム NSG ルールを追加することにより、これらのルールを上書きできます。
* カスタム NSG ルールは、1000 から 3000 の範囲内の優先順位にする必要があります。

## <a name="apply-nsg-rules"></a>NSG ルールを適用する

Service Fabric のクラシック (非マネージド) クラスターの場合、[クラスターにネットワーク セキュリティ グループ (NSG) ルールを適用する](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-nsg-cluster-65-node-3-nodetype)には、個別に *Microsoft.Network/networkSecurityGroups* リソースを宣言して管理する必要があります。 Service Fabric マネージド クラスターを使用すると、展開テンプレートのクラスター リソース内で NSG ルールを直接割り当てることができます。

NSG ルールを割り当てるには、*Microsoft.ServiceFabric/managedclusters* リソース (バージョン `2021-01-01-preview` 以降) の [networkSecurityRules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) プロパティを使用します。 次に例を示します。

```json
            "apiVersion": "2021-01-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                ...
                "networkSecurityRules" : [
                    {
                        "name": "AllowCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33000-33499",
                        "access": "Allow",
                        "priority": 2001,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "AllowARM",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "AzureResourceManager",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33500-33699",
                        "access": "Allow",
                        "priority": 2002,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "DenyCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33700-33799",
                        "access": "Deny",
                        "priority": 2003,
                        "direction": "Outbound"
                    },
                    {
                        "name": "DenyRDP",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "*",
                        "destinationAddressPrefix": "VirtualNetwork",
                        "destinationPortRange": "3389",
                        "access": "Deny",
                        "priority": 2004,
                        "direction": "Inbound",
                        "description": "Override for optional SFMC_AllowRdpPort rule. This is required in tests to avoid Sev2 incident for security policy violation."
                    }
                ],
                "fabricSettings": [
                ...
                ]
            }
```

## <a name="rdp-ports"></a>RDP ポート

Service Fabric マネージド クラスターの場合、RDP ポートへのアクセスは既定では許可されません。 Service Fabric マネージド クラスター リソースで次のプロパティを設定することにより、インターネットへの RDP ポートを開くことができます。

```json
"allowRDPAccess": true 
```

AllowRDPAccess プロパティを true に設定すると、次の NSG ルールがクラスターのデプロイに追加されます。  

```json
{
    "name": "SFMC_AllowRdpPort", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open RDP ports.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3002,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRange": "3389"
    }
}
```

## <a name="clientconnection-and-httpgatewayconnection-ports"></a>ClientConnection ポートと HttpGatewayConnection ポート

### <a name="nsg-rule-sfmc_allowservicefabricgatewaytosfrp"></a>NSG ルール: SFMC_AllowServiceFabricGatewayToSFRP

既定の NSG ルールが追加され、Service Fabric リソース プロバイダーはクラスターの clientConnectionPort と httpGatewayConnectionPort にアクセスできるようになります。 このルールにより、サービス タグ "ServiceFabric" でのポートへのアクセスが許可されます。

>[!NOTE]
>このルールは常に追加され、オーバーライドすることはできません。

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayToSFRP", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "This is required rule to allow SFRP to connect to the cluster. This rule cannot be overridden.", 
        "protocol": "TCP", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "ServiceFabric", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 500, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

### <a name="nsg-rule-sfmc_allowservicefabricgatewayports"></a>NSG ルール: SFMC_AllowServiceFabricGatewayPorts

これは、インターネットから clientConnectionPort および httpGatewayPort へのアクセスを許可するオプションの NSG ルールです。 このルールにより、お客様は SFX にアクセスしたり、PowerShell を使用してクラスターに接続したり、外部から Service Fabric クラスター API エンドポイントを使用したりすることができます。 

>[!NOTE]
>同じポートに同じアクセス、方向、プロトコルの値を持つカスタム ルールがある場合、このルールは追加されません。 このルールは、カスタム NSG ルールでオーバーライドできます。 

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayPorts", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "Optional rule to open SF cluster gateway ports. To override add a custom NSG rule for gateway ports in priority range 1000-3000.", 
        "protocol": "tcp", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "*", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 3001, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

## <a name="load-balancer-ports"></a>ロード バランサーのポート

Service Fabric マネージド クラスターにより、*ManagedCluster* プロパティの loadBalancingRules セクションで構成されているすべてのロード バランサー (LB) ポートに対する NSG ルールが、既定の優先順位範囲に作成されます。 このルールにより、インターネットからの受信トラフィック用に LB ポートが開かれます。  

>[!NOTE]
>このルールは、オプションの優先順位範囲に追加され、カスタム NSG ルールを追加することによってオーバーライドできます。

```json
{
    "name": "SFMC_AllowLoadBalancedPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open LB ports",
        "protocol": "*", 
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3003,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
        "80", "8080", "4343"
        ]
    }
}
```

## <a name="load-balancer-probes"></a>ロード バランサーのプローブ

Service Fabric マネージド クラスターにより、ファブリック ゲートウェイのポート用、およびマネージド クラスターのプロパティの "loadBalancingRules" セクションで構成されているすべてのポート用のロード バランサー プローブが、自動的に作成されます。

```json
{ 
  "value": [ 
    { 
        "name": "FabricTcpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19000, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>"
                } 
            ] 
        }, 
        "type": "Microsoft.Network/loadBalancers/probes" 
    }, 
    { 
        "name": "FabricHttpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>" 
                } 
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes" 
    },
    {
        "name": "probe1_tcp_8080", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 8080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
            { 
                "id": "<>" 
            } 
        ] 
      }, 
      "type": "Microsoft.Network/loadBalancers/probes" 
    } 
  ] 
} 
```

## <a name="next-steps"></a>次のステップ

[Service Fabric マネージド クラスターの構成オプション](how-to-managed-cluster-configuration.md)

[Service Fabric マネージド クラスターの概要](overview-managed-cluster.md)
