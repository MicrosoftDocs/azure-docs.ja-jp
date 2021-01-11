---
title: Azure Kubernetes Service (AKS) で Istio をインストールする
description: Istio をインストールして使用し、Azure Kubernetes Service (AKS) クラスターでサービス メッシュを作成する方法について説明します
author: paulbouwer
ms.topic: article
ms.date: 02/19/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 5d47bb7f1f9c1ce39239d6f6ee5826a60ce1741a
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86244169"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で Istio をインストールして使用する

[Istio][istio-github] はオープンソースのサービス メッシュであり、Kubernetes クラスターのマイクロサービスに対して重要な機能のセットが提供されます。 これらの機能には、トラフィック管理、サービスの ID とセキュリティ、ポリシー適用、可観測性などがあります。 Istio について詳しくは、公式ドキュメント「[Istio とは][istio-docs-concepts]」をご覧ください。

この記事では、Istio をインストールする方法について説明します。 Istio `istioctl` クライアント バイナリはクライアント コンピューターにインストールされます。それから、Istio コンポーネントが AKS の Kubernetes クラスターにインストールされます。

> [!NOTE]
> 次の手順は Istio バージョン `1.4.0` を参考にしています。
>
> Istio `1.4.x` リリースは、Istio チームによって Kubernetes バージョン `1.13`、`1.14`、`1.15` に対してテストされています。 Istio のその他のバージョンは [GitHub の Istio リリース][istio-github-releases]に関するページにあります。各リリースに関する情報は [Istio のニュース][istio-release-notes]を参照してください。サポートされている Kubernetes のバージョンについては、[Istio の全般的な FAQ][istio-faq] のページを参照してください。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Istio istioctl クライアント バイナリをダウンロードしてインストールする
> * AKS に Istio をインストールする
> * Istio インストールを検証する
> * アドオンにアクセスする
> * AKS から Istio をアンインストールする

## <a name="before-you-begin"></a>開始する前に

この記事で詳しく説明する手順では、AKS クラスターを作成済みで (Kubernetes `1.13`以上、RBAC を有効にする)、そのクラスターとの `kubectl` 接続が確立されていることを前提としています。 いずれかの項目でヘルプが必要な場合、[AKS クイック スタート][aks-quickstart]を参照してください。

[Istio のパフォーマンスとスケーラビリティ](https://istio.io/docs/concepts/performance-and-scalability/)に関するドキュメントを読み、AKS クラスターで Istio を実行するための追加リソース要件を理解しておいてください。 コアとメモリの要件は、特定のワークロードに基づいて変わります。 セットアップに適したノード数と VM サイズを選択します。

この記事では、Istio のインストール ガイドを個別のステップに分割しています。 最終的な結果は、公式の Istio インストール [ガイド][istio-install-istioctl]と構造的に同じになります。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-components-on-aks"></a>Istio コンポーネントを AKS にインストールする

[Grafana][grafana] と [Kiali][kiali] を Istio インストールの一部としてインストールします。 Grafana では分析と監視のダッシュボードが提供され、Kiali ではサービス メッシュ監視ダッシュボードが提供されます。 ご使用のセットアップでは、これらのコンポーネントそれぞれに資格情報が必要であり、[シークレット][kubernetes-secrets]として提供する必要があります。

Istio コンポーネントをインストールするには、前もって Grafana と Kiali 両方のシークレットを作成する必要があります。 これらのシークレットは、Istio で使用される `istio-system` 名前空間にインストールする必要があるため、名前空間も作成する必要があります。 `kubectl create` によって名前空間を作成する場合は、`--save-config` オプションを使用する必要があります。これにより、Istio インストーラーは、今後このオブジェクトで `kubectl apply` を実行できるようになります。

```console
kubectl create namespace istio-system --save-config
```

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - create secrets for Grafana and Kiali](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash check for CRDs](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell check for CRDs](includes/servicemesh/istio/install-create-secrets-powershell.md)]

::: zone-end

### <a name="install-istio-components"></a>Istio コンポーネントをインストールする

AKS クラスターに Grafana および Kiali シークレットを正常に作成したところで、Istio コンポーネントをインストールします。 

今後、Istio に対する [Helm][helm] のインストール方法は非推奨となります。 Istio の新しいインストール方法では、`istioctl` クライアント バイナリ、[Istio 構成プロファイル][istio-configuration-profiles]、および新しい [Istio コントロール プレーンの仕様と API][istio-control-plane] を利用しています。 この新しいアプローチを使用して、Istio のインストールを行います。

