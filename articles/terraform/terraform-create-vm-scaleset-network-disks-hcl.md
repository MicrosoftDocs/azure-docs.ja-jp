---
title: Terraform を使用して Azure 仮想マシン スケール セットを作成する
description: 仮想ネットワークとアタッチされたマネージド ディスクを含む Azure 仮想マシン スケール セットの構成とバージョン管理を行うための Terraform の使用に関するチュートリアル
services: terraform
ms.service: azure
keywords: terraform, devops, 仮想マシン, Azure, スケール セット, ネットワーク, ストレージ, モジュール
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2018
ms.openlocfilehash: 21fea65ed7056afa57d9acbacb2457bb4d09cff5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58002302"
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set"></a>Terraform を使用して Azure 仮想マシン スケール セットを作成する

[Azure 仮想マシン スケール セット](/azure/virtual-machine-scale-sets)では、負荷分散された同一の仮想マシンのグループを作成および管理できます。この場合、需要や定義したスケジュールに応じて仮想マシン インスタンスの数を自動的に増減できます。

このチュートリアルでは、[Azure Cloud Shell](/azure/cloud-shell/overview) を使用して次のタスクを実行する方法について説明します。

> [!div class="checklist"]
> * Terraform デプロイを設定する
> * Terraform デプロイ用の変数と出力を使用する
> * ネットワーク インフラストラクチャを作成してデプロイする
> * 仮想マシン スケール セットを作成してデプロイし、ネットワークにアタッチする
> * SSH 経由で VM に接続するジャンプボックスを作成してデプロイする

> [!NOTE]
> この記事で使用される Terraform 構成ファイルの最新バージョンは、[GitHub の Awesome Terraform リポジトリ](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss)にあります。

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。

- **Terraform のインストール**:[Terraform および Azure へのアクセスの構成](/azure/virtual-machines/linux/terraform-install-configure)に関する記事の指示に従ってください

- **SSH キー ペアの作成**:SSH キー ペアをまだ持っていない場合は、[Azure 内に Linux VM 用の SSH 公開/秘密キーの組を作成して使用する方法](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)に関する記事の手順に従います。

## <a name="create-the-directory-structure"></a>ディレクトリ構造を作成する

