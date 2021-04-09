---
title: Azure Kubernetes Service (AKS) で Linkerd をインストールする
description: Linkerd をインストールして使用し、Azure Kubernetes Service (AKS) クラスターでサービス メッシュを作成する方法について説明します
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d5b2ec220bbf14a79247ed2c78a0e84f4717fae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94684238"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で Linkerd をインストールする

[Linkerd ][linkerd-github] は、オープンソースのサービス メッシュおよび [CNCF incubating プロジェクト][linkerd-cncf]です。 Linkerd は、トラフィック管理、サービス ID とセキュリティ、信頼性、可観測性などの機能を提供する超軽量のサービス メッシュです。 Linkerd の詳細については、公式の [Linkerd に関する FAQ][linkerd-faq] と [Linkerd アーキテクチャ][linkerd-architecture]のドキュメントを参照してください。

この記事では、Linkerd をインストールする方法について説明します。 Linkerd `linkerd` クライアント バイナリはクライアント コンピューターにインストールされます。それから、Linkerd コンポーネントが AKS の Kubernetes クラスターにインストールされます。

> [!NOTE]
> これらの手順は Linkerd バージョン `stable-2.6.0` を参考にしています。
>
> Linkerd `stable-2.6.x` は、Kubernetes バージョン `1.13+` に対して実行できます。 Linkerd のその他の安定したエッジ バージョンは [GitHub - Linkerd Releases][linkerd-github-releases] で確認できます。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Linkerd の linkerd クライアント バイナリをダウンロードしてインストールする
> * Linkerd を AKS にインストールする
> * Linkerd インストールを検証する
> * ダッシュボードにアクセスする
> * Linkerd を AKS からアンインストールする

## <a name="before-you-begin"></a>開始する前に

この記事で詳しく説明する手順では、AKS クラスターを (Kubernetes `1.13` 以降で Kubernetes RBAC を有効にして) 作成済みであり、そのクラスターとの `kubectl` 接続が確立されていることを前提としています。 いずれかの項目でヘルプが必要な場合、[AKS クイック スタート][aks-quickstart]を参照してください。

すべての Linkerd ポッドを Linux ノードで実行するようにスケジュールする必要があります。このセットアップは以下で詳しく説明するインストール方法の既定値であり、追加の構成は必要ありません。

この記事では、Linkerd のインストール ガイドを個別のステップに分割しています。 結果は、公式の Linkerd ファースト ステップ [ガイド][linkerd-getting-started]と構造的に同じになります。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>Linkerd を AKS にインストールする

Linkerd をインストールする前に、インストール前のチェックを実行して、コントロール プレーンを AKS クラスターにインストールできるかどうかを確認します。

```console
linkerd check --pre
```

AKS クラスターが Linkerd の有効なインストールの対象であることを示すために、次のような内容が表示されます。

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

pre-kubernetes-setup
--------------------
√ control plane namespace does not already exist
√ can create Namespaces
√ can create ClusterRoles
√ can create ClusterRoleBindings
√ can create CustomResourceDefinitions
√ can create PodSecurityPolicies
√ can create ServiceAccounts
√ can create Services
√ can create Deployments
√ can create CronJobs
√ can create ConfigMaps
√ no clock skew detected

pre-kubernetes-capability
-------------------------
√ has NET_ADMIN capability
√ has NET_RAW capability

pre-linkerd-global-resources
----------------------------
√ no ClusterRoles exist
√ no ClusterRoleBindings exist
√ no CustomResourceDefinitions exist
√ no MutatingWebhookConfigurations exist
√ no ValidatingWebhookConfigurations exist
√ no PodSecurityPolicies exist

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

Status check results are √
```

この時点で、Linkerd コンポーネントをインストールします。 AKS クラスターに Linkerd コンポーネントをインストールするには、`linkerd` および `kubectl` バイナリを使用します。 `linkerd` 名前空間が自動的に作成され、この名前空間にコンポーネントがインストールされます。

```console
linkerd install | kubectl apply -f -
```

Linkerd によって、多くのオブジェクトがデプロイされます。 上記の `linkerd install` コマンドの出力で一覧が表示されます。 Linkerd コンポーネントのデプロイには、クラスター環境によっては、完了するまで約 1 分かかります。

これで、Linkerd が AKS クラスターにデプロイされました。 Linkerd が正常にデプロイされたことを確認するには、次の [Linkerd インストールを検証する](#validate-the-linkerd-installation)セクションに進みます。

## <a name="validate-the-linkerd-installation"></a>Linkerd インストールを検証する

リソースが正常に作成されたことを確認します。 [kubectl get svc][kubectl-get] コマンドと [kubectl get pod][kubectl-get] コマンドを使用して、`linkerd install` コマンドによって Linkerd コンポーネントがインストールされた `linkerd` 名前空間のクエリを実行します。

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

次の出力例では、現在実行中のサービスとポッド (Linux ノードでスケジュールされたもの) を確認できます。

```console
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE  SELECTOR
linkerd-controller-api   ClusterIP   10.0.110.67    <none>        8085/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-destination      ClusterIP   10.0.224.29    <none>        8086/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-dst              ClusterIP   10.0.225.148   <none>        8086/TCP            66s  linkerd.io/control-plane-component=destination
linkerd-grafana          ClusterIP   10.0.61.124    <none>        3000/TCP            65s  linkerd.io/control-plane-component=grafana
linkerd-identity         ClusterIP   10.0.6.104     <none>        8080/TCP            67s  linkerd.io/control-plane-component=identity
linkerd-prometheus       ClusterIP   10.0.27.168    <none>        9090/TCP            65s  linkerd.io/control-plane-component=prometheus
linkerd-proxy-injector   ClusterIP   10.0.100.133   <none>        443/TCP             64s  linkerd.io/control-plane-component=proxy-injector
linkerd-sp-validator     ClusterIP   10.0.221.5     <none>        443/TCP             64s  linkerd.io/control-plane-component=sp-validator
linkerd-tap              ClusterIP   10.0.18.14     <none>        8088/TCP,443/TCP    64s  linkerd.io/control-plane-component=tap
linkerd-web              ClusterIP   10.0.37.108    <none>        8084/TCP,9994/TCP   66s  linkerd.io/control-plane-component=web

