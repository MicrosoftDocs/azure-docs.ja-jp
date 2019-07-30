---
title: Azure Kubernetes Service (AKS) で Istio をインストールする
description: Istio をインストールして使用し、Azure Kubernetes Service (AKS) クラスターでサービス メッシュを作成する方法について説明します
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 9d973cb2ac210e912d93941a2f81889557379f43
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625986"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で Istio をインストールして使用する

[Istio][istio-github] is an open-source service mesh that provides a key set of functionality across the microservices in a Kubernetes cluster. These features include traffic management, service identity and security, policy enforcement, and observability. For more information about Istio, see the official [What is Istio?][istio-docs-concepts] ドキュメント。

この記事では、Istio をインストールする方法について説明します。 Istio `istioctl` クライアント バイナリはクライアント コンピューターにインストールされます。それから、Istio コンポーネントが AKS の Kubernetes クラスターにインストールされます。

> [!NOTE]
> これらの手順は Istio バージョン `1.1.3` を参考にしています。
>
> Istio `1.1.x` リリースは、Istio チームによって Kubernetes バージョン `1.11`、`1.12`、`1.13` に対してテストされています。 他の Istio バージョンは [GitHub - Istio Releases][istio-github-releases] and information about each of the releases at [Istio - Release Notes][istio-release-notes] で確認できます。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Istio をダウンロードする
> * Istio istioctl クライアント バイナリをインストールする
> * Istio CRD を AKS にインストールする
> * Istio コンポーネントを AKS にインストールする
> * Istio インストールを検証する
> * アドオンにアクセスする
> * AKS から Istio をアンインストールする

## <a name="before-you-begin"></a>開始する前に

この記事で詳しく説明する手順では、AKS クラスターを作成済みで (Kubernetes `1.11`以上、RBAC を有効にする)、そのクラスターとの `kubectl` 接続が確立されていることを前提としています。 いずれかの項目でヘルプが必要な場合、[AKS クイック スタート][aks-quickstart]を参照してください。

これらの手順に従い Istio をインストールするには [Helm][helm] が必要です。 バージョン `2.12.2` 以降をクラスターに正しくインストールし、構成しておくことをお勧めします。 Helm のインストールでヘルプが必要な場合は、[AKS Helm インストール ガイド][helm-install]をご覧ください。 Linux ノード上で実行するようにすべての Istio ポッドをスケジュールすることも必要です。

この記事では、Istio のインストール ガイドを個別のステップに分割しています。 最終的な結果は、公式の Istio インストール [ガイド][istio-install-helm]と構造的に同じになります。

## <a name="download-istio"></a>Istio をダウンロードする

最初に Istio の最新版をダウンロードし、解凍します。 MacOS、Linux、Windows Subsystem for Linux の bash シェルと PowerShell シェルでは手順が若干異なります。 ご利用の環境に適したインストール手順いずれかをお選びください。

* [MacOS、Linux、Windows Subsystem for Linux の bash](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

MacOS の場合、`curl` を使用して Istio の最新版をダウンロードし、`tar` で次のように解凍します。

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

Linux または Windows Subsystem for Linux の場合、`curl` を使用して Istio の最新版をダウンロードし、`tar` で次のように解凍します。

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

次は [Istio istioctl クライアント ライブラリをインストールする](#install-the-istio-istioctl-client-binary)セクションに進みます。

### <a name="powershell"></a>PowerShell

PowerShell の場合、`Invoke-WebRequest` を使用して Istio の最新版をダウンロードし、`Expand-Archive` で次のように解凍します。

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
# Use TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

次は [Istio istioctl クライアント ライブラリをインストールする](#install-the-istio-istioctl-client-binary)セクションに進みます。

## <a name="install-the-istio-istioctl-client-binary"></a>Istio istioctl クライアント バイナリをインストールする

> [!IMPORTANT]
> このセクションの手順は、必ずダウンロードして展開した Istio リリースの最上位フォルダーから実行してください。

`istioctl` クライアント バイナリはクライアント コンピューターで実行されます。このバイナリによって、Istio サービス メッシュの対話型操作が可能になります。 インストール手順はクライアント オペレーティング システムによって若干異なります。 ご利用の環境に適したインストール手順いずれかをお選びください。

* [MacOS](#macos)
* [Linux または Windows Subsystem for Linux](#linux-or-windows-subsystem-for-linux)
* [Windows](#windows)

### <a name="macos"></a>MacOS

MacOS で bash ベースのシェルで Istio `istioctl` クライアント バイナリをインストールするには、次のコマンドを使用します。 これらのコマンドによって、`istioctl` の標準ユーザー プログラムの場所に `PATH` クライアント バイナリがコピーされます。

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
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

次は [Istio CRD を AKS にインストールする](#install-the-istio-crds-on-aks)セクションに進みます。

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux または Windows Subsystem for Linux

Linux または [Windows Subsystem for Linux][install-wsl] で bash ベースのシェルで Istio `istioctl` クライアント バイナリをインストールするには、次のコマンドを使用します。 これらのコマンドによって、`istioctl` の標準ユーザー プログラムの場所に `PATH` クライアント バイナリがコピーされます。

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
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

次は [Istio CRD を AKS にインストールする](#install-the-istio-crds-on-aks)セクションに進みます。

### <a name="windows"></a>Windows

Windows で **Powershell** ベースのシェルで Istio `istioctl` クライアント バイナリをインストールするには、次のコマンドを使用します。 これらのコマンドによって、Istio フォルダーに `istioctl` クライアント バイナリがコピーされ、即座 (現在のシェル内で) かつ永続的 (シェルの再起動により) に `PATH` 経由で利用できるようになります。 これらのコマンドの実行には昇格された (管理者) 特権は必要ありません。また、シェルを再起動する必要はありません。

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```

次は [Istio CRD を AKS にインストールする](#install-the-istio-crds-on-aks)セクションに進みます。

## <a name="install-the-istio-crds-on-aks"></a>Istio CRD を AKS にインストールする

> [!IMPORTANT]
> このセクションの手順は、必ずダウンロードして展開した Istio リリースの最上位フォルダーから実行してください。

Istio は [Custom Resource Definitions (CRD)][kubernetes-crd] を使用してランタイム構成を管理します。 Istio コンポーネントには Istio CRD に対する依存関係があるため、最初にインストールする必要があります。 Helm と `istio-init` チャートを使用して Istio CRD を AKS クラスターの `istio-system` 名前空間にインストールします。

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

[ジョブ][kubernetes-jobs]が、CRD をインストールする `istio-init` Helm Chart の一部としてデプロイされます。 これらのジョブは、クラスター環境によって異なりますが、完了するまでに 1 - 2 分かかります。 ジョブが正常に完了したことは次のように確認できます。

```azurecli
kubectl get jobs -n istio-system
```

次の出力例では、正常に完了したジョブが示されています。

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

ジョブの正常な完了を確認したところで、正しい数の Istio CRD がインストールされたことを確認します。 環境で適切なコマンドを実行すると、全部で 53 の Istio CRD がインストールされたことを確認できます。 コマンドから数 `53` が返されます。

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

Powershell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

この時点まで進んだということは、Istio CRD のインストールが正常に行われたことを意味します。 次は [Istio コンポーネントを AKS にインストールする](#install-the-istio-components-on-aks)セクションに進みます。

## <a name="install-the-istio-components-on-aks"></a>Istio コンポーネントを AKS にインストールする

> [!IMPORTANT]
> このセクションの手順は、必ずダウンロードして展開した Istio リリースの最上位フォルダーから実行してください。

[Grafana][grafana] and [Kiali][kiali] を Istio インストールの一部としてインストールします。 Grafana では分析と監視のダッシュボードが提供され、Kiali ではサービス メッシュ監視ダッシュボードが提供されます。 このセットアップでは、これらのコンポーネントそれぞれに資格情報が必要であり、[シークレット][kubernetes-secrets] として提供する必要があります。

Istio コンポーネントをインストールするには、前もって Grafana と Kiali 両方のシークレットを作成する必要があります。 ご使用の環境で適切なコマンドを実行してこれらのシークレットを作成します。

### <a name="add-grafana-secret"></a>Grafana のシークレットを追加する

`REPLACE_WITH_YOUR_SECURE_PASSWORD` トークンを自分のパスワードで置き換え、次のコマンドを実行します。

#### <a name="macos-linux"></a>MacOS、Linux

```bash
GRAFANA_USERNAME=$(echo -n "grafana" | base64)
GRAFANA_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$GRAFANA_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("grafana"))
$GRAFANA_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE" | kubectl apply -f -
```

### <a name="add-kiali-secret"></a>Kiali のシークレットを追加する

`REPLACE_WITH_YOUR_SECURE_PASSWORD` トークンを自分のパスワードで置き換え、次のコマンドを実行します。

#### <a name="macos-linux"></a>MacOS、Linux

```bash
KIALI_USERNAME=$(echo -n "kiali" | base64)
KIALI_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$KIALI_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("kiali"))
$KIALI_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE" | kubectl apply -f -
```

### <a name="install-istio-components"></a>Istio コンポーネントをインストールする

AKS クラスターに Grafana および Kiali シークレットを正常に作成したところで、Istio コンポーネントをインストールします。 Helm と `istio` チャートを使用して Istio コンポーネントを AKS クラスターの `istio-system` 名前空間にインストールします。 実際の環境に合った適切なコマンドを使用してください。

> [!NOTE]
> このインストールにおいては次のオプションを使用しています。
> - `global.controlPlaneSecurityEnabled=true` - コントロール プレーンの相互 TLS を有効化します
> - `mixer.adapters.useAdapterCRDs=false` - ミキサー アダプター CRD のウォッチを削除します (非推奨になることが予定されており、削除によりパフォーマンスが向上するため)
> - `grafana.enabled=true` - 分析よび監視ダッシュボードのための Grafana のデプロイを有効化します
> - `grafana.security.enabled=true` - Grafana の認証を有効化します
> - `tracing.enabled=true` - トレースのための Jaeger のデプロイを有効化します
> - `kiali.enabled=true` - サービス メッシュ監視ダッシュボードのための Kiali のデプロイを有効化します

Bash

```bash
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set mixer.adapters.useAdapterCRDs=false \
  --set grafana.enabled=true --set grafana.security.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

Powershell

```powershell
helm install install/kubernetes/helm/istio --name istio --namespace istio-system `
  --set global.controlPlaneSecurityEnabled=true `
  --set mixer.adapters.useAdapterCRDs=false `
  --set grafana.enabled=true --set grafana.security.enabled=true `
  --set tracing.enabled=true `
  --set kiali.enabled=true
```

`istio`Helm Chart によって、たくさんのオブジェクトがデプロイされます。 上記の `helm install` コマンドの出力で一覧を確認できます。 Istio コンポーネントのデプロイには、クラスター環境によっては、完了するまで 4 分から 5 分かかります。

> [!NOTE]
> Linux ノード上で実行するようにすべての Istio ポッドをスケジュールすることが必要です。 クラスター上に Linux ノード プールの他に Windows Server ノード プールがある場合は、すべての Istio ポッドが Linux ノードで実行するようにスケジュールされていることを確認します。

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
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                                                                                                                      AGE       SELECTOR
grafana                  ClusterIP      10.0.81.182    <none>          3000/TCP                                                                                                                                     119s      app=grafana
istio-citadel            ClusterIP      10.0.96.33     <none>          8060/TCP,15014/TCP                                                                                                                           119s      istio=citadel
istio-galley             ClusterIP      10.0.237.158   <none>          443/TCP,15014/TCP,9901/TCP                                                                                                                   119s      istio=galley
istio-ingressgateway     LoadBalancer   10.0.154.12    20.188.211.19   15020:30603/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:31198/TCP,15030:30610/TCP,15031:30937/TCP,15032:31344/TCP,15443:31499/TCP   119s      app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.178.56    <none>          15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       119s      istio=pilot
istio-policy             ClusterIP      10.0.116.118   <none>          9091/TCP,15004/TCP,15014/TCP                                                                                                                 119s      istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.31.160    <none>          443/TCP                                                                                                                                      119s      istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.187.246   <none>          9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       119s      istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>          5775/UDP,6831/UDP,6832/UDP                                                                                                                   119s      app=jaeger
jaeger-collector         ClusterIP      10.0.116.63    <none>          14267/TCP,14268/TCP                                                                                                                          119s      app=jaeger
jaeger-query             ClusterIP      10.0.22.108    <none>          16686/TCP                                                                                                                                    119s      app=jaeger
kiali                    ClusterIP      10.0.142.50    <none>          20001/TCP                                                                                                                                    119s      app=kiali
prometheus               ClusterIP      10.0.138.134   <none>          9090/TCP                                                                                                                                     119s      app=prometheus
tracing                  ClusterIP      10.0.165.210   <none>          80/TCP                                                                                                                                       118s      app=jaeger
zipkin                   ClusterIP      10.0.126.211   <none>          9411/TCP                                                                                                                                     118s      app=jaeger
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
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-88779954d-nzpm7                  1/1       Running     0          6m26s
istio-citadel-7f699dc8c8-n7q8g           1/1       Running     0          6m26s
istio-galley-649bc8cd97-wfjzm            1/1       Running     0          6m26s
istio-ingressgateway-65dfbd566-42wkn     1/1       Running     0          6m26s
istio-init-crd-10-tmtw5                  0/1       Completed   0          20m38s
istio-init-crd-11-ql25l                  0/1       Completed   0          20m38s
istio-pilot-958dd8cc4-4ckf9              2/2       Running     0          6m26s
istio-policy-86b4b7cf9-zf7v7             2/2       Running     4          6m26s
istio-sidecar-injector-d48786c5c-pmrj9   1/1       Running     0          6m26s
istio-telemetry-7f6996fdcc-84w94         2/2       Running     3          6m26s
istio-tracing-79db5954f-h7hmz            1/1       Running     0          6m26s
kiali-5c4cdbb869-s28dv                   1/1       Running     0          6m26s
prometheus-67599bf55b-pgxd8              1/1       Running     0          6m26s
```

`Completed` ステータスの 2 つの `istio-init-crd-*` ポッドがあります。 これらのポッドは、前の手順で CRD を作成したジョブの実行に使用されました。 他のすべてのポッドのステータスは `Running` と表示される必要があります。 ポッドのステータスが実行中ではない場合、実行中にナルまで数分待ってください。 ポッドから問題が報告された場合、[kubectl describe pod][kubectl-describe] コマンドを使用し、出力と状態を確認してください。

## <a name="accessing-the-add-ons"></a>アドオンにアクセスする

上記の例では、追加機能を提供するたくさんのアドオンが Istio にインストールされています。 アドオンのユーザー インターフェイスが外部 IP アドレス経由で公開されることはありません。 アドオン ユーザー インターフェイスにアクセスするには、[kubectl port-forward][kubectl-port-forward] コマンドを使用します。 このコマンドによって、クライアント コンピューターと AKS クラスターの関連ポッドの間に安全な接続が作成されます。

この記事の前半で Grafana と Kiali の資格情報を指定してさらにセキュリティのレベルを高めています。

### <a name="grafana"></a>Grafana

Istio の分析と監視ダッシュボードが [Grafana][grafana] によって提供されます。 AKS クラスターで Grafana を実行しているポッドのポート `3000` にクライアント コンピューターのローカル ポート `3000` を転送します。

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

次の出力例では、ポート転送が Grafana に構成されています。

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

これで、クライアント コンピューターから URL: [http://localhost:3000](http://localhost:3000) で Grafana にアクセスできます。 プロンプトが表示されたら、前に Grafana シークレットを使用して作成した資格情報を使用することを忘れないでください。

### <a name="prometheus"></a>Prometheus

Istio のメトリックは [Prometheus][prometheus] によって提供されます。 AKS クラスターで Prometheus を実行しているポッドのポート `9090` にクライアント コンピューターのローカル ポート `9090` を転送します。

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

Istio でのトレースは [Jaeger][jaeger] によって提供されます。 AKS クラスターで Jaeger を実行しているポッドのポート `16686` にクライアント コンピューターのローカル ポート `16686` を転送します。

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

サービス メッシュ可観測性ダッシュボードは [Kiali][kiali] によって提供されます。 AKS クラスターで Kiali を実行しているポッドのポート `20001` にクライアント コンピューターのローカル ポート `20001` を転送します。

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

次の出力例では、ポート転送が Kiali に構成されています。

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

これで、クライアント コンピューターから URL: [http://localhost:20001/kiali/console/](http://localhost:20001/kiali/console/) で Kiali r サービス メッシュ可観測性ダッシュボードにアクセスできます。 プロンプトが表示されたら、前に Kiali シークレットを使用して作成した資格情報を使用することを忘れないでください。

## <a name="uninstall-istio-from-aks"></a>AKS から Istio をアンインストールする

> [!WARNING]
> 実行中のシステムから Istio を削除すると、サービス間のトラフィックに関連する問題が発生することがあります。 進める前に、Istio なしでもシステムが正しく動作するようにプロビジョニングを作成することをお勧めします。

### <a name="remove-istio-components-and-namespace"></a>Istio コンポーネントおよび名前空間を削除する

Istio を AKS クラスターから削除するには、次のコマンドを使用します。 `helm delete` コマンドによって `istio` および `istio-init` チャートが削除され、`kubectl delete ns` コマンドによって `istio-system` 名前空間が削除されます。

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Istio CRD を削除する

上記のコマンドによってすべての Istio コンポーネントと名前空間が削除されますが、まだ Istio CRD が残っています。 CRD を削除するには、次のいずれかの方法を使用できます。

方法 #1 - このコマンドでは、ダウンロードして解凍した Istio リリース (Istio のインストールに使用したもの) の最上位フォルダーからこの手順を実行することが前提です。

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

方法 #2 - ダウンロードして解凍した Istio リリース (Istio のインストールに使用したもの) にもうアクセスできない場合は、次のいずれかのコマンドを使用します。 このコマンドは完了するまでに少し長くかかります。数分間お待ちください。

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

Powershell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>次の手順

次のドキュメントでは、カナリア リリースを展開するために Istio を使用してインテリジェント ルーティングを提供する方法を説明しています。

> [!div class="nextstepaction"]
> [AKS Istio インテリジェント経路指定シナリオ][istio-scenario-routing]

Istio のインストール オプションと構成オプションを他にも見るには、次の公式 Istio 記事をご覧ください。

- [Istio - Helm インストール ガイド][istio-install-helm]
- [Istio - Helm インストール オプション][istio-install-helm-options]

[Istio Bookinfo Application 例][istio-bookinfo-example]を使用した追加のシナリオに沿って進めることもできます。

Application Insights と Istio を使用して AKS アプリケーションを監視する方法については、次の Azure Monitor のドキュメントを参照してください。
- [Kubernetes でホストされるアプリケーションに対するゼロ インストルメンテーション アプリケーション監視][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/about/notes/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
