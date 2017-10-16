---
title: "Ansible を使用して Azure で基本的な Linux VM を作成する |Microsoft Docs"
description: "Ansible を使用して、Azure で基本的な Linux 仮想マシンを作成し管理する方法を説明します"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: iainfou
ms.openlocfilehash: 07cb786e3053cb933fb87b2991cebb979a91ace0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-basic-virtual-machine-in-azure-with-ansible"></a>Ansible を使用して Azure で基本的な仮想マシンを作成する
Ansible を使用すると、環境でのリソースの展開と構成を自動化することができます。 他のリソースの場合と同じように、Ansible を使用して、Azure の仮想マシン (VM) を管理できます。 この記事では、Ansible で基本的な VM を作成する方法を説明します。 また、[Ansible を使用して完全な VM 環境を作成する](ansible-create-complete-vm.md)方法についても説明します。


## <a name="prerequisites"></a>前提条件
Ansible で Azure リソースを管理するには、次の項目が必要です。

- ホスト システムにインストールされた Ansible および Azure Python SDK モジュール。
    - [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts)、[CentOS 7.3](ansible-install-configure.md#centos-73)、[SLES 12.2 SP2](ansible-install-configure.md#sles-122-sp2) への Ansible のインストール
- Azure の資格情報と、それを使用するように構成された Ansible。
    - [Azure 資格情報の作成と Ansible の構成](ansible-install-configure.md#create-azure-credentials)
- Azure CLI バージョン 2.0.4 以降。 バージョンを確認するには、`az --version` を実行します。 
    - アップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 ブラウザーから [Cloud Shell](/azure/cloud-shell/quickstart) を使用することもできます。


## <a name="create-supporting-azure-resources"></a>サポートする Azure リソースの作成
この例では、既存のインフラストラクチャに VM をデプロイする Runbook を作成します。 最初に、[az group create](/cli/azure/vm#create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli
az group create --name myResourceGroup --location eastus
```

[az network vnet create](/cli/azure/network/vnet#create) を使用して、VM 用の仮想ネットワークを作成します。 次の例では、*myVnet* という名前の仮想ネットワークと *mySubnet* という名前のサブネットを作成します。

```azurecli
az network vnet create \
  --resource-group myResourceGroup \
  --name myVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name mySubnet \
  --subnet-prefix 10.0.1.0/24
```


## <a name="create-and-run-ansible-playbook"></a>Ansible プレイブックの作成と実行
**azure_create_vm.yml** という名前の Ansible プレイブックを作成し、次の内容を貼り付けます。 この例では、単一の VM を作成し、SSH 資格情報を構成します。 次のように、*key_data* ペアに独自の公開キー データを入力します。

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys: 
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

Ansible 使用して VM を作成するには、次のようにプレイブックを実行します。

```bash
ansible-playbook azure_create_vm.yml
```

出力は、VM が正常に作成されたことを示す、次の例のようになります。

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```


## <a name="next-steps"></a>次のステップ
この例では、既に展開されている仮想ネットワークを使用して、既存のリソース グループに VM を作成します。 Ansible を使用して、仮想ネットワーク、ネットワーク セキュリティ グループのルールなどのサポート リソースを作成する方法の詳細な例については、「[Create a complete VM environment with Ansible (Ansible で完全な仮想マシン環境を作成する)](ansible-create-complete-vm.md)」を参照してください。