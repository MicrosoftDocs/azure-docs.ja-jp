---
title: "Azure CLI 1.0 を使用して Linux VM の OS ディスクを拡張する | Microsoft Docs"
description: "Azure CLI 1.0 と Resource Manager デプロイメント モデルを使用して、Linux VM のオペレーティング システム (OS) 仮想ディスクを拡張する方法について説明します"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/10/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: cb615f41704457d5cc79be7fec7b80d491058230
ms.lasthandoff: 03/21/2017


---

# <a name="expand-os-disk-on-a-linux-vm-using-the-azure-cli-with-the-azure-cli-10"></a>Azure CLI と Azure CLI 1.0 を使用して Linux VM の OS ディスクを拡張する
Azure の Linux 仮想マシン (VM) では、通常、オペレーティング システム (OS) の既定の仮想ハード ディスク サイズは 30 GB です。 [データ ディスクを追加](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)して追加の記憶域を提供できますが、OS ディスクを拡張することもできます。 この記事では、Azure CLI 1.0 で非管理対象ディスクを使用して Linux VM の OS ディスクを拡張する方法について詳しく説明します

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン
次のいずれかの CLI バージョンを使用してタスクを完了できます。

- [Azure CLI 1.0](#prerequisites) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI (本記事)
- [Azure CLI 2.0](virtual-machines-linux-expand-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - Resource Manager デプロイ モデル用の次世代 CLI

## <a name="prerequisites"></a>前提条件
[最新の Azure CLI 1.0](../cli-install-nodejs.md) をインストールし、次のように Resource Manager モードを使用して [Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)にログインする必要があります。

```azurecli
azure config mode arm
```

以下のサンプルでは、パラメーター名を独自の値に置き換えてください。 `myResourceGroup` や `myVM` などは、例として使用されているパラメーター名です。

## <a name="expand-os-disk"></a>OS ディスクを拡張する

1. 仮想ハード ディスクに対する操作は、実行中の VM では実行できません。 次の例では、`myResourceGroup` という名前のリソース グループの `myVM` という名前の VM を停止し、割り当てを解除します。

    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `azure vm stop` では、コンピューティング リソースは解放されません。 コンピューティング リソースを解放するには、`azure vm deallocate` を使用します。 仮想ハード ディスクを拡張するには、VM の割り当てを解除する必要があります。

2. `azure vm set` コマンドを使用して、OS 非管理対象ディスクのサイズを更新します。 次の例では、`myResourceGroup` という名前のリソース グループの `myVM` という名前の VM を `50` GB に更新します。

    ```azurecli
    azure vm set --resource-group myResourceGroup --name myVM --new-os-disk-size 50
    ```

3. 次のように VM を起動します。

    ```azurecli
    azure vm start --resource-group myResourceGroup --name myVM
    ```

4. 適切な資格情報を使用して VM に SSH 接続します。 OS ディスクのサイズが変更されたことを確認するには、`df -h` を使用します。 次の出力例は、プライマリ パーティション (`/dev/sda1`) が 50 GB になったことを示しています。

    ```bash
    Filesystem      Size  Used Avail Use% Mounted on
    udev            1.7G     0  1.7G   0% /dev
    tmpfs           344M  5.0M  340M   2% /run
    /dev/sda1        49G  1.3G   48G   3% /
    ```

## <a name="next-steps"></a>次のステップ
追加の記憶域が必要な場合に、[Linux VM にデータ ディスクを追加](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)します。 ディスク暗号化の詳細については、「[Azure CLI を使って Linux VM のディスクを暗号化する](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

