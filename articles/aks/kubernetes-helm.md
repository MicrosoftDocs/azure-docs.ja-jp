---
title: Helm を使用して Azure の Kubernetes にコンテナーをデプロイする
description: Helm パッケージ化ツールを使用して、Azure Kubernetes Service (AKS) クラスターにコンテナーをデプロイする方法について説明します
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 11/22/2019
ms.author: zarhoads
ms.openlocfilehash: 849f4e182d4bd9255ca8e60b69b6ad6c8a8e0fc7
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74869376"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での Helm を使用したアプリケーションのインストール

[Helm][helm] は、Kubernetes アプリケーションのライフサイクルをインストールおよび管理するのに役立つオープン ソースのパッケージ化ツールです。 *APT* や *Yum* などの Linux パッケージ マネージャーと同様に、Helm は、構成済みの Kubernetes リソースのパッケージである Kubernetes チャートの管理に使用されます。

この記事では、AKS 上の Kubernetes クラスターに Helm を構成して使用する方法を説明します。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

Helm CLI もインストールする必要があります。これは、開発システムで実行されるクライアントです。 Helm を使用してアプリケーションを起動、停止、管理することができます。 Azure Cloud Shell を使用している場合、Helm CLI は既にインストールされています。 ローカル プラットフォームでのインストール手順については、[Helm のインストール][helm-install]に関するページを参照してください。

> [!IMPORTANT]
> Helm は Linux ノードで実行するものです。 クラスター内に Windows Server ノードがある場合、Helm ポッドが確実に Linux ノードでのみ実行されるようにスケジュールする必要があります。 また、インストールする Helm チャートが確実に正しいノードで実行されるようにスケジュールする必要もあります。 この記事のコマンドでは、[node-selectors][k8s-node-selector] を使用し、ポッドが正しいノードにスケジュールされるが、一部の Helm チャートではノード セレクターが公開されないようにします。 [taints][taints] などの、他のオプションをクラスターで使用することを検討することもできます。

## <a name="verify-your-version-of-helm"></a>Helm のバージョンを確認する

インストールされている Helm のバージョンを確認するには、`helm version` コマンドを使用します。

```console
helm version
```

次の例は、Helm バージョン 3.0.0 がインストールされていることを示しています。

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

