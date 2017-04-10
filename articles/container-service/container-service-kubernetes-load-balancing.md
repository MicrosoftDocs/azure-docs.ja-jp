---
title: "Azure の Kubernetes コンテナーで負荷を分散する | Microsoft Docs"
description: "外部から接続し、Azure Container Service の Kubernetes クラスターの複数のコンテナーで負荷を分散します。"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "コンテナー、マイクロサービス、Kubernetes、Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: e89ec01cb47a87a45378f73d138224095bcbebed
ms.openlocfilehash: 201d98c4f4ff29393ad308824ed0575f1ff602ee
ms.lasthandoff: 02/27/2017


---
# <a name="load-balance-containers-in-a-kubernetes-cluster-in-azure-container-service"></a>Azure Container Service の Kubernetes クラスターのコンテナーで負荷を分散する 
この記事では、Azure Container Service の Kubernetes クラスターでの負荷分散について説明します。 負荷分散により、サービスに外部からアクセスできる IP アドレスが提供され、エージェント VM で実行されるポッド間のネットワーク トラフィックが分散されます。

Kubernetes サービスが [Azure Load Balancer](../load-balancer/load-balancer-overview.md) を使用するように設定して、外部ネットワーク (TCP または UDP) トラフィックを管理することができます。 追加の構成をすると、HTTP または HTTPS トラフィックの負荷分散とルーティングのほか、さらに高度なシナリオも可能になります。

## <a name="prerequisites"></a>前提条件
* Azure Container Service で [Kubernetes クラスターをデプロイする](container-service-kubernetes-walkthrough.md)
* クラスターに[クライアントを接続する](container-service-connect.md)

## <a name="azure-load-balancer"></a>Azure Load Balancer

既定では、Azure Container Service にデプロイした Kubernetes クラスターには、インターネットに接続したエージェント VM 用の Azure Load Balancer が含まれます。 (マスター VM には、別のロード バランサーのリソースが構成されます。)Azure Load Balancer は、第 4 層 (TCP、UDP) のロード バランサーです。

Kubernetes サービスを作成する場合は、サービスへのアクセスを許可するように Azure Load Balancer を自動的に構成できます。 ロード バランサーを構成するには、サービス `type` を `LoadBalancer` に設定します。 ロード バランサーは、着信サービス トラフィックのパブリック IP アドレスとポート番号を、エージェント VM のポッドのプライベート IP アドレスとポート番号にマップする規則を作成します (応答トラフィックについてはその逆にマップする規則を作成します)。 

 Kubernetes サービス `type` を `LoadBalancer` に設定する方法を説明する&2; つの例を次に示します。 (例を試したら、不要な場合はデプロイを削除してください。)

### <a name="example-use-the-kubectl-expose-command"></a>例: `kubectl expose` コマンドを使用する 
[Kubernetes チュートリアル](container-service-kubernetes-walkthrough.md)には、`kubectl expose` コマンドとその `--type=LoadBalancer` フラグを使用してサービスを公開する方法の例が記載されています。 手順は次のようになります。

1. 新しいコンテナーのデプロイを開始します。 たとえば、次のコマンドによって `mynginx` という新しいデプロイが開始されます。 デプロイは、Web サーバー Nginx の Docker イメージに基づく&3; つのコンテナーで構成されます。

    ```console
    kubectl run mynginx --replicas=3 --image nginx
    ```
2. コンテナーが実行中であることを確認します。 たとえば、`kubectl get pods` を使用してコンテナーを照会すると、次のような出力が表示されます。

    ![Nginx コンテナーの取得](./media/container-service-kubernetes-load-balancing/nginx-get-pods.png)

3. ロード バランサーがデプロイに対する外部トラフィックを受け入れるように構成するには、`--type=LoadBalancer` を使用して `kubectl expose` を実行します。 次のコマンドでは、Nginx サーバーをポート 80 で公開します。

    ```console
    kubectl expose deployments mynginx --port=80 --type=LoadBalancer
    ```

4. `kubectl get svc` を入力して、クラスター内のサービスの状態を確認します。 ロード バランサーが規則を構成する間、サービスの `EXTERNAL-IP` が `<pending>` として表示されます。 数分後に、外部 IP アドレスが構成されます。 

    ![Azure Load Balancer の構成](./media/container-service-kubernetes-load-balancing/nginx-external-ip.png)

5. 外部の IP アドレスでサービスにアクセスできることを確認します。 たとえば、Web ブラウザーに表示された IP アドレスを入力して開きます。 いずれかのコンテナーで実行されている Nginx Web サーバーがブラウザーに表示されます。 または、`curl` か `wget` コマンドを実行します。 For example:

    ```
    curl 13.82.93.130
    ```

    次のような出力が表示されます。

    ![Curl を使用した Nginx へのアクセス](./media/container-service-kubernetes-load-balancing/curl-output.png)

6. Azure Load Balancer の構成を表示するには、[Azure Portal](https://portal.azure.com) に移動します。

7. Container Service クラスターのリソース グループを参照し、エージェント VM のロード バランサーを選択します。 ロード バランサーの名前は Container Service と同じである必要があります。 (**master-lb** を含む名前のマスター ノードのロード バランサーは選択しないでください。) 

    ![リソース グループのロード バランサー](./media/container-service-kubernetes-load-balancing/container-resource-group-portal.png)

8. ロード バランサーの構成の詳細を表示するには、**[負荷分散規則]** をクリックして構成した規則名をクリックします。

    ![負荷分散規則](./media/container-service-kubernetes-load-balancing/load-balancing-rules.png) 

### <a name="example-specify-type-loadbalancer-in-the-service-configuration-file"></a>例：サービス構成ファイルで `type: LoadBalancer` を指定する

Kubernetes コンテナー アプリを YAML または JSON [サービス構成ファイル](https://kubernetes.io/docs/user-guide/services/operations/#service-configuration-file)からデプロイする場合は、サービス仕様に次の行を追加して外部ロード バランサーを指定します。

```YAML
 "type": "LoadBalancer"
``` 



次の手順では Kubernetes の[ゲストブックの例](https://github.com/kubernetes/kubernetes/tree/master/examples/guestbook)を使用しています。 この例は、Redis および PHP の Docker イメージに基づく多層 Web アプリです。 サービス構成ファイルで、フロントエンドの PHP サーバーが Azure Load Balancer を使用するように指定できます。

1. [GitHub](https://github.com/kubernetes/kubernetes/tree/master/examples/guestbook/all-in-one) からファイル `guestbook-all-in-one.yaml` をダウンロードします。 
2. `frontend` サービスの `spec` を参照します。
3. `type: LoadBalancer` 行のコメントを解除します。

    ![サービス構成でのロード バランサー](./media/container-service-kubernetes-load-balancing/guestbook-frontend-loadbalance.png)

4. ファイルを保存してから、次のコマンドを実行してアプリをデプロイします。

    ```
    kubectl create -f guestbook-all-in-one.yaml
    ```

5. `kubectl get svc` を入力して、クラスター内のサービスの状態を確認します。 ロード バランサーが規則を構成する間、`frontend` サービスの `EXTERNAL-IP` が `<pending>` として表示されます。 数分後に、外部 IP アドレスが構成されます。 

    ![Azure Load Balancer の構成](./media/container-service-kubernetes-load-balancing/guestbook-external-ip.png)

6. 外部の IP アドレスでサービスにアクセスできることを確認します。 たとえば、Web ブラウザーにサービスの外部 IP アドレスを入力して開きます。

    ![外部からのゲストブックへのアクセス](./media/container-service-kubernetes-load-balancing/guestbook-web.png)

    ゲストブックのエントリを追加できます。

7. Azure Load Balancer の構成を表示するには、[Azure Portal](https://portal.azure.com) でクラスターのロード バランサーのリソースを参照します。 前の例の手順を参照してください。

### <a name="considerations"></a>考慮事項

* 負荷分散規則の作成は非同期的に発生し、プロビジョニング済みのバランサーに関する情報はサービスの `status.loadBalancer` フィールドに公開されます。
* すべてのサービスにはロード バランサーで独自の仮想 IP アドレスが自動的に割り当てられます。
* DNS 名でロード バランサーに接続する場合は、ドメイン サービス プロバイダーと連携して、規則の IP アドレス用の DNS 名を作成します。

## <a name="http-or-https-traffic"></a>HTTP または HTTPS トラフィック

コンテナーの Web アプリへの HTTP または HTTPS トラフィックの負荷を分散し、トランスポート層セキュリティ (TLS) の証明書を管理するには、Kubernetes の[イングレス](https://kubernetes.io/docs/user-guide/ingress/) リソースを使用します。 イングレスは、クラスター サービスに接続する受信接続を許可する規則を集めたものです。 イングレス リソースが機能するには、Kubernetes クラスターで[イングレス コントローラー](https://kubernetes.io/docs/user-guide/ingress/#ingress-controllers)が実行されている必要があります。

Azure Container Service では、Kubernetes イングレス コントローラーは自動的に実装されません。 いくつかのコントローラーの実装を利用できます。 現在、イングレス規則の構成と HTTP および HTTPS トラフィックの負荷分散には [Nginx イングレス コントローラー](https://github.com/kubernetes/ingress/tree/master/examples/deployment/nginx)をお勧めします。 

詳細については、[Nginx イングレス コントローラーのドキュメント](https://github.com/kubernetes/ingress/tree/master/controllers/nginx/README.md)を参照してください。

> [!IMPORTANT]
> Nginx イングレス コントローラーを Azure Container Service で使用する場合は、`type: LoadBalancer` を使ってコントローラーのデプロイをサービスとして公開する必要があります。 これにより、Azure Load Balancer がトラフィックをコントローラーにルーティングするように構成されます。 詳細については、前のセクションを参照してください。


## <a name="next-steps"></a>次のステップ

* [Kubernetes ロード バランサーのドキュメント](https://kubernetes.io/docs/user-guide/load-balancer/)を参照する
* [Kubernetes イングレスとイングレス コントローラー](https://kubernetes.io/docs/user-guide/ingress/)の詳細
* [Kubernetes の例](https://github.com/kubernetes/kubernetes/tree/master/examples)を参照する


