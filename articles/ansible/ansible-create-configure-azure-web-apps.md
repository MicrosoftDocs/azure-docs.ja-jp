---
title: Ansible を使用して Azure Web アプリを作成する
description: Linux 上の App Service で、Ansible を使用して、Java 8 と Tomcat コンテナー ランタイムを使った Web アプリを作成する方法について学習します。
ms.service: azure
keywords: ansible、azure、devops、bash、プレイブック、Azure App Service、Web アプリ、Java
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: 5f67a9f7d629eec9ab1462a25940355869c1cd28
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791224"
---
# <a name="create-azure-app-service-web-apps-by-using-ansible"></a>Ansible を使用して Azure App Service Web アプリを作成する
[Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/overview) (または単に Web Apps) は、Web アプリケーション、REST API、モバイル バックエンドをホストします。 開発には、.NET、.NET Core、Java、Ruby、Node.js、PHP、Python のうち、お気に入りの言語をご利用いただけます。

Ansible を使用すると、環境でのリソースの展開と構成を自動化することができます。 この記事では、Ansible を使用して、Java ランタイムを使った Web アプリを作成する方法について説明します。 

## <a name="prerequisites"></a>前提条件
- **Azure サブスクリプション** - Azure サブスクリプションをお持ちでない場合は、開始する前に[無料のアカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> このチュートリアルでは、次のサンプルのプレイブックを実行するのに Ansible 2.7 が必要です。

## <a name="create-a-simple-app-service"></a>簡素な App Service の作成
このセクションでは、次のリソースを定義するサンプルの Ansible プレイブックを示します。
- App Service プランと Web アプリのデプロイ先となるリソース グループ
- Linux 上の App Service で Java 8 と Tomcat コンテナー ランタイムを使った Web アプリ

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
前述のプレイブックを **firstwebapp.yml** として保存します。

プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。
```bash
ansible-playbook firstwebapp.yml
```

Ansible プレイブックの実行による出力では、Web アプリが正常に作成されていることを示しています。

```Output
PLAY [localhost] *************************************************

TASK [Gathering Facts] *************************************************
ok: [localhost]

TASK [Create a resource group] *************************************************
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] *************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP *************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-service-by-using-traffic-manager"></a>Traffic Manager を使用した App Service の作成
[Azure Traffic Manager](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager) を使用すると、Web クライアントからの要求を Azure App Service 内のアプリに振り分ける方法を制御できます。 App Service のエンドポイントが Azure Traffic Manager のプロファイルに追加されると、Azure Traffic Manager は App Service アプリの状態を追跡します。 それらの状態としては、実行中、停止、削除済みなどがあります。 これにより、トラフィックを受信すべきエンドポイントを Traffic Manager が判断できるようになります。

App Service では、アプリは "[App Service プラン](https://docs.microsoft.com/azure/app-service/overview-hosting-plans
)" で実行されます。 App Service プランでは、Web アプリを実行するための一連のコンピューティング リソースを定義します。 App Service プランと Web アプリを別のグループで管理できます。

このセクションでは、次のリソースを定義するサンプルの Ansible プレイブックを示します。
- App Service プランの配備先となるリソース グループ
- App Service プラン
- Web アプリのデプロイ先となる二次的リソース グループ
- Linux 上の App Service で Java 8 と Tomcat コンテナー ランタイムを使った Web アプリ
- Traffic Manager プロファイル
- 作成した Web サイトを使用する Traffic Manager エンドポイント

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
前述のプレイブックを **webapp.yml** として保存するか、[プレイブックをダウンロード](https://github.com/Azure-Samples/ansible-playbooks/blob/master/webapp.yml)します。

プレイブックを実行するには、次のように **ansible-playbook** コマンドを使用します。
```bash
ansible-playbook webapp.yml
```

Ansible プレイブックの実行による出力では、App Service プラン、Web アプリ、Traffic Manager プロファイル、エンドポイントが正常に作成されていることを示しています。
```Output
PLAY [localhost] *************************************************

TASK [Gathering Facts] *************************************************
ok: [localhost]

TASK [Create resource group] ****************************************************************************
changed: [localhost]

TASK [Create resource group for app service plan] ****************************************************************************
changed: [localhost]

TASK [Create App Service Plan] ****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] ****************************************************************************
changed: [localhost]

TASK [Get web app facts] *****************************************************************************
ok: [localhost]

TASK [Create Traffic Manager Profile] *****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] *****************************************************************************
changed: [localhost]

TASK [Get Traffic Manager Profile facts] ******************************************************************************
ok: [localhost]

PLAY RECAP ******************************************************************************
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [Ansible を使用して Azure App Service Web アプリをスケーリングする](https://docs.microsoft.com/azure/ansible/ansible-scale-azure-web-apps)