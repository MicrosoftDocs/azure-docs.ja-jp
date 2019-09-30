---
title: Azure で Terraform を使用してハブ仮想ネットワーク アプライアンスを作成する
description: 他のすべてのネットワーク間の共通の接続ポイントとして機能するハブ VNet を作成するチュートリアル
services: terraform
ms.service: azure
keywords: terraform, ハブとスポーク, ネットワーク, ハイブリッド ネットワーク, devops, 仮想マシン, Azure, VNet ピアリング, ハブとスポーク, ハブ
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 1fae21e9a60f533533607e74609853ef68348daf
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173417"
---
# <a name="tutorial-create-a-hub-virtual-network-appliance-with-terraform-in-azure"></a>チュートリアル:Azure で Terraform を使用してハブ仮想ネットワーク アプライアンスを作成する

**VPN デバイス**は、オンプレミス ネットワークとの外部接続を実現するデバイスです。 VPN デバイスには、ハードウェア デバイスとソフトウェア ソリューションとがあります。 ソフトウェア ソリューションとしては、たとえば Windows Server 2012 のルーティングとリモート アクセス サービス (RRAS) があります。 VPN アプライアンスの詳細については、[サイト間 VPN Gateway 接続の VPN デバイス](/azure/vpn-gateway/vpn-gateway-about-vpn-devices)に関するページを参照してください。

Azure では、さまざまなネットワーク仮想アプライアンスがサポートされ、その豊富な選択肢から選ぶことができます。 このチュートリアルでは、Ubuntu イメージを使用しています。 Azure でサポートされる幅広いデバイス ソリューションについて詳しくは、[Network Appliances のホーム ページ](https://azure.microsoft.com/solutions/network-appliances/)を参照してください。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * HCL (HashiCorp 言語) を使用して、ハブスポーク トポロジにハブ VNet を実装する
> * Terraform を使用して、アプライアンスとして機能するハブ ネットワーク仮想マシンを作成する
> * Terraform と CustomScript の拡張機能を使用してルートを有効にする
> * Terraform を使用してハブ スポーク ゲートウェイのルート テーブルを作成する

## <a name="prerequisites"></a>前提条件

1. [Azure で Terraform を使用して、ハブ スポーク ハイブリッド ネットワーク トポロジを作成します](./terraform-hub-spoke-introduction.md)。
1. [Azure で Terraform を使用して、オンプレミス仮想ネットワークを作成します](./terraform-hub-spoke-on-prem.md)。
1. [Azure で Terraform を使用して、ハブ仮想ネットワークを作成します](./terraform-hub-spoke-hub-network.md)。

## <a name="create-the-directory-structure"></a>ディレクトリ構造を作成する

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

## <a name="declare-the-hub-network-appliance"></a>ハブ ネットワーク アプライアンスを宣言する

オンプレミスの仮想ネットワークを宣言する Terraform 構成ファイルを作成します。

1. Cloud Shell で、`hub-nva.tf` という名前の新しいファイルを作成します。

    ```bash
    code hub-nva.tf
    ```

1. 以下のコードをエディターに貼り付けます。
    
    ```hcl
    locals {
      prefix-hub-nva         = "hub-nva"
      hub-nva-location       = "CentralUS"
      hub-nva-resource-group = "hub-nva-rg"
    }

    resource "azurerm_resource_group" "hub-nva-rg" {
      name     = "${local.prefix-hub-nva}-rg"
      location = "${local.hub-nva-location}"

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_network_interface" "hub-nva-nic" {
      name                 = "${local.prefix-hub-nva}-nic"
      location             = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name  = "${azurerm_resource_group.hub-nva-rg.name}"
      enable_ip_forwarding = true

      ip_configuration {
        name                          = "${local.prefix-hub-nva}"
        subnet_id                     = "${azurerm_subnet.hub-dmz.id}"
        private_ip_address_allocation = "Static"
        private_ip_address            = "10.0.0.36"
      }

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_virtual_machine" "hub-nva-vm" {
      name                  = "${local.prefix-hub-nva}-vm"
      location              = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name   = "${azurerm_resource_group.hub-nva-rg.name}"
      network_interface_ids = ["${azurerm_network_interface.hub-nva-nic.id}"]
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
        computer_name  = "${local.prefix-hub-nva}-vm"
        admin_username = "${var.username}"
        admin_password = "${var.password}"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_virtual_machine_extension" "enable-routes" {
      name                 = "enable-iptables-routes"
      location             = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name  = "${azurerm_resource_group.hub-nva-rg.name}"
      virtual_machine_name = "${azurerm_virtual_machine.hub-nva-vm.name}"
      publisher            = "Microsoft.Azure.Extensions"
      type                 = "CustomScript"
      type_handler_version = "2.0"

      settings = <<SETTINGS
        {
            "fileUris": [
            "https://raw.githubusercontent.com/mspnp/reference-architectures/master/scripts/linux/enable-ip-forwarding.sh"
            ],
            "commandToExecute": "bash enable-ip-forwarding.sh"
        }
    SETTINGS

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_route_table" "hub-gateway-rt" {
      name                          = "hub-gateway-rt"
      location                      = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name           = "${azurerm_resource_group.hub-nva-rg.name}"
      disable_bgp_route_propagation = false

      route {
        name           = "toHub"
        address_prefix = "10.0.0.0/16"
        next_hop_type  = "VnetLocal"
      }

      route {
        name                   = "toSpoke1"
        address_prefix         = "10.1.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      route {
        name                   = "toSpoke2"
        address_prefix         = "10.2.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_subnet_route_table_association" "hub-gateway-rt-hub-vnet-gateway-subnet" {
      subnet_id      = "${azurerm_subnet.hub-gateway-subnet.id}"
      route_table_id = "${azurerm_route_table.hub-gateway-rt.id}"
      depends_on = ["azurerm_subnet.hub-gateway-subnet"]
    }

    resource "azurerm_route_table" "spoke1-rt" {
      name                          = "spoke1-rt"
      location                      = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name           = "${azurerm_resource_group.hub-nva-rg.name}"
      disable_bgp_route_propagation = false

      route {
        name                   = "toSpoke2"
        address_prefix         = "10.2.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      route {
        name           = "default"
        address_prefix = "0.0.0.0/0"
        next_hop_type  = "vnetlocal"
      }

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_subnet_route_table_association" "spoke1-rt-spoke1-vnet-mgmt" {
      subnet_id      = "${azurerm_subnet.spoke1-mgmt.id}"
      route_table_id = "${azurerm_route_table.spoke1-rt.id}"
      depends_on = ["azurerm_subnet.spoke1-mgmt"]
    }

    resource "azurerm_subnet_route_table_association" "spoke1-rt-spoke1-vnet-workload" {
      subnet_id      = "${azurerm_subnet.spoke1-workload.id}"
      route_table_id = "${azurerm_route_table.spoke1-rt.id}"
      depends_on = ["azurerm_subnet.spoke1-workload"]
    }

    resource "azurerm_route_table" "spoke2-rt" {
      name                          = "spoke2-rt"
      location                      = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name           = "${azurerm_resource_group.hub-nva-rg.name}"
      disable_bgp_route_propagation = false

      route {
        name                   = "toSpoke1"
        address_prefix         = "10.1.0.0/16"
        next_hop_in_ip_address = "10.0.0.36"
        next_hop_type          = "VirtualAppliance"
      }

      route {
        name           = "default"
        address_prefix = "0.0.0.0/0"
        next_hop_type  = "vnetlocal"
      }

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_subnet_route_table_association" "spoke2-rt-spoke2-vnet-mgmt" {
      subnet_id      = "${azurerm_subnet.spoke2-mgmt.id}"
      route_table_id = "${azurerm_route_table.spoke2-rt.id}"
      depends_on = ["azurerm_subnet.spoke2-mgmt"]
    }

    resource "azurerm_subnet_route_table_association" "spoke2-rt-spoke2-vnet-workload" {
      subnet_id      = "${azurerm_subnet.spoke2-workload.id}"
      route_table_id = "${azurerm_route_table.spoke2-rt.id}"
      depends_on = ["azurerm_subnet.spoke2-workload"]
    }

    ```

1. ファイルを保存し、エディターを終了します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure で Terraform を使用して、スポーク仮想ネットワークを作成する](./terraform-hub-spoke-spoke-network.md)