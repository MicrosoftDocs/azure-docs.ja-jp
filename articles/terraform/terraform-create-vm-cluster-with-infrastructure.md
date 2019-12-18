---
title: チュートリアル - Terraform と HCL を使用した Azure VM クラスターの作成
description: Azure で Terraform と HCL を使用してロード バランサ―を備えた Linux 仮想マシン クラスターを作成する
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: f28cbbf13015d07c9d789ed258a9e2b0582ba1da
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159268"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-and-hcl"></a>チュートリアル:Terraform と HCL を使用した Azure VM クラスターの作成

このチュートリアルでは、[HCL](https://www.terraform.io/docs/configuration/syntax.html) を使用して小規模なコンピューティング クラスターを作成する方法について説明します。 

次のタスクを実行する方法について説明します。

> [!div class="checklist"]
> * Azure 認証をセットアップします。
> * Terraform 構成ファイルを作成します。
> * Terraform 構成ファイルを使用して、ロード バランサーを作成します。
> * Terraform 構成ファイルを使用して、可用性セットに 2 つの Linux VM をデプロイします。
> * Terraform を初期化します。
> * Terraform 実行プランを作成します。
> * Terraform 実行プランを適用して、Azure リソースを作成します。

## <a name="1-set-up-azure-authentication"></a>1.Azure 認証をセットアップする

> [!NOTE]
> [Terraform 環境変数を使う](/azure/virtual-machines/linux/terraform-install-configure)場合、または [Azure Cloud Shell](terraform-cloud-shell.md) でこのチュートリアルを実行する場合は、このセクションを省略します。

このセクションでは、Azure のサービス プリンシパルと、セキュリティ プリンシパルからの資格情報を含む 2 つの Terraform 構成ファイルを生成します。

1. [Azure AD サービス プリンシパルをセットアップ](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure)して、Terraform で Azure にリソースをプロビジョニングできるようにします。 プリンシパルを作成するときに、サブスクリプション ID、テナント、appId、パスワードの値を書き留めておきます。

2. コマンド プロンプトを開きます。

3. Terraform ファイルを格納するための空のディレクトリを作成します。

4. 変数の宣言を保持する新しいファイルを作成します。 このファイルには、拡張子が `.tf` の任意の名前を付けることができます。

5. 変数宣言ファイルに次のコードをコピーします。

   ```hcl
   variable subscription_id {}
   variable tenant_id {}
   variable client_id {}
   variable client_secret {}
  
   provider "azurerm" {
      subscription_id = var.subscription_id
      tenant_id = var.tenant_id
      client_id = var.client_id
      client_secret = var.client_secret
   }
   ```

6. Terraform の変数の値を含む新しいファイルを作成します。 Terraform は現在のディレクトリにある `terraform.tfvars` という名前のファイル (または `*.auto.tfvars` というパターンのファイル) を自動的に読み込むので、Terraform 変数ファイルの名前は `terraform.tfvars` にするのが一般的です。 

7. 変数ファイルに次のコードをコピーします。 次のように、必ずプレースホルダーを置き換えます。`subscription_id` には、`az account set` の実行時に指定した Azure サブスクリプション ID を使用します。 `tenant_id` には、`az ad sp create-for-rbac` から返された `tenant` の値を使います。 `client_id` には、`az ad sp create-for-rbac` から返された `appId` の値を使います。 `client_secret` には、`az ad sp create-for-rbac` から返された `password` の値を使います。

   ```hcl
   subscription_id = "<azure-subscription-id>"
   tenant_id = "<tenant-returned-from-creating-a-service-principal>"
   client_id = "<appId-returned-from-creating-a-service-principal>"
   client_secret = "<password-returned-from-creating-a-service-principal>"
   ```

## <a name="2-create-a-terraform-configuration-file"></a>2.Terraform 構成ファイルを作成する

このセクションでは、インフラストラクチャのリソース定義を含むファイルを作成します。

1. `main.tf` という名前で新しいファイルを作成します。 

2. 次のサンプル リソース定義を、新しく作成した `main.tf` ファイルにコピーします。 

   ```hcl
   resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
   }

   resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name
   }

   resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = azurerm_resource_group.test.name
    virtual_network_name = azurerm_virtual_network.test.name
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    allocation_method            = "Static"
   }

   resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = azurerm_public_ip.test.id
    }
   }

   resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = azurerm_resource_group.test.name
    loadbalancer_id     = azurerm_lb.test.id
    name                = "BackEndAddressPool"
   }

   resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = azurerm_subnet.test.id
      private_ip_address_allocation = "dynamic"
      load_balancer_backend_address_pools_ids = [azurerm_lb_backend_address_pool.test.id]
    }
   }

   resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = azurerm_resource_group.test.location
    resource_group_name  = azurerm_resource_group.test.name
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
   }

   resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
   }

   resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = azurerm_resource_group.test.location
    availability_set_id   = azurerm_availability_set.avset.id
    resource_group_name   = azurerm_resource_group.test.name
    network_interface_ids = [element(azurerm_network_interface.test.*.id, count.index)]
    vm_size               = "Standard_DS1_v2"

    # Uncomment this line to delete the OS disk automatically when deleting the VM
    # delete_os_disk_on_termination = true

    # Uncomment this line to delete the data disks automatically when deleting the VM
    # delete_data_disks_on_termination = true

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "myosdisk${count.index}"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    # Optional data disks
    storage_data_disk {
      name              = "datadisk_new_${count.index}"
      managed_disk_type = "Standard_LRS"
      create_option     = "Empty"
      lun               = 0
      disk_size_gb      = "1023"
    }

    storage_data_disk {
      name            = element(azurerm_managed_disk.test.*.name, count.index)
      managed_disk_id = element(azurerm_managed_disk.test.*.id, count.index)
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = element(azurerm_managed_disk.test.*.disk_size_gb, count.index)
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags {
      environment = "staging"
    }
   }
   ```

## <a name="3-initialize-terraform"></a>3.Terraform を初期化する 

前のセクションで作成した Terraform 構成ファイルが格納されているディレクトリを初期化するには、[terraform init コマンド](https://www.terraform.io/docs/commands/init.html)を使います。 新しい Terraform 構成を作成した後は常に、`terraform init` コマンドを実行することをお勧めします。 

> [!TIP]
> `terraform init` コマンドはべき等なので、繰り返し呼び出しても同じ結果が得られます。 したがって、コラボレーション環境で作業していて、構成ファイルが変更されていると思われる場合は、プランを実行または適用する前に、常に `terraform init` コマンドを呼び出すことをお勧めします。

Terraform を初期化するには、次のコマンドを実行します。

  ```bash
  terraform init
  ```

  ![Terraform の初期化](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4.Terraform 実行プランを作成する

実行プランを作成するには、[terraform plan コマンド](https://www.terraform.io/docs/commands/plan.html)を使います。 実行プランを生成するため、Terraform は現在のディレクトリにあるすべての `.tf` ファイルを集約します。 

[-out パラメーター](https://www.terraform.io/docs/commands/plan.html#out-path)により、実行プランが出力ファイルに保存されます。 この機能は、マルチ開発環境でよく見られるコンカレンシーの問題に対処します。 出力ファイルによって解決されるそうした問題の 1 つに、次のようなシナリオがあります。

1. Dev 1 が構成ファイルを作成します。
1. Dev 2 が構成ファイルを変更します。
1. Dev 1 が構成ファイルを適用 (実行) します。
1. Dev 1 は、Dev 2 が構成を変更したことを知らないため、予期しない結果になります。

Dev 1 が出力ファイルを指定すると、Dev 2 が Dev 1 に影響を及ぼさなくなります。 

実行プランを保存する必要がない場合は、次のコマンドを実行します。

  ```bash
  terraform plan
  ```

実行プランを保存する必要がある場合は、次のコマンドを実行します。 プレースホルダーをご使用の環境の適切な値に置き換えます。

  ```bash
  terraform plan -out=<path>
  ```

もう 1 つの便利なパラメーターは、[-var-file](https://www.terraform.io/docs/commands/plan.html#var-file-foo) です。

既定では、Terraform は次のように変数ファイルを見つけようとしました。
- `terraform.tfvars` という名前のファイル
- `*.auto.tfvars` のパターンを使用した名前のファイル

ただし、変数ファイルは、上記の 2 つの規則のいずれにも従う必要はありません。 その場合は、`-var-file` パラメーターを使用して、変数ファイル名を指定します。 この点について次の例で説明します。

```hcl
terraform plan -var-file <my-variables-file.tf>
```

Terraform は、構成ファイルに指定された状態を実現するために必要なアクションを決定します。

![Terraform 実行プランの作成](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5.Terraform 実行プランを適用する

最後のステップでは、[terraform apply コマンド](https://www.terraform.io/docs/commands/apply.html)を使って、`terraform plan` コマンドによって生成されたアクションのセットを適用します。

最新の実行プランを適用する場合は、次のコマンドを実行します。

  ```bash
  terraform apply
  ```

以前に保存した実行プランを適用する場合は、次のコマンドを実行します。 プレースホルダーをご使用の環境の適切な値に置き換えます。

  ```bash
  terraform apply <path>
  ```

![Terraform 実行プランの適用](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [Terraform を使用して Azure 仮想マシン スケール セットを作成する](terraform-create-vm-scaleset-network-disks-hcl.md)