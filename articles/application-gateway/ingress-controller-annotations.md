---
title: Application Gateway イングレス コントローラーの注釈
description: この記事では、Application Gateway イングレス コントローラーに固有の注釈に関するドキュメントを提供します。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 4f570f9f18f9c9d484a9bc9c1a5c64d42dbdc714
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397452"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Application Gateway イングレス コントローラーの注釈 

## <a name="introductions"></a>はじめに

Kubernetes イングレス リソースには、任意のキー/値のペアを使用して注釈を付けることができます。 AGIC は、イングレス YAML を介して構成できない Application Gateway 機能をプログラムするために、注釈に依存します。 イングレス注釈は、イングレス リソースから派生したすべての HTTP 設定、バックエンド プール、およびリスナーに適用されます。

## <a name="list-of-supported-annotations"></a>サポートされている注釈の一覧

イングレス リソースを AGIC によって監視するためには、`kubernetes.io/ingress.class: azure/application-gateway` を使用してリソースに **注釈を付ける必要があります**。 そうしないと、AGIC は問題のイングレス リソースに対して機能しません。

| 注釈キー | 値の種類 | 既定値 | 使用できる値
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32` (秒) | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32` (秒) | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>バックエンド パス プレフィックス

この注釈を使用すると、イングレス リソースで指定されたバックエンド パスを、この注釈で指定されたプレフィックスで書き換えることができます。 これによりユーザーは、サービスのエンドポイントが、イングレス リソースでサービスを公開するために使用されるエンドポイント名と異なっている場合でも、そのサービスを公開できます。

### <a name="usage"></a>使用法

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>例

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-bkprefix
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```
上の例では、注釈 `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` を使用して、`go-server-ingress-bkprefix` という名前のイングレス リソースを定義しました。 この注釈は、パス `/hello` から `/test/` へのパス プレフィックス オーバーライドを含む HTTP 設定を作成するよう、アプリケーション ゲートウェイに指示します。

> [!NOTE] 
> 上記の例では、1 つの規則のみを定義しています。 ただし、注釈はイングレス リソース全体に適用可能なため、ユーザーが複数の規則を定義した場合、指定されたパスのそれぞれに対してバックエンド パス プレフィックスが設定されます。 したがって、(同じサービスに対するものであっても) パス プレフィックスが異なっている別々の規則が必要な場合、ユーザーは別々のイングレス リソースを定義する必要があります。

## <a name="tls-redirect"></a>TLS リダイレクト

対応する HTTPS に HTTP URL を自動的にリダイレクトするように Application Gateway を[構成できます](./redirect-overview.md)。 この注釈が存在し、TLS が正しく構成されている場合、Kubernetes イングレス コントローラーは、[リダイレクト構成を持つルーティング規則](./redirect-http-to-https-portal.md#add-a-routing-rule-with-a-redirection-configuration)を作成し、変更を Application Gateway に適用します。 作成されるリダイレクトは HTTP `301 Moved Permanently` になります。

### <a name="usage"></a>使用法

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>例

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-redirect
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
   - hosts:
     - www.contoso.com
     secretName: testsecret-tls
  rules:
  - host: www.contoso.com
    http:
      paths:
      - backend:
          serviceName: websocket-repeater
          servicePort: 80
```

## <a name="connection-draining"></a>接続のドレイン

`connection-draining`: この注釈によりユーザーは、接続のドレインを有効にするかどうかを指定できます。
`connection-draining-timeout`: この注釈により、ユーザーはタイムアウトを指定できます。この時間の経過後に Application Gateway は、ドレイン状態のバックエンド エンドポイントへの要求を終了します。

### <a name="usage"></a>使用法

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>例

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-drain
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/connection-draining: "true"
    appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="cookie-based-affinity"></a>Cookie ベースのアフィニティ

この注釈では、Cookie ベースのアフィニティを有効にするかどうかを指定できます。

### <a name="usage"></a>使用法

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>例

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-affinity
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="request-timeout"></a>要求タイムアウト

この注釈では、要求タイムアウトを秒単位で指定できます。この時間を過ぎても応答が受信されない場合、Application Gateway は要求を失敗と見なします。

### <a name="usage"></a>使用法

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
```

### <a name="example"></a>例

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/request-timeout: "20"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="use-private-ip"></a>プライベート IP を使用

この注釈では、このエンドポイントを Application Gateway のプライベート IP で公開するかどうかを指定できます。

> [!NOTE]
> * Application Gateway では、同じポート上の複数の IP (例: 80/443) はサポートされません。 `HTTP` で、`appgw.ingress.kubernetes.io/use-private-ip: "false"` という注釈を持つイングレスと、`appgw.ingress.kubernetes.io/use-private-ip: "true"` という注釈を持つ別のイングレスがある場合、AGIC は Application Gateway の更新に失敗します。
> * プライベート IP を持たない Application Gateway では、`appgw.ingress.kubernetes.io/use-private-ip: "true"` という注釈を持つイングレスは無視されます。 `NoPrivateIP` 警告が有効なイングレスの場合、これはコントローラーのログとイングレス イベントに反映されます。


### <a name="usage"></a>使用法
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

### <a name="example"></a>例
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/use-private-ip: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="backend-protocol"></a>バックエンド プロトコル

この注釈では、ポッドとの通信中に Application Gateway が使用するプロトコルを指定できます。 サポートされるプロトコル: `http`、`https`

> [!NOTE]
> * 自己署名証明書は Application Gateway でサポートされていますが、現在は、よく知られた CA によって署名された証明書をポッドが使用している場合、AGIC は `https` のみをサポートします。
> * ポッドでは、HTTPS ではポート 80、HTTP ではポート 443 を使用しないようにしてください。

### <a name="usage"></a>使用法
```yaml
appgw.ingress.kubernetes.io/backend-protocol: "https"
```

### <a name="example"></a>例
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-protocol: "https"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 443
```