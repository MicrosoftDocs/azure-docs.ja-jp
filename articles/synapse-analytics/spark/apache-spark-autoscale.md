---
title: Apache Spark インスタンスの自動スケーリング
description: Azure Synapse の自動スケール機能を使用して、Apache Spark インスタンスを自動的にスケーリングします
author: euangMS
ms.author: euang
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 03/31/2020
ms.openlocfilehash: c043941543088d9bdbfd535f372e2335e1ba55a5
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500353"
---
# <a name="automatically-scale-azure-synapse-analytics-apache-spark-pools"></a>Azure Synapse Analytics Apache Spark プールの自動スケーリング

Apache Spark for Azure Synapse Analytics プールの自動スケーリング機能は、クラスター インスタンス内のノード数を自動的に増減する機能です。 新しい Apache Spark for Azure Synapse Analytics プールを作成する際には、自動スケーリングが選択されている場合の、ノードの最小数と最大数を設定できます。 その後、自動スケーリングによって負荷のリソース要件が監視され、ノードの数がスケールアップまたはスケールダウンされます。 この機能に対して追加料金は発生しません。

## <a name="metrics-monitoring"></a>メトリックの監視

自動スケーリングは Spark インスタンスを継続的に監視し、次のメトリックを収集します。

|メトリック|説明|
|---|---|
|保留中の CPU の合計|すべての保留中のノードの実行を開始するために必要なコアの総数。|
|保留中のメモリの合計|すべての保留中のノードの実行を開始するために必要なメモリの合計 (MB 単位)。|
|空き CPU の合計|アクティブなノード上のすべての未使用のコアの合計。|
|空きメモリの合計|アクティブなノード上の未使用のメモリの合計 (MB 単位)。|
|ノードごとの使用済みメモリ|ノードに対する負荷。 10 GB のメモリが使用されているノードは、使用済みメモリが 2 GB のワーカーより多くの負荷がかかっていると見なされます。|

上のメトリックは 30 秒ごとにチェックされます。 自動スケーリングでは、これらのメトリックに基づいてスケールアップおよびスケールダウンの決定が行われます。

## <a name="load-based-scale-conditions"></a>負荷ベースのスケール条件

次の条件が検出されると、自動スケーリングによりスケーリング要求が発行されます。

|スケールアップ|スケールダウン|
|---|---|
|保留中の CPU の合計が空き CPU の合計を超えて 1 分以上が経過した。|保留中の CPU の合計が空き CPU の合計を下回って 2 分以上が経過した。|
|保留中のメモリの合計が空きメモリの合計を超えて 1 分以上が経過した。|保留中のメモリの合計が空きメモリの合計を下回って 2 分以上が経過した。|

スケールアップの場合、Azure Synapse Autoscale サービスによって、現在の CPU とメモリの要件を満たすために必要な新しいノードの数が計算された後、必要な数のノードを追加するスケールアップ要求が発行されます。

スケールダウンの場合、ノードごとの Executor の数、アプリケーション マスターの数、および現在の CPU とメモリの要件に基づき、自動スケーリングにより、特定の数のノードを削除する要求が発行されます。 また、サービスでは、現在のジョブの実行に基づいて、削除候補のノードも検出されます。 スケールダウン操作では、最初にノードの使用が停止された後、クラスターから削除されます。

## <a name="get-started"></a>はじめに

### <a name="create-a-spark-pool-with-autoscaling"></a>自動スケーリングを使用して Spark プールを作成する

自動スケーリング機能を有効にするには、通常のプールの作成プロセスの一部として、次の手順を行います。

1. **[基本]** タブで、 **[自動スケーリングの有効化]** チェック ボックスをオンにします。
1. 次のプロパティに希望する値を入力します。  

    * ノードの**最小**数。
    * ノードの**最大**数。

ノードの初期数は最小値になります。 この値によって、インスタンスが作成されるときのその初期サイズが定義されます。 ノードの最小数を 3 より小さくすることはできません。

## <a name="best-practices"></a>ベスト プラクティス

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>スケールアップ操作またはスケールダウン操作の待ち時間について検討する

スケーリング操作が完了するまでに、1 分から 5 分かかる場合があります。

### <a name="prepare-for-scaling-down"></a>スケール ダウンを準備する

インスタンスのスケール ダウン処理中には、自動スケーリングによってノードが使用停止状態にされるため、そのノードでは新しい Executor を起動できなくなります。

実行中のジョブは引き続き実行されて終了します。 保留中のジョブは、少ない数の使用可能ノードで通常どおりにスケジュールされるのを待ちます。

## <a name="next-steps"></a>次のステップ

新しい Spark プールを設定する ([Spark プールの作成](../quickstart-create-apache-spark-pool-portal.md)に関するクイック スタート)
