---
title: カスタム traefik イングレス コントローラーの使用と HTTPS の構成
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Azure Dev Spaces をカスタム traefik イングレス コントローラーを使用するように構成し、そのイングレス コントローラーを使用して HTTPS を構成する方法を説明します。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 036725f3c1eb909407e157d33ece05b1f55213ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204101"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>カスタム traefik イングレス コントローラーの使用と HTTPS の構成

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

この記事では、カスタム traefik イングレス コントローラーを使用するように Azure Dev Spaces を構成する方法について説明します。 この記事では、そのカスタム イングレス コントローラーが HTTPS も使用するように構成する方法についても説明します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 アカウントがない場合は、[無料アカウントを作成する][azure-account-create]ことができます。
* [Azure CLI がインストールされていること][az-cli]。
* [Azure Dev Spaces が有効になっている Azure Kubernetes Service (AKS) クラスター][qs-cli]。
* [kubectl][kubectl] がインストールされていること。
* [Helm 3 がインストールされていること][helm-installed]。
* [DNS ゾーン][dns-zone]を持つ[カスタム ドメイン][custom-domain]。 この記事では、カスタム ドメインと DNS ゾーンが AKS クラスターと同じリソース グループに含まれていることを前提としていますが、別のリソース グループにあるカスタム ドメインと DNS ゾーンを使用することもできます。

## <a name="configure-a-custom-traefik-ingress-controller"></a>カスタム traefik イングレス コントローラーの構成

Kubernetes のコマンドライン クライアントである [kubectl][kubectl] を使ってクラスターに接続します。 Kubernetes クラスターに接続するように `kubectl` を構成するには、[az aks get-credentials][az-aks-get-credentials] コマンドを使用します。 このコマンドは、資格情報をダウンロードし、それを使用するように Kubernetes CLI を構成します。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

クラスターへの接続を確認するには、クラスター ノードの一覧を返す [kubectl get][kubectl-get] コマンドを使用します。

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

traefik イングレス コントローラーの Helm グラフを含む[公式の安定版 Helm リポジトリ][helm-stable-repo]を追加します。

```console
helm repo add stable https://charts.helm.sh/stable
```

traefik イングレス コントローラー用に Kubernetes 名前空間を作成し、それを `helm` を使用してインストールします。

> [!NOTE]
> ご使用の AKS クラスターで Kubernetes RBAC を有効にしない場合は、 *--set rbac.enabled=true* パラメーターを削除します。

```console
kubectl create ns traefik
helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0
```

> [!NOTE]
> 上の例では、イングレス コントローラーのパブリック エンドポイントを作成します。 代わりに、イングレス コントローラーのプライベート エンドポイントを使用する必要がある場合は、*helm install* コマンドに *--set service.annotations."service\\.beta\\.kubernetes\\.io/azure-load-balancer-internal"=true* パラメーターを追加します。
> ```console
> helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --set service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.85.0
> ```
> このプライベート エンドポイントは、AKS クラスターがデプロイされている仮想ネットワーク内で公開されます。

[kubectl get][kubectl-get] を使用して、traefik イングレス コントローラー サービスの IP アドレスを取得します。

```console
kubectl get svc -n traefik --watch
```

このサンプル出力では、*traefik* 名前空間にあるすべてのサービスの IP アドレスを示しています。

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

*A* レコードを、[az network dns record-set a add-record][az-network-dns-record-set-a-add-record] を使用し、traefik サービスの外部 IP アドレスが使用された DNS ゾーンに追加します。

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

上記の例では、*A* レコードを *MY_CUSTOM_DOMAIN* DNS ゾーンに追加します。

この記事では、[Azure Dev Spaces 自転車シェア サンプル アプリケーション](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp)を使用して、Azure Dev Spaces の使い方のデモを行います。 GitHub からアプリケーションを複製して、そのディレクトリに移動します。

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

[values.yaml][values-yaml] を開き、以下の更新を加えます。
* *<REPLACE_ME_WITH_HOST_SUFFIX>* のすべてのインスタンスを *traefik.MY_CUSTOM_DOMAIN* で置き換えます (ここで、*MY_CUSTOM_DOMAIN* にはご自身のドメインを使用してください)。 
* *kubernetes.io/ingress.class: traefik  # Custom Ingress* を *kubernetes.io/ingress.class: traefik-azds  # Dev Spaces-specific* で置き換えます。 

更新された `values.yaml` ファイルの例を次に示します。

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

変更を保存し、ファイルを閉じます。

`azds space select` を使用して、サンプル アプリケーションで *dev* 空間を作成します。

```console
azds space select -n dev -y
```

`helm install` を使用してサンプル アプリケーションをデプロイします。

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

サンプル アプリケーションは、上記の例では、*dev* 名前空間にデプロイされます。

`azds list-uris` を使用してサンプル アプリケーションにアクセスするための URL を表示します。

```console
azds list-uris
```

`azds list-uris` で出力される URL の例は次の通りです。

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

`azds list-uris` コマンドからパブリック URL を開いて、*bikesharingweb* サービスに移動します。 上記の例では、*bikesharingweb* サービスのパブリック URL は `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/` です。

