---
title: Container insights によるデプロイと HPA メトリック | Microsoft Docs
description: この記事では、Container insights を使用して収集されるデプロイと HPA (水平ポッド オートスケーラー) のメトリックについて説明します。
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: c8bb100b756ea92d73e1c3a698f119b4f8157930
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717708"
---
# <a name="deployment--hpa-metrics-with-container-insights"></a>Container insights によるデプロイと HPA メトリック

エージェント バージョン *ciprod08072020* 以降、Container insights が統合されたエージェントでは、デプロイと HPA のメトリックを収集するようになりました。

## <a name="deployment-metrics"></a>デプロイのメトリック

Container insights により、次のメトリックが 60 秒間隔で収集されて、**InsightMetrics** テーブルに格納されることで、デプロイの監視が自動的に開始されます。

|メトリックの名前 |メトリック ディメンション (タグ) |説明 |
|------------|------------------------|------------|
|kube_deployment_status_replicas_ready |container.azm.ms/clusterId、container.azm.ms/clusterName、creationTime、deployment、deploymentStrategy、k8sNamespace、spec_replicas、status_replicas_available、status_replicas_updated (status.updatedReplicas) | このデプロイのターゲットとなる準備完了状態のポッドの合計数 (readyReplicas)。 このメトリックのディメンションを以下に示します。 <ul> <li> deployment - デプロイの名前 </li> <li> k8sNamespace - デプロイのための Kubernetes 名前空間 </li> <li> deploymentStrategy - ポッドを新しいものと交換するために使用するデプロイ戦略 (spec.strategy.type)</li><li> creationTime - デプロイ作成のタイムスタンプ </li> <li> spec_replicas - 必要なポッドの数 (spec.replicas) </li> <li>status_replicas_available - このデプロイのターゲットとなる利用可能なポッド (少なくとも minReadySeconds に対応) の合計数 (status.availableReplicas)</li><li>status_replicas_updated - このデプロイのターゲットとなる、必要なテンプレート仕様を持つ終了していないポッドの合計数 (status.updatedReplicas) </li></ul>|

## <a name="hpa-metrics"></a>HPA のメトリック

Container insights により、次のメトリックが 60 秒間隔で収集され **InsightMetrics** テーブルに格納されることで、HPA の監視が自動的に開始されます。

|メトリックの名前 |メトリック ディメンション (タグ) |説明 |
|------------|------------------------|------------|
|kube_hpa_status_current_replicas |container.azm.ms/clusterId、container.azm.ms/clusterName、creationTime、hpa、k8sNamespace、lastScaleTime、spec_max_replicas、spec_min_replicas、status_desired_replicas、targetKind、targetName | このオートスケーラーによって管理されているポッドの現在のレプリカ数 (status.currentReplicas)。 このメトリックのディメンションを以下に示します。 <ul> <li> hpa - HPA の名前 </li> <li> k8sNamespace - HPA のための Kubernetes 名前空間 </li> <li> lastScaleTime - HPA によってポッドの数が最後にスケーリングされた時間 (status.lastScaleTime)</li><li> creationTime - HPA 作成のタイムスタンプ </li> <li> spec_max_replicas - オートスケーラーによって設定できるポッドの数の上限 (spec.maxReplicas) </li> <li> spec_min_replicas - オートスケーラーでスケールダウンできるレプリカの数の下限 (spec.minReplicas) </li><li>status_desired_replicas - このオートスケーラーによって管理されるポッドのレプリカの望ましい数 (desiredReplicas)</li><li>targetKind - HPA のターゲットの種類 (spec.scaleTargetRef.kind) </li><li>targetName - HPA のターゲットの名前 (spec.scaleTargetRef.name) </li></ul>|

## <a name="deployment--hpa-charts"></a>デプロイと HPA のグラフ 

Container insights には、すべてのクラスターに対するブックとして、前の表で示したメトリックの事前構成済みのグラフが含まれています。 左側のペインで **[ブック]** を選択して AKS クラスターから直接、または [分析情報] の **[ブックの表示]** ドロップダウン リストから、デプロイと HPA のブック **Deployments & HPA** を見つけることができます。

## <a name="next-steps"></a>次の手順

- Kube の状態メトリックの詳細については、[Kubernetes の Kube-state-metrics](https://github.com/kubernetes/kube-state-metrics/tree/master/docs) をご覧ください。