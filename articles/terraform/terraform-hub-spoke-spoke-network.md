---
title: Azure で Terraform を使用してスポーク ネットワークを作成する
description: ハブスポーク トポロジー内に 1 つのハブに接続される 2 つの VNet 接続を実装する方法について説明します
services: terraform
ms.service: azure
keywords: Terraform, ハブとスポーク, ネットワーク, ハイブリッド ネットワーク, DevOps, 仮想マシン, Azure, VNet ピアリング, スポーク, ハブスポーク
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 9437f43a12204c9a08e1c0da11fc737e8c026c80
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173398"
---
# <a name="tutorial-create-a-spoke-virtual-network-with-terraform-in-azure"></a>チュートリアル:Azure で Terraform を使用してスポーク仮想ネットワークを作成する

このチュートリアルでは、ワークロードの分離を示すために 2 つの別個のスポーク ネットワークを実装します。 このネットワークでは、ハブ仮想ネットワークを使用して共通のリソースを共有します。 スポークを使用すると、独自の VNet にワークロードを分離して、その他のスポークから個別に管理できます。 各ワークロードには複数の階層が含まれる場合があります。これらの階層には、Azure ロード バランサーを使用して接続されている複数のサブネットがあります。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * HCL (HashiCorp 言語) を使用して、ハブスポーク トポロジ内にスポーク VNet を実装する
> * Terraform を使用して、スポーク ネットワーク内に仮想マシンを作成する
> * Terraform を使用して、ハブ ネットワークとの仮想ネットワーク ピアリングを確立する

## <a name="prerequisites"></a>前提条件

1. [Azure で Terraform を使用して、ハブ スポーク ハイブリッド ネットワーク トポロジを作成します](./terraform-hub-spoke-introduction.md)。
1. [Azure で Terraform を使用して、オンプレミス仮想ネットワークを作成します](./terraform-hub-spoke-on-prem.md)。
1. [Azure で Terraform を使用して、ハブ仮想ネットワークを作成します](./terraform-hub-spoke-hub-network.md)。
1. [Azure で Terraform を使用して、ハブ仮想ネットワーク アプライアンスを作成します](./terraform-hub-spoke-hub-nva.md)。

## <a name="create-the-directory-structure"></a>ディレクトリ構造を作成する

このセクションでは、2 つのスポーク スクリプトが作成されます。 各スクリプトは、ワークロード用のスポーク仮想ネットワークと仮想マシンを定義します。 その後、ハブからスポークに対してピアリングされた仮想ネットワークが作成されます。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。

