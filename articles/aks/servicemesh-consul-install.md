---
title: Azure Kubernetes Service (AKS) に Consul をインストールする
description: Consul をインストールして使用し、Azure Kubernetes Service (AKS) クラスターでサービス メッシュを作成する方法について説明します
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 7c5ad53c0040009e9ed1f28072540b46ce7b0b9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94683921"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で Consul をインストールして使用する

[Consul][consul-github] はオープンソースのサービス メッシュであり、Kubernetes クラスターのマイクロサービスに対して重要な機能のセットが提供されます。 これらの機能には、サービスの検出、正常性チェック、サービスのセグメント化、可観測性が含まれます。 Consul の詳細については、[Consul の概要][consul-docs-concepts]に関する公式ドキュメントをご覧ください。

この記事では、Consul をインストールする方法について説明します。 Consul コンポーネントは、AKS 上の Kubernetes クラスターにインストールされます。

> [!NOTE]
> これらの手順は Consul バージョン `1.6.0` を参考にし、Helm バージョン `2.14.2` 以上を使用しています。
>
> Consul `1.6.x` リリースは、Kubernetes バージョン `1.13+` に対して実行できます。 Consul のその他のバージョンは[GitHub の Consul リリース][consul-github-releases]に関するページにあります。また、各リリースに関する情報は[Consul のリリース ノート][consul-release-notes]を参照してください。

この記事では、次の方法について説明します。

> [!div class="checklist"]
> * Consul コンポーネントを AKS にインストールする
> * Consul インストールを検証する
> * Consul を AKS からアンインストールする

## <a name="before-you-begin"></a>開始する前に

この記事で詳しく説明する手順では、AKS クラスターを (Kubernetes `1.13` 以降で Kubernetes RBAC を有効にして) 作成済みであり、そのクラスターとの `kubectl` 接続が確立されていることを前提としています。 いずれかの項目でヘルプが必要な場合、[AKS クイック スタート][aks-quickstart]を参照してください。 クラスターの Linux ノード プールに少なくとも 3 つのノードがあることを確認します。

これらの手順に従い Consul をインストールするには [Helm][helm] が必要です。 クラスターに最新の安定バージョンを正しくインストールし、構成しておくことをお勧めします。 Helm のインストールでヘルプが必要な場合は、[AKS Helm インストール ガイド][helm-install]をご覧ください。 Linux ノード上で実行するようにすべての Consul ポッドをスケジュールすることも必要です。

この記事では、Consul のインストール ガイドを個別のステップに分割しています。 最終的な結果は、公式の Consul インストール [ガイド][consul-install-k8]と構造的に同じになります。

### <a name="install-the-consul-components-on-aks"></a>Consul コンポーネントを AKS にインストールする

まず、Consul Helm Chart のバージョン `v0.10.0` をダウンロードします。 このバージョンのグラフには、Consul バージョン `1.6.0`が含まれています。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

Helm とダウンロードした `consul-helm` チャートを使用して Consul コンポーネントを AKS クラスターの `consul` 名前空間にインストールします。 

> [!NOTE]
> **インストールのオプション**
> 
> このインストールにおいては次のオプションを使用しています。
> - `connectInject.enabled=true` - プロキシをポッドに挿入できるようにします
> - `client.enabled=true` - Consul クライアントをすべてのノードで実行できるようにします
> - `client.grpc=true` - connectInject に対して gRPC リスナーを有効にします
> - `syncCatalog.enabled=true` - Kubernetes と Consul サービスを同期します
>
> **ノードのセレクター**
>
> 現時点で Consul は Linux ノード上で実行するようにスケジュールする必要があります。 クラスター内に Windows Server ノードがある場合、Consul ポッドが確実に Linux ノードでのみ実行されるようにスケジュールする必要があります。 [ノードのセレクター][kubernetes-node-selectors]を使用して、ポッドが適切なノードにスケジュールされていることを確認します。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

`Consul` Helm Chart によって、多くのオブジェクトがデプロイされます。 上記の `helm install` コマンドの出力で一覧を確認できます。 Consul コンポーネントのデプロイには、クラスター環境によっては、完了するまで約 3 分かかります。

これで、Consul が AKS クラスターにデプロイされました。 Consul が正常にデプロイされたことを確認するには、Consul インストールを検証する次のセクションに進みます。

## <a name="validate-the-consul-installation"></a>Consul インストールを検証する

リソースが正常に作成されたことを確認します。 [kubectl get svc][kubectl-get] コマンドと [kubectl get pod][kubectl-get] コマンドを使用して、`helm install` コマンドによって Consul コンポーネントがインストールされた `consul` 名前空間のクエリを実行します。

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

次の出力例では、現在実行中のサービスとポッド (Linux ノードでスケジュールされたもの) を確認できます。

```output
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

すべてのポッドの状態が `Running` と表示される必要があります。 ポッドのステータスが実行中ではない場合、実行中にナルまで数分待ってください。 ポッドから問題が報告された場合、[kubectl describe pod][kubectl-describe] コマンドを使用し、出力と状態を確認してください。

## <a name="accessing-the-consul-ui"></a>Consul UI へのアクセス

Consul UI は上記のセットアップでインストールされていて、Consul の UI ベースの構成を提供します。 Consul の UI は、外部 IP アドレス経由で公開されていません。 Consul ユーザー インターフェイスにアクセスするには、[kubectl port-forward][kubectl-port-forward] コマンドを使用します。 このコマンドによって、クライアント コンピューターと AKS クラスターの関連ポッドの間に安全な接続が作成されます。

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

これでブラウザーを開き、`http://localhost:8080/ui` を指定して Consul UI を開くことができます。 UI を開くと、次のように表示されます。

![Consul UI](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Consul を AKS からアンインストールする

> [!WARNING]
> 実行中のシステムから Consul を削除すると、サービス間のトラフィックに関連する問題が発生することがあります。 進める前に、Consul なしでもシステムが正しく動作するようにプロビジョニングを作成することをお勧めします。

### <a name="remove-consul-components-and-namespace"></a>Consul コンポーネントおよび名前空間を削除する

Consul を AKS クラスターから削除するには、次のコマンドを使用します。 `helm delete` コマンドによって `consul` チャートが削除され、`kubectl delete namespace` コマンドによって `consul` 名前空間が削除されます。

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>次のステップ

Consul のインストール オプションと構成オプションを他にも見るには、次の公式 Consul 記事をご覧ください。

- [Consul - Helm インストール ガイド][consul-install-k8]
- [Consul - Helm インストール オプション][consul-install-helm-options]

以下を使用した追加のシナリオに沿って進めることもできます。

- [Consul のサンプル アプリケーション][consul-app-example]
- [Consul Kubernetes 参照アーキテクチャ][consul-reference]
- [Consul メッシュ ゲートウェイ][consul-mesh-gateways]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://learn.hashicorp.com/consul/kubernetes/kubernetes-deployment-guide
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-mesh-gateways]: https://learn.hashicorp.com/consul/kubernetes/mesh-gateways
[consul-reference]: https://learn.hashicorp.com/consul/kubernetes/kubernetes-reference
[consul-app-example]: https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh
[install-wsl]: /windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
