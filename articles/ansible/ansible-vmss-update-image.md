---
title: チュートリアル - Ansible を使用して Azure Virtual Machine Scale Sets のカスタム イメージを更新する
description: Ansible を使用して、カスタム イメージを使って Azure 内の仮想マシン スケール セットを更新する方法について説明します。
keywords: ansible, azure, devops, bash, プレイブック, 仮想マシン, 仮想マシン スケール セット, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 3b7baffe6ce0fadbac2dd56b9c8296c80546fa72
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241344"
---
# <a name="tutorial-update-the-custom-image-of-azure-virtual-machine-scale-sets-using-ansible"></a>チュートリアル:Ansible を使用して Azure Virtual Machine Scale Sets のカスタム イメージを更新する

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

VM がデプロイされたら、アプリに必要なソフトウェアを使用して VM を構成します。 この構成タスクを VM ごとに行う代わりに、カスタム イメージを作成することができます。 カスタム イメージは、インストールされているソフトウェアを含む既存の VM のスナップショットです。 [スケール セットを構成する](./ansible-create-configure-vmss.md)ときに、そのスケール セットの VM に使用するイメージを指定します。 カスタム イメージを使用することにより、各 VM インスタンスはご使用のアプリに対して同じように構成されます。 場合によっては、スケール セットのカスタム イメージの更新が必要になることがあります。 そのタスクがこのチュートリアルの焦点です。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * HTTPD を使用して 2 つの VM を構成する
> * 既存の VM からカスタム イメージを作成する
> * イメージからスケール セットを作成する
> * カスタム イメージを更新する

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-two-vms"></a>2 つの VM を構成する

このセクションのプレイブック コードは、HTTPD が両方にインストールされた 2 つの仮想マシンを作成します。 

各 VM の `index.html` ページには、次のようにテスト文字列が表示されます。

* 最初の VM には値 `Image A` が表示される
* 2 番目の VM には値 `Image B` が表示される

この文字列は、異なるソフトウェアを使用した各 VM の構成を模倣するように意図されています。

サンプル プレイブックを取得するには、次の 2 つの方法があります。

* [プレイブックをダウンロード](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/01-create-vms.yml)して `create_vms.yml` に保存する。
* `create_vms.yml` という名前の新規ファイルを作成して、次のコンテンツをコピーする。

```yml
- name: Create two VMs (A and B) with HTTPS
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
    admin_username: testuser
    admin_password: Pass123$$$abx!
    location: eastus
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      address_prefixes: "10.0.0.0/16"

  - name: Create subnets for VM A and B
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      virtual_network: "{{ vm_name }}"
      name: "{{ vm_name }}"
      address_prefix: "10.0.1.0/24"

  - name: Create Network Security Group that allows HTTP
    azure_rm_securitygroup:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      rules:
        - name: HTTP
          protocol: Tcp
          destination_port_range: 80
          access: Allow
          priority: 1002
          direction: Inbound

  - name: Create public IP addresses for VM A and B
    azure_rm_publicipaddress:
      resource_group: "{{ resource_group }}"
      allocation_method: Static
      name: "{{ vm_name }}_{{ item }}"
    loop:
      - A
      - B
    register: pip_output

  - name: Create virtual network inteface cards for VM A and B
    azure_rm_networkinterface:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}_{{ item }}"
      virtual_network: "{{ vm_name }}"
      subnet: "{{ vm_name }}"
      public_ip_name: "{{ vm_name }}_{{ item }}"
      security_group: "{{ vm_name }}"
    loop:
      - A
      - B

  - name: Create VM A and B
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      vm_size: Standard_B1ms
      network_interfaces: "{{ vm_name }}_{{ item }}"
      image:
        offer: UbuntuServer
        publisher: Canonical
        sku: 16.04-LTS
        version: latest
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "sudo apt-get -y install apache2"}
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "printf '<html><body><h1>Image {{ item }}</h1></body></html>' >> index.html; sudo cp index.html /var/www/html/"}
    loop:
      - A
      - B

  - debug:
      msg: "Public IP Address A: {{ pip_output.results[0].state.ip_address }}"

  - debug:
      msg: "Public IP Address B: {{ pip_output.results[1].state.ip_address }}"
```

