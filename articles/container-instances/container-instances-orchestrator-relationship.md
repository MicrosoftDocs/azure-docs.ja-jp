---
title: "Azure Container Instances とコンテナーのオーケストレーション"
description: "Azure Container Instances とコンテナー オーケストレーターがどのように対話するかを理解します"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: a678700884b612cad6281eb8f3b74ce63a0ebb69
ms.openlocfilehash: 6f0a1e5263a034e825d4c12860fd6f97a4c556c6
ms.contentlocale: ja-jp
ms.lasthandoff: 07/26/2017

---

# <a name="azure-container-instances-and-container-orchestrators"></a>Azure Container Instances とコンテナー オーケストレーター

コンテナーはサイズが小さく、アプリケーション向きであるため、アジャイル配信環境およびマイクロサービス ベースのアーキテクチャに適しています。 多数のコンテナーを自動化して管理するタスクと、こうしたコンテナーの対話は "*オーケストレーション*" と呼ばれます。 Kubernetes、DC/OS、Docker Swarm などが、よく利用されているコンテナー オーケストレーターで、これはすべて [Azure Container Service](https://docs.microsoft.com/azure/container-service/) から入手できます。

Azure Container Instances には、オーケストレーション プラットフォームの基本的なスケジュール設定機能がいくつか用意されていますが、こうしたプラットフォームが提供する高価値サービスは含まれておらず、実際は、こうしたサービスを補完することができます。 この記事では、Azure Container Instances の処理範囲と、完全なコンテナー オーケストレーターとの対話について説明します。

## <a name="traditional-orchestration"></a>従来のオーケストレーション

オーケストレーションの標準の定義には、次のタスクが含まれます。

- **スケジュール設定**: コンテナー イメージとリソース要求を指定して、コンテナーの実行に適したマシンを見つけます。
- **アフィニティ/アンチアフィニティ**: それぞれの間隔が近い一連のコンテナーが実行されるように指定するか (パフォーマンス重視)、それぞれが十分に離れているコンテナーが実行されるように指定します (可用性重視)。
- **正常性の監視**: コンテナー エラーを監視し、自動的にスケジュールを再調整します。
- **フェールオーバー**: 各コンピューターで何が実行されているかを追跡し、障害が発生したマシンから正常なノードへのコンテナーのスケジュールを再調整します。
- **拡大縮小**: 要求に応じて、手動または自動でコンテナー インスタンスを追加または削除します。
- **ネットワーク**: オーバーレイ ネットワークを提供し、複数のホスト コンピューター間で通信できるようにコンテナーを調整します。
- **サービス検出**: 複数のコンテナーが、ホスト コンピューター間を移動中でも相互に特定し、IP アドレスを変更できるようにします。
- **アプリケーション アップグレード調整**: アプリケーションのダウンタイムが発生しないようにコンテナー アップグレードを管理し、問題が生じた場合にロールバックできるようにします。

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Azure Container Instances とのオーケストレーション: レイヤード アプローチ

Azure Container Instances を使用すると、オーケストレーションへのレイヤード アプローチが可能で、1 つのコンテナーの実行に必要なすべてのスケジュールおよび管理機能を提供しながら、オーケストレーター プラットフォームが複数コンテナー タスクを管理できます。

Container Instances の基になるインフラストラクチャはすべて Azure で管理されるため、オーケストレーター プラットフォームが、1 つのコンテナーの実行に適したホスト コンピューターを自身で見つけることを心配する必要はありません。 クラウドの柔軟性により、ホスト コンピューターはいつでも確実に使用できます。 代わりに、オーケストレーターは、拡大縮小、アップグレード調整など、複数コンテナー アーキテクチャの開発を簡略化するタスクに集中することができます。



## <a name="potential-scenarios"></a>潜在的なシナリオ

Azure Container Instances とのオーケストレーターの統合はまだ発展途上にありますが、その環境はこれからいくつか登場すると予測しています。

### <a name="orchestration-of-container-instances-exclusively"></a>Container Instances の専用オーケストレーション

Azure Container Instances 専用の環境は、すばやく起動し、秒単位で請求が行われるため、迅速に開始し、変動が大きなワークロードを処理できます。

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Container Instances と仮想マシンのコンテナーの組み合わせ

ワークロードの実行時間が長く、安定している場合は、専用仮想マシンのクラスターでコンテナーを調整した方が、通常は、Container Instances で同じコンテナーを実行するよりもコストがかかりません。 ただし、Container Instances が提供する優れたソリューションでは、全体的な容量をすばやく拡張および縮小し、使用中に発生する予期しない、または短時間のスパイクに対応できます。 クラスター内の仮想マシン数をスケールアウトして、追加コンテナーをこうしたマシンにデプロイするのではなく、オーケストレーターは単純に Container Instances を使用して、追加コンテナーのスケジュールを設定し、不要になったときに削除できます。

## <a name="sample-implementation-azure-container-instances-connector-for-kubernetes"></a>サンプル実装: Kubernetes 用の Azure Container Instances コネクタ

コンテナー オーケストレーション プラットフォームを Azure Container Instances に統合する方法を示すために、[Kubernetes 用サンプル コネクタ][aci-connector-k8s]の構築を開始しました。 

Kubernetes 用コネクタは、無制限の容量を持つノードとして登録し、Azure Container Instances のコンテナー グループとして[ポッド][pod-doc]の作成をディスパッチすることで、[kubelet][kubelet-doc] に似せています。 

<!-- ![ACI Connector for Kubernetes][aci-connector-k8s-gif] -->

その他のオーケストレーター用コネクタを構築するときも、同じようにプラットフォーム プリミティブに統合され、これによりオーケストレーター API の能力と、Azure Container Instances でのコンテナー管理の速度とシンプルさが結合されます。

> [!WARNING]
> Kubernetes ACI コネクタは "*試験段階*" です。運用環境では使用しないでください。

## <a name="next-steps"></a>次のステップ

[クイック スタート ガイド](container-instances-quickstart.md)を使用して Azure Container Instances で最初のコンテナーを作成します。

<!-- IMAGES -->
[aci-connector-k8s-gif]: ./media/container-instances-orchestrator-relationship/aci-connector-k8s.gif

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/azure/aci-connector-k8s
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
