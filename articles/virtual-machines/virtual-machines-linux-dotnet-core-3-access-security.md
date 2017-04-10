---
title: "Linux VM の Azure テンプレートでのアクセスとセキュリティ | Microsoft Docs"
description: "Azure Virtual Machines DotNet Core チュートリアル"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 07e47189-680e-4102-a8d4-5a8eb9c00213
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2016
ms.author: nepeters
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2fd9fd50be79e8af239101147e5ae0a39eb2dc07
ms.openlocfilehash: 7cef940b26003578a3b08c453ed4f91d3e617b2e
ms.lasthandoff: 03/01/2017

---
# <a name="access-and-security-in-azure-resource-manager-templates-for-linux-vms"></a>Linux VM の Azure Resource Manager テンプレートでのアクセスとセキュリティ

Azure でホストされるアプリケーションには、インターネットまたは Azure を使った VPN/ExpressRoute 接続経由でアクセスできることが必要になる場合があります。 ミュージック ストア アプリケーション サンプルでは、パブリック IP アドレスを使ってインターネットで Web サイトを利用できます。 アクセスを確立したら、アプリケーションへの接続と仮想マシン リソース自体へのアクセスをセキュリティで保護する必要があります。 このアクセス セキュリティは、ネットワーク セキュリティ グループで設定します。 

このドキュメントでは、サンプルの Azure Resource Manager テンプレートでミュージック ストア アプリケーションをどのようにセキュリティで保護しているかについて説明します。 すべての依存関係と固有の構成に焦点を当てます。 最善の結果を得るために、ソリューションのインスタンスを Azure サブスクリプションに事前にデプロイし、Azure Resource Manager テンプレートを手元に用意して取り組んでください。 完全なテンプレートは、こちら ( [Ubuntu のミュージック ストア デプロイ](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)) にあります。 

## <a name="public-ip-address"></a>パブリック IP アドレス
Azure リソースへのパブリック アクセスを提供するには、パブリック IP アドレス リソースを使うことができます。 パブリック IP アドレスは、静的または動的な IP アドレスで構成できます。 動的アドレスを使った場合は、仮想マシンを停止して割り当てを解除すると、アドレスが削除されます。 マシンをもう一度起動すると、別のパブリック IP アドレスが割り当てられます。 IP アドレスが変更されないようにするには、予約済み IP アドレスを使うことができます。 

パブリック IP アドレスを Azure Resource Manager テンプレートに追加するには、Visual Studio の新しいリソースの追加ウィザードを使うか、有効な JSON をテンプレートに挿入します。 

Resource Manager テンプレート内の JSON サンプルを確認するには、こちらのリンク ( [パブリック IP アドレス](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L121)) をご覧ください。

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('publicipaddressName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "public-ip-front"
  },
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('publicipaddressDnsName')]"
    }
  }
}
```

パブリック IP アドレスは、仮想ネットワーク アダプターまたはロード バランサーに関連付けることができます。 この例では、ミュージック ストア Web サイトは複数の仮想マシンに負荷分散されるため、パブリック IP アドレスはロード バランサーに関連付けられています。

Resource Manager テンプレート内の JSON サンプルを確認するには、こちらのリンク ( [パブリック IP アドレスとロード バランサーの関連付け](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L208)) をご覧ください。

```json
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicipaddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

Azure Portal に表示されたパブリック IP アドレス。 パブリック IP アドレスは、仮想マシンではなく、ロード バランサーに関連付けられていることに注意してください。 ネットワーク ロード バランサーについては、このシリーズの次のドキュメントで説明します。

![パブリック IP アドレス](./media/virtual-machines-linux-dotnet-core/pubip.png)

Azure のパブリック IP アドレスについて詳しくは、「 [Azure 内の IP アドレス](../virtual-network/virtual-network-ip-addresses-overview-arm.md)」をご覧ください。

## <a name="network-security-group"></a>ネットワーク セキュリティ グループ
Azure リソースへのアクセスを確立したら、このアクセスを制限する必要があります。 Azure 仮想マシンについては、セキュリティで保護されたアクセスはネットワーク セキュリティ グループを使って実現します。 ミュージック ストア アプリケーション サンプルでは、仮想マシンへのすべてのアクセスは、http アクセス用のポート 80 と SSH アクセス用のポート 22 を除いて、制限されています。 ネットワーク セキュリティ グループを Azure Resource Manager テンプレートに追加するには、Visual Studio の新しいリソースの追加ウィザードを使うか、有効な JSON をテンプレートに挿入します。

Resource Manager テンプレート内の JSON サンプルを確認するには、こちらのリンク ( [ネットワーク セキュリティ グループ](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L68)) をご覧ください。

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Network/networkSecurityGroups",
  "name": "[variables('nsgfront')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "nsg-front"
  },
  "properties": {
    "securityRules": [
      {
        "name": "http",
        "properties": {
          "description": "http endpoint",
          "protocol": "Tcp",
          "sourcePortRange": "*",
          "destinationPortRange": "80",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
      ........<truncated> 
    ]
  }
}
```

この例では、ネットワーク セキュリティ グループは Virtual Network リソースで宣言されているサブネット オブジェクトに関連付けられています。 

Resource Manager テンプレート内の JSON サンプルを確認するには、こちらのリンク ( [ネットワーク セキュリティ グループと Virtual Network の関連付け](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L158)) をご覧ください。

```json
"subnets": [
  {
    "name": "[variables('subnetName')]",
    "properties": {
      "addressPrefix": "10.0.0.0/24",
      "networkSecurityGroup": {
        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroup'))]"
      }
    }
  }
```

Azure Portal では、ネットワーク セキュリティ グループは次のように表示されます。 NSG を関連付けることができるのはサブネットまたはネットワーク インターフェイスであることに注意してください。 ここでは、NSG はサブネットに関連付けられています。 この構成では、サブネットに接続されているすべての仮想マシンに受信の規則が適用されます。

![ネットワーク セキュリティ グループ](./media/virtual-machines-linux-dotnet-core/nsg.png)

ネットワーク セキュリティ グループについて詳しくは、「[ネットワーク セキュリティ グループ (NSG) について](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)」をご覧ください。

## <a name="next-step"></a>次のステップ
<hr>

[手順 3 - Azure Resource Manager テンプレートでの可用性とスケール](virtual-machines-linux-dotnet-core-4-availability-scale.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