`myrg` を自分のリソース グループ名に置き換えて、`ansible-playbook` を使用してプレイブックを実行します。

```bash
ansible-playbook create-vms.yml --extra-vars "resource_group=myrg"
```

プレイブックの `debug` セクションのため、`ansible-playbook` コマンドは各 VM の IP アドレスを表示します。 これらの IP アドレスを後で使用するためにコピーします。

![仮想マシンの IP アドレス](media/ansible-vmss-update-image/vmss-update-vms-ip-addresses.png)

## <a name="connect-to-the-two-vms"></a>2 つの VM に接続する

このセクションでは、各 VM に接続します。 前のセクションで述べたように、文字列 `Image A` と `Image B` は、異なる構成を持つ 2 つの個別の VM を持つことを模倣します。

次のように、前のセクションの IP アドレスを使用して両方の VM に接続します。

![仮想マシン A のスクリーンショット](media/ansible-vmss-update-image/vmss-update-browser-vma.png)

![仮想マシン B のスクリーンショット](media/ansible-vmss-update-image/vmss-update-browser-vmb.png)

## <a name="create-images-from-each-vm"></a>各 VM からイメージを作成する

この時点で、若干異なる構成 (それらの `index.html` ファイル) を持つ 2 つの VM があります。

このセクションのプレイブック コードは、次のような各 VM のカスタム イメージを作成します。

* `image_vmforimageA` - ホーム ページに `Image A` を表示する VM 用に作成されたカスタム イメージ。
* `image_vmforimageB` - ホーム ページに `Image B` を表示する VM 用に作成されたカスタム イメージ。

サンプル プレイブックを取得するには、次の 2 つの方法があります。

* [プレイブックをダウンロード](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/02-capture-images.yml)して `capture-images.yml` に保存する。
* `capture-images.yml` という名前の新規ファイルを作成して、次のコンテンツをコピーする。

```yml
- name: Capture VM Images
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
  tasks:

  - name: Stop and generalize VMs
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      generalized: yes
    loop:
      - A
      - B

  - name: Create an images from a VMs
    azure_rm_image:
      resource_group: "{{ resource_group }}"
      name: "image_{{ vm_name }}{{ item }}"
      source: "{{ vm_name }}{{ item }}"
    loop:
      - A
      - B
```

`myrg` を自分のリソース グループ名に置き換えて、`ansible-playbook` を使用してプレイブックを実行します。

```bash
ansible-playbook capture-images.yml --extra-vars "resource_group=myrg"
```

## <a name="create-scale-set-using-image-a"></a>イメージ A を使用してスケール セットを作成する

このセクションでは、プレイブックを使用して次の Azure リソースを構成します。

* パブリック IP アドレス
* Load Balancer
* `image_vmforimageA` を参照するスケール セット

サンプル プレイブックを取得するには、次の 2 つの方法があります。

* [プレイブックをダウンロード](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/03-create-vmss.yml)して、`create-vmss.yml` に保存する。
* `create-vmss.yml` という名前の新規ファイルを作成して、次のコンテンツをコピーする。

```yml
---
- hosts: localhost
  vars:
    vmss_name: vmsstest
    location: eastus
    admin_username: vmssadmin
    admin_password: User123!!!abc
    vm_name: vmforimage
    image_name: "image_vmforimageA"

  tasks:

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
      register: pip_output

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_name }}lb"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 80
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 80
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create a scale set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vm_name }}"
        subnet_name: "{{ vm_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          name: "{{ image_name }}"
          resource_group: "{{ resource_group }}"
        load_balancer: "{{ vmss_name }}lb"

    - debug:
        msg: "Scale set public IP address: {{ pip_output.state.ip_address }}"
```

