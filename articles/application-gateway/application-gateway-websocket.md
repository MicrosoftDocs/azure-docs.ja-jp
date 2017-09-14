---
title: "Azure Application Gateway による WebSocket のサポート | Microsoft Docs"
description: "このページでは、Application Gateway による WebSocket のサポートの概要を示します。"
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 8968dac1-e9bc-4fa1-8415-96decacab83f
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: amsriva
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 75b06ddd02da231b7813c609c848c75e42116da5
ms.contentlocale: ja-jp
ms.lasthandoff: 06/08/2017


---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Application Gateway での WebSocket のサポートの概要

Application Gateway では、あらゆる規模のゲートウェイで WebSocket がネイティブにサポートされます。 ユーザーが構成可能な、WebSocket のサポートを選択的に有効または無効にするための設定はありません。 

[RFC6455](https://tools.ietf.org/html/rfc6455) で標準化された WebSocket プロトコルは、長時間に及ぶ TCP 接続上でサーバーとクライアント間の全二重通信を可能にします。 この機能により、HTTP ベースの実装では必須だったポーリングを使用することなく、Web サーバーとクライアントの間により対話的な双方向通信が可能になります。 WebSocket は、HTTP とは異なってオーバーヘッドが少なく、複数の要求や応答で同じ TCP 接続を再利用できるため、リソースをより効率的に使用できます。 WebSocket プロトコルは、従来の HTTP ポート 80 および 443 上で動作するよう設計されています。

これまでどおり標準の HTTP リスナーをポート 80 または 443 で使用して WebSocket トラフィックを受信することができます。 WebSocket トラフィックは、アプリケーション ゲートウェイの規則で指定されている適切なバックエンド プールを使用して、WebSocket が有効なバックエンド サーバーに送られます。 バックエンド サーバーは、アプリケーション ゲートウェイ プローブに応答する必要があります (アプリケーション ゲートウェイ プローブについては、[正常性プローブの概要](application-gateway-probe-overview.md)に関するセクションをご覧ください)。 アプリケーション ゲートウェイの正常性プローブは HTTP/HTTPS のみです。 アプリケーション ゲートウェイが WebSocket トラフィックをサーバーにルーティングするには、各バックエンド サーバーが HTTP プローブに応答する必要があります。

## <a name="listener-configuration-element"></a>リスナーの構成要素

既存の HTTP リスナーを使用して WebSocket トラフィックをサポートできます。 サンプル テンプレート ファイルの httpListeners 要素のスニペットを次に示します。 WebSocket トラフィックおよびセキュリティで保護された WebSocket トラフィックをサポートするには、HTTP リスナーと HTTPS リスナーの両方が必要です。 同様に、[ポータル](application-gateway-create-gateway-portal.md)または [PowerShell](application-gateway-create-gateway-arm.md) を使用して、ポート 80/443 にリスナーを設定したアプリケーション ゲートウェイを作成して WebSocket トラフィックをサポートできます。

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>BackendAddressPool、BackendHttpSetting、およびルーティング規則の構成

WebSocket が有効なサーバーにバックエンド プールを定義するには、BackendAddressPool を使用します。 backendHttpSetting は、バックエンド ポート 80 および 443 を使用して定義されます。 Cookie ベースのアフィニティのプロパティと requestTimeouts は、WebSocket のトラフィックには関係ありません。 適切なリスナーを対応するバックエンド アドレス プールに結び付けるには、引き続きルーティング規則の "Basic" を使用する必要があります。 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>WebSocket が有効なバックエンド

WebSocket が動作するためには、バックエンドの構成済みのポート (通常は 80/443) で HTTP/HTTPS Web サーバーが実行されている必要があります。 この要件は、WebSocket プロトコルでは最初のハンドシェイクで HTTP を使用し、ヘッダー フィールドで WebSocket プロトコルへのアップグレードを指定する必要があるためです。 ヘッダーの例を次に示します。

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

もう 1 つの理由としては、アプリケーション ゲートウェイのバックエンドの正常性プローブでサポートされるプロトコルが HTTP と HTTPS のみであるという点が挙げられます。 バックエンド サーバーが HTTP または HTTPS プローブに応答しない場合、そのバックエンド サーバーはバックエンド プールから取り除かれます。

## <a name="next-steps"></a>次のステップ

WebSocket のサポートについて学習した後は、 [アプリケーション ゲートウェイの作成](application-gateway-create-gateway.md) に関するページに進んで、WebSocket が有効な Web アプリケーションを作成しましょう。


