---
title: チュートリアル - Ansible を使用して Azure Service Bus のトピックを構成する
description: Ansible を使用して、Azure Service Bus のトピックを作成する方法について説明します
keywords: ansible, azure, devops, bash, プレイブック, service bus, トピック, サブスクリプション
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 952779db582f9437f10608bf86b0b80560ded2c0
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241212"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>チュートリアル:Ansible を使用して Azure Service Bus のトピックを構成する

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * トピックを作成する
> * サブスクリプションの作成
> * SAS ポリシーを作成する
> * 名前空間情報を取得する
> * トピックとサブスクリプションの情報を取得する
> * SAS ポリシーを取り消す

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>Service Bus トピックを作成する

サンプル プレイブック コードは、次のリソースを作成します。
- Azure リソース グループ
- リソース グループ内の Service Bus 名前空間
- 名前空間の Service Bus トピック

次のプレイブックを `servicebus_topic.yml` という名前で保存します。

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: topic
    - debug:
          var: topic
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>サブスクリプションを作成する

サンプル プレイブック コードは、Service Bus トピックにサブスクリプションを作成します。 Azure Service Bus トピックは、複数のサブスクリプションを持つことができます。 トピックのサブスクライバーは、そのトピックに送信された各メッセージのコピーを受信できます。 サブスクリプションは名前付きエンティティであり、永続的に作成されますが、必要に応じて期限切れさせることもできます。

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Create a subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs
    - debug:
          var: subs
```

次のプレイブックを `servicebus_subscription.yml` という名前で保存します。

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>SAS ポリシーを作成する

[Shared Access Signature (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) は、トークンを使う要求ベースの承認メカニズムです。 

サンプル プレイブック コードは、異なる特権を持つ Service Bus キューの 2 つの SAS ポリシーを作成します。

次のプレイブックを `servicebus_topic_policy.yml` という名前で保存します。

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-{{ item }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: "{{ item }}"
      with_items:
        - send
        - listen
      register: policy
    - debug:
          var: policy
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook servicebus_topic_policy.yml
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

## <a name="retrieve-topic-and-subscription-information"></a>トピックとサブスクリプションの情報を取得する

サンプル プレイブック コードは、次の情報のクエリを実行します。
- Service Bus トピックの情報
- トピックのサブスクリプションの詳細の一覧
 
次のプレイブックを `servicebus_list.yml` という名前で保存します。

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Get a topic's information
      azure_rm_servicebus_facts:
          type: topic
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: topic_fact
    - name: "List subscriptions under topic {{ topic }}"
      azure_rm_servicebus_facts:
          type: subscription
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs_fact
    - debug:
          var: "{{ item }}"
      with_items:
        - topic_fact.servicebuses[0]
        - subs_fact.servicebuses
```

プレイブックを実行する前に、次の注意事項を参照してください。
- `show_sas_policies` の値は、指定したキューの下に SAS ポリシーを表示するかどうかを示します。 既定では、この値は `False` に設定され、ネットワークのオーバーヘッドが追加されないようにします。

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook servicebus_list.yml
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
      topic: servicebustesttopic
  tasks:
    - name: Delete a policy
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-policy"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook servicebus_topic_policy_delete.yml
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
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Delete subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
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
> [Azure 上の Ansible](/azure/ansible/)