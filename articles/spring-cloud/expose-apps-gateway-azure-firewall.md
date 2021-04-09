---
title: アプリケーション ゲートウェイと Azure Firewall を使用してアプリケーションをインターネットに公開する
description: アプリケーション ゲートウェイと Azure Firewall を使用してアプリケーションをインターネットに公開する方法
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 6c22d1bae4f1d116aa52846880498c7c2a425174
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877622"
---
# <a name="expose-applications-to-the-internet-using-application-gateway-and-azure-firewall"></a>アプリケーション ゲートウェイと Azure Firewall を使用してアプリケーションをインターネットに公開する

このドキュメントでは、アプリケーション ゲートウェイと Azure Firewall を使用してアプリケーションをインターネットに公開する方法について説明します。 Azure Spring Cloud サービス インスタンスが仮想ネットワークにデプロイされている場合、このサービス インスタンス上のアプリケーションには、プライベート ネットワークからのみアクセスできます。 アプリケーションにインターネットからアクセスできるようにするには、**Azure Application Gateway** との統合が必要であり、また必要に応じて **Azure Firewall** とも統合します。

## <a name="prerequisites"></a>前提条件

- [Azure CLI バージョン 2.0.4 以降](/cli/azure/install-azure-cli)。

## <a name="define-variables"></a>変数の定義

「[Azure 仮想ネットワークに Azure Spring Cloud をデプロイする (VNet インジェクション)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)」の指示に従い、作成したリソース グループと仮想ネットワークに対して変数を定義します。 実際の環境に基づいて値をカスタマイズします。

```
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_APP_PRIVATE_FQDN='my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="login-to-azure"></a>Azure にログインする

Azure CLI にログインし、アクティブなサブスクリプションを選択します。

```
az login
az account set --subscription ${SUBSCRIPTION}
```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する

作成する **Azure Application Gateway** は、Azure Spring Cloud サービス インスタンスと同じ仮想ネットワーク、または Azure Spring Cloud サービス インスタンスとピアリングされた仮想ネットワークに参加します。 最初に、`az network vnet subnet create` を使用して、仮想ネットワーク内にアプリケーション ゲートウェイ用の新しいサブネットを作成し、さらに `az network public-ip create` を使用して、アプリケーション ゲートウェイのフロントエンドとしてパブリック IP アドレスを作成します。

```
APPLICATION_GATEWAY_PUBLIC_IP_NAME='app-gw-public-ip'
az network vnet subnet create \
    --name ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --address-prefix ${APPLICATION_GATEWAY_SUBNET_CIDR}
az network public-ip create \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --allocation-method Static \
    --sku Standard
```

## <a name="create-application-gateway"></a>アプリケーション ゲートウェイの作成

`az network application-gateway create` を使用してアプリケーション ゲートウェイを作成し、バックエンド プール内のサーバーとしてアプリケーションのプライベート完全修飾ドメイン名 (FQDN) を指定します。 次に、バックエンド プールのホスト名を使用するように、`az network application-gateway http-settings update` を使用して HTTP 設定を更新します。

```
APPLICATION_GATEWAY_NAME='my-app-gw'
az network application-gateway create \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --capacity 2 \
    --sku Standard_v2 \
    --http-settings-cookie-based-affinity Enabled \
    --http-settings-port 443 \
    --http-settings-protocol Https \
    --public-ip-address ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --subnet ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --servers ${SPRING_APP_PRIVATE_FQDN}
az network application-gateway http-settings update \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name appGatewayBackendHttpSettings \
    --host-name-from-backend-pool true
```

Azure によってアプリケーション ゲートウェイが作成されるのに最大 30 分かかる場合があります。 作成後、`az network application-gateway show-backend-health` を使用してバックエンドの正常性を確認します。  これにより、アプリケーション ゲートウェイがプライベート FQDN を介してアプリケーションに到達するかどうかが検証されます。

```
az network application-gateway show-backend-health \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP}
```

出力により、バックエンド プールが正常な状態であることが示されます。

```
{
  "backendAddressPools": [
    {
      "backendHttpSettingsCollection": [
        {
          "servers": [
            {
              "address": "my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io",
              "health": "Healthy",
              "healthProbeLog": "Success. Received 200 status code",
              "ipConfiguration": null
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="access-your-application-using-the-frontend-public-ip-of-the-application-gateway"></a>アプリケーション ゲートウェイのフロントエンド パブリック IP を使用してアプリケーションにアクセスする

`az network public-ip show` を使用して、アプリケーション ゲートウェイのパブリック IP アドレスを取得します。

```
az network public-ip show \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --query [ipAddress] \
    --output tsv
```

そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。

  ![パブリック IP のアプリ](media/spring-cloud-expose-apps-gateway-az-firewall/app-gateway-public-ip.png)

## <a name="see-also"></a>関連項目

- [VNET での Azure Spring Cloud のトラブルシューティング](spring-cloud-troubleshooting-vnet.md)
- [VNET での Azure Spring Cloud の実行に関するお客様の責任](spring-cloud-vnet-customer-responsibilities.md)