---
title: Terraform:VNet 統合およびプライベート エンドポイントと安全に接続されたフロントエンド Web アプリとバックエンド Web アプリをデプロイする
description: App Service 用の Terraform プロバイダーを使用して、プライベート エンドポイントおよび VNet 統合と安全に接続された 2 つの Web アプリをデプロイする方法について説明します
author: ericgre
ms.assetid: 3e5d1bbd-5581-40cc-8f65-bc74f1802156
ms.topic: sample
ms.date: 08/10/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: c1de8ebbd9ad381628cfeb19413baa295b42b3db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91739835"
---
# <a name="create-two-web-apps-connected-securely-with-private-endpoint-and-vnet-integration"></a>プライベート エンドポイントおよび VNet 統合と安全に接続された 2 つの Web アプリを作成する

この記事では、以下の手順に従い、[プライベート エンドポイント](../networking/private-endpoint.md)とリージョン [VNet 統合](../web-sites-integrate-with-vnet.md)を使用して、2 つの Web アプリ (フロントエンドとバックエンド) を安全に接続する例について説明します。
- VNet をデプロイします
- 統合用の最初のサブネットを作成します
- プライベート エンドポイント用の 2 番目のサブネットを作成します。特定のパラメーターを設定して、ネットワーク ポリシーを無効にする必要があります
- プライベート エンドポイント機能に必要な、PremiumV2 または PremiumV3 タイプの App Service プランを 1 つデプロイします
- プライベート DNS ゾーンを使用する特定のアプリ設定を使用して、フロントエンド Web アプリを作成します。詳細は[こちら](../web-sites-integrate-with-vnet.md#azure-dns-private-zones)を参照してください
- フロントエンド Web アプリを統合サブネットに接続します
- バックエンド Web アプリを作成します
- Web アプリのプライベート リンク ゾーンの名前 (privatelink.azurewebsites.net) を使用して、DNS プライベート ゾーンを作成します
- このゾーンを VNet にリンクします
- エンドポイント サブネットでバックエンド Web アプリ用のプライベート エンドポイントを作成し、前に作成した DNS プライベート ゾーンに DNS 名 (Web サイトと SCM) を登録します

## <a name="how-to-use-terraform-in-azure"></a>Azure で Terraform を使用する方法

Azure で Terraform を使用する方法については、[Azure のドキュメント](/azure/developer/terraform/)を参照してください。

## <a name="the-complete-terraform-file"></a>完全な Terraform ファイル

このファイルを使用するには、frontwebapp および backwebapp リソースの name プロパティを変更する必要があります (Web アプリの名前は、世界全域で一意の DNS 名である必要があります)。 

```hcl
provider "azurerm" {
  version = "~>2.0"
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = "appservice-rg"
  location = "francecentral"
}

resource "azurerm_virtual_network" "vnet" {
  name                = "vnet"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "integrationsubnet" {
  name                 = "integrationsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
  delegation {
    name = "delegation"
    service_delegation {
      name = "Microsoft.Web/serverFarms"
    }
  }
}

resource "azurerm_subnet" "endpointsubnet" {
  name                 = "endpointsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.2.0/24"]
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_app_service_plan" "appserviceplan" {
  name                = "appserviceplan"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  sku {
    tier = "Premiumv2"
    size = "P1v2"
  }
}

resource "azurerm_app_service" "frontwebapp" {
  name                = "frontwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id

  app_settings = {
    "WEBSITE_DNS_SERVER": "168.63.129.16",
    "WEBSITE_VNET_ROUTE_ALL": "1"
  }
}

resource "azurerm_app_service_virtual_network_swift_connection" "vnetintegrationconnection" {
  app_service_id  = azurerm_app_service.frontwebapp.id
  subnet_id       = azurerm_subnet.integrationsubnet.id
}

resource "azurerm_app_service" "backwebapp" {
  name                = "backwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id
}

resource "azurerm_private_dns_zone" "dnsprivatezone" {
  name                = "privatelink.azurewebsites.net"
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_private_dns_zone_virtual_network_link" "dnszonelink" {
  name = "dnszonelink"
  resource_group_name = azurerm_resource_group.rg.name
  private_dns_zone_name = azurerm_private_dns_zone.dnsprivatezone.name
  virtual_network_id = azurerm_virtual_network.vnet.id
}

resource "azurerm_private_endpoint" "privateendpoint" {
  name                = "backwebappprivateendpoint"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  subnet_id           = azurerm_subnet.endpointsubnet.id

  private_dns_zone_group {
    name = "privatednszonegroup"
    private_dns_zone_ids = [azurerm_private_dns_zone.dnsprivatezone.id]
  }

  private_service_connection {
    name = "privateendpointconnection"
    private_connection_resource_id = azurerm_app_service.backwebapp.id
    subresource_names = ["sites"]
    is_manual_connection = false
  }
}
```




## <a name="next-steps"></a>次のステップ


> [Azure での Terraform の使用について詳細を参照](/azure/developer/terraform/)