---
title: Container Instances とコンテナーのオーケストレーション
description: Azure Container Instances とコンテナー オーケストレーターがどのように対話するかを理解します。
ms.topic: article
ms.date: 04/15/2019
ms.custom: mvc
ms.openlocfilehash: c7faeecfcc3a1d006cc923b48339a1242a0b9e9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148600"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure Container Instances とコンテナー オーケストレーター

コンテナーはサイズが小さく、アプリケーション向きであるため、アジャイル配信環境およびマイクロサービス ベースのアーキテクチャに適しています。 多数のコンテナーを自動化して管理するタスクと、こうしたコンテナーの対話は "*オーケストレーション*" と呼ばれます。 一般的なコンテナー オーケストレーターには、Kubernetes、DC/OS、および Docker Swarm が含まれます。

Azure Container Instances には、オーケストレーション プラットフォームの基本的なスケジュール設定機能のいくつかが用意されています。 また、これらのプラットフォームが提供する高価値サービスは含まれていませんが、Azure Container Instances はこれらを補完できます。 この記事では、Azure Container Instances の処理範囲と、完全なコンテナー オーケストレーターとの対話方法について説明します。

## <a name="traditional-orchestration"></a>従来のオーケストレーション

オーケストレーションの標準の定義には、次のタスクが含まれます。

- **スケジュール設定**:コンテナー イメージとリソース要求を指定して、コンテナーの実行に適したマシンを見つけます。
- **アフィニティ/アンチアフィニティ**:それぞれの間隔が近い一連のコンテナーが実行されるように指定するか (パフォーマンス重視)、それぞれが十分に離れているコンテナーが実行されるように指定します (可用性重視)。
- **正常性の監視**:コンテナー エラーを監視し、自動的にスケジュールを再調整します。
- **フェールオーバー**:各マシン上で実行されている内容を追跡し、コンテナーを障害が発生したマシンから正常なノードに再スケジュールします。
- **拡大縮小**:要求に応じて、手動または自動でコンテナー インスタンスを追加または削除します。
- **ネットワーク**:オーバーレイ ネットワークを提供し、複数のホスト コンピューター間で通信できるようにコンテナーを調整します。
- **サービス検出**:コンテナーが、ホスト マシン間を移動したり IP アドレスを変更したりしても、互いの場所を自動的に見つけることができるようにします。
- **調整されたアプリケーション アップグレード**:アプリケーションのダウンタイムを回避するようにコンテナー アップグレードを管理し、何か問題が発生した場合はロールバックを可能にします。

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Azure Container Instances とのオーケストレーション:レイヤード アプローチ

Azure Container Instances を使用すると、オーケストレーションへのレイヤード アプローチが可能で、1 つのコンテナーの実行に必要なすべてのスケジュールおよび管理機能を提供しながら、オーケストレーター プラットフォームが複数コンテナー タスクを管理できます。

Container Instances の基になるインフラストラクチャは Azure によって管理されるため、オーケストレーター プラットフォームが、1 つのコンテナーを実行するための適切なホスト マシンを自身で見つける必要はありません。 クラウドの柔軟性により、ホスト コンピューターはいつでも確実に使用できます。 代わりに、オーケストレーターは、拡大縮小、アップグレード調整など、複数コンテナー アーキテクチャの開発を簡略化するタスクに集中することができます。

## <a name="scenarios"></a>シナリオ

Azure Container Instances とのオーケストレーターの統合はまだ発展途上にありますが、その環境はこれからいくつか登場すると予測しています。

### <a name="orchestration-of-container-instances-exclusively"></a>Container Instances の専用オーケストレーション

Azure Container Instances 専用の環境は、すばやく起動し、秒単位で請求が行われるため、迅速に開始し、変動が大きなワークロードを処理できます。

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Container Instances と仮想マシン内のコンテナーの組み合わせ

実行時間の長い安定したワークロードの場合は通常、Azure Container Instances で同じコンテナーを実行するより、専用仮想マシンのクラスターでコンテナーを調整する方が安価です。 ただし、Container Instances は、使用中の予期しない、または短時間のスパイクに対処するために全体的な容量をすばやく拡張したり縮小したりするための優れたソリューションを提供します。

クラスター内の仮想マシンの数をスケールアウトしてから、これらのマシンに追加のコンテナーをデプロイするのではなく、オーケストレーターは、単に Azure Container Instances で追加のコンテナーのスケジュールを設定し、必要なくなったらそれらのコンテナーを削除できます。

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>サンプル実装: Azure Kubernetes Service (AKS) 用の仮想ノード

[Azure Kubernetes Service](../aks/intro-kubernetes.md) (AKS) クラスターでアプリケーション ワークロードをすばやくスケーリングするには、Azure Container Instances 内で動的に作成された "*仮想ノード*" を使用できます。 仮想ノードを使用すると、ACI および AKS クラスターで実行されているポッド間でのネットワーク通信が可能になります。 

仮想ノードでは、現在、Linux コンテナー インスタンスがサポートされています。 仮想ノードを使い始めるには、[Azure CLI](../aks/virtual-nodes-cli.md) または [Azure portal](../aks/virtual-nodes-portal.md) を使用します。

仮想ノードでは、オープン ソースの [Virtual Kubelet][aci-connector-k8s] を使用し、容量無制限のノードとして登録することで、Kubernetes の [kubelet][kubelet-doc] を模倣します。 Virtual Kubelet は、[ポッド][pod-doc]の作成を Azure Container Instances 内のコンテナー グループとしてディスパッチします。

Kubernetes API をサーバーレス コンテナー プラットフォーム内に拡張する追加のサンプルについては、[Virtual Kubelet](https://github.com/virtual-kubelet/virtual-kubelet) プロジェクトを参照してください。

## <a name="next-steps"></a>次のステップ

[クイック スタート ガイド](container-instances-quickstart.md)を使用して Azure Container Instances で最初のコンテナーを作成します。

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/azure-aci
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/