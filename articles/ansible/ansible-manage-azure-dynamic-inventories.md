---
title: Ansible を使用した Azure の動的インベントリの管理
description: Ansible を使用して Azure の動的インベントリを管理する方法について説明します
ms.service: azure
keywords: Ansible, Azure, DevOps, Bash, Cloud Shell, 動的インベントリ
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 08/09/2018
ms.topic: tutorial
ms.openlocfilehash: 0ef754b792654281f2a12b8eee613434896d5476
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58092210"
---
# <a name="use-ansible-to-manage-your-azure-dynamic-inventories"></a>Ansible を使用した Azure の動的インベントリの管理
Ansible を使用して、(Azure などのクラウド ソースを含む) さまざまなソースから "*動的インベントリ*" にインベントリ情報をプルすることができます。 この記事では、[Azure Cloud Shell](./ansible-run-playbook-in-cloudshell.md) を使用して、Ansible Azure 動的インベントリを構成します。この動的インベントリに 2 つの仮想マシンを作成した後、そのうちの 1 つの仮想マシンにタグを付け、タグを付けた仮想マシンに Nginx をインストールします。

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション** - Azure サブスクリプションをお持ちでない場合は、開始する前に[無料のアカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。

- **Azure 資格情報** - [Azure 資格情報の作成と Ansible の構成](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="create-the-test-virtual-machines"></a>テスト用仮想マシンの作成

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。

1. [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) を開きます。

1. このチュートリアル用の仮想マシンを保持する Azure リソース グループを作成します。

    > [!IMPORTANT]  
    > この手順で作成する Azure リソース グループには、名前をすべて小文字で指定する必要があります。 それ以外の場合、動的インベントリの生成は失敗します。

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. 次の手法のいずれかを使用して、Azure に 2 つの Linux 仮想マシンを作成します。

    - **Ansible プレイブック** - Ansible プレイブックから仮想マシンを作成する方法については、「[Ansible を使用して Azure で基本的な仮想マシンを作成する](/azure/virtual-machines/linux/ansible-create-vm)」を参照してください。 プレイブックを使用して仮想マシンの一方または両方を定義する場合は、パスワードの代わりに SSH 接続を必ず使用してください。

    - **Azure CLI** - Cloud Shell で次の各コマンドを発行して、2 つの仮想マシンを作成します。

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-virtual-machine"></a>仮想マシンへのタグ付け
[タグを使用して、Azure リソースをユーザー定義のカテゴリ別に整理](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli)できます。 

次の [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) コマンドを入力して、仮想マシン `ansible-inventory-test-vm1` にキー `nginx` を使用してタグを付けます。

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>動的インベントリの生成
仮想マシンを定義 (およびタグ付け) した後は、動的インベントリを生成します。 Ansible には、Azure Resource Manager に対して API 要求を行って Azure リソースの動的インベントリを生成する、[azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) という名前の Python スクリプトが用意されています。 `azure_rm.py` スクリプトを使用して 2 つのテスト用 Azure 仮想マシンに接続する手順を次に示します。

1. GNU `wget` コマンドを使用して、`azure_rm.py` スクリプトを取得します。

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. `chmod` コマンドを使用して、`azure_rm.py` スクリプトへのアクセス許可を変更します。 次のコマンドでは、`+x` パラメーターを使用して、指定されたファイル (`azure_rm.py`) の実行を許可しています。

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. [ansible コマンド](https://docs.ansible.com/ansible/2.4/ansible.html)を使用して、リソース グループに接続します。 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. 接続されると、次の出力のような結果が表示されます。

    ```Output
    ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ansible-inventory-test-vm2 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

## <a name="enable-the-virtual-machine-tag"></a>仮想マシンのタグの有効化
目的のタグを設定した後は、タグを "有効" にする必要があります。 タグを有効にする 1 つの方法に、**export** コマンドを使用して `AZURE_TAGS` という名前の環境変数にタグをエクスポートする方法があります。

```azurecli-interactive
export AZURE_TAGS=nginx
```

タグをエクスポートしたら、もう一度 `ansible` コマンドを試します。

```azurecli-interactive
ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
```

今回は、仮想マシンが 1 つしか表示されません (タグが **AZURE_TAGS** 環境変数にエクスポートされた値と一致する仮想マシン)。

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>タグ付けされた VM での Nginx の設定
タグの目的は、仮想マシンのサブグループをすばやく簡単に操作できるようにすることです。 たとえば、`nginx` というタグを割り当てた仮想マシンにのみ Nginx をインストールするとします。 これは、次の手順に示すように簡単に行うことができます。

1. 次のように、(プレイブックを保存するための) `nginx.yml` という名前のファイルを作成します。

   ```azurecli-interactive
   vi nginx.yml
   ```

1. 新しく作成した `nginx.yml` ファイルに次のコードを挿入します。

    ```yml
    ---
    - name: Install and start Nginx on an Azure virtual machine
    hosts: azure
    become: yes
    tasks:
    - name: install nginx
      apt: pkg=nginx state=installed
      notify:
      - start nginx

    handlers:
    - name: start nginx
      service: name=nginx state=started
    ```

1. `nginx.yml` プレイブックを実行します。

    ```azurecli-interactive
    ansible-playbook -i azure_rm.py nginx.yml
    ```

1. プレイブックを実行すると、次の出力のような結果が表示されます。

    ```Output
    PLAY [Install and start Nginx on an Azure virtual machine] **********

    TASK [Gathering Facts] **********
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] **********
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] **********
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP **********
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>Nginx のインストールのテスト
このセクションでは、Nginx が仮想マシンにインストールされているかどうかをテストする 1 つの方法を示します。

1. [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) コマンドを使用して、`ansible-inventory-test-vm1` 仮想マシンの IP アドレスを取得します。 次に、返された値 (仮想マシンの IP アドレス) を、仮想マシンに接続するための SSH コマンドのパラメーターとして使用します。

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. `ansible-inventory-test-vm1` の仮想マシンに接続されている間に、[nginx -v](https://nginx.org/en/docs/switches.html) コマンドを実行して Nginx がインストールされていることを判定します。

    ```azurecli-interactive
    nginx -v
    ```

1. `nginx -v` コマンドを実行すると、Nginx のバージョン (2 行目) が表示され、Nginx がインストールされていることがわかります。

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)
    
    tom@ansible-inventory-test-vm1:~$
    ```

1. **Ctrl + D** キーの組み合わせを押して、SSH セッションを切断します。

1. `ansible-inventory-test-vm2` 仮想マシンに対して上記の手順を実行すると、Nginx を入手できる場所を示す情報メッセージが表示されます (これは、この時点で Nginx がインストールされていないことを暗に意味しています)。

    ```Output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [Ansible を使用して Azure で基本的な仮想マシンを作成する](/azure/virtual-machines/linux/ansible-create-vm)
