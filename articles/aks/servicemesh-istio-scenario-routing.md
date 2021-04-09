---
title: インテリジェントなルーティングに Istio を使用する
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) クラスターで Istio を使用してインテリジェントなルーティングを提供し、カナリア リリースをデプロイする方法について説明します
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d66f3099ba225fbdd2bfc3d54db56ffd8ed2c43f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94684034"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で Istio によりインテリジェントなルーティングとカナリア リリースを使用する

[Istio][istio-github] はオープンソースのサービス メッシュであり、Kubernetes クラスターのマイクロサービスに対して重要な機能のセットが提供されます。 これらの機能には、トラフィック管理、サービスの ID とセキュリティ、ポリシー適用、可観測性などがあります。 Istio について詳しくは、公式ドキュメント「[Istio とは][istio-docs-concepts]」をご覧ください。

この記事では、Istio のトラフィック管理機能を使用する方法を説明します。 インテリジェントなルーティングとカナリア リリースについて説明するために、サンプルの AKS 投票アプリを使用します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * アプリケーションの配置
> * アプリケーションの更新
> * アプリケーションのカナリア リリースをロールアウトする
> * ロールアウトを終了する

## <a name="before-you-begin"></a>開始する前に

> [!NOTE]
> このシナリオは、Istio バージョン `1.3.2` に対してテスト済みです。

この記事で詳しく説明する手順では、AKS クラスターを (Kubernetes `1.13` 以降で Kubernetes RBAC を有効にして) 作成済みであり、そのクラスターとの `kubectl` 接続が確立されていることを前提としています。 さらに、Istio がクラスターにインストールされている必要があります。

いずれかの項目でヘルプが必要な場合は、[AKS のクイック スタート][aks-quickstart]に関する記事と [AKS への Istio のインストール][istio-install]に関するガイダンスをご覧ください。

## <a name="about-this-application-scenario"></a>このアプリケーションのシナリオについて

サンプルの AKS 投票アプリでは、ユーザーに 2 つの投票オプション (**Cats** または **Dogs**) が提供されます。 各オプションの投票の数を保持するストレージ コンポーネントがあります。 さらに、各オプションの投票総数に関する詳細を提供する分析コンポーネントがあります。

このアプリケーション シナリオでは、投票アプリのバージョン `1.0` と分析コンポーネントのバージョン `1.0` をデプロイすることから始めます。 分析コンポーネントでは、投票数に関する簡単なカウントが提供されます。 投票アプリと分析コンポーネントは、Redis によってサポートされるストレージ コンポーネントのバージョン `1.0` を使用してやり取りします。

分析コンポーネントを、カウントだけでなく合計とパーセンテージも提供するバージョン `1.1` にアップグレードします。

カナリア リリースを使用して、小集団のユーザーで、アプリのバージョン `2.0` をテストします。 この新しいバージョンでは、MySQL データベースによってサポートされるストレージ コンポーネントが使用されています。

バージョン `2.0` が小集団のユーザーで期待どおりに動作することを確認した後、バージョン `2.0` をすべてのユーザーにロールアウトします。

## <a name="deploy-the-application"></a>アプリケーションの配置

最初に、アプリケーションを Azure Kubernetes Service (AKS) クラスターにデプロイしましょう。 次の図に、このセクションが終わった時点で何が実行されているかを示します。つまり、すべてのコンポーネントのバージョン `1.0` では、Istio イングレス ゲートウェイ経由で受信要求が処理されます。

![すべてのコンポーネントのバージョン 1.0 を示す図。Istio のイングレス ゲートウェイ経由で受信要求が提供されています。](media/servicemesh/istio/scenario-routing-components-01.png)

この記事を理解するために必要な成果物は、[Azure-Samples/aks-voting-app][github-azure-sample] GitHub リポジトリにあります。 成果物をダウンロードするか、次のようにしてリポジトリを複製することができます。

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

ダウンロード/複製したリポジトリで次のフォルダーに変更し、以降の手順はすべてこのフォルダーから実行します。

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

最初に、次のようにして、サンプルの AKS 投票アプリ用の名前空間を `voting` という名前で、自分の AKS クラスターに作成します。

```console
kubectl create namespace voting
```

