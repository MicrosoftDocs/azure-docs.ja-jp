---
title: Azure Kubernetes Services (AKS) における Kubernetes の基本概念
description: Kubernetes の基本のクラスターおよびワークロードについてと、クラスターおよびワークロードが Azure Kubernetes Service (AKS) の機能にどのように関連しているかを説明します。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: mlearned
ms.openlocfilehash: 5f387310e737982b824d0ac9662822d9a74f39e9
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616020"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Azure Kubernetes Services (AKS) における Kubernetes の中心概念

アプリケーション開発がコンテナーベースの手法に移行するにつれて、リソースを調整して管理する必要性が重要になります。 Kubernetes は、フォールト トレラント アプリケーションワークロードの信頼性の高いスケジュール機能を提供する主要プラットフォームです。 Azure Kubernetes Service (AKS) は、コンテナーベースのアプリケーション開発と管理をさらに簡素化するマネージド Kubernetes のオファリングです。

この記事では、*クラスター マスター*、*ノード*、および*ノード プール*など、主要な Kubernetes インフラストラクチャ コンポーネントを紹介します。 また、*プール*、*デプロイ*、および*セット*などのワークロード リソースと、リソースを*名前空間*にグループ化する方法も紹介します。

## <a name="what-is-kubernetes"></a>Kubernetes とは

Kubernetes は、コンテナー ベース アプリケーションとそれに関連するネットワークおよびストレージ コンポーネントを管理するプラットフォームであり、急速に進化しています。 基本のインフラストラクチャ コンポーネントではなく、アプリケーション ワークロードに重点を置いています。 Kubernetes は、管理操作のための一連の堅牢な API によって支援され、デプロイに対して宣言型のアプローチを提供しています。

アプリケーション コンポーネントの可用性を調整して管理する Kubernetes の機能を活かした、最新のポータブルなマイクロサービス ベース アプリケーションを構築して実行できます。 チームがマイクロサービス ベース アプリケーションを採用して作業を進める場合、Kubernetes は、ステートフルおよびステートレスの両方のアプリケーションをサポートします。

Kubernetes はオープン プラットフォームとして、使いたいプログラミング言語、OS、ライブラリ、またはメッセージング バスでアプリケーションを開発することを可能にします。 既存の継続的インテグレーションと継続的デリバリー (CI/CD) ツールは、リリースをスケジュール設定してデプロイするために、Kubernetes と連携できます。

Azure Kubernetes Service (AKS) は、アップグレードの調整など、デプロイ タスクと主要な管理タスクの複雑さを軽減する、マネージド Kubernetes サービスを提供します。 AKS クラスター マスターは Azure プラットフォームによって管理され、お使いのアプリケーションを実行する AKS ノードに対してのみ課金されます。 AKS は、オープンソースの Azure Kubernetes Service Engine ([aks-engine][aks-engine]) の最上位に構築されます。

## <a name="kubernetes-cluster-architecture"></a>Kubernetes クラスターのアーキテクチャ

Kubernetes クラスターは、次の 2 つのコンポーネントに分割されています。

- "*クラスター マスター*" ノード。主要な Kubernetes サービスと、アプリケーション ワークロード間の調整を提供します。
- "*ノード*"。アプリケーション ワークロードを実行します。

![Kubernetes クラスター マスターとノードのコンポーネント](media/concepts-clusters-workloads/cluster-master-and-nodes.png)

## <a name="cluster-master"></a>クラスター マスター

AKS クラスターを作成すると、クラスター マスターが自動的に作成され構成されます。 このクラスター マスターは、ユーザーから抽象化されたマネージド Azure リソースとして提供されます。 クラスター マスターには課金されません。AKS クラスターに属するノードに対してのみ課金されます。

クラスター マスターには、次の主要な Kubernetes コンポーネントが含まれます。

