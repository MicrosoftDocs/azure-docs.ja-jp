---
title: Azure CLI を使用した Linux VM のコピー | Microsoft Docs
description: Azure CLI と Managed Disks を使用して Azure Linux VM のコピーを作成する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: 5a77152aea00ca094a78dc0173d48bc8e276cce5
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668071"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Azure CLI と Managed Disks を使用して Linux VM のコピーを作成する

この記事では、Azure CLI と Azure Resource Manager デプロイ モデルを使用して、Linux を実行する Azure 仮想マシン (VM) のコピーを作成する方法について説明します。 

さらに、[VHD から VM をアップロードして作成する](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)こともできます。

## <a name="prerequisites"></a>前提条件

-   [Azure CLI](/cli/azure/install-az-cli2) をインストールします。

-   [az login](/cli/azure/reference-index#az-login) で Azure アカウントにサインインする。

-   コピーのソースとして使用する Azure VM がある。

## <a name="stop-the-source-vm"></a>ソース VM を停止する

[az vm deallocate](/cli/azure/vm#az-vm-deallocate) を使用して、ソース VM の割り当てを解除します。
次の例では、*myResourceGroup* というリソース グループ内の *myVM* という VM の割り当てを解除します。

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>ソース VM をコピーする

VM をコピーするには、基となる仮想ハード ディスクのコピーを作成します。 このプロセスでは、ソース VM と同じ構成および設定が含まれるマネージド ディスクとして、特殊な仮想ハード ディスク (VHD) を作成します。

Azure Managed Disks の詳細については、「[Azure Managed Disks の概要](../windows/managed-disks-overview.md)」をご覧ください。 

1.  [az vm list](/cli/azure/vm#az-vm-list) で、各 VM とその OS ディスクの名前を一覧表示します。 次の例では、*myResourceGroup* という名前のリソース グループに含まれているすべての VM の一覧を表示します。
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    出力は次の例のようになります。

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  新しいマネージド ディスクを作成し、[az disk create](/cli/azure/disk#az-disk-create) を使用することによって、ディスクをコピーします。 次の例では、*myDisk* という名前のマネージド ディスクから *myCopiedDisk* という名前のディスクを作成します。

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  [az disk list](/cli/azure/disk#az-disk-list) を使用して、リソース グループ内にマネージド ディスクがあることを確認します。 次の例では、*myResourceGroup* という名前のリソース グループ内のマネージド ディスクの一覧を表示します。

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>仮想ネットワークを設定する

省略可能な次の手順では、新しい仮想ネットワーク、サブネット、パブリック IP アドレス、仮想ネットワーク インターフェイス カード (NIC) を作成します。

トラブルシューティングまたは追加デプロイの目的で VM をコピーする場合は、既存の仮想ネットワークで VM を使用しないほうがよいことがあります。

コピーする VM のために仮想ネットワーク インフラストラクチャを作成する場合、次の手順に従ってください。 仮想ネットワークを作成しない場合は、「[VM を作成する](#create-a-vm)」に進んでください。

1.  [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) を使用して、仮想ネットワークを作成します。 次の例では、*myVnet* という名前の仮想ネットワークと *mySubnet* という名前のサブネットを作成します。

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) を使用して、パブリック IP を作成します。 次の例では、DNS 名が *mypublicdns* で *myPublicIP* という名前のパブリック IP を作成します (DNS 名は一意である必要があるため、一意の名前を入力してください)。

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  [az network nic create](/cli/azure/network/nic#az-network-nic-create) を使用して、NIC を作成します。
    次の例では、*mySubnet* サブネットに接続される NIC *myNic* を作成します。

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>VM の作成

[az vm create](/cli/azure/vm#az-vm-create) を使用して VM を作成します。

次のコマンドを使用して、コピーしたマネージド ディスクを OS ディスク (`--attach-os-disk`) として使用するよう指定します。

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>次の手順

Azure CLI を使用して新しい VM を管理する方法については、[Azure Resource Manager の Azure CLI コマンド](../azure-cli-arm-commands.md)に関する記事を参照してください。
