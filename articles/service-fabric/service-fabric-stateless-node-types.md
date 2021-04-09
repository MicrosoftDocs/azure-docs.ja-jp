---
title: Service Fabric クラスターにステートレス専用ノード タイプをデプロイする
description: Azure Service Fabric クラスターでステートレス ノード タイプを作成してデプロイする方法について説明します。
author: peterpogorski
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: pepogors
ms.openlocfilehash: eb19005019a6e4e878f6b0bd6a145048d4a2804c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563778"
---
# <a name="deploy-an-azure-service-fabric-cluster-with-stateless-only-node-types-preview"></a>ステートレス専用ノード タイプを使用した Azure Service Fabric クラスターのデプロイ (プレビュー)
Service Fabric ノード タイプには、ある時点でステートフル サービスがノードに配置されるという固有の前提があります。 ステートレス ノード タイプを使用することで、ノード タイプに対するこの仮定を緩和します。これにより、ノード タイプでスケールアウト操作の高速化、ブロンズ持続性での自動 OS アップグレードのサポート、および単一の仮想マシン スケール セット内の 100 以上のノードへのスケールアウトなどの他の機能を使用することができます。

* プライマリ ノード タイプをステートレスに構成することはできません
* ステートレス ノード タイプは、ブロンズ持続性レベルでのみサポートされています
* ステートレス ノード タイプは Service Fabric ランタイム バージョン 7.1.409 以上でのみサポートされています


