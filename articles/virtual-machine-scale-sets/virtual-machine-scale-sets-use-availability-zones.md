---
title: Availability Zones を使用する Azure スケール セットを作成する
description: 障害に対する冗長性を高めるために可用性ゾーンを使用する Azure 仮想マシン スケール セットを作成する方法について説明します
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 08/08/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: c5ddd5846be91e9fc99a251d6ad45ade8bde2937
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016660"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>可用性ゾーンを使用する仮想マシン スケール セットを作成する

データセンター レベルの障害から仮想マシン スケール セットを保護するには、複数の可用性ゾーンにまたがるスケール セットを作成できます。 可用性ゾーンをサポートする Azure リージョンには少なくとも 3 つの異なるゾーンがあり、それぞれが独自の独立した電源、ネットワーク、冷却装置を備えています。 詳細については、[可用性ゾーンの概要](../availability-zones/az-overview.md)に関するページをご覧ください。

## <a name="availability-considerations"></a>可用性に関する考慮事項

リージョン (非ゾーン) スケール セットを API バージョン *2017-12-01* で 1 つ以上のゾーンにデプロイする場合、次のような可用性オプションがあります。
- 最大拡散 (platformFaultDomainCount = 1)
- 静的固定拡散 (platformFaultDomainCount = 5)
- ストレージ ディスクの障害ドメインに適合した拡散 (platforFaultDomainCount = 2 または 3)

最大拡散を選ぶと、スケール セットは各ゾーン内の可能な限り多くの障害ドメインに VM を拡散します。 この拡散では、ゾーンごとの障害ドメインが 5 個より多く、または少なくなる可能性があります。 静的固定拡散を使用すると、スケール セットはゾーンごとに 5 個の障害ドメインに VM を拡散します。 割り当て要求を満たすために各ゾーン 5 個の個別障害ドメインを検出できない場合、要求は失敗します。

この方法ではほとんどの場合に最善の拡散を提供するので、**ほとんどのワークロードでは最大拡散を使って展開することをお勧めします**。 個別のハードウェア分離ユニットにレプリカを拡散する必要がある場合は、複数の可用性ゾーンに拡散し、各ゾーン内では最大拡散を利用することを勧めします。

> [!NOTE]
> 最大拡散では、VM が拡散される障害ドメインの数に関係なく、スケール セット VM インスタンス ビューおよびインスタンス メタデータには、既定の 1 つのドメインだけが表示されることに注意してください。 各ゾーン内の拡散は暗黙で行われます。

### <a name="placement-groups"></a>配置グループ

スケール セットを展開するときは、可用性ゾーンごとに 1 つの[配置グループ](./virtual-machine-scale-sets-placement-groups.md)、またはゾーンごとに複数の配置グループのどちらかを選んで展開することもできます リージョン (非ゾーン) スケール セットでの選択肢は、リージョンに 1 つの配置グループ、またはリージョンに複数の配置グループです。 ほとんどのワークロードでは、より大きいスケールに対応できるので、複数の配置グループをお勧めします。 API バージョン *2017-12-01* でのスケール セットの既定値は、単一ゾーンとクロスゾーンのスケール セットについては複数の配置グループですが、リージョン (非ゾーン) スケール セットについては単一の配置グループです。

> [!NOTE]
> 最大拡散を使う場合は、複数の配置グループを使う必要があります。

### <a name="zone-balancing"></a>ゾーン バランス

最後に、複数のゾーンにまたがって展開されるスケール セットの場合、"ベスト エフォートのゾーン バランス" または "厳密なゾーン バランス" を選ぶこともできます。 スケール セットが "バランスが取れている" ものと見なされるのは、スケール セットの各ゾーンの VM 数が同じであるか差が \\1 つ以内の場合です。 次に例を示します。

- ゾーン 1 が 2 VM、ゾーン 2 が 3 VM、ゾーン 3 が 3 VM であるスケール セットは、バランスが取れているものと考えられます。 VM 数が異なるゾーンは 1 つのみで、他のゾーンよりも 1 個少ないだけです。 
- ゾーン 1 が 1 VM、ゾーン 2 が 3 VM、ゾーン 3 が 3 VM であるスケール セットは、バランスが取れていないものと考えられます。 ゾーン 1 の VM 数は、ゾーン 2 および 3 と比べて 2 個少ないです。

