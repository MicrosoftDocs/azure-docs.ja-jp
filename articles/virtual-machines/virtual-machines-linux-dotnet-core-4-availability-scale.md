---
title: "Azure Resource Manager テンプレートでの可用性とスケール | Microsoft Docs"
description: "Azure Virtual Machines DotNet Core チュートリアル"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 8fcfea79-f017-4658-8c51-74242fcfb7f6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2016
ms.author: nepeters
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 1c8f8a958a6ea6ea096d4a55479879aab7c7e96d
ms.lasthandoff: 03/03/2017


---
# <a name="availability-and-scale-in-azure-resource-manager-templates-for-linux-vms"></a>Linux VM での Azure Resource Manager テンプレートの可用性とスケール

可用性とスケールは、アップタイムと、需要に対応する機能を指します。 アプリケーションのアップタイムが 99.9% である必要がある場合は、複数の同時コンピューティング リソースに対応したアーキテクチャが必要です。 たとえば、可用性の高い構成には、単一の Web サイトではなく、同じサイトのインスタンスが複数含まれ、その前面に分散テクノロジが配置されています。 この構成では、アプリケーション インスタンスの 1 つをメンテナンスのために停止し、残りは継続して機能させることができます。 一方、スケールは、需要に対応するアプリケーション機能を指します。 負荷分散されたアプリケーションでは、プールからインスタンスを追加または削除することで、需要に応じてアプリケーションをスケールできます。

このドキュメントでは、ミュージック ストア サンプルのデプロイをどのように構成して可用性とスケールを実現しているかについて説明します。 すべての依存関係と固有の構成に焦点を当てます。 最善の結果を得るために、ソリューションのインスタンスを Azure サブスクリプションに事前にデプロイし、Azure Resource Manager テンプレートを手元に用意して取り組んでください。 完全なテンプレートは、こちら ( [Ubuntu のミュージック ストア デプロイ](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)) にあります。

## <a name="availability-set"></a>可用性セット
可用性セットは、Azure 仮想マシンを物理ホストと他のインフラストラクチャ コンポーネント (電源や物理ネットワーク ハードウェアなど) に論理的に分散します。 可用性セットにより、メンテナンス、デバイスの障害、またはその他のダウンタイムが発生した場合に、すべての仮想マシンが影響を受けることはなくなります。 可用性セットを Azure Resource Manager テンプレートに追加するには、Visual Studio の新しいリソースの追加ウィザードを使うか、有効な JSON をテンプレートに挿入します。