`myrg` を自分のリソース グループ名に置き換えて、`ansible-playbook` を使用してプレイブックを実行します。

```bash
ansible-playbook create-vmss.yml --extra-vars "resource_group=myrg"
```

プレイブックの `debug` セクションのため、`ansible-playbook` コマンドはスケール セットの IP アドレスを表示します。 この IP アドレスを後で使用するためにコピーします。

![パブリック IP アドレス](media/ansible-vmss-update-image/vmss-update-vmss-public-ip.png)

## <a name="connect-to-the-scale-set"></a>スケール セットに接続する

このセクションでは、スケール セットに接続します。 

前のセクションの IP アドレスを使用してスケール セットに接続します。

前のセクションで述べたように、文字列 `Image A` と `Image B` は、異なる構成を持つ 2 つの個別の VM を持つことを模倣します。

スケール セットは、`image_vmforimageA` という名前のカスタム イメージを参照します。 カスタム イメージ `image_vmforimageA` は、ホーム ページに `Image A` が表示される VM から作成されました。

その結果として、`Image A` を表示するホーム ページが表示されます。

![スケール セットは、最初の VM に関連付けられます。](media/ansible-vmss-update-image/vmss-update-browser-initial-vmss.png)

次のセクションに進むので、ブラウザー ウィンドウは開いたままにしておきます。

## <a name="change-custom-image-in-scale-set-and-upgrade-instances"></a>スケール セットのカスタム イメージを変更して、インスタンスをアップグレードする

このセクションのプレイブック コードは、スケール セットのイメージを `image_vmforimageA` から `image_vmforimageB` に変更します。 また、このスケール セットによってデプロイされている現在のすべての仮想マシンが更新されます。

サンプル プレイブックを取得するには、次の 2 つの方法があります。

* [プレイブックをダウンロード](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/04-update-vmss-image.yml)して `update-vmss-image.yml` に保存する。
* `update-vmss-image.yml` という名前の新規ファイルを作成して、次のコンテンツをコピーする。

```yml
- name: Update scale set image reference
  hosts: localhost
  connection: local
  vars:
    vmss_name: vmsstest
    image_name: image_vmforimageB
    admin_username: vmssadmin
    admin_password: User123!!!abc
  tasks:

  - name: Update scale set - second image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      ssh_password_enabled: true
      capacity: 3
      virtual_network_name: "{{ vmss_name }}"
      subnet_name: "{{ vmss_name }}"
      upgrade_policy: Manual
      tier: Standard
      managed_disk_type: Standard_LRS
      os_disk_caching: ReadWrite
      image:
        name: "{{ image_name }}"
        resource_group: "{{ resource_group }}"
      load_balancer: "{{ vmss_name }}lb"

  - name: List all of the instances
    azure_rm_virtualmachinescalesetinstance_facts:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
    register: instances

  - debug:
      var: instances

  - name: manually upgrade all the instances 
    azure_rm_virtualmachinescalesetinstance:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
      instance_id: "{{ item.instance_id }}"
      latest_model: yes
    with_items: "{{ instances.instances }}"
```

`myrg` を自分のリソース グループ名に置き換えて、`ansible-playbook` を使用してプレイブックを実行します。

```bash
ansible-playbook update-vmss-image.yml --extra-vars "resource_group=myrg"
```

ブラウザーに戻り、ページを更新します。 

仮想マシンの基になるカスタム イメージが更新されているのを確認できます。

![スケール セットは、2 番目の VM に関連付けられています](media/ansible-vmss-update-image/vmss-update-browser-updated-vmss.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事で作成したリソースが不要になったら、削除してください。 

`cleanup.yml` として次のコードを保存します。

```yml
- hosts: localhost
  vars:
    resource_group: myrg
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
> [Azure 上の Ansible](/azure/ansible)