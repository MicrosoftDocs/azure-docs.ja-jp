---
title: Helm を使用して Azure の Kubernetes にコンテナーをデプロイする
description: Helm パッケージ化ツールを使用して、Azure Kubernetes Service (AKS) クラスターにコンテナーをデプロイする方法について説明します
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 05/23/2019
ms.author: zarhoads
ms.openlocfilehash: bc74ac660c5bba0624416d0a1724d959a4c385a7
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305270"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での Helm を使用したアプリケーションのインストール

[Helm][helm] は、Kubernetes アプリケーションのライフサイクルをインストールおよび管理するのに役立つオープン ソースのパッケージ化ツールです。 *APT* や *Yum* などの Linux パッケージ マネージャーと同様に、Helm は、構成済みの Kubernetes リソースのパッケージである Kubernetes チャートの管理に使用されます。

この記事では、AKS 上の Kubernetes クラスターに Helm を構成して使用する方法を説明します。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

Helm CLI もインストールする必要があります。これは、開発システムで実行されるクライアントです。 Helm を使用してアプリケーションを起動、停止、管理することができます。 Azure Cloud Shell を使用している場合、Helm CLI は既にインストールされています。 お使いのローカル プラットフォームでのインストール手順については、「[Installing Helm][helm-install]」 (Helm のインストール) をご覧ください。

> [!IMPORTANT]
> Helm は Linux ノードで実行するものです。 クラスター内に Windows Server ノードがある場合、Helm ポッドが確実に Linux ノードでのみ実行されるようにスケジュールする必要があります。 また、インストールする Helm チャートが確実に正しいノードで実行されるようにスケジュールする必要もあります。 この記事のコマンドでは、[node-selectors][k8s-node-selector] を使用し、ポッドが正しいノードにスケジュールされるが、一部の Helm チャートではノード セレクターが公開されないようにします。 [taints][taints] などの、他のオプションをクラスターで使用することを検討することもできます。

## <a name="create-a-service-account"></a>サービス アカウントの作成

RBAC が有効になった AKS クラスターに Helm をデプロイする前に、Tiller サービスのサービス アカウントとロール バインディングが必要になります。 RBAC 対応クラスター内の Helm/Tiller のセキュリティ保護の詳細については、[Tiller、名前空間、および RBAC][tiller-rbac] に関する記事を参照してください。 ご利用の AKS クラスターが RBAC に対応していない場合は、この手順をスキップしてください。

`helm-rbac.yaml` という名前のファイルを作成し、そこに以下の YAML をコピーします。

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

`kubectl apply` コマンドを使って、サービス アカウントとロール バインディングを作成します。

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="secure-tiller-and-helm"></a>Tiller と Helm をセキュリティで保護する

Helm クライアントと Tiller サービスは TLS/SSL を使用して互いに認証および通信を行います。 この認証方法では、Kubernetes クラスターをセキュリティで保護して、どのサービスをデプロイできるかを確認できます。 セキュリティを強化するために、独自の署名証明書を生成できます。 各 Helm ユーザーは独自のクライアント証明書を受信し、Tiller は証明書が適用された Kubernetes クラスターで初期化されていました。 詳しくは、「[Using TLS/SSL between Helm and Tiller][helm-ssl]」 (Helm と Tiller 間での TLS/SSL の使用) を参照してください。

RBAC 対応の Kubernetes クラスターを使用している場合、Tiller のクラスターに対するアクセスのレベルを制御できます。 Tiller のデプロイに使用される Kubernetes 名前空間を定義し、Tiller によるリソースのデプロイに使用できる名前空間を制限できます。 この方法により、さまざまな名前空間の Tiller インスタンスを作成し、デプロイ境界を制限したり、Helm クライアントのユーザーの範囲を特定の名前空間に制限したりできます。 詳しくは、「[Helm role-based access controls][helm-rbac]」 (Helm でのロール ベースのアクセス制御) を参照してください。

## <a name="configure-helm"></a>Helm の構成

基本的な Tiller を AKS クラスターにデプロイするには、[helm init][helm-init] コマンドを使用します。 ご利用のクラスターが RBAC に対応していない場合は、`--service-account` 引数と値を削除します。 次の例でも、[history-max][helm-history-max] が 200 に設定されます。

