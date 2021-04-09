---
title: Azure で cloud-init を使用してユーザーを Linux VM に追加する
description: Azure CLI による作成時に、cloud-init を使用して、ユーザーを Linux VM に追加する方法
author: rickstercdn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 2c459965f2eb29a469ac90fdeb42107d1dbcf86a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559411"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Azure で cloud-init を使用してユーザーを Linux VM に追加する
この記事では、Azure でのプロビジョニング時に、[cloud-init](https://cloudinit.readthedocs.io) を使用して、仮想マシン (VM) または仮想マシン スケール セット (VMSS) でユーザーを追加する方法を示します。 この cloud-init スクリプトは、リソースが Azure によってプロビジョニングされた後の最初の起動時に実行されます。 cloud-init が Azure およびサポートされている Linux ディストリビューションでネイティブに動作する方法の詳細については、[cloud-init の概要](using-cloud-init.md)に関するページをご覧ください。

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>cloud-init で VM にユーザーを追加する
新しい Linux VM での最初のタスクの 1 つとして、*root* の使用を避けるために自分用の追加ユーザーを追加することがあります。 SSH キーは、セキュリティと使いやすさのためのベスト プラクティスです。 キーは、この cloud-init スクリプトで *~/.ssh/authorized_keys* ファイルに追加されます。

Linux VM にユーザーを追加するには、現在のシェルに *cloud_init_upgrade.txt* という名前のファイルを作成し、次の構成を貼り付けます。 この例では、ローカル コンピューター上にない Cloud Shell でファイルを作成します。 任意のエディターを使用することができます。 `sensible-editor cloud_init_add_user.txt` を入力し、ファイルを作成して使用可能なエディターの一覧を確認します。 **nano** エディターを使用するには #1 を選びます。 cloud-init ファイル全体 (特に最初の行) が正しくコピーされたことを確認してください。  値 `ssh-authorized-keys:` に対してご自身の公開キー ( *~/.ssh/id_rsa.pub* の内容など) を指定する必要があります。ここでは、例を簡単にするために短くなっています。

```yaml
#cloud-config
users:
  - default
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```
> [!NOTE] 
> #cloud-config ファイルには、`- default` パラメーターが含まれます。 これにより、プロビジョニング中に作成された既存の管理ユーザーに、ユーザーが追加されます。 `- default` パラメーターを指定しないでユーザーを作成すると、Azure プラットフォームによって作成された自動生成された管理者ユーザーは上書きされます。 

このイメージをデプロイする前に、[az group create](/cli/azure/group) コマンドを使用してリソース グループを作成する必要があります。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

ここで、[az vm create](/cli/azure/vm) で VM を作成し、次のように `--custom-data cloud_init_add_user.txt` で cloud-init ファイルを指定します。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

前述のコマンドからの出力に示すように、VM のパブリック IP アドレスに SSH 接続します。 実際の **publicIpAddress** を次のように入力します。

```bash
ssh <publicIpAddress>
```

ユーザーが VM および指定されたグループに追加されたことを確認するには、 */etc/group* ファイルの内容を次のように表示します。

```bash
cat /etc/group
```

次の出力例は、*cloud_init_add_user.txt* ファイルからのユーザーが VM と適切なグループに追加されたことを示しています。

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>次のステップ
構成変更の cloud-init の他の例については、以下をご覧ください。
 
- [VM に他の Linux ユーザーを追加する](cloudinit-add-user.md)
- [初回起動時にパッケージ マネージャーを実行して既存のパッケージを更新する](cloudinit-update-vm.md)
- [VM のローカル ホスト名を変更する](cloudinit-update-vm-hostname.md) 
- [アプリケーション パッケージのインストール、構成ファイルの更新、キーの挿入](tutorial-automate-vm-deployment.md)
