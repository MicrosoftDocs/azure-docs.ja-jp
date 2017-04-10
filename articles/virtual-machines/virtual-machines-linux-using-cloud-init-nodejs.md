---
title: "Azure での作成時に cloud-init を使用して Linux VM をカスタマイズする | Microsoft Docs"
description: "Azure CLI 1.0 による作成時に cloud-init を使用して Linux VM をカスタマイズする方法"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: cdd446dbacb531c3778d232446231bfae08c74ac
ms.lasthandoff: 03/21/2017


---
# <a name="use-cloud-init-to-customize-a-linux-vm-during-creation-with-the-azure-cli-10"></a>Azure CLI 1.0 による作成時に cloud-init を使用して Linux VM をカスタマイズする
この記事では、ホスト名の設定、インストールされているパッケージの更新、およびユーザー アカウントの管理を行う cloud-init スクリプトを作成する方法について説明します。  cloud-init スクリプトは、VM の作成時に Azure CLI から呼び出します。  この記事では以下が必要です。

* Azure アカウント ([無料試用版を入手](https://azure.microsoft.com/pricing/free-trial/))。
* `azure login` でログインした [Azure CLI](../cli-install-nodejs.md)。
* Azure CLI は、Azure Resource Manager モード (`azure config mode arm`) である**"必要があります"。

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン
次のいずれかの CLI バージョンを使用してタスクを完了できます。

- [Azure CLI 1.0](#quick-commands) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI (本記事)
- [Azure CLI 2.0](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - Resource Manager デプロイ モデル用の次世代 CLI

## <a name="quick-commands"></a>クイック コマンド
ホスト名の設定、すべてのパッケージの更新、および Linux への sudo ユーザーの追加を実行する cloud-init.txt スクリプトを作成します。

```sh
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```
VM を起動するリソース グループを作成します。

```azurecli
azure group create myResourceGroup westus
```

cloud-init を使用して Linux VM を作成し、起動時にこの VM を構成します。

```azurecli
azure vm create \
  -g myResourceGroup \
  -n myVM \
  -l westus \
  -y Linux \
  -f myVMnic \
  -F myVNet \
  -P 10.0.0.0/22 \
  -j mySubnet \
  -k 10.0.0.0/24 \
  -Q canonical:ubuntuserver:14.04.2-LTS:latest \
  -M ~/.ssh/id_rsa.pub \
  -u myAdminUser \
  -C cloud-init.txt
```

## <a name="detailed-walkthrough"></a>詳細なチュートリアル
### <a name="introduction"></a>はじめに
新しい Linux VM の起動時には、何もカスタマイズされていない (ニーズに合わせてカスタマイズ可能な) 標準 Linux VM が起動されます。 [cloud-init](https://cloudinit.readthedocs.org) は、初回起動時にこの Linux VM にスクリプトまたは構成設定を挿入する一般的な方法です。

Azure では、3 つの方法でデプロイ時または起動時に Linux VM を変更できます。

* cloud-init を使用してスクリプトを挿入する。
* Azure [VMAccess 拡張機能](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を使用してスクリプトを挿入する。
* cloud-init を使用した Azure テンプレート。
* [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を使用した Azure テンプレート。

次の方法で起動後いつでもスクリプトを挿入できます。

* SSH でコマンドを直接実行する
* Azure [VMAccess 拡張機能](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を使用して、強制的に、または Azure テンプレートにスクリプトを挿入する
* Ansible、Salt、Chef、Puppet などの構成管理ツール

> [!NOTE]
> VMAccess 拡張機能は、SSH と同じ方法でスクリプトをルートとして実行します。  ただし、VM 拡張機能を使用すると、シナリオに応じて使用可能な Azure 提供の各種機能を利用できます。
> 
> 

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Azure VM の各簡易作成イメージ エイリアスで cloud-init を使用できるかどうかを次に示します。
| エイリアス | 発行元 | プラン | SKU | バージョン | cloud-init |
|:--- |:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |最新 |× |
| CoreOS |CoreOS |CoreOS |安定版 |最新 |○ |
| Debian |credativ |Debian |8 |最新 |× |
| openSUSE |SUSE |openSUSE |13.2 |最新 |× |
| RHEL |Redhat |RHEL |7.2 |最新 |× |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |最新 |○ |

Microsoft ではパートナーと協力して、パートナーから Azure に提供されたイメージに cloud-init を含めて、使用できるようにしています。

## <a name="adding-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>Azure CLI を使用し、VM の作成に cloud-init スクリプトを追加する
Azure で VM を作成するときに cloud-init スクリプトを起動するには、Azure CLI の `--custom-data` スイッチを使用して cloud-init ファイルを指定します。

VM を起動するリソース グループを作成します。

```azurecli
azure group create myResourceGroup westus
```

cloud-init を使用して Linux VM を作成し、起動時にこの VM を構成します。

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubnet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud-init.txt
```

## <a name="creating-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>Linux VM のホスト名を設定する cloud-init スクリプトを作成する
Linux VM の最も単純で最も重要な設定にホスト名があります。 このスクリプトで cloud-init を使用し、ホスト名を簡単に設定できます。  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>`cloud_config_hostname.txt`という名前の cloud-init スクリプトの例
```sh
#cloud-config
hostname: myservername
```

VM の初回起動時に、この cloud-init スクリプトによってホスト名が `myservername` に設定されます。

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_hostname.txt
```

ログインし、新しい VM のホスト名を確認します。

```bash
ssh myVM
hostname
myservername
```

## <a name="creating-a-cloud-init-script-to-update-linux"></a>Linux を更新する cloud-init スクリプトを作成する
セキュリティ上の理由から、最初の起動時に Ubuntu VM を更新する必要があるとします。  Linux ディストリビューションによっては、cloud-init を利用し、次のスクリプトで更新できます。

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Debian 製品用の cloud-init スクリプト `cloud_config_apt_upgrade.txt` の例
```sh
#cloud-config
apt_upgrade: true
```

Linux の起動後、 `apt-get`によりすべてのインストール済みパッケージが更新されます。

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_apt_upgrade.txt
```

ログインし、すべてのパッケージが更新されていることを確認します。

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="creating-a-cloud-init-script-to-add-a-user-to-linux"></a>ユーザーを Linux に追加する cloud-init スクリプトを作成する
新しい Linux VM での最初のタスクの&1; つとして、自分用に、または `root`の使用を避けるためにユーザーを追加することがあります。 セキュリティおよび使いやすさの面では SSH キーを使用するのが最も一般的であるため、次の cloud-init スクリプトを使用してこのキーを `~/.ssh/authorized_keys`

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Debian 製品用の cloud-init スクリプト `cloud_config_add_users.txt` の例
```sh
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Linux の起動後、リストに含まれるすべてのユーザーが作成され、sudo グループに追加されます。

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_add_users.txt
```

ログインし、新しく作成したユーザーを確認します。

```bash
ssh myVM
cat /etc/group
```

出力

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>次のステップ
cloud-init は、起動時に Linux VM を変更する標準的な方法の&1; つになっています。 Azure では、Linux VM を起動時または実行中に変更できる VM 拡張機能も使用可能です。 たとえば、Azure VMAccess 拡張機能を使用すると、VM の実行中に SSH またはユーザー情報をリセットすることができます。 cloud-init を使用する場合、パスワードをリセットするには再起動の必要があります。

[仮想マシンの拡張機能とその機能について](virtual-machines-linux-extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[VMAccess 拡張機能を使用して、Azure Linux VM 上のユーザー、SSH を管理し、ディスクをチェックまたは修復する](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