Helm v3 の場合は、[Helm v3 のセクション](#install-an-application-with-helm-v3)の手順に従ってください。 Helm v2 の場合は、[Helm v2 のセクション](#install-an-application-with-helm-v2)の手順に従ってください。

## <a name="install-an-application-with-helm-v3"></a>Helm v3 を使用してアプリケーションをインストールする

### <a name="add-the-official-helm-stable-charts-repository"></a>公式の Helm の安定したチャート リポジトリを追加する

公式の Helm の安定したチャート リポジトリを追加するには [helm repo][helm-repo-add] コマンドを使用します。

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

### <a name="find-helm-charts"></a>Helm チャートの検索

Helm チャートは、Kubernetes クラスターにアプリケーションをデプロイするために使用されます。 事前に作成されている Helm チャートを検索するには、[helm search][helm-search] コマンドを使用します。

```console
helm search repo stable
```

次の出力例の抜粋は、使用できる一部の Helm チャートを示しています。


```console
$ helm search repo stable

NAME                                    CHART VERSION   APP VERSION                     DESCRIPTION                                       
stable/acs-engine-autoscaler            2.2.2           2.1.1                           DEPRECATED Scales worker nodes within agent pools 
stable/aerospike                        0.3.1           v4.5.0.5                        A Helm chart for Aerospike in Kubernetes          
stable/airflow                          4.10.0          1.10.4                          Airflow is a platform to programmatically autho...
stable/ambassador                       4.4.7           0.85.0                          A Helm chart for Datawire Ambassador              
stable/anchore-engine                   1.3.7           0.5.2                           Anchore container analysis and policy evaluatio...
stable/apm-server                       2.1.5           7.0.0                           The server receives data from the Elastic APM a...
stable/ark                              4.2.2           0.10.2                          DEPRECATED A Helm chart for ark                   
stable/artifactory                      7.3.1           6.1.0                           DEPRECATED Universal Repository Manager support...
stable/artifactory-ha                   0.4.1           6.2.0                           DEPRECATED Universal Repository Manager support...
stable/atlantis                         3.8.4           v0.8.2                          A Helm chart for Atlantis https://www.runatlant...
stable/auditbeat                        1.1.0           6.7.0                           A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler           0.3.3                                           Scales worker nodes within autoscaling groups.    
stable/aws-iam-authenticator            0.1.1           1.0                             A Helm chart for aws-iam-authenticator            
stable/bitcoind                         0.2.2           0.17.1                          Bitcoin is an innovative payment network and a ...
stable/bookstack                        1.1.2           0.27.4-1                        BookStack is a simple, self-hosted, easy-to-use...
stable/buildkite                        0.2.4           3                               DEPRECATED Agent for Buildkite                    
stable/burrow                           1.5.2           0.29.0                          Burrow is a permissionable smart contract machine 
stable/centrifugo                       3.1.0           2.1.0                           Centrifugo is a real-time messaging server.       
stable/cerebro                          1.3.1           0.8.5                           A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager                     v0.6.7          v0.6.2                          A Helm chart for cert-manager                     
stable/chaoskube                        3.1.2           0.14.0                          Chaoskube periodically kills random pods in you...
stable/chartmuseum                      2.4.0           0.8.2                           Host your own Helm Chart Repository               
stable/chronograf                       1.1.0           1.7.12                          Open-source web application written in Go and R...
stable/clamav                           1.0.4           1.6                             An Open-Source antivirus engine for detecting t...
stable/cloudserver                      1.0.3           8.1.5                           An open-source Node.js implementation of the Am...
stable/cluster-autoscaler               6.2.0           1.14.6                          Scales worker nodes within autoscaling groups.    
stable/cluster-overprovisioner          0.2.6           1.0                             Installs the a deployment that overprovisions t...
stable/cockroachdb                      2.1.16          19.1.5                          CockroachDB is a scalable, survivable, strongly...
stable/collabora-code                   1.0.5           4.0.3.1                         A Helm chart for Collabora Office - CODE-Edition  
stable/concourse                        8.2.7           5.6.0                           Concourse is a simple and scalable CI system.     
stable/consul                           3.9.2           1.5.3                           Highly available and distributed service discov...
stable/contour                          0.1.0           v0.15.0                         Contour Ingress controller for Kubernetes         
stable/coredns                          1.7.4           1.6.4                           CoreDNS is a DNS server that chains plugins and...
stable/cosbench                         1.0.1           0.0.6                           A benchmark tool for cloud object storage services
stable/coscale                          1.0.0           3.16.0                          CoScale Agent                                     
stable/couchbase-operator               1.0.1           1.2.1                           A Helm chart to deploy the Couchbase Autonomous...
stable/couchdb                          2.3.0           2.3.1                           DEPRECATED A database featuring seamless multi-...
stable/dask                             3.1.0           1.1.5                           Distributed computation in Python with task sch...
stable/dask-distributed                 2.0.2                                           DEPRECATED: Distributed computation in Python     
stable/datadog                          1.38.3          6.14.0                          DataDog Agent 
...
```

チャートの一覧を更新するには、[helm repo update][helm-repo-update] コマンドを使用します。 リポジトリ更新の成功例を次に示します。

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Helm チャートの実行

Helm を使用してチャートをインストールするには、[helm install][helm-install-command] コマンドを使用し、リリース名とインストールするチャートの名前を指定します。 Helm チャートのインストールを実際に確かめるために、Helm チャートを使用して基本的な nginx デプロイをインストールしてみましょう。

```console
helm install my-nginx-ingress stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

次の出力例の抜粋は、Helm チャートによって作成された Kubernetes リソースのデプロイ状態を示します。

```console
$ helm install my-nginx-ingress stable/nginx-ingress \
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
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-controller'
...
```

サービスの *EXTERNAL-IP* を取得するには、`kubectl get services` コマンドを使用します。 たとえば、次のコマンドは *my-nginx-ingress-controller* サービスの *EXTERNAL-IP* を示しています。

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-controller

NAME                          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
my-nginx-ingress-controller   LoadBalancer   10.0.123.1     <EXTERNAL-IP>   80:31301/TCP,443:31623/TCP   96s   app=nginx-ingress,component=controller,release=my-nginx-ingress
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

### <a name="clean-up-resources"></a>リソースのクリーンアップ

Helm グラフをデプロイすると、多数の Kubernetes リソースが作成されます。 これらのリソースには、ポッド、デプロイ、およびサービスが含まれます。 これらのリソースをクリーンアップするには、[helm uninstall][helm-cleanup] コマンドを使用し、前の `helm list` コマンドで見つかったリリース名を指定します。

```console
helm uninstall my-nginx-ingress
```

次の例は、*my-nginx-ingress* という名前のリリースがアンインストールされたことを示しています。

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="install-an-application-with-helm-v2"></a>Helm v2 を使用してアプリケーションをインストールする

### <a name="create-a-service-account"></a>サービス アカウントの作成

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

### <a name="secure-tiller-and-helm"></a>Tiller と Helm をセキュリティで保護する

Helm クライアントと Tiller サービスは TLS/SSL を使用して互いに認証および通信を行います。 この認証方法では、Kubernetes クラスターをセキュリティで保護して、どのサービスをデプロイできるかを確認できます。 セキュリティを強化するために、独自の署名証明書を生成できます。 各 Helm ユーザーは独自のクライアント証明書を受信し、Tiller は証明書が適用された Kubernetes クラスターで初期化されていました。 詳しくは、「[Using TLS/SSL between Helm and Tiller][helm2-ssl]」 (Helm と Tiller 間での TLS/SSL の使用) を参照してください。

RBAC 対応の Kubernetes クラスターを使用している場合、Tiller のクラスターに対するアクセスのレベルを制御できます。 Tiller のデプロイに使用される Kubernetes 名前空間を定義し、Tiller によるリソースのデプロイに使用できる名前空間を制限できます。 この方法により、さまざまな名前空間の Tiller インスタンスを作成し、デプロイ境界を制限したり、Helm クライアントのユーザーの範囲を特定の名前空間に制限したりできます。 詳しくは、「[Helm role-based access controls][helm2-rbac]」 (Helm でのロール ベースのアクセス制御) を参照してください。

### <a name="configure-helm"></a>Helm の構成

基本的な Tiller を AKS クラスターにデプロイするには、[helm init][helm2-init] コマンドを使用します。 ご利用のクラスターが RBAC に対応していない場合は、`--service-account` 引数と値を削除します。 次の例でも、[history-max][helm2-history-max] が 200 に設定されます。

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

### <a name="find-helm-charts"></a>Helm チャートの検索

Helm チャートは、Kubernetes クラスターにアプリケーションをデプロイするために使用されます。 事前に作成されている Helm チャートを検索するには、[helm search][helm2-search] コマンドを使用します。

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

チャートの一覧を更新するには、[helm repo update][helm2-repo-update] コマンドを使用します。 リポジトリ更新の成功例を次に示します。

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete.
```

### <a name="run-helm-charts"></a>Helm チャートの実行

Helm を使用してチャートをインストールするには、[helm install][helm2-install-command] コマンドを使用して、インストールするチャートの名前を指定します。 Helm チャートのインストールを実際に確かめるために、Helm チャートを使用して基本的な nginx デプロイをインストールしてみましょう。 TLS/SSL を構成した場合は、`--tls` パラメーターを追加して Helm クライアント証明書を使用します。

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

### <a name="list-helm-releases"></a>Helm リリースの一覧表示

クラスターにインストールされているリリースの一覧を表示するには、[helm list][helm2-list] コマンドを使用します。 次の例は、前の手順でデプロイされた nginx-ingress リリースを示しています。 TLS/SSL を構成した場合は、`--tls` パラメーターを追加して Helm クライアント証明書を使用します。

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

### <a name="clean-up-resources"></a>リソースのクリーンアップ

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
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm2-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm2-install-command]: https://v2.helm.sh/docs/helm/#helm-install
[helm2-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm2-list]: https://v2.helm.sh/docs/helm/#helm-list
[helm2-history-max]: https://v2.helm.sh/docs/using_helm/#initialize-helm-and-install-tiller
[helm2-rbac]: https://v2.helm.sh/docs/using_helm/#role-based-access-control
[helm2-repo-update]: https://v2.helm.sh/docs/helm/#helm-repo-update
[helm2-search]: https://v2.helm.sh/docs/helm/#helm-search
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
[helm2-ssl]: https://v2.helm.sh/docs/using_helm/#using-ssl-between-helm-and-tiller
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
[taints]: operator-best-practices-advanced-scheduler.md
