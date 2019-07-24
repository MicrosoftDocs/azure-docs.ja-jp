---
title: Azure Kubernetes Service (AKS) クラスターを使用したイングレス用の独自の TLS 証明書の使用
description: Azure Kubernetes Service (AKS) クラスターで独自の証明書を使用する NGINX イングレス コントローラーをインストールして構成する方法を説明します。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/23/2018
ms.author: iainfou
ms.openlocfilehash: c109febc90c9dd8d9b17489c9e612f677695bd25
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2019
ms.locfileid: "55727094"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で HTTPS イングレス コントローラーを作成し、独自の TLS 証明書を使用する

イングレス コントローラーは、リバース プロキシ、構成可能なトラフィック ルーティング、および Kubernetes サービスの TLS 終端を提供するソフトウェアです。 個別の Kubernetes サービスのイングレス ルールとルートを構成するには、Kubernetes イングレス リソースが使われます。 イングレス コントローラーとイングレス ルールを使用すれば、1 つの IP アドレスで Kubernetes クラスター内の複数のサービスにトラフィックをルーティングできます。

この記事では、Azure Kubernetes Service (AKS) クラスターに [NGINX イングレス コントローラー][nginx-ingress]を展開する方法について説明します。 独自の証明書を生成し、イングレス ルートで使用する Kubernetes シークレットを作成します。 最後に、それぞれが 1 つの IP アドレスでアクセスできる、2 つのアプリケーションを AKS クラスターで実行します。

さらに、以下を実行できます。

- [外部のネットワーク接続を使用して基本的なイングレス コントローラーを作成する][aks-ingress-basic]
- [HTTP アプリケーションのルーティング アドオンを有効にする][aks-http-app-routing]
- [内部のプライベート ネットワークと IP アドレスを使用するイングレス コントローラーを作成する][aks-ingress-internal]
- Let's Encrypt を使用して、[動的パブリック IP アドレスを使用][aks-ingress-tls]または[静的パブリック IP アドレスを使用][aks-ingress-static-tls]して TLS 証明書を自動的に作成する、イングレス コントローラーを作成する

## <a name="before-you-begin"></a>開始する前に

この記事では、Helm を使用し、NGINX イングレス コントローラーおよびサンプル Web アプリをインストールします。 Helm は、AKS クラスター内で初期化され、Tiller 用のサービス アカウントが使用されている必要があります。 最新リリースの Helm を使用していることを確認します。 アップグレード手順については、「[Helm のインストール ドキュメント][helm-install]」を参照してください。Helm の構成および使用方法の詳細については、「[Azure Kubernetes Service (AKS) での Helm を使用したアプリケーションのインストール][use-helm]」を参照してください。

この記事では、Azure CLI バージョン 2.0.41 以降も実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="create-an-ingress-controller"></a>イングレス コントローラーを作成する

イングレス コントローラーを作成するには、`Helm` を使用して *nginx-ingress* をインストールします。 追加された冗長性については、NGINX イングレス コントローラーの 2 つのレプリカが `--set controller.replicaCount` パラメーターでデプロイされています。 イングレス コントローラーのレプリカの実行から十分にメリットを享受するには、AKS クラスターに複数のノードが存在していることを確認します。

> [!TIP]
> 次の例では、`kube-system` の名前空間にイングレス コントローラーをインストールします。 必要に応じて、ご利用の環境に別の名前空間を指定できます。 AKS クラスターが RBAC 対応でない場合は、コマンドに `--set rbac.create=false` を追加します。

```console
helm install stable/nginx-ingress --namespace kube-system --set controller.replicaCount=2
```

インストールの間に、Azure パブリック IP アドレスがイングレス コントローラーに対して作成されます。 このパブリック IP アドレスは、イングレス コントローラーが存続している間は静的です。 イングレス コントローラーを削除すると、パブリック IP アドレスの割り当てが失われます。 続いてさらに別のイングレス コントローラーを作成すると、新しいパブリック IP アドレスが割り当てられます。 パブリック IP アドレスを使用し続けることを望む場合は、代わりに[静的パブリック IP アドレス][aks-ingress-static-tls]を使用してイングレス コントローラーを作成できます。

