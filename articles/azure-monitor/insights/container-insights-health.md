---
title: Azure Monitor for containers を使用して Kubernetes クラスターの正常性を監視する | Microsoft Docs
description: この記事では、Azure Monitor for containers を使用して、AKS クラスターと非 AKS クラスターの正常性を表示および分析する方法について説明します。
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 94fdb6388134eae326ed5a8e46fbd3d13f8da2d5
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649576"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>Azure Monitor for containers を使用して Kubernetes クラスターの正常性を把握する

Azure Monitor for containers を使用すると、マネージド インフラストラクチャ コンポーネントと、Azure Monitor for containers でサポートされている任意の Kubernetes クラスターで実行されているすべてのノードの正常性状態が監視されて報告されます。 このエクスペリエンスは、[マルチクラスター ビュー](container-insights-analyze.md#multi-cluster-view-from-azure-monitor)で計算および報告されたクラスターの正常性状態にはとどまりません。クラスター内の 1 つ以上のノードがリソースの制約を受けているかどうか、またはノードまたはポッドが使用できないことがクラスター内の実行中のアプリケーションに影響を与える可能性があるかどうかを、キュレートされたメトリックに基づいて把握することができるようになりました。

>[!NOTE]
>正常性機能は、2020 年 6 月末にプライベート プレビューに移行する予定です。 詳細については、次の [Azure の更新に関するお知らせ](https://azure.microsoft.com/updates/ci-health-limited-preview/)に関するページを参照してください。
>

Azure Monitor for containers を有効にする方法については、[Azure Monitor for containers のオンボード](container-insights-onboard.md)に関する記事を参照してください。

>[!NOTE]
>AKS エンジン クラスターをサポートするには、次の条件が満たされていることを確認してください。
>- 最新バージョンの [HELM クライアント](https://helm.sh/docs/using_helm/)を使用している。
>- コンテナー化されたエージェントのバージョンが *microsoft/oms:ciprod11012019* である。 エージェントをアップグレードするには、[Kubernetes クラスターでのエージェントのアップグレード](container-insights-manage-agent.md#how-to-upgrade-the-azure-monitor-for-containers-agent)に関するページを参照してください。
>

## <a name="overview"></a>概要

コンテナーに対する Azure Monitor では、正常性 (プレビュー) 機能によって、問題の識別や診断に役立つ Kubernetes クラスターのプロアクティブな正常性の監視が提供されます。 これにより、検出された重要な問題を表示できます。 クラスターの正常性を評価するモニターはコンテナー化されたエージェントで実行され、正常性データは Log Analytics ワークスペース内の **KubeHealth** テーブルに書き込まれます。 

Kubernetes クラスターの正常性は、次の Kubernetes オブジェクトと抽象化によって編成されたさまざまな監視シナリオに基づいています。

- Kubernetes インフラストラクチャ - CPU とメモリの使用率、ポッドの可用性を評価することで、クラスターにデプロイされているノードで実行されている Kubernetes API サーバー、ReplicaSet、および DaemonSet のロールアップを提供します。

    ![Kubernetes インフラストラクチャの正常性ロールアップ ビュー](./media/container-insights-health/health-view-kube-infra-01.png)

- ノード - CPU とメモリの使用率と、Kubernetes によって報告されたノードの状態を評価することによって、ノード プールと各プール内の個々のノードの状態のロールアップを提供します。

    ![ノードの正常性ロールアップ ビュー](./media/container-insights-health/health-view-nodes-01.png)

現時点では、仮想 kubelet の状態のみがサポートされています。 仮想 kublet ノードの CPU およびメモリからシグナルが受信されないため、これらの使用率の正常性状態は**不明**として報告されます。

すべてのモニターは、[Health Hierarchy]\(正常性の階層\) ウィンドウに階層構造で表示されます。ここでは、Kubernetes オブジェクトまたは抽象化 (つまり、Kubernetes インフラストラクチャまたはノード) を表す集合モニターが、依存関係にあるすべての子モニターの組み合わされた正常性を反映する最上位のモニターです。 正常性を得るために使用される主要な監視シナリオは次のとおりです。

* ノードとコンテナーから CPU 使用率を評価する。
* ノードとコンテナーからメモリ使用率を評価する。
* Kubernetes によって報告されたポッドとノードの準備完了状態の計算に基づいたポッドとノードの状態。

状態を示すために使用されるアイコンは次のとおりです。

|アイコン|意味|  
|--------|-----------|  
|![正常であることを示す緑色のチェック マーク アイコン](./media/container-insights-health/healthyicon.png)|成功、正常性は問題なし (緑)|  
|![黄色の三角形と感嘆符は警告](./media/container-insights-health/warningicon.png)|警告 (黄)|  
|![クリティカルな状態を示す白い X 印が付いた赤いボタン](./media/container-insights-health/criticalicon.png)|クリティカル (赤)|  
|![淡色表示されているアイコン](./media/container-insights-health/grayicon.png)|不明 (グレー)|  

## <a name="monitor-configuration"></a>モニターの構成

Azure Monitor for containers の正常性機能をサポートする各モニターの動作と構成を理解するには、[正常性モニターの構成ガイド](container-insights-health-monitors-config.md)に関するページを参照してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com) にサインインします。 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>AKS または非 AKS クラスターの正常性を表示する

コンテナーに対する Azure Monitor の正常性 (プレビュー) 機能には、Azure portal の左側のウィンドウから **[洞察]** を選択することによって AKS クラスターから直接アクセスできます。 **[分析情報]** セクションで **[コンテナー]** を選択します。 

オンプレミスまたは Azure Stack でホストされている AKS Engine クラスターである非 AKS クラスターの正常性を表示するには、Azure portal の左側のウィンドウで **[Azure Monitor]** を選択します。 **[分析情報]** セクションで **[コンテナー]** を選択します。  マルチクラスター ページで、一覧から非 AKS クラスターを選択します。

Azure Monitor for containers の **[クラスター]** ページで、 **[正常性]** を選択します。

![クラスターの正常性ダッシュボードの例](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>クラスターの正常性を確認する

[正常性] ページが開くと、 **[正常性の側面]** グリッドに **[Kubernetes Infrastructure]** が既定で選択されています。  グリッドには、Kubernetes インフラストラクチャとクラスター ノードの現在の正常性ロールアップの状態が要約されます。 いずれかの正常性の側面を選択すると、[Health Hierarchy]\(正常性の階層\) ウィンドウ (中央のウィンドウ) の結果が更新され、すべての子モニターが階層構造で表示され、現在の正常性状態が表示されます。 任意の依存関係モニターの詳細情報を表示するには、そのモニターを選択することで、ページの右側にプロパティ ウィンドウが自動的に表示されます。 

![クラスターの正常性プロパティ ウィンドウ](./media/container-insights-health/health-view-property-pane.png)

プロパティ ウィンドウには、次の情報が表示されます。

- **[概要]** タブには、選択したモニターの現在の状態、モニターが最後に計算された日時、および状態の変更が発生した最後の日時が表示されます。 階層内の選択されたモニターの種類に応じて、追加情報が表示されます。

    [Health Hierarchy]\(正常性の階層\) ウィンドウで集合モニターを選択すると、プロパティ ウィンドウの **[概要]** タブの下に、階層内の子モニターの合計数のロールアップと、重大、警告、および正常の状態にある集合モニターの数が表示されます。 

    ![集合モニターの正常性プロパティ ウィンドウの [概要] タブ](./media/container-insights-health/health-overview-aggregate-monitor.png)

    [Health Hierarchy]\(正常性の階層\) ウィンドウでユニット モニターを選択した場合は、過去 4 時間以内にコンテナー化されたエージェントによって計算および報告された前のサンプルの **[最終状態変更]** の下にもこれが表示されます。 これは、複数の連続する値を比較してその状態を決定するためのユニット モニターの計算に基づいています。 たとえば、*Pod ready state* ユニット モニターを選択した場合、パラメーター *ConsecutiveSamplesForStateTransition* によって制御される最後の 2 つのサンプルが表示されます。 詳細については、[ユニット モニター](container-insights-health-monitors-config.md#unit-monitors)の詳細な説明を参照してください。
    
    ![正常性プロパティ ウィンドウの [概要] タブ](./media/container-insights-health/health-overview-unit-monitor.png)

    **[最終状態変更]** によって報告された日時が 1 日以上前の場合は、モニターの状態が変更されていないという結果になります。 ただし、ユニット モニターの最後に受信したサンプルが 4 時間より前のものである場合、コンテナー化されたエージェントがデータを送信していないことを示している可能性があります。 ノードなど、特定のリソースが存在することがエージェントによって認識されていても、ノードの CPU またはメモリ使用率モニターから (サンプルとして) データを受信していない場合、モニターの正常性状態は**不明**に設定されます。  

- **[構成]** タブには、既定の構成パラメーターの設定 (集合モニターではなくユニット モニターに対してのみ) とその値が表示されます。
- **[Knowledge]\(ナレッジ\)** タブには、モニターの動作と異常な状態の評価方法を説明する情報が含まれています。

このページのデータは、監視することで自動的に更新されることはありません。クラスターから受信した最新の正常性状態を表示するには、ページの上部にある **[更新]** を選択する必要があります。

## <a name="next-steps"></a>次のステップ

[ログ クエリの例](container-insights-log-search.md#search-logs-to-analyze-data)を表示して、事前定義されたクエリや例を確認し、実際のクラスターのアラート、視覚化、または分析のために評価やカスタマイズを行います。
