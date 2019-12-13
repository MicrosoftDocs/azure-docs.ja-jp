---
title: コンテナー用 Azure Monitor の概要 | Microsoft Docs
description: この記事では、AKS Container Insights ソリューションを監視するコンテナーに対する Azure Monitor と、Azure の AKS クラスターと Container Instances の正常性を監視することによって提供される価値について説明します。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/18/2019
ms.openlocfilehash: 8267f8148269f8b1a0717435e57614f09c229de1
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841406"
---
# <a name="azure-monitor-for-containers-overview"></a>コンテナーに対する Azure Monitor の概要

Azure Monitor for containers は、以下にデプロイされているコンテナー ワークロードのパフォーマンスを監視するために設計された機能です。

- Azure Kubernetes Service (AKS) にホストされているマネージド Kubernetes クラスター
- Azure Container Instances
- Azure Stack またはオンプレミスでホストされた自己管理の Kubernetes クラスター
- Azure Red Hat OpenShift

コンテナーの監視は、複数のアプリケーションを含む大規模な運用クラスターを実行するときは特に重要です。

コンテナーに対する Azure Monitor では、Kubernetes で使用可能なコントローラー、ノード、およびコンテナーから Metrics API 経由でメモリやプロセッサ メトリックを収集することにより、パフォーマンスを把握できます。 コンテナーのログも収集されます。  Kubernetes クラスターから監視を有効化すると、コンテナー化されたバージョンの Linux 向けの Log Analytics エージェントを使用してメトリックとログが自動的に収集されます。 メトリックはメトリック ストアに書き込まれ、ログ データは [Log Analytics](../log-query/log-query-overview.md) ワークスペースに関連付けられたログ ストアに書き込まれます。 

![Azure Monitor for containers のアーキテクチャ](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>コンテナーに対する Azure Monitor の機能

コンテナーに対する Azure Monitor では、お使いの Kubernetes クラスターやコンテナー ワークロードのパフォーマンスと正常性を把握できる Azure Monitor のさまざまな機能を利用した、包括的な監視エクスペリエンスを提供しています。 コンテナーに対する Azure Monitor を利用すると、次のことが可能です。

* ノードで実行されている AKS コンテナーと、そのプロセッサおよびメモリの平均使用率を特定します。 この知識は、リソースのボトルネックを特定するのに役立ちます。
* Azure Container Instances でホストされているコンテナー グループとそのコンテナーのプロセッサおよびメモリ使用率を特定します。  
* コントローラーまたはポッドのどこにコンテナーが存在するかを特定します。 この知識は、コントローラーまたはポッド全体のパフォーマンスを表示するのに役立ちます。 
* ポッドをサポートする標準プロセスと関連のない、ホスト上で実行されているワークロードのリソース使用率を確認します。
* 平均的な負荷、および最大の負荷がかかったときのクラスターの動作を理解します。 この知識は、容量ニーズを特定し、クラスターが維持できる最大負荷を判断するのに役立ちます。 
* ノードまたはコンテナーの CPU およびメモリの使用率がしきい値を超えた場合や、インフラストラクチャまたはノードの正常性ロールアップで正常性状態の変更がクラスターで発生した場合に、事前に通知または記録するようにアラートを構成します。
* [Prometheus](https://prometheus.io/docs/introduction/overview/) と統合して、[クエリ](container-insights-log-search.md)を使用してノードと Kubernetes から収集したアプリケーションとワークロードのメトリックを表示し、カスタム アラート、ダッシュボード、詳細な分析を作成します。

    >[!NOTE]
    >現時点では、Prometheus のサポートはパブリック プレビューの機能です。
    >

* オンプレミスの [AKS エンジンにデプロイされた](https://github.com/microsoft/OMS-docker/tree/aks-engine)コンテナー ワークロードおよび [Azure Stack の AKS エンジン](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)を監視します。
* [Azure Red Hat OpenShift にデプロイされた](../../openshift/intro-openshift.md)コンテナー ワークロードを監視します。

    >[!NOTE]
    >現時点では、Azure Red Hat OpenShift のサポートはパブリック プレビューの機能です。
    >

コンテナーに対する Azure Monitor を使用して AKS クラスターを監視する方法について、中間レベルの詳細情報を紹介した次の動画をご覧ください。

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>この機能にアクセスする方法

コンテナーに対する Azure Monitor には、Azure Monitor からアクセス、または選択した AKS クラスターから直接アクセスという 2 つの方法でアクセスできます。 Azure Monitor からは、監視対象かどうかにかかわらず、デプロイされているすべてのコンテナーを全体的に把握し、サブスクリプションとリソース グループ全体を検索してフィルター処理することができます。また、選択したコンテナーからコンテナーに対する Azure Monitor を詳細に調べることができます。  それ以外にも、選択した AKS コンテナーの AKS ページから機能に直接アクセスできます。  

![コンテナーに対する Azure Monitor にアクセスする方法の概要](./media/container-insights-overview/azmon-containers-experience.png)

構成、監査、リソースの使用率を確認するために、AKS の外部で実行される Docker および Windows コンテナー ホストを監視および管理する必要がある場合は、[コンテナー監視ソリューション](../../azure-monitor/insights/containers.md)を参照してください。

## <a name="next-steps"></a>次の手順

Kubernetes クラスターの監視を開始するには、「[Azure Monitor for containers を有効にする方法](container-insights-onboard.md)」を確認し、監視を有効にするための要件と使用できる方法を把握してください。 
