---
title: Azure Monitor ログでロジック アプリのクエリを表示および作成する
description: Azure Logic Apps の Azure Monitor ログでクエリを表示および作成する
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5de795811923c965a1473f8d91e3fd654aeef266
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574548"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>Azure Logic Apps の Azure Monitor ログで監視と追跡を行うクエリを表示および作成します

[Azure Monitor ログ](../azure-monitor/logs/log-query-overview.md)から結果を生成する基になるクエリを表示し、特定の条件に基づいて結果をフィルター処理するクエリを作成できます。 たとえば、特定のインターチェンジ制御番号に基づいてメッセージを検索できます。 クエリには [Kusto クエリ言語](/azure/data-explorer/kusto/query/)が使用されます。これは、他の結果を表示するために編集できます。 詳細については、「[Azure Monitor ログ クエリ](/azure/data-explorer/kusto/query/)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Log Analytics ワークスペース。 Log Analytics ワークスペースがない場合は、[Log Analytics ワークスペースの作成方法](../azure-monitor/logs/quick-create-workspace.md)に関するページを参照してください。

* Azure Monitor ログ記録が設定され、その情報を Log Analytics ワークスペースに送信するロジック アプリ。 [ロジック アプリの Azure Monitor ログを設定する方法](../logic-apps/monitor-logic-apps.md)を確認してください。

* 統合アカウントを使用している場合は、その情報を Log Analytics ワークスペースに送信するために、アカウントで Azure Monitor ログ記録が設定されていることを確認します。 [統合アカウントの Azure Monitor ログ記録を設定する方法](../logic-apps/monitor-b2b-messages-log-analytics.md)を確認してください。

## <a name="view-queries-behind-results"></a>結果の背後にあるクエリを表示する

ワークスペースの概要で結果を生成するクエリを表示または編集するには、次の手順を実行します。

1. 任意の [結果] ページの下部にある **[すべて表示]** を選択します。

   ![すべての結果を見る](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   [ログ] ページが開き、先の [結果] ページの背後にあるクエリが表示されます。

   ![ログ ページ - クエリ ビュー](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. **[ログ]** ページでは、次のオプションを選択できます。

   * クエリ結果をテーブルとして表示するには、クエリ エディターで **[テーブル]** を選択します。

   * クエリを変更するには、クエリ文字列を更新し、 **[実行]** を選択してテーブルに結果を表示します。

## <a name="create-your-own-query"></a>独自のクエリを作成する

特定のプロパティまたは値に基づいて結果を検索する、またはフィルター処理するには、空のクエリから開始するか既存のクエリを使用して、独自のクエリを作成できます。 詳細については、「[Azure Monitor でログ クエリの使用を開始する](../azure-monitor/logs/get-started-queries.md)」を参照してください。

1. [Azure portal](https://portal.azure.com) で、Log Analytics ワークスペースを検索して選択します。

1. ワークスペース メニューの **[全般]** で、 **[ログ]** を選択します。

1. 空のクエリから、または使用可能な既存のクエリから開始します。

   * 既存のクエリが使用できるかどうかを確認するには、[クエリ] ツール バーで **[サンプル クエリ]**  >  **[履歴]** を選択します。これにより、以前のクエリ実行からのクエリが表示されます。または、事前に構築されたクエリが表示される **[クエリ エクスプローラー]** を選択します。

     たとえば、Logic Apps B2B ソリューションは、次のような事前に構築されたクエリを提供します。

     ![[Logic Apps B2B] ソリューションの事前に構築されたクエリを使用して開始する](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * 空のクエリから開始するには、クエリ エディターでクエリ用の [Kusto クエリ言語](/azure/data-explorer/kusto/query/)の入力を開始します。

     ![空のクエリで開始](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>次のステップ

* [AS2 の追跡スキーマ](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 の追跡スキーマ](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [カスタム追跡スキーマ](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)