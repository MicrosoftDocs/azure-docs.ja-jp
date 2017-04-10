---
title: "Azure CLI 1.0 を使用した Linux VM へのポートの開放 | Microsoft Docs"
description: "Azure Resource Manager デプロイメント モデルと Azure CLI 1.0 を使用して、Linux VM へのポートを開き、エンドポイントを作成する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 96d34c176e028b89617c1f09d7baba7b31eb1601
ms.lasthandoff: 03/21/2017

---

# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure-using-the-azure-cli-10"></a>Azure CLI 1.0 を使用した Azure の Linux VM へのポートとエンドポイントの開放
サブネットまたは仮想マシン (VM) ネットワーク インターフェイスでネットワーク フィルターを作成して、Azure で VM へのポートを開くか、エンドポイントを作成します。 着信および発信の両方のトラフィックを制御するこれらのフィルターを、トラフィックを受信するリソースに接続されているネットワーク セキュリティ グループに配置します。 ポート 80 での Web トラフィックの一般的な例を使用して説明します。 この記事では、Azure CLI 1.0 を使用して VM へのポートを開く方法を説明します。


## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン
次のいずれかの CLI バージョンを使用してタスクを完了できます。

- [Azure CLI 1.0](#quick-commands) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI (本記事)
- [Azure CLI 2.0](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - Resource Manager デプロイ モデル用の次世代 CLI


## <a name="quick-commands"></a>クイック コマンド
ネットワーク セキュリティ グループと規則を作成するには、[Azure CLI 1.0](../cli-install-nodejs.md) をインストールし、Resource Manager モードで使用する必要があります。

```azurecli
azure config mode arm
```

次の例では、パラメーター名を独自の値を置き換えます。 パラメーター名の例には、`myResourceGroup`、`myNetworkSecurityGroup`、および `myVnet` が含まれています。

独自の名前と場所を適宜入力してネットワーク セキュリティ グループを作成します。 次の例では、`myNetworkSecurityGroup` という名前のネットワーク セキュリティ グループを `WestUS` の場所に作成します。

```azurecli
azure network nsg create --resource-group myResourceGroup --location westus \
    --name myNetworkSecurityGroup
```

Web サーバーへの HTTP トラフィックを許可する規則を追加します (または SSH アクセス、データベース接続など、独自のシナリオに合わせて調整します)。 次の例では、`myNetworkSecurityGroupRule` という名前の規則を作成して、ポート 80 での TCP トラフィックを許可します。

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow
```

ネットワーク セキュリティ グループと仮想マシンのネットワーク インターフェイス (NIC) を関連付けます。 次の例では、`myNic` という名前の既存の NIC を `myNetworkSecurityGroup` という名前のネットワーク セキュリティ グループに関連付けます。

```azurecli
azure network nic set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup --name myNic
```

また、ネットワーク セキュリティ グループは、単一の VM のネットワーク インターフェイスに関連付けるのではなく、仮想ネットワークのサブネットに関連付けることもできます。 次の例では、`myVnet` 仮想ネットワーク内の `mySubnet` という名前の既存のサブネットを `myNetworkSecurityGroup` という名前のネットワーク セキュリティ グループに関連付けます。

```azurecli
azure network vnet subnet set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
```

## <a name="more-information-on-network-security-groups"></a>ネットワーク セキュリティ グループの詳細
このページのクイック コマンドでは、VM にフローするトラフィックの開始と実行を行うことができます。 ネットワーク セキュリティ グループには優れた機能が多数用意されており、リソースへのアクセスをきめ細かく制御できます。 詳細については、 [ネットワーク セキュリティ グループと ACL 規則の作成](../virtual-network/virtual-networks-create-nsg-arm-cli.md)に関するページをご覧ください。

ネットワーク セキュリティ グループと ACL 規則は、Azure Resource Manager のテンプレートの一部として定義できます。 詳細については、 [テンプレートを使用したネットワーク セキュリティ グループの作成](../virtual-network/virtual-networks-create-nsg-arm-template.md)に関するページをご覧ください。

ポート フォワーディングを使用して、一意の外部ポートを VM 上の内部ポートにマップする必要がある場合は、ロード バランサーとネットワーク アドレス変換 (NAT) 規則を使用します。 たとえば、TCP ポート 8080 を外部に公開し、VM 上の TCP ポート 80 にトラフィックを送ることができます。 詳細については、 [インターネットに接続するロード バランサーの作成](../load-balancer/load-balancer-get-started-internet-arm-cli.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
この例では、HTTP トラフィックを許可する単純な規則を作成します。 より精密な環境の作成については、次の記事で確認できます。

* [Azure リソース マネージャーの概要](../azure-resource-manager/resource-group-overview.md)
* [ネットワーク セキュリティ グループ (NSG) について](../virtual-network/virtual-networks-nsg.md)
* [ロード バランサー用の Azure Resource Manager の概要](../load-balancer/load-balancer-arm.md)


