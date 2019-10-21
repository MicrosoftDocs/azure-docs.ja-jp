---
title: チュートリアル - Ansible を使用して Azure 内に仮想マシン スケール セットを構成する
description: Ansible を使用して、Azure で仮想マシン スケール セットを作成して構成する方法について説明します
keywords: ansible, azure, devops, bash, プレイブック, 仮想マシン, 仮想マシン スケール セット, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 1d9b8cd207596aefa01af852627f11cb9b4ce5dc
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241725"
---
# <a name="tutorial-configure-virtual-machine-scale-sets-in-azure-using-ansible"></a>チュートリアル:Ansible を使用して Azure 内に仮想マシン スケール セットを構成する

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * VM 用のリソースを構成する
> * スケール セットを構成する
> * その VM インスタンスを増加してスケール セットをスケーリングする 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-a-scale-set"></a>スケール セットを構成する

このセクションのプレイブック コードは、次のリソースを定義します。

* **リソース グループ**。すべてのリソースがデプロイされるリソース グループです。
* **仮想ネットワーク**。10.0.0.0/16 アドレス空間内の仮想ネットワークです。
* **サブネット**。仮想ネットワーク内のサブネットです。
* **パブリック IP アドレス**。このアドレスを使用して、インターネットのリソースにアクセスできます。
* **ネットワーク セキュリティ グループ**。スケール セットへのネットワーク トラフィックおよびスケール セットからのネットワーク トラフィックのフローを制御します。
* **ロード バランサー**。ロード バランサー ルールを使用して、定義された一連の VM にトラフィックを分散します。
* **仮想マシン スケール セット**。作成されたすべてのリソースを使用します。

サンプル プレイブックを取得するには、次の 2 つの方法があります。

* [プレイブックをダウンロード](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml)して `vmss-create.yml` に保存する。
* `vmss-create.yml` という名前の新規ファイルを作成して、次のコンテンツをコピーする。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    vmss_lb_name: myScaleSetLb
    location: eastus
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        address_prefixes: "10.0.0.0/16"
    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ vmss_name }}"
    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
    - name: Create Network Security Group that allows SSH
      azure_rm_securitygroup:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        rules:
          - name: SSH
            protocol: Tcp
            destination_port_range: 22
            access: Allow
            priority: 1001
            direction: Inbound

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_lb_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 8080
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 8080
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create Scale Set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vmss_name }}"
        subnet_name: "{{ vmss_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
        load_balancer: "{{ vmss_lb_name }}"
        data_disks:
          - lun: 0
            disk_size_gb: 20
            managed_disk_type: Standard_LRS
            caching: ReadOnly
          - lun: 1
            disk_size_gb: 30
            managed_disk_type: Standard_LRS
            caching: ReadOnly
```

プレイブックを実行する前に、次の注意事項を参照してください。

* `vars` セクションで、`{{ admin_password }}` プレースホルダーを自分のパスワードに置き換えます。

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook vmss-create.yml
```

プレイブックを実行すると、次の結果のような出力が表示されます。

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Create public IP address] 
changed: [localhost]

TASK [Create Network Security Group that allows SSH] 
changed: [localhost]

TASK [Create a load balancer] 
changed: [localhost]

TASK [Create Scale Set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=8    changed=7    unreachable=0    failed=0

```

## <a name="view-the-number-of-vm-instances"></a>VM インスタンスの数を表示する

[構成されているスケール セット](#configure-a-scale-set)には、現在 2 つのインスタンスがあります。 次の手順を使用してその値を確認します。

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。

1. 構成したスケール セットに移動します。

1. 次のように、インスタンスの数がかっこ内に入ったスケール セット名を確認できます。`Standard_DS1_v2 (2 instances)`

1. 次のコマンドを実行して、[Azure Cloud Shell](https://shell.azure.com/) でインスタンスの数を確認することもできます。

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    Cloud Shell で Azure CLI コマンドを実行した結果に、現在 3 つのインスタンスが存在することが示されています。 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="scale-out-a-scale-set"></a>スケール セットをスケールアウトする

次のセクションのプレイブック コードは、スケール セットに関する情報を取得して、その容量を 2 から 3 に変更します。

サンプル プレイブックを取得するには、次の 2 つの方法があります。

* [プレイブックをダウンロード](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml)して `vmss-scale-out.yml` に保存する。
* `vmss-scale-out.yml` という名前の新規ファイルを作成して、次のコンテンツをコピーする。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
  tasks: 
    - name: Get scaleset info
      azure_rm_virtualmachine_scaleset_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        format: curated
      register: output_scaleset

    - name: Dump scaleset info
      debug:
        var: output_scaleset

    - name: Modify scaleset (change the capacity to 3)
      set_fact:
        body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

    - name: Update something in that scale set
      azure_rm_virtualmachinescaleset: "{{ body }}"
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook vmss-scale-out.yml
```

プレイブックを実行すると、次の結果のような出力が表示されます。

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get scaleset info] 
ok: [localhost]

TASK [Dump scaleset info] 
ok: [localhost] => {
    "output_scaleset": {
        "ansible_facts": {
            "azure_vmss": [
                {
                    ......
                }
            ]
        },
        "changed": false,
        "failed": false
    }
}

TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] 
ok: [localhost]

TASK [Update something in that scale set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=5    changed=1    unreachable=0    failed=0
```

## <a name="verify-the-results"></a>結果を確認する

次のように、Azure portal を使用して作業の結果を確認します。

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。

1. 構成したスケール セットに移動します。

1. 次のように、インスタンスの数がかっこ内に入ったスケール セット名を確認できます。`Standard_DS1_v2 (3 instances)` 

1. 次のコマンドを実行して、[Azure Cloud Shell](https://shell.azure.com/) で変更を検証することもできます。

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    Cloud Shell で Azure CLI コマンドを実行した結果には、現在 3 つのインスタンスが存在することが示されています。 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [チュートリアル:Ansible を使用して Azure の仮想マシン スケール セットにアプリをデプロイする](./ansible-deploy-app-vmss.md)