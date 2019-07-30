---
title: クイックスタート - Ansible を使用して Azure で Linux 仮想マシンを構成する | Microsoft Docs
description: このクイック スタートでは、Ansible を使用して Azure で Linux 仮想マシンを作成する方法について説明します
keywords: Ansible, Azure, DevOps, 仮想マシン
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 32d4486138f21bd99c3d75ee72ae5dd0df667e41
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668643"
---
# <a name="quickstart-configure-linux-virtual-machines-in-azure-using-ansible"></a>クイック スタート:Ansible を使用して Azure で Linux 仮想マシンを構成する

Ansible には宣言型の言語が使用され、Azure リソースの作成、構成、デプロイを Ansible の "*プレイブック*" で自動化することができます。 この記事では、Linux 仮想マシンを構成するための Ansible プレイブックのサンプルを示します。 この記事の最後には、[Ansible プレイブック全体](#complete-sample-ansible-playbook)を掲載しています。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)]

## <a name="create-a-resource-group"></a>リソース グループの作成

Ansible では、リソースのデプロイ先となるリソース グループが必要です。 次のサンプル Ansible プレイブック セクションでは、`myResourceGroup` という名前のリソース グループを `eastus` という場所に作成します。

```yaml
- name: Create resource group
  azure_rm_resourcegroup:
    name: myResourceGroup
    location: eastus
```

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

Azure 仮想マシン (VM) を作成する場合、[仮想ネットワーク](/azure/virtual-network/virtual-networks-overview)を作成するか、既存の仮想ネットワークを使用する必要があります。 仮想ネットワークにおける仮想マシンへの望ましいアクセス方法を決定する必要もあります。 次のサンプル Ansible プレイブック セクションでは、`myVnet` という名前の仮想ネットワークを `10.0.0.0/16` アドレス空間に作成します。

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

仮想ネットワークにデプロイされているすべての Azure リソースは、仮想ネットワーク内の[サブネット](/azure/virtual-network/virtual-network-manage-subnet)にデプロイされます。 

次のサンプル Ansible プレイブック セクションでは、`myVnet` 仮想ネットワークに `mySubnet` という名前のサブネットを作成します。

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```

## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成





インターネット リソースから Azure リソースに入ってくる通信には、[パブリック IP アドレス](/azure/virtual-network/virtual-network-ip-addresses-overview-arm)が使用されます。 また、パブリック IP アドレスにより、Azure リソースから公開されている Azure サービスへのアウトバウンド通信が可能になります。 どちらのシナリオでも、リソースに割り当てられた IP アドレスがアクセスされます。 そのアドレスは、割り当てが解除されるまで、そのリソース専用になります。 パブリック IP アドレスがリソースに割り当てられていない場合でも、リソースはインターネットにアウトバウンド通信できます。 接続は、Azure が使用可能な IP アドレスを動的に割り当てることによって行われます。 動的に割り当てられたアドレスは、リソース専用ではありません。

次のサンプル Ansible プレイブック セクションでは、`myPublicIP` という名前のパブリック IP アドレスを作成します。

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```

## <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

[ネットワーク セキュリティ グループ](/azure/virtual-network/security-overview)では、仮想ネットワーク内の Azure リソース間のネットワーク トラフィックがフィルター処理されます。 Azure リソースのインバウンド トラフィックとアウトバウンド トラフィックを制御するセキュリティ規則が定義されています。 Azure リソースとネットワーク セキュリティ グループについて詳しくは、「[Azure サービスの仮想ネットワーク統合](/azure/virtual-network/virtual-network-for-azure-services)」をご覧ください

次のプレイブックでは、`myNetworkSecurityGroup` という名前のネットワーク セキュリティ グループを作成します。 そのネットワーク セキュリティ グループには、TCP ポート 22 での SSH トラフィックを許可する規則が含まれます。

```yaml
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
```

## <a name="create-a-virtual-network-interface-card"></a>仮想ネットワーク インターフェイス カードの作成

仮想ネットワーク インターフェイス カードは、仮想マシンを特定の仮想ネットワーク、パブリック IP アドレス、およびネットワーク セキュリティ グループに接続します。 

サンプル Ansible プレイブックの次のセクションでは、作成した仮想ネットワーク リソースに接続された `myNIC` という名前の仮想ネットワーク インターフェイス カードが作成されます。

```yaml
- name: Create virtual network interface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

最後の手順は、この記事の先行セクションで作成したリソースをすべて使用する仮想マシンの作成です。 

このセクションで紹介するサンプル Ansible プレイブック セクションは、`myVM` という名前の仮想マシンを作成し、`myNIC` という名前の仮想ネットワーク インターフェイス カードをアタッチするものです。 &lt;your-key-data> プレースホルダーは、実際に所有している完全な公開キー データに置き換えてください。

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
        key_data: <your-key-data>
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.5'
      version: latest
```

## <a name="complete-sample-ansible-playbook"></a>サンプル Ansible プレイブック全体

このセクションに掲載したのは、この記事で作成してきたサンプル Ansible プレイブック全体です。 

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: myResourceGroup
      location: eastus
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
    register: output_ip_address
  - name: Dump public IP for VM which will be created
    debug:
      msg: "The public IP is {{ output_ip_address.state.ip_address }}."
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
  - name: Create virtual network interface card
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
          key_data: <your-key-data>
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

## <a name="run-the-sample-ansible-playbook"></a>サンプル Ansible プレイブックの実行

このセクションでは、この記事で紹介したサンプル Ansible プレイブックを実行してみます。

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。

1. [Cloud Shell](/azure/cloud-shell/overview) を開きます。

1. 次のように、(プレイブックを保存するための) `azure_create_complete_vm.yml` という名前のファイルを作成し、vi エディターで開きます。

   ```azurecli-interactive
   vi azure_create_complete_vm.yml
   ```

1. **I** キーを選択して挿入モードに入ります。

1. [サンプル Ansible プレイブック全体](#complete-sample-ansible-playbook)をエディターに貼り付けます。

1. **Esc** キーを押して、挿入モードを終了します。

1. ファイルを保存し、次のコマンドを入力して vi エディターを終了します。

    ```bash
    :wq
    ```

1. サンプル Ansible プレイブックを実行します。

   ```bash
   ansible-playbook azure_create_complete_vm.yml
   ```

1. 次のような出力結果が表示され、仮想マシンが正常に作成されたことを確認できます。

   ```bash
   PLAY [Create Azure VM] ****************************************************

   TASK [Gathering Facts] ****************************************************
   ok: [localhost]

   TASK [Create resource group] *********************************************
   changed: [localhost]

   TASK [Create virtual network] *********************************************
   changed: [localhost]

   TASK [Add subnet] *********************************************************
   changed: [localhost]

   TASK [Create public IP address] *******************************************
   changed: [localhost]

   TASK [Dump public IP for VM which will be created] ********************************************************************
   ok: [localhost] => {
      "msg": "The public IP is <ip-address>."
   }

   TASK [Create Network Security Group that allows SSH] **********************
   changed: [localhost]

   TASK [Create virtual network interface card] *******************************
   changed: [localhost]

   TASK [Create VM] **********************************************************
   changed: [localhost]

   PLAY RECAP ****************************************************************
   localhost                  : ok=8    changed=7    unreachable=0    failed=0
   ```

1. Linux VM にアクセスするには、SSH コマンドを使用します。 &lt;ip-address> プレースホルダーは、前の手順で確認した IP アドレスに置き換えてください。

    ```bash
    ssh azureuser@<ip-address>
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [クイック スタート:Ansible を使用して Azure で Linux 仮想マシンを管理する](./ansible-manage-linux-vm.md)
