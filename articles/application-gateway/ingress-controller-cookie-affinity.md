---
title: Application Gateway で Cookie ベースのアフィニティを有効にする
description: この記事では、アプリケーション ゲートウェイで Cookie ベースのアフィニティを有効にする方法について説明します。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3af2705fedbb9c19d4f128e8e997d3fa73f8b5a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807965"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>アプリケーション ゲートウェイで Cookie ベースのアフィニティを有効にする
[Azure Application Gateway のドキュメント](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings)に示されているように、Application Gateway では Cookie ベースのアフィニティがサポートされています。これは、あるユーザー セッションからの後続のトラフィックを同じサーバーに送信して処理できるということです。

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
