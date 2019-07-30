---
title: オペレーターのベスト プラクティス - Azure Kubernetes Services (AKS) の高度なスケジューラ機能
description: テイントと容認、ノード セレクターとアフィニティ、ポッド間アフィニティと非アフィニティなど、Azure Kubernetes Service (AKS) の高度なスケジューラ機能の使用に関する、クラスター オペレーターのベスト プラクティスについて説明します
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: mlearned
ms.openlocfilehash: 4caa4219d2bf7558dbdf71e92e4993722c6e8f6a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614875"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での高度なスケジューラ機能に関するベスト プラクティス

Azure Kubernetes Service (AKS) でクラスターを管理する際は、多くの場合、チームとワークロードを分離する必要があります。 Kubernetes スケジューラで提供されている高度な機能を使用すると、特定のノードでスケジュールできるポッドや、マルチポッド アプリケーションをクラスター全体に適切に分散させる方法を、制御することができます。 

このベスト プラクティス記事では、クラスター オペレーター向けに Kubernetes の高度なスケジュール機能について説明します。 この記事では、次のことについて説明します。

> [!div class="checklist"]
> * テイントと容認を使用して、ノードでスケジュールできるポッドを制限する
> * ノード セレクターまたはノード アフィニティを使用して、特定のノードで実行するポッドに優先順位を指定する
> * ポッド間アフィニティまたは非アフィニティを使用して、ポッドを分けたりまとめたりする

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>テイントと容認を使用して専用のノードを提供する

**ベスト プラクティス ガイダンス** - イングレス コントローラーのようなリソースを消費するアプリケーションのアクセスを特定のノードに制限します。 ノードのリソースをそれが必要なワークロードで使用できるように保ち、そのノードでは他のワークロードをスケジュールできないようにします。

AKS クラスターを作成するときは、GPU のサポートや多数の強力な CPU を備えたノードをデプロイできます。 このようなノードは、機械学習 (ML) や人工知能 (AI) などの大規模なデータ処理ワークロードによく使用されます。 通常、この種のハードウェアはデプロイするのが高価なノード リソースなので、これらのノードでスケジュールできるワークロードを制限します。 代わりに、クラスターの一部のノードをイングレス サービスの実行専用にして、他のワークロードを防ぐこともできます。

さまざまなノードに対するこのサポートは、複数のノード プールを使用して提供されます。 AKS クラスターでは、1 つ以上のノード プールが提供されます。 AKS での複数のノード プールのサポートは現在プレビュー段階です。

Kubernetes スケジューラでは、テイントと容認を使用して、ノードで実行できるワークロードを制限できます。

* **テイント**は、ノードに適用されて、特定のポッドのみをそのノードでスケジュールできることを示します。
* **容認**は、ポッドに適用されて、ポッドがノードのテイントを "*許容する*" ことを許可します。

ポッドを AKS クラスターにデプロイするときは、容認とテイントが一致している場合にのみ、ノードでポッドがスケジュールされます。 たとえば、GPU のサポートを備えたノード用のノード プールが AKS クラスターにあると仮定します。 *gpu* などの名前と、スケジュールのための値を定義します。 この値を *NoSchedule* に設定した場合、ポッドで適切な容認が定義されていない場合、そのノードをポッドにスケジュールすることはできません。

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

ノードにテイントを適用したら、そのノードでのスケジュールを許可する容認をポッドの仕様で定義します。 次の例では、前のステップでノードに適用したテイントを許容するための `sku: gpu` と `effect: NoSchedule` を定義しています。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

`kubectl apply -f gpu-toleration.yaml` を使用するなどして、このポッドをデプロイすると、テイントが適用されているノードでそのポッドを正常にスケジュールできます。 この論理的な分離を使用することで、クラスター内のリソースへのアクセスを制御できます。

テイントを適用するときは、アプリケーションの開発者や所有者と協力して、デプロイで必要な容認を定義できるようにします。

テイントと容認について詳しくは、[テイントと容認の適用][k8s-taints-tolerations]に関する記事をご覧ください。

AKS での複数のノード プールの使用方法の詳細については、[AKS でのクラスターの複数のノード プールの作成と管理][use-multiple-node-pools]に関する記事をご覧ください。

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>AKS でのテイントと容認の動作

AKS でノード プールをアップグレードすると、テイントと容認は新しいノードに適用されるときに、次のように設定されたパターンに従います。

- **仮想マシン スケールのサポートがない既定のクラスター**
  - 2 つのノード クラスター *node1* と *node2* があると仮定します。 アップグレードすると、追加ノード (*node3*) が作成されます。
  - *node1* のテイントが *node3* に適用されて、次に *node1* が削除されます。
  - 別の新規ノードが作成されます (前の *node1* が削除されたため、名前は *node1* になります)、そして *node2* のテイントが新しい *node1* に適用されます。 次に、*node2* が削除されます。
  - 要するに、*node1* が *node3* になり、*node2* が *node1* になります。

- **仮想マシン スケール セットを使用するクラスター** (現在、AKS でプレビュー段階)
  - ここでも、2 つのノード クラスター *node1* と *node2* があると仮定します。 ノード プールをアップグレードします。
  - 2 つの追加ノード *node3* と *node4* が作成されて、それぞれにテイントが渡されます。
  - 元の *node1* と *node2* は削除されます。

