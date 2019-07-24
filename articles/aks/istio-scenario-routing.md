---
title: Azure Kubernetes Service (AKS) での Istio によるインテリジェントなルーティングとカナリア リリース
description: Azure Kubernetes Service (AKS) クラスターで Istio を使用してインテリジェントなルーティングを提供し、カナリア リリースをデプロイする方法について説明します
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 12/3/2018
ms.author: pabouwer
ms.openlocfilehash: 0a4e5e7e310a9949ee59291c2032eafda46955a9
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2018
ms.locfileid: "52893093"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で Istio によりインテリジェントなルーティングとカナリア リリースを使用する

[Istio][istio-github] はオープンソースのサービス メッシュであり、Kubernetes クラスターのマイクロサービスに対して重要な機能のセットが提供されます。 これらの機能には、トラフィック管理、サービスの ID とセキュリティ、ポリシー適用、可観測性などがあります。 Istio について詳しくは、公式ドキュメント「[What is Istio? (Istio とは何か)][istio-docs-concepts]」をご覧ください。

この記事では、Istio のトラフィック管理機能を使用する方法を説明します。 インテリジェントなルーティングとカナリア リリースについて説明するために、サンプルの AKS 投票アプリを使用します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * アプリケーションのデプロイ
> * アプリケーションの更新
> * アプリケーションのカナリア リリースをロールアウトする
> * ロールアウトを終了する

## <a name="before-you-begin"></a>開始する前に

この記事で詳しく説明する手順では、AKS クラスターを作成済みで (Kubernetes 1.10 以上、RBAC を有効にする)、そのクラスターとの `kubectl` 接続が確立されていることを前提としています。 また、Istio がクラスターにインストールされている必要もあります。

いずれかの項目でヘルプが必要な場合は、[AKS のクイック スタート][aks-quickstart]に関する記事および [install Istio in AKS (AKS への Istio のインストール)][istio-install] に関する記事をご覧ください。

## <a name="about-this-application-scenario"></a>このアプリケーションのシナリオについて

サンプルの AKS 投票アプリでは、ユーザーに 2 つの投票オプション (猫または犬) が提供されます。 各オプションの投票の数を保持するストレージ コンポーネントがあります。 さらに、各オプションの投票総数に関する詳細を提供する分析コンポーネントがあります。

この記事では最初に、バージョン *1.0* の投票アプリとバージョン *1.0* の分析コンポーネントをデプロイします。 分析コンポーネントでは、投票数に関する簡単なカウントが提供されます。 投票アプリと分析コンポーネントは、Redis によってサポートされるバージョン *1.0* のストレージ コンポーネントを使用して相互作用します。

分析コンポーネントを、カウントだけでなく合計とパーセンテージが提供されるようになったバージョン *1.1* にアップグレードします。

ユーザーのサブセットが、カナリア リリースを使用してアプリのバージョン *2.0* をテストします。 この新しいバージョンでは、MySQL データベースによってサポートされるストレージ コンポーネントが使用されています。

ユーザーのサブセットでバージョン *2.0* が期待どおりに動作することを確認した後、バージョン *2.0* をすべてのユーザーにロールアウトします。

## <a name="deploy-the-application"></a>アプリケーションのデプロイ

最初に、アプリケーションを Azure Kubernetes Service (AKS) クラスターにデプロイしましょう。 次の図では、このセクションの終わりまでどのようなものが実行されているかを示します。すべてのコンポーネントはバージョン *1.0* で、Istio のイングレス ゲートウェイ経由で受信要求が提供されます。

![AKS 投票アプリのコンポーネントとルーティング。](media/istio/components-and-routing-01.png)

この記事を理解するために必要な成果物は、[Azure-Samples/aks-voting-app][github-azure-sample] GitHub リポジトリにあります。 成果物をダウンロードするか、次のようにしてリポジトリを複製することができます。

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

ダウンロード/複製したリポジトリで次のフォルダーに変更し、以降の手順はすべてこのフォルダーから実行します。

```console
cd scenarios/intelligent-routing-with-istio
```

最初に、次のようにして、サンプル AKS 投票アプリ用の名前空間を *voting* という名前で AKS クラスターに作成します。

```console
kubectl create namespace voting
```

その名前空間に `istio-injection=enabled` というラベルを付けます。 このラベルにより、この名前空間のすべてのポッドにサイドカーとして Istio プロキシを自動的に挿入するよう、Istio に指示します。

```console
kubectl label namespace voting istio-injection=enabled
```

次に、AKS 投票アプリ用のコンポーネントを作成しましょう。 これらのコンポーネントを、前のステップで作成した *voting* 名前空間内に作成します。

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