その名前空間に `istio-injection=enabled` というラベルを付けます。 このラベルにより、この名前空間のすべてのポッドにサイドカーとして Istio プロキシを自動的に挿入するよう、Istio に指示します。

```console
kubectl label namespace voting istio-injection=enabled
```

次に、AKS 投票アプリ用のコンポーネントを作成しましょう。 これらのコンポーネントを、前の手順で作成した `voting` 名前空間内に作成します。

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

次の出力例は、リソースが作成されたことを示しています。

```output
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
kubectl get pods -n voting --show-labels
```

次の出力例は、`voting-app` ポッドには 3 つのインスタンスがあり、`voting-analytics` ポッドと `voting-storage` ポッドにはどちらも 1 つのインスタンスがあることを示しています。 各ポッドには 2 つのコンテナーがあります。 これらのコンテナーの 1 つがコンポーネントであり、もう 1 つが `istio-proxy` です。

```output
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

ポッドに関する情報を表示するには、ラベル セレクターを指定して [kubectl describe pod][kubectl-describe] コマンドを使用し、`voting-analytics` ポッドを選択します。 出力をフィルター処理して、ポッドに存在する 2 つのコンテナーの詳細を表示します。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

Istio [ゲートウェイ][istio-reference-gateway]および[仮想サービス][istio-reference-virtualservice]を作成するまで、投票アプリに接続することはできません。 これらの Istio リソースにより、既定の Istio イングレス ゲートウェイからアプリケーションにトラフィックがルーティングされます。

> [!NOTE]
> "**ゲートウェイ**" はサービス メッシュのエッジにあるコンポーネントであり、インバウンドまたはアウトバウンドの HTTP および TCP トラフィックが受信されます。
> 
> "**仮想サービス**" では、1 つまたは複数の送信先サービスに対するルーティング規則のセットが定義されています。

`kubectl apply` コマンドを使用して、ゲートウェイと仮想サービス yaml をデプロイします。 これらのリソースがデプロイされる名前空間を必ず指定してください。

```console
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

次の出力例は、新しいゲートウェイと仮想サービスが作成されたことを示しています。

```output
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

次のコマンドを使用して、Istio イングレス ゲートウェイの IP アドレスを取得します。

```output
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

次の出力例では、イングレス ゲートウェイの IP アドレスが示されています。

```output
20.188.211.19
```

ブラウザーを開き、IP アドレスを貼り付けます。 サンプルの AKS 投票アプリが表示されます。

