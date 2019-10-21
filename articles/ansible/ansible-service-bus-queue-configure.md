---
title: チュートリアル - Ansible を使用して Azure Service Bus のキューを構成する
description: Ansible を使用して、Azure Service Bus キューを作成する方法について説明します
keywords: ansible、azure、devops、bash、プレイブック、サービス バス、キュー
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: a48796c2177a8b5b818553bf8aa0ff36f712d4e0
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241397"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>チュートリアル:Ansible を使用して Azure Service Bus のキューを構成する

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * キューを作成する
> * SAS ポリシーを作成する
> * 名前空間情報を取得する
> * キュー情報を取得する
> * キューの SAS ポリシーを取り消す

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Service Bus キューを作成する

プレイブックのサンプル コードでは、次のリソースが作成されます。
- Azure リソース グループ
- リソース グループ内の Service Bus 名前空間
- 名前空間を持つ Service Bus キュー

次のプレイブックを `servicebus_queue.yml` という名前で保存します。

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: queue
    - debug:
          var: queue
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>SAS ポリシーを作成する

[Shared Access Signature (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) は、トークンを使う要求ベースの承認メカニズムです。 

サンプル プレイブック コードは、異なる特権を持つ Service Bus キューの 2 つの SAS ポリシーを作成します。

次のプレイブックを `servicebus_queue_policy.yml` という名前で保存します。

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: listen_send
      register: policy
    - debug:
          var: policy
```

プレイブックを実行する前に、次の注意事項を参照してください。
- `rights` 値は、ユーザーがキューに対して持っている特権を表します。 `manage`、`listen`、`send`、または `listen_send` の値のいずれかを指定します。

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>名前空間情報を取得する

サンプル プレイブック コードは、名前空間情報のクエリを実行します。

次のプレイブックを `servicebus_namespace_info.yml` という名前で保存します。

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
  tasks:
    - name: Get a namespace's information
      azure_rm_servicebus_facts:
          type: namespace
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: ns
    - debug:
          var: ns
```

プレイブックを実行する前に、次の注意事項を参照してください。
- `show_sas_policies` 値は、指定した名前空間下に SAS ポリシーを表示するかどうかを示します。 既定では、値は `False` になり、ネットワークのオーバーヘッドが追加されないようにします。

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>キュー情報を取得する

プレイブックのサンプル コードは、キュー情報をクエリします。 

次のプレイブックを `servicebus_queue_info.yml` という名前で保存します。

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Get a queue's information
      azure_rm_servicebus_facts:
          type: queue
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: queue
    - debug:
          var: queue
```

プレイブックを実行する前に、次の注意事項を参照してください。
- `show_sas_policies` の値は、指定したキューの下に SAS ポリシーを表示するかどうかを示します。 既定では、この値は `False` に設定され、ネットワークのオーバーヘッドが追加されないようにします。

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>キューの SAS ポリシーを取り消す

サンプル プレイブック コードは、キューの SAS ポリシーを削除します。

次のプレイブックを `servicebus_queue_policy_delete.yml` という名前で保存します。

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事で作成したリソースが不要になったら、削除してください。 

`cleanup.yml` として次のコードを保存します。

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Delete queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
    - name: Delete resource group
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          state: absent
          force_delete_nonempty: yes
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [チュートリアル:Ansible を使用して Azure Service Bus のトピックを構成する](ansible-service-bus-topic-configure.md)