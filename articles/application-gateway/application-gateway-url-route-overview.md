---
title: "URL ベースのコンテンツ ルーティングの概要 | Microsoft Docs"
description: "このページでは、Application Gateway URL ベースのコンテンツ ルーティング、UrlPathMap 構成、および PathBasedRouting ルールの概要を説明します。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 4409159b-e22d-4c9a-a103-f5d32465d163
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: ee8cfffdbf054b4251ed269745f6b9ee5a5e6c64
ms.openlocfilehash: 1f273f3b55d719e37b9cdb6cefda30c3566e7226
ms.lasthandoff: 11/18/2016


---
# <a name="url-path-based-routing-overview"></a>URL パス ベースのルーティングの概要

URL パス ベースのルーティングを使用すると、要求の URL パスに基づいてバックエンド サーバー プールにトラフィックをルーティングできます。 1 つのシナリオとして、異なる種類のコンテンツの要求を、異なるバックエンド サーバー プールにルーティングします。
次の例では、Application Gateway は 3 つのバックエンド サーバー プール (VideoServerPool、ImageServerPool、DefaultServerPool など) からの contoso.com のトラフィックを処理します。

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

http://contoso.com/video* に対する要求は VideoServerPool に、http://contoso.com/images* に対する要求は ImageServerPool に、それぞれルーティングされます。 一致するパス パターンがない場合は、DefaultServerPool が選択されます。
    
## <a name="urlpathmap-configuration-element"></a>UrlPathMap 構成要素

UrlPathMap 要素は、パス パターンのバックエンド サーバー プールのマッピングへの指定に使用します。 次のコード例は、テンプレート ファイルの urlPathMap 要素のスニペットです。

```json
"urlPathMaps": [{
    "name": "<urlPathMapName>",
    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/urlPathMaps/<urlPathMapName>",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendAddressPools/<poolName>"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendHttpSettingsList/<settingsName>"
        },
        "pathRules": [{
            "name": "<pathRuleName>",
            "properties": {
                "paths": [
                    "<pathPattern>"
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendAddressPools/<poolName2>"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendHttpsettingsList/<settingsName2>"
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

"name": "<ruleName>",
"id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/requestRoutingRules/<ruleName>",
"properties": {
    "ruleType": "PathBasedRouting",
    "httpListener": {
        "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/httpListeners/<listenerName>"
    },
    "urlPathMap": {
        "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/ urlPathMaps/<urlPathMapName>"
    },

}
    }
]
```

## <a name="next-steps"></a>次のステップ

URL ベースのコンテンツ ルーティングについて理解したら、 [URL ベースのルーティングを使用した Application Gateway の作成](application-gateway-create-url-route-portal.md) に関するセクションに移動して、URL ルーティング ルールを使ってアプリケーション ゲートウェイを作成します。


