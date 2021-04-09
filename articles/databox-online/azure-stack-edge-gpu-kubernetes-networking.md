---
title: Azure Stack Edge Pro デバイスでの Kubernetes ネットワークについて | Microsoft Docs
description: Azure Stack Edge Pro デバイスでの Kubernetes ネットワークの動作について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 1a0fc569b7d552101c1e0bd1a065b2a91ebfa3c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439614"
---
# <a name="kubernetes-networking-in-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスでの Kubernetes ネットワーク

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro デバイスでは、コンピューティング ロールを構成するときに Kubernetes クラスターが作成されます。 Kubernetes クラスターが作成されると、コンテナー化されたアプリケーションを Kubernetes クラスターのポッドにデプロイできます。 ご利用の Kubernetes クラスター内のポッドに対しては、さまざまな方法でネットワークを使用できます。 

この記事では、Kubernetes クラスターでのネットワークについて、一般的な内容を、さらに Azure Stack Edge Pro デバイスのコンテキストで具体的な内容を説明します。 

## <a name="networking-requirements"></a>ネットワーク要件

Kubernetes クラスターにデプロイされる一般的な 2 層アプリの例を次に示します。

- アプリは、Web サーバー フロント エンドと、バックエンドのデータベース アプリケーションを備えています。 
- すべてのポッドに IP が割り当てられますが、これらの IP はポッドの再起動およびフェールオーバー時に変更される可能性があります。 
- 各アプリは複数のポッドで構成されており、すべてのポッド レプリカ間でトラフィックの負荷分散が行われる必要があります。 

![Kubernetes のネットワーク要件](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1.png)

上記のシナリオでは、次のようなネットワーク要件が発生します。

 - 外部に接続するアプリケーションには、Kubernetes クラスターの外部にいるアプリケーション ユーザーが名前または IP アドレスを使用してアクセスする必要があります。 
 - Kubernetes クラスター内のアプリケーション (たとえば、ここではフロント エンドとバックエンド ポッド) は相互に通信できる必要があります。

上記のニーズを両方とも解決できる Kubernetes サービスを紹介します。 


## <a name="networking-services"></a>ネットワーク サービス

Kubernetes サービスには次の 2 種類があります。 

- **クラスター IP サービス** - アプリケーション ポッドに一定のエンドポイントを提供するものと考えてください。 これらのサービスに関連付けられているポッドには、Kubernetes クラスターの外部からアクセスすることはできません。 これらのサービスで使用される IP アドレスは、プライベート ネットワークのアドレス空間から取得されます。 
- **ロード バランサー IP** - クラスター IP サービスに似ています。ただし、関連する IP は外部ネットワークからのものであり、Kubernetes クラスターの外部からアクセスできます。


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->


## <a name="kubernetes-networking-on-azure-stack-edge-pro"></a>Azure Stack Edge Pro 上の Kubernetes ネットワーク

Calico、Metallb、および Core DNS はすべて、Azure Stack Edge Pro 上のネットワーク用にインストールされるコンポーネントです。 

- **Calico** では、プライベート IP 範囲からの IP アドレスがすべてのポッドに割り当てられ、これらのポッドに合わせてネットワークが構成されます。これにより、あるノードのポッドが別のノードのポッドと通信できるようになります。 
- **Metallb** はクラスター内ポッドで実行され、ロード バランサー型のサービスに IP アドレスを割り当てます。 ロード バランサーの IP アドレスは、ローカル UI を介して提供されるサービス IP の範囲から選択されます。 
- **コア DNS** - このアドオンでは、サービス名をクラスター IP アドレスにマッピングする DNS レコードが構成されます。

Kubernetes ノードと外部サービスに使用される IP アドレスは、デバイスのローカル UI にある **[コンピューティング ネットワーク]** ページで指定されます。

![ローカル UI での Kubernetes IP の割り当て](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

IP 割り当ての対象は次のとおりです。

- **Kubernetes ノード IP**: この IP 範囲は、Kubernetes のマスターおよびワーカー ノードに使用されます。 これらの IP は、Kubernetes ノードが相互に通信するときに使用されます。

- **Kubernetes 外部サービス IP**: この IP 範囲は、Kubernetes クラスターの外部で公開されている外部サービス (ロード バランサー サービスとも呼ばれます) に使用されます。  


## <a name="next-steps"></a>次のステップ

Azure Stack Edge Pro 上で Kubernetes ネットワークを構成するには、以下を参照してください。

- [Azure Stack Edge Pro で IoT Edge を介してステートレス アプリケーションを外部に公開する](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)。

- [Azure Stack Edge Pro で kubectl を介してステートレス アプリケーションを外部に公開する](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md)。
