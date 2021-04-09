---
title: リソース管理のベストプラクティス
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) でのリソース管理に関するアプリケーション開発者のベスト プラクティスについて説明します
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: 693cabac616dca8e108a2029c173a5e1b71c2695
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516734"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でリソースを管理するアプリケーション開発者のベスト プラクティス

Azure Kubernetes Service (AKS) でアプリケーションを開発して実行する際に、考慮すべき主な領域がいくつかあります。 アプリケーション デプロイの管理方法は、提供するサービスのエンド ユーザー エクスペリエンスに悪影響を与える場合があります。 適切に行えるように、AKS でアプリケーションを開発して実行する際に従うことができるいくつかのベスト プラクティスに留意してください。

このベスト プラクティスの記事では、アプリケーション開発者の観点からクラスターとワークロードを実行する方法に重点を置いています。 管理のベスト プラクティスについては、[Azure Kubernetes Service (AKS) での分離とリソース管理に関するクラスター オペレーターのベスト プラクティス][operator-best-practices-isolation]に関するページを参照してください。 この記事では、次のことについて説明します。

> [!div class="checklist"]
> * ポッドのリソースの要求と制限とは
> * Bridge to Kubernetes と Visual Studio Code を使用したアプリケーションの開発およびデプロイ方法
> * `kube-advisor` ツールを使用してデプロイに関する問題を確認する方法

## <a name="define-pod-resource-requests-and-limits"></a>ポッドのリソースの要求と制限を定義する

**ベスト プラクティス ガイダンス** - YAML マニフェストですべてのポッドに対して、ポッドの要求と制限を設定します。 AKS クラスターで *リソース クォータ* を使用する場合に、これらの値を定義しないと、デプロイが拒否される可能性があります。

AKS クラスター内のコンピューティング リソースを管理する主な方法は、ポッドの要求と制限を使用することです。 これらの要求と制限を使用することで、Kubernetes スケジューラでは、ポッドを割り当てる必要があるコンピューティング リソースを把握できます。

* **ポッドの CPU/メモリ要求** は、ポッドが定期的に必要とする CPU とメモリの量を定義します。
    * Kubernetes スケジューラがノードにポッドを配置しようとすると、ポッド要求を使用して、スケジュール設定に使用できるリソースが十分にあるノードが特定されます。
    * ポッド要求を設定しないと、既定で、定義済みの制限に設定されます。
    * アプリケーションのパフォーマンスを監視して、これらの要求を調整することが非常に重要です。 不十分なポッドのリソースの要求が行われると、ノードの過剰なスケジュール設定が原因でアプリケーションのパフォーマンスが低下する可能性があります。 要求を過大に見積もると、アプリケーションのスケジュール設定がより困難になる可能性があります。
* **ポッドの CPU/メモリの制限** は、ポッドが使用できる CPU とメモリの最大量です。 メモリの制限は、リソース不足によりノードが不安定になった場合にどのポッドを強制終了すべきかを定義するのに役立ちます。 適切な制限が設定されていないと、リソース不足が解消されるまでポッドの強制終了が行われます。 ポッドは、一定の期間 CPU の制限を超えることができる場合とできない場合がありますが、CPU の制限を超えてもポッドは強制終了されません。 
    * ポッド制限は、ポッドがいつリソース消費の制御を失うかを定義するのに役立ちます。 制限を超えると、ノードの正常性を維持し、ノードを共有するポッドへの影響を最小限に抑えるために、強制終了に対してポッドの優先順位が付けられます。
    * ポッド制限を設定しないと、既定で、特定のノードで使用可能な最大値に設定されます。
    * ポッドの制限を、ノードでサポートできる制限より高く設定しないようにします。 各 AKS ノードでは、主要な Kubernetes コンポーネント用に一定量の CPU とメモリが予約されます。 アプリケーションでは、ノードで他のポッドを正常に実行するのに過剰なリソースの使用が試行される場合があります。
    * この場合も、1 日または 1 週間の異なるタイミングでアプリケーションのパフォーマンスを監視することが非常に重要になります。 需要のピークがいつ発生するかを判断し、アプリケーションの最大ニーズを満たすために必要なリソースにポッドの制限を合わせます。

ポッドの指定では、上記の情報に基づいてこれらの要求と制限を定義することが **ベストプラクティスであり、非常に重要** です。 これらの値を含めないと、Kubernetes スケジューラは、スケジュール設定の決定を支援するためにアプリケーションが必要とするリソースを考慮に入れることができません。

スケジューラによって、リソースが不足しているノードにポッドが配置されると、アプリケーションのパフォーマンスが低下します。 クラスター管理者は、リソースの要求と制限を設定する必要がある名前空間に "*リソース クォータ*" を設定することを強くお勧めします。 詳細については、[AKS クラスターでのリソース クォータ][resource-quotas]に関する記事を参照してください。

