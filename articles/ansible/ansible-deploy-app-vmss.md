---
title: チュートリアル - Ansible を使用して Azure の仮想マシン スケール セットにアプリをデプロイする
description: Ansible を使用して Azure 仮想マシン スケール セットを構成し、そのスケール セットにアプリケーションをデプロイする方法について説明します。
keywords: ansible, azure, devops, bash, プレイブック, 仮想マシン, 仮想マシン スケール セット, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: f9035259d466a50b83fe0094d43bc0fe985e8c4e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241745"
---
# <a name="tutorial-deploy-apps-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>チュートリアル:Ansible を使用して Azure の仮想マシン スケール セットにアプリをデプロイする

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Azure VM グループのホスト情報を取得する
> * サンプル アプリを複製してビルドする
> * スケール セットに JRE (Java Runtime Environment) をインストールする
> * スケール セットに Java アプリケーションをデプロイする

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]
- **git** - このチュートリアルでは、[git](https://git-scm.com) を使用して Java サンプルをダウンロードします。
- **Java SE Development Kit (JDK)** - [JDK](https://aka.ms/azure-jdks) は、サンプルの Java プロジェクトをビルドする際に使用します。
- **Apache Maven** - [Apache Maven](https://maven.apache.org/download.cgi) は、サンプルの Java プロジェクトをビルドするために使用します。

## <a name="get-host-information"></a>ホスト情報を取得する

このセクションのプレイブック コードでは、仮想マシンのグループのホスト情報を取得します。 このコードは、指定されたリソース グループ内のパブリック IP アドレスとロード バランサーを取得し、`scalesethosts` という名前のホスト グループをインベントリ内に作成します。

このサンプルのプレイブックは、`get-hosts-tasks.yml` という名前で保存します。

  ```yml
  - name: Get facts for all Public IPs within a resource groups
    azure_rm_publicipaddress_facts:
      resource_group: "{{ resource_group }}"
    register: output_ip_address

  - name: Get loadbalancer info
    azure_rm_loadbalancer_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ loadbalancer_name }}"
    register: output

  - name: Add all hosts
    add_host:
      groups: scalesethosts
      hostname: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_facts.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>デプロイするアプリケーションを準備する

このセクションのプレイブック コードでは、`git` を使用して GitHub から Java サンプル プロジェクトを複製し、プロジェクトをビルドします。 

次のプレイブックを `app.yml` という名前で保存します。

  ```yml
  - hosts: localhost
    vars:
      repo_url: https://github.com/spring-guides/gs-spring-boot.git
      workspace: ~/src/helloworld

    tasks:
    - name: Git Clone sample app
      git:
        repo: "{{ repo_url }}"
        dest: "{{ workspace }}"

    - name: Build sample app
      shell: mvn package chdir="{{ workspace }}/complete"
  ```

次のコマンドを使用して、サンプルの Ansible プレイブックを実行します。

  ```bash
  ansible-playbook app.yml
  ```

プレイブックを実行すると、次の結果のような出力が表示されます。

  ```Output
  PLAY [localhost] 

  TASK [Gathering Facts] 
  ok: [localhost]

  TASK [Git Clone sample app] 
  changed: [localhost]

  TASK [Build sample app] 
  changed: [localhost]

  PLAY RECAP 
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-a-scale-set"></a>スケール セットにアプリケーションをデプロイする

このセクションのプレイブック コードでは、次のことを行います。

* `saclesethosts` という名前のホスト グループに JRE をインストールする
* `saclesethosts` という名前のホスト グループに Java アプリケーションをデプロイする

サンプルのプレイブックを取得するには、次の 2 つの方法があります。

* [プレイブックをダウンロード](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-setup-deploy.yml)して `vmss-setup-deploy.yml` に保存する。
* `vmss-setup-deploy.yml` という名前の新規ファイルを作成して、次のコンテンツをコピーする。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    scaleset_name: myScaleSet
    loadbalancer_name: myScaleSetLb
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
  - include: get-hosts-tasks.yml

- name: Install JRE on a scale set
  hosts: scalesethosts
  become: yes
  vars:
    workspace: ~/src/helloworld
    admin_username: azureuser

  tasks:
  - name: Install JRE
    apt:
      name: default-jre
      update_cache: yes

  - name: Copy app to Azure VM
    copy:
      src: "{{ workspace }}/complete/target/gs-spring-boot-0.1.0.jar"
      dest: "/home/{{ admin_username }}/helloworld.jar"
      force: yes
      mode: 0755

  - name: Start the application
    shell: java -jar "/home/{{ admin_username }}/helloworld.jar" >/dev/null 2>&1 &
    async: 5000
    poll: 0
```

プレイブックを実行する前に、次の注意事項を参照してください。

* `vars` セクションで、`{{ admin_password }}` プレースホルダーを自分のパスワードに置き換えます。
* 接続の種類として ssh を使用するときにパスワードを併用する場合は、sshpass プログラムをインストールします。

    Ubuntu:

    ```bash
    apt-get install sshpass
    ```

    CentOS:

    ```bash
    yum install sshpass
    ```

* 環境によっては、キーの代わりに SSH パスワードを使用するとエラーが発生することがあります。 このエラーが発生した場合は、ホスト キーの検査を無効にするために、`/etc/ansible/ansible.cfg` または `~/.ansible.cfg` に次の行を追加します。

    ```bash
    [defaults]
    host_key_checking = False
    ```

次のコマンドを使用して、プレイブックを実行します。

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

ansible-playbook コマンドを実行して出力された内容を見ると、サンプルの Java アプリケーションがスケール セットのホスト グループにインストールされていることがわかります。

  ```Output
  PLAY [localhost]

  TASK [Gathering Facts]
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups]
  ok: [localhost]

  TASK [Get loadbalancer info]
  ok: [localhost]

  TASK [Add all hosts]
  changed: [localhost] ...

  PLAY [Install JRE on scale set]

  TASK [Gathering Facts]
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM]
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application]
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

## <a name="verify-the-results"></a>結果を確認する

スケール セットのロード バランサーの URL にアクセスして、作業の結果を確認します。

![Azure 内のスケール セットで実行中の Java アプリ。](media/ansible-vmss-deploy/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル:Ansible を使用して Azure の仮想マシン スケール セットを自動的にスケーリングする](./ansible-auto-scale-vmss.md)