---
title: チュートリアル - Ansible を使用して Azure App Service のアプリを構成する
description: Java 8 と Tomcat コンテナー ランタイムを使って Azure App Service のアプリを作成する方法について説明します。
keywords: ansible、azure、devops、bash、プレイブック、Azure App Service、Web アプリ、Java
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 5104db6bd7fa57600c7212e041263971ca4c91d4
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242047"
---
# <a name="tutorial-configure-apps-in-azure-app-service-using-ansible"></a>チュートリアル:Ansible を使用して Azure App Service のアプリを構成する

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Java 8 と Tomcat コンテナー ランタイムを使って Azure App Service のアプリを作成する
> * Azure Traffic Manager プロファイルを作成する
> * 作成したアプリを使用して Traffic Manager エンドポイントを定義する

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-basic-app-service"></a>基本的な App Service の作成

このセクションのプレイブック コードでは、次のリソースを定義します。

* App Service プランとアプリがデプロイされる Azure リソース グループ
* Java 8 と Tomcat コンテナー ランタイムを使用する Linux 上の App Service

次のプレイブックを `firstwebapp.yml` という名前で保存します。

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    webapp_name: myfirstWebApp
    plan_name: myAppServicePlan
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create App Service on Linux with Java Runtime
      azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        frameworks:
          - name: "java"
            version: "8"
            settings:
              java_container: tomcat
              java_container_version: 8.5
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook firstwebapp.yml
```

プレイブックを実行すると、次の結果のような出力が表示されます。

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-and-use-azure-traffic-manager"></a>アプリの作成と Azure Traffic Manager プロファイルの使用

[Azure Traffic Manager](/azure/app-service/web-sites-traffic-manager) を使用すると、Web クライアントからの要求を Azure App Service 内のアプリに振り分ける方法を制御できます。 App Service のエンドポイントが Azure Traffic Manager のプロファイルに追加されると、Azure Traffic Manager は App Service アプリの状態を追跡します。 それらの状態としては、実行中、停止、削除済みなどがあります。 Traffic Manager は、トラフィックを受信すべきエンドポイントを判断するために使用されます。

App Service では、アプリは "[App Service プラン](/azure/app-service/overview-hosting-plans)" で実行されます。 App Service プランでは、アプリを実行するための一連のコンピューティング リソースを定義します。 App Service プランと Web アプリを別のグループで管理できます。

このセクションのプレイブック コードでは、次のリソースを定義します。

* App Service プランがデプロイされる Azure リソース グループ
* App Service プラン
* アプリがデプロイされる Azure リソース グループ
* Java 8 と Tomcat コンテナー ランタイムを使用する Linux 上の App Service
* Traffic Manager プロファイル
* 作成したアプリを使用する Traffic Manager エンドポイント

次のプレイブックを `webapp.yml` という名前で保存します。

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group_webapp: myResourceGroupWebapp
    resource_group: myResourceGroup
    webapp_name: myLinuxWebApp
    plan_name: myAppServicePlan
    location: eastus
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group_webapp }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group_webapp }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        app_settings:
          testkey: "testvalue"
        frameworks:
          - name: java
            version: 8
            settings:
              java_container: "Tomcat"
              java_container_version: "8.5"

  - name: Get web app facts
    azure_rm_webapp_facts:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ webapp_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ traffic_manager_profile_name }}"
      location: global
      routing_method: performance
      dns_config:
        relative_name: "{{ traffic_manager_profile_name }}"
        ttl:  60
      monitor_config:
        protocol: HTTPS
        port: 80
        path: '/'

  - name: Add endpoint to traffic manager profile, using created web site
    azure_rm_trafficmanagerendpoint:
      resource_group: "{{ resource_group_webapp }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook webapp.yml
```

プレイブックを実行すると、次の結果のような出力が表示されます。

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create resource group for app service plan] 
changed: [localhost]

TASK [Create App Service Plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
changed: [localhost]

TASK [Get web app facts] 
ok: [localhost]

TASK [Create Traffic Manager Profile] 
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] 
changed: [localhost]

TASK [Get Traffic Manager Profile facts] 
ok: [localhost]

PLAY RECAP 
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [チュートリアル:Ansible を使用して Azure App Service のアプリをスケーリングする](/azure/ansible/ansible-scale-azure-web-apps)