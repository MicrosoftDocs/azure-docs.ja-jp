---
title: Azure Kubernetes Services (AKS) における Kubernetes の基本概念
description: Kubernetes の基本のクラスターおよびワークロードについてと、クラスターおよびワークロードが Azure Kubernetes Service (AKS) の機能にどのように関連しているかを説明します。
services: container-service
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 2a1718d906ab5f51ea71be9b304028576c9fffa0
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122444"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Azure Kubernetes Services (AKS) における Kubernetes の中心概念

アプリケーション開発がコンテナーベースの手法に移行するにつれて、リソースを調整して管理する必要性が重要になります。 Kubernetes は、フォールト トレラント アプリケーションワークロードの信頼性の高いスケジュール機能を提供する主要プラットフォームです。 Azure Kubernetes Service (AKS) は、コンテナーベースのアプリケーション開発と管理をさらに簡素化するマネージド Kubernetes のオファリングです。

この記事では、"*コントロール プレーン*"、"*ノード*"、および "*ノード プール*" などの主要な Kubernetes インフラストラクチャ コンポーネントを紹介します。 また、*プール*、*デプロイ*、および *セット* などのワークロード リソースと、リソースを *名前空間* にグループ化する方法も紹介します。

## <a name="what-is-kubernetes"></a>Kubernetes とは

Kubernetes は、コンテナー ベース アプリケーションとそれに関連するネットワークおよびストレージ コンポーネントを管理するプラットフォームであり、急速に進化しています。 基本のインフラストラクチャ コンポーネントではなく、アプリケーション ワークロードに重点を置いています。 Kubernetes は、管理操作のための一連の堅牢な API によって支援され、デプロイに対して宣言型のアプローチを提供しています。

アプリケーション コンポーネントの可用性を調整して管理する Kubernetes の機能を活かした、最新のポータブルなマイクロサービス ベース アプリケーションを構築して実行できます。 チームがマイクロサービス ベース アプリケーションを採用して作業を進める場合、Kubernetes は、ステートフルおよびステートレスの両方のアプリケーションをサポートします。

Kubernetes はオープン プラットフォームとして、使いたいプログラミング言語、OS、ライブラリ、またはメッセージング バスでアプリケーションを開発することを可能にします。 既存の継続的インテグレーションと継続的デリバリー (CI/CD) ツールは、リリースをスケジュール設定してデプロイするために、Kubernetes と連携できます。

Azure Kubernetes Service (AKS) は、アップグレードの調整など、デプロイ タスクと主要な管理タスクの複雑さを軽減する、マネージド Kubernetes サービスを提供します。 AKS コントロール プレーンは Azure プラットフォームによって管理され、お使いのアプリケーションを実行する AKS ノードに対してのみ課金されます。 AKS は、オープンソースの Azure Kubernetes Service Engine ([aks-engine][aks-engine]) の最上位に構築されます。

## <a name="kubernetes-cluster-architecture"></a>Kubernetes クラスターのアーキテクチャ

Kubernetes クラスターは、次の 2 つのコンポーネントに分割されています。

- "*コントロール プレーン*"。主要な Kubernetes サービスと、アプリケーション ワークロードのオーケストレーションを提供します。
- "*ノード*"。アプリケーション ワークロードを実行します。