> [!NOTE]
> 現時点で Istio は Linux ノード上で実行するようにスケジュールする必要があります。 クラスター内に Windows Server ノードがある場合、Istio ポッドが確実に Linux ノードでのみ実行されるようにスケジュールする必要があります。 [ノードのセレクター][kubernetes-node-selectors]を使用して、ポッドが適切なノードにスケジュールされていることを確認します。

> [!CAUTION]
> [SDS (シークレット検出サービス)][istio-feature-sds] と [Istio CNI][istio-feature-cni] の機能は現在[アルファ版][istio-feature-stages]であるため、これらを有効にする場合は事前に十分な考慮をする必要があります。 
>
> 現在、[サービス アカウント トークン ボリューム プロジェクション][kubernetes-feature-sa-projected-volume] Kubernetes 機能 (SDS の要件) は、AKS における Kubernetes 1.13 以上の全バージョンで**有効**になっています。

次のコンテンツを含む `istio.aks.yaml` という名前のファイルを作成します。 このファイルには、Istio を構成するための [Istio コントロール プレーンの仕様][istio-control-plane]に関する詳細が保持されます。

```yaml
apiVersion: install.istio.io/v1alpha2
kind: IstioControlPlane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
      # Enable mutual TLS for the control plane
      controlPlaneSecurityEnabled: true
      mtls:
        # Require all service to service communication to have mtls
        enabled: false
    grafana:
      # Enable Grafana deployment for analytics and monitoring dashboards
      enabled: true
      security:
        # Enable authentication for Grafana
        enabled: true
    kiali:
      # Enable the Kiali deployment for a service mesh observability dashboard
      enabled: true
    tracing:
      # Enable the Jaeger deployment for tracing
      enabled: true
```

次のように、`istioctl apply` コマンドと上記の `istio.aks.yaml` Istio コントロール プレーンの仕様ファイルを使用して、Istio をインストールします。

```console
istioctl manifest apply -f istio.aks.yaml --logtostderr --set installPackagePath=./install/kubernetes/operator/charts
```

インストーラーは、多数の [CRD][kubernetes-crd] をデプロイし、依存関係を管理して、この Istio の構成で定義されているすべての関連オブジェクトをインストールします。 次のような出力スニペットが表示されます。

```console
Applying manifests for these components:
- Tracing
- EgressGateway
- NodeAgent
- Grafana
- Policy
- Citadel
- CertManager
- IngressGateway
- Injector
- Prometheus
- PrometheusOperator
- Kiali
- Telemetry
- Galley
- Cni
- Pilot
- Base
- CoreDNS
NodeAgent is waiting on a prerequisite...
Telemetry is waiting on a prerequisite...
Galley is waiting on a prerequisite...
Cni is waiting on a prerequisite...
Grafana is waiting on a prerequisite...
Policy is waiting on a prerequisite...
Citadel is waiting on a prerequisite...
EgressGateway is waiting on a prerequisite...
Tracing is waiting on a prerequisite...
Kiali is waiting on a prerequisite...
PrometheusOperator is waiting on a prerequisite...
IngressGateway is waiting on a prerequisite...
Prometheus is waiting on a prerequisite...
CertManager is waiting on a prerequisite...
Injector is waiting on a prerequisite...
Pilot is waiting on a prerequisite...
Applying manifest for component Base
Waiting for CRDs to be applied.
CRDs applied.
Finished applying manifest for component Base
Prerequisite for Tracing has completed, proceeding with install.
Prerequisite for Injector has completed, proceeding with install.
Prerequisite for Telemetry has completed, proceeding with install.
Prerequisite for Policy has completed, proceeding with install.
Prerequisite for PrometheusOperator has completed, proceeding with install.
Prerequisite for NodeAgent has completed, proceeding with install.
Prerequisite for IngressGateway has completed, proceeding with install.
Prerequisite for Kiali has completed, proceeding with install.
Prerequisite for EgressGateway has completed, proceeding with install.
Prerequisite for Galley has completed, proceeding with install.
Prerequisite for Grafana has completed, proceeding with install.
Prerequisite for Cni has completed, proceeding with install.
Prerequisite for Citadel has completed, proceeding with install.
Applying manifest for component Tracing
Prerequisite for Prometheus has completed, proceeding with install.
Prerequisite for Pilot has completed, proceeding with install.
Prerequisite for CertManager has completed, proceeding with install.
Applying manifest for component Kiali
Applying manifest for component Prometheus
Applying manifest for component IngressGateway
Applying manifest for component Policy
Applying manifest for component Telemetry
Applying manifest for component Citadel
Applying manifest for component Galley
Applying manifest for component Pilot
Applying manifest for component Injector
Applying manifest for component Grafana
Finished applying manifest for component Kiali
Finished applying manifest for component Tracing
Finished applying manifest for component Prometheus
Finished applying manifest for component Citadel
Finished applying manifest for component Policy
Finished applying manifest for component IngressGateway
Finished applying manifest for component Injector
Finished applying manifest for component Galley
Finished applying manifest for component Pilot
Finished applying manifest for component Grafana
Finished applying manifest for component Telemetry

Component IngressGateway installed successfully:
================================================

serviceaccount/istio-ingressgateway-service-account created
deployment.apps/istio-ingressgateway created
gateway.networking.istio.io/ingressgateway created
sidecar.networking.istio.io/default created
poddisruptionbudget.policy/ingressgateway created
horizontalpodautoscaler.autoscaling/istio-ingressgateway created
service/istio-ingressgateway created

...
```

