---
title: チュートリアル - Ansible を使用して Azure Kubernetes Service (AKS) で Azure CNI ネットワークを構成する
description: Ansible を使用して Azure Kubernetes Service (AKS) クラスターで kubenet ネットワークを構成する方法について説明します。
keywords: ansible, azure, devops, bash, cloudshell, プレイブック, aks, コンテナー, aks, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 04da0e8fb06d0a32c8e8bdc39d7722fc1c3fcdba
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242027"
---
# <a name="tutorial-configure-azure-cni-networking-in-azure-kubernetes-service-aks-using-ansible"></a>チュートリアル:Ansible を使用して Azure Kubernetes Service (AKS) で Azure CNI ネットワークを構成する

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS を使用して、次のネットワーク モデルを使用するクラスターをデプロイできます。

- [Kubenet ネットワーク](/azure/aks/configure-kubenet) - ネットワーク リソースは通常、AKS クラスターのデプロイ時に作成され、構成されます。
- [Azure CNI ネットワーク](/azure/aks/configure-azure-cni) - AKS クラスターは、既存の仮想ネットワーク (VNET) リソースと構成に接続されます。

AKS のアプリケーションに対するネットワークの詳細については、「[Azure Kubernetes Service (AKS) でのアプリケーションに対するネットワークの概念](/azure/aks/concepts-network)」を参照してください。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * AKS クラスターの作成
> * Azure CNI ネットワークの構成

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 

## <a name="create-a-virtual-network-and-subnet"></a>仮想ネットワークとサブネットの作成

このセクションのプレイブックのサンプル コードは、次の目的で使用します。

- 仮想ネットワークの作成
- 仮想ネットワーク内にサブネットを作成する

次のプレイブックを `vnet.yml` という名前で保存します。

```yml
- name: Create vnet
  azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefixes_cidr:
          - 10.0.0.0/8

- name: Create subnet
  azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefix_cidr: 10.240.0.0/16
      virtual_network_name: "{{ name }}"
  register: subnet
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>仮想ネットワークに AKS クラスターを作成する

このセクションのプレイブックのサンプル コードは、次の目的で使用します。

- 仮想ネットワーク内に AKS クラスターを作成する。

次のプレイブックを `aks.yml` という名前で保存します。

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster within a VNet
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
          vnet_subnet_id: "{{ vnet_subnet_id }}"
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      network_profile:
          network_plugin: azure
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.2.0.10
          service_cidr: 10.2.0.0/24
  register: aks
```

以下に、サンプルのプレイブックを使用する際に考慮すべき重要な点をいくつか示します。

- `azure_rm_aks_version` モジュールを使用して、サポートされているバージョンを見つけます。
- `vnet_subnet_id` は、前のセクションで作成したサブネットです。
- このプレイブックは `~/.ssh/id_rsa.pub` から `ssh_key` を読み込みます。 これを変更する場合は、"ssh-rsa" (引用符は除く) で始まる単一行形式を使用してください。
- `client_id` および `client_secret` の値は、既定の資格情報ファイルである `~/.azure/credentials` から読み込まれます。 これらの値をサービス プリンシパルに設定するか、環境変数からこれらの値を読み込むことができます。

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="run-the-sample-playbook"></a>サンプル プレイブックを実行する

このセクションのプレイブックのサンプル コードは、このチュートリアル全体を通して示されるさまざまな機能をテストするために使用します。

次のプレイブックを `aks-azure-cni.yml` という名前で保存します。

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exists
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Show AKS cluster detail
       debug:
           var: aks
```

以下に、サンプルのプレイブックを使用する際に考慮すべき重要な点をいくつか示します。

- `aksansibletest` 値は実際のリソース グループ名に変更します。
- `aksansibletest` 値は実際の AKS 名に変更します。
- `eastus` 値は実際のリソース グループの場所に変更します。

ansible-playbook コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook aks-azure-cni.yml
```

プレイブックを実行すると、次の結果のような出力が表示されます。

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exists] 
changed: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-cni/vnet.yml for localhost

TASK [Create vnet] 
changed: [localhost]

TASK [Create subnet] 
changed: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-cni/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "aks": {
        "aad_profile": {},
        "addon": {},
        "agent_pool_profiles": [
            {
                "count": 3,
                "name": "nodepool1",
                "os_disk_size_gb": 100,
                "os_type": "Linux",
                "storage_profile": "ManagedDisks",
                "vm_size": "Standard_D2_v2",
                "vnet_subnet_id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
            }
        ],
        "changed": true,
        "dns_prefix": "aksansibletest",
        "enable_rbac": false,
        "failed": false,
        "fqdn": "aksansibletest-0272707d.hcp.eastus.azmk8s.io",
        "id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "..."
        },
        "location": "eastus",
        "name": "aksansibletest",
        "network_profile": {
            "dns_service_ip": "10.2.0.10",
            "docker_bridge_cidr": "172.17.0.1/16",
            "network_plugin": "azure",
            "network_policy": null,
            "pod_cidr": null,
            "service_cidr": "10.2.0.0/24"
        },
        "node_resource_group": "MC_aksansibletest_aksansibletest_eastus",
        "provisioning_state": "Succeeded",
        "service_principal_profile": {
            "client_id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
        },
        "tags": null,
        "type": "Microsoft.ContainerService/ManagedClusters",
        "warnings": [
            "Azure API profile latest does not define an entry for ContainerServiceClient",
            "Azure API profile latest does not define an entry for ContainerServiceClient"
        ]
    }
}

PLAY RECAP 
localhost                  : ok=9    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事で作成したリソースが不要になったら、削除してください。 

このセクションのプレイブックのサンプル コードは、次の目的で使用します。

- `vars` セクションで参照されているリソース グループを削除する。

次のプレイブックを `cleanup.yml` という名前で保存します。

```yml
---
- hosts: localhost
  vars:
      resource_group: {{ resource_group_name }}
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

以下に、サンプルのプレイブックを使用する際に考慮すべき重要な点をいくつか示します。

- `{{ resource_group_name }}` プレースホルダーは実際のリソース グループの名前に置き換えます。
- 指定したリソース グループ内のすべてのリソースが削除されます。

ansible-playbook コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル:Ansible を使用して AKS で Azure Active Directory を構成する](./ansible-aks-configure-rbac.md)