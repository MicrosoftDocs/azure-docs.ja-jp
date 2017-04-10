---
title: "Azure CLI 1.0 を使用して Linux VM のコピーを作成する | Microsoft Docs"
description: "Azure CLI 1.0 を使用して Resource Manager デプロイメント モデルに Azure Linux 仮想マシンのコピーを作成する方法を説明します"
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
ms.date: 03/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: e1fa4ab140c553cab4dc535f750398d633bbb8d2
ms.lasthandoff: 03/21/2017


---
# <a name="create-a-copy-of-a-linux-virtual-machine-running-on-azure-with-the-azure-cli-10"></a>Azure CLI 1.0 を使用して Azure で実行されている Linux 仮想マシンのコピーを作成する
この記事では、Resource Manager デプロイ モデルを使用して、Linux を実行する Azure 仮想マシン (VM) のコピーを 作成する方法について説明します。 はじめにオペレーティング システムとデータ ディスクを新しいコンテナーにコピーし、次にネットワーク リソースをセットアップして、新しい仮想マシンを作成します。

[カスタム ディスク イメージをアップロードして VM を作成する](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)こともできます。

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン
次のいずれかの CLI バージョンを使用してタスクを完了できます。

- Azure CLI 1.0 - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI (本記事)
- [Azure CLI 2.0](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - Resource Manager デプロイ モデル用の次世代 CLI

## <a name="before-you-begin"></a>開始する前に
手順を開始する前に、次の前提条件が満たされていることを確認します。

* コンピューターに [Azure CLI](../cli-install-nodejs.md) をダウンロードしてインストールしてある必要があります。 
* 既存の Azure Linux VM について次の情報も必要です。

| ソース VM 情報 | 情報の入手元 |
| --- | --- |
| VM 名 |`azure vm list` |
| リソース グループ名 |`azure vm list` |
| Location (場所) |`azure vm list` |
| ストレージ アカウント名 |`azure storage account list -g <resourceGroup>` |
| コンテナー名 |`azure storage container list -a <sourcestorageaccountname>` |
| ソース VM の VHD ファイル名 |`azure storage blob list --container <containerName>` |

* 新しい VM について、以下を選択する必要があります。   <br> - コンテナー名    <br> - VM 名    <br> - VM サイズ    <br> - VNet 名    <br> - サブネット名    <br> - IP 名    <br> - NIC 名

## <a name="login-and-set-your-subscription"></a>ログインしてサブスクリプションを設定する
1. CLI にログインします。

    ```azurecli
    azure login
    ```
2. リソース マネージャー モードになっていることを確認します。

    ```azurecli
    azure config mode arm
    ```
3. 現在のサブスクリプションを設定します。 "azure account list" を使用すると、すべてのサブスクリプションを表示することができます。

    ```azurecli
    azure account set mySubscriptionID
    ```

## <a name="stop-the-vm"></a>VM を停止する
ソース VM を停止して割り当てを解除します。 "azure vm list" を使用すると、サブスクリプション内のすべての VM と、そのリソース グループ名の一覧を取得できます。

```azurecli
azure vm stop myResourceGroup myVM
azure vm deallocate myResourceGroup MyVM
```


## <a name="copy-the-vhd"></a>VHD をコピーする
`azure storage blob copy start`を使用して、ソース ストレージからターゲットに VHD をコピーできます。 この例では、同じストレージ アカウントの別のコンテナーに VHD をコピーします。

VHD を同じストレージ アカウントの別のコンテナーにコピーするには、次のように入力します。

```azurecli
azure storage blob copy start \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd \
        myNewContainerName
```

## <a name="set-up-the-virtual-network-for-your-new-vm"></a>新しい VM の仮想ネットワークを設定する
新しい VM の仮想ネットワークと NIC を設定します。 

```azurecli
azure network vnet create myResourceGroup myVnet -l myLocation

azure network vnet subnet create -a <address.prefix.in.CIDR/format> myResourceGroup myVnet mySubnet

azure network public-ip create myResourceGroup myPublicIP -l myLocation

azure network nic create myResourceGroup myNic -k mySubnet -m myVnet -p myPublicIP -l myLocation
```


## <a name="create-the-new-vm"></a>新しい VM を作成する
これでアップロード済みの仮想ディスクから VM を作成することができます。VM を作成するには、[Resource Manager テンプレートを使用する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd)か、次のように入力して CLI からコピーしたディスクの URI を指定します。

```azurecli
azure vm create -n myVM -l myLocation -g myResourceGroup -f myNic \
        -z Standard_DS1_v2 -y Linux \
        https://mystorageaccountname.blob.core.windows.net:8080/mycontainername/myVHD.vhd 
```



## <a name="next-steps"></a>次のステップ
Azure CLI を使用して新しい仮想マシンを管理する方法については、 [Azure Resource Manager の Azure CLI コマンド](azure-cli-arm-commands.md)に関する記事をご覧ください。


