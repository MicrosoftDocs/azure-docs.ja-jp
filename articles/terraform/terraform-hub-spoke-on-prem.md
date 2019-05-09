---
title: Azure で Terraform を使用してオンプレミス仮想ネットワークを作成する
description: ローカル リソースを格納するオンプレミス VNet を Azure で実装する方法を示すチュートリアル
services: terraform
ms.service: azure
keywords: Terraform, ハブとスポーク, ネットワーク, ハイブリッド ネットワーク, devops, 仮想マシン, Azure, VNet ピアリング, オンプレミス
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 03/01/2019
ms.openlocfilehash: 435ee13de28fb1591a5579761ecc7ad5bf9f9d76
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58000829"
---
# <a name="tutorial-create-on-premises-virtual-network-with-terraform-in-azure"></a>チュートリアル:Azure で Terraform を使用してオンプレミス仮想ネットワークを作成する

このチュートリアルでは、Azure 仮想ネットワーク (VNet) を使用して、オンプレミス ネットワークを実装します。 Azure VNet は、ご自身のプライベート仮想ネットワークに置き換えることができます。 これを行うには、サブネット内の適切な IP アドレスをマップします。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * HCL (HashiCorp 言語) を使用して、ハブスポーク トポロジにオンプレミス VNet を実装する
> * Terraform を使用して、ハブ ネットワーク アプライアンス リソースを作成する
> * Terraform を使用して、オンプレミス仮想マシンを作成する
> * Terraform を使用して、オンプレミス仮想プライベート ネットワーク ゲートウェイを作成する

## <a name="prerequisites"></a>前提条件

1. [Azure で Terraform を使用して、ハブとスポークのハイブリッド ネットワーク トポロジを作成します](./terraform-hub-spoke-introduction.md)。

## <a name="create-the-directory-structure"></a>ディレクトリ構造を作成する

オンプレミス ネットワークをシミュレートするには、Azure 仮想ネットワークを作成します。 デモの VNet は、実際のプライベート オンプレミス ネットワークの代わりとなるものです。 既存のオンプレミス ネットワークでこれと同じことを行うには、サブネット内の適切な IP アドレスをマップします。

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

## <a name="declare-the-on-premises-vnet"></a>オンプレミス VNet を宣言する

オンプレミス VNet を宣言する Terraform 構成ファイルを作成します。

1. Cloud Shell で、`on-prem.tf` という名前の新しいファイルを開きます。

    ```bash
    code on-prem.tf
    ```

1. 以下のコードをエディターに貼り付けます。

    ```JSON
    locals {
      onprem-location       = "SouthCentralUS"
      onprem-resource-group = "onprem-vnet-rg"
      prefix-onprem         = "onprem"
    }

    resource "azurerm_resource_group" "onprem-vnet-rg" {
      name     = "${local.onprem-resource-group}"
      location = "${local.onprem-location}"
    }

    resource "azurerm_virtual_network" "onprem-vnet" {
      name                = "onprem-vnet"
      location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"
      address_space       = ["192.168.0.0/16"]

      tags {
        environment = "${local.prefix-onprem}"
      }
    }

    resource "azurerm_subnet" "onprem-gateway-subnet" {
      name                 = "GatewaySubnet"
      resource_group_name  = "${azurerm_resource_group.onprem-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.onprem-vnet.name}"
      address_prefix       = "192.168.255.224/27"
    }

    resource "azurerm_subnet" "onprem-mgmt" {
      name                 = "mgmt"
      resource_group_name  = "${azurerm_resource_group.onprem-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.onprem-vnet.name}"
      address_prefix       = "192.168.1.128/25"
    }

    resource "azurerm_public_ip" "onprem-pip" {
        name                         = "${local.prefix-onprem}-pip"
        location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
        resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"
        allocation_method   = "Dynamic"

        tags {
            environment = "${local.prefix-onprem}"
        }
    }

    resource "azurerm_network_interface" "onprem-nic" {
      name                 = "${local.prefix-onprem}-nic"
      location             = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name  = "${azurerm_resource_group.onprem-vnet-rg.name}"
      enable_ip_forwarding = true

      ip_configuration {
        name                          = "${local.prefix-onprem}"
        subnet_id                     = "${azurerm_subnet.onprem-mgmt.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.onprem-pip.id}"
      }
    }

    # Create Network Security Group and rule
    resource "azurerm_network_security_group" "onprem-nsg" {
        name                = "${local.prefix-onprem}-nsg"
        location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
        resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"

        security_rule {
            name                       = "SSH"
            priority                   = 1001
            direction                  = "Inbound"
            access                     = "Allow"
            protocol                   = "Tcp"
            source_port_range          = "*"
            destination_port_range     = "22"
          source_address_prefix      = "*"
            destination_address_prefix = "*"
        }

        tags {
            environment = "onprem"
        }
    }

    resource "azurerm_subnet_network_security_group_association" "mgmt-nsg-association" {
      subnet_id                 = "${azurerm_subnet.onprem-mgmt.id}"
      network_security_group_id = "${azurerm_network_security_group.onprem-nsg.id}"
    }

    resource "azurerm_virtual_machine" "onprem-vm" {
      name                  = "${local.prefix-onprem}-vm"
      location              = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name   = "${azurerm_resource_group.onprem-vnet-rg.name}"
      network_interface_ids = ["${azurerm_network_interface.onprem-nic.id}"]
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
        computer_name  = "${local.prefix-onprem}-vm"
        admin_username = "${var.username}"
        admin_password = "${var.password}"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = "${local.prefix-onprem}"
      }
    }

    resource "azurerm_public_ip" "onprem-vpn-gateway1-pip" {
      name                = "${local.prefix-onprem}-vpn-gateway1-pip"
      location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"

      allocation_method = "Dynamic"
    }

    resource "azurerm_virtual_network_gateway" "onprem-vpn-gateway" {
      name                = "onprem-vpn-gateway1"
      location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"

      type     = "Vpn"
      vpn_type = "RouteBased"

      active_active = false
      enable_bgp    = false
      sku           = "VpnGw1"

      ip_configuration {
        name                          = "vnetGatewayConfig"
        public_ip_address_id          = "${azurerm_public_ip.onprem-vpn-gateway1-pip.id}"
        private_ip_address_allocation = "Dynamic"
        subnet_id                     = "${azurerm_subnet.onprem-gateway-subnet.id}"
      }
      depends_on = ["azurerm_public_ip.onprem-vpn-gateway1-pip"]

    }
    ```

1. ファイルを保存し、エディターを終了します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure で Terraform を使用してハブ仮想ネットワークを作成する](./terraform-hub-spoke-hub-network.md)