NAME                                      READY   STATUS    RESTARTS   AGE   IP            NODE                            NOMINATED NODE   READINESS GATES
linkerd-controller-66ddc9f94f-cm9kt       3/3     Running   0          66s   10.240.0.50   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-destination-c94bc454-qpkng        2/2     Running   0          66s   10.240.0.78   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-grafana-6868fdcb66-4cmq2          2/2     Running   0          65s   10.240.0.69   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-identity-74d8df4b85-tqq8f         2/2     Running   0          66s   10.240.0.48   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-prometheus-699587cf8-k8ghg        2/2     Running   0          65s   10.240.0.41   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-proxy-injector-6556447f64-n29wr   2/2     Running   0          64s   10.240.0.32   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-sp-validator-56745cd567-v4x7h     2/2     Running   0          64s   10.240.0.6    aks-linux-16165125-vmss000000   <none>           <none>
linkerd-tap-5cd9fc566-ct988               2/2     Running   0          64s   10.240.0.15   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-web-774c79b6d5-dhhwf              2/2     Running   0          65s   10.240.0.70   aks-linux-16165125-vmss000002   <none>           <none>
```

Linkerd は、Linkerd コントロール プレーンが正常にインストールおよび構成されたことを検証するために、`linkerd` クライアント バイナリを介してコマンドを提供します。

```console
linkerd check
```

インストールが正常に終了したことを示すために、次のような内容が表示されます。

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

linkerd-config
--------------
√ control plane Namespace exists
√ control plane ClusterRoles exist
√ control plane ClusterRoleBindings exist
√ control plane ServiceAccounts exist
√ control plane CustomResourceDefinitions exist
√ control plane MutatingWebhookConfigurations exist
√ control plane ValidatingWebhookConfigurations exist
√ control plane PodSecurityPolicies exist

linkerd-existence
-----------------
√ 'linkerd-config' config map exists
√ heartbeat ServiceAccount exist
√ control plane replica sets are ready
√ no unschedulable pods
√ controller pod is running
√ can initialize the client
√ can query the control plane API

linkerd-api
-----------
√ control plane pods are ready
√ control plane self-check
√ [kubernetes] control plane can talk to Kubernetes
√ [prometheus] control plane can talk to Prometheus
√ no invalid service profiles

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

control-plane-version
---------------------
√ control plane is up-to-date
√ control plane and cli versions match

Status check results are √
```

## <a name="access-the-dashboard"></a>ダッシュボードにアクセスする

Linkerd には、サービス メッシュとワークロードに関する分析情報を提供するダッシュボードが付属しています。 ダッシュボードにアクセスするには、`linkerd dashboard` コマンドを使用します。 このコマンドでは、[kubectl port-forward][kubectl-port-forward] を活用して、クライアント コンピューターと AKS クラスターの関連ポッドの間に安全な接続を作成します。 これにより、自動的に既定のブラウザーでダッシュボードが開きます。

```console
linkerd dashboard
```

また、このコマンドでは、port-forward を作成し、Grafana ダッシュボードのリンクを返します。

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>Linkerd を AKS からアンインストールする

> [!WARNING]
> 実行中のシステムから Linkerd を削除すると、サービス間のトラフィックに関連する問題が発生することがあります。 進める前に、Linkerd なしでもシステムが正しく動作するようにプロビジョニングを作成することをお勧めします。

まず、データ プレーンのプロキシを削除する必要があります。 ワークロードの名前空間から自動プロキシ挿入の[注釈][linkerd-automatic-proxy-injection]をすべて削除し、ワークロードのデプロイをロールアウトします。 ワークロードには、関連付けられているデータ プレーン コンポーネントがなくなります。

最後に、コントロール プレーンを次のように削除します。

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>次のステップ

Linkerd のインストール オプションと構成オプションを他にも調べるには、次の公式 Linkerd ガイダンスをご覧ください。

- [Linkerd - Helm のインストール][linkerd-install-with-helm]
- [Linkerd - ロールの特権に対応するためのマルチステージのインストール][linkerd-multi-stage-installation]

以下を使用した追加のシナリオに沿って進めることもできます。

- [Linkerd emojivoto デモ][linkerd-demo-emojivoto]
- [Linkerd books デモ][linkerd-demo-books]

<!-- LINKS - external -->

[linkerd]: https://linkerd.io/
[linkerd-cncf]: https://landscape.cncf.io/selected=linkerd
[linkerd-faq]: https://linkerd.io/2/faq/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-getting-started]: https://linkerd.io/2/getting-started/
[linkerd-overview]: https://linkerd.io/2/overview/
[linkerd-github]: https://github.com/linkerd/linkerd2
[linkerd-github-releases]: https://github.com/linkerd/linkerd2/releases/

[linkerd-install-with-helm]: https://linkerd.io/2/tasks/install-helm/
[linkerd-multi-stage-installation]: https://linkerd.io/2/tasks/install/#multi-stage-install
[linkerd-automatic-proxy-injection]: https://linkerd.io/2/features/proxy-injection/

[linkerd-demo-emojivoto]: https://linkerd.io/2/getting-started/#step-5-install-the-demo-app
[linkerd-demo-books]: https://linkerd.io/2/tasks/books/

[helm]: https://helm.sh

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
