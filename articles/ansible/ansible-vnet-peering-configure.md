---
title: チュートリアル - Ansible を使用して Azure Virtual Network ピアリングを構成する
description: Ansible を使用して、仮想ネットワーク ピアリングによって仮想ネットワークを接続する方法について説明します。
keywords: ansible, azure, devops, bash, プレイブック, ネットワーキング, ピアリング
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 180bdff48a2ace4dfee1d1cb10eb75a33d360f4c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241232"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>チュートリアル:Ansible を使用して Azure Virtual Network ピアリングを構成する

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[仮想ネットワーク (VNet) ピアリング](/azure/virtual-network/virtual-network-peering-overview)を使用すると、2 つの Azure 仮想ネットワークをシームレスに接続することができます。 ピアリングされた 2 つの仮想ネットワークは、接続において、見かけ上 1 つのネットワークとして機能します。 

同じ仮想ネットワーク内の VM 間のトラフィックは、プライベート IP アドレスを使用してルーティングされます。 同様に、ピアリングされた仮想ネットワーク内の VM 間のトラフィックは、Microsoft のバックボーン インフラストラクチャを介してルーティングされます。 その結果、異なる仮想ネットワーク内の VM が相互に通信することができます。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 2 つの仮想ネットワークを作成する
> * 2 つの仮想ネットワークをピアリングする
> * 2 つのネットワーク間のピアリングを削除する

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>2 つのリソース グループを作成する

リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

このセクションのサンプル プレイブック コードは、次の目的で使用します。

- 2 つのリソース グループを作成する 

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
```

## <a name="create-the-first-virtual-network"></a>最初の仮想マシンを作成する

このセクションのサンプル プレイブック コードは、次の目的で使用します。

- 仮想ネットワークの作成
- 仮想ネットワーク内のサブネットを作成する

```yml
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
```

## <a name="create-the-second-virtual-network"></a>2 番目の仮想ネットワークを作成する

このセクションのサンプル プレイブック コードは、次の目的で使用します。

- 仮想ネットワークの作成
- 仮想ネットワーク内のサブネットを作成する

```yml
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
```

## <a name="peer-the-two-virtual-networks"></a>2 つの仮想ネットワークをピアリングする

このセクションのサンプル プレイブック コードは、次の目的で使用します。

- 仮想ネットワーク ピアリングを初期化する
- 前に作成した 2 つの仮想ネットワークをピアリングする

```yml
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true
```

## <a name="delete-the-virtual-network-peering"></a>仮想ネットワーク ピアリングを削除する

このセクションのサンプル プレイブック コードは、次の目的で使用します。

- 前に作成した 2 つの仮想ネットワーク間のピアリングを削除する

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>サンプル プレイブックを取得する

完全なサンプル プレイブックを取得するには、次の 2 つの方法があります。

- [プレイブックをダウンロード](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml)して、`vnet_peering.yml` に保存する。
- `vnet_peering.yml` という名前の新規ファイルを作成して、次のコンテンツをコピーする。

```yml
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 1000 | random }}"
      run_once: yes

- name: Connect virtual networks with virtual network peering
  hosts: localhost
  connection: local
  vars:
    resource_group: "{{ resource_group_name }}"
    resource_group_secondary: "{{ resource_group_name }}2"
    vnet_name1: "myVnet{{ rpfx }}"
    vnet_name2: "myVnet{{ rpfx }}2"
    peering_name: peer1
    location: eastus2
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="run-the-sample-playbook"></a>サンプル プレイブックを実行する

このセクションのサンプル プレイブック コードは、このチュートリアル全体を通して示されるさまざまな機能をテストするために使用します。

以下に、サンプル プレイブックを使用する際に考慮すべき重要な点をいくつか示します。

- `vars` セクションで、`{{ resource_group_name }}` プレースホルダーを実際のリソース グループの名前に置き換えます。

ansible-playbook コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook vnet_peering.yml
```

プレイブックを実行すると、次の結果のような出力が表示されます。

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Prepare random postfix] 
ok: [localhost]

PLAY [Connect virtual networks with virtual network peering] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create secondary resource group] 
changed: [localhost]

TASK [Create first virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Ceate second virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Initial vnet peering] 
changed: [localhost]

TASK [Connect vnet peering] 
changed: [localhost]

TASK [Delete vnet peering] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=12   changed=9    unreachable=0    failed=0    skipped=0   rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事で作成したリソースが不要になったら、削除してください。 

このセクションのサンプル プレイブック コードは、次の目的で使用します。

- 前に作成した 2 つのリソース グループを削除する

次のプレイブックを `cleanup.yml` という名前で保存します。

```bash
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name-1 }}"
    resource_group_secondary: "{{ resource_group_name-2 }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent

    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group_secondary }}"
        force_delete_nonempty: yes
        state: absent
```

以下に、サンプル プレイブックを使用する際に考慮すべき重要な点をいくつか示します。

- `{{ resource_group_name-1 }}` プレースホルダーは、作成した最初のリソース グループの名前に置き換えます。
- `{{ resource_group_name-2 }}` プレースホルダーは、作成した 2 番目のリソース グループの名前に置き換えます。
- 指定した 2 つのリソース グループ内のすべてのリソースが削除されます。

ansible-playbook コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [Azure 上の Ansible](/azure/ansible/)