パブリック IP アドレスを取得するには、`kubectl get service` コマンドを使います。 IP アドレスがサービスに割り当てられるまでに、少し時間がかかる場合があります。

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

このパブリック IP アドレスをメモしておきます。これは、最後の手順でデプロイをテストするために使用します。

まだイングレス ルールは作成されていません。 パブリック IP アドレスを参照すると、NGINX イングレス コントローラーの既定の 404 ページが表示されます。

## <a name="generate-tls-certificates"></a>TLS 証明書を生成する

この記事では、`openssl` を使用して自己署名証明書を生成します。 運用環境で使用する場合は、プロバイダーまたは独自の証明機関 (CA) からの信頼された署名証明書を要求する必要があります。 次の手順では、TLS 証明書と OpenSSL によって生成された秘密キーを使用して、Kubernetes *シークレット*を生成します。

次の例では、365 日間有効な *aks-ingress-tls.crt* という名前の 2048 ビット RSA X509 証明書を作成します。 秘密キー ファイルの名前は *aks-ingress-tls.key* です。 Kubernetes の TLS シークレットには、これらの両方のファイルが必要です。

この記事では *demo.azure.com* サブジェクトの共通名を使用していて、変更する必要はありません。 運用環境で使用する場合は、`-subj` パラメーターに独自の組織の値を指定します。

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>TLS 証明書の Kubernetes シークレットを作成する

Kubernetes でイングレス コントローラーの TLS 証明書と秘密キーを使用できるようにするには、シークレットを作成して使用します。 シークレットは 1 回だけ定義し、前の手順で作成した証明書とキー ファイルを使用します。 その後、イングレス ルートを定義するときにこのシークレットを参照します。

次の例では、*aks-ingress-tls* という名前のシークレットを作成します。

```console
kubectl create secret tls aks-ingress-tls \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>デモ アプリケーションを実行する

証明書でイングレス コントローラーとシークレットが構成されました。 ここでは、AKS クラスターで 2 つのデモ アプリケーションを実行します。 この例では、Helm を使って、単純な 'Hello world' アプリケーションのインスタンスを 2 つ実行します。

サンプルの Helm チャートをインストールする前に、次のように Helm 環境に Azure サンプル リポジトリを追加します。

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

次のコマンドを使用して、Helm チャートから最初のデモ アプリケーションを作成します。

```console
helm install azure-samples/aks-helloworld
```

デモ アプリケーションの 2 番目のインスタンスをインストールします。 2 番目のインスタンスでは、2 つのアプリケーションを見た目で区別できるように、新しいタイトルを指定します。 また、一意サービス名を指定する必要があります。

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>イングレス ルートを作成する

両方のアプリケーションが Kubernetes クラスターで実行されるようになりましたが、構成されているサービスの種類は `ClusterIP` です。 そのため、アプリケーションにはインターネットからアクセスできません。 公開するには、Kubernetes イングレス リソースを作成します。 イングレス リソースでは、2 つのアプリケーションのいずれかにトラフィックをルーティングするルールを構成します。

次の例では、アドレス `https://demo.azure.com/` へのトラフィックが `aks-helloworld` という名前のサービスにルーティングされることに注意してください。 アドレス `https://demo.azure.com/hello-world-two` へのトラフィックは、`ingress-demo` サービスにルーティングされます。 この記事では、これらのデモのホスト名を変更する必要はありません。 運用環境で使用する場合は、証明書の要求および生成プロセスの一環として指定した名前を指定します。