![Istio が有効な AKS クラスターで実行されている AKS 投票アプリ。](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

このアプリでは、`voting-app` のバージョン `1.0` と `voting-storage` (Redis) のバージョン `1.0` が使用されていることが画面下部の情報に表示されます。

## <a name="update-the-application"></a>アプリケーションの更新

新しいバージョンの分析コンポーネントをデプロイしましょう。 この新しいバージョン `1.1` では、各カテゴリのカウントに加えて合計とパーセンテージが表示されます。

次の図に、このセクションが終わった時点で何が実行されているかを示します。`voting-analytics` コンポーネントのバージョン `1.1` だけが、`voting-app` コンポーネントからトラフィックをルーティングされます。 `voting-analytics` コンポーネント のバージョン `1.0` は引き続き実行され、`voting-analytics` サービスによって参照されますが、Istio プロキシではそのコンポーネントとのトラフィックは許可されません。

![voting-app コンポーネントからルーティングされたトラフィックが voting-analytics コンポーネントのバージョン 1.1 のみに含まれていることを示す図。](media/servicemesh/istio/scenario-routing-components-02.png)

`voting-analytics` コンポーネントのバージョン `1.1` をデプロイしましょう。 `voting` 名前空間にこのコンポーネントを作成します。

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

次の出力例は、リソースが作成されたことを示しています。

```output
deployment.apps/voting-analytics-1-1 created
```

前の手ステップで取得した Istio イングレス ゲートウェイの IP アドレスを使用して、サンプルの AKS 投票アプリをブラウザーで再び開きます。

ブラウザーでは次に示す 2 つのビューが交互に表示されます。 単一のラベル セレクター (`app: voting-analytics`) だけを使用して `voting-analytics` コンポーネントに対して Kubernetes [サービス][kubernetes-service]を使用しているため、Kubernetes では、セレクターと一致するポッド間のラウンドロビンという既定の動作が使用されています。 ここでは、`voting-analytics` ポッドの `1.0` と `1.1` の両方になります。

![AKS 投票アプリで実行されている分析コンポーネントのバージョン 1.0。](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![AKS 投票アプリで実行されている分析コンポーネントのバージョン 1.1。](media/servicemesh/istio/scenario-routing-update-app-01.png)

次のようにして、`voting-analytics` コンポーネントの 2 つのバージョン間の切り替えを視覚化できます。 自分の Istio イングレス ゲートウェイの IP アドレスを使用することを忘れないでください。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

次の出力例では、バージョン間のサイト切り替えとして返される Web サイトの関連部分を示します。

```output
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>アプリケーションのバージョン 1.1 にトラフィックをロックする

次に、トラフィックを `voting-analytics` コンポーネントのバージョン `1.1` と `voting-storage` コンポーネントのバージョン `1.0` にロックダウンしましょう。 その後、他のすべてのコンポーネントに対するルーティング規則を定義します。

> * "**仮想サービス**" では、1 つまたは複数の送信先サービスに対するルーティング規則のセットが定義されています。
> * "**宛先ルール**" では、トラフィック ポリシーとバージョン固有のポリシーが定義されています。
> * "**ポリシー**" では、ワークロードで受け付けられる認証方法を定義します。

`kubectl apply` コマンドを使用して `voting-app` の仮想サービス定義を置き換え、他のコンポーネント用の[宛先ルール][istio-reference-destinationrule]と[仮想サービス][istio-reference-virtualservice]を追加します。 [ポリシー][istio-reference-policy]を `voting` 名前空間に追加して、サービス間のすべての通信が相互 TLS とクライアント証明書を使用してセキュリティ保護されるようにします。

* ポリシーでは `peers.mtls.mode` を `STRICT` に設定して、`voting` 名前空間内のサービス間で相互 TLS が確実に実行されるようにします。
* さらに、すべての宛先ルールで `trafficPolicy.tls.mode` を `ISTIO_MUTUAL` に設定します。 Istio では、強力な ID を持つサービスが提供され、Istio によって透過的に管理される相互 TLS とクライアント証明書を使用してサービス間の通信がセキュリティ保護されます。

```console
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

次の出力例は、新しいポリシー、宛先ルール、および仮想サービスが更新/作成されたことを示しています。

```output
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

ブラウザーで再び AKS 投票アプリを開くと、`voting-analytics` コンポーネントの新しいバージョンである `1.1` だけが `voting-app` コンポーネントによって使用されます。

![AKS 投票アプリで実行されている分析コンポーネントのバージョン 1.1。](media/servicemesh/istio/scenario-routing-update-app-01.png)

次のようにすると、`voting-analytics` コンポーネントのバージョン `1.1` にのみルーティングされるようになっていることを視覚化できます。 自分の Istio イングレス ゲートウェイの IP アドレスを使用することを忘れないでください。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

次の出力例では、返される Web サイトの関連する部分を示します。

```output
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

次に、Istio によって相互 TLS を使用してサービス間の通信がセキュリティ保護されていることを確認しましょう。 これを行うために、`istioctl` クライアント バイナリの [authn tls-check][istioctl-authn-tls-check] コマンドを、次の書式で使用します。

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

このコマンド セットによって、名前空間内にある一連のラベルと一致するすべてのポッドから、指定されたサービスへのアクセスに関する情報が提供されます。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

次の出力例は、上記の各クエリで相互 TLS が実行されていることを示しています。 この出力では、相互 TLS を実行するポリシーと宛先ルールも示されています。

```output
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>アプリケーションのカナリア リリースをロールアウトする

次に、`voting-app`、`voting-analytics`、および `voting-storage` の各コンポーネントの新しいバージョンである `2.0` をデプロイしましょう。 新しい `voting-storage` コンポーネントでは Redis ではなく MySQL を使用しています。`voting-app` コンポーネントと `voting-analytics` コンポーネントを更新して、この新しい `voting-storage` コンポーネントを使用できるようにします。

これで、`voting-app` コンポーネントは、フラグ機能をサポートするようになります。 この機能フラグを使用すると、ユーザーのサブセットに対して Istio のカナリア リリース機能をテストできます。

次の図に、このセクションが終わった時点で何が実行されているかを示します。

* `voting-app` コンポーネントのバージョン `1.0`、`voting-analytics` コンポーネントのバージョン `1.1`、および `voting-storage` コンポーネントのバージョン `1.0` が、相互に通信できます。
* `voting-app` コンポーネントのバージョン `2.0`、`voting-analytics` コンポーネントのバージョン `2.0`、および `voting-storage` コンポーネントのバージョン `2.0` が、相互に通信できます。
* `voting-app` コンポーネントのバージョン `2.0` は、特定の機能フラグが設定されているユーザーのみがアクセスできます。 この変更は、Cookie により機能フラグを使用して管理されます。

![このセクションの最後で実行されるものを示す図。](media/servicemesh/istio/scenario-routing-components-03.png)

最初に、これらの新しいコンポーネントに対応するように Istio の宛先ルールと仮想サービスを更新します。 これらの更新では、新しいコンポーネントに間違ってトラフィックがルーティングされないこと、予期せずユーザーにアクセス権が与えられないことが保証されます。

```console
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

次の出力例は、宛先ルールと仮想サービスが更新されたことを示しています。

```output
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

次に、各コンポーネントの新しいバージョン `2.0` 用の Kubernetes オブジェクトを追加しましょう。 `voting-storage` サービスも、MySQL 用のポート `3306` を含むように更新します。

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

次の出力例では、Kubernetes オブジェクトが正常に更新または作成されたことが示されています。

```output
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

バージョン `2.0` のすべてのポッドが実行されるまで待ちます。 `-w` 監視スイッチを指定して [kubectl get pods][kubectl-get] コマンドを使用し、`voting` 名前空間内のすべてのポッドでの変更を監視します。

```console
kubectl get pods --namespace voting -w
```

これで、投票アプリケーションのバージョン `1.0` とバージョン `2.0` (カナリア) を切り替えられるようになります。 画面の下部にある機能フラグ トグルによって、Cookie が設定されます。 この Cookie は、`voting-app` 仮想サービスが新しいバージョンである `2.0` にユーザーをルーティングするために使用されます。

![バージョン 1.0 の AKS 投票アプリ - 機能フラグが設定されていない。](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![バージョン 2.0 の AKS 投票アプリ - 機能フラグが設定されている。](media/servicemesh/istio/scenario-routing-canary-release-02.png)

投票カウントは、アプリのバージョンによって異なります。 この違いにより、2 つの異なるストレージ バックエンドを使用していることが示されます。

## <a name="finalize-the-rollout"></a>ロールアウトを終了する

カナリア リリースのテストで問題がなければ、`voting-app` 仮想サービスを更新して、すべてのトラフィックが `voting-app` コンポーネントのバージョン `2.0` にルーティングされるようにします。 その後は、機能フラグが設定されているかどうかに関係なく、すべてのユーザーにアプリケーションのバージョン `2.0` が表示されます。

![機能フラグが設定されているかどうかに関係なく、アプリケーションのバージョン 2.0 がユーザーに表示されることを示す図。](media/servicemesh/istio/scenario-routing-components-04.png)

すべての宛先ルールを更新し、アクティブにする必要がなくなったコンポーネントのバージョンを削除します。 その後、すべての仮想サービスを更新して、これらのバージョンの参照を停止します。

古いバージョンのコンポーネントへのトラフィックがなくなったので、それらのコンポーネントに関するすべてのデプロイを安全に削除できます。

![AKS 投票アプリのコンポーネントとルーティング。](media/servicemesh/istio/scenario-routing-components-05.png)

AKS 投票アプリの新しいバージョンを正常にロールアウトできました。

## <a name="clean-up"></a>クリーンアップ 

次のように、`voting` 名前空間を削除することで、このシナリオで使用した AKS 投票アプリを自分の AKS クラスターから削除できます。

```console
kubectl delete namespace voting
```

次の出力例は、AKS 投票アプリのすべてのコンポーネントが自分の AKS クラスターから削除されたことを示しています。

```output
namespace "voting" deleted
```

## <a name="next-steps"></a>次のステップ

[Istio Bookinfo Application の例][istio-bookinfo-example]を使用する他のシナリオを調べることができます。

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.mesh.v1alpha1/#AuthenticationPolicy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./servicemesh-istio-install.md