次の出力例では、リソースが正常に作成されたことが示されています。

```
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio には、ポッドやサービスに関していくつか特定の要件があります。 詳しくは、[ポッドとサービスに関する Istio の要件のドキュメント][istio-requirements-pods-and-services]をご覧ください。

作成されたポッドを確認するには、次のように [kubectl get pods][kubectl-get] コマンドを使用します。

```console
kubectl get pods -n voting
```

次の出力例では、*voting-app* ポッドの 3 つのインスタンスと、*voting-analytics* ポッドおよび *voting-storage* ポッドの 1 つのインスタンスが示されています。 各ポッドには 2 つのコンテナーがあります。 これらのコンテナーの 1 つはコンポーネントであり、もう 1 つは "*Istio プロキシ*" です。

```
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-669f99dcc8-lzh7k   2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-bdmld         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-gcrng         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-strzc         2/2       Running   0          1m
voting-storage-1-0-7954799d96-5fv9r     2/2       Running   0          1m
```

ポッドに関する情報を表示するには、[kubectl describe pod][kubectl-describe] を使用します。 ポッド名は、前の出力で示されている実際の AKS クラスターのポッド名に置き換えます。

```console
kubectl describe pod voting-app-1-0-6c65c4bdd4-bdmld --namespace voting
```

次の出力例で示すように、"*Istio プロキシ*" コンテナーは、コンポーネント間のネットワーク トラフィックを管理するため、Istio によって自動的に挿入されます。

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.0.4
[...]
```

Istio [ゲートウェイ][istio-reference-gateway]および[仮想サービス][istio-reference-virtualservice]を作成するまで、投票アプリに接続することはできません。 これらの Istio リソースにより、既定の Istio イングレス ゲートウェイからアプリケーションにトラフィックがルーティングされます。

> [!NOTE]
> "*ゲートウェイ*" はサービス メッシュのエッジにあるコンポーネントであり、インバウンドまたはアウトバウンドの HTTP および TCP トラフィックが受信されます。
>
> "*仮想サービス*" では、1 つまたは複数の送信先サービスに対するルーティング規則のセットが定義されています。

ゲートウェイと仮想サービスの yaml をデプロイするには、`istioctl` クライアント バイナリを使用します。 `kubectl apply` コマンドと同様に、これらのリソースのデプロイ先の名前空間を指定します。

```console
istioctl create -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

次のコマンドを使用して、Istio イングレス ゲートウェイの IP アドレスを取得します。

```console
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

次の出力例では、イングレス ゲートウェイの IP アドレスが示されています。

```
52.187.250.239
```

ブラウザーを開き、IP アドレスを貼り付けます。 サンプルの AKS 投票アプリが表示されます。

![Istio が有効な AKS クラスターで実行されている AKS 投票アプリ。](media/istio/deploy-app-01.png)

画面の下部にある情報では、アプリでバージョン *1.0* の *voting-app* およびバージョン *1.0* の (Redis) ストレージ オプションが使用されていることがわかります。

## <a name="update-the-application"></a>アプリケーションの更新

新しいバージョンの分析コンポーネントをデプロイしましょう。 この新しいバージョン *1.1* では、各カテゴリのカウントに加えて合計とパーセンテージが表示されます。

次の図では、このセクションの最後で実行されているものを示します。バージョン *1.1* の *voting-analytics* コンポーネントだけが、*voting-app* コンポーネントからトラフィックをルーティングされています。 バージョン *1.0* の *voting-analytics* コンポーネントは引き続き実行され、*voting-analytics* サービスによって参照されますが、Istio プロキシではそのコンポーネントとのトラフィックのやり取りが許可されません。

![AKS 投票アプリのコンポーネントとルーティング。](media/istio/components-and-routing-02.png)

バージョン *1.1* の *voting-analytics* コンポーネントをデプロイしましょう。 *voting* 名前空間にこのコンポーネントを作成します。

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

前の手ステップで取得した Istio イングレス ゲートウェイの IP アドレスを使用して、サンプルの AKS 投票アプリをブラウザーで再び開きます。

ブラウザーでは次に示す 2 つのビューが交互に表示されます。 ただ 1 つのラベル セレクター (`app: voting-analytics`) を使用して *voting-analytics* コンポーネントに Kubernetes の[サービス][kubernetes-service]を使用しているため、Kubernetes ではそのセレクターに一致するポッド間のラウンドロビンという既定の動作が使用されます。 この例では、*voting-analytics* ポッドの両方のバージョン *1.0* と *1.1* です。

![AKS 投票アプリで実行されている分析コンポーネントのバージョン 1.0。](media/istio/deploy-app-01.png)