CPU の要求または制限を定義する場合、値は CPU 単位で測定されます。 
* *1.0* CPU は、ノード上の 1 つの基になる仮想 CPU コアに相当します。 
* これと同じ測定が GPU で使用されます。
* 端数は、ミリコア単位で測定できます。 たとえば、*100m* は、基になる vCPU コアの *0.1* です。

1 つの NGINX ポッドの次の基本的な例では、ポッドで *100m* の CPU 時間と、*128Mi* のメモリが要求されます。 ポッドのリソース制限は、*250m* CPU および *256Mi* メモリに設定されます。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

リソースの測定と割り当ての詳細については、「[コンテナーのコンピューティング リソースの管理][k8s-resource-limits]」を参照してください。

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>AKS クラスターに対するアプリケーションの開発とデバッグを行う

**ベスト プラクティス ガイダンス** - 開発チームでは、Bridge to Kubernetes を使用して AKS クラスターに対するデプロイおよびデバッグを行う必要があります。

Bridge to Kubernetes を使用すると、AKS クラスターに対して直接アプリケーションを開発、デバッグ、テストできます。 チーム内の開発者は連携して、アプリケーション ライフサイクル全体を通してビルドとテストを行います。 Visual Studio または Visual Studio Code などの既存のツールを引き続き使用することができます。 Bridge to Kubernetes 用の拡張機能がインストールされているため、AKS クラスターで直接開発できます。

Bridge to Kubernetes を使用するこの統合された開発とテストのプロセスにより、[minikube][minikube] などのローカル テスト環境の必要性が低くなります。 代わりに、AKS クラスターに対する開発およびテストを行います。 クラスターを論理的に分離するための名前空間の使用に関する前のセクションで説明したように、このクラスターをセキュリティで保護し、分離することができます。

Bridge to Kubernetes は、Linux ポッドおよびノード上で実行されるアプリケーションで使用することを目的としています。

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Kubernetes 用の Visual Studio Code 拡張機能を使用する

**ベスト プラクティス ガイダンス** - YAML マニフェストを記述する場合、Kubernetes 用の VS Code 拡張機能をインストールして使用します。 統合されたデプロイ ソリューション用の拡張機能を使用することもできます。これは、AKS クラスターをあまり操作しないアプリケーション所有者に役立つ場合があります。

[Kubernetes 用の Visual Studio Code 拡張機能][vscode-kubernetes]は、アプリケーションの開発と AKS へのデプロイに役立ちます。 拡張機能では、Kubernetes のリソース、および Helm チャートとテンプレートのためのインテリセンスが提供されます。 VS Code 内から Kubernetes リソースを参照、デプロイ、編集することもできます。 また、拡張機能では、ポッド仕様で設定されているリソースの要求や制限についてインテリセンス チェックが提供されます。

![メモリ制限の欠落について警告する Kubernetes 用の VS Code 拡張機能](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>kube-advisor を使用してアプリケーション問題を定期的に確認する

**ベスト プラクティス ガイダンス** - 最新バージョンの `kube-advisor` オープン ソース ツールを定期的に実行して、クラスターでの問題を検出します。 既存の AKS クラスターでリソース クォータを適用する場合は、まず、`kube-advisor` を実行し、リソースの要求と制限が定義されていないポッドを見つけます。

[kube-advisor][kube-advisor] ツールでは、Kubernetes クラスターをスキャンし、見つかった問題について報告する、関連する AKS オープンソース プロジェクトです。 確認の際に、リソースの要求と制限が存在しないポッドの特定もできるため便利です。

kube-advisor ツールは、PodSpecs for Windows アプリケーションおよび Linux アプリケーションに欠けているリソース要求と制限を報告することができますが、kube-advisor ツール自体は Linux ポッドでスケジュールする必要があります。 ポッドの構成で[ノード セレクター][k8s-node-selector]を使用して、特定の OS のノード プールで実行するようにポッドをスケジュールすることができます。

多くの開発チームとアプリケーションをホストする AKS クラスターでは、これらのリソースの要求と制限が設定されていないポッドを追跡するのは困難な場合があります。 ベスト プラクティスとして、AKS クラスターで `kube-advisor` を定期的に実行します。

## <a name="next-steps"></a>次のステップ

このベスト プラクティスの記事では、クラスター オペレーターの観点からクラスターとワークロードを実行する方法に重点を置きました。 管理のベスト プラクティスについては、[Azure Kubernetes Service (AKS) での分離とリソース管理に関するクラスター オペレーターのベスト プラクティス][operator-best-practices-isolation]に関するページを参照してください。

これらのベスト プラクティスのいくつかを実装する場合は、次の記事を参照してください。

* [Bridge to Kubernetes を使用した開発][btk]
* [kube-advisor を使用した問題の確認][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[btk]: /visualstudio/containers/overview-bridge-to-kubernetes
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
