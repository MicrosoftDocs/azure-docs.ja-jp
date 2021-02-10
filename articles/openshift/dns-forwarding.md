---
title: Azure Red Hat OpenShift 4 の DNS 転送を構成する
description: Azure Red Hat OpenShift 4 の DNS 転送を構成する
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: aa18959d0dc78dedf8b57dc120ab6744afa9051f
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070595"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>Azure Red Hat OpenShift 4 クラスターで DNS 転送を構成する

Azure Red Hat OpenShift クラスターで DNS 転送を構成するには、DNS オペレーターを変更する必要があります。 この変更によって、クラスター内で実行されているアプリケーション ポッドはクラスター外のプライベート DNS サーバーでホストされている名前を解決できます。 OpenShift 4.6 向けのこれらの手順は[こちら](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)に記載されています。

たとえば、DNS サーバー 192.168.100.10 によって解決される *.example.com に対する DNS 要求をすべて転送する場合、次を実行してオペレーター構成を編集できます。
 
```bash
oc edit dns.operator/default
```
 
これによってエディターが起動します。`spec: {}` は次で置換できます。
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

ファイルを保存し、エディターを終了します。

## <a name="next-steps"></a>次のステップ
OpenShift 4.6 の DNS 転送に関する詳細は[こちら](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)でご覧ください。