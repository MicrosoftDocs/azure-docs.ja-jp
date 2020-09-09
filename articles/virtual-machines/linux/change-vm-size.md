---
title: Azure CLI を使用して Linux VM のサイズを変更する方法
description: スケールアップまたは VM のサイズを変更することで Linux 仮想マシンをスケールアップまたはスケールダウンする方法。
author: DavidCBerry13
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 02/10/2017
ms.author: daberry
ms.openlocfilehash: a93b1dcfe803fdead0d741553c45894f1675372b
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87372265"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Azure CLI を使用して Linux 仮想マシンのサイズを変更する 

仮想マシン (VM) をプロビジョニングした後、[VM のサイズ][vm-sizes]を変更することで、VM をスケールアップまたはスケールダウンできます。 場合によっては、先に VM の割り当てを解除する必要があります。 VM をホストしているハードウェア クラスターで目的のサイズが使用できない場合、VM の割り当てを解除する必要があります。 この記事では、Azure CLI を使用して Linux VM のサイズを変更する方法を詳しく説明します。 

## <a name="resize-a-vm"></a>VM のサイズを変更する
VM のサイズを変更するには、[Azure CLI ](/cli/azure/install-az-cli2) の最新版をインストールし、[az login](/cli/azure/reference-index) を使用して Azure アカウントにログインしてください。

1. [az vm list-vm-resize-options](/cli/azure/vm) を使用して、VM がホストされているハードウェア クラスター上で使用できる、VM のサイズの一覧を確認します。 次の例では、リソース グループ `myResourceGroup` リージョンの `myVM` という名前の VM のサイズが表示されています。
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. 必要な VM サイズが表示されている場合は、[az vm resize](/cli/azure/vm) を使用して、VM のサイズを変更してください。 次の例では、`myVM` という名前の VM のサイズを `Standard_DS3_v2` サイズに変更します。
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    この処理中に、VM が再起動します。 再起動後に、既存の OS とデータ ディスクがリマップされます。 一時ディスク上に格納されているデータはすべて失われます。

3. 必要な VM サイズが表示されない場合は、まず [az vm deallocate](/cli/azure/vm) を使用して VM の割り当てを解除します。 これにより、リージョンがサポートする任意のサイズに VM を変更できるようになり、その後 VM を起動できます。 次の手順では、`myResourceGroup` という名前のリソース グループの `myVM` という VM の割り当てを解除し、サイズを変更し、起動します。
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > VM の割り当てを解除すると、VM に割り当てられているすべての動的 IP アドレスも解放されます。 OS とデータ ディスクには影響しません。

## <a name="next-steps"></a>次のステップ
スケーラビリティを高めるには、複数の VM インスタンスを実行してスケール アウトします。詳細については、「[仮想マシン スケール セットでの Linux マシンの自動スケール][scale-set]」をご覧ください。 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/tutorial-autoscale-cli.md 
[vm-sizes]:sizes.md
