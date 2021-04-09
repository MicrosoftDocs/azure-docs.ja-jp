---
title: 内部ネットワーク上のイングレス コントローラー
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) クラスターで内部のプライベート ネットワーク用の NGINX イングレス コントローラーをインストールして構成する方法を説明します。
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 3201f510db9970b7db548ee6a3348fa68d278248
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601467"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で内部の仮想ネットワークにイングレス コントローラーを作成する

イングレス コントローラーは、リバース プロキシ、構成可能なトラフィック ルーティング、および Kubernetes サービスの TLS 終端を提供するソフトウェアです。 個別の Kubernetes サービスのイングレス ルールとルートを構成するには、Kubernetes イングレス リソースが使われます。 イングレス コントローラーとイングレス ルールを使用すれば、1 つの IP アドレスで Kubernetes クラスター内の複数のサービスにトラフィックをルーティングできます。

この記事では、Azure Kubernetes Service (AKS) クラスターに [NGINX イングレス コントローラー][nginx-ingress]を展開する方法について説明します。 イングレス コント ローラーは、内部のプライベート仮想ネットワークと IP アドレスで構成されます。 外部アクセスは許可されません。 続いて、それぞれが 1 つの IP アドレスでアクセスできる、2 つのアプリケーションを AKS クラスターで実行します。

次のこともできます。

- [外部のネットワーク接続を使用して基本的なイングレス コントローラーを作成する][aks-ingress-basic]
- [HTTP アプリケーションのルーティング アドオンを有効にする][aks-http-app-routing]
- [ご自身の TLS 証明書を使用するイングレス コントローラーを作成する][aks-ingress-own-tls]
- Let's Encrypt を使用して、[動的パブリック IP アドレスを指定][aks-ingress-tls]または[静的パブリック IP アドレスを指定][aks-ingress-static-tls]して TLS 証明書を自動的に作成する、イングレス コントローラーを作成する

## <a name="before-you-begin"></a>開始する前に

この記事では、[Helm 3][helm] を使用し、NGINX イングレス コントローラーをインストールします。 最新リリースの Helm を使用していること、および *ingress-nginx* の Helm リポジトリにアクセスできることを確認します。 Helm の構成および使用方法の詳細については、「[Azure Kubernetes Service (AKS) での Helm を使用したアプリケーションのインストール][use-helm]」を参照してください。

この記事ではまた、Azure CLI バージョン 2.0.64 以降を実行していることも必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="create-an-ingress-controller"></a>イングレス コントローラーを作成する

既定では、NGINX イングレス コントローラーは動的パブリック IP アドレスの割り当てによって作成されます。 一般的な構成要件は、内部のプライベート ネットワークと IP アドレスを使用することです。 この手法では、外部アクセスなしで、サービスへのアクセスを内部ユーザーに制限できます。

次の例のマニフェスト ファイルを使用して、*internal-ingress.yaml* という名前のファイルを作成します。 この例では、*loadBalancerIP* リソースに *10.240.0.42* を割り当てます。 イングレス コント ローラーで使用する場合は、独自の内部 IP アドレスを指定します。 この IP アドレスが仮想ネットワーク内でまだ使用されていないことを確認します。 また、既存の仮想ネットワークとサブネットを使用している場合は、適切なアクセス許可を使用して AKS クラスターを構成し、仮想ネットワークとサブネットを管理する必要もあります。 詳細については、「[Azure Kubernetes Service (AKS) の独自の IP アドレス範囲で kubenet ネットワークを使用する][aks-configure-kubenet-networking]」または「[Azure Kubernetes サービス (AKS) で Azure CNI ネットワークを構成する][aks-configure-advanced-networking]」を参照してください。

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

次に、Helm を使用して *nginx-ingress* グラフをデプロイします。 前の手順で作成されたマニフェスト ファイルを使用するには、次の `-f internal-ingress.yaml` パラメーターを追加します。 追加された冗長性については、NGINX イングレス コントローラーの 2 つのレプリカが `--set controller.replicaCount` パラメーターでデプロイされています。 イングレス コントローラーのレプリカの実行から十分にメリットを享受するには、AKS クラスターに複数のノードが存在していることを確認します。

イングレス コントローラーも Linux ノード上でスケジュールする必要があります。 Windows Server ノードでは、イングレス コントローラーを実行しないでください。 ノード セレクターは、`--set nodeSelector` パラメーターを使用して指定され、Linux ベース ノード上で NGINX イングレス コントローラーを実行するように Kubernetes スケジューラに指示されます。

> [!TIP]
> 次の例では、*ingress-basic* という名前のイングレス リソースの Kubernetes 名前空間が作成されます。 必要に応じて、ご自身の環境の名前空間を指定できます。 AKS クラスターが Kubernetes RBAC 対応でない場合は、Helm コマンドに `--set rbac.create=false` を追加してください。

> [!TIP]
> クラスター内のコンテナーへの要求で[クライアント ソース IP の保持][client-source-ip]を有効にする場合は、Helm インストール コマンドに `--set controller.service.externalTrafficPolicy=Local` を追加します。 クライアント ソース IP が要求ヘッダーの *X-Forwarded-For* の下に格納されます。 クライアント ソース IP の保持が有効になっているイングレス コントローラーを使用する場合、TLS パススルーは機能しません。

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

NGINX イングレス コントローラー用の Kubernetes ロード バランサー サービスが作成されると、内部 IP アドレスが割り当てられます。 パブリック IP アドレスを取得するには、`kubectl get service` コマンドを使います。

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

