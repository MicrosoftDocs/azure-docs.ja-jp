---
title: Azure Kubernetes Service (AKS) の HTTP アプリケーション ルーティング アドオン
description: HTTP アプリケーション ルーティング アドオンを使用して、Azure Kubernetes Service (AKS) にデプロイされたアプリケーションにアクセスします。
services: container-service
author: lachie83
ms.topic: article
ms.date: 07/20/2020
ms.author: laevenso
ms.openlocfilehash: 25fc021a48e8936f242df35f7485fc59a93bba13
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172802"
---
# <a name="http-application-routing"></a>HTTP アプリケーション ルーティング

HTTP アプリケーション ルーティング ソリューションを使用すると、Azure Kubernetes Service (AKS) にデプロイされたアプリケーションに簡単にアクセスできます。 ソリューションを有効にすると、AKS クラスター内に[イングレス コントローラー](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/)が構成されます。 アプリケーションがデプロイされると、このソリューションは、アプリケーション エンドポイントのパブリックにアクセス可能な DNS 名も作成します。

アドオンを有効にすると、サブスクリプション内に DNS ゾーンが作成されます。 DNS のコストの詳細については、[DNS の価格][dns-pricing]に関するページを参照してください。

> [!CAUTION]
> HTTP アプリケーションのルーティング アドオンは、ユーザーがすばやくイングレス コントローラーを作成し、アプリケーションにアクセスできるように設計されています。 このアドオンは、現在、運用環境で使用できるように設計されていないため、運用環境で使用することはお勧めできません。 複数のレプリカと TLS のサポートを含む運用環境対応のイングレス デプロイについては、[HTTPS イングレス コントローラーの作成](./ingress-tls.md)に関するページを参照してください。

## <a name="http-routing-solution-overview"></a>HTTP のルーティング ソリューションの概要

アドオンによって、[Kubernetes イングレス コントローラー][ingress]と[外部 DNS][external-dns] コントローラーの 2 つのコンポーネントがデプロイされます。

- **イングレス コントローラー**:イングレス コントローラーは、LoadBalancer タイプの Kubernetes サービスを使用することで、インターネットに公開されます。 イングレス コントローラーは、アプリケーションのエンドポイントへのルートを作成する [Kubernetes イングレス リソース][ingress-resource]を監視し、実装します。
- **外部 DNS コントローラー**:Kubernetes イングレス リソースを監視し、クラスター固有の DNS ゾーンに DNS A レコードを作成します。

## <a name="deploy-http-routing-cli"></a>HTTP ルーティングをデプロイする:CLI

HTTP アプリケーションのルーティング アドオンは、AKS クラスターのデプロイ時に、Azure CLI を使用して有効にできます。 これを行うには、`--enable-addons` 引数を付けて [az aks create][az-aks-create] コマンドを使用します。

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

> [!TIP]
> 複数のアドオンを有効にする場合は、各アドオンをコンマで区切って指定してください。 たとえば、HTTP アプリケーションのルーティングと監視を有効にするには、`--enable-addons http_application_routing,monitoring` という形式を使用します。

また、[az aks enable-addons][az-aks-enable-addons] コマンドを使用して、既存の AKS クラスターで HTTP ルーティングを有効にすることもできます。 既存のクラスターで HTTP ルーティングを有効にするには、次の例のように `--addons` パラメーターを追加して *http_application_routing* を指定します。

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

クラスターのデプロイまたは更新が完了したら、[az aks show][az-aks-show] コマンドを使用して DNS ゾーン名を取得します。

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table
```

この名前は、アプリケーションを AKS クラスターにデプロイするのに必要であり、次の出力例に示されています。

```console
9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io
```

## <a name="deploy-http-routing-portal"></a>HTTP ルーティングをデプロイする:ポータル

HTTP アプリケーション ルーティング アドオンは、AKS クラスターをデプロイするときに、Azure portal から有効にできます。

![HTTP ルーティング機能を有効化する](media/http-routing/create.png)

クラスターがデプロイされたら、自動的に作成された AKS リソース グループに移動して、DNS ゾーンを選択します。 DNS ゾーンの名前を書き留めます。 この名前は、アプリケーションを AKS クラスターにデプロイするのに必要です。

![DNS ゾーン名を取得する](media/http-routing/dns.png)

## <a name="connect-to-your-aks-cluster"></a>ご利用の AKS クラスターに接続する

お使いのローカル コンピューターから Kubernetes クラスターに接続するには、[kubectl][kubectl] (Kubernetes コマンドライン クライアント) を使用します。

Azure Cloud Shell を使用している場合、`kubectl` は既にインストールされています。 [az aks install-cli][] コマンドを使用してローカルにインストールすることもできます。

```azurecli
az aks install-cli
```

Kubernetes クラスターに接続するように `kubectl` を構成するには、[az aks get-credentials][] コマンドを使用します。 次の例では、*MyResourceGroup* の *MyAKSCluster* という名前の AKS クラスターの資格情報を取得します。

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKSCluster
```

