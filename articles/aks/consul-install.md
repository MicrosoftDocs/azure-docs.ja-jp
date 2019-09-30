---
title: Azure Kubernetes Service (AKS) に Hashicorp Consul をインストールする
description: Consul をインストールして使用し、Azure Kubernetes Service (AKS) クラスターでサービス メッシュを作成する方法について説明します
services: container-service
author: dstrebel
ms.service: container-service
ms.topic: article
ms.date: 06/19/2019
ms.author: dastrebe
ms.openlocfilehash: 7acd2533079499091427c7e63741b9c587ee29e5
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70189080"
---
# <a name="install-and-use-consul-connect-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で Consul Connect をインストールして使用する

[Consul][consul-github] はオープンソースのサービス メッシュであり、Kubernetes クラスターのマイクロサービスに対して重要な機能のセットが提供されます。 これらの機能には、サービスの検出、正常性チェック、サービスのセグメント化、可観測性が含まれます。 Consul の詳細については、[Consul の概要][consul-docs-concepts]に関する公式ドキュメントをご覧ください。

この記事では、Consul をインストールする方法について説明します。 Consul コンポーネントは、AKS 上の Kubernetes クラスターにインストールされます。

> [!NOTE]
> これらの手順は Consul バージョン `1.5` を参考にしています。
>
> Consul `1.5.x` リリースは、Hashicorp チームによって Kubernetes バージョン `1.12`、`1.14`、`1.14` に対してテストされています。 Consul のその他のバージョンは[GitHub の Consul リリース][consul-github-releases]に関するページにあります。また、各リリースに関する情報は[Consul のリリース ノート][consul-release-notes]を参照してください。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Consul コンポーネントを AKS にインストールする
> * Consul インストールを検証する
> * Consul を AKS からアンインストールする

## <a name="before-you-begin"></a>開始する前に

この記事で詳しく説明する手順では、AKS クラスターを作成済みで (Kubernetes `1.11`以上、RBAC を有効にする)、そのクラスターとの `kubectl` 接続が確立されていることを前提としています。 いずれかの項目でヘルプが必要な場合、[AKS クイック スタート][aks-quickstart]を参照してください。

これらの手順に従い Consul をインストールするには [Helm][helm] が必要です。 バージョン `2.12.2` 以降をクラスターに正しくインストールし、構成しておくことをお勧めします。 Helm のインストールでヘルプが必要な場合は、[AKS Helm インストール ガイド][helm-install]をご覧ください。 Linux ノード上で実行するようにすべての Consul ポッドをスケジュールすることも必要です。

この記事では、Consul のインストール ガイドを個別のステップに分割しています。 最終的な結果は、公式の Consul インストール [ガイド][consul-install-k8]と構造的に同じになります。

### <a name="install-the-consul-components-on-aks"></a>Consul コンポーネントを AKS にインストールする

まず、公式の HashiCorp Consul を Kubernetes GitHub リポジトリに複製します。

```bash
git clone https://github.com/hashicorp/consul-helm.git
cd consul-helm
```

次に、Consul インストール用のカスタム Helm 値ファイルを作成する必要があります。 Consul をインストールする前に、次のカスタム値ファイルを作成します。

```bash
vim consul-values.yaml
```

次に、次の値をコピーして consul-values.yaml ファイルに貼り付けます。

```yaml
# Sets datacenter name and version Consul to use
global:
  datacenter: dc1
  image: "consul:1.5.2"

# Enables proxies to be injected into pods
connectInject:
  enabled: true

# Enables UI on ClusterIP
ui:
  service:
    type: "ClusterIP"

# Enables GRCP that is required for connectInject
client:
  enabled: true
  grpc: true

# Sets replicase to 3 for HA
server:
  replicas: 3
  bootstrapExpect: 1
  disruptionBudget:
    enabled: true
    maxUnavailable: 1

# Syncs Kubernetes service discovery with Consul
syncCatalog:
  enabled: true
```

これでカスタム値ファイルが作成されたので、AKS クラスターに Consul をインストールできます。

Bash

```bash
helm install -f consul-values.yaml --name consul --namespace consul .
```
`Consul` Helm Chart によって、多くのオブジェクトがデプロイされます。 上記の `helm install` コマンドの出力で一覧を確認できます。 Consul コンポーネントのデプロイには、クラスター環境によっては、完了するまで 4 分から 5 分かかります。

> [!NOTE]
> Linux ノード上で実行するようにすべての Consul ポッドをスケジュールする必要があります。 クラスター上に Linux ノード プールの他に Windows Server ノード プールがある場合は、すべての Consul ポッドが Linux ノードで実行するようにスケジュールされていることを確認します。

