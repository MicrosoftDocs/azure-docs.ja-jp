---
title: "Linux VM を作成するさまざまな方法 | Microsoft Docs"
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
ms.topic: 
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: c3959babbfa6d7d512248e262060d44390de28a4
ms.lasthandoff: 03/21/2017


---
# <a name="different-ways-to-create-a-linux-virtual-machine-in-azure"></a>Azure で Linux 仮想マシンを作成するさまざまな方法
Azure では、使いやすいツールとワークフローを使用して Linux 仮想マシン (VM) を柔軟に作成できます。 この記事は、それらの違いと、Linux VM を作成する例をまとめたものです。

## <a name="azure-cli"></a>Azure CLI
次のいずれかの CLI バージョンを使用して、Azure で VM を作成できます。

- Azure CLI 1.0 - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI (本記事)
- [Azure CLI 2.0](virtual-machines-linux-creation-choices.md) - Resource Manager デプロイト モデル用の次世代 CLI

Azure CLI 1.0 はさまざまなプラットフォームで利用できます。利用時には、npm パッケージ、ディストリビューション提供のパッケージ、Docker コンテナーのいずれかを使用します。 詳細については、[Azure CLI のインストールと構成の方法](../cli-install-nodejs.md)に関する記事を参照してください。 Azure CLI 1.0 の使用例については、次のチュートリアルを参照してください。 次に示す CLI のクイック スタート コマンドの詳細については、各記事を参照してください。

* [開発用とテスト用の Linux VM を Azure CLI から作成する](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 次の例では、 `azure_id_rsa.pub`という名前のパブリック キーを使用して CoreOS VM を作成します。
    
    ```azurecli
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
      --image-urn CoreOS
    ```
* [Azure テンプレートを使用して安全な Linux VM を作成する](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 次の例では、GitHub に格納されているテンプレートを使用して VM を作成します。
    
    ```azurecli
    azure group create --name myResourceGroup --location WestUS 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```
* [Azure CLI を使用して、完全な Linux 環境を作成する](virtual-machines-linux-create-cli-complete-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 可用性セット内のロード バランサーと複数の VM の作成が含まれています。
* [Linux VM へのディスクの追加](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 次の例では、 `TestVM`という名前の既存の VM に 5 Gb のディスクを追加します。
    
    ```azurecli
    azure vm disk attach-new --resource-group myResourceGroup  --vm-name myVM \
      --size-in-GB 5
    ```

## <a name="azure-portal"></a>Azure ポータル
[Azure Portal](https://portal.azure.com) を使用すると、システムに何もインストールする必要がないため、VM をすばやく作成できます。 Azure ポータルを使用して VM を作成する方法については、以下のページを参照してください。

* [Azure ポータルを使用して Linux VM を作成する](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Azure ポータルを使用してディスクを接続する](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>オペレーティング システムとイメージの選択肢
VM を作成するときは、実行するオペレーティング システムに基づいてイメージを選択します。 Azure とそのパートナーから多数のイメージが提供されており、中にはアプリケーションやツールがプレインストールされているイメージもあります。 または、独自のイメージのいずれかをアップロードすることができます ( [次のセクションで](#use-your-own-image)を参照してください)。

### <a name="azure-images"></a>Azure のイメージ
`azure vm image` CLI コマンドを使用して、発行元、ディストリビューション リリース、ビルドごとに利用可能な内容を確認します。

利用可能な発行元を一覧表示する場合:

```azurecli
azure vm image list-publishers --location WestUS
```

特定の発行元の利用可能な製品 (プラン) を一覧表示する場合:

```azurecli
azure vm image list-offers --location WestUS --publisher Canonical
```

特定のプランの利用可能な SKU (ディストリビューション リリース) を一覧表示する場合:

```azurecli
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

特定のリリースの利用可能なすべてのイメージを一覧表示する場合:

```azurecli
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

提供されているイメージの探し方と使い方の例については、 [Azure CLI を使用した Azure 仮想マシン イメージの検索と選択](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページを参照してください。

`azure vm quick-create` コマンドと `azure vm create` コマンドにはエイリアスがあり、より一般的なディストリビューションと、その最新リリースにすばやくアクセスするために使用できます。 一般的に、エイリアスを使う方が、VM を作成するたびに発行元、プラン、SKU、バージョンを指定するよりも簡単です。

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

* [Azure での動作保証済みディストリビューション](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [動作保証外のディストリビューションに関する情報](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [カスタム ディスク イメージをアップロードして Linux VM を作成する](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Resource Manager テンプレートとして使用する Linux 仮想マシンをキャプチャする方法](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 既存の VM をキャプチャするクイック スタート コマンドの例:
    
    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --vm-name myVM
    azure vm generalize --resource-group myResourceGroup --vm-name myVM
    azure vm capture --resource-group myResourceGroup --vm-name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>次のステップ
* [ポータル](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、[CLI](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、[Azure Resource Manager テンプレート](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のいずれかの方法で Linux VM を作成します。
* Linux VM の作成後、 [データ ディスクを追加](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)します。
* [パスワードや SSH キーをリセットしたり、ユーザーを管理したりする](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


