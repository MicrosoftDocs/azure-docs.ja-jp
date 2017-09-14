---
title: "URL ベースのコンテンツ ルーティングの概要 | Microsoft Docs"
description: "このページでは、Application Gateway URL ベースのコンテンツ ルーティング、UrlPathMap 構成、および PathBasedRouting ルールの概要を説明します。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: 
ms.assetid: 4409159b-e22d-4c9a-a103-f5d32465d163
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 75c3279d2d02cb3c6e949d191c88a1eb18b58a27
ms.contentlocale: ja-jp
ms.lasthandoff: 08/04/2017

---
# <a name="url-path-based-routing-overview"></a>URL パス ベースのルーティングの概要

URL パス ベースのルーティングを使用すると、要求の URL パスに基づいてバックエンド サーバー プールにトラフィックをルーティングできます。 

1 つのシナリオとして、異なる種類のコンテンツの要求を、異なるバックエンド サーバー プールにルーティングします。

次の例では、Application Gateway は 3 つのバックエンド サーバー プール (VideoServerPool、ImageServerPool、DefaultServerPool など) からの contoso.com のトラフィックを処理します。

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

http://contoso.com/video* に対する要求は VideoServerPool に、http://contoso.com/images* に対する要求は ImageServerPool に、それぞれルーティングされます。 一致するパス パターンがない場合は、DefaultServerPool が選択されます。

> [!IMPORTANT]
> 規則は、ポータルにおける表示順に処理されます。 基本リスナーを構成する前に、まずマルチサイト リスナーを構成することを強くお勧めします。  そうすることで、トラフィックが確実に適切なバックエンドにルーティングされます。 基本リスナーが先に表示されていて、なおかつ受信要求と一致した場合、そのリスナーによって要求が処理されます。

## <a name="urlpathmap-configuration-element"></a>UrlPathMap 構成要素

urlPathMap 要素は、パス パターンのバックエンド サーバー プールのマッピングへの指定に使用します。 次のコード例は、テンプレート ファイルの urlPathMap 要素のスニペットです。

```json
"urlPathMaps": [{
    "name": "{urlpathMapName}",
    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/    {subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName1}"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpSettingsList/{settingname1}"
        },
        "pathRules": [{
            "name": "{pathRuleName}",
            "properties": {
                "paths": [
                    "{pathPattern}"
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName2}"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpsettingsList/{settingName2}"
                }
            }
        }]
    }
}]
```

> [!NOTE]
> PathPattern: この設定は、照合するパス パターンの一覧です。 それぞれ / で始まる必要があり、"*" が許可されるのは末尾の "/" の後だけです。 パス照合に提供する文字列には最初の ? または # の後にテキストを含めません (これらの文字は、ここでは許可されません)。

詳しくは、「 [Resource Manager template using URL-based routing (URL ベースのルーティングを使用した Resource Manager テンプレート)](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing) 」をご覧ください。

## <a name="pathbasedrouting-rule"></a>PathBasedRouting ルール

タイプ PathBasedRouting の RequestRoutingRule は、リスナーを urlPathMap にバインドする際に使用します。 このリスナーで受け取られるすべての要求は、urlPathMap に指定されたポリシーに基づいてルーティングされます。
PathBasedRouting ルールのスニペット:

```json
"requestRoutingRules": [
    {

"name": "{ruleName}",
"id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/requestRoutingRules/{ruleName}",
"properties": {
    "ruleType": "PathBasedRouting",
    "httpListener": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/httpListeners/<listenerName>"
    },
    "urlPathMap": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/ urlPathMaps/{urlpathMapName}"
    },

}
    }
]
```

## <a name="next-steps"></a>次のステップ

URL ベースのコンテンツ ルーティングについて理解したら、 [URL ベースのルーティングを使用した Application Gateway の作成](application-gateway-create-url-route-portal.md) に関するセクションに移動して、URL ルーティング ルールを使ってアプリケーション ゲートウェイを作成します。