![Kubernetes のコントロール プレーンとノードのコンポーネント](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>コントロール プレーン

AKS クラスターを作成すると、コントロール プレーンが自動的に作成されて構成されます。 このコントロール プレーンは、ユーザーから抽象化されたマネージド Azure リソースとして提供されます。 コントロール プレーンは課金対象にはなりません。AKS クラスターに属するノードのみが対象になります。 コントロール プレーンとそのリソースは、クラスターを作成したリージョンにのみ存在します。

コントロール プレーンには、次の主要な Kubernetes コンポーネントが含まれます。

- *kube apiserver* - API サーバーは、基本となる Kubernetes API が公開される仕組みです。 このコンポーネントは、`kubectl` や Kubernetes ダッシュボードなど、管理ツールに対する操作を提供します。
- *etcd* - お使いの Kubernetes クラスターと構成の状態を維持するために、高可用性を備えた *etcd* は Kubernetes 内のキー値ストアです。
- *kube-scheduler* - スケジュールは、アプリケーションを作成またはスケーリングするときに、どのノードがワークロードを実行して開始できるかを判断します。
- *kube-controller-manager* - コントローラー マネージャーは、ポッドのレプリケーションやノード調整の処理などのアクションを実行する、より小規模な多数のコントローラーを監視します。

AKS は、専用の API サーバーや Scheduler などを備えたシングル テナント コントロール プレーンを提供します。ノードの数とサイズを定義すると、Azure プラットフォームによって、コントロール プレーンとノードの間のセキュリティで保護された通信が構成されます。 コントロール プレーンとの対話は、`kubectl` や Kubernetes ダッシュボードなどの Kubernetes API を介して行われます。

このマネージ コントロール プレーンによって、高可用性を備えた *etcd* ストアなどのコンポーネントを構成する必要はなくなりますが、それはまた、コントロール プレーンに直接アクセスできないことも意味します。 Kubernetes へのアップグレードは、Azure CLI または Azure portal を介して調整され、コントロール プレーン、そして次にノードの順にアップグレードされます。 発生する可能性のある問題のトラブルシューティングを行うために、Azure Monitor ログからコントロール プレーンのログを確認できます。

コントロール プレーンを特定の方法で構成したり、それに直接アクセスしたりする必要がある場合は、[aks-engine][aks-engine] を使用して、独自の Kubernetes クラスターをデプロイできます。

関連付けられているベスト プラクティスについては、[AKS でのクラスターのセキュリティとアップグレードに関するベスト プラクティス][operator-best-practices-cluster-security]のページを参照してください。

## <a name="nodes-and-node-pools"></a>ノードとノード プール

アプリケーションとサポート対象のサービスを実行するには、Kubernetes の *ノード* が必要です。 AKS クラスターには 1 つ以上のノードがあります。このノードは、Kubernetes ノード コンポーネントとコンテナー ランタイムを実行する Azure Vrtual Machine (VM) です。

- `kubelet` は、コントロール プレーンからのオーケストレーション要求と、要求されたコンテナーの実行のスケジュール設定を処理する Kubernetes エージェントです。
- 仮想ネットワークは、各ノード上の *kube-proxy* によって処理されます。 プロキシはネットワーク トラフィックをルーティングして、サービスとポッドの IP アドレスを管理します。
- *コンテナー ランタイム* は、コンテナー化されたアプリケーションが仮想ネットワークやストレージなどの追加リソースを実行して操作できるようにするためのコンポーネントです。 Kubernetes バージョン 1.19 以降のノード プールを使用する AKS クラスターでは、コンテナー ランタイムとして `containerd` が使用されます。 Kubernetes v1.19 よりも前のノード プールを使用する AKS クラスターでは、コンテナー ランタイムとして [Moby](https://mobyproject.org/) (アップストリーム Docker) が使用されます。

![Kubernetes ノードの Azure 仮想マシンとサポート対象リソース](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

ノードの Azure VM サイズには、CPU 数、メモリ量、利用可能なストレージのサイズと種類 (高パフォーマンスの SSD や正規の HDD など) を定義します。 大容量の CPU およびメモリ、または高パフォーマンスのストレージを必要とするアプリケーションの需要を想定している場合は、それに沿ったノード サイズを計画してください。 また、需要に合わせて、AKS クラスター内のノード数をスケールアウトすることも可能です。

AKS では、クラスター内のノードに対する VM イメージは現在、Ubuntu Linux または Windows Server 2019 に基づいています。 AKS クラスターを作成するか、またはノード数をスケールアウトすると、Azure プラットフォームが、要求された数の VM を作成して構成します。 手動の構成を実行する必要はありません。 エージェント ノードは、標準の仮想マシンとして課金されるので、使用している VM サイズに対して付与されている割引 ([Azure の予約][reservation-discounts]を含む) は、自動的に適用されます。

別のホスト OS、コンテナー ランタイムを使用する必要がある場合や、クラスター パッケージを組み入れる必要がある場合、[aks-engine][aks-engine] を使用して独自の Kubernetes クラスターをデプロイできます。 アップストリームの `aks-engine` リリースでは構成オプションに注目し、AKS クラスターで公式にサポートされる前にそれらを提供しています。 たとえば、`containerd` または Moby 以外のコンテナー ランタイムの使用を検討している場合、`aks-engine` を使用して、現在のニーズに合った Kubernetes クラスターを構成してデプロイできます。

### <a name="resource-reservations"></a>リソース予約

ノード リソースは、お使いのクラスターの一部としてノードを機能させる目的で AKS により活用されます。 この使用により、お使いのノードの合計リソースと AKS で利用するときに割り当て可能なリソースが一致しなくなることがあります。 この情報は、ユーザーがデプロイするポッドに要求と上限を設定するとき、念頭に入れておくことが重要です。

ノードの割り当てできるリソースを見つけるには、次を実行します。
```kubectl
kubectl describe node [NODE_NAME]
```

ノードのパフォーマンスと機能を維持するために、AKS により各ノードでリソースが予約されます。 あるノードがリソースの中で大きくなると、ユーザーがデプロイする管理を必要なポッドの数量が増加し、リソース予約が増大します。

>[!NOTE]
> Container Insights (OMS) などの AKS アドオンを使用すると、追加のノード リソースが消費されます。

2 種類のリソースが予約されています。

- **CPU** - 予約された CPU は、ノードの種類とクラスター構成に依存します。追加の機能が実行されている場合は、割り当てることができる CPU が少なくなることがあります

   | ホスト上の CPU コア数 | 1    | 2    | 4    | 8    | 16 | 32|64|
   |---|---|---|---|---|---|---|---|
   |Kube 予約 (ミリコア)|60|100|140|180|260|420|740|

- **メモリ** - AKS で使用されるメモリには、2 つの値の合計が含まれます。

   1. Kubelet デーモンは、コンテナーの作成と終了を管理するために、すべての Kubernetes エージェント ノードにインストールされます。 AKS の既定では、このデーモンには、*memory.available<750Mi* という削除規則があります。つまり、ノードでは常に、少なくとも 750Mi が割り当て可能になっている必要があります。  ホストがその使用可能メモリのしきい値を下回ると、kubelet は実行中のポッドの 1 つを終了して、ホスト コンピューターのメモリを解放して保護します。 これは、使用できるメモリがしきい値 750Mi を下回ったときにトリガーされます。

   2. 2 番目の値は、kubelet デーモンが適切に機能するために予約されているメモリの回帰率です (kube-reserved)。
      - 最初の 4 GB のメモリの 25%
      - 次の 4 GB のメモリの 20% (最大 8 GB)
      - 次の 8 GB のメモリの 10% (最大 16 GB)
      - 次の 112 GB のメモリの 6% (最大 128 GB)
      - 128 GB を超えるメモリの 2%

メモリと CPU の割り当てに関する上記の規則は、クラスターの正常性に不可欠ないくつかのホスティング システム ポッドを含むエージェント ノードを正常な状態に保つために使用されます。 これらの割り当て規則により、ノードが通常報告する割り当て可能なメモリと CPU は、それが Kubernetes クラスターに含まれていない場合よりも少なくなります。 上記のリソースの予約を変更することはできません。

たとえば、ノードで 7 GB が提供される場合、750Mi のハード削除しきい値を含めて、メモリの 34% が割り当て不可として報告されます。

`0.75 + (0.25*4) + (0.20*3) = 0.75GB + 1GB + 0.6GB = 2.35GB / 7GB = 33.57% reserved`

Kubernetes 自体の予約に加えて、基になるノード OS も OS 機能を維持するための CPU とメモリ リソースの量を予約します。

関連付けられているベスト プラクティスについては、[AKS の基本的なスケジューラ機能のベスト プラクティス][operator-best-practices-scheduler]に関するページを参照してください。

### <a name="node-pools"></a>ノード プール

同じ構成のノードは、*ノード プール* にグループ化できます。 1 つの Kubernetes クラスターには、1 つ以上のノード プールが含まれています。 ノードとサイズの最初の数は、*既定のノード プール* の作成が行われる AKS クラスターの作成時に定義されています。 AKS のこの既定のノード プールには、お使いのエージェント ノードを実行する基本の VM が含まれています。

> [!NOTE]
> クラスターが確実に動作するようにするには、既定のノード プールで少なくとも 2 つのノードを実行する必要があります。

AKS クラスターをスケーリングまたはアップグレードするとき、既定のノード プールに対してアクションが実行されます。 特定のノード プールに対して、スケーリングまたはアップグレードのいずれを選択することもできます。 アップグレード操作では、すべてのノードが正常にアップグレードされるまで、実行中のコンテナーはノード プ―ル内の他のノード上にスケジュールされます。

AKS での複数のノード プールの使用方法の詳細については、[AKS でのクラスターの複数のノード プールの作成と管理][use-multiple-node-pools]に関する記事をご覧ください。

### <a name="node-selectors"></a>ノードのセレクター

複数のノード プールを含む AKS クラスターでは、特定のリソースに使用するノード プールを、Kubernetes スケジューラに指示することができます。 たとえば、Windows Server ノード上でイングレス コントローラーを実行することはできません。 ノード セレクターでは、ノード OS などのさまざまなパラメーターを定義でき、ポッドがスケジュールされる場所を制御できます。

次の基本的な例は、ノード セレクター *"beta.kubernetes.io/os": linux* を使用して Linux ノード上の NGINX インスタンスをスケジュールします。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.12-alpine
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

ポッドがスケジュールされる場所を制御する方法の詳細については、「[Azure Kubernetes Service (AKS) での高度なスケジューラ機能に関するベスト プラクティス][operator-best-practices-advanced-scheduler]」を参照してください。

## <a name="pods"></a>ポッド

Kubernetes は *ポッド* を使用して、お使いのアプリケーションのインスタンスを実行します。 ポッドは、アプリケーションの単一のインスタンスを表します。 ポッドは通常、コンテナーと 1 対 1 のマッピングを保持していますが、ポッドに複数のコンテナーを含むことができる高度なシナリオもあります。 これらのマルチコンテナー ポッドは、同じノード上にまとめてスケジュールされ、コンテナーが関連するリソースを共有できるようにします。

ポッドを作成する場合、"*リソース要求*" を定義して、一定量の CPU やメモリ リソースを要求できます。 Kubernetes スケジューラは、要件を満たす利用可能なリソースを備えたノード上で実行されるように、ポッドのスケジュール設定を試みます。 また、指定されたポッドでの基本ノードからのコンピューティング リソースの消費量が過大にならないように、リソースの上限を指定することも可能です。 ベスト プラクティスは、必要かつ許可されているリソースがどれかを Kubernetes スケジューラが認識できるように、すべてのポッドにリソース制限を組み入れることです。

詳細については、[Kubernetes ポッド][kubernetes-pods]と [Kubernetes ポッドのライフサイクル][kubernetes-pod-lifecycle]に関するページをご覧ください。

ポッドは論理リソースですが、コンテナーは、アプリケーション ワークロードが実行される場所です。 ポッドは通常、短期間の破棄可能なリソースであり、個々にスケジュール設定されたポッドでは、Kubernetes が提供する高可用性と冗長性の一部の機能が欠落します。 代わりに、ポッドは、デプロイ コントローラーなどの Kubernetes の *コントローラー* によってデプロイされ管理されます。

## <a name="deployments-and-yaml-manifests"></a>デプロイと YAML マニフェスト

1 つの *デプロイ* は 1 つ以上の同一ポッドに相当し、Kubernetes デプロイ コントローラーによって管理されます。 デプロイでは、作成する *レプリカ* (ポッド) の数を定義し、Kubernetes スケジューラは、ポッドまたはノードで問題が発生した場合に、追加のポッドが正常なノード上にスケジュールされることを保証します。

デプロイを更新して、ポッドの構成、使用されるコンテナー イメージの構成、またはアタッチされたストレージの構成を変更できます。 デプロイ コントローラーは、指定された数のレプリカをドレインして終了し、新しいデプロイ定義からレプリカを作成して、デプロイ内のすべてのレプリカが更新されるまで、プロセスを継続します。

AKS にある最もステートレスなアプリケーションでは、個々のポッドをスケジュール設定するのではなく、デプロイ モデルを使用する必要があります。 Kubernetes では、必要な数のレプリカがクラスター内で確実に実行されるように、デプロイの正常性と状態を監視できます。 個々のポッドのスケジュール設定だけを行うと、ポッドで問題が発生した場合、そのポッドは再起動されません。また、ポッドの現在のノードで問題が発生した場合、ポッドは正常なノード上に再スケジュールされません。

アプリケーションが、管理上の判断に常に利用できるインスタンスのクォーラムを必要としている場合、更新プロセスによってその機能が中断されることは望ましくありません。 *ポッドの中断バジェット* は、更新またはノードのアップグレード期間中にデプロイ内で取り下げ可能なレプリカ数を定義するために使用できます。 たとえば、ご自分のデプロイ内に *5* 個のレプリカがある場合、ポッド中断数を *4* と定義して、一度に削除および再スケジュールできるレプリカ数を 1 個のみにすることが可能です。 ポッドのリソース制限と同様に、ベスト プラクティスは、レプリカの最小数を常に提示する必要があるアプリケーション上に、ポッドの中断バジェットを定義することです。

デプロイは通常、`kubectl create` または `kubectl apply` で作成および管理されます。 デプロイを作成するには、YAML (YAML ではマークアップ言語を使用しない) 形式のマニフェスト ファイルを定義します。 次の例では、NGINX Web サーバーの基本のデプロイを作成します。 デプロイでは、*3* 個のレプリカを作成すること、またポート *80* をコンテナー上に開くことを指定します。 また、CPU とメモリに対しては、リソース要求と制限が定義されます。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: mcr.microsoft.com/oss/nginx/nginx:1.15.2-alpine
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

YAML マニフェスト内にロード バランサーなどのサービスも含めると、より複雑なアプリケーションを作成できます。

詳細については、[Kubernetes のデプロイ][kubernetes-deployments]に関するページをご覧ください。

### <a name="package-management-with-helm"></a>Helm によるパッケージの管理

Kubernetes のアプリケーションを管理する一般的な方法としては、[Helm][helm] を使用します。 アプリケーション コードのパッケージ化されたバージョンと、Kubernetes YAML マニフェストを含む既存のパブリック Helm *Chart* を構築して使用し、リソースをデプロイできます。 これらの Helm Chart はローカルに保存したり、多くの場合、[Azure Container Registry の Helm Chart リポジトリ][acr-helm]などのリモート リポジトリ内に保存したりできます。

Helm を使用するには、コンピューターに Helm クライアントをインストールするか、[Azure Cloud Shell][azure-cloud-shell] で Helm クライアントを使用します。 クライアントの Helm Chart を検索または作成して、お使いの Kubernetes クラスターにインストールできます。 詳細については、「[AKS で Helm を使用して既存のアプリケーションをインストールする][aks-helm]」を参照してください。

## <a name="statefulsets-and-daemonsets"></a>StatefulSet と DaemonSet

デプロイ コントローラーでは、Kubernetes スケジューラを使って、使用できるリソースがある任意の利用可能なノード上で、指定された数のレプリカを実行します。 デプロイを使用するこの方法は、ステートレス アプリケーションでは十分なかもしれませんが、永続的な名前規則やストレージを必要とするアプリケーションでは、不十分です。 1 つのクラスター内で、各ノード上または選択されたノード上にレプリカが存在することが必要なアプリケーションの場合、デプロイ コントローラーでは、ノード間にレプリカが配信される方法を確認しません。

これらの種類のアプリケーションを管理できる Kubernetes リソースとして、次の 2 つがあります。

- *StatefulSet* - ストレージなど、個々のポッド ライフサイクルを超えて、アプリケーションの状態を保守します。
- *DaemonSet* - Kubernetes ブートストラップ プロセスの初期段階で、各ノード上で実行されるインスタンスを確保します。

### <a name="statefulsets"></a>StatefulSet

最新のアプリケーション開発では、通常、ステートレス アプリケーションを目指しますが、*StatefulSet* はデータベース コンポーネントを含むアプリケーションなど、ステートフル アプリケーションに使用できます。 StatefulSet は、1 つ以上のポッドが作成され管理されるデプロイとほぼ同じです。 デプロイ、スケーリング、アップグレード、および強制終了において、StatefulSet 内のレプリカは、適切な順序付けの手法に従います。 StatefulSet では (レプリカが再スケジュールされるときに)、名前規則、ネットワーク名、およびストレージが永続化されます。

`kind: StatefulSet` を使用して YAML 形式でアプリケーションを定義すると、StatefulSet コントローラーは要求されたレプリカのデプロイと管理を処理します。 データは、Azure Managed Disks または Azure Files によって提供された永続的なストレージに書き込まれます。 複数の StatefulSet を使用すると、StatefulSet が削除されても、基本の永続化ストレージは残ります。

詳細については、[Kubernetes の StatefulSet][kubernetes-statefulsets] に関するページを参照してください。

StatefulSet 内のレプリカは、1 つの AKS クラスター内にある任意の利用可能なノード全体にスケジュールされて、実行されます。 セット内の 1 つ以上のポッドが、ノード上で実行されることを保証する必要がある場合は、代わりに、DaemonSet を使用できます。

### <a name="daemonsets"></a>DaemonSet

特定のログ コレクションや監視のニーズのために、すべてのノード上または選択されたノード上で、指定されたポッドを実行する必要が生じる場合があります。 この場合も、1 つ以上の同一ポッドをデプロイするために *DaemonSet* が使用されますが、DaemonSet コントローラーが、指定された各ノードでポッドのインスタンスが実行されることを保証します。

DaemonSet コントローラーでは、既定の Kubernetes スケジューラが起動済みになる前に、クラスターのブート プロセスの初期段階でノード上にポッドをスケジュール設定できます。 この機能によって、1 つのデプロイまたは StatefulSet 内の従来のポッドがスケジュールされる前に、確実に DaemonSet 内のポッドが開始されます。

StatefulSet と同様に、DaemonSet は `kind: DaemonSet` を使用して、YAML 定義の一部として定義されています。

詳細については、[Kubernetes の DaemonSet][kubernetes-daemonset] に関するページを参照してください。

> [!NOTE]
> [仮想ノードのアドオン](virtual-nodes-cli.md#enable-virtual-nodes-addon)を使用している場合は、DaemonSet によって仮想ノード上にポッドが作成されることはありません。

## <a name="namespaces"></a>名前空間

ポッドやデプロイなどの Kubernetes リソースは、論理的に 1 つの *名前空間* にグループ化されます。 これらのグループ化は、論理的に AKS クラスターを分割する方法を提供し、リソースを作成、表示、または管理するためのアクセスを制限します。 たとえば、名前空間を作成して業務グループを分けることができます。 ユーザーは、割り当てられている名前空間内のリソースのみを操作できます。

![リソースとアプリケーションを論理的に分割する Kubernetes の名前空間](media/concepts-clusters-workloads/namespaces.png)

AKS クラスターを作成すると、次の名前空間が利用可能になります。

- *既定* - この名前空間は、何も指定されない場合に、既定でポッドおよびデプロイが作成される場所です。 より小規模な環境では、追加の論理分割を作成せずに、既定の名前空間にアプリケーションを直接デプロイできます。 `kubectl get pods` などの Kubernetes API を操作する場合、何も指定しないと、既定の名前空間が使用されます。
- *kube-system* - この名前空間は、DNS とプロキシ、または Kubernetes ダッシュボードのようなネットワーク機能など、重要なリソースが置かれている場所です。 通常は、この名前空間に独自のアプリケーションをデプロイしません。
- *kube-public* - この名前空間は、通常は使用されませんが、クラスター全体でリソースを表示可能にして、ユーザーが確認できるようにするために使用できます。

詳細については、[Kubernetes の名前空間][kubernetes-namespaces] に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、Kubernetes の主要なコンポーネントの一部と、それらのコンポーネントを AKS クラスターに適用する方法について説明しました。 Kubernetes と AKS の中心概念の詳細については、次の記事を参照してください。

- [Kubernetes/AKS のアクセスと ID][aks-concepts-identity]
- [Kubernetes/AKS のセキュリティ][aks-concepts-security]
- [Kubernetes/AKS の仮想ネットワーク][aks-concepts-network]
- [Kubernetes/AKS のストレージ][aks-concepts-storage]
- [Kubernetes/AKS のスケール][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