Resource Manager テンプレート内の JSON サンプルを確認するには、こちらのリンク ( [可用性セット](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L387)) をご覧ください。

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/availabilitySets",
  "name": "[variables('availabilitySetName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "avalibility-set"
  },
  "properties": {
    "platformUpdateDomainCount": 5,
    "platformFaultDomainCount": 3
  }
}
```

可用性セットは、仮想マシン リソースのプロパティとして宣言します。 

Resource Manager テンプレート内の JSON サンプルを確認するには、こちらのリンク ( [可用性セットと仮想マシンの関連付け](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L313)) をご覧ください。

```json
"properties": {
  "availabilitySet": {
    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
  }
```
Azure ポータルに表示された可用性セット。 各仮想マシンと構成の詳細がここに表示されます。

![可用性セット](./media/virtual-machines-linux-dotnet-core/aset.png)

可用性セットについて詳しくは、「 [仮想マシンの可用性管理](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 

## <a name="network-load-balancer"></a>ネットワーク ロード バランサー
可用性セットはアプリケーションのフォールト トレランスを実現します。一方、ロード バランサーは、1 つのネットワーク アドレスで利用可能なアプリケーション インスタンスを複数作成します。 複数のアプリケーション インスタンスは複数の仮想マシンでホストでき、それぞれロード バランサーに接続されます。 アプリケーションへのアクセスが行われると、ロード バランサーは接続されているメンバーに着信要求をルーティングします。 ロード バランサーを追加するには、Visual Studio の新しいリソースの追加ウィザードを使うか、適切な形式の JSON リソースを Azure Resource Manager テンプレートに挿入します。

Resource Manager テンプレート内の JSON サンプルを確認するには、こちらのリンク ( [ネットワーク ロード バランサー](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L208)) をご覧ください。

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers",
  "name": "[variables('loadBalancerName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "load-balancer-front"
  },
  ........<truncated>
}
```

サンプル アプリケーションはパブリック IP アドレスを使ってインターネットに公開されるため、このアドレスはロード バランサーに関連付けられています。 

Resource Manager テンプレート内の JSON サンプルを確認するには、こちらのリンク ( [ネットワーク ロード バランサーとパブリック IP アドレスの関連付け](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L221)) をご覧ください。

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

Azure ポータルでは、ネットワーク ロード バランサーの概要にパブリック IP アドレスとの関連付けが表示されます。

![ネットワーク ロード バランサー](./media/virtual-machines-linux-dotnet-core/nlb.png)

## <a name="load-balancer-rule"></a>ロード バランサー規則
ロード バランサーを使う場合、トラフィックを目的のリソースに分散させる方法を制御する規則を構成します。 サンプルのミュージック ストア アプリケーションでは、トラフィックはパブリック IP アドレスのポート 80 で受信し、すべての仮想マシンのポート 80 に分散されます。 

Resource Manager テンプレート内の JSON サンプルを確認するには、こちらのリンク ( [ロード バランサー規則](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L270)) をご覧ください。

```json
"loadBalancingRules": [
  {
    "name": "[variables('loadBalencerRule')]",
    "properties": {
      "frontendIPConfiguration": {
        "id": "[concat(resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName')), '/frontendIPConfigurations/LoadBalancerFrontend')]"
      },
      "backendAddressPool": {
        "id": "[variables('lbPoolID')]"
      },
      "protocol": "Tcp",
      "frontendPort": 80,
      "backendPort": 80,
      "enableFloatingIP": false,
      "idleTimeoutInMinutes": 5,
      "probe": {
        "id": "[variables('lbProbeID')]"
      }
    }
  }
]
```

ポータルに表示されたネットワーク ロード バランサー規則。

![Network Load Balancer Rule](./media/virtual-machines-linux-dotnet-core/lbrule.png)

## <a name="load-balancer-probe"></a>ロード バランサー プローブ
ロード バランサーは、実行中のシステムにのみ要求が分散されるように、各仮想マシンを監視する必要もあります。 この監視は、事前定義されたポートを定期的にプローブすることによって実行されます。 ミュージック ストアのデプロイは、含まれているすべての仮想マシンのポート 80 をプローブするように構成されています。 

Resource Manager テンプレート内の JSON サンプルを確認するには、こちらのリンク ( [ロード バランサー プローブ](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L257)) をご覧ください。

```json
"probes": [
  {
    "properties": {
      "protocol": "Tcp",
      "port": 80,
      "intervalInSeconds": 15,
      "numberOfProbes": 2
    },
    "name": "lbprobe"
  }
]
```

Azure Portal に表示されたロード バランサー プローブ。

![Network Load Balancer Probe](./media/virtual-machines-linux-dotnet-core/lbprobe.png)

## <a name="inbound-nat-rules"></a>受信 NAT のルール
ロード バランサーを使う場合、各仮想マシンへの負荷分散されないアクセスを提供する規則を作成する必要があります。 たとえば、各仮想マシンとの SSH 接続を作成する場合は、このトラフィックが負荷分散されないように、事前に決められたパスを構成する必要があります。 事前に決められたパスを構成するには、受信 NAT 規則リソースを使います。 このリソースを使って、受信通信を個々の仮想マシンにマップできます。 

ミュージック ストア アプリケーションでは、5000 から始まるポートは SSH アクセス用に各仮想マシンのポート 22 にマップされています。 `copyindex()` 関数を使って、受信ポートをインクリメントします。これにより、2 番目の仮想マシンは受信ポート 5001 を受信し、3 番目は 5002 を受信します。以降も同様です。 

Resource Manager テンプレート内の JSON サンプルを確認するには、こちらのリンク ( [受信 NAT 規則](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L270)) をご覧ください。 

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/loadBalancers/inboundNatRules",
  "name": "[concat(variables('loadBalancerName'), '/', 'SSH-VM', copyIndex())]",
  "tags": {
    "displayName": "load-balancer-nat-rule"
  },
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "lbNatLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
  ],
  "properties": {
    "frontendIPConfiguration": {
      "id": "[variables('ipConfigID')]"
    },
    "protocol": "tcp",
    "frontendPort": "[copyIndex(5000)]",
    "backendPort": 22,
    "enableFloatingIP": false
  }
}
```

Azure Portal に表示された受信 NAT 規則の一例。 このデプロイでは、仮想マシンごとに SSH NAT 規則を作成しています。

![Inbound NAT Rule](./media/virtual-machines-linux-dotnet-core/natrule.png)

Azure のネットワーク ロード バランサーについて詳しくは、「 [Azure インフラストラクチャ サービスの負荷分散](virtual-machines-linux-load-balance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

## <a name="deploy-multiple-vms"></a>複数の VM のデプロイ
最後に、可用性セットまたはロード バランサーを効果的に機能させるには、複数の仮想マシンが必要です。 Azure Resource Manager テンプレートのコピー関数を使って、複数の VM をデプロイできます。 コピー関数を使うと、仮想マシンの数の上限を定義する必要がなく、この値をデプロイ時に動的に指定できます。 コピー関数では、作成するインスタンスの数を利用し、適切な数の仮想マシンと関連リソースのデプロイを処理します。

ミュージック ストア サンプル テンプレートでは、インスタンス数を受け取るパラメーターを定義しています。 この数は、テンプレート全体で仮想マシンと関連リソースを作成するときに使われます。

```json
"numberOfInstances": {
  "type": "int",
  "minValue": 1,
  "defaultValue": 1,
  "metadata": {
    "description": "Number of VM instances to be created behind load balancer."
  }
}
```

仮想マシン リソースでは、名前と、作成するコピーの数を制御するために使うインスタンス数パラメーターがコピー ループに渡されます。

Resource Manager テンプレート内の JSON サンプルを確認するには、こちらのリンク ( [仮想マシンのコピー関数](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L300)) をご覧ください。 

```json
"apiVersion": "2015-06-15",
"type": "Microsoft.Compute/virtualMachines",
"name": "[concat(variables('vmName'),copyindex())]",
"location": "[resourceGroup().location]",
"copy": {
  "name": "virtualMachineLoop",
  "count": "[parameters('numberOfInstances')]"
}
```

コピー関数の現在の反復値には、 `copyIndex()` 関数でアクセスできます。 コピー インデックス関数の値を使って、仮想マシンなどのリソースに名前を付けることができます。 たとえば、仮想マシンのインスタンスを 2 つデプロイする場合、それぞれに異なる名前が必要です。 `copyIndex()` 関数を仮想マシン名の一部として使って、一意の名前を作成できます。 命名に `copyindex()` 関数を使う例については、仮想マシン リソースをご覧ください。 ここでは、コンピューティング名は `vmName` パラメーターと `copyIndex()` 関数を連結した名前になっています。 

Resource Manager テンプレート内の JSON サンプルを確認するには、こちらのリンク ( [コピー インデックス関数](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L319)) をご覧ください。 

```json
"osProfile": {
  "computerName": "[concat(parameters('vmName'),copyindex())]",
  "adminUsername": "[parameters('adminUsername')]",
  "linuxConfiguration": {
    "disablePasswordAuthentication": "true",
    "ssh": {
      "publicKeys": [
        {
          "path": "[variables('sshKeyPath')]",
          "keyData": "[parameters('sshKeyData')]"
        }
      ]
    }
  }
}
```

`copyIndex` 関数は、ミュージック ストア サンプル テンプレート内で何度も使われています。 `copyIndex` を利用するリソースや関数としては、仮想マシンの&1; つのインスタンスに固有のネットワーク インターフェイスやロード バランサー規則などが挙げられます。いずれも関数に依存します。 

コピー関数について詳しくは、「 [Azure Resource Manager でリソースの複数のインスタンスを作成する](../azure-resource-manager/resource-group-create-multiple.md)」をご覧ください。

## <a name="next-step"></a>次のステップ
<hr>

[手順 4 - Azure Resource Manager テンプレートを使ったアプリケーションのデプロイ](virtual-machines-linux-dotnet-core-5-app-deployment.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


