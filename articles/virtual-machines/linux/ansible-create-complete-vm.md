---
title: "Ansible を使用して、Azure の完全な Linux VM を作成する | Microsoft Docs"
description: "Ansible を使用して、Azure で完全な Linux 仮想マシン環境を作成して管理する方法について説明します。"
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
ms.openlocfilehash: f5dfecd1f5aafc1b7117433ef1bdb1805be2c57b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-complete-linux-virtual-machine-environment-in-azure-with-ansible"></a>Ansible によって Azure に完全な Linux 仮想マシンの環境を作成する
Ansible を使用すると、環境内のリソースのデプロイと構成を自動化することができます。 Ansible を使用すると、他のリソースと同じように、Azure で仮想マシン (VM) を管理できます。 この記事では、Ansible を使用して、完全な Linux 環境とサポート リソースを作成する方法を示します。 [Ansible を使用して基本的な VM を作成する](ansible-create-vm.md)方法も説明します。


## <a name="prerequisites"></a>前提条件
Ansible で Azure リソースを管理するには、次の項目が必要です。

- ホスト システムにインストールされた Ansible および Azure Python SDK モジュール。
    - [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts)、[CentOS 7.3](ansible-install-configure.md#centos-73)、[SLES 12.2 SP2](ansible-install-configure.md#sles-122-sp2) への Ansible のインストール
- Azure の資格情報と、それを使用するように構成された Ansible。
    - [Azure 資格情報の作成と Ansible の構成](ansible-install-configure.md#create-azure-credentials)
- Azure CLI バージョン 2.0.4 以降。 バージョンを確認するには、`az --version` を実行します。 
    - アップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 ブラウザーから [Cloud Shell](/azure/cloud-shell/quickstart) を使用することもできます。


## <a name="create-virtual-network"></a>Create virtual network
Ansible プレイブックの次のセクションでは、*10.0.0.0/16* アドレス空間に、*myVnet* という名前の仮想ネットワークを作成しています。

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.10.0.0/16"
```

サブネットを追加するには、次のセクションで、*myVnet* 仮想ネットワークに *mySubnet* という名前のサブネットを作成します。

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```


## <a name="create-public-ip-address"></a>パブリック IP アドレスの作成
インターネット経由でリソースにアクセスするには、パブリック IP アドレスを作成して、VM に割り当てます。 Ansible プレイブックの次のセクションでは、*myPublicIP* という名前のパブリック IP アドレスを作成しています。

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```


## <a name="create-network-security-group"></a>ネットワーク セキュリティ グループの作成
ネットワーク セキュリティ グループは、VM から送受信されるネットワーク トラフィック フローを制御します。 Ansible プレイブックの次のセクションでは、*myNetworkSecurityGroup* という名前のネットワーク セキュリティ グループを作成して、TCP ポート 22 で SSH トラフィックを許可するルールを定義しています。

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: TCP
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```


## <a name="create-virtual-network-interface-card"></a>仮想ネットワーク インターフェイス カードの作成
仮想ネットワーク インターフェイス カード (NIC) は、VM を特定の仮想ネットワーク、パブリック IP アドレス、およびネットワーク セキュリティ グループに接続します。 Ansible プレイブックの次のセクションでは、作成した仮想ネットワーク リソースに接続された *myNIC* という名前の仮想 NIC を作成しています。

```yaml
- name: Create virtual network inteface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```


## <a name="create-virtual-machine"></a>仮想マシンの作成
最後の手順は、VM を作成し、作成したすべてのリソースを使用することです。 Ansible プレイブックの次のセクションでは、*myVM* という名前の VM を作成し、*myNIC* という名前の仮想 NIC を接続しています。 次のように、*key_data* ペアに独自の公開キー データを入力します。

```yaml
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
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.3'
      version: latest
```

## <a name="complete-ansible-playbook"></a>完全な Ansible プレイブック
これらのすべてのセクションをまとめるには、*azure_create_complete_vm.yml* という名前で Ansible プレイブックを作成し、次のコンテンツを貼り付けます。

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network inteface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
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
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

Ansible では、すべてのリソースをデプロイするリソース グループが必要です。 [az group create](/cli/azure/vm#create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli
az group create --name myResourceGroup --location eastus
```

Ansible を使用して完全な VM 環境を作成するには、次のようにプレイブックを実行します。

```bash
ansible-playbook azure_create_complete_vm.yml
```

出力は、次の例のようになり、VM が正常に作成されたことが示されます。

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create virtual network] *********************************************
changed: [localhost]

TASK [Add subnet] *********************************************************
changed: [localhost]

TASK [Create public IP address] *******************************************
changed: [localhost]

TASK [Create Network Security Group that allows SSH] **********************
changed: [localhost]

TASK [Create virtual network inteface card] *******************************
changed: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=7    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>次のステップ
この例では、必要な仮想ネットワーク リソースを含む完全な VM 環境を作成しています。 既定のオプションで既存のネットワーク リソースに VM を作成する直接的な例については、[VM の作成](ansible-create-vm.md)に関するページをご覧ください。