---
title: Azure Application Gateway での WebSocket のサポート
description: Application Gateway では、あらゆる規模のゲートウェイで WebSocket がネイティブにサポートされます。 ユーザーが構成可能な設定はありません。
author: vhorne
ms.author: amsriva
ms.service: application-gateway
services: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.openlocfilehash: 59ca7708b24d2e75381290b80adeb671e2b49822
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91362697"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Application Gateway での WebSocket のサポートの概要

Application Gateway では、あらゆる規模のゲートウェイで WebSocket がネイティブにサポートされます。 ユーザーが構成可能な、WebSocket のサポートを選択的に有効または無効にするための設定はありません。 

[RFC6455](https://tools.ietf.org/html/rfc6455) で標準化された WebSocket プロトコルは、長時間に及ぶ TCP 接続上でサーバーとクライアント間の全二重通信を可能にします。 この機能により、HTTP ベースの実装では必須だったポーリングを使用することなく、Web サーバーとクライアントの間により対話的な双方向通信が可能になります。 WebSocket は、HTTP とは異なってオーバーヘッドが少なく、複数の要求や応答で同じ TCP 接続を再利用できるため、リソースをより効率的に使用できます。 WebSocket プロトコルは、従来の HTTP ポート 80 および 443 上で動作するよう設計されています。

これまでどおり標準の HTTP リスナーをポート 80 または 443 で使用して WebSocket トラフィックを受信することができます。 WebSocket トラフィックは、アプリケーション ゲートウェイの規則で指定されている適切なバックエンド プールを使用して、WebSocket が有効なバックエンド サーバーに送られます。 バックエンド サーバーは、アプリケーション ゲートウェイ プローブに応答する必要があります (アプリケーション ゲートウェイ プローブについては、[正常性プローブの概要](application-gateway-probe-overview.md)に関するセクションをご覧ください)。 アプリケーション ゲートウェイの正常性プローブは HTTP/HTTPS のみです。 アプリケーション ゲートウェイが WebSocket トラフィックをサーバーにルーティングするには、各バックエンド サーバーが HTTP プローブに応答する必要があります。

これは、チャット、ダッシュボード、ゲーム アプリなど、高速でリアルタイムの通信を利用するアプリケーションで使用されます。

## <a name="how-does-websocket-work"></a>WebSocket のしくみ

WebSocket の接続を確立するために、特定の HTTP ベースのハンドシェイクがクライアントとサーバーの間で交換されます。 成功すると、アプリケーション レイヤー プロトコルは、以前に確立された TCP 接続を使用して、HTTP から WebSockets に "アップグレード" されます。 これが発生すると、HTTP は完全に無関係になります。WebSocket 接続が閉じられるまで、両方のエンドポイントによって WebSocket プロトコルを使用してデータが送受信されます。 

![図では、Web サーバーとやりとりするクライアントを WebSocket のやりとりと比較しています。前者では 2 回接続し、2 つの応答を取得しますが、後者ではクライアントがサーバーに 1 回接続し、複数の応答を取得します。](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>リスナーの構成要素

既存の HTTP リスナーを使用して WebSocket トラフィックをサポートできます。 サンプル テンプレート ファイルの httpListeners 要素のスニペットを次に示します。 WebSocket トラフィックおよびセキュリティで保護された WebSocket トラフィックをサポートするには、HTTP リスナーと HTTPS リスナーの両方が必要です。 同様に、ポータルまたは Azure PowerShell を使用して、ポート 80/443 にリスナーを設定したアプリケーション ゲートウェイを作成して WebSocket トラフィックをサポートできます。

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

WebSocket が有効なサーバーにバックエンド プールを定義するには、BackendAddressPool を使用します。 backendHttpSetting は、バックエンド ポート 80 および 443 を使用して定義されます。 HTTP 設定の要求タイムアウト値は、WebSocket セッションにも適用されます。 ルーティング規則を変更する必要はありません。それは、適切なリスナーを対応するバックエンド アドレス プールに関連付けるために使用されます。 

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
    Origin: https://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

もう 1 つの理由としては、アプリケーション ゲートウェイのバックエンドの正常性プローブでサポートされるプロトコルが HTTP と HTTPS のみであるという点が挙げられます。 バックエンド サーバーが HTTP または HTTPS プローブに応答しない場合、そのバックエンド サーバーはバックエンド プールから取り除かれます。

## <a name="next-steps"></a>次のステップ

WebSocket のサポートについて学習した後は、 [アプリケーション ゲートウェイの作成](quick-create-powershell.md) に関するページに進んで、WebSocket が有効な Web アプリケーションを作成しましょう。