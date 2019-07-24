---
title: Ansible を使用した Azure Database for MySQL サーバーの作成と構成
description: Ansible を使用して Azure Database for MySQL サーバーを作成して構成する方法を学習します。
ms.service: azure
keywords: ansible、azure、devops、bash、プレイブック、mysql、データベース
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/23/2018
ms.openlocfilehash: 63472cf9c4b6b16f74ececfb6c6e61cf5f89ff9d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58095400"
---
# <a name="create-and-configure-an-azure-database-for-mysql-server-by-using-ansible"></a>Ansible を使用した Azure Database for MySQL サーバーの作成と構成
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) は、高可用な MySQL データベースをクラウドで実行し、管理し、スケーリングするために使用されるマネージド サービスです。 Ansible を使用すると、環境でのリソースの展開と構成を自動化することができます。 

このクイック スタートでは、Ansible を使用して、Azure Database for MySQL サーバーを作成し、そのファイアウォール規則を構成する方法を示します。 これらのタスクは、Azure portal を使用して 5 分程度で完了できます。

## <a name="prerequisites"></a>前提条件
- **Azure サブスクリプション** - Azure サブスクリプションをお持ちでない場合は、開始する前に[無料のアカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> このチュートリアルでは、次のサンプルのプレイブックを実行するのに Ansible 2.7 が必要です。 

## <a name="create-a-resource-group"></a>リソース グループの作成
リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。  

次の例では、**myResourceGroup** という名前のリソース グループを **eastus** に作成します。

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

前述のプレイブックを **rg.yml** として保存します。 プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。
```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>MySQL サーバーとデータベースの作成
次の例では、**mysqlserveransible** という名前の MySQL サーバーと、**mysqldbansible** という名前の Azure Database for MySQL インスタンスを作成します。 これは、1 つの仮想コアを備えた Gen 5 Basic Purpose サーバーです。 

**mysqlserver_name** の値は一意であることが必要です。 リージョンごとおよびレベルごとの有効な値については、[価格レベルのドキュメント](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers)を参照してください。 `<server_admin_password>` はパスワードに置き換えます。

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

前述のプレイブックを **mysql_create.yml** として保存します。 プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。
```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>ファイアウォール規則を構成する
サーバーレベルのファイアウォール規則により、外部アプリケーションが、Azure MySQL サービスのファイアウォールを経由してサーバーに接続できるようになります。 外部アプリケーションの例としては、**mysql** コマンドライン ツールや MySQL Workbench があります。
次の例では、外部の IP アドレスからの接続を許可する、**extenalaccess** と呼ばれるファイアウォール規則を作成しています。 

**startIpAddress** と **endIpAddress** には実際の値を入力します。 実際の接続元となる場所に対応する IP アドレスの範囲を使用してください。 

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

> [!NOTE]
> Azure Database for MySQL との接続では、ポート 3306 が通信に使用されます。 企業ネットワーク内から接続を試みる場合、ポート 3306 での送信トラフィックが許可されていない場合があります。 その場合、会社の IT 部門によってポート 3306 が開放されない限り、サーバーに接続することはできません。
> 

ここで、**azure_rm_resource** モジュールを使用してこのタスクを実行します。 これにより、REST API を直接使用できるようになります。

前述のプレイブックを **mysql_firewall.yml** として保存します。 プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。
```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server-by-using-the-command-line-tool"></a>コマンドライン ツールを使用してサーバーに接続する
[MySQL をダウンロード](https://dev.mysql.com/downloads/)して、コンピューターにインストールすることができます。 または、コード サンプルの **[試してみる]** ボタンを選択するか、Azure portal の右上のツールバーにある **[>_]** ボタンを選択して、**Azure Cloud Shell** を開くこともできます。

次のコマンドを入力します。 

1. **mysql** コマンドライン ツールを使用してサーバーに接続する
   ```azurecli-interactive
   mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
   ```

2. サーバーの状態を表示します。
   ```sql
   mysql> status
   ```

すべてが問題ない場合は、コマンドライン ツールの出力は次のようになります。

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

## <a name="using-facts-to-query-mysql-servers"></a>ファクトを使用して MySQL サーバーに問い合わせる
次の例では、**myResourceGroup** 内の MySQL サーバーに問い合わせ、続いてそのサーバー上のすべてのデータベースに問い合わせます。

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

前述のプレイブックを **mysql_query.yml** として保存します。 プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。

```bash
ansible-playbook mysql_query.yml
```

その後、MySQL サーバーに関する次の出力が表示されます。 
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

これらのリソースが不要な場合は、下記の例を実行することでこれらを削除できます。 これは **myResourceGroup** という名前のリソース グループを削除します。 

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

前述のプレイブックを **rg_delete.yml** として保存します。 プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。
```bash
ansible-playbook rg_delete.yml
```

新しく作成した MySQL サーバーだけを削除する場合は、次の例を実行します。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Delete MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        state: absent
```

前述のプレイブックを **mysql_delete.yml** として保存します。 プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。
```bash
ansible-playbook mysql_delete.yml
```

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [Azure 上の Ansible](https://docs.microsoft.com/azure/ansible/)