これで、Consul が AKS クラスターにデプロイされました。 Consul が正常にデプロイされたことを確認するには、Consul インストールを検証する次のセクションに進みます。

## <a name="validate-the-consul-installation"></a>Consul インストールを検証する

最初に、サービスが期待どおりに作成されていることを確認します。 実行中のサービスを表示するには、[kubectl get svc][kubectl-get] コマンドを使用します。 `consul` 名前空間に照会します。この名前空間は `consul` Helm Chart によって Consul コンポーネントがインストールされた場所です。

```console
kubectl get svc --namespace consul
```

次の出力例では、現在実行中のサービスを確認できます。

```console
NAME                                 TYPE           CLUSTER-IP     EXTERNAL-IP             PORT(S)                                                                   AGE
consul                               ExternalName   <none>         consul.service.consul   <none>                                                                    38m
consul-consul-connect-injector-svc   ClusterIP      10.0.89.113    <none>                  443/TCP                                                                   40m
consul-consul-dns                    ClusterIP      10.0.166.82    <none>                  53/TCP,53/UDP                                                             40m
consul-consul-server                 ClusterIP      None           <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   40m
consul-consul-ui                     ClusterIP      10.0.117.164   <none>                  80/TCP                                                                    40m
```

次に、必要なポッドが作成されていることを確認します。 [kubectl get pods][kubectl-get] コマンドを使用し、再度、`consul` 名前空間に照会します。

```console
kubectl get pods --namespace consul
```

次の出力例では、実行中のポッドを確認できます。

```console
NAME                                                              READY   STATUS    RESTARTS   AGE
consul-consul-7cc9v                                               1/1     Running   0          37m
consul-consul-7klg7                                               1/1     Running   0          37m
consul-consul-connect-injector-webhook-deployment-57f88df8hgmfs   1/1     Running   0          37m
consul-consul-lq8qb                                               1/1     Running   0          37m
consul-consul-server-0                                            1/1     Running   0          37m
consul-consul-server-1                                            1/1     Running   0          36m
consul-consul-server-2                                            1/1     Running   0          36m
consul-consul-sync-catalog-7cf7d5bfff-jjbjv                       1/1     Running   2          37m
```

 すべてのポッドの状態が `Running` と表示される必要があります。 ポッドのステータスが実行中ではない場合、実行中にナルまで数分待ってください。 ポッドから問題が報告された場合、[kubectl describe pod][kubectl-describe] コマンドを使用し、出力と状態を確認してください。

## <a name="accessing-the-consul-ui"></a>Consul UI へのアクセス

Consul UI は、Consul の UI ベースの構成を提供する上記のセットアップでインストールされています。 Consul の UI は、外部 IP アドレス経由で公開されていません。 アドオン ユーザー インターフェイスにアクセスするには、[kubectl port-forward][kubectl-port-forward] コマンドを使用します。 このコマンドによって、クライアント コンピューターと AKS クラスターの関連ポッドの間に安全な接続が作成されます。

```bash
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```
これでブラウザーを開き、`http://localhost:8080/ui` を指定して Consul UI を開くことができます。 UI を開くと、次のように表示されます。

![Consul UI](./media/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Consul を AKS からアンインストールする

> [!WARNING]
> 実行中のシステムから Consul を削除すると、サービス間のトラフィックに関連する問題が発生することがあります。 進める前に、Consul なしでもシステムが正しく動作するようにプロビジョニングを作成することをお勧めします。

### <a name="remove-consul-components-and-namespace"></a>Consul コンポーネントおよび名前空間を削除する

Consul を AKS クラスターから削除するには、次のコマンドを使用します。 `helm delete` コマンドによって `consul` チャートが削除され、`kubectl delete ns` コマンドによって `consul` 名前空間が削除されます。

```bash
helm delete --purge consul
kubectl delete ns consul
```

## <a name="next-steps"></a>次の手順

Consul のインストール オプションと構成オプションを他にも見るには、次の公式 Consul 記事をご覧ください。

- [Consul - Helm インストール ガイド][consul-install-k8]
- [Consul - Helm インストール オプション][consul-install-helm-options]

[Consul サンプル アプリケーション][consul-app-example]を使用した追加のシナリオに沿って進めることもできます。

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://www.consul.io/docs/platform/k8s/run.html
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-app-example]: https://github.com/hashicorp/demo-consul-101/tree/master/k8s
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