> [!TIP]
> 証明書の要求プロセスの際に指定したホスト名 (CN 名) が、イングレス ルートに定義されているホストに一致しない場合、イングレス コントローラーには *Kubernetes イングレス コントローラーの偽の証明書*が表示されます。 証明書とイングレス ルートのホスト名が一致することを確認します。

*ｔls* セクションは、ホスト *demo.azure.com* の *aks-ingress-tls* という名前のシークレットを使用することをイングレス ルートに伝えます。 ここでも、運用環境で使用する場合は、独自のホスト アドレスを指定します。

`hello-world-ingress.yaml` という名前のファイルを作成し、次の例の YAML 内にコピーします。

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

`kubectl apply -f hello-world-ingress.yaml` コマンドを使用してイングレス リソースを作成します。

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>イングレスの構成をテストする

偽の *demo.azure.com* ホストで証明書をテストするには、`curl` を使用し、*--resolve* パラメーターを指定します。 このパラメーターを使用すると、*demo.azure.com* という名前をイングレス コントローラーのパブリック IP アドレスにマッピングできます。 次の例で示すように、独自のイングレス コントローラーのパブリック IP アドレスを指定します。

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

アドレスには追加のパスは指定されなかったため、イングレス コントローラーは既定の */* ルートに設定されます。 次の簡約された出力例に示すように、最初のデモ アプリケーションが返されます。

```
$ curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

`curl` コマンドの *-v* パラメーターにより、受信した TLS 証明書などの詳細な情報が出力されます。 curl の出力の途中で、独自の TLS 証明書が使用されたことを確認できます。 *-k* パラメーターにより、自己署名証明書を使用している場合でも、引き続きページが読み込まれます。 次の例は、*issuer:CN=demo.azure.com; O=aks-ingress-tls* 証明書が使用されたことを示しています。

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

ここで `https://demo.azure.com/hello-world-two` などの */hello-world-two* パスをアドレスに追加します。 次の簡約された出力例に示すように、カスタム タイトル付きの 2 番目のデモ アプリケーションが返されます。

```
$ curl -v -k --resolve demo.azure.com:443:137.117.36.18 https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事では、Helm を使用して、イングレス コンポーネントおよびサンプル アプリをインストールしました。 Helm グラフをデプロイすると、多数の Kubernetes リソースが作成されます。 これらのリソースには、ポッド、デプロイ、およびサービスが含まれます。 クリーンアップするには、最初に `helm list` コマンドで Helm リリースを一覧表示します。 次の出力例に示すように、*nginx-ingress* および *aks-helloworld* という名前のグラフを探します。

```
$ helm list

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

`helm delete` コマンドでリリースを削除します。 次の例では、NGINX イングレス デプロイおよび 2 つのサンプルの AKS hello world アプリを削除します。

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

次に AKS hello world アプリの Helm repo を削除します。

```console
helm repo remove azure-samples
```

サンプル アプリにトラフィックを送信していたイングレス ルートを削除します。

```console
kubectl delete -f hello-world-ingress.yaml
```

最後に、証明書シークレットを削除します。

```console
kubectl delete secret aks-ingress-tls
```

## <a name="next-steps"></a>次の手順

この記事には、AKS への外部コンポーネントが含まれています。 これらのコンポーネントの詳細については、次のプロジェクト ページを参照してください。

- [Helm CLI][helm-cli]
- [NGINX イングレス コントローラー][nginx-ingress]

さらに、以下を実行できます。

- [外部のネットワーク接続を使用して基本的なイングレス コントローラーを作成する][aks-ingress-basic]
- [HTTP アプリケーションのルーティング アドオンを有効にする][aks-http-app-routing]
- [内部のプライベート ネットワークと IP アドレスを使用するイングレス コントローラーを作成する][aks-ingress-internal]
- Let's Encrypt を使用して、[動的パブリック IP アドレスを使用][aks-ingress-tls]または[静的パブリック IP アドレスを使用][aks-ingress-static-tls]して TLS 証明書を自動的に作成する、イングレス コントローラーを作成する

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
