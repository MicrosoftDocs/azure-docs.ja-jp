---
title: クイックスタート - Ansible を使用して Azure で Linux 仮想マシンを管理する | Microsoft Docs
description: このクイック スタートでは、Ansible を使用して Azure で Linux 仮想マシンを管理する方法について説明します
keywords: Ansible, Azure, DevOps, Bash, Cloud Shell, プレイブック, Bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: c4878902425a26086ad77647ea06568f2110ccfe
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668634"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>クイック スタート:Ansible を使用して Azure で Linux 仮想マシンを管理する

Ansible を使用すると、環境でのリソースの展開と構成を自動化することができます。 この記事では、Ansible プレイブックを使って Linux 仮想マシンの起動と停止を行います。 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>仮想マシンの停止

このセクションでは、Ansible を使って Azure 仮想マシンの割り当て解除 (停止) を行います。

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。

1. [Cloud Shell](/azure/cloud-shell/overview) を開きます。

1. `azure-vm-stop.yml` という名前のファイルを作成し、エディターで開きます。

    ```azurecli-interactive
    code azure-vm-stop.yml
    ```

1. 以下のサンプル コードをエディターに貼り付けます。

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Stop virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
            allocated: no
    ```

1. `{{ resource_group_name }}` および `{{ vm_name }}` プレースホルダーを、実際の値に置き換えます。

1. ファイルを保存し、エディターを終了します。

1. `ansible-playbook` コマンドを使用してプレイブックを実行します。

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. プレイブックを実行すると、次の結果のような出力が表示されます。

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="start-a-virtual-machine"></a>仮想マシンの起動

このセクションでは、割り当て解除 (停止) された Azure 仮想マシンを、Ansible を使って起動します。

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。

1. [Cloud Shell](/azure/cloud-shell/overview) を開きます。

1. `azure-vm-start.yml` という名前のファイルを作成し、エディターで開きます。

    ```azurecli-interactive
    code azure-vm-start.yml
    ```

1. 以下のサンプル コードをエディターに貼り付けます。

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Start virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
    ```

1. `{{ resource_group_name }}` および `{{ vm_name }}` プレースホルダーを、実際の値に置き換えます。

1. ファイルを保存し、エディターを終了します。

1. `ansible-playbook` コマンドを使用してプレイブックを実行します。

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. プレイブックを実行すると、次の結果のような出力が表示されます。

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [チュートリアル:Ansible を使用して Azure の動的インベントリを管理する](~/articles/ansible/ansible-manage-azure-dynamic-inventories.md)