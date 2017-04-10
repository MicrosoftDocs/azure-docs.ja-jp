---
title: "Windows VM の Azure テンプレートでのアクセスとセキュリティ | Microsoft Docs"
description: "Azure Virtual Machines DotNet Core チュートリアル"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: e671fc45-5e4d-40fd-aac5-290892713cc0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: nepeters
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 8eeeef0bb33b5b8ed265532d160829c076190fc4
ms.openlocfilehash: 91c4550f9caadc790d1b6aea8f037e2089ebec3c
ms.lasthandoff: 03/01/2017

---
# <a name="access-and-security-in-azure-resource-manager-templates-for-windows-vms"></a>Windows VM の Azure Resource Manager テンプレートでのアクセスとセキュリティ

Azure でホストされるアプリケーションには、インターネットまたは Azure を使った VPN/ExpressRoute 接続経由でアクセスできることが必要になる場合があります。 ミュージック ストア アプリケーション サンプルでは、パブリック IP アドレスを使ってインターネットで Web サイトを利用できます。 アクセスを確立したら、アプリケーションへの接続と仮想マシン リソース自体へのアクセスをセキュリティで保護する必要があります。 このアクセス セキュリティは、ネットワーク セキュリティ グループで設定します。 

このドキュメントでは、サンプルの Azure Resource Manager テンプレートでミュージック ストア アプリケーションをどのようにセキュリティで保護しているかについて説明します。 すべての依存関係と固有の構成に焦点を当てます。 最善の結果を得るために、ソリューションのインスタンスを Azure サブスクリプションに事前にデプロイし、Azure Resource Manager テンプレートを手元に用意して取り組んでください。 完全なテンプレートは、こちら ([Windows のミュージック ストア デプロイ](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)) にあります。

## <a name="public-ip-address"></a>パブリック IP アドレス
Azure リソースへのパブリック アクセスを提供するには、パブリック IP アドレス リソースを使うことができます。 パブリック IP アドレスは、静的または動的な IP アドレスで構成できます。 動的アドレスを使った場合は、仮想マシンを停止して割り当てを解除すると、アドレスが削除されます。 マシンをもう一度起動すると、別のパブリック IP アドレスが割り当てられます。 IP アドレスが変更されないようにするには、予約済み IP アドレスを使うことができます。 

パブリック IP アドレスを Azure Resource Manager テンプレートに追加するには、Visual Studio の新しいリソースの追加ウィザードを使うか、有効な JSON をテンプレートに挿入します。 

Resource Manager テンプレート内の JSON サンプルを確認するには、こちらのリンク ( [パブリック IP アドレス](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L110)) をご覧ください。

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('publicIpAddressName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "public-ip"
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

Resource Manager テンプレート内の JSON サンプルを確認するには、こちらのリンク ( [パブリック IP アドレスとロード バランサーの関連付け](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L211)) をご覧ください。

```json
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIpAddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

Azure Portal に表示されたパブリック IP アドレス。 パブリック IP アドレスは、仮想マシンではなく、ロード バランサーに関連付けられていることに注意してください。 ネットワーク ロード バランサーについては、このシリーズの次のドキュメントで説明します。

![パブリック IP アドレス](./media/virtual-machines-windows-dotnet-core/pubip-win.png)

Azure のパブリック IP アドレスについて詳しくは、「 [Azure 内の IP アドレス](../virtual-network/virtual-network-ip-addresses-overview-arm.md)」をご覧ください。

## <a name="network-security-group"></a>ネットワーク セキュリティ グループ
Azure リソースへのアクセスを確立したら、このアクセスを制限する必要があります。 Azure 仮想マシンについては、セキュリティで保護されたアクセスはネットワーク セキュリティ グループを使って実現します。 ミュージック ストア アプリケーション サンプルでは、仮想マシンへのすべてのアクセスは、HTTP アクセス用のポート 80 と RDP アクセス用のポート 3389 を除いて、制限されています。 ネットワーク セキュリティ グループを Azure Resource Manager テンプレートに追加するには、Visual Studio の新しいリソースの追加ウィザードを使うか、有効な JSON をテンプレートに挿入します。

Resource Manager テンプレート内の JSON サンプルを確認するには、こちらのリンク ( [ネットワーク セキュリティ グループ](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L57)) をご覧ください。

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Network/networkSecurityGroups",
  "name": "[variables('networkSecurityGroup')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-security-group"
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
},
```

この例では、ネットワーク セキュリティ グループは Virtual Network リソースで宣言されているサブネット オブジェクトに関連付けられています。 

Resource Manager テンプレート内の JSON サンプルを確認するには、こちらのリンク ( [ネットワーク セキュリティ グループと Virtual Network の関連付け](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L143)) をご覧ください。

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
]
```

Azure Portal では、ネットワーク セキュリティ グループは次のように表示されます。 NSG を関連付けることができるのはサブネットまたはネットワーク インターフェイスであることに注意してください。 ここでは、NSG はサブネットに関連付けられています。 この構成では、サブネットに接続されているすべての仮想マシンに受信の規則が適用されます。

![ネットワーク セキュリティ グループ](./media/virtual-machines-windows-dotnet-core/nsg-win.png)

ネットワーク セキュリティ グループについて詳しくは、「[ネットワーク セキュリティ グループ (NSG) について](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)」をご覧ください。

## <a name="next-step"></a>次のステップ
<hr>

[手順 3 - Azure Resource Manager テンプレートでの可用性とスケール](virtual-machines-windows-dotnet-core-4-availability-scale.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


