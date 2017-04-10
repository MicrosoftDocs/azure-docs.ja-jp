--- 
title: "Azure CLI 2.0 を使用した Linux VM のコピー | Microsoft Docs"
description: "Azure CLI 2.0 と Managed Disks を使用して Azure Linux VM のコピーを作成する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: f24b5efb0f970149d30f93b7ad50002a613b4394
ms.lasthandoff: 03/11/2017


---                    
               
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-20-and-managed-disks"></a>Azure CLI 2.0 と Managed Disks を使用して Linux VM のコピーを作成する


この記事では、Azure CLI 2.0 と Azure Resource Manager デプロイメント モデルを使用して、Linux を実行する Azure 仮想マシン (VM) のコピーを作成する方法について説明します。 これらの手順は、[Azure CLI 1.0](virtual-machines-linux-copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して実行することもできます。

さらに、[VHD から VM をアップロードして作成する](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)こともできます。

## <a name="prerequisites"></a>前提条件


-   [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールする。

-   [az login](/cli/azure/#login) で Azure アカウントにサインインする。

-   コピーのソースとして使用する Azure VM がある。

## <a name="step-1-stop-the-source-vm"></a>手順 1: ソース VM を停止する


[az vm deallocate](/cli/azure/vm#deallocate) を使用して、ソース VM の割り当てを解除します。
次の例では、**myResourceGroup** というリソース グループ内の **myVM** という VM の割り当てを解除します。

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>手順 2: ソース VM をコピーする


VM をコピーするには、基となる仮想ハード ディスクのコピーを作成します。 このプロセスでは、ソース VM と同じ構成および設定が含まれる管理ディスクとして、特殊な VHD を作成します。

Azure Managed Disks の詳細については、「[Azure Managed Disks overview](../storage/storage-managed-disks-overview.md)」 (Azure Managed Disks の概要) をご覧ください。 

1.  [az vm list](/cli/azure/vm#list) で、各 VM とその OS ディスクの名前を一覧表示します。 次の例では、**myResourceGroup** という名前のリソース グループに含まれているすべての VM の一覧を表示します。
    
    ```azurecli
    az vm list -g myTestRG --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' --output table
    ```

    出力は次の例のようになります。

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  [az disk create](/cli/azure/disk#create) を使用して新しい管理ディスクを作成することによって、ディスクをコピーします。 次の例では、**myDisk** という名前の管理ディスクから **myCopiedDisk** という名前のディスクを作成します。

    ```azurecli
    az disk create --resource-group myResourceGroup --name myCopiedDisk --source myDisk
    ``` 

1.  [az disk list](/cli/azure/disk#list) を使用して、リソース グループ内に管理ディスクがあることを確認します。 次の例では、**myResourceGroup** という名前のリソース グループ内の管理ディスクの一覧を表示します。

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```

1.  「[手順 3: 仮想ネットワークをセットアップする](#step-3-set-up-a-virtual-network)」に進みます。


## <a name="step-3-set-up-a-virtual-network"></a>手順 3: 仮想ネットワークをセットアップする


省略可能な次の手順では、新しい仮想ネットワーク、サブネット、パブリック IP アドレス、仮想ネットワーク インターフェイス カード (NIC) を作成します。

トラブルシューティングまたは追加デプロイの目的で VM をコピーする場合は、既存の仮想ネットワークで VM を使用しないほうがよいことがあります。

コピーする VM のために仮想ネットワーク インフラストラクチャを作成する場合、次の手順に従ってください。 仮想ネットワークを作成しない場合は、「[手順 4: VM を作成する](#step-4-create-a-vm)」に進んでください。

1.  [az network vnet create](/cli/azure/network/vnet#create) を使用して、仮想ネットワークを作成します。 次の例では、**myVnet** という名前の仮想ネットワークと **mySubnet** という名前のサブネットを作成します。

    ```azurecli
    az network vnet create --resource-group myResourceGroup --location westus --name myVnet \
        --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
    ```

1.  [az network public-ip create](/cli/azure/network/public-ip#create) を使用して、パブリック IP を作成します。 次の例では、DNS 名が **mypublicdns** で **myPublicIP** という名前のパブリック IP を作成します  (DNS 名は一意である必要があるため、一意の名前を入力してください)。

    ```azurecli
    az network public-ip create --resource-group myResourceGroup --location westus \
        --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
    ```

1.  [az network nic create](/cli/azure/network/nic#create) を使用して、NIC を作成します。
    次の例では、**mySubnet** サブネットに接続される NIC **myNic** を作成します。

    ```azurecli
    az network nic create --resource-group myResourceGroup --location westus --name myNic \
        --vnet-name myVnet --subnet mySubnet --public-ip-address myPublicIP
    ```

## <a name="step-4-create-a-vm"></a>手順 4: VM を作成する

これで、[az vm create](/cli/azure/vm#create) を使用して VM を作成できます。

次のように、コピーした管理ディスクを OS ディスクとして使用するように指定します (--attach-os-disk)。

```azurecli
az vm create --resource-group myResourceGroup --name myCopiedVM \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>次のステップ

Azure CLI を使用して新しい VM を管理する方法については、[Azure Resource Manager の Azure CLI コマンド](azure-cli-arm-commands.md)に関する記事を参照してください。

