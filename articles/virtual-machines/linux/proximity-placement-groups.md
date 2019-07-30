---
title: Linux VM に対して近接通信配置グループのプレビューを使用する | Microsoft Docs
description: Azure で Linux 仮想マシンに対して近接通信配置グループを作成して使用する方法について説明します。
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 0d44e38343d6f7113b296b57353080e2de068bb6
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278293"
---
# <a name="preview-deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>更新:Azure CLI を使用して近接通信配置グループに VM をデプロイする

各 VM をできるだけ近くに配置して、可能性のある最も短い待ち時間を実現するには、それらを[近接通信配置グループ](co-location.md#preview-proximity-placement-groups)内にデプロイするようにしてください。

近接通信配置グループは、Azure コンピューティング リソースが互いに物理的に近くに配置されるようにするために使用される論理的なグループ化です。 近接通信配置グループは、短い待ち時間が要件であるワークロードに役立ちます。

> [!IMPORTANT]
> 近接通信配置グループは現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
>
> 近接通信配置グループは、プレビュー期間中、**東日本**、**オーストラリア東部**、**インド中部**の各リージョンでは使用できません。

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

## <a name="availability-sets"></a>可用性セット
近接通信配置グループ内に可用性セットを作成することもできます。 [az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) で同じ `--ppg` パラメーターを使用して可用性セットを作成すると、可用性セット内のすべての VM も同じ近接通信配置グループ内に作成されます。

## <a name="scale-sets"></a>スケール セット

近接通信配置グループ内にスケール セットを作成することもできます。 [az vmss create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) で同じ `--ppg` パラメーターを使用してスケール セットを作成すると、すべてのインスタンスが同じ近接通信配置グループ内に作成されます。

## <a name="next-steps"></a>次の手順

近接通信配置グループのための [Azure CLI](/cli/azure/ppg) コマンドについて学習します。