- *kube apiserver* - API サーバーは、基本となる Kubernetes API が公開される仕組みです。 このコンポーネントは、`kubectl` や Kubernetes ダッシュボードなど、管理ツールに対する操作を提供します。
- *etcd* - お使いの Kubernetes クラスターと構成の状態を維持するために、高可用性を備えた *etcd* は Kubernetes 内のキー値ストアです。
- *kube-scheduler* - スケジュールは、アプリケーションを作成またはスケーリングするときに、どのノードがワークロードを実行して開始できるかを判断します。
- *kube-controller-manager* - コントローラー マネージャーは、ポッドのレプリケーションやノード調整の処理などのアクションを実行する、より小規模な多数のコントローラーを監視します。

AKS は、専用の API サーバー、スケジューラなど、単一のテナント クラスター マスターを提供します。ノードの数とサイズを定義すると、Azure プラットフォームはクラスター マスターとノード間にセキュアな通信を構成します。 クラスター マスターの操作は、`kubectl` や Kubernetes ダッシュボードなどの、Kubernetes API を介して行われます。

このマネージド クラスター マスターによって、高可用性を備えた *etcd* ストアなどのコンポーネントを構成する必要はなくなりますが、それはまた、クラスター マスターに直接アクセスできないことも意味します。 Kubernetes のアップグレードは、Azure CLI または Azure portal 経由で調整され、クラスター マスターの次にノードのアップグレードが、順番に行われます。 想定される問題をトラブルシューティングするために、Azure Monitor ログからクラスター マスター ログを確認できます。

クラスター マスターを特定の方法で構成する必要がある場合や、クラスター マスターに直接アクセスする必要がある場合は、[aks-engine][aks-engine] を使用して、独自の Kubernetes クラスターをデプロイできます。

関連付けられているベスト プラクティスについては、[AKS でのクラスターのセキュリティとアップグレードに関するベスト プラクティス][operator-best-practices-cluster-security]のページを参照してください。

## <a name="nodes-and-node-pools"></a>ノードとノード プール

アプリケーションとサポート対象のサービスを実行するには、Kubernetes の*ノード*が必要です。 AKS クラスターには 1 つ以上のノードがあります。このノードは、Kubernetes ノード コンポーネントとコンテナー ランタイムを実行する Azure Vrtual Machine (VM) です。

- `kubelet` は、クラスター マスターからの調整要求と、要求されたコンテナーの実行のスケジュール設定を処理する Kubernetes エージェントです。
- 仮想ネットワークは、各ノード上の *kube-proxy* によって処理されます。 プロキシはネットワーク トラフィックをルーティングして、サービスとポッドの IP アドレスを管理します。
- *コンテナー ランタイム*は、コンテナー化されたアプリケーションが仮想ネットワークやストレージなどの追加リソースを実行して操作できるようにするためのコンポーネントです。 AKS では、Moby はコンテナー ランタイムとして使用されます。

![Kubernetes ノードの Azure 仮想マシンとサポート対象リソース](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

ノードの Azure VM サイズには、CPU 数、メモリ量、利用可能なストレージのサイズと種類 (高パフォーマンスの SSD や正規の HDD など) を定義します。 大容量の CPU およびメモリ、または高パフォーマンスのストレージを必要とするアプリケーションの需要を想定している場合は、それに沿ったノード サイズを計画してください。 また、需要に合わせて、AKS クラスター内のノード数をスケールアップすることも可能です。

AKS では、クラスター内のノードに対する VM イメージは現在、Ubuntu Linux または Windows Server 2019 に基づいています。 AKS クラスターを作成するか、またはノード数をスケールアップすると、Azure プラットフォームが、要求された数の VM を作成して構成します。 手動の構成を実行する必要はありません。 エージェント ノードは、標準の仮想マシンとして課金されるので、使用している VM サイズに対して付与されている割引 ([Azure の予約][reservation-discounts]を含む) は、自動的に適用されます。

別のホスト OS、コンテナー ランタイムを使用する必要がある場合や、クラスター パッケージを組み入れる必要がある場合、[aks-engine][aks-engine] を使用して独自の Kubernetes クラスターをデプロイできます。 アップストリームの `aks-engine` リリースでは構成オプションに注目し、AKS クラスターで公式にサポートされる前にそれらを提供しています。 たとえば、Moby 以外のコンテナー ランタイムの使用を検討している場合、`aks-engine` を使用して、現在のニーズに合った Kubernetes クラスターを構成してデプロイできます。

### <a name="resource-reservations"></a>リソース予約

Kubernetes のコア コンポーネントは、各ノード (*kubelet*、*kube-proxy*、*kube-dns* など) で管理する必要はありませんが、使用可能なコンピューティング リソースを一部消費します。 ノードのパフォーマンスと機能を維持するために、各ノードでは次のコンピューティング リソースが予約されます。

- **CPU** - 60 ミリ秒
- **メモリ** - 20% (最大 4 GiB)

これらの予約があるため、アプリケーションに使用可能な CPU とメモリの量は、ノード自体に含まれる量よりも小さく見える場合があります。 実行するアプリケーションの数によってリソースの制約が生じる場合は、これらの予約によって、Kubernetes のコア コンポーネントに使用可能な CPU とメモリが確保されます。 リソースの予約を変更することはできません。

例:

- **Standard DS2 v2** ノード サイズには、2 つの vCPU と 7 GiB のメモリが含まれます
    - 7 GiB メモリの 20% = 1.4 GiB
    - ノードで使用できるメモリの合計は、 *(7 - 1.4) = 5.6 GiB* になります
    
- **Standard E4s v3** ノード サイズには、4 つの vCPU と 32 GiB のメモリが含まれます
    - 32 GiB メモリの 20% = 6.4 GiB (ただし、AKS で予約されるのは最大で 4 GiB のみ)
    - ノードで使用できるメモリの合計は、 *(32 - 4) = 28 GiB* になります
    
基盤のノード OS にも、自身のコア機能を果たすために一定の CPU とメモリ リソースが必要になります。

関連付けられているベスト プラクティスについては、[AKS の基本的なスケジューラ機能のベスト プラクティス][operator-best-practices-scheduler]に関するページを参照してください。

### <a name="node-pools"></a>ノード プール

同じ構成のノードは、*ノード プール*にグループ化できます。 1 つの Kubernetes クラスターには、1 つ以上のノード プールが含まれています。 ノードとサイズの最初の数は、*既定のノード プール*の作成が行われる AKS クラスターの作成時に定義されています。 AKS のこの既定のノード プールには、お使いのエージェント ノードを実行する基本の VM が含まれています。 複数のノード プールのサポートは、現在 AKS ではプレビュー段階です。

AKS クラスターをスケーリングまたはアップグレードするとき、既定のノード プールに対してアクションが実行されます。 特定のノード プールに対して、スケーリングまたはアップグレードのいずれを選択することもできます。 アップグレード操作では、すべてのノードが正常にアップグレードされるまで、実行中のコンテナーはノード プ―ル内の他のノード上にスケジュールされます。

AKS での複数のノード プールの使用方法の詳細については、[AKS でのクラスターの複数のノード プールの作成と管理][use-multiple-node-pools]に関する記事をご覧ください。

### <a name="node-selectors"></a>ノードのセレクター

複数のノード プールを含む AKS クラスターでは、特定のリソースに使用するノード プールを、Kubernetes スケジューラに指示することができます。 たとえば、イングレス コントローラーは、Windows Server ノードで実行しないでください (現在、AKS でプレビュー中)。 ノード セレクターでは、ノード OS などのさまざまなパラメーターを定義でき、ポッドがスケジュールされる場所を制御できます。

次の基本的な例は、ノード セレクター *"beta.kubernetes.io/os": linux* を使用して Linux ノード上の NGINX インスタンスをスケジュールします。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx:1.15.12
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

ポッドがスケジュールされる場所を制御する方法の詳細については、「[Azure Kubernetes Service (AKS) での高度なスケジューラ機能に関するベスト プラクティス][operator-best-practices-advanced-scheduler]」を参照してください。

## <a name="pods"></a>ポッド

Kubernetes は*ポッド*を使用して、お使いのアプリケーションのインスタンスを実行します。 ポッドは、アプリケーションの単一のインスタンスを表します。 ポッドは通常、コンテナーと 1 対 1 のマッピングを保持していますが、ポッドに複数のコンテナーを含むことができる高度なシナリオもあります。 これらのマルチコンテナー ポッドは、同じノード上にまとめてスケジュールされ、コンテナーが関連するリソースを共有できるようにします。

ポッドを作成する場合、*リソースの上限*を定義して、一定量の CPU やメモリ リソースを要求できます。 Kubernetes スケジューラは、要件を満たす利用可能なリソースを備えたノード上で実行されるように、ポッドのスケジュール設定を試みます。 また、指定されたポッドでの基本ノードからのコンピューティング リソースの消費量が過大にならないように、リソースの上限を指定することも可能です。 ベスト プラクティスは、必要かつ許可されているリソースがどれかを Kubernetes スケジューラが認識できるように、すべてのポッドにリソース制限を組み入れることです。

詳細については、[Kubernetes ポッドに関するページと、][kubernetes-pods]Kubernetes ポッドのライフサイクル and [Kubernetes pod lifecycle][kubernetes-pod-lifecycle] に関するページを参照してください。

ポッドは論理リソースですが、コンテナーは、アプリケーション ワークロードが実行される場所です。 ポッドは通常、短期間の破棄可能なリソースであり、個々にスケジュール設定されたポッドでは、Kubernetes が提供する高可用性と冗長性の一部の機能が欠落します。 ポッドは通常、個々にスケジュール設定されず、デプロイ コントローラーなどの Kubernetes の "*コントローラー*" によってデプロイされ管理されます。

## <a name="deployments-and-yaml-manifests"></a>デプロイと YAML マニフェスト

1 つの*デプロイ*は 1 つ以上の同一ポッドに相当し、Kubernetes デプロイ コントローラーによって管理されます。 デプロイでは、作成する*レプリカ* (ポッド) の数を定義し、Kubernetes スケジューラは、ポッドまたはノードで問題が発生した場合に、追加のポッドが正常なノード上にスケジュールされることを保証します。

デプロイを更新して、ポッドの構成、使用されるコンテナー イメージの構成、またはアタッチされたストレージの構成を変更できます。 デプロイ コントローラーは、指定された数のレプリカをドレインして終了し、新しいデプロイ定義からレプリカを作成して、デプロイ内のすべてのレプリカが更新されるまで、プロセスを継続します。

AKS にある最もステートレスなアプリケーションでは、個々のポッドをスケジュール設定するのではなく、デプロイ モデルを使用する必要があります。 Kubernetes では、必要な数のレプリカがクラスター内で確実に実行されるように、デプロイの正常性と状態を監視できます。 個々のポッドのスケジュール設定だけを行うと、ポッドで問題が発生した場合、そのポッドは再起動されません。また、ポッドの現在のノードで問題が発生した場合、ポッドは正常なノード上に再スケジュールされません。

アプリケーションが、管理上の判断に常に利用できるインスタンスのクォーラムを必要としている場合、更新プロセスによってその機能が中断されることは望ましくありません。 *ポッドの中断バジェット*は、更新またはノードのアップグレード期間中にデプロイ内で取り下げ可能なレプリカ数を定義するために使用できます。 たとえば、デプロイ内に *5* 個のレプリカがある場合、ポッド中断数を *4* と定義して、一度に削除/再スケジュールが許可されるレプリカ数を 1 個のみにすることが可能です。 ポッドのリソース制限と同様に、ベスト プラクティスは、レプリカの最小数を常に提示する必要があるアプリケーション上に、ポッドの中断バジェットを定義することです。

デプロイは通常、`kubectl create` または `kubectl apply` で作成および管理されます。 デプロイを作成するには、YAML (YAML ではマークアップ言語を使用しない) 形式のマニフェスト ファイルを定義します。 次の例では、NGINX Web サーバーの基本のデプロイを作成します。 デプロイでは、*3* 個のレプリカを作成すること、また、ポート *80* をコンテナー上に開くことを指定します。 また、CPU とメモリに対しては、リソース要求と制限が定義されます。

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
        image: nginx:1.15.2
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

Helm を使用するために、Kubernetes クラスターに *Tiller* と呼ばれるサーバー コンポーネントがインストールされます。 Tiller では、クラスター内のチャートのインストールを管理します。 Helm クライアント自体は、お使いのコンピューターにローカルにインストールするか、または [Azure Cloud Shell][azure-cloud-shell] 内で使用できます。 クライアントの Helm Chart を検索または作成して、お使いの Kubernetes クラスターにインストールできます。

![Helm には、クライアント コンポーネントと Kubernetes クラスター内にリソースを作成するサーバー側の Tiller コンポーネントが含まれます。](media/concepts-clusters-workloads/use-helm.png)

詳細については、「[Azure Kubernetes Service (AKS) での Helm を使用したアプリケーションのインストール][aks-helm]」を参照してください。

## <a name="statefulsets-and-daemonsets"></a>StatefulSet と DaemonSet

デプロイ コントローラーでは、Kubernetes スケジューラを使って、使用できるリソースがある任意の利用可能なノード上で、指定された数のレプリカを実行します。 デプロイを使用するこの方法は、ステートレス アプリケーションでは十分なかもしれませんが、永続的な名前規則やストレージを必要とするアプリケーションでは、不十分です。 1 つのクラスター内で、各ノード上または選択されたノード上にレプリカが存在することが必要なアプリケーションの場合、デプロイ コントローラーでは、ノード間にレプリカが配信される方法を確認しません。

これらの種類のアプリケーションを管理できる Kubernetes リソースとして、次の 2 つがあります。

- *StatefulSet* - ストレージなど、個々のポッド ライフサイクルを超えて、アプリケーションの状態を保守します。
- *DaemonSet* - Kubernetes ブートストラップ プロセスの初期段階で、各ノード上で実行されるインスタンスを確保します。

### <a name="statefulsets"></a>StatefulSet

最新のアプリケーション開発では、通常、ステートレス アプリケーションを目指しますが、*StatefulSet* はデータベース コンポーネントを含むアプリケーションなど、ステートフル アプリケーションに使用できます。 StatefulSet は、1 つ以上のポッドが作成され管理されるデプロイとほぼ同じです。 デプロイ、スケーリング、アップグレード、および強制終了において、StatefulSet 内のレプリカは、適切な順序付けの手法に従います。 1 つのStatefulSet を使用すると、レプリカが再スケジュールされるときに、名前規則、ネットワーク名、およびストレージが永続化します。

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

ポッドやデプロイなどの Kubernetes リソースは、論理的に 1 つの*名前空間*にグループ化されます。 これらのグループ化は、論理的に AKS クラスターを分割する方法を提供し、リソースを作成、表示、または管理するためのアクセスを制限します。 たとえば、名前空間を作成して業務グループを分けることができます。 ユーザーは、割り当てられている名前空間内のリソースのみを操作できます。

![リソースとアプリケーションを論理的に分割する Kubernetes の名前空間](media/concepts-clusters-workloads/namespaces.png)

AKS クラスターを作成すると、次の名前空間が利用可能になります。

- *既定* - この名前空間は、何も指定されない場合に、既定でポッドおよびデプロイが作成される場所です。 より小規模な環境では、追加の論理分割を作成せずに、既定の名前空間にアプリケーションを直接デプロイできます。 `kubectl get pods` などの Kubernetes API を操作する場合、何も指定しないと、既定の名前空間が使用されます。
- *kube-system* - この名前空間は、DNS とプロキシ、または Kubernetes ダッシュボードのようなネットワーク機能など、重要なリソースが置かれている場所です。 通常は、この名前空間に独自のアプリケーションをデプロイしません。
- *kube-public* - この名前空間は、通常は使用されませんが、クラスター全体でリソースを表示可能にして、ユーザーが確認できるようにするために使用できます。

詳細については、[Kubernetes の名前空間][kubernetes-namespaces] に関するページを参照してください。

## <a name="next-steps"></a>次の手順

この記事では、Kubernetes の主要なコンポーネントの一部と、それらのコンポーネントを AKS クラスターに適用する方法について説明しました。 Kubernetes と AKS の中心概念の追加情報については、次の記事を参照してください。

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
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md