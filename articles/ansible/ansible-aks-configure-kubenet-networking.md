---
title: チュートリアル - Ansible を使用して Azure Kubernetes Service (AKS) で kubenet ネットワークを構成する
description: Ansible を使用して Azure Kubernetes Service (AKS) クラスターで kubenet ネットワークを構成する方法について説明します。
keywords: ansible, azure, devops, bash, cloudshell, プレイブック, aks, コンテナー, aks, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 949a55fd8c004bc656d02816231c4ebb6dd8f92b
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242179"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>チュートリアル:Ansible を使用して Azure Kubernetes Service (AKS) で kubenet ネットワークを構成する

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS を使用して、次のネットワーク モデルを使用するクラスターをデプロイできます。

- [Kubenet ネットワーク](/azure/aks/configure-kubenet) - ネットワーク リソースは通常、AKS クラスターのデプロイ時に作成され、構成されます。
- [Azure Container Networking Interface (CNI) ネットワーク](/azure/aks/configure-azure-cni) - AKS クラスターは、既存の仮想ネットワーク リソースと構成に接続されます。

AKS のアプリケーションに対するネットワークの詳細については、「[Azure Kubernetes Service (AKS) でのアプリケーションに対するネットワークの概念](/azure/aks/concepts-network)」を参照してください。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * AKS クラスターの作成
> * Azure kubenet ネットワークの構成

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>仮想ネットワークとサブネットの作成

このセクションのプレイブック コードでは、次の Azure リソースを作成します。

- 仮想ネットワーク
- 仮想ネットワーク内のサブネット

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

このセクションのプレイブック コードでは、仮想ネットワーク内に AKS クラスターを作成します。 

次のプレイブックを `aks.yml` という名前で保存します。

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with vnet
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
          network_plugin: kubenet
          pod_cidr: 192.168.0.0/16
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.0.0.10
          service_cidr: 10.0.0.0/16
  register: aks
```

以下に、サンプルのプレイブックを使用する際に考慮すべき重要な点をいくつか示します。

- `azure_rm_aks_version` モジュールを使用して、サポートされているバージョンを見つけます。
- `vnet_subnet_id` は、前のセクションで作成したサブネットです。
- `network_profile` は、kubenet ネットワーク プラグインのプロパティを定義します。
- `service_cidr` は、AKS クラスター内の内部サービスを IP アドレスに割り当てるために使用します。 この IP アドレス範囲は、ネットワーク内の他の場所で使われていないアドレス空間でなければなりません。 
- `dns_service_ip` アドレスは、サービス IP アドレス範囲の ".10" アドレスにする必要があります。
- `pod_cidr` は、ネットワーク環境の他の場所で使われていない大きいアドレス空間にする必要があります。 このアドレス範囲は、スケールアップ後に予想されるノードの数を格納するのに十分な大きさである必要があります。 このアドレス範囲は、クラスターをデプロイした後は変更できません。
- ポッドの IP アドレス範囲は、クラスター内の各ノードに /24 アドレス空間を割り当てるために使用されます。 次の例の `pod_cidr` の 192.168.0.0/16 では、1 番目のノードに 192.168.0.0/24 が、2 番目のノードに 192.168.1.0/24 が、3 番目のノードに 192.168.2.0/24 が、それぞれ割り当てられてます。
- クラスターをスケーリングまたはアップグレードすると、Azure によって引き続き新しい各ノードにポッドの IP アドレス範囲が割り当てられます。
- このプレイブックは `~/.ssh/id_rsa.pub` から `ssh_key` を読み込みます。 これを変更する場合は、"ssh-rsa" (引用符は除く) で始まる単一行形式を使用してください。
- `client_id` および `client_secret` の値は、既定の資格情報ファイルである `~/.azure/credentials` から読み込まれます。 これらの値をサービス プリンシパルに設定するか、環境変数からこれらの値を読み込むことができます。

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>ネットワーク リソースを関連付ける

AKS クラスターを作成すると、ネットワーク セキュリティ グループとルート テーブルが作成されます。 これらのリソースは、AKS によって管理され、ユーザーがサービスを作成および公開するときに更新されます。 次のように、ネットワーク セキュリティ グループおよびルート テーブルを仮想ネットワーク サブネットと関連付けます。 

次のプレイブックを `associate.yml` という名前で保存します。

```yml
- name: Get route table
  azure_rm_routetable_facts:
      resource_group: "{{ node_resource_group }}"
  register: routetable

- name: Get network security group
  azure_rm_securitygroup_facts:
      resource_group: "{{ node_resource_group }}"
  register: nsg

