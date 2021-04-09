---
title: WebSocket サーバーを Application Gateway に公開する
description: この記事では、AKS クラスター用のイングレス コントローラーを使用して Application Gateway に WebSocket サーバーを公開する方法について説明します。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 68d4ff7e4617136e4c58ce672f34de56e46f0229
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85207789"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>WebSocket サーバーを Application Gateway に公開する

Application Gateway v2 のドキュメントで説明されているように、[WebSocket および HTTP/2 プロトコルをネイティブにサポート](features.md#websocket-and-http2-traffic)しています。 Application Gateway と Kubernetes イングレスのどちらも、WebSocket サポートの有効または無効を選択できるようにユーザーが構成できる設定はないことに注意してください。

次の Kubernetes デプロイ YAML は、WebSocket サーバーのデプロイに使用される最小構成を示しています。これは、通常の Web サーバーをデプロイする場合と同じです。
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

すべての前提条件が満たされ、使用する AKS の Kubernetes イングレスによって制御されている Application Gateway がある場合、上記のデプロイでは、Application Gateway のパブリック IP と `ws.contoso.com` ドメインのポート 80 で Websocket サーバーが公開されます。

次の cURL コマンドで、WebSocket サーバーのデプロイをテストします。
```shell
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>WebSocket の正常性プローブ

デプロイによって正常性プローブが明示的に定義されていない場合、Application Gateway では WebSocket サーバー エンドポイントで HTTP GET が試行されます。
サーバーの実装によっては ([お勧めはこちら](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go))、WebSocket 固有のヘッダーが必要になる場合があります (たとえば `Sec-Websocket-Version`)。
Application Gateway では WebSocket ヘッダーが追加されないため、WebSocket サーバーからの Application Gateway の正常性プローブ応答が `400 Bad Request` になる可能性が高くなります。
結果として、Application Gateway によりポッドが異常としてマークされ、WebSocket サーバーのコンシューマーが `502 Bad Gateway` になります。
これを回避するには、正常性チェックのための HTTP GET ハンドラーをサーバーに追加することが必要になる場合があります (たとえば `/health` では `200 OK` が返される)。
