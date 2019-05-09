---
title: Ansible を使用して Azure 内の Linux 仮想マシンを管理する
description: Ansible を使用して Azure 内の Linux 仮想マシンを管理する方法について説明します。
ms.service: virtual-machines-linux
keywords: Ansible, Azure, DevOps, Bash, Cloud Shell, プレイブック, Bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 09/27/2018
ms.openlocfilehash: 8f97cf8a4231e9a2144f27c0540de96574e13795
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57789879"
---
# <a name="use-ansible-to-manage-a-linux-virtual-machine-in-azure"></a>Ansible を使用して Azure 内の Linux 仮想マシンを管理する
Ansible を使用すると、環境でのリソースの展開と構成を自動化することができます。 Azure 仮想マシンも、他のリソースと同様、Ansible を使って管理することができます。 この記事では、Ansible プレイブックを使って Linux 仮想マシンを起動したり停止させたりする方法について説明します。 

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション** - Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

## <a name="use-ansible-to-deallocate-stop-an-azure-virtual-machine"></a>Ansible を使用して Azure 仮想マシンの割り当てを解除 (停止) する
このセクションでは、Ansible を使用して Azure 仮想マシンの割り当てを解除 (停止) する方法について説明します。

1.  [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。

1.  [Cloud Shell](/azure/cloud-shell/overview) を開きます。

1.  次のように、(プレイブックを保存するための) `azure-vm-stop.yml` という名前のファイルを作成し、vi エディターで開きます。

    ```azurecli-interactive
    vi azure-vm-stop.yml
    ```

1.  **I** キーを選択して挿入モードに入ります。

1.  以下のサンプル コードをエディターに貼り付けます。

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
      - name: Deallocate the virtual machine
        azure_rm_virtualmachine:
          resource_group: myResourceGroup
          name: myVM
          allocated: no
    ```

1.  **Esc** キーを押して、挿入モードを終了します。

1.  ファイルを保存し、次のコマンドを入力して vi エディターを終了します。

    ```bash
    :wq
    ```

1.  サンプル Ansible プレイブックを実行します。

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1.  出力は、次の例のようになり、仮想マシンが正常に割り当て解除 (停止) されたことが示されます。

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="use-ansible-to-start-a-deallocated-stopped-azure-virtual-machine"></a>割り当て解除 (停止) された Azure 仮想マシンを Ansible を使用して起動する
このセクションでは、割り当て解除 (停止) された Azure 仮想マシンを Ansible を使用して起動する方法について説明します。

1.  [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。

1.  [Cloud Shell](/azure/cloud-shell/overview) を開きます。

1.  次のように、(プレイブックを保存するための) `azure-vm-start.yml` という名前のファイルを作成し、vi エディターで開きます。

    ```azurecli-interactive
    vi azure-vm-start.yml
    ```

1.  **I** キーを選択して挿入モードに入ります。

1.  以下のサンプル コードをエディターに貼り付けます。

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
      - name: Start the virtual machine
        azure_rm_virtualmachine:
          resource_group: myResourceGroup
          name: myVM
    ```

1.  **Esc** キーを押して、挿入モードを終了します。

1.  ファイルを保存し、次のコマンドを入力して vi エディターを終了します。

    ```bash
    :wq
    ```

1.  サンプル Ansible プレイブックを実行します。

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1.  出力は、次の例のようになり、仮想マシンが正常に起動されたことが示されます。

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
> [Ansible を使用した Azure の動的インベントリの管理](~/articles/ansible/ansible-manage-azure-dynamic-inventories.md)