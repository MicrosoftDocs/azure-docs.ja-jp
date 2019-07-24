---
title: Azure Monitor ログを使用して Azure Service Fabric のコンテナーを監視する | Microsoft Docs
description: Azure Service Fabric クラスターで実行されているコンテナーを監視するために Azure Monitor ログを使用します。
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: d03d68560502821b9c343be983d9f7b5a83ed977
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663194"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Azure Monitor ログでコンテナーを監視する
 
この記事では、コンテナーのイベントを表示するための Azure Monitor ログ コンテナー監視ソリューションを設定するために必要な手順について説明します。 コンテナー イベントを収集するクラスターを設定するには、こちらの[ステップ バイ ステップ チュートリアル](service-fabric-tutorial-monitoring-wincontainers.md)を参照してください。 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>コンテナー監視ソリューションの設定

> [!NOTE]
> お使いのクラスターに対して Azure Monitor ログを設定し、ノード上に Log Analytics エージェントをデプロイする必要があります。 実行していない場合は、[Azure Monitor ログの設定](service-fabric-diagnostics-oms-setup.md)と [Log Analytics エージェントのクラスターへの追加](service-fabric-diagnostics-oms-agent.md)の手順を先に実行してください。

1. お使いのクラスターに Azure Monitor ログと Log Analytics エージェントを設定した後、コンテナーをデプロイします。 コンテナーが展開されるのを待ってから次の手順に進みます。

2. Azure Marketplace で、「*コンテナー監視ソリューション*」を検索し、監視 + 管理] カテゴリの下に表示される **[コンテナー監視ソリューション]** リソースをクリックします。

    ![コンテナー ソリューションの追加](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. 既にクラスターに対して作成したのと同じワークスペース内でソリューションを作成します。 この変更により、コンテナーの Docker データの収集を開始するエージェントが自動的にトリガーされます。 下の図に示すように、約 15 分位でソリューションが点灯し、ログの受信と統計が表示されるのを確認できます。

    ![Log Analytics の基本のダッシュボード](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

エージェントによって、Azure Monitor ログでクエリしたり、パフォーマンス インジケーターで視覚化するために使用したりできるコンテナー固有のさまざまなログを収集できます。 収集されるログの種類は次のとおりです。

* ContainerInventory: コンテナーの場所、名前、およびイメージに関する情報を表示
* ContainerImageInventory: ID やサイズなど、デプロイ済みのイメージに関する情報
* ContainerLog: 特定のエラー ログ、Docker ログ (stdout など)、およびその他のエントリ
* ContainerServiceLog: 実行されている Docker デーモン コマンド
* Perf: コンテナーの CPU、メモリ、ネットワーク トラフィック、ディスク I/O、およびホスト コンピューターからのカスタム メトリックなどのパフォーマンス カウンター



## <a name="next-steps"></a>次の手順
* [Azure Monitor ログ コンテナー ソリューション](../azure-monitor/insights/containers.md)の詳細を確認する
* Service Fabric のコンテナー オーケストレーションについての詳細 - [Service Fabric とコンテナー](service-fabric-containers-overview.md)
* Azure Monitor ログの一部として提供されている[ログ検索とクエリ](../log-analytics/log-analytics-log-searches.md)機能をよく理解する
* Azure Monitor ログを構成して、検出と診断に役立つ[自動アラート](../log-analytics/log-analytics-alerts.md)を設定する