スケール セットでの VM の作成は成功するかもしれませんが、それらの VM での拡張機能はデプロイに失敗します。 スケール セットのバランスが取れているかどうかを判断するとき、これらの拡張機能が失敗する VM もカウントされます。 たとえば、ゾーン 1 が 3 VM、ゾーン 2 が 3 VM、ゾーン 3 が 3 VM であるスケール セットは、ゾーン 1 のすべての拡張機能が失敗し、ゾーン 2 と 3 のすべての拡張機能が成功した場合でも、バランスが取れているものと見なされます。

ベスト エフォートのゾーン バランスでは、スケール セットはバランスを維持しながらスケールインとスケールアウトを試みます。 ただし、何らかの理由によりそれが不可能な場合 (たとえば、1 つのゾーンがダウンして、スケール セットがそのゾーンに新しい VM を作成できなくなった場合)、スケール セットは、スケールインまたはスケールアウトを成功させるため、一時的なアンバランスを許可します。後続のスケールアウトの試行では、スケール セットは、スケール セットのバランスを取るために VM を増やす必要があるゾーンに VM を追加します。 同様に、後続のスケールインの試行では、スケール セットは、スケール セットのバランスを取るために VM を少なくする必要があるゾーンから VM を削除します。 "厳密なゾーン バランス" では、実行することによってバランスが崩れるスケールインまたはスケールアウトの試行はすべて失敗します。

ベスト エフォートのゾーン バランスを使うには、*zoneBalance* を *false* に設定します。 この設定は API バージョン *2017-12-01* の既定値です。 厳密なゾーン バランスを使うには、*zoneBalance* を *true* に設定します。

## <a name="single-zone-and-zone-redundant-scale-sets"></a>単一ゾーン スケール セットとゾーン冗長スケール セット

仮想マシン スケール セットを展開するときは、1 つのリージョン内の単一の可用性ゾーンまたは複数のゾーンを使うことができます。

単一のゾーンにスケール セットを作成する場合は、すべての VM インスタンスが実行するゾーンをユーザーが制御し、スケール セットはそのゾーン内でのみ管理および自動スケールされます。 ゾーン冗長スケール セットを使うと、複数のゾーンにまたがる単一のスケール セットを作成できます。 VM インスタンスが作成されると、既定で、複数のゾーンに均等に分散されます。 ゾーンの 1 つで中断が発生しても、スケール セットは容量を増やすためのスケールアウトを自動的には行いません。 CPU またはメモリの使用率に基づく自動スケール ルールを構成するのがベスト プラクティスです。 自動スケール ルールを作成すると、スケール セットは、その 1 つのゾーンでの VM インスタンスの喪失に、残りの動作しているゾーンに新しいインスタンスをスケールアウトすることによって、対応できるようになります。

可用性ゾーンを使うには、[サポートされている Azure リージョン](../availability-zones/az-region.md)にスケール セットを作成する必要があります。 次のいずれかの方法で、可用性ゾーンを使うスケール セットを作成できます。