![AKS 投票アプリで実行されている分析コンポーネントのバージョン 1.1。](media/istio/update-app-01.png)

次のようにして、*voting-analytics* コンポーネントの 2 つのバージョン間の切り替えを目に見えるようにすることができます。 自分の Istio イングレス ゲートウェイの IP アドレスを使用することを忘れないでください。

```console
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

次の出力例では、バージョン間のサイト切り替えとして返される Web サイトの関連部分を示します。

```
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>アプリケーションのバージョン 1.1 にトラフィックをロックする

*voting-analytics* コンポーネントのバージョン *1.1* と、*voting-storage* コンポーネントのバージョン *1.0* だけに、トラフィックをロックしてみましょう。 その後、他のすべてのコンポーネントに対するルーティング規則を定義します。

> * "*仮想サービス*" では、1 つまたは複数の送信先サービスに対するルーティング規則のセットが定義されています。
> * "*宛先ルール*" では、トラフィック ポリシーとバージョン固有のポリシーが定義されています。
> * "*ポリシー*" では、ワークロードで受け付けられる認証方法を定義します。

`istioctl` クライアント バイナリを使用して *voting-app* 上の仮想サービス定義を置き換え、他のコンポーネントに[宛先ルール][istio-reference-destinationrule]と[仮想サービス][istio-reference-virtualservice]を追加します。

また、[ポリシー][istio-reference-policy]を *voting* 名前空間に追加して、サービス間のすべての通信が相互 TLS とクライアント証明書を使用してセキュリティ保護されるようにします。

置き換える *voting-app* には既存の仮想サービス定義があるので、`istioctl replace` コマンドを次のように使用します。

```console
istioctl replace -f istio/step-2a-update-voting-app-virtualservice.yaml --namespace voting
```

次の出力例では、Istio 仮想サービスが正常に更新されたことが示されています。

```
Updated config virtual-service/voting/voting-app to revision 141902
```

次に、`istioctl create` コマンドを使用して、他のすべてのコンポーネントに新しいポリシーおよび新しい宛先ルールと仮想サービスを追加します。

* ポリシーでは `peers.mtls.mode` が `STRICT` に設定されているので、*voting* 名前空間内のサービス間で相互 TLS が強制されます。
* すべての宛先ルールで `trafficPolicy.tls.mode` を `ISTIO_MUTUAL` に設定することもできます。 Istio では、強力な ID を持つサービスが提供され、Istio によって透過的に管理される相互 TLS とクライアント証明書を使用してサービス間の通信がセキュリティ保護されます。

```console
istioctl create -f istio/step-2b-add-routing-for-all-components.yaml --namespace voting
```

次の出力例では、新しいポリシー、宛先ルール、仮想サービスが正常に作成されたことが示されています。

```
Created config policy/voting/default to revision 142118
Created config destination-rule/voting/voting-app at revision 142119
Created config destination-rule/voting/voting-analytics at revision 142120
Created config virtual-service/voting/voting-analytics at revision 142121
Created config destination-rule/voting/voting-storage at revision 142122
Created config virtual-service/voting/voting-storage at revision 142123
```

ブラウザーで再び AKS 投票アプリを開くと、新しいバージョン *1.1* の *voting-analytics* コンポーネントのみが、*voting-app* コンポーネントによって使用されます。

![AKS 投票アプリで実行されている分析コンポーネントのバージョン 1.1。](media/istio/update-app-01.png)

次のようにすると、*voting-analytics* コンポーネントのバージョン *1.1* にのみルーティングされるようになっていることをもっと簡単に視覚化できます。 Istio イングレス ゲートウェイの IP アドレスを使用することを忘れないでください。

次のようにすると、*voting-analytics* コンポーネントのバージョン *1.1* にのみルーティングされるようになっていることを視覚化できます。 自分の Istio イングレス ゲートウェイの IP アドレスを使用することを忘れないでください。

```azurecli-interactive
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

次の出力例では、返される Web サイトの関連する部分を示します。

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Istio によって相互 TLS を使用してサービス間の通信がセキュリティ保護されていることを確認します。 次のコマンドでは、各 *voting-app* サービスの TLS 設定が確認されます。

```console
istioctl authn tls-check voting-app.voting.svc.cluster.local
istioctl authn tls-check voting-analytics.voting.svc.cluster.local
istioctl authn tls-check voting-storage.voting.svc.cluster.local
```

次の出力例では、ポリシーと宛先ルールによって各サービスに相互 TLS が適用されていることが示されています。

```
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>アプリケーションのカナリア リリースをロールアウトする

