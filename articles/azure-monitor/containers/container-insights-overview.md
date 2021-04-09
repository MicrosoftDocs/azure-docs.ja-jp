---
title: Container insights の概要 | Microsoft Docs
description: この記事では、AKS Container insights ソリューションを監視する Container insights と、Azure の AKS クラスターとコンテナー インスタンスの正常性を監視することによって提供される価値について説明します。
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: a29890f569c47c48aae7d5d61badee7edeef58a0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102441518"
---
# <a name="container-insights-overview"></a>Container insights の概要

Container insights は、以下にデプロイされているコンテナー ワークロードのパフォーマンスを監視するために設計された機能です。

- [Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md) にホストされているマネージド Kubernetes クラスター
- [AKS エンジン](https://github.com/Azure/aks-engine)を使用して、Azure でホストされた自己管理の Kubernetes クラスター
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)またはオンプレミスでホストされた自己管理の Kubernetes クラスター
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)
- [Azure Arc 対応 Kubernetes](../../azure-arc/kubernetes/overview.md) (プレビュー)

Container insights では、Linux および Windows Server 2019 オペレーティング システムが実行されているクラスターをサポートします。 サポートされているコンテナー ランタイムには、Docker、Moby、および CRI 互換性のあるランタイム (CRI-O、ContainerD など) があります。

コンテナーの監視は、複数のアプリケーションを含む大規模な運用クラスターを実行するときは特に重要です。

Container insights により、Kubernetes で使用可能なコントローラー、ノード、およびコンテナーから Metrics API 経由でメモリやプロセッサのメトリックが収集されることで、パフォーマンスを可視化します。 コンテナーのログも収集されます。  Kubernetes クラスターから監視を有効化すると、コンテナー化されたバージョンの Linux 向けの Log Analytics エージェントを使用してメトリックとログが自動的に収集されます。 メトリックはメトリック ストアに書き込まれ、ログ データは [Log Analytics](../logs/log-query-overview.md) ワークスペースに関連付けられたログ ストアに書き込まれます。

![Container insights のアーキテクチャ](./media/container-insights-overview/azmon-containers-architecture-01.png)

## <a name="what-does-container-insights-provide"></a>Container insights の機能

Container insights では、Azure Monitor のさまざまな機能を使用して、包括的なモニタリング エクスペリエンスを提供します。 これらの機能を使用すると、Linux と Windows Server 2019 オペレーティングシステムを実行している Kubernetes クラスターのパフォーマンスと正常性、およびコンテナーのワークロードを把握できます。 Container insights を使用すると、次のことを行うことができます。

* ノードで実行されている AKS コンテナーと、そのプロセッサおよびメモリの平均使用率を特定します。 この知識は、リソースのボトルネックを特定するのに役立ちます。
* Azure Container Instances でホストされているコンテナー グループとそのコンテナーのプロセッサおよびメモリ使用率を特定します。
* コントローラーまたはポッドのどこにコンテナーが存在するかを特定します。 この知識は、コントローラーまたはポッド全体のパフォーマンスを表示するのに役立ちます。
* ポッドをサポートする標準プロセスと関連のない、ホスト上で実行されているワークロードのリソース使用率を確認します。
* 平均的な負荷、および最大の負荷がかかったときのクラスターの動作を理解します。 この知識は、容量ニーズを特定し、クラスターが維持できる最大負荷を判断するのに役立ちます。
* ノードまたはコンテナーの CPU およびメモリの使用率がしきい値を超えた場合や、インフラストラクチャまたはノードの正常性ロールアップで正常性状態の変更がクラスターで発生した場合に、事前に通知または記録するようにアラートを構成します。
* [Prometheus](https://prometheus.io/docs/introduction/overview/) と統合して、[クエリ](container-insights-log-search.md)を使用してノードと Kubernetes から収集したアプリケーションとワークロードのメトリックを表示し、カスタム アラート、ダッシュボード、詳細な分析を作成します。
* オンプレミスの [AKS エンジンにデプロイされた](https://github.com/Azure/aks-engine)コンテナー ワークロードおよび [Azure Stack の AKS エンジン](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)を監視します。
* [Azure Red Hat OpenShift にデプロイされた](../../openshift/intro-openshift.md)コンテナー ワークロードを監視します。

    >[!NOTE]
    >現時点では、Azure Red Hat OpenShift のサポートはパブリック プレビューの機能です。
    >

* [Azure Arc 対応 Kubernetes (プレビュー) にデプロイされた](../../azure-arc/kubernetes/overview.md)コンテナー ワークロードを監視します。

Windows Server クラスターを監視する場合の、Linux クラスターとの主な相違点は以下のとおりです。

- Windows にはメモリ RSS メトリックがないため、Windows ノードとコンテナーでこれを使用することはできません。 [ワーキング セット](/windows/win32/memory/working-set) メトリックは使用できます。
- Windows ノードではディスク ストレージ容量の情報を使用できません。
- ポッド環境のみが監視され、Docker 環境は対象外です。
- プレビュー リリースでは、最大 30 の Windows Server コンテナーがサポートされます。 この制限は、Linux コンテナーには適用されません。

Container insights を使用して AKS クラスターを監視する方法については、中間レベルの詳細情報を紹介している次の動画をご覧ください。

> [!VIDEO https://www.youtube.com/embed/XEdwGvS2AwA]

## <a name="how-do-i-access-this-feature"></a>この機能にアクセスする方法

Container insights には、Azure Monitor からのアクセス、または選択した AKS クラスターからの直接アクセスという 2 つの方法でアクセスできます。 Azure Monitor からは、監視対象かどうかにかかわらず、デプロイされているすべてのコンテナーを全体的に把握し、サブスクリプションとリソース グループ全体を検索してフィルター処理することができます。また、選択したコンテナーから Container insights を詳細に調べることができます。  それ以外にも、選択した AKS コンテナーの AKS ページから機能に直接アクセスできます。

![Container insights にアクセスするためのメソッドの概要](./media/container-insights-overview/azmon-containers-experience.png)

構成、監査、リソースの使用率を確認するために、AKS の外部で実行される Docker および Windows コンテナー ホストを監視および管理する必要がある場合は、[コンテナー監視ソリューション](./containers.md)を参照してください。

## <a name="next-steps"></a>次のステップ

Kubernetes クラスターの監視を開始するには、[Container insights を有効にする方法](container-insights-onboard.md)に関するページを確認し、監視を有効にするための要件と使用できる方法を把握してください。
