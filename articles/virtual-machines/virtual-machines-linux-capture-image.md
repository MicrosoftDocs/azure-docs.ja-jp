---
title: "Azure CLI 2.0 を使用した Linux VM のキャプチャ | Microsoft Docs"
description: "Azure CLI 2.0 で作成した管理ディスクを使用する Linux ベースの Azure 仮想マシン (VM) のイメージをキャプチャし、一般化する方法"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 93abc8b1b14f58b0d0be52517a2b63dfe2dc32fb
ms.openlocfilehash: c72f576d992c9adfa83b9744672397045833c43f
ms.lasthandoff: 02/27/2017


---
# <a name="how-to-generalize-and-capture-a-linux-virtual-machine"></a>Linux 仮想マシンを一般化してキャプチャする方法
Azure にデプロイされて構成された仮想マシン (VM) を再利用するには、VM のイメージをキャプチャします。 このプロセスでは、イメージから新しい VM をデプロイする前に、個人アカウント情報を削除するために VM を一般化する必要もあります。 この記事では、Azure Managed Disks を使用して、Azure CLI 2.0 で VM に対して VM イメージをキャプチャする方法について詳しく説明します。 これらのディスクは Azure プラットフォームによって処理されるため、ディスクを格納するための準備も場所も必要ありません。 詳細については、「[Azure Managed Disks の概要](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 この記事では、Azure CLI 2.0 を使用して Linux VM をキャプチャする方法を詳しく説明しています。 これらの手順は、[Azure CLI 1.0](virtual-machines-linux-capture-image-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して実行することもできます。

> [!TIP]
> バックアップまたはデバッグ用に設定した特別な状態の既存の Linux VM のコピーを作成したい場合は、「[Azure で実行されている Linux 仮想マシンのコピーを作成する](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 また、オンプレミスの VM から Linux VHD をアップロードしたい場合は、「[カスタム ディスク イメージをアップロードして Linux VM を作成する](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。  


## <a name="before-you-begin"></a>開始する前に
次の前提条件が満たされていることを確認します。

* **Resource Manager デプロイメント モデルで作成された Azure VM** - Linux VM を作成していない場合は、[ポータル](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、[Azure CLI](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、または [Resource Manager テンプレート](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を使うことができます。 必要に応じて VM を構成します。 たとえば、[データ ディスクを追加](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)し、更新プログラムを適用し、アプリケーションをインストールします。 

最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) がインストールされ、[az login](/cli/azure/#login) を使用して Azure アカウントにログインしている必要もあります。

## <a name="quick-commands"></a>クイック コマンド
タスクをすばやく実行する必要がある場合のために、次のセクションでは、Azure で Linux VM のイメージをキャプチャするための基本的なコマンドの詳細について説明します。 詳細な情報と各手順のコンテキストが、ドキュメントの残りの部分に記載されています。[ここからお読みください](#detailed-steps)。 次の例では、パラメーター名を独自の値を置き換えます。 `myResourceGroup`、`myVM`、`myImage` などは、例として使われているパラメーター名です。

1. ソース VM のプロビジョニングを解除します。

    ```bash
    ssh ops@myvm.westus.cloudapp.azure.com
    sudo waagent -deprovision+user -force
    exit
    ```

2. [az vm deallocate](/cli/azure/vm#deallocate) で VM の割り当てを解除します。

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. [az vm generalize](/cli/azure/vm#generalize) で VM を一般化します。
   
    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ```

4. [az image create](/cli/azure/image#create) で VM リソースからイメージを作成します。
   
    ```azurecli
    az image create --resource-group myResourceGroup --name myImage --source myVM
    ```

5. [az vm create](/cli/azure/vm#create) でイメージ リソースから VM を作成します。

    ```azurecli
    az vm create --resource-group myResourceGroup --name myVMDeployed --image myImage
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
    ```

## <a name="detailed-steps"></a>詳細な手順
以降の手順では、既存の VM のプロビジョニングを解除し、VM リソースの割り当てを解除して一般化した後、イメージを作成します。 このイメージを使用して、サブスクリプション内の任意のリソース グループに VM を作成できます。 このプロセスでは、非管理対象ディスクよりも [Azure Managed Disks](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用したほうが多くのメリットが得られます。 非管理対象ディスクを使用する場合は、基になる仮想ハード ディスク (VHD) の BLOB コピーを作成します。VM は、コピーした VHD BLOB と同じストレージ アカウントにしか作成できません。 管理ディスクを使用すると、イメージ リソースを作成して、サブスクリプション全体にデプロイできます。

## <a name="step-1-remove-the-azure-linux-agent"></a>手順 1: Azure Linux エージェントを削除する
VM を一般化する準備として、Azure VM エージェントを使用している VM のプロビジョニングを解除し、ファイルとデータを削除します。 対象の Linux VM で **deprovision** パラメーターを指定して **waagent** コマンドを実行します。 詳細については、「[Azure Linux エージェント ユーザー ガイド](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

1. SSH クライアントを使って Linux VM に接続します。
2. SSH のウィンドウで、次のコマンドを入力します。
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > このコマンドはイメージとしてキャプチャする VM に対して実行するだけとします。 これにより、イメージからすべての機密情報が削除されることや、イメージが再配布に適した状態になることが保証されるわけではありません。
 
3. 「 **y** 」と入力して続行します。 **-force** パラメーターを追加すると、この確認手順を省略できます。
4. コマンドが完了した後、「**exit**」と入力します。 SSH クライアントが閉じられます。

## <a name="step-2-capture-the-vm"></a>手順 2: VM をキャプチャする
Azure CLI 2.0 を使って、VM を一般化してキャプチャします。 次の例では、パラメーター名を独自の値を置き換えます。 たとえば、**myResourceGroup**、**myVnet**、**myVM**といったパラメーター名にします。

1. [az vm deallocate](/cli//azure/vm#deallocate) で、プロビジョニングを解除した VM の割り当てを解除します。 次の例では、`myResourceGroup` という名前のリソース グループに含まれる `myVM` という名前の VM の割り当てを解除します。
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. [az vm generalize](/cli//azure/vm#generalize) で VM を一般化します。 次の例では、`myResourceGroup` という名前のリソース グループに含まれる `myVM` という名前の VM を一般化します。
   
    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ```

3. [az image create](/cli//azure/image#create) で VM リソースのイメージを作成します。 次の例では、`myVM` という名前の VM リソースを使用して `myResourceGroup` という名前のリソース グループに含まれる `myImage` という名前のイメージを作成します。
   
    ```azurecli
    az image create --resource-group myResourceGroup --name myImage --source myVM
    ```
   
   > [!NOTE]
   > このイメージは、ソース VM と同じリソース グループに作成されます。 このイメージから、サブスクリプション内の任意のリソース グループに VM を作成できます。 管理の観点から、VM のリソースとイメージに専用のリソース グループを作成することをお勧めします。

## <a name="step-3-create-a-vm-from-the-captured-image"></a>手順 3: キャプチャしたイメージから VM を作成する
[az vm create](/cli/azure/vm#create) で、作成したイメージを使用して VM を作成します。 次の例では、`myImage` という名前のイメージから `myVMDeployed` という名前の VM を作成します。

```azurecli
az vm create --resource-group myResourceGroup --name myVMDeployed --image myImage
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
```

管理ディスクを使用する場合は、サブスクリプション内の任意のリソース グループでイメージから VM を作成できます。 この点が、非管理対象ディスクとは異なります。非管理対象ディスクでは、VM をソース VHD と同じストレージ アカウントにしか作成できません。 イメージとは別のリソース グループに VM を作成するには、イメージに対する完全なリソース ID を指定します。 [az image list](/cli/azure/image#list) を実行すると、イメージの一覧が表示されます。 出力は次の例のようになります。

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

次の例では、**az vm create** を実行し、イメージ リソース ID を指定してソース イメージとは別のリソース グループに VM を作成します。

```azurecli
az vm create --resource-group myOtherResourceGroup --name myOtherVMDeployed 
    --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage"
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
```


### <a name="verify-the-deployment"></a>デプロイを検証する
ここで、作成した仮想マシンに SSH を使用して接続し、デプロイを検証し、新しい VM の使用を開始します。 SSH を介して接続するには、[az vm show](/cli/azure/vm#show) を実行して、VM の IP アドレスか FQDN を見つけます。

```azurecli
az vm show --resource-group myResourceGroup --name myVM --show-details
```

## <a name="next-steps"></a>次のステップ
ソース VM イメージから複数の VM を作成できます。 イメージに変更を加える必要がある場合は、次の手順を実行します。 

- イメージから VM を作成します。
- 必要な更新または構成の変更を行います。
- 再度本記事の手順に従って、イメージのプロビジョニングの解除、割り当ての解除、一般化、作成を行います。
- 今後のデプロイには、この新しいイメージを使用します。 必要に応じて、元のイメージを削除します。

CLI を使用して VM を管理する方法の詳細については、[Azure CLI 2.0](/cli/azure/overview) に関するページをご覧ください。