- [Azure Portal](#use-the-azure-portal)
- Azure CLI
- [Azure PowerShell](#use-azure-powershell)
- [Azure リソース マネージャーのテンプレート](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

可用性ゾーンを使うスケール セットを作成するプロセスは、[使用の開始に関する記事](quick-create-portal.md)で詳しく説明されているものと同じです。 次の例で示すように、サポートされている Azure リージョンを選ぶときに、使用可能なゾーンの 1 つ以上にスケール セットを作成できます。

![単一の可用性ゾーンにスケール セットを作成する](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

スケール セットと、Azure ロード バランサーやパブリック IP アドレスなどのそれをサポートするリソースは、指定した 1 つのゾーンに作成されます。

## <a name="use-the-azure-cli"></a>Azure CLI の使用

可用性ゾーンを使うスケール セットを作成するプロセスは、[使用の開始に関する記事](quick-create-cli.md)で詳しく説明されているものと同じです。 可用性ゾーンを使うには、サポートされている Azure リージョンにスケール セットを作成する必要があります。

`--zones` パラメーターを [az vmss create](/cli/azure/vmss) コマンドに追加して、使うゾーンを指定します (ゾーン *1*、*2*、*3* など)。 次の例では、*myScaleSet* という名前の単一ゾーン スケール セットをゾーン *1* に作成します。

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

単一ゾーン スケール セットとネットワーク リソースの完全な例については、[こちらのサンプル CLI スクリプト](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh)をご覧ください

### <a name="zone-redundant-scale-set"></a>ゾーン冗長スケール セット

ゾーン冗長スケールを作成するには、*Standard* SKU のパブリック IP アドレスとロード バランサーを使います。 冗長性を高めるため、*Standard* SKU はゾーン冗長ネットワーク リソースを作成します。 詳しくは、「[Azure Load Balancer Standard の概要](../load-balancer/load-balancer-overview.md)」および「[Standard Load Balancer と可用性ゾーン](../load-balancer/load-balancer-standard-availability-zones.md)」をご覧ください。

ゾーン冗長スケール セットを作成するには、`--zones` パラメーターで複数のゾーンを指定します。 次の例では、*myScaleSet* という名前のゾーン冗長スケール セットを、ゾーン *1、2、3* にまたがって作成します。

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1 2 3
```

指定したゾーンにスケール セットのすべてのリソースと VM が作成および構成されるのに、数分かかります。 ゾーン冗長スケール セットとネットワーク リソースの完全な例については、[こちらのサンプル CLI スクリプト](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)をご覧ください

## <a name="use-azure-powershell"></a>Azure PowerShell の使用

可用性ゾーンを使うには、サポートされている Azure リージョンにスケール セットを作成する必要があります。 `-Zone` パラメーターを [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) コマンドに追加して、使うゾーンを指定します (ゾーン *1*、*2*、*3* など)。

次の例では、*myScaleSet* という名前の単一ゾーン スケール セットを、*米国東部 2* のゾーン *1* に作成します。 仮想ネットワーク用の Azure ネットワーク リソース、パブリック IP アドレス、およびロード バランサーが自動的に作成されます。 メッセージが表示されたら、スケール セット内の VM インスタンス用の自分の管理者資格情報を指定します。

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1"
```

### <a name="zone-redundant-scale-set"></a>ゾーン冗長スケール セット

ゾーン冗長スケール セットを作成するには、`-Zone` パラメーターで複数のゾーンを指定します。 次の例では、*myScaleSet* という名前のゾーン冗長スケール セットを、"*米国東部 2*" のゾーン *1、2、3* にまたがって作成します。 仮想ネットワーク用のゾーン冗長 Azure ネットワーク リソース、パブリック IP アドレス、およびロード バランサーが自動的に作成されます。 メッセージが表示されたら、スケール セット内の VM インスタンス用の自分の管理者資格情報を指定します。

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1", "2", "3"
```

## <a name="use-azure-resource-manager-templates"></a>Azure リソース マネージャー テンプレートの使用

可用性ゾーンを使うスケール セットを作成するプロセスは、[Linux](quick-create-template-linux.md) または [Windows](quick-create-template-windows.md) での使用の開始に関する記事で詳しく説明されているものと同じです。 可用性ゾーンを使うには、サポートされている Azure リージョンにスケール セットを作成する必要があります。 テンプレートの *Microsoft.Compute/virtualMachineScaleSets* のリソースの種類に `zones` プロパティを追加し、使うゾーンを指定します (ゾーン *1*、*2*、*3* など)。

次の例では、*myScaleSet* という名前の Linux 単一ゾーン スケール セットを、"*米国東部 2*" のゾーン *1* に作成します。

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

単一ゾーン スケール セットとネットワーク リソースの完全な例については、[こちらのサンプル Resource Manager テンプレート](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)をご覧ください

### <a name="zone-redundant-scale-set"></a>ゾーン冗長スケール セット

ゾーン冗長スケール セットを作成するには、リソースの種類 *Microsoft.Compute/virtualMachineScaleSets* の `zones` プロパティで複数の値を指定します。 次の例では、*myScaleSet* という名前のゾーン冗長スケール セットを、"*米国東部 2*" のゾーン *1、2、3* にまたがって作成します。

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

パブリック IP アドレスまたはロード バランサーを作成する場合、ゾーン冗長ネットワーク リソースを作成するには、*"sku": { "name": "Standard" }"* プロパティを指定します。 また、ネットワーク セキュリティ グループとルールを作成して、すべてのトラフィックを許可する必要があります。 詳しくは、「[Azure Load Balancer Standard の概要](../load-balancer/load-balancer-overview.md)」および「[Standard Load Balancer と可用性ゾーン](../load-balancer/load-balancer-standard-availability-zones.md)」をご覧ください。

ゾーン冗長スケール セットとネットワーク リソースの完全な例については、[こちらのサンプル Resource Manager テンプレート](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)をご覧ください

## <a name="next-steps"></a>次の手順

可用性ゾーンにスケール セットを作成したので、次に、[仮想マシン スケール セットにアプリケーションを展開する](tutorial-install-apps-cli.md)方法または[仮想マシン スケール セットで自動スケールを使用する](tutorial-autoscale-cli.md)方法を学習できます。