サンプル テンプレートを使用できます。[Service Fabric のステートレス ノード タイプのテンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enabling-stateless-node-types-in-service-fabric-cluster"></a>Service Fabric クラスターでステートレス ノード タイプを有効化する
クラスター リソースで 1 つまたは複数のノード タイプをステートレスとして設定するには、**isStateless** プロパティを "true" に設定します。 ステートレス ノード タイプで Service Fabric クラスターをデプロイする場合は、クラスター リソースに少なくとも 1 つのプライマリ ノード タイプがあるようにしてください。

* Service Fabric クラスター リソース apiVersion は、"2020-12-01-preview" 以上である必要があります。

```json
{
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "isStateless": false,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Bronze",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": false,
        "isStateless": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    }    
    ],
}
```

## <a name="configuring-virtual-machine-scale-set-for-stateless-node-types"></a>ステートレス ノード タイプ用に仮想マシン スケール セットを構成する
ステートレス ノード タイプを有効にするには、基になる仮想マシン スケール セットのリソースを次の方法で構成する必要があります。

* 値 **singlePlacementGroup** プロパティは、100 台を超える VM に拡張する場合は **false** に設定する必要があります。
* スケール セットの **upgradePolicy** の **モード** は、**Rolling** に設定する必要があります。
* ローリング アップグレード モードを使用するには、アプリケーション正常性拡張機能または正常性プローブが構成されている必要があります。 次に示すように、ステートレス ノード タイプの既定の構成を使用して正常性プローブを構成します。 アプリケーションがノード タイプにデプロイされると、正常性プローブまたは正常性拡張機能のポートを変更して、アプリケーションの正常性を監視できます。

>[!NOTE]
> ステートレス ノード タイプが、複数のゾーンにまたがる仮想マシン スケール セットに基づいている場合は、プラットフォーム障害ドメイン数を 5 に更新する必要があります。 詳細は、こちらの[テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-2-NodeTypes-Windows-Stateless-CrossAZ-Secure)を参照してください。
> 
> **platformFaultDomainCount:5**
```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Rolling",
          "automaticOSUpgradePolicy": {
            "enableAutomaticOSUpgrade": true
          }
        }
    }
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Bronze",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
        },
        "typeHandlerVersion": "1.0"
    }
    },
    {
        "type": "extensions",
        "name": "HealthExtension",
        "properties": {
            "publisher": "Microsoft.ManagedServices",
            "type": "ApplicationHealthWindows",
            "autoUpgradeMinorVersion": true,
            "typeHandlerVersion": "1.0",
            "settings": {
            "protocol": "tcp",
            "port": "19000"
            }
            }
        },
    ]
}
```

## <a name="networking-requirements"></a>ネットワーク要件
### <a name="public-ip-and-load-balancer-resource"></a>パブリック IP とロード バランサーのリソース
仮想マシン スケール セット リソースで 100 以上の VM へのスケーリングを有効にするには、その仮想マシン スケール セットによって参照されているロード バランサーと IP リソースの両方で *Standard* SKU が使用されている必要があります。 SKU プロパティを使用せずにロード バランサーまたは IP リソースを作成すると、100 以上の VM へのスケーリングがサポートされない Basic SKU が作成されます。 Standard SKU ロード バランサーは既定で外部からのすべてのトラフィックをブロックします。外部トラフィックを許可するには、NSG をサブネットにデプロイする必要があります。

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> パブリック IP およびロード バランサー リソースへの SKU のインプレース変更を行うことはできません。 Basic SKU のある既存のリソースから移行する場合は、この記事の移行のセクションを参照してください。

### <a name="virtual-machine-scale-set-nat-rules"></a>仮想マシン スケール セットの NAT 規則
ロード バランサーのインバウンド NAT 規則は、仮想マシン スケール セットの NAT プールと一致する必要があります。 各仮想マシン スケール セットに一意のインバウンド NAT プールが必要です。

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standard SKU Load Balancer のアウトバウンド規則
Standard Load Balancer および Standard パブリック IP では、Basic SKU を使用する場合と比較して、アウトバウンド接続に新しい機能とさまざまな動作が導入されています。 Standard SKU を操作するときにアウトバウンド接続が必要な場合は、Standard パブリック IP アドレスまたは Standard パブリック Load Balancer で明示的に定義する必要があります。 詳細については、[アウトバウンド接続](../load-balancer/load-balancer-outbound-connections.md)と [Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md) に関するページを参照してください。

>[!NOTE]
> 標準テンプレートでは、既定ですべてのアウトバウンド トラフィックを許可する NSG が参照されます。 インバウンド トラフィックは、Service Fabric 管理操作に必要なポートに制限されます。 NSG 規則は、要件に合わせて変更できます。

>[!NOTE]
> Standard SKU SLB を使用しているすべての Service Fabric クラスターでは、ポート 443 での送信トラフィックを許可する規則を確実に各ノード タイプに設定する必要があります。 これはクラスターの設定を完了するために必要であり、このルールのないデプロイは失敗します。



### <a name="migrate-to-using-stateless-node-types-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Basic SKU Load Balancer と Basic SKU IP を使用したクラスターからステートレス ノード タイプの使用に移行する
すべての移行シナリオで、新しいステートレス専用ノード タイプを追加する必要があります。 既存のノード タイプをステートレス専用に移行することはできません。

Basic SKU で Load Balancer と IP を使用していたクラスターを移行するには、まず Standard SKU を使用したまったく新しい Load Balancer と IP リソースを作成する必要があります。 これらのリソースをインプレースで更新することはできません。

使用する新しいステートレス ノード タイプで、新しい LB と IP が参照されている必要があります。 上の例では、ステートレス ノード タイプとして使用するよう新しい仮想マシン スケール セットのリソースが追加されています。 これらの仮想マシン スケール セットは、新しく作成された LB と IP を参照し、Service Fabric クラスター リソース内でステートレス ノード タイプとしてマークされます。

まず、既存の Resource Manager テンプレートに新しいリソースを追加する必要があります。 これらのリソースには次が含まれます。
* Standard SKU を使用したパブリック IP リソース。
* Standard SKU を使用した Load Balancer リソース。
* 仮想マシン スケール セットをデプロイするサブネットによって参照されている NSG。

リソースのデプロイが完了したら、元のクラスターから削除したいノード タイプのノードを無効にしていくことができます。

>[!NOTE]
> Bronze の持続性を備えたステートレスなノードタイプで自動スケーリングを使用している間、スケールダウン操作後は、ノードの状態は自動的には消去されません。 自動スケーリング時にダウン ノードのノードの状態を消去するには、[Service Fabric 自動スケーリング ヘルパー](https://github.com/Azure/service-fabric-autoscale-helper)を使用することをお勧めします。

## <a name="next-steps"></a>次の手順 
* [Reliable Service](service-fabric-reliable-services-introduction.md)
* [ノード タイプと仮想マシン スケール セット](service-fabric-cluster-nodetypes.md)

