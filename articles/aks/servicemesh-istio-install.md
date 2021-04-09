---
title: Azure Kubernetes Service (AKS) で Istio をインストールする
description: Istio をインストールして使用し、Azure Kubernetes Service (AKS) クラスターでサービス メッシュを作成する方法について説明します
author: paulbouwer
ms.topic: article
ms.date: 10/02/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: c42e9c31397e9313898d7029366bc8de169d368e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94683830"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で Istio をインストールして使用する

[Istio][istio-github] はオープンソースのサービス メッシュであり、Kubernetes クラスターのマイクロサービスに対して重要な機能のセットが提供されます。 これらの機能には、トラフィック管理、サービスの ID とセキュリティ、ポリシー適用、可観測性などがあります。 Istio について詳しくは、公式ドキュメント「[Istio とは][istio-docs-concepts]」をご覧ください。

この記事では、Istio をインストールする方法について説明します。 Istio `istioctl` クライアント バイナリはクライアント コンピューターにインストールされます。それから、Istio コンポーネントが AKS の Kubernetes クラスターにインストールされます。

> [!NOTE]
> 次の手順は Istio バージョン `1.7.3` を参考にしています。
>
> Istio `1.7.x` リリースは、Istio チームによって Kubernetes バージョン `1.16+` に対してテストされています。 Istio のその他のバージョンは [GitHub の Istio リリース][istio-github-releases]に関するページにあります。各リリースに関する情報は [Istio のニュース][istio-release-notes]を参照してください。サポートされている Kubernetes のバージョンについては、[Istio の全般的な FAQ][istio-faq] のページを参照してください。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Istio istioctl クライアント バイナリをダウンロードしてインストールする
> * AKS に Istio をインストールする
> * Istio インストールを検証する
> * アドオンにアクセスする
> * AKS から Istio をアンインストールする

## <a name="before-you-begin"></a>開始する前に

この記事で詳しく説明する手順では、AKS クラスターを (Kubernetes `1.16` 以降で Kubernetes RBAC を有効にして) 作成済みであり、そのクラスターとの `kubectl` 接続が確立されていることを前提としています。 いずれかの項目でヘルプが必要な場合、[AKS クイック スタート][aks-quickstart]を参照してください。

