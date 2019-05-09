---
title: Azure Kubernetes Service (AKS) で Istio をインストールする
description: Istio をインストールして使用し、Azure Kubernetes Service (AKS) クラスターでサービス メッシュを作成する方法について説明します
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 12/3/2018
ms.author: pabouwer
ms.openlocfilehash: d85b830b63e2d52f3eeb5df8645edccfccf43c76
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58138152"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で Istio をインストールして使用する

[Istio][istio-github] は Kubernetes クラスターのマイクロサービス全体で重要な一連の機能を提供するオープンソースのサービス メッシュです。 これらの機能には、トラフィック管理、サービスの ID とセキュリティ、ポリシー適用、可観測性などがあります。 Istio の詳細については、公式ドキュメント「[What is Istio?][istio-docs-concepts]」 (Istio とは何か) を参照してください。

この記事では、Istio をインストールする方法について説明します。 Istio `istioctl` クライアント バイナリはクライアント コンピューターにインストールされます。それから、Istio コンポーネントが AKS の Kubernetes クラスターにインストールされます。 これらの手順は Istio バージョン *1.0.4* を参考にしています。 他の Istio バージョンは [GitHub - Istio Releases][istio-github-releases] で確認できます。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Istio をダウンロードする
> * Istio クライアント バイナリをインストールする
> * Istio Kubernetes コンポーネントをインストールする
> * インストールを検証する

## <a name="before-you-begin"></a>開始する前に

この記事で詳しく説明する手順では、AKS クラスターを作成済みで (Kubernetes 1.10 以上、RBAC を有効にする)、そのクラスターとの `kubectl` 接続が確立されていることを前提としています。 いずれかの項目でヘルプが必要な場合、[AKS クイック スタート][aks-quickstart]を参照してください。

Istio をインストールするには、[Helm][helm] バージョン *2.10.0* 以降をクラスターに正しくインストールし、構成しておく必要があります。 Helm のインストールでヘルプが必要な場合は、[AKS Helm インストール ガイド][helm-install]をご覧ください。 バージョン *2.10.0* 以降の Helm がインストールされていない場合は、アップグレードするか、「[Istio - Installation with Helm guide][istio-install-helm]」 (Istio - Helm インストール ガイド) を参照し、他のインストール方法を選択してください。

この記事では、Istio のインストール ガイドを個別のステップに分割しています。 各ステップが説明されているため、Istio のインストール方法や Istio が Kubernetes と連動するしくみを学習できます。 最終的な結果は、公式の Istio インストール [ガイド][istio-install-k8s-quickstart]と構造的に同じになります。

## <a name="download-istio"></a>Istio をダウンロードする

最初に Istio の最新版をダウンロードし、解凍します。 MacOS、Linux、Windows Subsystem for Linux の bash シェルと PowerShell シェルでは手順が若干異なります。 ご利用の環境に適したインストール手順をお選びください。