> [!NOTE]
> *bikesharingweb* サービスではなくエラー ページが表示される場合は、*values.yaml* ファイル内で *kubernetes.io/ingress.class* 注釈とホストの **両方** を更新したことを確認してください。

`azds space select` コマンドを使用すると、*dev* の下に子空間を作成し、子開発空間にアクセスするための URL を列挙できます。

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

以下は、`azds list-uris` で出力された、*azureuser1* 子開発空間のサンプル アプリケーションにアクセスするための URL の例です。

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

`azds list-uris` コマンドからパブリック URL を開いて、*azureuser1* 子開発空間の *bikesharingweb* サービスに移動します。 上記の例で、*azureuser1* 子開発空間の *bikesharingweb* サービスのパブリック URL は `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/` です。

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>HTTPS を使用する traefik イングレス コントローラーの構成

[cert-manager][cert-manager] を使用して、HTTPS を使用するように traefik イングレス コントローラーを構成するときに TLS 証明書の管理を自動化します。 `helm` を使用して、*certmanager* グラフをインストールします。

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace traefik
kubectl label namespace traefik certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace traefik --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

`letsencrypt-clusterissuer.yaml` ファイルを作成し、自分のメール アドレスでメール フィールドを更新します。

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
      - http01:
          ingress:
            class: traefik
```

> [!NOTE]
> テストの場合、*ClusterIssuer* に使用できる [ステージング サーバー][letsencrypt-staging-issuer]もあります。

`kubectl` を使用して `letsencrypt-clusterissuer.yaml` を適用します。

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace traefik
```

以前の *traefik* *ClusterRole* および *ClusterRoleBinding* を削除し、`helm` を使用して、traefik を HTTPS にアップグレードします。

> [!NOTE]
> ご使用の AKS クラスターで Kubernetes RBAC を有効にしない場合は、 *--set rbac.enabled=true* パラメーターを削除します。

```console
kubectl delete ClusterRole traefik
kubectl delete ClusterRoleBinding traefik
helm upgrade traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0 --set ssl.enabled=true --set ssl.enforced=true --set ssl.permanentRedirect=true
```

[kubectl get][kubectl-get] を使用して、traefik イングレス コントローラー サービスの更新済み IP アドレスを取得します。

```console
kubectl get svc -n traefik --watch
```

このサンプル出力では、*traefik* 名前空間にあるすべてのサービスの IP アドレスを示しています。

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP          PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>            80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_NEW_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

[az network dns record-set a add-record][az-network-dns-record-set-a-add-record] を使用して、*A* レコードを、traefik サービスの外部 IP アドレスが使用された DNS ゾーンに追加し、[az network dns record-set a remove-record][az-network-dns-record-set-a-remove-record] を使用して、以前の *A* レコードを削除します。

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_NEW_EXTERNAL_IP

az network dns record-set a remove-record \
    --resource-group myResourceGroup \
    --zone-name  MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address PREVIOUS_EXTERNAL_IP
```

上記の例は、*MY_CUSTOM_DOMAIN* DNS ゾーンの *A* レコードを更新して、*PREVIOUS_EXTERNAL_IP* を使用しています。

*cert-manager* および HTTPS を使用するための詳細を含むように [values.yaml][values-yaml] を更新します。 更新された `values.yaml` ファイルの例を次に示します。

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

`helm` を使用してサンプル アプリケーションをアップグレードします。

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

*dev/azureuser1* 子空間のサンプル アプリケーションに移動し、HTTPS を使用するようにリダイレクトされていることを確認します。

> [!IMPORTANT]
> DNS の変更が完了し、サンプル アプリケーションにアクセスできるようになるには、30 分以上かかる場合があります。

ページが読み込まれ、ブラウザーにいくつかエラーが表示されていることも確認します。 ブラウザー コンソールを開くと、HTTP リソースを読み込もうとしている HTTPS ページにエラーが関連していることが表示されます。 次に例を示します。

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

このエラーを修正するには、[BikeSharingWeb/azds.yaml][azds-yaml] で *kubernetes.io/ingress.class* に *traefik* が使用され、 *$(hostSuffix)* にお使いのカスタム ドメインが使用されるように更新します。 次に例を示します。

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.traefik.MY_CUSTOM_DOMAIN
...
```

[BikeSharingWeb/package.json][package-json] で *url* パッケージの依存関係が使用されるように更新します。

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

[BikeSharingWeb/lib/helpers.js][helpers-js] の *getApiHostAsync* メソッドで HTTPS が使用されるように更新します。

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

`BikeSharingWeb` ディレクトリに移動し、`azds up` を使用し、更新されたお使いの BikeSharingWeb サービスを実行します。

```console
cd ../BikeSharingWeb/
azds up
```

*dev/azureuser1* 子空間のサンプル アプリケーションに移動し、HTTPS を使用するようにリダイレクトされ、エラーがないことを確認します。

## <a name="next-steps"></a>次のステップ

Azure Dev Spaces のしくみの詳細について確認します。

> [!div class="nextstepaction"]
> [Azure Dev Spaces のしくみ](../how-dev-spaces-works.md)


[az-cli]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a#az-network-dns-record-set-a-add-record
[az-network-dns-record-set-a-remove-record]: /cli/azure/network/dns/record-set/a#az-network-dns-record-set-a-remove-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/lib/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
