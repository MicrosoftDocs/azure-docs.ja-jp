---
title: "Operations Management Suite で B2B メッセージのクエリを行う - Azure Logic Apps  | Microsoft Docs"
description: "Operations Management Suiteで AS2、X12、および EDIFACT メッセージを追跡するクエリを作成する"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 2748d3d3daf7c13dca05f663a4a088598e1b3605
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="query-for-as2-x12-and-edifact-messages-in-the-microsoft-operations-management-suite-oms"></a>Microsoft Operations Management Suite (OMS) で AS2、X12、および EDIFACT メッセージのクエリを実行する

[Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) で、[Azure Log Analytics](../log-analytics/log-analytics-overview.md) を使用して追跡している AS2、X12、または EDIFACT メッセージを見つけるには、特定の条件に基づいてアクションをフィルター処理するクエリを作成します。 たとえば、特定のインターチェンジ制御番号に基づいてメッセージを検索できます。

## <a name="requirements"></a>必要条件

* 診断ログが設定されているロジック アプリ。 [ロジック アプリの作成方法](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事と[ロジック アプリのログの設定方法](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)を参照してください。

* 監視とログが設定されている統合アカウント。 [統合アカウントの作成方法](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)に関する記事と[ロジック アプリのログの設定方法](../logic-apps/logic-apps-monitor-b2b-message.md)に関する記事を参照してください。

* [診断データの Log Analytics への発行](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)と[OMS でのメッセージ追跡の設定](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)をまだ行っていない場合は、これらの操作を実行します。

> [!NOTE]
> 上の要件を満たした後、[Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) 内にワークスペースを用意する必要があります。 OMS で B2B 通信を追跡するための OMS ワークスペースと同じ OMS ワークスペースを使用する必要があります。 
>  
> OMS ワークスペースを持っていない場合は、[OMS ワークスペースの作成方法](../log-analytics/log-analytics-get-started.md)に関する記事を参照してください。

## <a name="create-message-queries-with-filters-in-the-operations-management-suite-portal"></a>Operations Management Suite ポータルでフィルター付きのメッセージ クエリを作成する

ここでは、インターチェンジ制御番号に基づいてメッセージを検索する方法の例を示します。

> [!TIP] 
> OMS ワークスペースの名前がわかっている場合は、ワークスペースのホーム ページ (`https://{your-workspace-name}.portal.mms.microsoft.com`)に移動して、手順 4 から始めてください。 それ以外の場合は、手順 1 から始めてください。

1. [Azure ポータル](https://portal.azure.com)で、**[その他のサービス]** を選択します。 次に示すように、"ログ分析" を検索し、**[Log Analytics]** を選択します。

   ![Log Analytics の検索](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/browseloganalytics.png)

2. **[Log Analytics]** で、OMS ワークスペースを検索して選択します。

   ![OMS ワークスペースを選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/selectla.png)

3. **[管理]** で、**[OMS ポータル]** を選択します。

   ![OMS ポータルを選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/omsportalpage.png)

4. OMS ホーム ページで、**[ログ検索]** を選択します。

   ![OMS ホーム ページで [ログ検索] を選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   または

   ![[OMS] メニューの [ログ検索] を選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

5. 検索ボックスに、検索するフィールドを入力し、**Enter**キーを押します。 入力を開始すると、OMS によって、一致候補と使用できる操作が表示されます。 詳細については、[Log Analytics のデータの検索方法](../log-analytics/log-analytics-log-searches.md)に関する記事を参照してください。

   この例では、**Type=AzureDiagnostics** のイベントを検索します。

   ![クエリ文字列の入力を開始する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-start-query.png)

6. 左側のバーで、表示する期間を選択します。 クエリにフィルターを追加するには、**[+追加]** を選択します。

   ![クエリにフィルターを追加する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

7. **[フィルターの追加]** にフィルター名を入力して、目的のフィルターを検索できるようにします。 フィルターを選択し、**[+追加]** を選択します。

   インターチェンジ制御番号を検索するため、この例では、「インターチェンジ」という単語を検索し、フィルターとして **[event_record_messageProperties_interchangeControlNumber_s]** を選択しています。

   ![フィルターを選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-add-filter.png)

9. 左側のバーで、使用するフィルター値を選択し、**[適用]** を選択します。

   この例では、目的のメッセージのインターチェンジ制御番号を選択します。

   ![フィルター値を選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-select-filter-value.png)

10. これで、バインドされているクエリに戻ります。 選択したフィルター イベントと値によってクエリが更新されています。 前の結果もフィルター処理されています。

    ![フィルター処理された結果を持つクエリに戻る](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-your-query-for-future-use"></a>将来的に使用するためにクエリを保存する

1. **[ログ検索]** ページのクエリから **[保存]** を選択します。 クエリに名前を付け、カテゴリを選択した後、**[保存]** を選択します。

   ![クエリの名前付けとカテゴリの選択](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-save.png)

2. クエリを表示するには、**[お気に入り]** を選択します。

   ![[お気に入り] を選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-favorites.png)

3. **[保存された検索]** で、クエリを選択して結果を表示できるようにします。 クエリを更新して異なる結果を検索できるようにするには、クエリを編集します。

   ![クエリを選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="find-and-run-saved-queries-in-the-operations-management-suite-portal"></a>Operations Management Suite ポータルで保存されているクエリを検索して実行する

1. OMS ワークスペースのホーム ページ (`https://{your-workspace-name}.portal.mms.microsoft.com`) を開き、**[ログ検索]** を選択します。

   ![OMS ホーム ページで [ログ検索] を選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   または

   ![[OMS] メニューの [ログ検索] を選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

2. **[ログ検索]** ホーム ページで、**[お気に入り]** を選択します。

   ![[お気に入り] を選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-favorites.png)

3. **[保存された検索]** で、クエリを選択して結果を表示できるようにします。 クエリを更新して異なる結果を検索できるようにするには、クエリを編集します。

   ![クエリを選択する](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="next-steps"></a>次のステップ

* [AS2 の追跡スキーマ](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 の追跡スキーマ](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [カスタム追跡スキーマ](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
