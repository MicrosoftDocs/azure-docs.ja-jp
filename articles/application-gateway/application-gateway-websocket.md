---
title: "Application Gateway による WebSocket のサポート | Microsoft Docs"
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
ms.date: 12/16/2016
ms.author: amsriva
translationtype: Human Translation
ms.sourcegitcommit: 119275f335344858cd20b6a17ef87e3ef32b6e12
ms.openlocfilehash: 5af1b1bacce2fe189a7b557527520795ed622b54
ms.lasthandoff: 03/01/2017


---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Application Gateway での WebSocket のサポートの概要

Application Gateway では、あらゆる規模のゲートウェイで WebSocket がネイティブにサポートされます。 ユーザーが構成可能な、WebSocket のサポートを選択的に有効または無効にするための設定はありません。 これまでどおり標準の HTTPListener をポート 80/443 で使用して WebSocket トラフィックを受信することができます。 WebSocket トラフィックは、アプリケーション ゲートウェイの規則で指定されている適切なバックエンド プールを使用して、WebSocket が有効なバックエンド サーバーに送られます。 [RFC6455](https://tools.ietf.org/html/rfc6455) で標準化された WebSocket プロトコルは、長時間に及ぶ TCP 接続上でサーバーとクライアント間の全二重通信を可能にします。 この機能により、HTTP ベースの実装では必須だったポーリングを使用することなく、Web サーバーとクライアントの間により対話的な双方向通信が可能になります。  WebSocket は、HTTP とは異なってオーバーヘッドが少なく、複数の要求や応答で同じ TCP 接続を再利用できるため、リソースをより効率的に使用できます。 WebSocket プロトコルは、従来の HTTP ポート 80 および 443 上で動作するよう設計されています。

バックエンド サーバーは、アプリケーション ゲートウェイ プローブに応答する必要があります (アプリケーション ゲートウェイ プローブについては、[正常性プローブの概要](application-gateway-probe-overview.md)に関するセクションをご覧ください)。 アプリケーション ゲートウェイの正常性プローブは HTTP または HTTPS のみに対応します。このことは、アプリケーション ゲートウェイが WebSocket トラフィックをサーバーにルーティングするためにはすべてのバックエンド サーバーが HTTP プローブに応答する必要があることを意味します。

## <a name="listener-configuration-element"></a>リスナーの構成要素

既存の HTTPListener を使用して WebSocket をサポートできます。 サンプル テンプレート ファイルの HttpListeners 要素のスニペットを次に示します。 WebSocket トラフィックおよびセキュリティで保護された WebSocket トラフィックをサポートするには、HTTP リスナーと HTTPS リスナーの両方が必要です。 同様に、[ポータル](application-gateway-create-gateway-portal.md)または [PowerShell](application-gateway-create-gateway-arm.md) を使用して、ポート 80/443 にリスナーを設定したアプリケーション ゲートウェイを作成して WebSocket トラフィックをサポートできます。

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>BackendAddressPool、BackendHttpSetting、およびルーティング規則の構成

WebSocket が有効なサーバーにバックエンド プールを定義するには、BackendAddressPool を使用する必要があります。 BackendHttpSetting には、バックエンド ポート 80/443 のみを定義する必要があります。 Cookie ベースのアフィニティのプロパティと requestTimeouts は、WebSocket のトラフィックには関係ありません。 ルーティング ルールには必要な変更がありません。 適切なリスナーを対応するバックエンド アドレス プールに結び付けるには、引き続きルーティング規則の "Basic" を使用する必要があります。 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>WebSocket が有効なバックエンド

WebSocket が動作するためには、バックエンドの構成済みのポート (通常は 80/443) で HTTP/HTTPS Web サーバーが実行されている必要があります。 この要件は、WebSocket プロトコルでは最初のハンドシェイクで HTTP を使用し、ヘッダー フィールドで WebSocket プロトコルへの Upgrade を指定する必要があるためです。

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

もう&1; つの理由としては、アプリケーション ゲートウェイのバックエンドの正常性プローブでサポートされるプロトコルが HTTP/HTTPS のみであるという点が挙げられます。 バックエンド サーバーが HTTP/HTTPS プローブに応答しない場合、そのバックエンド サーバーはバックエンド プールから取り除かれます。その結果、WebSocket 要求を含むすべての要求がこのバックエンドに到達できなくなります。

## <a name="next-steps"></a>次のステップ

WebSocket のサポートについて学習した後は、 [アプリケーション ゲートウェイの作成](application-gateway-create-gateway.md) に関するページに進んで、WebSocket が有効な Web アプリケーションを作成しましょう。


