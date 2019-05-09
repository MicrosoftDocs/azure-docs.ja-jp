---
title: 開発者のベスト プラクティス - Azure Kubernetes Service (AKS) でのリソース管理
description: Azure Kubernetes Service (AKS) でのリソース管理に関するアプリケーション開発者のベスト プラクティスについて説明します
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: zarhoads
ms.openlocfilehash: aebade14f3a8a1095925d17325ce99b78031dc32
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757242"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でリソースを管理するアプリケーション開発者のベスト プラクティス

Azure Kubernetes Service (AKS) でアプリケーションを開発して実行する際に、考慮すべき主な領域がいくつかあります。 アプリケーション デプロイの管理方法は、提供するサービスのエンド ユーザー エクスペリエンスに悪影響を与える場合があります。 適切に行えるように、AKS でアプリケーションを開発して実行する際に従うことができるいくつかのベスト プラクティスに留意してください。

このベスト プラクティスの記事では、アプリケーション開発者の観点からクラスターとワークロードを実行する方法に重点を置いています。 管理のベスト プラクティスについては、[Azure Kubernetes Service (AKS) での分離とリソース管理に関するクラスター オペレーターのベスト プラクティス][operator-best-practices-isolation]に関するページを参照してください。 この記事では、次のことについて説明します。

> [!div class="checklist"]
> * ポッドのリソースの要求と制限とは
> * Dev Spaces と Visual Studio Code でのアプリケーションの開発およびデプロイ方法
> * `kube-advisor` ツールを使用してデプロイに関する問題を確認する方法

## <a name="define-pod-resource-requests-and-limits"></a>ポッドのリソースの要求と制限を定義する

**ベスト プラクティス ガイダンス** - YAML マニフェストですべてのポッドに対して、ポッドの要求と制限を設定します。 AKS クラスターで*リソース クォータ* を使用する場合に、これらの値を定義しないと、デプロイが拒否される可能性があります。

AKS クラスター内のコンピューティング リソースを管理する主な方法は、ポッドの要求と制限を使用することです。 これらの要求と制限を使用することで、Kubernetes スケジューラでは、ポッドを割り当てる必要があるコンピューティング リソースを把握できます。

* **ポッドの要求**では、ポッドで必要な一定量の CPU およびメモリが定義されます。 これらの要求は、許容可能なレベルのパフォーマンスを提供するためにポッドで必要なコンピューティング リソースの量である必要があります。
    * Kubernetes スケジューラでノードでのポッドの配置が試行されるときに、利用できる十分なリソースがあるノードを特定するためにポッドの要求が使用されます。
    * アプリケーションのパフォーマンスを監視し、これらの要求を調整して、許容可能なレベルのパフォーマンスを維持するのに必要とされる量より少ないリソースを定義しないようにしてください。
* **ポッドの制限**は、ポッドで使用できる CPU とメモリの最大量となります。 これらの制限は、1 つまたは 2 つのランナウェイ ポッドで、ノードから過剰な CPU とメモリが使用されないようにするのに役立ちます。 このシナリオでは、ノードと、そこで実行されるその他のポッドのパフォーマンスが低下します。
    * ポッドの制限を、ノードでサポートできる制限より高く設定しないようにします。 各 AKS ノードでは、主要な Kubernetes コンポーネント用に一定量の CPU とメモリが予約されます。 アプリケーションでは、ノードで他のポッドを正常に実行するのに過剰なリソースの使用が試行される場合があります。
    * ここでも、1 日または 1 週間の異なる時間にアプリケーションのパフォーマンスを監視します。 需要のピーク時を判断し、アプリケーションのニーズを満たすために必要なリソースに合わせてポッドの制限を調整します。

ご利用のポッド仕様では、これらの要求と制限を定義することをお勧めします。 これらの値を含めないと、Kubernetes スケジューラでは必要なリソースが認識されません。 スケジューラでは、許容可能なアプリケーション パフォーマンスを提供するために十分なリソースがないノード上にポッドをスケジュールする可能性があります。 クラスター管理者は、リソースの要求と制限を設定する必要がある名前空間に*リソース クォータ* を設定する可能性があります。 詳細については、[AKS クラスターでのリソース クォータ][resource-quotas]に関する文書を参照してください。

CPU の要求または制限を定義する場合、値は CPU 単位で測定されます。 *1.0* CPU は、ノード上の 1 つの基になる仮想 CPU コアに相当します。 これと同じ測定が GPU で使用されます。 また、小数部の要求や制限は、通常、ミリ CPU で定義できます。 たとえば、*100m* の場合、基になる仮想コアは *0.1* となります。

