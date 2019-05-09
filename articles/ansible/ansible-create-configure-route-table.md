---
title: Ansible を使用して Azure ルート テーブルを作成、変更、削除する
description: Ansible を使用してルート テーブルを作成、変更、削除する方法について説明します
ms.service: azure
keywords: ansible, azure, devops, bash, プレイブック, ネットワーク, ルート, ルート テーブル
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/17/2018
ms.openlocfilehash: 025a8182d32a7d0d00a48795c848d356eb1c3d4e
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792448"
---
# <a name="create-change-or-delete-an-azure-route-table-using-ansible"></a>Ansible を使用して Azure ルート テーブルを作成、変更、削除する
Azure では、Azure のサブネット、仮想ネットワーク、およびオンプレミスのネットワーク間のトラフィックが自動的にルーティングされます。 Azure の既定のルーティングを変更する場合は、[ルート テーブル](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)を作成して変更します。

Ansible を使用すると、環境でのリソースの展開と構成を自動化することができます。 この記事では、Azure ルート テーブルを作成、変更、または削除する方法、およびサブネットにルート テーブルをアタッチする方法を説明します。 

## <a name="prerequisites"></a>前提条件
- **Azure サブスクリプション** - Azure サブスクリプションをお持ちでない場合は、開始する前に[無料のアカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> このチュートリアルでは、次のサンプルのプレイブックを実行するのに Ansible 2.7 が必要です。

## <a name="create-a-route-table"></a>ルート テーブルの作成
このセクションでは、ルート テーブルを作成するサンプルの Ansible プレイブックを示します。 Azure の場所およびサブスクリプションあたりの作成可能なルート テーブル数は、制限されています。 詳細については、[Azure の制限](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事をご覧ください。 

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

このプレイブックを `route_table_create.yml` として保存します。 プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>サブネットへのルート テーブルの関連付け
サブネットには、0 個または 1 個のルート テーブルを関連付けることができます。 ルート テーブルは、0 個または複数のサブネットに関連付けることができます。 ルート テーブルは仮想ネットワークに関連付けられていないので、ルート テーブルを関連付ける各サブネットにルート テーブルを関連付ける必要があります。 仮想ネットワークが Azure 仮想ネットワーク ゲートウェイに接続されている場合 (VPN ゲートウェイと BGP を使用する場合は ExpressRoute または VPN です)、サブネットを出るすべてのトラフィックは、ルート テーブル内に作成したルート、[既定のルート](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#default)、およびオンプレミスのネットワークから伝達されたルートに基づいてルーティングされます。 ルート テーブルは、そのルート テーブルと同じ Azure の場所とサブスクリプションに存在する仮想ネットワーク内のサブネットにのみ関連付けることができます。

このセクションでは、仮想ネットワークとサブネットを作成し、そのサブネットにルート テーブルを関連付ける、サンプルの Ansible プレイブックをお見せします。

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

このプレイブックを `route_table_associate.yml` として保存します。 Ansible プレイブックを実行するには、次のような **ansible-playbook** コマンドを使用します。

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>サブネットからのルート テーブルの関連付けの解除
サブネットからルート テーブルの関連付けを解除するときは、サブネットの `route_table` に `None` を設定することだけが必要です。 サンプルの Ansible プレイブックを次に示します。 

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

このプレイブックを `route_table_dissociate.yml` として保存します。 Ansible プレイブックを実行するには、次のような **ansible-playbook** コマンドを使用します。

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>ルートの作成
このセクションでは、ルート テーブルの下にルートを作成するサンプルの Ansible プレイブックを示します。 `next_hop_type` として `virtual_network_gateway` が定義され、`address_prefix` として `10.1.0.0/16` が定義されています。 ルート テーブル内の複数のルート上でプレフィックスを重複させることはできません。ただし、プレフィックスを別のプレフィックス内に指定することはできます。 Azure でルートが選択される方法の詳細と、すべての次ホップの種類の詳細な説明については、[ルーティングの概要](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)に関する記事をご覧ください。

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
このプレイブックを `route_create.yml` として保存します。 Ansible プレイブックを実行するには、次のような **ansible-playbook** コマンドを使用します。

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>ルートの削除
このセクションでは、ルート テーブルからルートを削除するサンプルの Ansible プレイブックを示します。

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

このプレイブックを `route_delete.yml` として保存します。 Ansible プレイブックを実行するには、次のような **ansible-playbook** コマンドを使用します。

```bash
ansible-playbook route_delete.yml
```

## <a name="get-information-of-a-route-table"></a>ルート テーブルの情報を取得する
`azure_rm_routetable_facts` という名前の Ansible モジュールを使用して、ルート テーブルの詳細を見ることができます。 facts モジュールでは、ルート テーブルの情報と、それにアタッチされているすべてのルートが返されます。
サンプルの Ansible プレイブックを次に示します。 

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

このプレイブックを `route_table_facts.yml` として保存します。 Ansible プレイブックを実行するには、次のような **ansible-playbook** コマンドを使用します。

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>ルート テーブルの削除
ルート テーブルは、サブネットに関連付けられている場合、削除できません。 ルート テーブルは、削除する前に、すべてのサブネットから[関連付けを解除](#dissociate-a-route-table-from-a-subnet)してください。

ルート テーブルと共にすべてのルートを削除することができます。 サンプルの Ansible プレイブックを次に示します。 

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

このプレイブックを `route_table_delete.yml` として保存します。 Ansible プレイブックを実行するには、次のような **ansible-playbook** コマンドを使用します。

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [Azure 上の Ansible](https://docs.microsoft.com/azure/ansible/)
