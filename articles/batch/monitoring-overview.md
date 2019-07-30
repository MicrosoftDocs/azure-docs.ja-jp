---
title: Azure Batch の監視 | Microsoft Docs
description: Azure Batch の Azure 監視サービス、メトリック、診断ログ、その他の監視機能について説明します。
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: lahugh
ms.openlocfilehash: 77fcfed125104588e27e6a0e80cdb4a92a744083
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322447"
---
# <a name="monitor-batch-solutions"></a>Batch ソリューションの監視

Azure と Batch サービスには、Batch ソリューションを監視するためのさまざまなサービス、ツール、API が用意されています。 この概要記事は、ニーズに合った監視アプローチの選択に役立ちます。

Azure リソースの監視に使用できる Azure のコンポーネントとサービスの概要については、「[Azure のアプリケーションおよびリソースの監視](../monitoring-and-diagnostics/monitoring-overview.md)」をご覧ください。

## <a name="subscription-level-monitoring"></a>サブスクリプション レベルの監視

Batch アカウントを含むサブスクリプション レベルでは、[Azure アクティビティ ログ](../azure-monitor/platform/activity-logs-overview.md)によって[いくつかのカテゴリ](../azure-monitor/platform/activity-logs-overview.md#categories-in-the-activity-log)の運用イベント データが収集されます。

具体的に Batch アカウントでは、アクティビティ ログによってアカウントの作成と削除やキー管理に関連するイベントが収集されます。

アクティビティ ログからイベントを取得する 1 つの方法として、Azure Portal を使用できます。 **[すべてのサービス]**  >  **[アクティビティ ログ]** をクリックします。 または Azure CLI、PowerShell コマンドレットか Azure Monitor REST API を使用して、イベントのクエリを実行します。 アクティビティ ログをエクスポートしたり、[アクティビティ ログ アラート](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md)を構成したりすることもできます。

## <a name="batch-account-level-monitoring"></a>Batch アカウント レベルの監視

[Azure Monitor](../azure-monitor/overview.md) の機能を使用して各 Batch アカウントを監視します。 Azure Monitor は、Batch アカウントのレベルをスコープとして、プール、ジョブ、タスクなどのリソースの[メトリック](../azure-monitor/platform/data-platform-metrics.md)と、必要に応じて[診断ログ](../azure-monitor/platform/diagnostic-logs-overview.md)を収集します。 このデータを手動またはプログラムによって収集および利用して、Batch アカウント内のアクティビティを監視し、問題を診断します。 詳しくは、「[Batch ソリューションの診断の評価と監視のログ イベント](batch-diagnostics.md)」をご覧ください。
 
> [!NOTE]
> メトリックは、既定では追加の構成なしで Batch アカウントで利用でき、30 日間のローリング履歴があります。 Batch アカウントの診断ログを有効化する必要があり、診断ログ データの格納または処理には追加のコストが生じる可能性があります。 

## <a name="batch-resource-monitoring"></a>Batch リソースの監視

Batch アプリケーションでは、Batch API を使用して、ジョブ、タスク、ノード、プールなどのリソースの状態の監視またはクエリを実行します。 例:

* [状態ごとにタスクとコンピューティング ノードをカウントする](batch-get-resource-counts.md)
* [効率的に Batch リソースを一覧表示するクエリを作成する](batch-efficient-list-queries.md)
* [タスクの依存関係を作成する](batch-task-dependencies.md)
* [ジョブ マネージャー タスク](/rest/api/batchservice/job/add#jobmanagertask)を使用する
* [タスクの状態](/rest/api/batchservice/task/list#taskstate)を監視する
* [ノードの状態](/rest/api/batchservice/computenode/list#computenodestate)を監視する
* [プールの状態](/rest/api/batchservice/pool/get#poolstate)を監視する
* [アカウントでのプールの使用状況](/rest/api/batchservice/pool/listusagemetrics)を監視する
* [状態別にプール ノード数をカウントする](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>VM パフォーマンス カウンターおよびアプリケーションの監視

* [Application Insights](../azure-monitor/app/app-insights-overview.md) は、Batch のジョブとタスクの可用性、パフォーマンス、使用状況をプログラムで監視するために使用できる Azure サービスです。 コンピューティング ノード (VM) からのパフォーマンス カウンターと VM からのタスクに関するカスタム情報を簡単に取得できます。 

  例については、[Application Insights による Batch .NET アプリケーションの監視とデバッグ](monitor-application-insights.md)に関する記事と付随する[コード サンプル](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights)をご覧ください。

  > [!NOTE]
  > Application Insights の使用には追加コストが発生する可能性があります。 [価格オプション](https://azure.microsoft.com/pricing/details/application-insights/)に関するページをご覧ください。 
  >

* [Batch Explorer](https://github.com/Azure/BatchExplorer) は、Azure Batch アプリケーションの作成、デバッグ、および監視を支援する、豊富な機能を備えた無料のスタンドアロン クライアント ツールです。 Mac、Linux、または Windows 用の[インストール パッケージ](https://azure.github.io/BatchExplorer/)をダウンロードしてください。 必要に応じて、Batch Explorer 内の VM パフォーマンス カウンターなどの [Application Insights データを表示](https://github.com/Azure/batch-insights)するように Batch ソリューションを構成します。


## <a name="next-steps"></a>次の手順

* Batch ソリューションの構築に使用できる [Batch API とツール](batch-apis-tools.md)について学習します。
* Batch での[診断ログ](batch-diagnostics.md)の詳細について学習します。