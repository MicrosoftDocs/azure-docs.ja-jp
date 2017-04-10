---
title: "Azure で Linux VM を作成するさまざまな方法 | Microsoft Azure"
description: "Azure で Linux 仮想マシンを作成するさまざまな方法のほか、各方法のツールとチュートリアルへのリンクを紹介します。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f38f8a44-6c88-4490-a84a-46388212d24c
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d7ff1317cdf6ccfe6b4e5035878fc4e096fcc0f9
ms.lasthandoff: 04/03/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>Linux VM を作成するさまざまな方法
Azure では、使いやすいツールとワークフローを使用して Linux 仮想マシン (VM) を柔軟に作成できます。 この記事は、それらの違いと、Azure CLI 2.0 を含む、Linux VM を作成する例をまとめたものです。 また、[Azure CLI 1.0](creation-choices-nodejs.md) などの作成の選択肢も確認できます。

[Azure CLI 2.0](/cli/azure/install-az-cli2) はさまざまなプラットフォームで利用できます。利用時には、npm パッケージ、ディストリビューション提供のパッケージ、Docker コンテナーのいずれかを使用します。 環境に最適なビルドをインストールし、[az login](/cli/azure/#login) を使用して Azure アカウントにログインします。

次の例では、Azure CLI 2.0 を使用します。 示されているコマンドの詳細については、各記事を参照してください。 [Azure CLI 1.0](creation-choices-nodejs.md) を使用する Linux 作成の選択肢の例もあります。

* [Azure CLI 2.0 を使用して Linux VM を作成する](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * この例では、[az group create](/cli/azure/group#create) を使用して、`myResourceGroup` というリソース グループを作成します。 
-    
    ```azurecli
    az group create --name myResourceGroup --location westus
    ```
    
  * この例では、[az vm create](/cli/azure/vm#create) を使用して、`myVM` という VM を作成します。その際に、Azure Managed Disks で最新の Debian イメージと、`id_rsa.pub` という公開キーを使用します。

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
     --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk  –-new --size-gb 5    --public-ip-address-dns-name myPublicDNS \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

    * 非管理対象ディスクを使用する場合は、`--use-unmanaged-disks` フラグを上記のコマンドに追加します。 ストレージ アカウントが作成されます。 詳しくは、「[Azure Managed Disks overview](../../storage/storage-managed-disks-overview.md)」(Azure Managed Disks の概要) をご覧ください。

* [Azure テンプレートを使用して安全な Linux VM を作成する](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 次の例では、[az group deployment create](/cli/azure/group/deployment#create) を使用し、GitHub に格納されているテンプレートを使って VM を作成します。
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Azure CLI を使用して、完全な Linux 環境を作成する](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 可用性セット内のロード バランサーと複数の VM の作成が含まれています。

* [Linux VM へのディスクの追加](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 次の例では、[az vm disk attach-new](/cli/azure/vm/disk#attach-new) を使用して、`myVM` という既存の VM に 50 GB の管理ディスクを追加します。
  
    ```azurecli
    az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk  \
    –-new --size-gb 50
    ```

## <a name="azure-portal"></a>Azure Portal
[Azure Portal](https://portal.azure.com) を使用すると、システムに何もインストールする必要がないため、VM をすばやく作成できます。 Azure ポータルを使用して VM を作成する方法については、以下のページを参照してください。

* [Azure ポータルを使用して Linux VM を作成する](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Azure ポータルを使用してディスクを接続する](../windows/attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>オペレーティング システムとイメージの選択肢
VM を作成するときは、実行するオペレーティング システムに基づいてイメージを選択します。 Azure とそのパートナーから多数のイメージが提供されており、中にはアプリケーションやツールがプレインストールされているイメージもあります。 または、独自のイメージのいずれかをアップロードすることができます ( [次のセクションで](#use-your-own-image)を参照してください)。

### <a name="azure-images"></a>Azure のイメージ
[az vm image](/cli/azure/vm/image) コマンドを使用して、発行元、ディストリビューション リリース、ビルドごとに利用可能な内容を確認します。

利用可能な発行元を一覧表示する場合:

```azurecli
az vm image list-publishers --location WestUS
```

特定の発行元の利用可能な製品 (プラン) を一覧表示する場合:

```azurecli
az vm image list-offers --publisher Canonical --location WestUS
```

特定のプランの利用可能な SKU (ディストリビューション リリース) を一覧表示する場合:

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location WestUS
```

特定のリリースの利用可能なすべてのイメージを一覧表示する場合:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location WestUS
```

提供されているイメージの探し方と使い方の例については、 [Azure CLI を使用した Azure 仮想マシン イメージの検索と選択](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページを参照してください。

**az vm create** コマンドにはエイリアスがあり、より一般的なディストリビューションとその最新リリースにすばやくアクセスするために使用できます。 一般的に、エイリアスを使う方が、VM を作成するたびに発行元、プラン、SKU、バージョンを指定するよりも簡単です。

| エイリアス | 発行元 | プラン | SKU | バージョン |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |最新 |
| CoreOS |CoreOS |CoreOS |安定版 |最新 |
| Debian |credativ |Debian |8 |最新 |
| openSUSE |SUSE |openSUSE |13.2 |最新 |
| RHEL |Redhat |RHEL |7.2 |最新 |
| SLES |SLES |SLES |12-SP1 |最新 |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |最新 |

### <a name="use-your-own-image"></a>独自のイメージを使用する
特定のカスタマイズが必要な場合は、既存の Azure VM を *キャプチャ* してそれをベースにしたイメージを使用できます。 また、オンプレミスで作成したイメージをアップロードすることもできます。 サポートされているディストリビューションと独自のイメージの使用方法について詳しくは、次の記事を参照してください。

* [Azure での動作保証済みディストリビューション](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [動作保証外のディストリビューションに関する情報](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Resource Manager テンプレートとして使用する Linux 仮想マシンをキャプチャする方法](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 非管理対象ディスクを使用して既存の VM をキャプチャするクイックスタート **az vm** コマンドの例:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm capture --resource-group myResourceGroup --name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>次のステップ
* [ポータル](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、[CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、[Azure Resource Manager テンプレート](../windows/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のいずれかの方法で Linux VM を作成します。
* Linux VM の作成後、 [データ ディスクを追加](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)します。
* [パスワードや SSH キーをリセットしたり、ユーザーを管理したりする](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

