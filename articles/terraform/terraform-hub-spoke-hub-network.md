---
title: Azure で Terraform を使用してハブ仮想ネットワークを作成する
description: Azure で異なるネットワーク間の共通接続ポイントとして機能するハブ VNet を作成する方法を示すチュートリアル
services: terraform
ms.service: azure
keywords: Terraform, ハブとスポーク, ネットワーク, ハイブリッド ネットワーク, devops, 仮想マシン, Azure, VNet ピアリング, ハブとスポーク, ハブ
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 03/01/2019
ms.openlocfilehash: 5f8d9119ea9caeb598946a384f321fa6f7e14def
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58000225"
---
# <a name="tutorial-create-a-hub-virtual-network-with-terraform-in-azure"></a>チュートリアル:Azure で Terraform を使用してハブ仮想ネットワークを作成する

ハブ仮想ネットワーク (VNet) は、オンプレミス ネットワークへの主要な接続ポイントとして機能します。 この VNet では、スポーク VNet 内でホストされるワークロードによって使用される共有サービスをホストします。 デモの目的で、このチュートリアルでは共有サービスは実装されていません。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * HCL (HashiCorp 言語) を使用して、ハブスポーク トポロジにハブ VNet を実装する
> * Terraform を使用して、ハブ ジャンプ ボックス仮想マシンを作成する
> * Terraform を使用して、ハブ仮想プライベート ネットワーク ゲートウェイを作成する
> * Terraform を使用して、ハブとオンプレミスのゲートウェイの接続を作成する

## <a name="prerequisites"></a>前提条件

1. [Azure で Terraform を使用して、ハブとスポークのハイブリッド ネットワーク トポロジを作成します](./terraform-hub-spoke-introduction.md)。
1. [Azure で Terraform を使用して、オンプレミス仮想ネットワークを作成します](./terraform-hub-spoke-on-prem.md)。

## <a name="create-the-directory-structure"></a>ディレクトリ構造を作成する

ハブ ネットワークは、次のコンポーネントで構成されます。

- ハブ仮想ネットワーク
- ハブ仮想ネットワーク ゲートウェイ
- ハブ ゲートウェイ接続 

次の Terraform 構成ファイルでリソースを定義します。

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

## <a name="declare-the-hub-vnet"></a>ハブ VNet を宣言する

ハブ仮想ネットワークを宣言する Terraform 構成ファイルを作成します。

1. Cloud Shell で、`hub-vnet.tf` という名前のファイルを作成します。

    ```bash
    code hub-vnet.tf
    ```

1. 以下のコードをエディターに貼り付けます。

    ```JSON
    locals {
      prefix-hub         = "hub"
      hub-location       = "CentralUS"
      hub-resource-group = "hub-vnet-rg"
      shared-key         = "4-v3ry-53cr37-1p53c-5h4r3d-k3y"
    }

    resource "azurerm_resource_group" "hub-vnet-rg" {
      name     = "${local.hub-resource-group}"
      location = "${local.hub-location}"
    }

    resource "azurerm_virtual_network" "hub-vnet" {
      name                = "${local.prefix-hub}-vnet"
      location            = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.hub-vnet-rg.name}"
      address_space       = ["10.0.0.0/16"]

      tags {
        environment = "hub-spoke"
      }
    }

    resource "azurerm_subnet" "hub-gateway-subnet" {
      name                 = "GatewaySubnet"
      resource_group_name  = "${azurerm_resource_group.hub-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.hub-vnet.name}"
      address_prefix       = "10.0.255.224/27"
    }

    resource "azurerm_subnet" "hub-mgmt" {
      name                 = "mgmt"
      resource_group_name  = "${azurerm_resource_group.hub-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.hub-vnet.name}"
      address_prefix       = "10.0.0.64/27"
    }

    resource "azurerm_subnet" "hub-dmz" {
      name                 = "dmz"
      resource_group_name  = "${azurerm_resource_group.hub-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.hub-vnet.name}"
      address_prefix       = "10.0.0.32/27"
    }

    resource "azurerm_network_interface" "hub-nic" {
      name                 = "${local.prefix-hub}-nic"
      location             = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name  = "${azurerm_resource_group.hub-vnet-rg.name}"
      enable_ip_forwarding = true

      ip_configuration {
        name                          = "${local.prefix-hub}"
        subnet_id                     = "${azurerm_subnet.hub-mgmt.id}"
        private_ip_address_allocation = "Dynamic"
      }

      tags {
        environment = "${local.prefix-hub}"
      }
    }

    #Virtual Machine
    resource "azurerm_virtual_machine" "hub-vm" {
      name                  = "${local.prefix-hub}-vm"
      location              = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name   = "${azurerm_resource_group.hub-vnet-rg.name}"
      network_interface_ids = ["${azurerm_network_interface.hub-nic.id}"]
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
        computer_name  = "${local.prefix-hub}-vm"
        admin_username = "${var.username}"
        admin_password = "${var.password}"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = "${local.prefix-hub}"
      }
    }

    # Virtual Network Gateway
    resource "azurerm_public_ip" "hub-vpn-gateway1-pip" {
      name                = "hub-vpn-gateway1-pip"
      location            = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.hub-vnet-rg.name}"

      allocation_method = "Dynamic"
    }

    resource "azurerm_virtual_network_gateway" "hub-vnet-gateway" {
      name                = "hub-vpn-gateway1"
      location            = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.hub-vnet-rg.name}"

      type     = "Vpn"
      vpn_type = "RouteBased"

      active_active = false
      enable_bgp    = false
      sku           = "VpnGw1"

      ip_configuration {
        name                          = "vnetGatewayConfig"
        public_ip_address_id          = "${azurerm_public_ip.hub-vpn-gateway1-pip.id}"
        private_ip_address_allocation = "Dynamic"
        subnet_id                     = "${azurerm_subnet.hub-gateway-subnet.id}"
      }
      depends_on = ["azurerm_public_ip.hub-vpn-gateway1-pip"]
    }

    resource "azurerm_virtual_network_gateway_connection" "hub-onprem-conn" {
      name                = "hub-onprem-conn"
      location            = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.hub-vnet-rg.name}"

      type           = "Vnet2Vnet"
      routing_weight = 1

      virtual_network_gateway_id      = "${azurerm_virtual_network_gateway.hub-vnet-gateway.id}"
      peer_virtual_network_gateway_id = "${azurerm_virtual_network_gateway.onprem-vpn-gateway.id}"

      shared_key = "${local.shared-key}"
    }

    resource "azurerm_virtual_network_gateway_connection" "onprem-hub-conn" {
      name                = "onprem-hub-conn"
      location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"
      type                            = "Vnet2Vnet"
      routing_weight = 1
      virtual_network_gateway_id      = "${azurerm_virtual_network_gateway.onprem-vpn-gateway.id}"
      peer_virtual_network_gateway_id = "${azurerm_virtual_network_gateway.hub-vnet-gateway.id}"

      shared_key = "${local.shared-key}"
    }
    ```
    
1. ファイルを保存し、エディターを終了します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [Azure で Terraform を使用してハブ仮想ネットワーク アプライアンスを作成する](./terraform-hub-spoke-hub-nva.md)