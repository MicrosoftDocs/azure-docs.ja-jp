---
title: オペレーターのベスト プラクティス - Azure Kubernetes Services (AKS) でのクラスターの分離
description: Azure Kubernetes Service (AKS) での分離に関するクラスター オペレーターのベスト プラクティスについて説明します
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: mlearned
ms.openlocfilehash: 8150e184f0c7533d5a6e7e4847bf126206f5e6c6
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614931"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Services (AKS) でのクラスターの分離に関するベスト プラクティス

Azure Kubernetes Service (AKS) でクラスターを管理する際は、多くの場合、チームとワークロードを分離する必要があります。 AKS では、柔軟にマルチ テナント クラスターを実行してリソースを分離することができます。 Kubernetes への投資を最大限に活用するには、これらのマルチ テナントおよび分離機能を理解して実装する必要があります。

このベスト プラクティスの記事では、クラスター オペレーターを対象とした分離に重点を置いています。 この記事では、次のことについて説明します。

> [!div class="checklist"]
> * マルチ テナント クラスターとリソースの分離の計画
> * AKS クラスターでの論理的または物理的な分離の使用

## <a name="design-clusters-for-multi-tenancy"></a>マルチ テナント用にクラスターを設計する

Kubernetes では、同じクラスター内のチームとワークロードを論理的に分離できる機能が提供されます。 各チームで必要なリソースをスコープとする、最小限の数の特権を提供することを目標にする必要があります。 Kubernetes の[名前空間][k8s-namespaces]では、論理的な分離境界が作成されます。 その他の Kubernetes の機能および分離とマルチテナントに関する考慮事項には、次の領域が含まれます。

* **スケジューリング**には、リソース クォータやポッドの中断バジェットなどの基本的な機能の使用が含まれます。 これらの機能の詳細については、[AKS の基本的なスケジューラ機能のベスト プラクティス][aks-best-practices-scheduler]に関するページを参照してください。
  * スケジューラのより高度な機能には、テイントと容認、ノード セレクター、およびノードとポッドのアフィニティまたは非アフィニティが含まれます。 これらの機能の詳細については、[AKS の高度なスケジューラ機能のベスト プラクティス][aks-best-practices-advanced-scheduler]に関するページを参照してください。
* **ネットワーク**には、ポッド内外のトラフィックのフローを制御するためのネットワーク ポリシーの使用が含まれます。
* **認証と承認**には、ロールベースのアクセス制御 (RBAC) と Azure Active Directory (AD) の統合、ポッド ID、および Azure Key Vault のシークレットの使用が含まれます。 これらの機能の詳細については、[AKS の認証と承認のベスト プラクティス][aks-best-practices-identity]に関するページを参照してください。
* **コンテナー**には、ポッドのセキュリティ ポリシー、ポッドのセキュリティ コンテキスト、脆弱性に関するイメージとランタイムのスキャンが含まれます。 また、基になるノードへのコンテナー アクセスを制限するための App Armor や Seccomp (セキュア コンピューティング) の使用も含まれます。

## <a name="logically-isolate-clusters"></a>クラスターを論理的に分離する

**ベスト プラクティス ガイダンス** - 論理的な分離を使用して、チームとプロジェクトを分離します。 チームまたはアプリケーションを分離するためにデプロイする物理 AKS クラスターの数を最小限にしてみます。

論理的な分離では、1 つの AKS クラスターを、複数のワークロード、チーム、環境で使用できます。 Kubernetes の[名前空間][k8s-namespaces]では、ワークロードとリソースの論理的な分離境界を形成します。

![AKS での Kubernetes クラスターの論理的な分離](media/operator-best-practices-cluster-isolation/logical-isolation.png)

クラスターの論理的な分離では、通常、物理的に分離されたクラスターよりも高いポッド密度が提供されます。 クラスターでアイドル状態の過剰なコンピューティング容量が少なくなります。 Kubernetes クラスターの自動スケーラーを組み合わせることで、ノード数をスケール アップまたはスケール ダウンして需要を満たすことできます。 自動スケールのためのこのベスト プラクティスの方法を使用すれば、必要な数のノードのみを実行でき、コストが最小限に抑えられます。

AKS などでは、Kubernetes 環境は、悪意のあるマルチテナント使用に対しては完全に安全ではありません。 ノードに対して *Pod Security Policy* やより高度なロール ベースのアクセス制御 (RBAC) などの追加のセキュリティ機能を使用すると、セキュリティ上の弱点を悪用されにくくなります。 ただし、悪意のあるマルチテナント ワークロードの実行に対して真のセキュリティを実現するために信頼できる唯一のセキュリティ レベルはハイパーバイザーです。 Kubernetes 用のセキュリティ ドメインは、個々のノードではなく、クラスター全体になります。 この種の悪意のあるマルチテナント ワークロードでは、物理的に分離されたクラスターを使用する必要があります。

## <a name="physically-isolate-clusters"></a>クラスターを物理的に分離する

**ベスト プラクティス ガイダンス** - 個々のチームまたはアプリケーションのデプロイごとの物理的な分離の使用を最小限に抑えます。 代わりに、前のセクションで説明したように、*論理的な* 分離を使用します。

クラスターを分離するための一般的な方法は、個々の AKS クラスターを物理的に使用することです。 この分離モデルでは、チームまたはワークロードに独自の AKS クラスターが割り当てられます。 この方法は、多くの場合、ワークロードやチームを分離する最も簡単な方法のようですが、管理および財務のオーバーヘッドがさらに増えます。 ここでこれらの複数のクラスターを維持し、個別にアクセスを提供してアクセス許可を割り当てる必要があります。 また、個々のすべてのノードについて課金されます。

![AKS での個々の Kubernetes クラスターの物理的な分離](media/operator-best-practices-cluster-isolation/physical-isolation.png)

物理的に分離されたクラスターのポッドの密度は通常、低くなります。 各チームまたはワークロードには独自の AKS クラスターがあるため、クラスターは多くの場合、コンピューティング リソースで過剰にプロビジョニングされます。 多くの場合、ノード上には少数のポッドがスケジュールされます。 ノード上の未使用の容量は、他のチームによって開発中のアプリケーションやサービスで使用することはできません。 これらの過剰なリソースは、物理的に分離されたクラスターでの追加コストの原因となります。

## <a name="next-steps"></a>次の手順

この記事ではクラスターの分離に重点を置きました。 AKS でのクラスター操作の詳細については、次のベスト プラクティスを参照してください。

* [Kubernetes スケジューラの基本的な機能][aks-best-practices-scheduler]
* [Kubernetes スケジューラの高度な機能][aks-best-practices-advanced-scheduler]
* [認証と権限承認][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
