---
title: チュートリアル - Ansible を使用して Azure 内で Azure Kubernetes Service (AKS) クラスターを構成する
description: Ansible を使用して、Azure 内に Azure Kubernetes Service クラスターを作成し、管理する方法について説明します。
keywords: ansible, azure, devops, bash, cloudshell, プレイブック, aks, コンテナー, aks, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 9b70a9c364768322a3eae6ef5b92c87b6839c540
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242086"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>チュートリアル:Ansible を使用して Azure 内で Azure Kubernetes Service (AKS) クラスターを構成する

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

ユーザー認証に [Azure Active Directory (AD)](/azure/active-directory/) を使うように AKS を構成できます。 構成後は、Azure AD 認証トークンを使用して AKS クラスターにサインインします。 ユーザーの ID またはディレクトリのグループ メンバーシップに基づく RBAC が可能です。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * AKS クラスターの作成
> * AKS クラスターの構成

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>マネージド AKS クラスターを作成する

このサンプルのプレイブックでは、リソース グループおよびリソース グループ内の AKS クラスターを作成します。

次のプレイブックを `azure_create_aks.yml` という名前で保存します。

```yml
- name: Create Azure Kubernetes Service
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create a managed Azure Container Services (AKS) cluster
    azure_rm_aks:
      name: "{{ aks_name }}"
      location: "{{ location }}"
      resource_group: "{{ resource_group }}"
      dns_prefix: "{{ aks_name }}"
      linux_profile:
        admin_username: "{{ username }}"
        ssh_key: "{{ ssh_key }}"
      service_principal:
        client_id: "{{ client_id }}"
        client_secret: "{{ client_secret }}"
      agent_pool_profiles:
        - name: default
          count: 2
          vm_size: Standard_D2_v2
      tags:
        Environment: Production
```

プレイブックを実行する前に、次の点に注意してください。

- `tasks` 内の最初のセクションでは、`eastus` の場所にある `myResourceGroup` という名前のリソース グループが定義されています。
- `tasks` 内の 2 番目のセクションでは、`myResourceGroup` というリソース グループ内に `myAKSCluster` という名前の AKS クラスターが定義されています。
- `your_ssh_key` プレースホルダーには、"ssh-rsa" (引用符は除く) で始まる実際の RSA 公開キーを 1 行形式で入力してください。

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook azure_create_aks.yml
```

プレイブックを実行すると、次の出力のような結果が表示されます。

```Output
PLAY [Create AKS] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create an Azure Container Services (AKS) cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="scale-aks-nodes"></a>AKS ノードのスケーリング

前のセクションで示したサンプルのプレイブックでは、2 つのノードが定義されています。 `agent_pool_profiles` ブロックの `count` の値を変更して、これらのノードの数を調整します。

次のプレイブックを `azure_configure_aks.yml` という名前で保存します。

```yml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

プレイブックを実行する前に、次の点に注意してください。

- `your_ssh_key` プレースホルダーには、"ssh-rsa" (引用符は除く) で始まる実際の RSA 公開キーを 1 行形式で入力してください。

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook azure_configure_aks.yml
```

プレイブックを実行すると、次の出力のような結果が表示されます。

```Output
PLAY [Scale AKS cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Scaling an existed AKS cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="delete-a-managed-aks-cluster"></a>マネージド AKS クラスターを削除する

このサンプルのプレイブックでは、AKS クラスターを削除します。

次のプレイブックを `azure_delete_aks.yml` という名前で保存します。


```yml
- name: Delete a managed Azure Container Services (AKS) cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    aks_name: myAKSCluster
  tasks:
  - name:
    azure_rm_aks:
      name: "{{ aks_name }}"
      resource_group: "{{ resource_group }}"
      state: absent
  ```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook azure_delete_aks.yml
```

プレイブックを実行すると、次の出力のような結果が表示されます。

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [azure_rm_aks] 

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル:Azure Kubernetes Service (AKS) でのアプリケーションのスケーリング](/azure/aks/tutorial-kubernetes-scale)