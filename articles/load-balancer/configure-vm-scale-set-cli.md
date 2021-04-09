---
title: 既存の Azure Load Balancer で仮想マシン スケール セットを構成する - Azure CLI
description: Azure CLI を使用し、既存の Azure Load Balancer で仮想マシン スケール セットを構成する方法について説明します。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: a60a6889217ce6ca8dccd5ebf5ee74b8f67a7757
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94518211"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Azure CLI を使用して、既存の Azure Load Balancer で仮想マシン スケール セットを構成する

この記事では、既存の Azure Load Balancer で仮想マシン スケール セットを構成する方法について説明します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件 

- 仮想マシン スケール セットがデプロイされるサブスクリプション内に、既存の標準 SKU ロード バランサーが必要です。

- 仮想マシン スケール セット用の Azure Virtual Network が必要です。
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- この記事では、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>既存のロード バランサーで仮想マシン スケール セットをデプロイする

かっこ内の値を、構成内のリソースの名前に置き換えます。

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

以下の例では、仮想マシン スケール セットを次の情報でデプロイします。

- 仮想マシン スケール セットの名前 **myVMSS**
- Azure Load Balancer の名前 **myLoadBalancer**
- ロード バランサー のバックエンド プールの名前 **myBackendPool**
- Azure Virtual Network の名前 **myVnet**
- サブネットの名前 **mySubnet**
- リソースグループの名前 **myResourceGroup**
- 仮想マシン スケ－ル セット用の Ubuntu Server イメージ

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> スケール セットが作成された後、ロード バランサーの正常性プローブに使用される負荷分散ルールのバックエンド ポートを変更することはできません。 ポートを変更するには、Azure 仮想マシン スケール セットを更新し、ポートを更新してから正常性プローブを再度構成することで、正常性プローブを削除できます。

## <a name="next-steps"></a>次のステップ

この記事では、既存の Azure Load Balancer で仮想マシン スケール セットをデプロイしました。  仮想マシン スケール セットとロードバランサーの詳細については、以下を参照してください。

- [Azure Load Balancer の概要](load-balancer-overview.md)
- [仮想マシン スケール セットとは](../virtual-machine-scale-sets/overview.md)
                                