次に、新しいバージョン *2.0* の *voting-app*、*voting-analytics*、*voting-storage* コンポーネントをデプロイしましょう。 新しい *voting-storage* コンポーネントでは Redis の代わりに MySQL が使用されており、*voting-app* および *voting-analytics* コンポーネントは、この新しい *voting-storage* コンポーネントを使用できるように更新されています。

*voting-app* コンポーネントでは、機能フラグ機能がサポートされるようになっています。 この機能フラグを使用すると、ユーザーのサブセットに対して Istio のカナリア リリース機能をテストできます。

次の図では、このセクションの最後に実行されるものを示します。

* バージョン *1.0* の *voting-app* コンポーネント、バージョン *1.1* の *voting-analytics* コンポーネント、バージョン *1.0* の *voting-storage* コンポーネントは、相互に通信することができます。
* バージョン *2.0* の *voting-app* コンポーネント、バージョン *2.0* の *voting-analytics* コンポーネント、バージョン *2.0* の *voting-storage* コンポーネントは、相互に通信することができます。
* バージョン *2.0* の *voting-app* コンポーネントには、特定の機能フラグが設定されているユーザーのみがアクセスできます。 この変更は、Cookie により機能フラグを使用して管理されます。

![AKS 投票アプリのコンポーネントとルーティング。](media/istio/components-and-routing-03.png)

最初に、これらの新しいコンポーネントに対応するように Istio の宛先ルールと仮想サービスを更新します。 これらの更新では、新しいコンポーネントに間違ってトラフィックがルーティングされないこと、予期せずユーザーにアクセス権が与えられないことが保証されます。

```console
istioctl replace -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

次の出力例では、宛先ルールと仮想サービスが正常に更新されたことが示されています。

```
Updated config destination-rule/voting/voting-app to revision 150930
Updated config virtual-service/voting/voting-app to revision 150931
Updated config destination-rule/voting/voting-analytics to revision 150937
Updated config virtual-service/voting/voting-analytics to revision 150939
Updated config destination-rule/voting/voting-storage to revision 150940
Updated config virtual-service/voting/voting-storage to revision 150941
```

次に、新しいバージョン *2.0* のコンポーネントに対する Kubernetes オブジェクトを追加しましょう。 *voting-storage* サービスも、MySQL 用のポート *3306* を含むように更新します。

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

次の出力例では、Kubernetes オブジェクトが正常に更新または作成されたことが示されています。

```
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

すべてのバージョン *2.0* ポッドが実行されるまで待ちます。 [kubectl get pods][kubectl-get] コマンドを使用して、*voting* 名前空間内のすべてのポッドを表示します。

```azurecli-interactive
kubectl get pods --namespace voting
```

投票アプリケーションのバージョン *1.0* とバージョン *2.0* (カナリア) の間を切り替えられるようになっているはずです。 画面の下部にある機能フラグ トグルによって、Cookie が設定されます。 この Cookie は、新しいバージョン *2.0* にユーザーをルーティングするために、*voting-app* 仮想サービスによって使用されます。

![バージョン 1.0 の AKS 投票アプリ - 機能フラグが設定されていない。](media/istio/canary-release-01.png)

![バージョン 2.0 の AKS 投票アプリ - 機能フラグが設定されている。](media/istio/canary-release-02.png)

投票カウントは、アプリのバージョンによって異なります。 この違いにより、2 つの異なるストレージ バックエンドを使用していることが示されます。

## <a name="finalize-the-rollout"></a>ロールアウトを終了する

カナリア リリースのテストで問題がなければ、*voting-app* 仮想サービスを更新して、すべてのトラフィックがバージョン *2.0* の *voting-app* コンポーネントにルーティングされるようにします。 その後は、機能フラグが設定されているかどうかに関係なく、すべてのユーザーに対してバージョン *2.0* のアプリケーションが表示されます。

![AKS 投票アプリのコンポーネントとルーティング。](media/istio/components-and-routing-04.png)

すべての宛先ルールを更新し、アクティブにする必要がなくなったコンポーネントのバージョンを削除します。 その後、すべての仮想サービスを更新して、これらのバージョンの参照を停止します。

古いバージョンのコンポーネントへのトラフィックがなくなったので、それらのコンポーネントに関するすべてのデプロイを安全に削除できます。

![AKS 投票アプリのコンポーネントとルーティング。](media/istio/components-and-routing-05.png)

AKS 投票アプリの新しいバージョンを正常にロールアウトできました。

## <a name="next-steps"></a>次の手順

[Istio Bookinfo Application の例][istio-bookinfo-example]を使用する他のシナリオを調べることができます。

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/spec-requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#Gateway
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#VirtualService
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#DestinationRule
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md