この時点で、Istio が AKS クラスターにデプロイされています。 Istio が正常にデプロイされたことを確認するには、次の [Istio インストールを検証する](#validate-the-istio-installation)セクションに進みます。

## <a name="validate-the-istio-installation"></a>Istio インストールを検証する

最初に、サービスが期待どおりに作成されていることを確認します。 実行中のサービスを表示するには、[kubectl get svc][kubectl-get] コマンドを使用します。 `istio-system` 名前空間に照会します。この名前空間は `istio` Helm Chart によって Istio とアドオン コンポーネントがインストールされた場所です。

```console
kubectl get svc --namespace istio-system --output wide
```

次の出力例では、現在実行中のサービスを確認できます。

- `istio-*` サービス
- `jaeger-*`、`tracing`、および `zipkin` アドオン トレーシング サービス
- `prometheus` アドオン メトリック サービス
- `grafana` アドオン分析と監視ダッシュボード サービス
- `kiali`アドオン サービス メッシュ ダッシュボード サービス

`istio-ingressgateway` に `<pending>` の外部 IP が表示された場合、Azure ネットワークによって IP アドレスが割り当てられるまで数分待ってください。

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.116.147   <none>           3000/TCP                                                                                                                     92s   app=grafana
istio-citadel            ClusterIP      10.0.248.152   <none>           8060/TCP,15014/TCP                                                                                                           94s   app=citadel
istio-galley             ClusterIP      10.0.50.100    <none>           443/TCP,15014/TCP,9901/TCP,15019/TCP                                                                                         93s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.36.213    20.188.221.111   15020:30369/TCP,80:31368/TCP,443:30045/TCP,15029:32011/TCP,15030:31212/TCP,15031:32411/TCP,15032:30009/TCP,15443:30010/TCP   93s   app=istio-ingressgateway
istio-pilot              ClusterIP      10.0.23.222    <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                       93s   istio=pilot
istio-policy             ClusterIP      10.0.59.250    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                 93s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.123.219   <none>           443/TCP                                                                                                                      93s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.216.9     <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                       89s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                   96s   app=jaeger
jaeger-collector         ClusterIP      10.0.221.24    <none>           14267/TCP,14268/TCP,14250/TCP                                                                                                95s   app=jaeger
jaeger-query             ClusterIP      10.0.46.154    <none>           16686/TCP                                                                                                                    95s   app=jaeger
kiali                    ClusterIP      10.0.174.97    <none>           20001/TCP                                                                                                                    94s   app=kiali
prometheus               ClusterIP      10.0.245.226   <none>           9090/TCP                                                                                                                     94s   app=prometheus
tracing                  ClusterIP      10.0.249.95    <none>           9411/TCP                                                                                                                     95s   app=jaeger
zipkin                   ClusterIP      10.0.154.89    <none>           9411/TCP                                                                                                                     94s   app=jaeger
```

次に、必要なポッドが作成されていることを確認します。 [kubectl get pods][kubectl-get] コマンドを使用し、再度、`istio-system` 名前空間に照会します。

```console
kubectl get pods --namespace istio-system
```

次の出力例では、実行中のポッドを確認できます。

- `istio-*` ポッド
- `prometheus-*` アドオン メトリック ポッド
- `grafana-*` アドオン分析と監視ダッシュボード ポッド
- `kiali` アドオン サービス メッシュ ダッシュボード ポッド

```console
NAME                                          READY   STATUS    RESTARTS   AGE
grafana-6bc97ff99-k9sk4                       1/1     Running   0          92s
istio-citadel-6b5c754454-tb8nf                1/1     Running   0          94s
istio-galley-7d6d78d7c5-zshsd                 2/2     Running   0          94s
istio-ingressgateway-85869c5cc7-x5d76         1/1     Running   0          95s
istio-pilot-787d6995b5-n5vrj                  2/2     Running   0          94s
istio-policy-6cf4fbc8dc-sdsg5                 2/2     Running   2          94s
istio-sidecar-injector-5d5b978668-wrz2s       1/1     Running   0          94s
istio-telemetry-5498db684-6kdnw               2/2     Running   1          94s
istio-tracing-78548677bc-74tx6                1/1     Running   0          96s
kiali-59b7fd7f68-92zrh                        1/1     Running   0          95s
prometheus-7c7cf9dbd6-rjxcv                   1/1     Running   0          94s
```

すべてのポッドの状態が `Running` と表示される必要があります。 ポッドのステータスが実行中ではない場合、実行中になるまで数分待ってください。 ポッドから問題が報告された場合、[kubectl describe pod][kubectl-describe] コマンドを使用し、出力と状態を確認してください。

## <a name="accessing-the-add-ons"></a>アドオンにアクセスする

上記の例では、追加機能を提供するたくさんのアドオンが Istio によってインストールされています。 アドオンの Web アプリが外部 IP アドレス経由で公開されることは**ありません**。 

アドオン ユーザー インターフェイスにアクセスするには、`istioctl dashboard` コマンドを使用します。 このコマンドでは、[kubectl port-forward][kubectl-port-forward] とランダム ポートを活用して、クライアント コンピューターと AKS クラスターの関連ポッドの間に安全な接続を作成します。 これにより、自動的に既定のブラウザーでアドオン Web アプリが開きます。

この記事の前半で Grafana と Kiali の資格情報を指定してさらにセキュリティのレベルを高めています。

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

### <a name="remove-istio-components-and-namespace"></a>Istio コンポーネントおよび名前空間を削除する

AKS クラスターから Istio を削除するには、`istio.aks.yaml` Istio コントロール プレーンの仕様ファイルと共に `istioctl manifest generate` コマンドを使用します。 これにより、展開したマニフェストが生成されます。これは、インストールされているすべてのコンポーネントと `istio-system` 名前空間を削除するために `kubectl delete` にパイプします。

```console
istioctl manifest generate -f istio.aks.yaml -o istio-components-aks --logtostderr --set installPackagePath=./install/kubernetes/operator/charts 

kubectl delete -f istio-components-aks -R
```

### <a name="remove-istio-crds-and-secrets"></a>Istio CRD とシークレットを削除する

上記のコマンドによってすべての Istio コンポーネントと名前空間が削除されますが、まだ生成された Istio シークレットが残っています。 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>次のステップ

次のドキュメントでは、カナリア リリースを展開するために Istio を使用してインテリジェント ルーティングを提供する方法を説明しています。

> [!div class="nextstepaction"]
> [AKS Istio インテリジェント経路指定シナリオ][istio-scenario-routing]

Istio のインストール オプションと構成オプションを他にも見るには、次の公式の Istio に関するガイダンスをご覧ください。

- [Istio - インストール ガイド][istio-installation-guides]

以下を使用した追加のシナリオに沿って進めることもできます。

- [Istio Bookinfo Application の例][istio-bookinfo-example]

Application Insights と Istio を使用して AKS アプリケーションを監視する方法については、次の Azure Monitor のドキュメントを参照してください。

- [Kubernetes でホストされるアプリケーションに対するゼロ インストルメンテーション アプリケーション監視][app-insights]

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
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: /windows/wsl/install-win10

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

[app-insights]: ../azure-monitor/app/kubernetes.md

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./servicemesh-istio-scenario-routing.md
