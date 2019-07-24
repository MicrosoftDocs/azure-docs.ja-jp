---
title: cloud-init を使用して Linux VM 上のスワップファイルを構成する | Microsoft Docs
description: Azure CLI による作成時に cloud-init を使用して Linux VM のスワップファイルを構成する方法
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 626fd4739daf2506854c42f16ac986a361ebab38
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55769914"
---
# <a name="use-cloud-init-to-configure-a-swapfile-on-a-linux-vm"></a>cloud-init を使用して Linux VM 上のスワップファイルを構成する
この記事では、[cloud-init](https://cloudinit.readthedocs.io) を使用してさまざまな Linux ディストリビューションのスワップファイルを構成する方法を示します。 従来、スワップファイルはそれを必要とするディストリビューションに基づいて Linux エージェント (WALA) によって構成されました。  このドキュメントでは、cloud-init を使用したプロビジョニング時にスワップファイルをオンデマンドで構築するプロセスを概説します。  cloud-init が Azure およびサポートされている Linux ディストリビューションでネイティブに動作する方法について詳しくは、「[cloud-init の概要](using-cloud-init.md)」をご覧ください

## <a name="create-swapfile-for-ubuntu-based-images"></a>Ubuntu ベースのイメージ用のスワップファイルを作成する
既定では、Azure では Ubuntu ギャラリー イメージはスワップ ファイルを作成しません。 cloud-init を使用して VM のプロビジョニング中にスワップ ファイル構成を有効にするには、Ubuntu wiki 上の [AzureSwapPartitions に関するドキュメント](https://wiki.ubuntu.com/AzureSwapPartitions)をご覧ください。

## <a name="create-swapfile-for-red-hat-and-centos-based-images"></a>Red Hat および CentOS ベースのイメージ用のスワップファイルを作成する

現在のシェルで、*cloud_init_swapfile.txt* という名前のファイルを作成し、次の構成を貼り付けます。 この例では、ローカル コンピューター上にない Cloud Shell でファイルを作成します。 任意のエディターを使用することができます。 `sensible-editor cloud_init_swapfile.txt` を入力し、ファイルを作成して使用可能なエディターの一覧を確認します。 **nano** エディターを使用するには #1 を選びます。 cloud-init ファイル全体 (特に最初の行) が正しくコピーされたことを確認してください。  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

このイメージをデプロイする前に、[az group create](/cli/azure/group) コマンドを使用してリソース グループを作成する必要があります。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

ここで、[az vm create](/cli/azure/vm) で VM を作成し、次のように `--custom-data cloud_init_swapfile.txt` で cloud-init ファイルを指定します。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swapfile.txt \
  --generate-ssh-keys 
```

## <a name="verify-swapfile-was-created"></a>スワップ ファイルが作成されたことを確認する
前述のコマンドからの出力に示すように、VM のパブリック IP アドレスに SSH 接続します。 実際の **publicIpAddress** を次のように入力します。

```bash
ssh <publicIpAddress>
```

VM に SSH 接続したら、スワップファイルが作成されたかどうかを確認します

```bash
swapon -s
```

このコマンドからの出力は、次のようになります。

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> スワップ ファイルが構成された既存の Azure イメージがあり、新しいイメージのスワップ ファイル構成を変更する場合は、既存のスワップ ファイルを削除する必要があります。 詳しくは、cloud-init でプロビジョニングするためのイメージのカスタマイズに関するドキュメントをご覧ください。

## <a name="next-steps"></a>次の手順
構成変更の cloud-init の他の例については、以下をご覧ください。
 
- [VM に他の Linux ユーザーを追加する](cloudinit-add-user.md)
- [初回起動時にパッケージ マネージャーを実行して既存のパッケージを更新する](cloudinit-update-vm.md)
- [VM のローカル ホスト名を変更する](cloudinit-update-vm-hostname.md) 
- [アプリケーション パッケージのインストール、構成ファイルの更新、キーの挿入](tutorial-automate-vm-deployment.md)
