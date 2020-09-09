---
title: イングレス エンドポイントの内部ルーティングにプライベート IP アドレスを使用する
description: この記事では、内部ルーティングにプライベート IP を使用し、クラスター内のイングレス エンドポイントをその他の VNet に公開する方法について説明します。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 33b70ba8ab7ffef90c42f53e58a2d27e619862f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84806794"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>イングレス エンドポイントの内部ルーティングにプライベート IP を使用する 

この機能を使って、プライベート IP を使用して `Virtual Network` 内のイングレス エンドポイントを公開できます。

## <a name="pre-requisites"></a>前提条件  
[プライベート IP 構成](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip)を使用した Application Gateway

イングレスにプライベート IP を使用するようにコントローラーを構成するには、次の 2 つの方法があります。

## <a name="assign-to-a-particular-ingress"></a>特定のイングレスに割り当てる
プライベート IP 経由で特定のイングレスを公開するには、イングレスで注釈 [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) を使用します。

### <a name="usage"></a>使用法
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

プライベート IP を使用しない Application Gateway の場合、`appgw.ingress.kubernetes.io/use-private-ip: "true"` の注釈が付けられたイングレスは無視されます。 これは、イングレス イベントと AGIC ポッド ログに示されます。

* イングレス イベントに示されるエラー

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* AGIC ログに示されるエラー

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>グローバルに割り当てる
念のため、プライベート IP で公開されるすべてのイングレスを制限することを要件にして、`helm` の構成で `appgw.usePrivateIP: true` を使用します。

### <a name="usage"></a>使用法
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

これにより、Application Gateway 上でフロントエンド リスナーを構成するときに、イングレス コントローラーで IP アドレス構成がプライベート IP に絞り込まれます。
`usePrivateIP: true` とプライベート IP が割り当てられていない場合、AGIC はパニック状態になり、クラッシュします。

> [!NOTE]
> Application Gateway v2 SKU にはパブリック IP が必要です。 Application Gateway をプライベートにする必要がある場合は、[`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview) を Application Gateway のサブネットにアタッチしてトラフィックを制限します。
