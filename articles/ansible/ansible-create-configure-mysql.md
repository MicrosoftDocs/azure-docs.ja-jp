---
title: チュートリアル - Ansible を使用して Azure Database for MySQL のデータベースを構成する
description: Ansible を使用して Azure Database for MySQL サーバーを作成して構成する方法を学習します。
keywords: ansible、azure、devops、bash、プレイブック、mysql、データベース
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 1b6c9a9aa3abbda7ffd72db0ecb137b3c9da1a6c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241837"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>チュートリアル:Ansible を使用して Azure Database for MySQL のデータベースを構成する

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure Database for MySQL](/azure/mysql/overview) は、MySQL Community Edition に基づくリレーショナル データベース サービスです。 Azure Database for MySQL では、Web アプリ内の MySQL データベースを管理できます。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * MySql サーバーを作成する
> * MySql データベースを作成する
> * 外部のアプリがサーバーに接続できるように、ファイアウォール規則を構成する
> * Azure Cloud Shell から MySql サーバーに接続する
> * 使用可能な MySQL サーバーをクエリする
> * 接続されているサーバー内のすべてのデータベースを一覧表示する

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>リソース グループの作成

このセクションのプレイブック コードでは、Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。  

次のプレイブックを `rg.yml` という名前で保存します。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

プレイブックを実行する前に、次の注意事項を参照してください。

* `myResourceGroup` という名前のリソース グループが作成されます。
* リソース グループが作成される場所は `eastus` です。

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>MySQL サーバーとデータベースの作成

このセクションのプレイブック コードでは、MySQL サーバーと Azure Database for MySQL インスタンスを作成します。 新しい MySQL サーバーは、1 つの仮想コアを持ち、`mysqlserveransible` という名前の Gen 5 Basic Purpose サーバーです。 データベース インスタンスの名前は `mysqldbansible` です。

価格レベルの詳細については、「[Azure Database for MySQL の価格レベル](/azure/mysql/concepts-pricing-tiers)」を参照してください。 

次のプレイブックを `mysql_create.yml` という名前で保存します。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus
    mysqlserver_name: mysqlserveransible
    mysqldb_name: mysqldbansible
    admin_username: mysqladmin
    admin_password: <server_admin_password> 
  tasks:
    - name: Create MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        sku:
          name: B_Gen5_1
          tier: Basic
        location: "{{ location }}"
        version: 5.6
        enforce_ssl: True
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        storage_mb: 51200
    - name: Create instance of MySQL Database
      azure_rm_mysqldatabase:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
        name: "{{ mysqldb_name }}"
```

プレイブックを実行する前に、次の注意事項を参照してください。

* `vars` セクションの `mysqlserver_name` の値は一意である必要があります。
* `vars` セクションの `<server_admin_password>` をパスワードで置き換えます。

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>ファイアウォール規則を構成する

サーバーレベルのファイアウォール規則により、外部アプリが、Azure MySQL サービスのファイアウォールを経由してサーバーに接続できるようになります。 外部アプリの例として、`mysql` コマンド ライン ツールや MySQL Workbench が挙げられます。

このセクションのプレイブック コードでは、外部の IP アドレスからの接続を許可する、`extenalaccess` という名前のファイアウォール規則を作成します。 

次のプレイブックを `mysql_firewall.yml` という名前で保存します。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
  - name: Open firewall to access MySQL Server from outside
    azure_rm_resource:
      api_version: '2017-12-01'
      resource_group: "{{ resource_group }}"
      provider: dbformysql
      resource_type: servers
      resource_name: "{{ mysqlserver_name }}"
      subresource:
        - type: firewallrules
          name: externalaccess
      body:
        properties:
          startIpAddress: "0.0.0.0"
          endIpAddress: "255.255.255.255"
```

プレイブックを実行する前に、次の注意事項を参照してください。

* vars セクションで、`startIpAddress` と `endIpAddress` を置き換えます。 接続元となる範囲に対応する IP アドレスの範囲を使用してください。
* Azure Database for MySQL との接続では、ポート 3306 が通信に使用されます。 企業ネットワーク内から接続を試みる場合、ポート 3306 での送信トラフィックが許可されていない場合があります。 その場合、会社の IT 部門によってポート 3306 が開放されない限り、サーバーに接続することはできません。
* このプレイブックでは、REST API の直接使用を許可する `azure_rm_resource` モジュールを使用します。

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>サーバーへの接続

このセクションでは、以前に作成したサーバーへ接続するために Azure Cloud Shell を使用します。

1. 次のコードの **[使ってみる]** ボタンを選択します。

    ```azurecli-interactive
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. プロンプトで、次のコマンドを入力してサーバーの状態をクエリします。

    ```sql
    mysql> status
    ```
    
    すべて正常に実行されると、次の結果のような出力が表示されます。
    
    ```
    demo@Azure:~$ mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    Enter password:
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65233
    Server version: 5.6.39.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> status
    --------------
    mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper
    
    Connection id:          65233
    Current database:
    Current user:           mysqladmin@13.76.42.93
    SSL:                    Cipher in use is AES256-SHA
    Current pager:          stdout
    Using outfile:          ''
    Using delimiter:        ;
    Server version:         5.6.39.0 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             mysqlserveransible.mysql.database.azure.com via TCP/IP
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    TCP port:               3306
    Uptime:                 36 min 21 sec
    
    Threads: 5  Questions: 559  Slow queries: 0  Opens: 96  Flush tables: 3  Open tables: 10  Queries per second avg: 0.256
    --------------
    ```
    
## <a name="query-mysql-servers"></a>MySQL サーバーをクエリする

このセクションのプレイブック コードでは、`myResourceGroup` 内の MySQL サーバーをクエリし、検出されたサーバー上のデータベースを一覧表示します。

次のプレイブックを `mysql_query.yml` という名前で保存します。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Query MySQL Servers in current resource group
      azure_rm_mysqlserver_facts:
        resource_group: "{{ resource_group }}"
      register: mysqlserverfacts

    - name: Dump MySQL Server facts
      debug:
        var: mysqlserverfacts

    - name: Query MySQL Databases
      azure_rm_mysqldatabase_facts:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
      register: mysqldatabasefacts

    - name: Dump MySQL Database Facts
      debug:
        var: mysqldatabasefacts
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook mysql_query.yml
```

プレイブックを実行すると、次の結果のような出力が表示されます。

```json
"servers": [
    {
        "admin_username": "mysqladmin",
        "enforce_ssl": false,
        "fully_qualified_domain_name": "mysqlserveransible.mysql.database.azure.com",
        "id": "/subscriptions/685ba005-af8d-4b04-8f16-a7bf38b2eb5a/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysqlserveransible",
        "location": "eastus",
        "name": "mysqlserveransible",
        "resource_group": "myResourceGroup",
        "sku": {
            "capacity": 1,
            "family": "Gen5",
            "name": "B_Gen5_1",
            "tier": "Basic"
        },
        "storage_mb": 5120,
        "user_visible_state": "Ready",
        "version": "5.6"
    }
]
```

また、MySQL データベースに関して次の出力も表示されます。

```json
"databases": [
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "information_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysql",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransibler"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysqldbansible",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "performance_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    }
]
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事で作成したリソースが不要になったら、削除してください。 

次のプレイブックを `cleanup.yml` という名前で保存します。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [Azure 上の Ansible](/azure/ansible/)