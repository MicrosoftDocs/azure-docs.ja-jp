---
title: Azure CLI を使用して VM へのポートを開く
description: Azure CLI を使用して VM へのポートを開き、エンドポイントを作成する方法について説明します。
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: a96c0f7c6fb767b96be273a615149143043e8bc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91975129"
---
# <a name="open-ports-and-endpoints-to-a-vm-with-the-azure-cli"></a>Azure CLI 使用した VM へのポートの開放とエンドポイント

サブネットまたは仮想マシン (VM) ネットワーク インターフェイスでネットワーク フィルターを作成して、Azure で VM へのポートを開くか、エンドポイントを作成します。 着信および発信の両方のトラフィックを制御するこれらのフィルターを、トラフィックを受信するリソースに接続されているネットワーク セキュリティ グループに配置します。 ポート 80 での Web トラフィックの一般的な例を使用して説明します。 この記事では、Azure CLI を使用して VM へのポートを開く方法を説明します。 


ネットワーク セキュリティ グループとルールを作成するには、最新の [Azure CLI](/cli/azure/install-az-cli2) がインストールされ、[az login](/cli/azure/reference-index) を使用して Azure アカウントにログインしている必要があります。

次の例では、パラメーター名を独自の値を置き換えます。 たとえば、*myResourceGroup*、*myNetworkSecurityGroup*、*myVnet* といったパラメーター名にします。


## <a name="quickly-open-a-port-for-a-vm"></a>VM 用のポートを簡単に開く
開発/テスト シナリオで VM 用のポートを簡単に開く必要がある場合は、[az vm open-port](/cli/azure/vm) コマンドを使用できます。 このコマンドは、ネットワーク セキュリティ グループを作成して規則を追加し、これを VM またはサブネットに適用します。 次の例では、*myResourceGroup* というリソース グループ内の *myVM* という VM に、ポート *80* を開きます。

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

発信元 IP アドレスの範囲を定義するなど、規則をより細かく制御するには、この記事の追加の手順を続行します。


## <a name="create-a-network-security-group-and-rules"></a>ネットワーク セキュリティ グループと規則の作成
[az network nsg create](/cli/azure/network/nsg) で、ネットワーク セキュリティ グループを作成します。 次の例では、*myNetworkSecurityGroup* という名前のネットワーク セキュリティ グループを *eastus* に作成します。

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

[az network nsg rule create](/cli/azure/network/nsg/rule) を使用して、Web サーバーへの HTTP トラフィックを許可する規則を追加します (または SSH アクセス、データベース接続など、独自のシナリオに合わせて調整します)。 次の例では、*myNetworkSecurityGroupRule* という名前の規則を作成して、ポート 80 での TCP トラフィックを許可します。

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>ネットワーク セキュリティ グループを VM に適用する
[az network nic update](/cli/azure/network/nic)を使用して、ネットワーク セキュリティ グループと仮想マシンのネットワーク インターフェイス (NIC) を関連付けます。 次の例では、*myNic* という名前の既存の NIC を *myNetworkSecurityGroup* という名前のネットワーク セキュリティ グループに関連付けます。

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

また、ネットワーク セキュリティ グループは、単一の VM のネットワーク インターフェイスに関連付けるのではなく、[az network vnet subnet update](/cli/azure/network/vnet/subnet) を使用して、仮想ネットワークのサブネットに関連付けることもできます。 次の例では、*myVnet* 仮想ネットワーク内の *mySubnet* という名前の既存のサブネットを *myNetworkSecurityGroup* という名前のネットワーク セキュリティ グループに関連付けます。

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>ネットワーク セキュリティ グループの詳細
このページのクイック コマンドでは、VM にフローするトラフィックの開始と実行を行うことができます。 ネットワーク セキュリティ グループには優れた機能が多数用意されており、リソースへのアクセスをきめ細かく制御できます。 詳細については、 [ネットワーク セキュリティ グループと ACL 規則の作成](tutorial-virtual-network.md#secure-network-traffic)に関するページをご覧ください。

高可用性 Web アプリケーション用に、Azure Load Balancer の背後に VM を配置する必要があります。 ロード バランサーは、トラフィックをフィルターできるネットワーク セキュリティ グループとともに、VM のトラフィックを分散します。 詳細については、[Azure の Linux 仮想マシンを負荷分散して高可用性アプリケーションを作成する方法](tutorial-load-balancer.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
この例では、HTTP トラフィックを許可する単純な規則を作成します。 より精密な環境の作成については、次の記事で確認できます。

* [Azure リソース マネージャーの概要](../../azure-resource-manager/management/overview.md)
* [ネットワーク セキュリティ グループ (NSG) について](../../virtual-network/network-security-groups-overview.md)