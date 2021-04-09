---
title: AKS で Helm を使用して既存のアプリケーションをインストールする
description: Helm パッケージ化ツールを使用して、Azure Kubernetes Service (AKS) クラスターにコンテナーをデプロイする方法について説明します
services: container-service
author: zr-msft
ms.topic: article
ms.date: 12/07/2020
ms.author: zarhoads
ms.openlocfilehash: f12dffe0b538738a8f6dd00cd3d87d44da828f21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96779169"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での Helm を使用した既存のアプリケーションのインストール

[Helm][helm] は、Kubernetes アプリケーションのライフサイクルをインストールおよび管理するのに役立つオープン ソースのパッケージ化ツールです。 *APT* や *Yum* などの Linux パッケージ マネージャーと同様に、Helm は、構成済みの Kubernetes リソースのパッケージである Kubernetes チャートの管理に使用されます。

この記事では、AKS 上の Kubernetes クラスターに Helm を構成して使用する方法を説明します。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

Helm CLI もインストールする必要があります。これは、開発システムで実行されるクライアントです。 Helm を使用してアプリケーションを起動、停止、管理することができます。 Azure Cloud Shell を使用している場合、Helm CLI は既にインストールされています。 ローカル プラットフォームでのインストール手順については、[Helm のインストール][helm-install]に関するページを参照してください。

> [!IMPORTANT]
> Helm は Linux ノードで実行するものです。 クラスター内に Windows Server ノードがある場合、Helm ポッドが確実に Linux ノードでのみ実行されるようにスケジュールする必要があります。 また、インストールする Helm チャートが確実に正しいノードで実行されるようにスケジュールする必要もあります。 この記事のコマンドでは、[node-selectors][k8s-node-selector] を使用し、ポッドが正しいノードにスケジュールされるが、一部の Helm チャートではノード セレクターが公開されないようにします。 [taints][taints] などの、他のオプションをクラスターで使用することを検討することもできます。

## <a name="verify-your-version-of-helm"></a>Helm のバージョンを確認する

`helm version` コマンドを使用して、Helm 3 がインストールされていることを確認します。

```console
helm version
```

次の例は、Helm バージョン 3.0.0 がインストールされていることを示しています。

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

## <a name="install-an-application-with-helm-v3"></a>Helm v3 を使用してアプリケーションをインストールする

### <a name="add-helm-repositories"></a>Helm リポジトリの追加

[helm repo][helm-repo-add] コマンドを使用して、*ingress-nginx* リポジトリを追加します。

```console
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### <a name="find-helm-charts"></a>Helm チャートの検索

Helm チャートは、Kubernetes クラスターにアプリケーションをデプロイするために使用されます。 事前に作成されている Helm チャートを検索するには、[helm search][helm-search] コマンドを使用します。

```console
helm search repo ingress-nginx
```

次の出力例の抜粋は、使用できる一部の Helm チャートを示しています。

```console
$ helm search repo ingress-nginx

NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
ingress-nginx/ingress-nginx     2.12.0          0.34.1          Ingress controller for Kubernetes using NGINX a...
```

チャートの一覧を更新するには、[helm repo update][helm-repo-update] コマンドを使用します。

```console
helm repo update
```

リポジトリ更新の成功例を次に示します。

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ingress-nginx" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Helm チャートの実行

Helm を使用してチャートをインストールするには、[helm install][helm-install-command] コマンドを使用し、リリース名とインストールするチャートの名前を指定します。 Helm チャートのインストールを実際に確かめるために、Helm チャートを使用して基本的な nginx デプロイをインストールしてみましょう。

```console
helm install my-nginx-ingress ingress-nginx/ingress-nginx \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

次の出力例の抜粋は、Helm チャートによって作成された Kubernetes リソースのデプロイ状態を示します。

```console
$ helm install my-nginx-ingress ingress-nginx/ingress-nginx \
>     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
>     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME: my-nginx-ingress
LAST DEPLOYED: Fri Nov 22 10:08:06 2019
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller'
...
```

サービスの *EXTERNAL-IP* を取得するには、`kubectl get services` コマンドを使用します。

```console
kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller
```

たとえば、次のコマンドは *my-nginx-ingress-ingress-nginx-controller* サービスの *EXTERNAL-IP* を示しています。

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller

NAME                                        TYPE           CLUSTER-IP   EXTERNAL-IP      PORT(S)                      AGE   SELECTOR
my-nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.2.237   <EXTERNAL-IP>    80:31380/TCP,443:32239/TCP   72s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=my-nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

### <a name="list-releases"></a>リリースを一覧表示する

クラスターにインストールされているリリースの一覧を表示するには、`helm list` コマンドを使用します。

```console
helm list
```

次の例は、前の手順でデプロイされた *my-nginx-ingress* リリースを示しています。

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>リソースをクリーンアップする

Helm グラフをデプロイすると、多数の Kubernetes リソースが作成されます。 これらのリソースには、ポッド、デプロイ、およびサービスが含まれます。 これらのリソースをクリーンアップするには、[helm uninstall][helm-cleanup] コマンドを使用し、前の `helm list` コマンドで見つかったリリース名を指定します。

```console
helm uninstall my-nginx-ingress
```

次の例は、*my-nginx-ingress* という名前のリリースがアンインストールされたことを示しています。

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="next-steps"></a>次のステップ

Kubernetes アプリケーションのデプロイの管理について詳しくは、Helm のドキュメントを参照してください。

> [!div class="nextstepaction"]
> [Helm のドキュメント][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[taints]: operator-best-practices-advanced-scheduler.md