## <a name="use-http-routing"></a>HTTP ルーティングを使用する

HTTP アプリケーション ルーティング ソリューションは、次のように記述されたイングレス リソースに対してのみトリガーできます。

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

**samples-http-application-routing.yaml** という名前のファイルを作成し、次の YAML にコピーします。 43 行目の `<CLUSTER_SPECIFIC_DNS_ZONE>` を、この記事の前の手順で収集した DNS ゾーン名で更新します。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld  
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld  
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: aks-helloworld
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: aks-helloworld.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /
```

[kubectl apply][kubectl-apply] コマンドを使用してリソースを作成します。

```bash
kubectl apply -f samples-http-application-routing.yaml
```

次の例は、作成されたリソースを示しています。

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment.apps/aks-helloworld created
service/aks-helloworld created
ingress.networking.k8s.io/aks-helloworld created
```

Web ブラウザーで *aks-helloworld.\<CLUSTER_SPECIFIC_DNS_ZONE\>* (たとえば、*aks-helloworld.9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io*) を開き、デモ アプリケーションが表示されることを確認します。 アプリケーションが表示されるまでに数分かかることがあります。

## <a name="remove-http-routing"></a>HTTP ルーティングを削除する

HTTP ルーティング ソリューションは、Azure CLI を使用して削除することができます。 これを行うには、次のコマンドを実行します。AKS クラスターとリソース グループの名前は実際のものに置き換えてください。

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

HTTP アプリケーションのルーティング アドオンが無効になっている場合は、一部の Kubernetes リソースがクラスターに残ることがあります。 これらのリソースには *configMaps* と *secrets* が含まれ、*kube-system* 名前空間で作成されます。 クリーンなクラスターを維持するために、これらのリソースを削除することができます。

次の [kubectl get][kubectl-get] コマンドを使用して、*addon-http-application-routing* リソースを探します。

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

次の出力例は、削除する必要がある configMaps を示しています。

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

リソースを削除するには、[kubectl delete][kubectl-delete] コマンドを使用します。 リソースの種類、リソース名、および名前空間を指定します。 次の例では、以前の configmaps の 1 つを削除します。

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

クラスターに残っているすべての *addon-http-application-routing* リソースに対して、前の `kubectl delete` の手順を繰り返します。

## <a name="troubleshoot"></a>トラブルシューティング

[kubectl logs][kubectl-logs] コマンドを使用して、外部 DNS アプリケーションのアプリケーション ログを表示します。 A および TXT DNS レコードが正常に作成されたことをログで確認してください。

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'aks-helloworld' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'aks-helloworld' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

これらのレコードは、Azure Portal の DNS ゾーン リソースにも表示されます。

![DNS レコードを取得する](media/http-routing/clippy.png)

[kubectl logs][kubectl-logs] コマンドを使用して、Nginx イングレス コントローラーのアプリケーション ログを表示します。 イングレス リソースの `CREATE` と、コントローラーが再読み込みされたことをログで確認してください。 すべての HTTP アクティビティがログに記録されます。

```bash
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"aks-helloworld", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/aks-helloworld
W0426 21:51:57.908771       9 controller.go:775] service default/aks-helloworld does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-aks-helloworld-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="clean-up"></a>クリーンアップ

`kubectl delete` を使用して、この記事で作成された関連する Kubernetes オブジェクトを削除します。

```bash
kubectl delete -f samples-http-application-routing.yaml
```

出力例は、Kubernetes オブジェクトが削除されたことを示しています。

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "aks-helloworld" deleted
service "aks-helloworld" deleted
ingress "aks-helloworld" deleted
```

## <a name="next-steps"></a>次のステップ

セキュリティ保護された HTTPS イングレス コントローラーを AKS にインストールする方法については、「[Azure Kubernetes Service (AKS) での HTTPS イングレス][ingress-https]」を参照してください。

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-show]: /cli/azure/aks#az-aks-show
[ingress-https]: ./ingress-tls.md
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials

<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource
