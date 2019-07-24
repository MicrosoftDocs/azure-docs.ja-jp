---
title: Azure Monitor ログで B2B メッセージの追跡クエリを作成する - Azure Logic Apps | Microsoft Docs
description: Azure Log Analytics で Azure Logic Apps の AS2、X12、EDIFACT メッセージを追跡するクエリを作成する
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: d4a94e75de34bbafd3bc8f1c1a0d1a6817245e5f
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194913"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-monitor-logs-for-azure-logic-apps"></a>Azure Monitor ログで Azure Logic Apps の B2B メッセージの追跡クエリを作成する

[Azure Monitor ログ](../log-analytics/log-analytics-overview.md) を使用して追跡している AS2、X12、または EDIFACT メッセージを検索するには、特定の条件に基づいてアクションをフィルター処理するクエリを作成できます。 たとえば、特定のインターチェンジ制御番号に基づいてメッセージを検索できます。

> [!NOTE]
> このページでは、以前、Microsoft Operations Management Suite (OMS) を使用してこれらのタスクを実行する手順を説明していましたが、OMS は [2019 年 1 月に廃止される](../azure-monitor/platform/oms-portal-transition.md)ため、代わりに Azure Log Analytics を使用する手順に置き換えられています。 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>前提条件

* 診断ログが設定されているロジック アプリ。 [ロジック アプリを作成する方法](quickstart-create-first-logic-app-workflow.md)および[そのロジック アプリのログを設定する方法](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)を参照してください。

* 監視とログが設定されている統合アカウント。 [統合アカウントを作成する方法](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)および[そのアカウントの監視とログを設定する方法](../logic-apps/logic-apps-monitor-b2b-message.md)を参照してください。

* [診断データの Azure Monitor ログへの発行](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)と [Azure Monitor lログでのメッセージ追跡の設定](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)をまだ行っていない場合は、これらの操作を実行します。

## <a name="create-queries-with-filters"></a>フィルターを使用するクエリを作成する

特定のプロパティや値に基づいてメッセージを検索するため、フィルターを使用するクエリを作成できます。 

1. [Azure portal](https://portal.azure.com) で **[すべてのサービス]** を選択します。 検索ボックスに「log analytics」と入力して検索し、**[Log Analytics]** を選択します。

   ![[Log Analytics] を選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. **[Log Analytics]** で、ご利用の Log Analytics ワークスペースを見つけて選択します。 

   ![Log Analytics ワークスペースを選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. ワークスペース メニューの **[全般]** で、**[ログ (クラシック)]** または **[ログ]** のいずれかを選択します。 

   この例では、クラシック ログのビューを使用する方法を示しています。 
   **[Maximize your Log Analytics experience]\(Log Analytics エクスペリエンスの最大化)** セクションの **[Search and analyze logs]\(検索と分析ログ)** で **[View logs]\(ログの表示)** を選択した場合は、**[Logs (classic view)]\(ログ (クラシック ビュー))** が表示されます。 

   ![クラシック ログを表示する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. クエリの編集ボックスで、検索するフィールド名の入力を開始します。 入力を開始すると、クエリ エディターには一致項目と使用できる操作が表示されます。 クエリを作成した後は、**[実行]** を選択するか、Enter キーを押します。

   この例では、**LogicAppB2B** に対する一致を検索します。 
   詳細については、[Azure Monitor ログでのデータの検索方法](../log-analytics/log-analytics-log-searches.md)に関する記事を参照してください。

   ![クエリ文字列の入力を開始する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. 表示する期間を変更するには、左側のウィンドウで、期間の一覧から選択するか、スライダーをドラッグします。 

   ![期間を変更する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. クエリにフィルターを追加するには、**[追加]** を選択します。 

   ![クエリにフィルターを追加する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. **[Add Filters]\(フィルターの追加)** で、検索するフィルター名を入力します。 フィルターが見つかった場合は、そのフィルターを選択します。 左側のウィンドウで、再度 **[追加]** を選択します。

   たとえば、ここでは **Type=="AzureDiagnostics"** であるイベントに対して検索する別のクエリがあり、**event_record_messageProperties_interchangeControlNumber_s** フィルターを選択することで、インターチェンジ制御番号に基づく結果を検索しています。

   ![フィルター値を選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   **[追加]** の選択後は、選択したフィルター イベントと値によってクエリが更新されています。 
   以前の結果もフィルター処理されています。 

   たとえば、このクエリは **Type=="AzureDiagnostics"** を検索し、**event_record_messageProperties_interchangeControlNumber_s** フィルターを使用して、インターチェンジ制御番号に基づく結果を見つけています。

   ![フィルターが適用された結果](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>クエリを保存する

**[Logs (classic view)]\(ログ (クラシック ビュー))** ビューでクエリを保存するには、以下の手順に従います。

1. **[Logs (classic view)]\(ログ (クラシック ビュー))** ページの自分のクエリから **[Analytics]** を選択します。 

   ![[Analytics] を選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. クエリのツールバーで、**[保存]** を選択します。

   ![[Save]\(保存\) を選択](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. クエリの詳細を指定します。たとえば、クエリに名前を付けて、**[クエリ]** を選択し、カテゴリ名を指定します。 完了したら、**[保存]** を選択します。

   ![[Save]\(保存\) を選択](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. 保存されたクエリを表示するには、クエリ ページに戻ります。 クエリのツールバーで、**[保存された検索条件]** を選択します。

   ![[保存された検索条件] を選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. **[保存された検索条件]** で目的のクエリを選択すると、結果を表示できます。 

   ![クエリを選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   クエリを更新して異なる結果を検索できるようにするには、クエリを編集します。

## <a name="find-and-run-saved-queries"></a>保存されたクエリを検索して実行する

1. [Azure portal](https://portal.azure.com) で **[すべてのサービス]** を選択します。 検索ボックスに「log analytics」と入力して検索し、**[Log Analytics]** を選択します。

   ![[Log Analytics] を選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. **[Log Analytics]** で、ご利用の Log Analytics ワークスペースを見つけて選択します。 

   ![Log Analytics ワークスペースを選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. ワークスペース メニューの **[全般]** で、**[ログ (クラシック)]** または **[ログ]** のいずれかを選択します。 

   この例では、クラシック ログのビューを使用する方法を示しています。 

1. クエリのページが開いたら、クエリのツールバーで **[保存された検索条件]** を選択します。

   ![[保存された検索条件] を選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. **[保存された検索条件]** で目的のクエリを選択すると、結果を表示できます。 

   ![クエリを選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   クエリは自動的に実行されますが、何らかの理由でクエリが実行されない場合は、クエリ エディターで **[実行]** を選択します。

## <a name="next-steps"></a>次の手順

* [AS2 の追跡スキーマ](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 の追跡スキーマ](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [カスタム追跡スキーマ](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)