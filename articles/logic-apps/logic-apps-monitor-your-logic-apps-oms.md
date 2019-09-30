---
title: Azure Monitor でロジック アプリを監視する - Azure Logic Apps
description: ロジック アプリの実行を診断し、問題を解決する目的で、Azure Monitor ログを使用して分析情報とデバッグ データを取得します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 08/29/2019
ms.openlocfilehash: a038a05f03ce7a209ae82203441750749bc6c4c4
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70138781"
---
# <a name="get-insights-and-debugging-data-for-logic-apps-by-using-azure-monitor-logs"></a>Azure Monitor ログを使用してロジック アプリの分析情報とデバッグデータを取得する

ロジック アプリを監視してより多くのデバッグ詳細情報を取得するには、ロジック アプリの作成時に [Azure Monitor ログ](../log-analytics/log-analytics-overview.md)を有効にします。 Azure portal で Logic Apps 管理ソリューションをインストールすると、Azure Monitor ログによりロジック アプリの診断ログと監視の機能が提供されます。 このソリューションは、ロジック アプリ実行の集計情報に加えて、状態、実行時間、再送信の状態、関連付け ID など、特定の詳細情報も提供します。 この記事では、Azure Monitor ログを有効にして、ロジック アプリ実行のランタイム イベントとランタイム データを確認する方法を説明します。

このトピックでは、ロジック アプリを作成するときに Azure Monitor ログを設定する方法について説明します。 既存のロジック アプリに対して Azure Monitor ログを有効にするには、[診断ログを有効にしてロジック アプリのランタイム データを Azure Monitor ログに送信する](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)手順に従います。

