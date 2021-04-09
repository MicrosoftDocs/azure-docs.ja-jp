---
title: 静的 IP でイングレス コントローラーを使用する
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) クラスターの静的パブリック IP アドレスを使用して NGINX イングレス コントローラーをインストールして構成する方法を説明します。
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: fa6572ddc694cb892f48cb3e618c176f087524f6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506567"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) の静的パブリック IP アドレスを使用してイングレス コントローラーを作成する

イングレス コントローラーは、リバース プロキシ、構成可能なトラフィック ルーティング、および Kubernetes サービスの TLS 終端を提供するソフトウェアです。 個別の Kubernetes サービスのイングレス ルールとルートを構成するには、Kubernetes イングレス リソースが使われます。 イングレス コントローラーとイングレス ルールを使用すれば、1 つの IP アドレスで Kubernetes クラスター内の複数のサービスにトラフィックをルーティングできます。

この記事では、Azure Kubernetes Service (AKS) クラスターに [NGINX イングレス コントローラー][nginx-ingress]を展開する方法について説明します。 イングレス コントローラーは、静的パブリック IP アドレスを使用して構成します。 [Let's Encrypt][lets-encrypt] 証明書を自動的に生成して構成するには、[cert-manager][cert-manager] プロジェクトを使用します。 最後に、それぞれが 1 つの IP アドレスでアクセスできる、2 つのアプリケーションを AKS クラスターで実行します。

次のこともできます。

- [外部のネットワーク接続を使用して基本的なイングレス コントローラーを作成する][aks-ingress-basic]
- [HTTP アプリケーションのルーティング アドオンを有効にする][aks-http-app-routing]
- [ご自身の TLS 証明書を使用するイングレス コントローラーを作成する][aks-ingress-own-tls]
- [Let's Encrypt を使用して動的パブリック IP アドレス付きの TLS 証明書を自動的に生成するイングレス コントローラーを作成する][aks-ingress-tls]

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

この記事では、[Helm 3][helm] を使用して、NGINX イングレス コントローラーおよび cert-manager をインストールします。 最新リリースの Helm を使用しており、*ingress-nginx* および *jetstack* の Helm リポジトリにアクセスできることを確認します。 アップグレード手順については、[Helm のインストール ドキュメント][helm-install]を参照してください。Helm の構成および使用方法の詳細については、「[Azure Kubernetes Service (AKS) での Helm を使用したアプリケーションのインストール][use-helm]」を参照してください。

この記事ではまた、Azure CLI バージョン 2.0.64 以降を実行していることも必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="create-an-ingress-controller"></a>イングレス コントローラーを作成する

既定では、NGINX イングレス コントローラーは新しいパブリック IP アドレスの割り当てによって作成されます。 このパブリック IP アドレスは、イングレス コントローラーの有効期間のみ静的で、コントローラーを削除して再作成すると失われます。 一般的な構成要件は、NGINX イングレス コントローラーに既存の静的パブリック IP アドレスを提供することです。 イングレス コントローラーが削除されても、静的パブリック IP アドレスはそのまま残ります。 このアプローチでは、アプリケーションの有効期間を通じて一貫した方法で既存の DNS レコードとネットワーク構成を使用できます。

静的パブリック IP アドレスを作成する必要がある場合は、まず [az aks show][az-aks-show] コマンドを使用して AKS クラスターのリソース グループ名を取得します。

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

次に、[az network public-ip create][az-network-public-ip-create] コマンドを使用して、*static* 割り当て方式を使用してパブリック IP アドレスを作成します。 次の例では、前の手順で取得した AKS クラスター リソース グループに *myAKSPublicIP* という名前のパブリック IP アドレスを作成します。

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

> [!NOTE]
> 上記のコマンドでは、AKS クラスターを削除すると削除される IP アドレスが作成されます。 または、AKS クラスターとは別に管理できる別のリソース グループに IP アドレスを作成することもできます。 別のリソース グループに IP アドレスを作成する場合は、AKS クラスターによって使用されるクラスター ID で、アクセス許可が他のリソース グループ ("*ネットワーク共同作成者*" など) に委任されていることを確認します。 詳細については、「[Azure Kubernetes Service (AKS) ロード バランサーで静的パブリック IP アドレスと DNS ラベルを使用する][aks-static-ip]」を参照してください。

次に、Helm を使用して *nginx-ingress* グラフをデプロイします。 追加された冗長性については、NGINX イングレス コントローラーの 2 つのレプリカが `--set controller.replicaCount` パラメーターでデプロイされています。 イングレス コントローラーのレプリカの実行から十分にメリットを享受するには、AKS クラスターに複数のノードが存在していることを確認します。

イングレス コントローラー サービスに割り当てられるロード バランサーの静的 IP アドレスと、パブリック IP アドレス リソースに適用されている DNS 名ラベルの両方をイングレス コントローラーが認識できるように、2 つの追加パラメーターを Helm リリースに渡す必要があります。 HTTPS 証明書が正常に動作するには、DNS 名ラベルを使用して、イングレス コントローラーの IP アドレス向けに FQDN を構成します。

1. `--set controller.service.loadBalancerIP`パラメーターを追加します。 前の手順で作成された独自のパブリック IP アドレスを指定します。
1. `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"`パラメーターを追加します。 前の手順で作成されたパブリック IP アドレスに適用する DNS 名ラベルを指定します。

イングレス コントローラーも Linux ノード上でスケジュールする必要があります。 Windows Server ノードでは、イングレス コントローラーを実行しないでください。 ノード セレクターは、`--set nodeSelector` パラメーターを使用して指定され、Linux ベース ノード上で NGINX イングレス コントローラーを実行するように Kubernetes スケジューラに指示されます。

> [!TIP]
> 次の例では、*ingress-basic* という名前のイングレス リソースの Kubernetes 名前空間が作成されます。 必要に応じて、ご自身の環境の名前空間を指定できます。 AKS クラスターが Kubernetes RBAC 対応でない場合は、Helm コマンドに `--set rbac.create=false` を追加してください。

> [!TIP]
> クラスター内のコンテナーへの要求で[クライアント ソース IP の保持][client-source-ip]を有効にする場合は、Helm インストール コマンドに `--set controller.service.externalTrafficPolicy=Local` を追加します。 クライアント ソース IP が要求ヘッダーの *X-Forwarded-For* の下に格納されます。 クライアント ソース IP の保持が有効になっているイングレス コントローラーを使用する場合、TLS パススルーは機能しません。

イングレス コントローラーの **IP アドレス** と、FQDN プレフィックスで使用する **一意の名前** で以下のスクリプトを更新します。

> [!IMPORTANT]
> コマンドの実行時に、*STATIC_IP* と *DNS_LABEL* を実際の IP アドレスと一意の名前に置き換える必要があります。

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="STATIC_IP" \
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="DNS_LABEL"
```

次の出力例に示すように、NGINX イングレス コントローラー用の Kubernetes ロード バランサー サービスが作成されると、静的 IP アドレスが割り当てられます。

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

イングレス ルールはまだ作成されていないため、パブリック IP アドレスを参照すると、NGINX イングレス コントローラーの既定の 404 ページが表示されます。 イングレス ルールは、後続の手順で構成します。

次のようにパブリック IP アドレスの FQDN を照会すると、DNS 名ラベルが適用されていることを確認できます。

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query "[?ipAddress=='myAKSPublicIP'].[dnsSettings.fqdn]" -o tsv
```

これでイングレス コントローラーは IP アドレスまたは FQDN 経由でアクセスできるようになります。

## <a name="install-cert-manager"></a>cert-manager をインストールする

NGINX イングレス コントローラーは、TLS の終端をサポートしています。 HTTPS の証明書を取得および構成するには、いくつかの方法があります。 この記事では、[Lets Encrypt][lets-encrypt] 証明書を自動的に作成および管理する機能を提供する [cert-manager][cert-manager] の使用方法を説明します。

> [!NOTE]
> この記事では、Let's Encrypt に `staging` 環境を使用します。 運用環境の展開では、リソースの定義と Helm チャートのインストールに `letsencrypt-prod` と `https://acme-v02.api.letsencrypt.org/directory` を使用します。

Kubernetes RBAC が有効になっているクラスターに cert-manager コントローラーをインストールするには、次の `helm install` コマンドを使用します。

```console
# Label the cert-manager namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.16.1 \
  --set installCRDs=true \
  --set nodeSelector."beta\.kubernetes\.io/os"=linux \
  jetstack/cert-manager
```

cert-manager の構成の詳細については、[cert-manager プロジェクト][cert-manager]についてのページを参照してください。

## <a name="create-a-ca-cluster-issuer"></a>CA クラスター発行者を作成する

証明書を発行する前に、cert-manager には [Issuer][cert-manager-issuer] リソースまたは [ClusterIssuer][cert-manager-cluster-issuer] リソースが必要です。 これらの Kubernetes リソースは機能面では同一ですが、`Issuer` は単一の名前空間で機能し、`ClusterIssuer` はすべての名前空間にわたって機能します。 詳細については、[cert-manager issuer][cert-manager-issuer] についてのページを参照してください。

次のマニフェスト例を使用して、`cluster-issuer.yaml` などのクラスター発行者を作成します。 メール アドレスを実際の組織の有効なアドレスで置き換えてください。

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

発行者を作成するには、`kubectl apply` コマンドを使用します。

```
kubectl apply -f cluster-issuer.yaml --namespace ingress-basic
```

出力は次の例のようになります。

```
clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>デモ アプリケーションを実行する

イングレス コントローラーと証明書管理ソリューションを構成しました。 ここでは、AKS クラスターで 2 つのデモ アプリケーションを実行します。 この例では、Helm を使って、単純な 'Hello world' アプリケーションのインスタンスを 2 つ実行します。

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

両方のアプリケーションが Kubernetes クラスターで実行されるようになりましたが、構成されているサービスの種類は `ClusterIP` です。 そのため、アプリケーションにはインターネットからアクセスできません。 公開するには、Kubernetes イングレス リソースを作成します。 イングレス リソースでは、2 つのアプリケーションのいずれかにトラフィックをルーティングするルールを構成します。

次の例では、アドレス `https://demo-aks-ingress.eastus.cloudapp.azure.com/` へのトラフィックが `aks-helloworld` という名前のサービスにルーティングされることに注意してください。 アドレス `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` へのトラフィックは、`ingress-demo` サービスにルーティングされます。 *hosts* と *host* を前の手順で作成した DNS 名に更新します。

`hello-world-ingress.yaml` という名前のファイルを作成し、次の例の YAML 内にコピーします。

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
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

`kubectl apply` コマンドを使用してイングレス リソースを作成します。

```
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

出力は次の例のようになります。

```
ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>証明書オブジェクトを作成する

次に、証明書リソースを作成する必要があります。 証明書リソースでは、必要な X.509 証明書を定義します。 詳細については、[cert-manager の証明書][cert-manager-certificates]についてのページを参照してください。

証明書マネージャーによって ingress-shim を使用して証明書オブジェクトが自動的に作成されている可能性があり、それは v0.2.2 以降の証明書マネージャーで自動的にデプロイされます。 詳しくは、[ingress-shim のドキュメント][ingress-shim]をご覧ください。

証明書が正常に作成されたことを確認するには、`kubectl describe certificate tls-secret --namespace ingress-basic` コマンドを使用します。

証明書が発行された場合、次のような出力が表示されます。
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

追加の証明書リソースを作成する必要がある場合は、次のマニフェストの例で行うことができます。 *dnsNames* と *domains* を前の手順で作成した DNS 名に更新します。 内部専用のイングレス コントローラーを使用する場合は、サービスに内部 DNS 名を指定します。

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

証明書のリソースを作成するには、`kubectl apply` コマンドを使用します。

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>イングレスの構成をテストする

Web ブラウザーで、 *`https://demo-aks-ingress.eastus.cloudapp.azure.com`* などの Kubernetes イングレス コントローラーの FQDN コントローラーを開きます。

これらの例は、`letsencrypt-staging` を使用するので、発行された TLS または SSL 証明書はブラウザーでは信頼されていません。 アプリケーションに続けるために警告のプロンプトを受け入れます。 証明書情報には、この *Fake LE Intermediate X1* 証明書が Let's Encrypt によって発行されていると表示されます。 この偽の証明書は、`cert-manager` が要求を正しく処理し、プロバイダーから証明書を受け取ったことを示します。

![Let's Encrypt ステージング証明書](media/ingress/staging-certificate.png)

Let's Encrypt を変更し、`staging` ではなく `prod` が使用されるように変更した場合、Let's Encrypt によって発行された信頼された証明書が次の例のように使用されます。

![Let's Encrypt の証明書](media/ingress/certificate.png)

Web ブラウザーには、アプリケーションのデモが表示されています。

![アプリケーションの例 1](media/ingress/app-one.png)

ここで *`https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`* などの */hello-world-two* パスを FQDN に追加します。 カスタム タイトルが表示された 2 番目のデモ アプリケーションが表示されています。

![アプリケーションの例 2](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事では、Helm を使用して、イングレス コンポーネント、証明書、およびサンプル アプリをインストールしました。 Helm グラフをデプロイすると、多数の Kubernetes リソースが作成されます。 これらのリソースには、ポッド、デプロイ、およびサービスが含まれます。 これらのリソースをクリーンアップするには、サンプルの名前空間全体を削除するか、またはリソースを個々に削除します。

### <a name="delete-the-sample-namespace-and-all-resources"></a>サンプルの名前空間とすべてのリソースを削除する

サンプルの名前空間全体を削除するには、`kubectl delete` コマンドを使用して、名前空間の名前を指定します。 名前空間内のすべてのリソースが削除されます。

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>リソースを個々に削除する

作成したリソースを個々に削除するという、きめ細かな方法もあります。 最初に証明書リソースを削除します。

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

次に、`helm list` コマンドで Helm リリースを一覧表示します。 次の出力例に示すように、*nginx-ingress* と *cert-manager* という名前のグラフを探します。

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic   1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

`helm uninstall` コマンドを使用してそれらのリリースをアンインストールします。 次の例では、NGINX イングレスのデプロイと証明書マネージャーのデプロイをアンインストールします。

```
$ helm uninstall nginx-ingress cert-manager -n ingress-basic

release "nginx-ingress" deleted
release "cert-manager" deleted
```

次に、2 つのサンプル アプリケーションを削除します。

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

名前空間自体を削除します。 `kubectl delete` コマンドを使用して、名前空間の名前を指定します。

```console
kubectl delete namespace ingress-basic
```

最後に、イングレス コントローラー用に作成した静的パブリック IP アドレスを削除します。 *MC_myResourceGroup_myAKSCluster_eastus* など、この記事の最初の手順で取得した *MC_* クラスター リソース グループ名を指定します。

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>次のステップ

この記事には、AKS への外部コンポーネントが含まれています。 これらのコンポーネントの詳細については、次のプロジェクト ページを参照してください。

- [Helm CLI][helm-cli]
- [NGINX イングレス コントローラー][nginx-ingress]
- [cert-manager][cert-manager]

次のこともできます。

- [外部のネットワーク接続を使用して基本的なイングレス コントローラーを作成する][aks-ingress-basic]
- [HTTP アプリケーションのルーティング アドオンを有効にする][aks-http-app-routing]
- [内部のプライベート ネットワークと IP アドレスを使用するイングレス コントローラーを作成する][aks-ingress-internal]
- [ご自身の TLS 証明書を使用するイングレス コントローラーを作成する][aks-ingress-own-tls]
- [動的パブリック IP アドレスを使用してイングレス コントローラーを作成し、Let's Encrypt を構成して TLS 証明書を自動的に生成する][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-static-ip]: static-ip.md