Tiller と Helm 用に TLS または SSL を構成した場合、この基本的な初期化手順はスキップし、次の例に示されているように必要な `--tiller-tls-` を指定します。

```console
helm init --history-max 200 --service-account tiller --node-selectors "beta.kubernetes.io/os=linux"
```

Helm と Tiller 間に TLS または SSL を構成した場合、次の例に示されているように、`--tiller-tls-*` パラメーターと独自の証明書の名前を指定します。

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --history-max 200 \
    --service-account tiller \
    --node-selectors "beta.kubernetes.io/os=linux"
```

## <a name="find-helm-charts"></a>Helm チャートの検索

Helm チャートは、Kubernetes クラスターにアプリケーションをデプロイするために使用されます。 事前に作成されている Helm チャートを検索するには、[helm search][helm-search] コマンドを使用します。

```console
helm search
```

次の出力例の抜粋は、使用できる一部の Helm チャートを示しています。

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

チャートの一覧を更新するには、[helm repo update][helm-repo-update] コマンドを使用します。 リポジトリ更新の成功例を次に示します。

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete.
```

## <a name="run-helm-charts"></a>Helm チャートの実行

Helm を使用してチャートをインストールするには、[helm install][helm-install] コマンドを使用して、インストールするチャートの名前を指定します。 Helm チャートのインストールを実際に確かめるために、Helm チャートを使用して基本的な nginx デプロイをインストールしてみましょう。 TLS/SSL を構成した場合は、`--tls` パラメーターを追加して Helm クライアント証明書を使用します。

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

次の出力例の抜粋は、Helm チャートによって作成された Kubernetes リソースのデプロイ状態を示します。

```
$ helm install stable/nginx-ingress --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME:   flailing-alpaca
LAST DEPLOYED: Thu May 23 12:55:21 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                      DATA  AGE
flailing-alpaca-nginx-ingress-controller  1     0s

==> v1/Pod(related)
NAME                                                            READY  STATUS             RESTARTS  AGE
flailing-alpaca-nginx-ingress-controller-56666dfd9f-bq4cl       0/1    ContainerCreating  0         0s
flailing-alpaca-nginx-ingress-default-backend-66bc89dc44-m87bp  0/1    ContainerCreating  0         0s

==> v1/Service
NAME                                           TYPE          CLUSTER-IP  EXTERNAL-IP  PORT(S)                     AGE
flailing-alpaca-nginx-ingress-controller       LoadBalancer  10.0.109.7  <pending>    80:31219/TCP,443:32421/TCP  0s
flailing-alpaca-nginx-ingress-default-backend  ClusterIP     10.0.44.97  <none>       80/TCP                      0s
...
```

nginx-ingress-controller サービスの *EXTERNAL-IP* アドレスが設定され、Web ブラウザーでアクセスできるようになるまでに 1 分から 2 分かかります。

## <a name="list-helm-releases"></a>Helm リリースの一覧表示

クラスターにインストールされているリリースの一覧を表示するには、[helm list][helm-list] コマンドを使用します。 次の例は、前の手順でデプロイされた nginx-ingress リリースを示しています。 TLS/SSL を構成した場合は、`--tls` パラメーターを追加して Helm クライアント証明書を使用します。

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Helm グラフをデプロイすると、多数の Kubernetes リソースが作成されます。 これらのリソースには、ポッド、デプロイ、およびサービスが含まれます。 これらのリソースをクリーンアップするには、`helm delete` コマンドを使用し、上記の `helm list` コマンドで確認したリリース名を指定します。 次の例では、*flailing-alpaca* という名前のリリースが削除されます。

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>次の手順

Kubernetes アプリケーションのデプロイの管理について詳しくは、Helm のドキュメントを参照してください。

> [!div class="nextstepaction"]
> [Helm のドキュメント][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-history-max]: https://helm.sh/docs/using_helm/#initialize-helm-and-install-tiller
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
[helm-ssl]: https://docs.helm.sh/using_helm/#using-ssl-between-helm-and-tiller

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
[taints]: operator-best-practices-advanced-scheduler.md
