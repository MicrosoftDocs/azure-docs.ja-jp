---
title: オペレーターのベスト プラクティス - Azure Kubernetes Services (AKS) の基本的なスケジューラ機能
description: Azure Kubernetes Service (AKS) でリソース クォータやポッド中断バジェットなどの基本的なスケジューラ機能を使用するための、クラスター オペレーターのベスト プラクティスについて説明します
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: mlearned
ms.openlocfilehash: 3ce59784b2c7c1d145d99786b10927c230146c8b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614618"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での基本的なスケジューラ機能に関するベスト プラクティス

Azure Kubernetes Service (AKS) でクラスターを管理する際は、多くの場合、チームとワークロードを分離する必要があります。 Kubernetes のスケジューラで提供されている機能を使用すると、コンピューティング リソースの分散を制御したり、メンテナンス イベントの影響を制限したりすることができます。

このベスト プラクティス記事では、クラスター オペレーター向けに Kubernetes の基本的なスケジュール機能について説明します。 この記事では、次のことについて説明します。

> [!div class="checklist"]
> * リソース クォータを使用して、チームやワークロードに固定量のリソースを提供する
> * ポッド中断バジェットを使用して、予定メンテナンスの影響を制限する
> * `kube-advisor` ツールを使用して、ポッド リソースの足りない要求と制限を調べる

## <a name="enforce-resource-quotas"></a>リソース クォータを適用する

**ベスト プラクティス ガイダンス** - 名前空間レベルでリソース クォータを計画して適用します。 ポッドでリソースの要求と制限が定義されていない場合は、デプロイを拒否します。 リソースの使用状況を監視し、必要に応じてクォータを調整します。

リソースの要求と制限は、ポッドの仕様に配置されます。 これらの制限は、クラスターで使用可能なノードを見つけるために、Kubernetes スケジューラによってデプロイ時に使用されます。 これらの制限と要求は、個々のポッド レベルで機能します。 これらの値を定義する方法について詳しくは、「[ポッドのリソースの要求と制限を定義する][resource-limits]」をご覧ください

開発チームまたはプロジェクト全体でリソースを予約および制限する手段を提供するには、"*リソース クォータ*" を使用する必要があります。 これらのクォータは名前空間で定義され、次の基準でクォータを設定するために使用できます。

* **コンピューティング リソース**: CPU とメモリ、GPU など。
* **ストレージ リソース**: ボリュームの総数または特定のストレージ クラスのディスク領域の量が含まれます。
* **オブジェクト数**: シークレット、サービス、ジョブの最大数などを作成できます。

Kubernetes では、リソースはオーバーコミットされません。 リソースの要求または制限の累積合計が、割り当てられたクォータを超えると、それ以上のデプロイは成功しません。

リソース クォータを定義するときは、名前空間内で作成されるすべてのポッドのポッド仕様で、制限または要求を指定する必要があります。 これらの値が指定されていない場合は、デプロイを拒否できます。 代わりに、[名前空間に対して既定の要求と制限を構成する][configure-default-quotas]ことができます。

次に示す *dev-app-team-quotas.yaml* という名前の YAML マニフェストの例では、CPU、メモリ、ポッドの総量のハード制限が、それぞれ *10* 個、*20 Gi*、*10* 個に設定されています。

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

*dev-apps* などの名前空間を指定することで、このリソース クォータを適用できます。

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

アプリケーションの開発者や所有者と協力してニーズを把握し、適切なリソース クォータを適用します。

使用可能なリソース オブジェクト、スコープ、優先順位について詳しくは、[Kubernetes でのリソース クォータ][k8s-resource-quotas]に関する記事をご覧ください。

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>ポッド中断バジェットを使用して可用性を計画する

**ベスト プラクティス ガイダンス** - アプリケーションの可用性を維持するには、ポッド中断バジェット (PDB) を定義して、最低限の数のポッドをクラスターで使用できるようにします。

ポッドが削除される原因になる 2 つの中断イベントがあります。

* "*非自発的な中断*" は、クラスター オペレーターまたはアプリケーション所有者の一般的な制御が及ばないイベントです。
  * このような非自発的な中断としては、物理マシンでのハードウェア障害、カーネル パニック、ノード VM の削除などがあります