1. [Azure ポータル](https://portal.azure.com)にアクセスします。

1. [Azure Cloud Shell](/azure/cloud-shell/overview) を開きます。 前に環境を選択しなかった場合、環境として **Bash** を選択します。

    ![Cloud Shell のプロンプト](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. ディレクトリを `clouddrive` ディレクトリに変更します。

    ```bash
    cd clouddrive
    ```

1. `vmss` という名前のディレクトリを作成します。

    ```bash
    mkdir vmss
    ```

1. 新しいディレクトリに移動します。

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>変数定義ファイルを作成する
このセクションでは、Terraform によって作成されたリソースをカスタマイズする変数を定義します。

Azure Cloud Shell 内で、次の手順を実行します。

1. `variables.tf` という名前でファイルを作成します。

    ```bash
    vi variables.tf
    ```

1. I キーを選択し、挿入モードに入ります。

1. 以下のコードをエディターに貼り付けます。

   ```JSON
   variable "location" {
    description = "The location where resources will be created"
   }

   variable "tags" {
    description = "A map of the tags to use for the resources that are deployed"
    type        = "map"

    default = {
      environment = "codelab"
    }
   }

   variable "resource_group_name" {
    description = "The name of the resource group in which the resources will be created"
    default     = "myResourceGroup"
   }
   ```

1. Esc キーを押して、挿入モードを終了します。

1. ファイルを保存し、次のコマンドを入力して vi エディターを終了します。

    ```bash
    :wq
    ```

## <a name="create-the-output-definitions-file"></a>出力定義ファイルを作成する
このセクションでは、デプロイ後の出力を記述するファイルを作成します。

Azure Cloud Shell 内で、次の手順を実行します。

1. `output.tf` という名前でファイルを作成します。

    ```bash
    vi output.tf
    ```

1. I キーを選択し、挿入モードに入ります。

1. 次のコードをエディターに貼り付けて、仮想マシンの完全修飾ドメイン名 (FQDN) が表示されるようにします。
   :

   ```JSON
    output "vmss_public_ip" {
        value = "${azurerm_public_ip.vmss.fqdn}"
    }
   ```

1. Esc キーを押して、挿入モードを終了します。

1. ファイルを保存し、次のコマンドを入力して vi エディターを終了します。

    ```bash
    :wq
    ```

## <a name="define-the-network-infrastructure-in-a-template"></a>テンプレートでネットワーク インフラストラクチャを定義する
このセクションでは、新しい Azure リソース グループに次のネットワーク インフラストラクチャを作成します。

  - 10.0.0.0/16 のアドレス空間を使用する 1 つの 仮想ネットワーク (VNET)
  - 10.0.2.0/24 のアドレス空間を使用する 1 つのサブネット
  - 2 つのパブリック IP アドレス。 1 つは仮想マシン スケール セット ロード バランサーに使用され、もう 1 つは SSH ジャンプボックスへの接続に使用されます。

Azure Cloud Shell 内で、次の手順を実行します。

1. 仮想マシン スケール セット インフラストラクチャを記述する `vmss.tf` という名前のファイルを作成します。

    ```bash
    vi vmss.tf
    ```

1. I キーを選択し、挿入モードに入ります。

1. 次のコードをファイルの末尾に貼り付けて、仮想マシンの完全修飾ドメイン名 (FQDN) が表示されるようにします。

   ```JSON
   resource "azurerm_resource_group" "vmss" {
    name     = "${var.resource_group_name}"
    location = "${var.location}"
    tags     = "${var.tags}"
   }

   resource "random_string" "fqdn" {
    length  = 6
    special = false
    upper   = false
    number  = false
   }

   resource "azurerm_virtual_network" "vmss" {
    name                = "vmss-vnet"
    address_space       = ["10.0.0.0/16"]
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"
    tags                = "${var.tags}"
   }

   resource "azurerm_subnet" "vmss" {
    name                 = "vmss-subnet"
    resource_group_name  = "${azurerm_resource_group.vmss.name}"
    virtual_network_name = "${azurerm_virtual_network.vmss.name}"
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "vmss" {
    name                         = "vmss-public-ip"
    location                     = "${var.location}"
    resource_group_name          = "${azurerm_resource_group.vmss.name}"
    allocation_method = "Static"
    domain_name_label            = "${random_string.fqdn.result}"
    tags                         = "${var.tags}"
   }
   ```

1. Esc キーを押して、挿入モードを終了します。

1. ファイルを保存し、次のコマンドを入力して vi エディターを終了します。

   ```bash
   :wq
   ```

## <a name="provision-the-network-infrastructure"></a>ネットワーク インフラストラクチャをプロビジョニングする
構成ファイル (.tf) を作成したディレクトリから Azure Cloud Shell を使用して、次の手順を実行します。

1. Terraform を初期化します。

   ```bash
   terraform init
   ```

1. 次のコマンドを実行して、Azure に定義済みインフラストラクチャをデプロイします。

   ```bash
   terraform apply
   ```

   **location** 変数は `variables.tf` で定義されていますが設定されていないため、Terraform により "location" の値を求められます。 "米国西部" などの任意の有効な場所を入力でき、続いて Enter キーを押します  (スペースを含む値はかっこで囲みます)。

1. Terraform は、`output.tf` ファイルでの定義に従って出力します。 次のスクリーンショットに示すように、FQDN の形式は &lt;id>.&lt;location>.cloudapp.azure.com のようになります。 id 値は計算された値で、location は Terraform の実行時に指定した値です。

   ![仮想マシン スケール セットのパブリック IP アドレスの完全修飾ドメイン名](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. Azure portal のメニューで、メイン メニューから **[リソース グループ]** を選択します。

1. **[リソース グループ]** タブで **[myResourceGroup]** を選択して、Terraform によって作成されたリソースを表示します。
   ![仮想マシン スケール セットのネットワーク リソース](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>仮想マシン スケール セットを追加する

このセクションでは、次のリソースをテンプレートに追加する方法について説明します。

- Azure Load Balancer およびアプリケーションにサービスを提供するルール (この記事で前に構成したパブリック IP アドレスに接続します)
- Azure バックエンド アドレス プール (ロード バランサーに割り当てます)
- アプリケーションで使用する正常性プローブ ポート (ロード バランサー上に構成します)
- ロード バランサーの背後に構成される仮想マシン スケール セット (この記事で前にデプロイした VNET 上で実行されます)
- [cloud-init](https://cloudinit.readthedocs.io/en/latest/) を使用する、仮想マシン スケールのノード上の [Nginx](https://nginx.org/)。

Cloud Shell で、次の手順を実行します。

1. `vmss.tf` 構成ファイルを開きます。

   ```bash
   vi vmss.tf
   ```

1. ファイルの末尾に移動し、A キーを選択して追加モードに入ります。

1. ファイルの末尾に次のコードを貼り付けます。

   ```JSON
   resource "azurerm_lb" "vmss" {
    name                = "vmss-lb"
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"

    frontend_ip_configuration {
      name                 = "PublicIPAddress"
      public_ip_address_id = "${azurerm_public_ip.vmss.id}"
    }

    tags = "${var.tags}"
   }

   resource "azurerm_lb_backend_address_pool" "bpepool" {
    resource_group_name = "${azurerm_resource_group.vmss.name}"
    loadbalancer_id     = "${azurerm_lb.vmss.id}"
    name                = "BackEndAddressPool"
   }

   resource "azurerm_lb_probe" "vmss" {
    resource_group_name = "${azurerm_resource_group.vmss.name}"
    loadbalancer_id     = "${azurerm_lb.vmss.id}"
    name                = "ssh-running-probe"
    port                = "${var.application_port}"
   }

   resource "azurerm_lb_rule" "lbnatrule" {
      resource_group_name            = "${azurerm_resource_group.vmss.name}"
      loadbalancer_id                = "${azurerm_lb.vmss.id}"
      name                           = "http"
      protocol                       = "Tcp"
      frontend_port                  = "${var.application_port}"
      backend_port                   = "${var.application_port}"
      backend_address_pool_id        = "${azurerm_lb_backend_address_pool.bpepool.id}"
      frontend_ip_configuration_name = "PublicIPAddress"
      probe_id                       = "${azurerm_lb_probe.vmss.id}"
   }

   resource "azurerm_virtual_machine_scale_set" "vmss" {
    name                = "vmscaleset"
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"
    upgrade_policy_mode = "Manual"

    sku {
      name     = "Standard_DS1_v2"
      tier     = "Standard"
      capacity = 2
    }

    storage_profile_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_profile_os_disk {
      name              = ""
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    storage_profile_data_disk {
      lun          = 0
      caching        = "ReadWrite"
      create_option  = "Empty"
      disk_size_gb   = 10
    }

    os_profile {
      computer_name_prefix = "vmlab"
      admin_username       = "${var.admin_user}"
      admin_password       = "${var.admin_password}"
      custom_data          = "${file("web.conf")}"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    network_profile {
      name    = "terraformnetworkprofile"
      primary = true

      ip_configuration {
        name                                   = "IPConfiguration"
        subnet_id                              = "${azurerm_subnet.vmss.id}"
        load_balancer_backend_address_pool_ids = ["${azurerm_lb_backend_address_pool.bpepool.id}"]
        primary = true
      }
    }

    tags = "${var.tags}"
   }
   ```

1. Esc キーを押して、挿入モードを終了します。

1. ファイルを保存し、次のコマンドを入力して vi エディターを終了します。

    ```bash
    :wq
    ```

1. スケール セットの一部である仮想マシンの cloud-init 構成として機能する `web.conf` という名前のファイルを作成します。

    ```bash
    vi web.conf
    ```

1. I キーを選択し、挿入モードに入ります。

1. 以下のコードをエディターに貼り付けます。

   ```JSON
   #cloud-config
   packages:
    - nginx
   ```

1. Esc キーを押して、挿入モードを終了します。

1. ファイルを保存し、次のコマンドを入力して vi エディターを終了します。

     ```bash
     :wq
     ```

1. `variables.tf` 構成ファイルを開きます。

    ```bash
    vi variables.tf
    ```

1. ファイルの末尾に移動し、A キーを選択して追加モードに入ります。

1. 次のコードをファイルの末尾に貼り付けてデプロイをカスタマイズします。

    ```JSON
    variable "application_port" {
       description = "The port that you want to expose to the external load balancer"
       default     = 80
    }

    variable "admin_user" {
       description = "User name to use as the admin account on the VMs that will be part of the VM Scale Set"
       default     = "azureuser"
    }

    variable "admin_password" {
       description = "Default password for admin account"
    }
    ```

1. Esc キーを押して、挿入モードを終了します。

1. ファイルを保存し、次のコマンドを入力して vi エディターを終了します。

     ```bash
     :wq
     ```

1. Terraform プランを作成して、仮想マシン スケール セットのデプロイを視覚化します  (選択したパスワードと、リソースの場所を指定する必要があります)。

    ```bash
    terraform plan
    ```

    このコマンドの出力は次のスクリーンショットのようになります。

    ![仮想マシン スケール セットの作成からの出力](./media/terraform-create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Azure で新しいリソースをデプロイします。

    ```bash
    terraform apply
    ```

    このコマンドの出力は次のスクリーンショットのようになります。

    ![Terraform の仮想マシン スケール セットのリソース グループ](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. ブラウザーを開き、コマンドから返された FQDN に接続します。

    ![FQDN の参照の結果](./media/terraform-create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>SSH ジャンプボックスを追加する
SSH "*ジャンプボックス*" は、ネットワーク上の他のサーバーにアクセスするために "ジャンプ" する単一のサーバーです。 この手順では、次のリソースを構成します。

- 仮想マシン スケール セットと同じサブネットに接続されているネットワーク インターフェイス (またはジャンプボックス)。

- このネットワーク インターフェイスと接続されている仮想マシン。 この "ジャンプボックス" にはリモートからアクセスできます。 接続すると、スケール セット内の任意の仮想マシンに対して SSH を実行できるようになります。

1. `vmss.tf` 構成ファイルを開きます。

   ```bash
   vi vmss.tf
   ```

1. ファイルの末尾に移動し、A キーを選択して追加モードに入ります。

1. ファイルの末尾に次のコードを貼り付けます。

   ```JSON
   resource "azurerm_public_ip" "jumpbox" {
    name                         = "jumpbox-public-ip"
    location                     = "${var.location}"
    resource_group_name          = "${azurerm_resource_group.vmss.name}"
    allocation_method = "Static"
    domain_name_label            = "${random_string.fqdn.result}-ssh"
    tags                         = "${var.tags}"
   }

   resource "azurerm_network_interface" "jumpbox" {
    name                = "jumpbox-nic"
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"

    ip_configuration {
      name                          = "IPConfiguration"
      subnet_id                     = "${azurerm_subnet.vmss.id}"
      private_ip_address_allocation = "dynamic"
      public_ip_address_id          = "${azurerm_public_ip.jumpbox.id}"
    }

    tags = "${var.tags}"
   }

   resource "azurerm_virtual_machine" "jumpbox" {
    name                  = "jumpbox"
    location              = "${var.location}"
    resource_group_name   = "${azurerm_resource_group.vmss.name}"
    network_interface_ids = ["${azurerm_network_interface.jumpbox.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "jumpbox-osdisk"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    os_profile {
      computer_name  = "jumpbox"
      admin_username = "${var.admin_user}"
      admin_password = "${var.admin_password}"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = "${var.tags}"
   }
   ```

1. `output.tf` 構成ファイルを開きます。

   ```bash
   vi output.tf
   ```

1. ファイルの末尾に移動し、A キーを選択して追加モードに入ります。

1. 次のコードをファイルの末尾に貼り付けて、デプロイの完了時にジャンプボックスのホスト名が表示されるようにします。

   ```
   output "jumpbox_public_ip" {
      value = "${azurerm_public_ip.jumpbox.fqdn}"
   }
   ```

1. Esc キーを押して、挿入モードを終了します。

1. ファイルを保存し、次のコマンドを入力して vi エディターを終了します。

    ```bash
    :wq
    ```

1. ジャンプボックスをデプロイします。

   ```bash
   terraform apply
   ```

デプロイが完了すると、リソース グループの内容は次のスクリーンショットのようになります。

![Terraform の仮想マシン スケール セットのリソース グループ](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> パスワードを使用してログインする機能は、デプロイしたジャンプボックスと仮想マシン スケール セットでは無効にされています。 仮想マシンにアクセスするには、SSH を使用してログインします。

## <a name="environment-cleanup"></a>環境のクリーンアップ

このチュートリアルで作成された Terraform リソースを削除するには、Cloud Shell に次のコマンドを入力します。

```bash
terraform destroy
```

この削除プロセスが完了するまでに数分かかることがあります。

## <a name="next-steps"></a>次の手順
この記事では、Terraform を使用して Azure 仮想マシン スケール セットを作成する方法について説明しました。 Azure 上の Terraform の詳細については、次のリソースもご覧ください。

[Microsoft.com での Terraform Hub](https://docs.microsoft.com/azure/terraform/)
[Terraform Azure プロバイダー ドキュメント](https://aka.ms/terraform)
[Terraform Azure プロバイダー ソース](https://aka.ms/tfgit)
[Terraform Azure モジュール](https://aka.ms/tfmodules)
