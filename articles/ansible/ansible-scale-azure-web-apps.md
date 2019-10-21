---
title: チュートリアル - Ansible を使用して Azure App Service のアプリをスケーリングする
description: Azure App Service のアプリをスケールアップする方法について説明します
keywords: ansible, azure, devops, bash, プレイブック, Azure App Service, Web アプリ, スケール, Java
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 0adcddb8afa4f0e3d0b9288f351dae7d77922612
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241491"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>チュートリアル:Ansible を使用して Azure App Service のアプリをスケーリングする

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 既存の App Service プランのファクトを取得する
> * App Service プランを 3 つのワーカーを持つ S2 にスケールアップする

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Azure App Service アプリ** - Azure App Service アプリがない場合は、[Ansible を使用して Azure App Service でアプリを構成](ansible-create-configure-azure-web-apps.md)します。

## <a name="scale-up-an-app"></a>アプリのスケールアップ

スケーリングには、"*スケールアップ*" と "*スケールアウト*" の 2 つのワークフローがあります。

**スケールアップ:** スケールアップとは、リソースを追加で取得することです。 対象となるリソースには、CPU、メモリ、ディスク領域、VM などがあります。 アプリをスケールアップするには、アプリが属している App Service プランの価格レベルを変更します。 
**スケールアウト:** スケールアウトとは、アプリを実行する VM インスタンスの数を増やすことです。 App Service プランの価格レベルに応じて、最大 20 個のインスタンスまでスケールアウトできます。 [自動スケーリング](/azure/azure-monitor/platform/autoscale-get-started)を使用すると、事前に定義したルールとスケジュールに基づいてインスタンス数を自動的にスケーリングできます。

このセクションのプレイブック コードでは、次の操作を定義します。

* 既存の App Service プランのファクトを取得する
* App Service プランを 3 つのワーカーを持つ S2 に更新します。

次のプレイブックを `webapp_scaleup.yml` という名前で保存します。

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App service plan
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku

  - name: Scale up the App service plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      is_linux: true
      sku: S2
      number_of_workers: 3
      
  - name: Get facts
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook webapp_scaleup.yml
```

プレイブックを実行すると、次の結果のような出力が表示されます。

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get facts of existing App service plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] 
changed: [localhost]

TASK [Get facts] 
ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP 
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [Azure 上の Ansible](/azure/ansible/)