AKS でノード プールをスケーリングするとき、テイントと容認は設計により持ち越されません。

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>ノード セレクターとアフィニティを使用してポッドのスケジュールを制御する

**ベスト プラクティス ガイダンス** - ノード セレクター、ノードのアフィニティ、ポッド間のアフィニティを使用して、ノードでのポッドのスケジュールを制御します。 これらの設定を使用すると、Kubernetes のスケジューラは、ノードのハードウェアなどによって、ワークロードを論理的に分離できます。

テイントと容認は、ハード カットオフでリソースを論理的に分離するために使用されます。つまり、ポッドがノードのテイントを許容しない場合、そのポッドはそのノードでスケジュールされません。 ノード セレクターを使用するのは別の方法です。 ローカルにアタッチされた SSD ストレージや大容量のメモリといったことを示すラベルをノードに付けた後、ポッドの仕様でノード セレクターを定義します。 その後、そのようなノードは一致するノードにスケジュールされます。 容認とは異なり、一致するノード セレクターのないポッドは、ラベルの付いているノードにスケジュールできます。 この動作により、ノード上の未使用のリソースを使用できますが、一致するノード セレクターが定義されているポッドが優先されます。

大容量メモリを装備したノードの例を見てみましょう。 これらのノードでは、大量のメモリを要求するポッドを優先させることができます。 リソースがアイドル状態で放置されないように、他のポッドの実行も許可します。

```console
kubectl label node aks-nodepool1 hardware:highmem
```

ポッドの仕様に、ノードで設定されているラベルと一致するノード セレクターを定義する `nodeSelector` プロパティを追加します。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
    nodeSelector:
      hardware: highmem
```

これらのスケジューラ オプションを使用するときは、アプリケーションの開発者および所有者と協力して、ポッドの仕様を正しく定義できるようにします。

ノード セレクターの使用について詳しくは、「[Assigning Pods to Nodes][k8s-node-selector]」 (ノードへのポッドの割り当て) をご覧ください。

### <a name="node-affinity"></a>ノード アフィニティ

ノード セレクターは、特定のノードにポッドを割り当てる基本的な方法です。 "*ノード アフィニティ*" を使用すると柔軟性が増します。 ノード アフィニティでは、ポッドがノードと一致しない場合の動作を定義します。 ポッドがラベル付けされたホストと一致することを、"*必須*" として指定できます。 または、一致することが "*望ましい*" けれども、一致するものがない場合は別のホストでのポッドのスケジュールを許可することもできます。

次の例では、ノード アフィニティを *requiredDuringSchedulingIgnoredDuringExecution* に設定しています。 このアフィニティでは、一致するラベルを持つノードを使用することが要求されます。 使用できるノードがない場合、ポッドはスケジュールの継続を待機する必要があります。 別のノードでポッドをスケジュールできるようにするには、代わりに値を *preferredDuringScheduledIgnoreDuringExecution* に設定します。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: hardware
            operator: In
            values: highmem
```

設定の *IgnoredDuringExecution* の部分は、ノードのラベルが変更された場合でも、ポッドをノードから削除してはならないことを示します。 Kubernetes スケジューラでは、新しくスケジュールされるポッドに対してのみ更新されたノード ラベルが使用され、ノードで既にスケジュールされているポッドには使用されません。

詳しくは、「[Affinity and anti-affinity][k8s-affinity]」 (アフィニティと非アフィニティ) をご覧ください。

### <a name="inter-pod-affinity-and-anti-affinity"></a>ポッド間アフィニティと非アフィニティ

Kubernetes スケジューラでワークロードを論理的に分離する最後の方法では、ポッド間アフィニティまたは非アフィニティを使用します。 設定では、一致するポッドが既にあるノードではポッドをスケジュール "*してはならない*" こと、またはスケジュール "*しなければならない*" ことを定義します。 既定では、Kubernetes スケジューラは、ノードをまたぐレプリカ セットに複数のポッドのスケジュールを試みます。 この動作に関しては、さらに具体的なルールを定義できます。

よい例は、Azure Cache for Redis キャッシュも使用している Web アプリケーションです。 ポッドの非アフィニティ ルールを使用すると、複数のノードにレプリカを分散させるよう Kubernetes スケジューラに要求できます。 その後、アフィニティ ルールを使用して、各 Web アプリ コンポーネントが対応するキャッシュと同じホストにスケジュールされるようにすることができます。 複数ノードへのポッドの分散は、次の例のようになります。

| **ノード 1** | **ノード 2** | **ノード 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

この例のデプロイは、ノード セレクターまたはノード アフィニティを使用する場合より複雑です。 デプロイでは、Kubernetes スケジューラによるノードでのポッドのスケジュール方法を制御でき、リソースを論理的に分離できます。 Azure Cache for Redis キャッシュを使用するこの Web アプリケーションの完全な例については、[同じノードへのポッドの併置][k8s-pod-affinity]に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

この記事では、Kubernetes の高度なスケジューラ機能に注目しました。 AKS でのクラスター操作の詳細については、次のベスト プラクティスを参照してください。

* [マルチ テナントとクラスター分離][aks-best-practices-scheduler]
* [Kubernetes スケジューラの基本的な機能][aks-best-practices-scheduler]
* [認証と権限承認][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[use-multiple-node-pools]: use-multiple-node-pools.md