[Istio のパフォーマンスとスケーラビリティ](https://istio.io/docs/concepts/performance-and-scalability/)に関するドキュメントを読み、AKS クラスターで Istio を実行するための追加リソース要件を理解しておいてください。 コアとメモリの要件は、特定のワークロードに基づいて変わります。 セットアップに適したノード数と VM サイズを選択します。

この記事では、Istio のインストール ガイドを個別のステップに分割しています。 最終的な結果は、公式の Istio インストール [ガイド][istio-install-istioctl]と構造的に同じになります。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-operator-on-aks"></a>Istio Operator を AKS にインストールする

Istio では、AKS クラスター内の Istio コンポーネントのインストールと更新を管理するための [Operator][istio-install-operator] が提供されています。 `istioctl` クライアント バイナリを使用して、Istio Operator をインストールします。

```bash
istioctl operator init
```

Istio Operator がインストールされたことを確認できる次のような出力が表示されます。

```console
Using operator Deployment image: docker.io/istio/operator:1.7.3
✔ Istio operator installed
✔ Installation complete
```

Istio Operator は `istio-operator` 名前空間にインストールされます。 この名前空間に対してクエリを実行します。

```bash
kubectl get all -n istio-operator
```

次のコンポーネントがデプロイされていることが表示されます。

```console
NAME                                  READY   STATUS    RESTARTS   AGE
pod/istio-operator-6d7958b7bf-wxgdc   1/1     Running   0          2m43s

NAME                     TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
service/istio-operator   ClusterIP   10.0.8.57    <none>        8383/TCP   2m43s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istio-operator   1/1     1            1           2m43s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/istio-operator-6d7958b7bf   1         1         1       2m43s
```

Operator のパターンの詳細と複雑なタスクを自動化する方法については、[kubernetes.io][kubernetes-operator] を参照してください。


### <a name="install-istio-components"></a>Istio コンポーネントをインストールする

AKS クラスターに Istio Operator が正常にインストールされたので、次は Istio のコンポーネントをインストールします。 

`default` [Istio 構成プロファイル][istio-configuration-profiles]を利用して、[Istio Operator Spec][istio-control-plane] を作成します。

次の `istioctl` コマンドを実行すると、`default` Istio 構成プロファイルの構成を表示できます。

```bash
istioctl profile dump default
```

> [!NOTE]
> 現時点で Istio は Linux ノード上で実行するようにスケジュールする必要があります。 クラスター内に Windows Server ノードがある場合、Istio ポッドが確実に Linux ノードでのみ実行されるようにスケジュールする必要があります。 [ノードのセレクター][kubernetes-node-selectors]を使用して、ポッドが適切なノードにスケジュールされていることを確認します。

> [!CAUTION]
> [Istio CNI][istio-feature-cni] の機能は現在[アルファ版][istio-feature-stages]であるため、これらを有効にする場合は事前に十分に考慮する必要があります。 

次のコンテンツを含む `istio.aks.yaml` という名前のファイルを作成します。 このファイルには、Istio を構成するための [Istio Operator Spec][istio-control-plane] が保持されます。

```yaml
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  namespace: istio-system
  name: istio-control-plane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  # Enable the addons that we will want to use
  addonComponents:
    grafana:
      enabled: true
    prometheus:
      enabled: true
    tracing:
      enabled: true
    kiali:
      enabled: true
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
    kiali:
      dashboard:
        auth:
          strategy: anonymous 
```

`istio-system` 名前空間を作成し、その名前空間に Istio Operator Spec をデプロイします。 Istio Operator では、Istio Operator Spec を監視し、これを使用して AKS クラスターで Istio のインストールと構成を行います。

```bash
kubectl create ns istio-system

kubectl apply -f istio.aks.yaml 
```

この時点で、Istio が AKS クラスターにデプロイされています。 Istio が正常にデプロイされたことを確認するには、次の [Istio インストールを検証する](#validate-the-istio-installation)セクションに進みます。

## <a name="validate-the-istio-installation"></a>Istio インストールを検証する

`istio-system` 名前空間に対してクエリを実行します。この名前空間は、Istio Operator によって Istio とアドオン コンポーネントがインストールされた場所です。

```bash
kubectl get all -n istio-system
```

次のコンポーネントが表示されます。

- `istio*` - Istio コンポーネント
- `jaeger-*`、`tracing`、および `zipkin` - トレース アドオン
- `prometheus` - メトリック アドオン
- `grafana` - 分析と監視のダッシュボード アドオン
- `kiali` - サービス メッシュ ダッシュボード アドオン

```console
NAME                                        READY   STATUS    RESTARTS   AGE
pod/grafana-7cf9794c74-mpfbp                1/1     Running   0          5m53s
pod/istio-ingressgateway-86b5dbdcb9-ndrp5   1/1     Running   0          5m57s
pod/istio-tracing-c98f4b8fc-zqklg           1/1     Running   0          82s
pod/istiod-6965c56995-4ph9h                 1/1     Running   0          6m15s
pod/kiali-7b44985d68-p87zh                  1/1     Running   0          81s
pod/prometheus-6868989549-5ghzz             1/1     Running   0          81s

NAME                                TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                                                      AGE
service/grafana                     ClusterIP      10.0.226.39    <none>         3000/TCP                                                     5m54s
service/istio-ingressgateway        LoadBalancer   10.0.143.56    20.53.72.254   15021:32166/TCP,80:31684/TCP,443:31302/TCP,15443:30863/TCP   5m57s
service/istiod                      ClusterIP      10.0.211.228   <none>         15010/TCP,15012/TCP,443/TCP,15014/TCP,853/TCP                6m16s
service/jaeger-agent                ClusterIP      None           <none>         5775/UDP,6831/UDP,6832/UDP                                   82s
service/jaeger-collector            ClusterIP      10.0.7.62      <none>         14267/TCP,14268/TCP,14250/TCP                                82s
service/jaeger-collector-headless   ClusterIP      None           <none>         14250/TCP                                                    82s
service/jaeger-query                ClusterIP      10.0.52.172    <none>         16686/TCP                                                    82s
service/kiali                       ClusterIP      10.0.71.179    <none>         20001/TCP                                                    82s
service/prometheus                  ClusterIP      10.0.171.151   <none>         9090/TCP                                                     82s
service/tracing                     ClusterIP      10.0.195.137   <none>         80/TCP                                                       82s
service/zipkin                      ClusterIP      10.0.136.111   <none>         9411/TCP                                                     82s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana                1/1     1            1           5m54s
deployment.apps/istio-ingressgateway   1/1     1            1           5m58s
deployment.apps/istio-tracing          1/1     1            1           83s
deployment.apps/istiod                 1/1     1            1           6m16s
deployment.apps/kiali                  1/1     1            1           83s
deployment.apps/prometheus             1/1     1            1           82s

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-7cf9794c74                1         1         1       5m54s
replicaset.apps/istio-ingressgateway-86b5dbdcb9   1         1         1       5m58s
replicaset.apps/istio-tracing-c98f4b8fc           1         1         1       83s
replicaset.apps/istiod-6965c56995                 1         1         1       6m16s
replicaset.apps/kiali-7b44985d68                  1         1         1       82s
replicaset.apps/prometheus-6868989549             1         1         1       82s

NAME                                                       REFERENCE                         TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istio-ingressgateway   Deployment/istio-ingressgateway   7%/80%    1         5         1          5m57s
horizontalpodautoscaler.autoscaling/istiod                 Deployment/istiod                 1%/80%    1         5         1          6m16s
```

Istio Operator のログを確認することで、インストールに関する追加の分析情報を得ることもできます。

```bash
kubectl logs -n istio-operator -l name=istio-operator -f
```

`istio-ingressgateway` に `<pending>` の外部 IP が表示された場合、Azure ネットワークによって IP アドレスが割り当てられるまで数分待ってください。

すべてのポッドの状態が `Running` と表示される必要があります。 ポッドのステータスが実行中ではない場合、実行中にナルまで数分待ってください。 ポッドから問題が報告された場合、[kubectl describe pod][kubectl-describe] コマンドを使用し、出力と状態を確認してください。

## <a name="accessing-the-add-ons"></a>アドオンにアクセスする

追加機能を提供する多数のアドオンが Istio Operator によってインストールされています。 アドオンの Web アプリが外部 IP アドレス経由で公開されることは **ありません**。 

アドオン ユーザー インターフェイスにアクセスするには、`istioctl dashboard` コマンドを使用します。 このコマンドでは、[kubectl port-forward][kubectl-port-forward] とランダム ポートを使用して、クライアント コンピューターと AKS クラスターの関連ポッドの間に安全な接続を作成します。 これにより、自動的に既定のブラウザーでアドオン Web アプリが開きます。

### <a name="grafana"></a>Grafana

Istio の分析と監視ダッシュボードが [Grafana][grafana] によって提供されます。 プロンプトが表示されたら、前に Grafana シークレットを使用して作成した資格情報を使用することを忘れないでください。 Grafana ダッシュボードを安全に開くには、次のコマンドを実行します。

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Istio のメトリックは [Prometheus][prometheus] によって提供されます。 Prometheus ダッシュボードを安全に開くには、次のコマンドを実行します。

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Istio でのトレースは [Jaeger][jaeger] によって提供されます。 Jaeger ダッシュボードを安全に開くには、次のコマンドを実行します。

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

サービス メッシュ可観測性ダッシュボードは [Kiali][kiali] によって提供されます。 プロンプトが表示されたら、前に Kiali シークレットを使用して作成した資格情報を使用することを忘れないでください。 Kiali ダッシュボードを安全に開くには、次のコマンドを実行します。

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

[Envoy][envoy] プロキシへのシンプルなインターフェイスが提供されています。 これは、指定されたポッドで実行されている Envoy プロキシの構成情報とメトリックを提供するものです。 Envoy インターフェイスを安全に開くには、次のコマンドを実行します。

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>AKS から Istio をアンインストールする

> [!WARNING]
> 実行中のシステムから Istio を削除すると、サービス間のトラフィックに関連する問題が発生することがあります。 進める前に、Istio なしでもシステムが正しく動作するようにプロビジョニングを作成することをお勧めします。

### <a name="remove-istio"></a>Istio を削除する

AKS クラスターから Istio を削除するには、前に追加した `istio-control-plane` という名前の `IstioOperator` リソースを削除します。 Istio Operator では、Istio Operator Spec が削除されたことが認識され、関連付けられているすべての Istio コンポーネントが削除されます。

```bash
kubectl delete istiooperator istio-control-plane -n istio-system
```

次のコマンドを実行すると、すべての Istio コンポーネントが削除されたことを確認できます。

```bash
kubectl get all -n istio-system
```

### <a name="remove-istio-operator"></a>Istio Operator を削除する

Istio が正常にアンインストールされたら、Istio Operator を削除することもできます。

```bash
istioctl operator remove
```

最後に `istio-` 名前空間を削除します。

```bash
kubectl delete ns istio-system
kubectl delete ns istio-operator
```

## <a name="next-steps"></a>次のステップ

Istio のインストール オプションと構成オプションを他にも見るには、次の公式の Istio に関するガイダンスをご覧ください。

- [Istio - インストール ガイド][istio-installation-guides]

以下を使用した追加のシナリオに沿って進めることもできます。

- [Istio Bookinfo Application の例][istio-bookinfo-example]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-install-operator]: https://istio.io/latest/docs/setup/install/operator/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[kubernetes-operator]: https://kubernetes.io/docs/concepts/extend-kubernetes/operator/
[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
