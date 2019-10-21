---
title: チュートリアル - Ansible を使用して Azure ルート テーブルを構成する
description: Ansible を使用して Azure ルート テーブルを作成、変更、および削除する方法について説明します。
keywords: ansible, azure, devops, bash, プレイブック, ネットワーク, ルート, ルート テーブル
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 14753af58a179ddf4011cb29c7ed08faab62875c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241779"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>チュートリアル:Ansible を使用して Azure ルート テーブルを構成する

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

Azure では、Azure のサブネット、仮想ネットワーク、およびオンプレミスのネットワーク間のトラフィックが自動的にルーティングされます。 ご使用環境のルーティングをより制御する必要がある場合は、[ルート テーブル](/azure/virtual-network/virtual-networks-udr-overview)を作成することができます。 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> ルート テーブルを作成する、仮想ネットワークとサブネットを作成する、ルート テーブルをサブネットに関連付ける、ルート テーブルのサブネットとの関連付けを解除する、ルートを作成および削除する、ルート テーブルのクエリを実行する、ルート テーブルを削除する

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>ルート テーブルの作成

このセクションのプレイブック コードはルート テーブルを作成します。 ルート テーブルの制限については、[Azure の制限](/azure/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits)に関する記事を参照してください。 

次のプレイブックを `route_table_create.yml` という名前で保存します。

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>サブネットへのルート テーブルの関連付け

このセクションのプレイブック コードは、次のことを行います。

* 仮想ネットワークを作成します
* 仮想ネットワーク内にサブネットを作成する
* サブネットにルート テーブルを関連付ける

ルート テーブルは仮想ネットワークには関連付られません。 むしろ、ルート テーブルは仮想ネットワークのサブネットに関連付けられます。

仮想ネットワークとルート テーブルは、同じ Azure の場所とサブスクリプションに共存している必要があります。

サブネットとルート テーブルは、一対多のリレーションシップを持ちます。 サブネットは、関連付けられたルート テーブルなしで定義することも、1 つのルート テーブルを使用して定義することもできます。 ルート テーブルは、ゼロ、1 つ、または多数のサブネットに関連付けることができます。 

サブネットからのトラフィックは、次に基づいてルーティングされます。

- ルート テーブル内に定義されているルート
- [既定のルート](/azure/virtual-network/virtual-networks-udr-overview#default)
- オンプレミス ネットワークから伝達されたルート

仮想マシンは Azure Virtual Network ゲートウェイに接続する必要があります。 ゲートウェイは ExpressRoute、または VPN (VPN ゲートウェイで BGP を使用する場合) にすることができます。

次のプレイブックを `route_table_associate.yml` という名前で保存します。

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create virtual network
      azure_rm_virtualnetwork:
        name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
        dns_servers:
        - 127.0.0.1
        - 127.0.0.3
    - name: Create a subnet with route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
        route_table: "{ route_table_name }"
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>サブネットからのルート テーブルの関連付けの解除

このセクションのプレイブック コードは、ルート テーブルのサブネットとの関連付けを解除します。

ルート テーブルのサブネットとの関連付けを解除するときは、サブネットの `route_table` を `None` に設定します。 

次のプレイブックを `route_table_dissociate.yml` という名前で保存します。

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    resource_group: myResourceGroup
  tasks:
    - name: Dissociate a route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>ルートの作成

このセクションのプレイブック コードは、ルート テーブル内にルートを作成します。 

次のプレイブックを `route_create.yml` という名前で保存します。

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        next_hop_type: virtual_network_gateway
        address_prefix: "10.1.0.0/16"
        route_table_name: "{{ route_table_name }}"
```

プレイブックを実行する前に、次の注意事項を参照してください。

* `virtual_network_gateway` は `next_hop_type` と定義されます。 Azure のルート選択方法の詳細については、[ルーティングの概要](/azure/virtual-network/virtual-networks-udr-overview)に関するページを参照してください。
* `address_prefix` は `10.1.0.0/16` と定義されます。 このプレフィックスをルート テーブル内で重複させることはできません。

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>ルートの削除

このセクションのプレイブック コードは、ルート テーブルからルートを削除します。

次のプレイブックを `route_delete.yml` という名前で保存します。

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Remove route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        route_table_name: "{{ route_table_name }}"
        state: absent
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>ルート テーブル情報の取得

このセクションのプレイブックは、Ansible モジュール `azure_rm_routetable_facts` を使用してルート テーブル情報を取得します。

次のプレイブックを `route_table_facts.yml` という名前で保存します。

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks: 
    - name: Get route table information
      azure_rm_routetable_facts:
         resource_group: "{{ resource_group }}"
         name: "{{ route_table_name }}"
      register: query
    
    - debug:
         var: query.route_tables[0]
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>ルート テーブルの削除

このセクションのプレイブック コードはルート テーブルを削除します。

ルート テーブルが削除されると、そのルートもすべて削除されます。

ルート テーブルがサブネットに関連付けられている場合は削除できません。 ルート テーブルを削除する前に、[ルート テーブルのすべてのサブネットとの関連付けを解除](#dissociate-a-route-table-from-a-subnet)してください。 

次のプレイブックを `route_table_delete.yml` という名前で保存します。

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [Azure 上の Ansible](/azure/ansible/)