- name: Parse subnet id
  set_fact:
      subnet_name: "{{ vnet_subnet_id | regex_search(subnet_regex, '\\1') }}"
      subnet_rg: "{{ vnet_subnet_id | regex_search(rg_regex, '\\1') }}"
      subnet_vn: "{{ vnet_subnet_id | regex_search(vn_regex, '\\1') }}"
  vars:
      subnet_regex: '/subnets/(.+)'
      rg_regex: '/resourceGroups/(.+?)/'
      vn_regex: '/virtualNetworks/(.+?)/'

- name: Associate network resources with the node subnet
  azure_rm_subnet:
      name: "{{ subnet_name[0] }}"
      resource_group: "{{  subnet_rg[0] }}"
      virtual_network_name: "{{ subnet_vn[0] }}"
      security_group: "{{ nsg.ansible_facts.azure_securitygroups[0].id }}"
      route_table: "{{ routetable.route_tables[0].id }}"
```

以下に、サンプルのプレイブックを使用する際に考慮すべき重要な点をいくつか示します。

- `node_resource_group` は、AKS ノードが作成されるリソース グループ名です。
- `vnet_subnet_id` は、前のセクションで作成したサブネットです。


## <a name="run-the-sample-playbook"></a>サンプル プレイブックの実行

このセクションでは、この記事で作成するタスクを呼び出す完全なサンプル プレイブックを示します。 

次のプレイブックを `aks-kubenet.yml` という名前で保存します。

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Associate network resources with the node subnet
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
           node_resource_group: "{{ aks.node_resource_group }}"
       include_tasks: associate.yml

     - name: Get details of the AKS
       azure_rm_aks_facts:
           name: "{{ name }}"
           resource_group: "{{ resource_group }}"
           show_kubeconfig: user
       register: output

     - name: Show AKS cluster detail
       debug:
           var: output.aks[0]
```

`vars` セクションで、次の変更を行います。

- `resource_group` キーについては、`aksansibletest` 値を実際のリソース グループ名に変更します。
- `name` キーについては、`aksansibletest` 値を実際の AKS 名に変更します。
- `Location` キーについては、`eastus` 値を実際のリソース グループの場所に変更します。

`ansible-playbook` コマンドを使用して完全なプレイブックを実行します。

```bash
ansible-playbook aks-kubenet.yml
```

プレイブックを実行すると、次の出力のような結果が表示されます。

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exist] 
ok: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-kubenet/vnet.yml for localhost

TASK [Create vnet] 
ok: [localhost]

TASK [Create subnet] 
ok: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-kubenet/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Associate network resources with the node subnet] 
included: /home/devops/aks-kubenet/associate.yml for localhost

TASK [Get route table] 
ok: [localhost]

TASK [Get network security group] 
ok: [localhost]

TASK [Parse subnet id] 
ok: [localhost]

TASK [Associate network resources with the node subnet] 
changed: [localhost]

TASK [Get details of the AKS] 
ok: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "output.aks[0]": {
        "id": /subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "apiVersion: ...",
        "location": "eastus",
        "name": "aksansibletest",
        "properties": {
            "agentPoolProfiles": [
                {
                    "count": 3,
                    "maxPods": 110,
                    "name": "nodepool1",
                    "osDiskSizeGB": 100,
                    "osType": "Linux",
                    "storageProfile": "ManagedDisks",
                    "vmSize": "Standard_D2_v2",
                    "vnetSubnetID": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
                }
            ],
            "dnsPrefix": "aksansibletest",
            "enableRBAC": false,
            "fqdn": "aksansibletest-cda2b56c.hcp.eastus.azmk8s.io",
            "kubernetesVersion": "1.12.6",
            "linuxProfile": {
                "adminUsername": "azureuser",
                "ssh": {
                    "publicKeys": [
                        {
                            "keyData": "ssh-rsa ..."
                        }
                    ]
                }
            },
            "networkProfile": {
                "dnsServiceIP": "10.0.0.10",
                "dockerBridgeCidr": "172.17.0.1/16",
                "networkPlugin": "kubenet",
                "podCidr": "192.168.0.0/16",
                "serviceCidr": "10.0.0.0/16"
            },
            "nodeResourceGroup": "MC_aksansibletest_pcaksansibletest_eastus",
            "provisioningState": "Succeeded",
            "servicePrincipalProfile": {
                "clientId": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
            }
        },
        "type": "Microsoft.ContainerService/ManagedClusters"
    }
}

PLAY RECAP 
localhost                  : ok=15   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事で作成したリソースが不要になったら、削除してください。 

`cleanup.yml` として次のコードを保存します。

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

`vars` セクションで、`{{ resource_group_name }}` プレースホルダーを実際のリソース グループの名前に置き換えます。

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル - Ansible を使用して AKS で Azure Container Networking Interface (CNI) ネットワークを構成する](./ansible-aks-configure-cni-networking.md)