---
title: Azure CLI を使用して近接通信配置グループを作成する
description: Azure で仮想マシンに対して近接通信配置グループを作成して使用する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.openlocfilehash: a347c9284608340811f9c2388df26129baeb8837
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505649"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Azure CLI を使用して近接通信配置グループに VM をデプロイする

各 VM をできるだけ近くに配置して、可能性のある最も短い待ち時間を実現するには、それらを[近接通信配置グループ](../co-location.md#proximity-placement-groups)内にデプロイするようにしてください。

近接通信配置グループは、Azure コンピューティング リソースが互いに物理的に近くに配置されるようにするために使用される論理的なグループ化です。 近接通信配置グループは、短い待ち時間が要件であるワークロードに役立ちます。


## <a name="create-the-proximity-placement-group"></a>近接通信配置グループを作成する
[`az ppg create`](/cli/azure/ppg#az-ppg-create) を使用して、近接通信配置グループを作成します。 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>近接通信配置グループを一覧表示する

[az ppg list](/cli/azure/ppg#az-ppg-list) を使用して、すべての近接通信配置グループを一覧表示できます。

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>VM の作成

[new az vm](/cli/azure/vm#az-vm-create) を使用して、近接通信配置グループ内に VM を作成します。

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

[az ppg show](/cli/azure/ppg#az-ppg-show) を使用して、近接通信配置グループ内の VM を表示できます。

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>可用性セット
近接通信配置グループ内に可用性セットを作成することもできます。 [az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) で同じ `--ppg` パラメーターを使用して可用性セットを作成すると、可用性セット内のすべての VM も同じ近接通信配置グループ内に作成されます。

## <a name="scale-sets"></a>スケール セット

近接通信配置グループ内にスケール セットを作成することもできます。 [az vmss create](/cli/azure/vmss#az_vmss_create) で同じ `--ppg` パラメーターを使用してスケール セットを作成すると、すべてのインスタンスが同じ近接通信配置グループ内に作成されます。

## <a name="next-steps"></a>次のステップ

近接通信配置グループのための [Azure CLI](/cli/azure/ppg) コマンドについて学習します。