次の出力例に示すように、IP アドレスがサービスに割り当てられるまでに、少し時間がかかる場合があります。

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

イングレス ルールはまだ作成されていないため、内部 IP アドレスを参照すると、NGINX イングレス コントローラーの既定の 404 ページが表示されます。 イングレス ルールは、後続の手順で構成します。

## <a name="run-demo-applications"></a>デモ アプリケーションを実行する

動作中のイングレス コントローラーを確認するには、AKS クラスターで 2 つのデモ アプリケーションを実行します。 この例では、`kubectl apply` を使って、シンプルな *Hello world* アプリケーションのインスタンスを 2 つデプロイします。

*aks-helloworld.yaml* ファイルを作成し、次の例のように YAML にコピーします。

```yml
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
```

*ingress-demo.yaml* ファイルを作成し、次の例のように YAML にコピーします。

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

`kubectl apply` を使用して、次の 2 つのデモ アプリケーションを実行します。

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>イングレス ルートを作成する

両方のアプリケーションは、Kubernetes クラスターで実行するようになります。 各アプリケーションにトラフィックをルーティングするには、Kubernetes イングレス リソースを作成します。 イングレス リソースでは、2 つのアプリケーションのいずれかにトラフィックをルーティングするルールを構成します。

次の例では、アドレス `http://10.240.0.42/` へのトラフィックが `aks-helloworld` という名前のサービスにルーティングされることに注意してください。 アドレス `http://10.240.0.42/hello-world-two` へのトラフィックは、`ingress-demo` サービスにルーティングされます。

`hello-world-ingress.yaml` という名前のファイルを作成し、次の例の YAML 内にコピーします。

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
```

`kubectl apply -f hello-world-ingress.yaml` コマンドを使用してイングレス リソースを作成します。

```console
kubectl apply -f hello-world-ingress.yaml
```

次の出力例は、イングレス リソースが作成されたことを示しています。

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>イングレス コントローラーをテストする

イングレス コント ローラーに対してルートをテストするには、Web クライアントで 2 つのアプリケーションを参照します。 必要な場合は、AKS クラスター上のポッドから、この内部専用機能をすぐにテストできます。 テスト ポッドを作成し、それにターミナル セッションをアタッチします。

```console
kubectl run -it --rm aks-ingress-test --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --namespace ingress-basic
```

`apt-get` を使用して、`curl` をポッドにインストールします。

```console
apt-get update && apt-get install -y curl
```

`curl` を使用して、 *http://10.240.0.42* などの Kubernetes イングレス コントローラーのアドレスに今すぐアクセスします。 この記事の最初の手順でイングレス コントローラーを配備したときに指定した、独自の内部 IP アドレスを指定します。

```console
curl -L http://10.240.0.42
```

アドレスには追加のパスは指定されなかったため、イングレス コントローラーは既定の */* ルートに設定されます。 次の簡約された出力例に示すように、最初のデモ アプリケーションが返されます。

```
$ curl -L http://10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

ここで *http://10.240.0.42/hello-world-two* などの */hello-world-two* パスをアドレスに追加します。 次の簡約された出力例に示すように、カスタム タイトル付きの 2 番目のデモ アプリケーションが返されます。

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事では、Helm を使用して、イングレス コンポーネントをインストールしました。 Helm グラフをデプロイすると、多数の Kubernetes リソースが作成されます。 これらのリソースには、ポッド、デプロイ、およびサービスが含まれます。 これらのリソースをクリーンアップするには、サンプルの名前空間全体を削除するか、またはリソースを個々に削除します。

### <a name="delete-the-sample-namespace-and-all-resources"></a>サンプルの名前空間とすべてのリソースを削除する

サンプルの名前空間全体を削除するには、`kubectl delete` コマンドを使用して、名前空間の名前を指定します。 名前空間内のすべてのリソースが削除されます。

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>リソースを個々に削除する

作成したリソースを個々に削除するという、きめ細かな方法もあります。 `helm list` コマンドを使用して、Helm リリースを一覧表示します。 

```console
helm list --namespace ingress-basic
```

次の出力例に示すように、*nginx-ingress* および *aks-helloworld* という名前のグラフを探します。

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

`helm uninstall` コマンドを使用してそれらのリリースをアンインストールします。

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

次の例では、NGINX イングレスのデプロイをアンインストールします。

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

次に、2 つのサンプル アプリケーションを削除します。

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

サンプル アプリにトラフィックを送信していたイングレス ルートを削除します。

```console
kubectl delete -f hello-world-ingress.yaml
```

最後に、名前空間自体を削除します。 `kubectl delete` コマンドを使用して、名前空間の名前を指定します。

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>次のステップ

この記事には、AKS への外部コンポーネントが含まれています。 これらのコンポーネントの詳細については、次のプロジェクト ページを参照してください。

- [Helm CLI][helm-cli]
- [NGINX イングレス コントローラー][nginx-ingress]

次のこともできます。

- [外部のネットワーク接続を使用して基本的なイングレス コントローラーを作成する][aks-ingress-basic]
- [HTTP アプリケーションのルーティング アドオンを有効にする][aks-http-app-routing]
- [動的パブリック IP アドレスを使用してイングレス コントローラーを作成し、Let's Encrypt を構成して TLS 証明書を自動的に生成する][aks-ingress-tls]
- [静的パブリック IP アドレスを使用してイングレス コントローラーを作成し、Let's Encrypt を構成して TLS 証明書を自動的に生成する][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md