* "*自発的な中断*" は、クラスター オペレーターまたはアプリケーション所有者によって要求されるイベントです。
  * このような自発的な中断としては、クラスターのアップグレード、更新されたデプロイ テンプレート、ポッドの誤った削除などがあります。

非自発的な中断は、ポッドの複数のレプリカをデプロイで使用することにより軽減できます。 AKS クラスターで複数のノードを実行することも、これらの非自発的な中断に役立ちます。 自発的な中断については、Kubernetes で提供されている "*ポッド中断バジェット*" を使用することで、クラスター オペレーターは使用可能な最小限の、または最大限のリソース数を定義できます。 これらのポッド中断バジェットにより、自発的な中断イベントが発生したときのデプロイまたはレプリカ セットの応答方法を計画できます。

クラスターをアップグレードする場合、またはデプロイ テンプレートを更新する場合、Kubernetes スケジューラは、自発的中断イベントを続行する前に、他のノードで追加のポッドがスケジュールされていることを確認します。 スケジューラは、クラスター内の他のノードで定義されている数のポッドが正常にスケジュールされるまで、ノードの再起動を待機します。

5 個のポッドで NGINX を実行するレプリカ セットの例を見てみましょう。 レプリカ セット内のポッドには、ラベル `app: nginx-frontend` が割り当てられています。 クラスターのアップグレードのような自発的中断イベントの間に、少なくとも 3 個のポッドが実行し続けるようにしたいと思います。 *PodDisruptionBudget* オブジェクトに対する YAML マニフェストでは、次の要件を定義します。

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
   matchLabels:
      app: nginx-frontend
```

*60%* のような割合を定義することもできます。そうすると、レプリカ セットでポッドの数がスケールアップされるときに自動的に補正できます。

レプリカ セットで利用できないインスタンスの最大数を定義できます。 この場合も、利用できないポッドの割合の最大値を定義できます。 次に示すポッド中断バジェットの YAML マニフェストでは、レプリカ セットで使用できないポッドが 2 個より多くならないように定義されています。

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
   matchLabels:
      app: nginx-frontend
```

ポッド中断バジェットを定義した後は、他の Kubernetes オブジェクトと同じように、AKS クラスター内でそれを作成します。

```console
kubectl apply -f nginx-pdb.yaml
```

アプリケーションの開発者や所有者と協力してニーズを把握し、適切なポッド中断バジェットを適用します。

ポッド中断バジェットの使用について詳しくは、「[Specifying a Disruption Budget for your Application][k8s-pdbs]」 (アプリケーションに対する中断バジェットの指定) をご覧ください。

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>kube-advisor を使用してクラスターの問題を定期的に確認する

**ベスト プラクティス ガイダンス** - 最新バージョンの `kube-advisor` オープン ソース ツールを定期的に実行して、クラスターでの問題を検出します。 既存の AKS クラスターでリソース クォータを適用する場合は、まず、`kube-advisor` を実行し、リソースの要求と制限が定義されていないポッドを見つけます。

[kube-advisor][kube-advisor] ツールでは、Kubernetes クラスターをスキャンし、見つかった問題について報告する、関連する AKS オープンソース プロジェクトです。 確認の際に、リソースの要求と制限が存在しないポッドの特定もできるため便利です。

kube-advisor ツールは、PodSpecs for Windows アプリケーションおよび Linux アプリケーションに欠けているリソース要求と制限を報告することができますが、kube-advisor ツール自体は Linux ポッドでスケジュールする必要があります。 ポッドの構成で[ノード セレクター][k8s-node-selector]を使用して、特定の OS のノード プールで実行するようにポッドをスケジュールすることができます。

複数の開発チームとアプリケーションをホストする AKS クラスターでは、これらのリソースの要求と制限が設定されていないポッドを追跡するのは困難な場合があります。 ベスト プラクティスとしては、AKS クラスターで `kube-advisor` を定期的に実行します (特に、リソース クォータを名前空間に割り当てていない場合)。

## <a name="next-steps"></a>次の手順

この記事では、Kubernetes の基本的なスケジューラ機能に注目しました。 AKS でのクラスター操作の詳細については、次のベスト プラクティスを参照してください。

* [マルチ テナントとクラスター分離][aks-best-practices-cluster-isolation]
* [Kubernetes スケジューラの高度な機能][aks-best-practices-advanced-scheduler]
* [認証と権限承認][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[kube-advisor]: https://github.com/Azure/kube-advisor
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
