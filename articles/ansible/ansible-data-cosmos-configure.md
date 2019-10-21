---
title: チュートリアル - Ansible を使用して Azure Cosmos DB アカウントを構成する
description: Ansible を使用して、Azure Cosmos DB を作成して構成する方法について説明します。
keywords: ansible, azure, devops, bash, プレイブック, cosmo db, データベース
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 82cb29cfe3aeb7b6ca43fceca4c900b2eeb148c2
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242064"
---
# <a name="tutorial-configure-azure-cosmos-db-accounts-using-ansible"></a>チュートリアル:Ansible を使用して Azure Cosmos DB アカウントを構成する

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Azure Cosmos DB](/azure/cosmos-db/) はいくつかのデータベースの種類をサポートするデータベース サービスです。 これらのデータベースの種類としては、ドキュメント、キー値、ワイドカラム、およびグラフがあります。 Ansible を使用することにより、ご使用環境でのリソースのデプロイと構成を自動化することができます。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * アカウントの作成
> * アカウント キーを取得する
> * アカウントを削除する

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-random-postfix"></a>ランダムな postfix を作成する

サンプル プレイブックのスニペットは、ランダムな postfix を作成します。 postfix は、Azure Cosmos DB アカウント名の一部として使用されます。

```yml
  - hosts: localhost
    tasks:
      - name: Prepare random postfix
        set_fact:
          rpfx: "{{ 1000 | random }}"
        run_once: yes
```

## <a name="create-resource-group"></a>リソース グループの作成 

このサンプル プレイブックのスニペットは、Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
```

## <a name="create-virtual-network-and-subnet"></a>仮想ネットワークとサブネットを作成する

次のコードは、Azure Cosmos DB アカウントの仮想ネットワークとサブネットを作成します。

```yml
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name }}"
      address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
      dns_servers:
        - 127.0.0.1
        - 127.0.0.3

  - name: Add subnet
    azure_rm_subnet:
      name: "{{ subnet_name }}"
      virtual_network_name: "{{ vnet_name }}"
      resource_group: "{{ resource_group }}"
      address_prefix_cidr: "10.1.0.0/24"
```

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントを作成する

次のコードは、Cosmos DB アカウントを作成します。

```yml
  - name: Create instance of Cosmos DB Account
    azure_rm_cosmosdbaccount:
      resource_group: "{{ resource_group }}"
      name: "{{ cosmosdbaccount_name }}"
      location: eastus
      kind: global_document_db
      geo_rep_locations:
        - name: eastus
          failover_priority: 0
        - name: westus
          failover_priority: 1
      database_account_offer_type: Standard
      is_virtual_network_filter_enabled: yes
      virtual_network_rules:
        - subnet:
            resource_group: "{{ resource_group }}"
            virtual_network_name: "{{ vnet_name }}"
            subnet_name: "{{ subnet_name }}"
          ignore_missing_vnet_service_endpoint: yes
      enable_automatic_failover: yes
```

アカウントの作成には完了までに数分かかります。

## <a name="retrieve-the-keys"></a>キーを取得する

次のコードは、アプリで使用するキーをフェッチします。

```yml
  - name: Get Cosmos DB Account facts with keys
    azure_rm_cosmosdbaccount_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ cosmosdbaccount_name }}"
      retrieve_keys: all
    register: output

  - name: Display Cosmos DB Acccount facts output
    debug:
      var: output
```

## <a name="delete-the-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントを削除する

最後に、最後のスニペットは Azure Cosmos DB アカウントを削除する方法を示します。

```yml
  - name: Delete instance of Cosmos DB Account
    azure_rm_cosmosdbaccount:
      resource_group: "{{ resource_group }}"
      name: "{{ cosmosdbaccount_name }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>サンプル プレイブックを取得する

完全なサンプル プレイブックを取得するには、次の 2 つの方法があります。
- [プレイブックをダウンロード](https://github.com/Azure-Samples/ansible-playbooks/blob/master/cosmosdb_create.yml)して、`cosmosdb.yml` に保存する。
- `cosmosdb.yml` という名前の新規ファイルを作成して、次のコンテンツをコピーする。

```yml
---
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 1000 | random }}"
      run_once: yes

- hosts: localhost
#  roles:
#    - azure.azure_preview_modules
  vars:
    resource_group: "{{ resource_group_name }}"
    location: eastus
    vnet_name: myVirtualNetwork
    subnet_name: mySubnet
    cosmosdbaccount_name: cosmos{{ rpfx }}

  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name }}"
        address_prefixes_cidr:
          - 10.1.0.0/16
          - 172.100.0.0/16
        dns_servers:
          - 127.0.0.1
          - 127.0.0.3

    - name: Add subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"

    - name: Create instance of Cosmos DB Account
      azure_rm_cosmosdbaccount:
        resource_group: "{{ resource_group }}"
        name: "{{ cosmosdbaccount_name }}"
        location: eastus
        kind: global_document_db
        geo_rep_locations:
          - name: eastus
            failover_priority: 0
          - name: westus
            failover_priority: 1
        database_account_offer_type: Standard
        is_virtual_network_filter_enabled: yes
        virtual_network_rules:
          - subnet:
              resource_group: "{{ resource_group }}"
              virtual_network_name: "{{ vnet_name }}"
              subnet_name: "{{ subnet_name }}"
            ignore_missing_vnet_service_endpoint: yes
        enable_automatic_failover: yes

    - name: Get Cosmos DB Account facts with keys
      azure_rm_cosmosdbaccount_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ cosmosdbaccount_name }}"
        retrieve_keys: all
      register: output

    - name: Display Cosmos DB Account facts output
      debug:
        var: output

    - name: Delete instance of Cosmos DB Account
      azure_rm_cosmosdbaccount:
        resource_group: "{{ resource_group }}"
        name: "{{ cosmosdbaccount_name }}"
        state: absent
```

## <a name="run-the-sample-playbook"></a>サンプル プレイブックを実行する

このセクションでは、プレイブックを実行して、この記事で示したさまざまな機能をテストします。

プレイブックを実行する前に、次の変更を加えます。
- `vars` セクションで、`{{ resource_group_name }}` プレースホルダーを実際のリソース グループの名前に置き換えます。
- cosmosdbaccount_name が小文字のみを含んでおり、グローバルに一意であることを確認します。

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook cosmosdb.yml
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事で作成したリソースが不要になったら、削除してください。 

`cleanup.yml` として次のコードを保存します。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [Azure 上の Ansible](/azure/ansible/)