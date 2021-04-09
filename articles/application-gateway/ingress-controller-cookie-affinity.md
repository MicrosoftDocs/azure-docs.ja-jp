---
title: Application Gateway で Cookie ベースのアフィニティを有効にする
description: この記事では、アプリケーション ゲートウェイで Cookie ベースのアフィニティを有効にする方法について説明します。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a7806cf9518090539ba540a9a522af1aae2691f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397418"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>アプリケーション ゲートウェイで Cookie ベースのアフィニティを有効にする
[Azure Application Gateway のドキュメント](./application-gateway-components.md#http-settings)に示されているように、Application Gateway では Cookie ベースのアフィニティがサポートされています。これは、あるユーザー セッションからの後続のトラフィックを同じサーバーに送信して処理できるということです。

## <a name="example"></a>例
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```