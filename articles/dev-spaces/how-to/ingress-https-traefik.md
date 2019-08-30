---
title: カスタム traefik イングレス コントローラーの使用と HTTPS の構成
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/13/2019
ms.topic: conceptual
description: Azure Dev Spaces をカスタム traefik イングレス コントローラーを使用するように構成し、そのイングレス コントローラーを使用して HTTPS を構成する方法を説明します。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s
ms.openlocfilehash: 50908bde65b69cb475391cd30bca758dd571f114
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69037299"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>カスタム traefik イングレス コントローラーの使用と HTTPS の構成

この記事では、カスタム traefik イングレス コントローラーを使用するように Azure Dev Spaces を構成する方法について説明します。 この記事では、そのカスタム イングレス コントローラーが HTTPS も使用するように構成する方法についても説明します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 アカウントがない場合は、[無料アカウントを作成する][azure-account-create]ことができます。
* [Azure CLI がインストールされていること][az-cli]。
* [Azure Dev Spaces が有効になっている Azure Kubernetes Service (AKS) クラスター][qs-cli]。
* [kubectl][kubectl] がインストールされていること。
* [Helm 2.13 以上がインストールされていること][helm-installed]。
* [カスタム ドメイン][custom-domain]の [DNS ゾーン][dns-zone]が、お使いの AKS クラスターと同じリソース グループにあること。

## <a name="configure-a-custom-traefik-ingress-controller"></a>カスタム traefik イングレス コントローラーの構成

Kubernetes のコマンドライン クライアントである [kubectl][kubectl] を使ってクラスターに接続します。 Kubernetes クラスターに接続するように `kubectl` を構成するには、[az aks get-credentials][az-aks-get-credentials] コマンドを使用します。 このコマンドは、資格情報をダウンロードし、それを使用するように Kubernetes CLI を構成します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

クラスターへの接続を確認するには、[kubectl get][kubectl-get] コマンドを使用して、クラスター ノードの一覧を返します。

```console
$ kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

traefik イングレス コントローラー用に Kubernetes 名前空間を作成し、それを `helm` を使用してインストールします。

```console
kubectl create ns traefik
helm init --wait
helm install stable/traefik --name traefik --namespace traefik --set kubernetes.ingressClass=traefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true
```

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

```console
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

[values.yaml][values-yaml] を開き、*MY_CUSTOM_DOMAIN* には自分自身のドメインを使用し、 *<REPLACE_ME_WITH_HOST_SUFFIX>* のすべてのインスタンスを *traefik.MY_CUSTOM_DOMAIN* に置き換えます。 また、*kubernetes.io/ingress.class: traefik  # Custom Ingress* を *kubernetes.io/ingress.class: traefik-azds  # Dev Spaces-specific* に置き換えます。 更新された `values.yaml` ファイルの例を次に示します。

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

`helm install` を使用してサンプル アプリケーションをデプロイします。

```console
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

サンプル アプリケーションは、上記の例では、*dev* 名前空間にデプロイされます。

ご自分のサンプル アプリケーションの *dev* 空間を `azds space select` を使用して選択し、`azds list-uris` を使用してそのサンプル アプリケーションにアクセスする URL を表示します。

```console
azds space select -n dev
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

以下の例のような `dev-spaces/samples/BikeSharingApp/traefik-values.yaml` ファイルを作成します。 *email* 値を自分自身のメールで更新します。これは、Let's Encrypt で証明書を生成するために使用されます。

```yaml
fullnameOverride: traefik
replicas: 1
cpuLimit: 400m
memoryRequest: 200Mi
memoryLimit: 500Mi
externalTrafficPolicy: Local
kubernetes:
  ingressClass: traefik
  ingressEndpoint:
    useDefaultPublishedService: true
dashboard:
  enabled: false
debug:
  enabled: false
accessLogs:
  enabled: true
  fields:
    defaultMode: keep
    headers:
      defaultMode: keep
      names:
        Authorization: redact
acme:
  enabled: true
  email: "someone@example.com"
  staging: false
  challengeType: tls-alpn-01
ssl:
  enabled: true
  enforced: true
  permanentRedirect: true
  tlsMinVersion: VersionTLS12
  cipherSuites:
    - TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_RSA_WITH_AES_128_GCM_SHA256
    - TLS_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
```

お使いの *traefik* サービスを、`helm repo update` を使用し、作成した *traefik-values.yaml* ファイルを含めて更新します。

```console
cd ..
helm upgrade traefik stable/traefik --namespace traefik --values traefik-values.yaml
```

上記のコマンドは、*traefik-values.yaml* の値を使用して traefik サービスの新しいバージョンを実行し、前のサービスを削除します。 traefik サービスによって、Let's Encrypt を使用して TLS 証明書も作成され、HTTPS が使用されるように Web トラフィックのリダイレクトが開始されます。

> [!NOTE]
> traefik サービスの新しいバージョンの開始には数分かかる場合があります。 進行状況は、`kubectl get pods --namespace traefik --watch` を使用して確認できます。

*dev/azureuser1* 子空間のサンプル アプリケーションに移動し、HTTPS を使用するようにリダイレクトされていることを確認します。 ページが読み込まれ、ブラウザーにいくつかエラーが表示されていることも確認します。 ブラウザー コンソールを開くと、HTTP リソースを読み込もうとしている HTTPS ページにエラーが関連していることが表示されます。 例:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

このエラーを修正するには、[BikeSharingWeb/azds.yaml][azds-yaml] で *kubernetes.io/ingress.class* に *traefik* が使用され、 *$(hostSuffix)* にお使いのカスタム ドメインが使用されるように更新します。 例:

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

[BikeSharingWeb/pages/helpers.js][helpers-js] の *getApiHostAsync* メソッドで HTTPS が使用されるように更新します。

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
cd BikeSharingWeb/
azds up
```

*dev/azureuser1* 子空間のサンプル アプリケーションに移動し、HTTPS を使用するようにリダイレクトされ、エラーがないことを確認します。

## <a name="next-steps"></a>次の手順

Azure Dev Spaces を使用して複数のコンテナーにまたがるより複雑なアプリケーションを開発する方法と、別の空間で別のバージョンまたは分岐を使用して作業することによって共同開発を簡略化する方法について学習します。

> [!div class="nextstepaction"]
> [Azure Dev Spaces でのチーム開発][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[helm-installed]: https://github.com/helm/helm/blob/master/docs/install.md
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/pages/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml