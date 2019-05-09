---
title: Azure Application Gateway での複数のサイトのホスト
description: このページでは、Azure Application Gateway による複数サイトのサポートの概要を示します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 1/16/2019
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: d01ff17966d2e6b9aeaaf3c1f69817dbc84c3a6f
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792635"
---
# <a name="application-gateway-multiple-site-hosting"></a>Application Gateway の複数サイトのホスト

複数サイトのホストにより、同じアプリケーション ゲートウェイ インスタンスで複数の Web アプリケーションを構成することができます。 この機能を使用すると、最大で 100 個の Web サイトを 1 つのアプリケーション ゲートウェイに追加することによって、デプロイに効率的なトポロジを構成できます。 各 Web サイトは、独自のバックエンド プールに送られるようにすることができます。 次の例では、アプリケーション ゲートウェイは、2 つのバックエンド サーバー プール (ContosoServerPool と FabrikamServerPool) からの contoso.com および fabrikam.com のトラフィックを処理します。

![imageURLroute](./media/application-gateway-multi-site-overview/multisite.png)

> [!IMPORTANT]
> 規則は、ポータルにおける表示順に処理されます。 基本リスナーを構成する前に、まずマルチサイト リスナーを構成することを強くお勧めします。  そうすることで、トラフィックが確実に適切なバックエンドにルーティングされます。 基本リスナーが先に表示されていて、なおかつ受信要求と一致した場合、そのリスナーによって要求が処理されます。

http://contoso.com の要求は ContosoServerPool にルーティングされ、 http://fabrikam.com は FabrikamServerPool にルーティングされます。

同様に、同じ親ドメインの 2 つのサブドメインも、同じアプリケーション ゲートウェイ デプロイでホストすることができます。 サブドメインを使用する例としては、単一のアプリケーション ゲートウェイ デプロイ上でホストされる http://blog.contoso.com 、 http://app.contoso.com などがあります。

## <a name="host-headers-and-server-name-indication-sni"></a>ホスト ヘッダーと Server Name Indication (SNI)

同じインフラストラクチャで複数サイトのホストを有効にするための一般的な方法は 3 つあります。

1. 複数の Web アプリケーションをそれぞれ、一意の IP アドレスでホストする。
2. ホスト名を使用して、同じ IP アドレスで複数の Web アプリケーションをホストする。
3. さまざまなポートを使用して、同じ IP アドレスで複数の Web アプリケーションをホストする。

現在、アプリケーション ゲートウェイは、トラフィックをリッスンする単一のパブリック IP アドレスを取得します。 そのため、それぞれ独自の IP アドレスを持つ複数のアプリケーションへの対応は、現在、サポートされていません。 Application Gateway は、それぞれが異なるポートをリッスンしている複数のアプリケーションのホストをサポートしていますが、このシナリオではアプリケーションが非標準ポートでトラフィックを受け入れることが必要です。多くの場合、それは望ましい構成ではありません。 Application Gateway は、複数の Web サイトを同じパブリック IP アドレスとポートでホストするために、HTTP 1.1 ホスト ヘッダーを利用します。 アプリケーション ゲートウェイでホストされているサイトは、Server Name Indication (SNI) TLS 拡張機能で SSL オフロードをサポートすることもできます。 つまり、クライアント ブラウザーとバックエンド Web ファームは、HTTP/1.1 と、RFC 6066 で定義されている TLS 拡張機能をサポートする必要があります。

## <a name="listener-configuration-element"></a>リスナーの構成要素

既存の HTTPListener 構成要素は、ホスト名とサーバー名の指示要素をサポートするように拡張されています。これらの要素は、アプリケーション ゲートウェイがトラフィックを適切なバックエンド プールにルーティングするために使用されます。 次のコード例は、テンプレート ファイルの HttpListeners 要素のスニペットです。

```json
"httpListeners": [
    {
        "name": "appGatewayHttpsListener1",
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
            "HostName": "contoso.com",
            "RequireServerNameIndication": "true"
        }
    },
    {
        "name": "appGatewayHttpListener2",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
            },
            "Protocol": "Http",
            "HostName": "fabrikam.com",
            "RequireServerNameIndication": "false"
        }
    }
],
```

エンド ツー エンドのテンプレート ベースのデプロイについては、[複数サイトのホスティングを使用する Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting)を参照してください。

## <a name="routing-rule"></a>ルーティング ルール

ルーティング規則には変更は必要ありません。 適切なサイト リスナーを、対応するバックエンド アドレス プールに結び付けるために、ルーティング規則の "Basic" が選択されたままにしておく必要があります。

```json
"requestRoutingRules": [
{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

},
{
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}
]
```

## <a name="next-steps"></a>次の手順

複数サイトのホストについて学習した後に、 [複数サイトのホストを使用するアプリケーション ゲートウェイの作成](application-gateway-create-multisite-azureresourcemanager-powershell.md) に関するページにアクセスして、複数の Web アプリケーションをサポートする機能を備えたアプリケーション ゲートウェイを作成してください。