> [!NOTE]
> このページでは以前、Microsoft Operations Management Suite (OMS) を使用してこれらのタスクを実行する手順を説明していましたが、OMS は [2019 年 1 月に廃止され](../azure-monitor/platform/oms-portal-transition.md)、Log Analytics という用語に取って代わった [Azure Monitor ログ](../azure-monitor/platform/data-platform-logs.md)で手順が置き換えられています。 ログ データは引き続き Log Analytics ワークスペースに格納され、同じ Log Analytics サービスによって収集されて分析されます。 詳細については、[Azure Monitor の用語の変更](../azure-monitor/terminology.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

開始する前に、Log Analytics ワークスペースが必要です。 [Log Analytics ワークスペースの作成方法](../azure-monitor/learn/quick-create-workspace.md)に関するページを参照してください。

## <a name="turn-on-logging-for-new-logic-apps"></a>新しいロジック アプリのログ記録を有効にする

1. [Azure portal](https://portal.azure.com) で、ロジック アプリを作成します。 Azure のメイン メニューで、 **[リソースの作成]**  >  **[統合]**  >  **[ロジック アプリ]** の順に選択します。

   ![新しいロジック アプリを作成する](media/logic-apps-monitor-your-logic-apps-oms/create-new-logic-app.png)

1. **[ロジック アプリ]** で次の手順を実行します。

   1. ロジック アプリに名前を付け、Azure サブスクリプションを選択します。

   1. Azure リソース グループを作成するか選択します。 ロジック アプリの場所を選択します。

   1. **[Log Analytics]** で **[オン]** を選択します。

   1. **Log Analytics ワークスペース**の一覧から、ロジック アプリの実行からデータを送信するワークスペースを選択します。

      ![ロジック アプリに関する情報の入力](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app-details.png)

      この手順を完了すると、Azure によりロジック アプリが作成されます。このアプリは、Log Analytics ワークスペースに関連付けられています。 この手順では、ワークスペースに Logic Apps 管理ソリューションが自動的にインストールされます。

   1. 完了したら **[作成]** を選択します。

1. ロジック アプリの実行を確認するには、[この手順を続行します](#view-logic-app-runs-oms)。

## <a name="install-logic-apps-management-solution"></a>Logic Apps 管理ソリューションをインストールする

ロジック アプリの作成時に既に Azure Monitor ログを設定している場合は、この手順をスキップします。 Logic Apps 管理ソリューションが既にインストールされています。

1. [Azure portal](https://portal.azure.com) で **[すべてのサービス]** を選択します。 検索ボックスに「log analytics workspaces」と入力して検索し、 **[Log Analytics ワークスペース]** を選択します。

   ![[Log Analytics ワークスペース] を選択する](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. **[Log Analytics ワークスペース]** でワークスペースを選択します。

   ![Log Analytics ワークスペースを選択する](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. [概要] ウィンドウで **[Log Analytics の使用を開始する]** 、 **[監視ソリューションの構成]** の順に選択し、 **[ソリューションの表示]** を選択します。

   ![[ソリューションの表示] を選択する](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. **[概要]** で **[追加]** を選択します。

   ![[追加] の選択](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

1. **[マーケットプレース]** が開いたら、検索ボックスに「logic apps management」と入力し、 **[Logic Apps 管理]** を選択します。

   ![[Logic Apps 管理] を選択する](./media/logic-apps-monitor-your-logic-apps-oms/select-logic-apps-management.png)

1. [ソリューションの説明] ウィンドウで **[作成]** を選択します。

   ![[Logic Apps 管理] の [作成] を選択する](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

1. ソリューションをインストールする Log Analytics ワークスペースを確認して確定し、もう一度 **[作成]** を選択します。

   ![[Logic Apps 管理] の [作成] を選択する](./media/logic-apps-monitor-your-logic-apps-oms/confirm-log-analytics-workspace.png)

   Log Analytics ワークスペースが含まれる Azure リソース グループに Azure によってソリューションがデプロイされると、ワークスペースの [概要] ウィンドウにソリューションが表示されます。

   ![ワークスペースの [概要] ウィンドウ](./media/logic-apps-monitor-your-logic-apps-oms/workspace-summary-pane-logic-apps-management.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>ロジック アプリ実行情報を参照する

ロジック アプリを実行した後、 **[Logic Apps 管理]** タイルで状態と実行件数を参照できます。

1. Log Analytics ワークスペースに移動し、 **[ワークスペースの概要]** 、 **[Logic Apps 管理]** の順に選択します。

   ![ロジック アプリの実行状態と実行件数](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary.png)

   ここでは、ロジック アプリの実行は名前または実行状態でグループ化されます。 このページには、ロジック アプリの実行のアクションまたはトリガーで発生したエラーに関する詳細も表示されます。

   ![ロジック アプリの実行状態の概要](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary-details.png)

1. 特定のロジック アプリまたは状態のすべての実行を表示するには、ロジック アプリまたは状態の行を選択します。

   特定のロジック アプリのすべての実行を表示する例は次の通りです。

   ![ロジック アプリの実行と状態を表示する](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   このページには、高度なオプションがあります。 

   * **[追跡対象プロパティ]** 列:追跡対象プロパティを設定し、アクションでグループ分けしたロジック アプリについては、この列からそのプロパティを表示できます。 追跡対象プロパティを表示するには、 **[表示]** を選択します。 追跡対象プロパティを検索するには、列フィルターを使用します。

      ![ロジック アプリの追跡対象プロパティを表示する](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

      追跡対象プロパティを新たに追加した場合、最初に表示されるまでに 10 - 15 分かかる場合があります。 [ロジック アプリに追跡対象プロパティを追加する方法](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details)をご覧ください。

   * **[再送信]** :失敗、成功、または実行中の状態の、1 つ以上のロジック アプリの実行を再送信できます。 再送信する実行のチェック ボックスを選択し、 **[再送信]** を選択します。

     ![ロジック アプリの実行を再送信する](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. 結果をフィルター処理するには、クライアント側とサーバー側の両方でフィルター処理を実行します。

   * **クライアント側のフィルター**:列ごとに次のように必要なフィルターを選択します。

     ![列フィルターの例](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **サーバー側のフィルター**:特定の時間枠を選択するか、表示される実行の数を制限するには、ページの上部にあるスコープ コントロールを使用します。 既定では、一度に 1000 件のレコードのみ表示されます。

     ![時間枠を変更する](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)

1. 特定の実行のすべてのアクションとその詳細を表示するには、ロジック アプリの実行の行を選択します。

   特定のロジック アプリの実行のすべてアクションとトリガーを表示する例は次のとおりです。

   ![ロジック アプリの実行のアクションを表示する](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)

1. [結果] ページで結果の背後にあるクエリを表示したり、すべての結果を表示したりするには、 **[すべて表示]** を選択します。選択後、 **[ログ]** ページが開きます。

   ![すべての結果を見る](media/logic-apps-monitor-your-logic-apps-oms/logic-app-see-all.png)

   **[ログ]** ページで、次のオプションを選択できます。

   * テーブルのクエリ結果を表示するには、 **[テーブル]** を選択します。

   * クエリには [Kusto クエリ言語](https://aka.ms/LogAnalyticsLanguageReference)が使用されます。これを編集し、他の結果を表示できます。 クエリを変更するには、クエリ文字列を更新し、 **[実行]** を選択してテーブルに結果を表示します。 

     ![Log Analytics - クエリ ビュー](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>次の手順

* [B2B メッセージを監視する](../logic-apps/logic-apps-monitor-b2b-message.md)