1. [Azure Cloud Shell](/azure/cloud-shell/overview) を開きます。 前に環境を選択しなかった場合、環境として **Bash** を選択します。

    ![Cloud Shell のプロンプト](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. ディレクトリを `clouddrive` ディレクトリに変更します。

    ```bash
    cd clouddrive
    ```

1. 新しいディレクトリに移動します。

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-two-spoke-networks"></a>2 つのスポーク ネットワークを宣言する

1. Cloud Shell で、`spoke1.tf` という名前の新しいファイルを開きます。

    ```bash
    code spoke1.tf
    ```

1. 以下のコードをエディターに貼り付けます。

    ```hcl
    locals {
      spoke1-location       = "CentralUS"
      spoke1-resource-group = "spoke1-vnet-rg"
      prefix-spoke1         = "spoke1"
    }

    resource "azurerm_resource_group" "spoke1-vnet-rg" {
      name     = "${local.spoke1-resource-group}"
      location = "${local.spoke1-location}"
    }

    resource "azurerm_virtual_network" "spoke1-vnet" {
      name                = "spoke1-vnet"
      location            = "${azurerm_resource_group.spoke1-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.spoke1-vnet-rg.name}"
      address_space       = ["10.1.0.0/16"]

      tags {
        environment = "${local.prefix-spoke1 }"
      }
    }

    resource "azurerm_subnet" "spoke1-mgmt" {
      name                 = "mgmt"
      resource_group_name  = "${azurerm_resource_group.spoke1-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.spoke1-vnet.name}"
      address_prefix       = "10.1.0.64/27"
    }

    resource "azurerm_subnet" "spoke1-workload" {
      name                 = "workload"
      resource_group_name  = "${azurerm_resource_group.spoke1-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.spoke1-vnet.name}"
      address_prefix       = "10.1.1.0/24"
    }

    resource "azurerm_virtual_network_peering" "spoke1-hub-peer" {
      name                      = "spoke1-hub-peer"
      resource_group_name       = "${azurerm_resource_group.spoke1-vnet-rg.name}"
      virtual_network_name      = "${azurerm_virtual_network.spoke1-vnet.name}"
      remote_virtual_network_id = "${azurerm_virtual_network.hub-vnet.id}"

      allow_virtual_network_access = true
      allow_forwarded_traffic = true
      allow_gateway_transit   = false
      use_remote_gateways     = true
      depends_on = ["azurerm_virtual_network.spoke1-vnet", "azurerm_virtual_network.hub-vnet" , "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }

    resource "azurerm_network_interface" "spoke1-nic" {
      name                 = "${local.prefix-spoke1}-nic"
      location             = "${azurerm_resource_group.spoke1-vnet-rg.location}"
      resource_group_name  = "${azurerm_resource_group.spoke1-vnet-rg.name}"
      enable_ip_forwarding = true

      ip_configuration {
        name                          = "${local.prefix-spoke1}"
        subnet_id                     = "${azurerm_subnet.spoke1-mgmt.id}"
        private_ip_address_allocation = "Dynamic"
      }
    }

    resource "azurerm_virtual_machine" "spoke1-vm" {
      name                  = "${local.prefix-spoke1}-vm"
      location              = "${azurerm_resource_group.spoke1-vnet-rg.location}"
      resource_group_name   = "${azurerm_resource_group.spoke1-vnet-rg.name}"
      network_interface_ids = ["${azurerm_network_interface.spoke1-nic.id}"]
      vm_size               = "${var.vmsize}"

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-spoke1}-vm"
        admin_username = "${var.username}"
        admin_password = "${var.password}"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = "${local.prefix-spoke1}"
      }
    }

    resource "azurerm_virtual_network_peering" "hub-spoke1-peer" {
      name                      = "hub-spoke1-peer"
      resource_group_name       = "${azurerm_resource_group.hub-vnet-rg.name}"
      virtual_network_name      = "${azurerm_virtual_network.hub-vnet.name}"
      remote_virtual_network_id = "${azurerm_virtual_network.spoke1-vnet.id}"
      allow_virtual_network_access = true
      allow_forwarded_traffic   = true
      allow_gateway_transit     = true
      use_remote_gateways       = false
      depends_on = ["azurerm_virtual_network.spoke1-vnet", "azurerm_virtual_network.hub-vnet", "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }
    ```

1. ファイルを保存し、エディターを終了します。

1. `spoke2.tf` という名前で新しいファイルを作成します。

      ```bash
      code spoke2.tf
      ```
    
1. 以下のコードをエディターに貼り付けます。
    
    ```hcl
    locals {
      spoke2-location       = "CentralUS"
      spoke2-resource-group = "spoke2-vnet-rg"
      prefix-spoke2         = "spoke2"
    }

    resource "azurerm_resource_group" "spoke2-vnet-rg" {
      name     = "${local.spoke2-resource-group}"
      location = "${local.spoke2-location}"
    }

    resource "azurerm_virtual_network" "spoke2-vnet" {
      name                = "${local.prefix-spoke2}-vnet"
      location            = "${azurerm_resource_group.spoke2-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.spoke2-vnet-rg.name}"
      address_space       = ["10.2.0.0/16"]

      tags {
        environment = "${local.prefix-spoke2}"
      }
    }

    resource "azurerm_subnet" "spoke2-mgmt" {
      name                 = "mgmt"
      resource_group_name  = "${azurerm_resource_group.spoke2-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.spoke2-vnet.name}"
      address_prefix       = "10.2.0.64/27"
    }

    resource "azurerm_subnet" "spoke2-workload" {
      name                 = "workload"
      resource_group_name  = "${azurerm_resource_group.spoke2-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.spoke2-vnet.name}"
      address_prefix       = "10.2.1.0/24"
    }

    resource "azurerm_virtual_network_peering" "spoke2-hub-peer" {
      name                      = "${local.prefix-spoke2}-hub-peer"
      resource_group_name       = "${azurerm_resource_group.spoke2-vnet-rg.name}"
      virtual_network_name      = "${azurerm_virtual_network.spoke2-vnet.name}"
      remote_virtual_network_id = "${azurerm_virtual_network.hub-vnet.id}"

      allow_virtual_network_access = true
      allow_forwarded_traffic = true
      allow_gateway_transit   = false
      use_remote_gateways     = true
      depends_on = ["azurerm_virtual_network.spoke2-vnet", "azurerm_virtual_network.hub-vnet", "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }

    resource "azurerm_network_interface" "spoke2-nic" {
      name                 = "${local.prefix-spoke2}-nic"
      location             = "${azurerm_resource_group.spoke2-vnet-rg.location}"
      resource_group_name  = "${azurerm_resource_group.spoke2-vnet-rg.name}"
      enable_ip_forwarding = true

      ip_configuration {
        name                          = "${local.prefix-spoke2}"
        subnet_id                     = "${azurerm_subnet.spoke2-mgmt.id}"
        private_ip_address_allocation = "Dynamic"
      }

      tags {
        environment = "${local.prefix-spoke2}"
      }
    }

    resource "azurerm_virtual_machine" "spoke2-vm" {
      name                  = "${local.prefix-spoke2}-vm"
      location              = "${azurerm_resource_group.spoke2-vnet-rg.location}"
      resource_group_name   = "${azurerm_resource_group.spoke2-vnet-rg.name}"
      network_interface_ids = ["${azurerm_network_interface.spoke2-nic.id}"]
      vm_size               = "${var.vmsize}"

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-spoke2}-vm"
        admin_username = "${var.username}"
        admin_password = "${var.password}"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = "${local.prefix-spoke2}"
      }
    }

    resource "azurerm_virtual_network_peering" "hub-spoke2-peer" {
      name                      = "hub-spoke2-peer"
      resource_group_name       = "${azurerm_resource_group.hub-vnet-rg.name}"
      virtual_network_name      = "${azurerm_virtual_network.hub-vnet.name}"
      remote_virtual_network_id = "${azurerm_virtual_network.spoke2-vnet.id}"
      allow_virtual_network_access = true
      allow_forwarded_traffic   = true
      allow_gateway_transit     = true
      use_remote_gateways       = false
      depends_on = ["azurerm_virtual_network.spoke2-vnet", "azurerm_virtual_network.hub-vnet", "azurerm_virtual_network_gateway.hub-vnet-gateway"]
    }
    ```
     
1. ファイルを保存し、エディターを終了します。
  
## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [Azure で Terraform を使用してハブとスポーク ネットワークを検証する](./terraform-hub-spoke-validation.md)