1 つの NGINX ポッドの次の基本的な例では、ポッドで *100m* の CPU 時間と、*128Mi* のメモリが要求されます。 ポッドのリソース制限は、*250m* CPU および *256Mi* メモリに設定されます。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

リソースの測定と割り当ての詳細については、「[Managing compute resources for containers][k8s-resource-limits]」 (コンテナーのコンピューティング リソースの管理) を参照してください。

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>AKS クラスターに対するアプリケーションの開発とデバッグを行う

**ベスト プラクティス ガイダンス** - 開発チームでは、Dev Spaces を使用して AKS クラスターに対するデプロイおよびデバッグを行う必要があります。 この開発モデルを使用することで、アプリが運用環境にデプロイされる前に、ロールベースのアクセス制御、ネットワーク、またはストレージのニーズが確実に実装されるようになります。

Azure Dev Spaces を使用して、AKS クラスターに対して直接アプリケーションを開発、デバッグ、テストします。 チーム内の開発者は連携して、アプリケーション ライフサイクル全体を通してビルドとテストを行います。 Visual Studio または Visual Studio Code などの既存のツールを引き続き使用することができます。 Dev Spaces 用に拡張機能がインストールされ、AKS クラスターでのアプリケーションの実行およびデバッグのオプションが提供されます。

![AKS クラスターで Dev Spaces を使用してアプリケーションをデバッグする](media/developer-best-practices-resource-management/dev-spaces-debug.png)

Dev Spaces を使用するこの統合開発およびテスト プロセスにより、[minikube][minikube] などの、ローカル テスト環境の必要性が軽減されます。 代わりに、AKS クラスターに対する開発およびテストを行います。 クラスターを論理的に分離するための名前空間の使用に関する前のセクションで説明したように、このクラスターをセキュリティで保護し、分離することができます。 アプリを運用環境にデプロイする準備ができたら、開発は実際の AKS クラスターに対してすべて行われているため、自信を持ってデプロイできます。

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Kubernetes 用の Visual Studio Code 拡張機能を使用する

**ベスト プラクティス ガイダンス** - YAML マニフェストを記述する場合、Kubernetes 用の VS Code 拡張機能をインストールして使用します。 統合されたデプロイ ソリューション用の拡張機能を使用することもできます。これは、AKS クラスターをあまり操作しないアプリケーション所有者に役立つ場合があります。

[Kubernetes 用の Visual Studio Code 拡張機能][ vscode-kubernetes]は、アプリケーションの開発と AKS へのデプロイに役立ちます。 拡張機能では、Kubernetes のリソース、および Helm チャートとテンプレートのためのインテリセンスが提供されます。 VS Code 内から Kubernetes リソースを参照、デプロイ、編集することもできます。 また、拡張機能では、ポッド仕様で設定されているリソースの要求や制限についてインテリセンス チェックが提供されます。

![メモリ制限の欠落について警告する Kubernetes 用の VS Code 拡張機能](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>kube-advisor を使用してアプリケーション問題を定期的に確認する

**ベスト プラクティス ガイダンス** - 最新バージョンの `kube-advisor` オープン ソース ツールを定期的に実行して、クラスターでの問題を検出します。 既存の AKS クラスターでリソース クォータを適用する場合は、まず、`kube-advisor` を実行し、リソースの要求と制限が定義されていないポッドを見つけます。

[kube-advisor][kube-advisor] ツールでは、Kubernetes クラスターをスキャンし、見つかった問題について報告する、関連する AKS オープンソース プロジェクトです。 確認の際に、リソースの要求と制限が存在しないポッドの特定もできるため便利です。

多くの開発チームとアプリケーションをホストする AKS クラスターでは、これらのリソースの要求と制限が設定されていないポッドを追跡するのは困難な場合があります。 ベスト プラクティスとして、AKS クラスターで `kube-advisor` を定期的に実行します。

## <a name="next-steps"></a>次の手順

このベスト プラクティスの記事では、クラスター オペレーターの観点からクラスターとワークロードを実行する方法に重点を置きました。 管理のベスト プラクティスについては、[Azure Kubernetes Service (AKS) での分離とリソース管理に関するクラスター オペレーターのベスト プラクティス][operator-best-practices-isolation]に関するページを参照してください。

これらのベスト プラクティスのいくつかを実装する場合は、次の記事を参照してください。

* [Dev Spaces を使用した開発][dev-spaces]
* [kube-advisor を使用した問題の確認][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[dev-spaces]: ../dev-spaces/get-started-netcore.md
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