* [MacOS、Linux、Windows Subsystem for Linux の bash](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

MacOS の場合、`curl` を使用して Istio の最新版をダウンロードし、`tar` で次のように解凍します。

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

Linux または Windows Subsystem for Linux の場合、`curl` を使用して Istio の最新版をダウンロードし、`tar` で次のように解凍します。

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

### <a name="powershell"></a>PowerShell

PowerShell で [Invoke-WebRequest][Invoke-WebRequest] を使用して Istio の最新版をダウンロードし、[Expand-Archive][Expand-Archive] で次のように解凍します。

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.0.4"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

## <a name="install-the-istio-client-binary"></a>Istio クライアント バイナリをインストールする

`istioctl` クライアント バイナリはクライアント コンピューターで実行されます。このバイナリによって、Istio のルーティングの規則とポリシーを管理できます。 繰り返しになりますが、インストール手順はクライアント オペレーティング システムによって若干異なります。 ご利用の環境に適したインストール手順をお選びください。

> [!IMPORTANT]
> このセクションの手順は、必ずダウンロードして展開した Istio リリースの最上位フォルダーから実行してください。

### <a name="macos"></a>MacOS

MacOS で bash ベースのシェルで Istio `istioctl` クライアント バイナリをインストールするには、次のコマンドを使用します。 これらのコマンドによって、`istioctl` の標準ユーザー プログラムの場所に `PATH` クライアント バイナリがコピーされます。

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Istio `istioctl` クライアント バイナリに対してコマンドラインで補完する場合、次のように設定します。

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

次は [Istio Kubernetes コンポーネントをインストールする](#install-the-istio-kubernetes-components)セクションに移ります。

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux または Windows Subsystem for Linux

Linux または [Windows Subsystem for Linux][install-wsl] で bash ベースのシェルで Istio `istioctl` クライアント バイナリをインストールするには、次のコマンドを使用します。 これらのコマンドによって、`istioctl` の標準ユーザー プログラムの場所に `PATH` クライアント バイナリがコピーされます。

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Istio `istioctl` クライアント バイナリに対してコマンドラインで補完する場合、次のように設定します。

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

次は [Istio Kubernetes コンポーネントをインストールする](#install-the-istio-kubernetes-components)セクションに移ります。

### <a name="windows"></a>Windows

Windows で PowerShell ベースのシェルで Istio `istioctl` クライアント バイナリをインストールするには、次のコマンドを使用します。 これらのコマンドによって、新しいユーザー プログラムの場所に `istioctl` クライアント バイナリがコピーされ、`PATH` 経由で利用できるようになります。

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:/Program Files/Istio"
mv ./bin/istioctl.exe "C:/Program Files/Istio/"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Program Files\Istio\", "User")
```

## <a name="install-the-istio-kubernetes-components"></a>Istio Kubernetes コンポーネントをインストールする

> [!IMPORTANT]
> このセクションの手順は、必ずダウンロードして展開した Istio リリースの最上位フォルダーから実行してください。

> [!NOTE]
> Istio Helm チャートのバージョン `1.0.6` 以降には破壊的変更があります。 このバージョンのインストールを選択する場合は、Kiali のシークレットを手動で作成する必要があります。 また、`grafana.security.enabled=true` を設定している場合は、手動で Grafana のシークレットも作成する必要があります。 このようなシークレットを作成する方法の詳細については、Istio Helm チャートの [README.md](https://github.com/istio/istio/tree/master/install/kubernetes/helm/istio#installing-the-chart) を参照してください。

AKS クラスターに Istio コンポーネントをインストールするには、Helm を使用します。 Istio リソースを `istio-system` 名前空間にインストールし、セキュリティや監視の追加オプションを次のように有効にします。

```azurecli
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set grafana.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

Helm グラフでは、たくさんのオブジェクトが配置されます。 クラスター環境によっては、デプロイの完了に 2 分から 3 分かかります。

## <a name="validate-the-installation"></a>インストールを検証する

Istio が正常にデプロイされたことを確認するために、インストールを検証してみましょう。

最初に、サービスが期待どおりに作成されていることを確認します。 実行中のサービスを表示するには、[kubectl get svc][kubectl-get] コマンドを使用します。 *istio-system* 名前空間に照会します。この名前空間は Helm グラフによって Istio とアドオン コンポーネントがインストールされた場所です。

```console
kubectl get svc --namespace istio-system --output wide
```

次の出力例では、現在実行中のサービスを確認できます。

- *istio-** サービス
- *jaeger-**、*tracing*、*zipkin* アドオン トレーシング サービス
- *prometheus* アドオン メトリック サービス
- *grafana* アドオン分析と監視ダッシュボード サービス
- *kiali* アドオン サービス メッシュ ダッシュボード サービス

`istio-ingressgateway` に `<pending>` の外部 IP が表示された場合、Azure ネットワークによって IP アドレスが割り当てられるまで数分待ってください。

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                   AGE       SELECTOR
grafana                  ClusterIP      10.0.26.60     <none>           3000/TCP                                                                                                                  3m        app=grafana
istio-citadel            ClusterIP      10.0.88.87     <none>           8060/TCP,9093/TCP                                                                                                         3m        istio=citadel
istio-egressgateway      ClusterIP      10.0.115.115   <none>           80/TCP,443/TCP                                                                                                            3m        app=istio-egressgateway,istio=egressgateway
istio-galley             ClusterIP      10.0.104.183   <none>           443/TCP,9093/TCP                                                                                                          3m        istio=galley
istio-ingressgateway     LoadBalancer   10.0.12.216    52.187.250.239   80:31380/TCP,443:31390/TCP,31400:31400/TCP,15011:30469/TCP,8060:31999/TCP,853:31235/TCP,15030:32000/TCP,15031:30293/TCP   3m        app=istio-ingressgateway,istio=ingressgateway
istio-pilot              ClusterIP      10.0.38.134    <none>           15010/TCP,15011/TCP,8080/TCP,9093/TCP                                                                                     3m        istio=pilot
istio-policy             ClusterIP      10.0.253.81    <none>           9091/TCP,15004/TCP,9093/TCP                                                                                               3m        istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.181.186   <none>           443/TCP                                                                                                                   3m        istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.177.113   <none>           9091/TCP,15004/TCP,9093/TCP,42422/TCP                                                                                     3m        istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                3m        app=jaeger
jaeger-collector         ClusterIP      10.0.112.81    <none>           14267/TCP,14268/TCP                                                                                                       3m        app=jaeger
jaeger-query             ClusterIP      10.0.179.193   <none>           16686/TCP                                                                                                                 3m        app=jaeger
kiali                    ClusterIP      10.0.211.63    <none>           20001/TCP                                                                                                                 3m        app=kiali
prometheus               ClusterIP      10.0.70.113    <none>           9090/TCP                                                                                                                  3m        app=prometheus
tracing                  ClusterIP      10.0.139.121   <none>           80/TCP                                                                                                                    3m        app=jaeger
zipkin                   ClusterIP      10.0.60.155    <none>           9411/TCP                                                                                                                  3m        app=jaeger
```

次に、必要なポッドが作成されていることを確認します。 [kubectl get pods][kubectl-get] コマンドを使用し、再度、*istio-system* に照会します。

```console
kubectl get pods --namespace istio-system
```

次の出力例では、実行中のポッドを確認できます。

- *istio-** ポッド
- *prometheus-** アドオン メトリック ポッド
- *grafana-** アドオン分析と監視ダッシュボード ポッド
- *kiali* アドオン サービス メッシュ ダッシュボード ポッド

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-59b787b9b-cr6d7                  1/1       Running     0          6m
istio-citadel-78df8c67d9-9lfpf           1/1       Running     0          6m
istio-egressgateway-6b96cd7f5-k848h      1/1       Running     0          6m
istio-galley-58f566cb66-8mhbv            1/1       Running     0          6m
istio-ingressgateway-6cbbf596f6-6jz8g    1/1       Running     0          6m
istio-pilot-8449d555fc-sl6kp             2/2       Running     0          6m
istio-policy-6b99d88bc5-55s52            2/2       Running     0          6m
istio-sidecar-injector-b88dfb954-8m86s   1/1       Running     0          6m
istio-telemetry-675cb4cb9d-8s7wd         2/2       Running     0          6m
istio-tracing-7596597bd7-sbnt9           1/1       Running     0          6m
kiali-5fbd6ffb-4qcxw                     1/1       Running     0          6m
prometheus-76db5fddd5-2tkxs              1/1       Running     0          6m
```

ポッドのステータスはすべて `Running` です。 ポッドのステータスが実行中ではない場合、実行中にナルまで数分待ってください。 ポッドから問題が報告された場合、[kubectl describe pod][kubectl-describe] コマンドを使用し、出力とステータスを確認してください。

## <a name="accessing-the-add-ons"></a>アドオンにアクセスする

上記の例では、追加機能を提供するたくさんのアドオンが Istio にインストールされています。 アドオンのユーザー インターフェイスが外部 IP アドレス経由で公開されることはありません。 アドオン ユーザー インターフェイスにアクセスするには、[kubectl port-forward][kubectl-port-forward] コマンドを使用します。 このコマンドによって、クライアント コンピューターのローカル ポートと AKS クラスターの関連ポッドの間に安全な接続が作成されます。

### <a name="grafana"></a>Grafana

Istio の分析と監視ダッシュボードが [Grafana][grafana] によって提供されます。 AKS クラスターで Grafana を実行しているポッドのポート *3000* にクライアント コンピューターのローカル ポート *3000* を転送します。

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

次の出力例では、ポート転送が Grafana に構成されています。

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

これで、クライアント コンピューターから URL: [http://localhost:3000](http://localhost:3000) で Grafana にアクセスできます。

### <a name="prometheus"></a>Prometheus

メトリックの式ブラウザーは [Prometheus][prometheus] によって提供されます。 AKS クラスターで Prometheus を実行しているポッドのポート *9090* にクライアント コンピューターのローカル ポート *9090* を転送します。

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

次の出力例では、ポート転送が Prometheus に構成されています。

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

これで、クライアント コンピューターから URL: [http://localhost:9090](http://localhost:9090) で Prometheus 式ブラウザーにアクセスできます。

### <a name="jaeger"></a>Jaeger

トレーシング ユーザー インターフェイスは [Jaeger][jaeger] によって提供されます。 AKS クラスターで Jaeger を実行しているポッドのポート *16686* にクライアント コンピューターのローカル ポート *16686* を転送します。

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

次の出力例では、ポート転送が Jaeger に構成されています。

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

これで、クライアント コンピューターから URL: [http://localhost:16686](http://localhost:16686) で Jaeger トレーシング ユーザー インターフェイスにアクセスできます。

### <a name="kiali"></a>Kiali

サービス メッシュ可観測性ダッシュボードは [Kiali][kiali] によって提供されます。 AKS クラスターで Kiali を実行しているポッドのポート *20001* にクライアント コンピューターのローカル ポート *20001* を転送します。

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

次の出力例では、ポート転送が Kiali に構成されています。

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

これで、クライアント コンピューターから URL: [http://localhost:20001](http://localhost:20001) で Kiali r サービス メッシュ可観測性ダッシュボードにアクセスできます。

Kiali ダッシュボードの既定のユーザー名とパスワードは *username:admin/password:admin* です。この資格情報は Helm 値の *kiali.dashboard.username* と *kiali.dashboard.passphrase* を介して設定できます。

## <a name="next-steps"></a>次の手順

Istio を使用し、複数のバージョンのアプリケーション間でインテリジェントに経路を指定し、カナリア リリースをロールアウトする方法については、次のドキュメントを参照してください。

> [!div class="nextstepaction"]
> [AKS Istio インテリジェント経路指定シナリオ][istio-scenario-routing]

Istio のインストール オプションと構成オプションを他にも見るには、次の公式 Istio 記事をご覧ください。

- [Istio - Kubernetes インストール クイック スタート][istio-install-k8s-quickstart]
- [Istio - Helm インストール ガイド][istio-install-helm]
- [Istio - Helm インストール オプション][istio-install-helm-options]

[Istio Bookinfo Application 例][istio-bookinfo-example]を使用した追加のシナリオに沿って勧めることもできます。

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-k8s-quickstart]: https://istio.io/docs/setup/kubernetes/quick-start/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/helm-install/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10
[kubernetes-crd]: https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
[Invoke-WebRequest]: /powershell/module/microsoft.powershell.utility/invoke-webrequest
[Expand-Archive]: /powershell/module/Microsoft.PowerShell.Archive/Expand-Archive
