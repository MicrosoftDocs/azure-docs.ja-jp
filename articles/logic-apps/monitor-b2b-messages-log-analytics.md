---
title: Azure Monitor を使用して B2B メッセージを監視する
description: Azure Monitor ログを設定して Azure Logic Apps の診断データを収集することで、AS2、X12、および EDIFACT メッセージのトラブルシューティングを行う
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 765c15897bd5d435503d3bef07e76a93b148971c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596857"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>Azure Monitor ログを設定し、Azure Logic Apps で B2B メッセージの診断データを収集する

統合アカウントで取引先間の B2B 通信を設定すると、これらの取引先が AS2、X12、および EDIFACT などのプロトコルを使用してメッセージを交換できるようになります。 この通信が期待どおりに動作することを確認するには、統合アカウントの [Azure Monitor ログ](../azure-monitor/logs/data-platform-logs.md)を設定します。 [Azure Monitor](../azure-monitor/overview.md) を使用すると、クラウド環境とオンプレミス環境を監視して、可用性とパフォーマンスをより簡単に維持することができます。 Azure Monitor ログを使用すると、トリガー イベント、実行イベント、アクション イベントなどのランタイム データやイベントに関するデータを、[Log Analytics ワークスペース](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)に記録して格納することができます。 メッセージの場合、ログは次のような情報も収集します。

* メッセージの数と状態
* 受信確認の状態
* メッセージと受信確認の相関関係
* エラーの詳細な説明

Azure Monitor を使用すると、この情報の検索と確認に役立つ[ログ クエリ](../azure-monitor/logs/log-query-overview.md)を作成できます。 Azure Storage や Azure Event Hubs などの[他の Azure サービスでこの診断データを使用する](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data)こともできます。

統合アカウントのログ記録を設定するには、Azure portal で [Logic Apps B2B ソリューションをインストール](#install-b2b-solution)します。 このソリューションは、B2B メッセージ イベントの集約情報を提供します。 次に、この情報に対してログの記録とクエリの作成を有効にするために、[Azure Monitor ログ](#set-up-resource-logs)を設定します。

この記事では、統合アカウントの Azure Monitor ログ記録を有効にする方法について説明します。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>前提条件

* Log Analytics ワークスペース。 Log Analytics ワークスペースがない場合は、[Log Analytics ワークスペースの作成方法](../azure-monitor/logs/quick-create-workspace.md)に関するページを参照してください。

* Azure Monitor ログ記録が設定され、その情報を Log Analytics ワークスペースに送信するロジック アプリ。 [ロジック アプリの Azure Monitor ログを設定する方法](../logic-apps/monitor-logic-apps.md)を確認してください。

* ロジック アプリにリンクされた統合アカウント。 [ロジック アプリに統合アカウントをリンクする方法](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)を確認してください。

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>Logic Apps B2B ソリューションをインストールする

Azure Monitor ログがロジック アプリの B2B メッセージを追跡できるようにする前に、**Logic Apps B2B** ソリューションを Log Analytics ワークスペースに追加します。

1. [Azure portal](https://portal.azure.com) の検索ボックスに「`log analytics workspaces`」と入力し、 **[Log Analytics ワークスペース]** を選択します。

   ![[Log Analytics ワークスペース] を選択する](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. **[Log Analytics ワークスペース]** でワークスペースを選択します。

   ![Log Analytics ワークスペースを選択する](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. [概要] ウィンドウで **[Log Analytics の使用を開始する]** 、 **[監視ソリューションの構成]** の順に選択し、 **[ソリューションの表示]** を選択します。

   ![[概要] ウィンドウで [ソリューションの表示] を選択する](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. [概要] ウィンドウで、 **[追加]** を選択します。

   ![[概要] ウィンドウで新しいソリューションを追加する](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. **[Marketplace]** が開いたら、検索ボックスに「`logic apps b2b`」と入力し、 **[Logic Apps B2B]** を選択します。

   ![Marketplace から [Logic Apps 管理] を選択する](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. [ソリューションの説明] ウィンドウで **[作成]** を選択します。

   ![[作成] を選択して [Logic Apps B2B] ソリューションを追加する](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. ソリューションをインストールする Log Analytics ワークスペースを確認して確定し、もう一度 **[作成]** を選択します。

   ![[Logic Apps B2B] の [作成] を選択する](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   Log Analytics ワークスペースが含まれる Azure リソース グループに Azure によってソリューションがデプロイされると、ワークスペースの [概要] ウィンドウにソリューションが表示されます。 B2B メッセージが処理されると、このウィンドウのメッセージ数が更新されます。

   ![ワークスペースの [概要] ウィンドウ](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Azure Monitor ログを設定する

統合アカウントから直接 Azure Monitor ログ記録を有効にすることができます。

1. [Azure ポータル](https://portal.azure.com)で、統合アカウントを検索して選択します。

   ![統合アカウントを探して選択する](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. 統合アカウントのメニューにある **[監視]** から、 **[診断設定]** を選択します。 **[診断設定の追加]** を選択します。

   ![[監視] で [診断設定] を選択する](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. 設定を作成するには、次の手順を実行します。

   1. 設定の名前を入力します。

   1. **[Log Analytics への送信]** を選択します。

   1. **[サブスクリプション]** では、Log Analytics ワークスペースに関連付けられている Azure サブスクリプションを選択します。

   1. **[Log Analytics ワークスペース]** では、使用するワークスペースを選択します。

   1. **[ログ]** にある **[IntegrationAccountTrackingEvents]** カテゴリを選択します。これは、記録するイベント カテゴリを指定します。

   1. 終了したら、 **[保存]** を選択します。

   次に例を示します。 

   ![診断データを収集するように Azure Monitor ログを設定する](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>メッセージの状態を表示する

ロジック アプリが実行されると、それらのメッセージに関する状態とデータを Log Analytics ワークスペースに表示できます。

1. [Azure portal](https://portal.azure.com) の検索ボックスで、Log Analytics ワークスペースを探して開きます。

1. ワークスペースのメニューで、 **[ワークスペースの概要]**  >  **[Logic Apps B2B]** を選択します。

   ![ワークスペースの [概要] ウィンドウ](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > 実行後すぐに [Logic Apps B2B] タイルに結果が表示されない場合は、 **[最新の情報に更新]** を選択するか、しばらく待ってから再試行してください。

   既定では、 **[Logic Apps B2B]** タイルには 1 日に基づいたデータが表示されます。 データの範囲を異なる間隔に変更するには、ページの上部にある範囲コントロールを選択します。

   ![間隔を変更する](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. メッセージの状態ダッシュボードが表示された後、特定のメッセージの種類の詳細を表示できます。ここには、1 日に基づいたデータが表示されます。 **AS2**、**X12**、または **EDIFACT** のタイルを選択します。

   ![メッセージの状態の表示](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   選択されたタイルのメッセージの一覧が表示されます。 たとえば、AS2 メッセージの一覧が表示されるようすを次に示します。

   ![AS2 メッセージの状態と詳細](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

   メッセージの種類ごとのプロパティの詳細については、次のメッセージのプロパティの説明を参照してください。

   * [AS2 メッセージのプロパティ](#as2-message-properties)
   * [X12 メッセージのプロパティ](#x12-message-properties)
   * [EDIFACT メッセージのプロパティ](#EDIFACT-message-properties)

<!--
1. To view or export the inputs and outputs for specific messages, select those messages, and select **Download**. When you're prompted, save the .zip file to your local computer, and then extract that file.

   The extracted folder includes a folder for each selected message. If you set up acknowledgements, the message folder also includes files with acknowledgement details. Each message folder has at least these files:
   
   * Human-readable files with the input payload and output payload details
   * Encoded files with the inputs and outputs

   For each message type, you can find the folder and file name formats here:

   * [AS2 folder and file name formats](#as2-folder-file-names)
   * [X12 folder and file name formats](#x12-folder-file-names)
   * [EDIFACT folder and file name formats](#edifact-folder-file-names)

   ![Download message files](./media/monitor-b2b-messages-log-analytics/download-messages.png)

1. To view all actions that have the same run ID, on the **Log Search** page, select a message from the message list.

   You can sort these actions by column, or search for specific results.

   * To search results with prebuilt queries, select **Favorites**.

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../azure-monitor/logs/log-query-overview.md).

   * To change query in the search box, update the query with the columns and values that you want to use as filters.
-->

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>AS2、X12、および EDIFACT メッセージのプロパティの説明と名前の形式

メッセージの種類ごとに、ダウンロードされたメッセージ ファイルのプロパティの説明と名前の形式を次に示します。

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>AS2 メッセージのプロパティの説明

AS2 メッセージごとのプロパティの説明を次に示します。

| プロパティ | 説明 |
|----------|-------------|
| **送信者** | **[受信設定]** で指定されているゲスト パートナー、または AS2 契約の **[送信設定]** で指定されているホスト パートナー |
| **受信者** | **[受信設定]** で指定されているホスト パートナー、または AS2 契約の **[送信設定]** で指定されているゲスト パートナー |
| **ロジック アプリ** | AS2 アクションが設定されているロジック アプリ |
| **状態** | AS2 メッセージの状態 <br>[成功] = 有効な AS2 メッセージを受信または送信しました。 MDN が設定されていません。 <br>[成功] = 有効な AS2 メッセージを受信または送信しました。 MDN が設定および受信されたか、または MDN が送信されました。 <br>[失敗] = 無効な AS2 メッセージを受信しました。 MDN が設定されていません。 <br>[Pending] \(保留中) = 有効な AS2 メッセージを受信または送信しました。 MDN が設定されており、MDN が予測されています。 |
| **ACK** | MDN メッセージの状態 <br>[承認済み] = 肯定の MDN を受信または送信しました。 <br>[Pending] \(保留中) = MDN の受信または送信を待機しています。 <br>[拒否] = 否定の MDN を受信または送信しました。 <br>[必要なし] = MDN が契約で設定されていません。 |
| **方向** | AS2 メッセージの方向 |
| **追跡 ID** | ロジック アプリ内のすべてのトリガーとアクションを関連付ける ID |
| **メッセージ ID** | AS2 メッセージ ヘッダーからの AS2 メッセージ ID |
| **Timestamp** | AS2 アクションがメッセージを処理した時間 |
|||

<!--
<a name="as2-folder-file-names"></a>

### AS2 name formats for downloaded message files

Here are the name formats for each downloaded AS2 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|||
-->

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>X12 メッセージのプロパティの説明

X12 メッセージごとのプロパティの説明を次に示します。

| プロパティ | 説明 |
|----------|-------------|
| **送信者** | **[受信設定]** で指定されているゲスト パートナー、または X12 契約の **[送信設定]** で指定されているホスト パートナー |
| **受信者** | **[受信設定]** で指定されているホスト パートナー、または X12 契約の **[送信設定]** で指定されているゲスト パートナー |
| **ロジック アプリ** | X12 アクションが設定されているロジック アプリ |
| **状態** | X12 メッセージの状態 <br>[成功] = 有効な X12 メッセージを受信または送信しました。 機能確認が設定されていません。 <br>[成功] = 有効な X12 メッセージを受信または送信しました。 機能確認が設定および受信されたか、または機能確認が送信されました。 <br>[失敗] = 無効な X12 メッセージを受信または送信しました。 <br>[Pending] \(保留中) = 有効な X12 メッセージを受信または送信しました。 機能確認が設定されており、機能確認が予測されています。 |
| **ACK** | 機能確認 (997) の状態 <br>[承認済み] = 肯定の機能確認を受信または送信しました。 <br>[拒否] = 否定の機能確認を受信または送信しました。 <br>[Pending] \(保留中) = 機能確認を予測していましたが、受信しませんでした。 <br>[Pending] \(保留中) = 機能確認を生成しましたが、パートナーに送信できません。 <br>[必要なし] = 機能確認が設定されていません。 |
| **方向** | X12 メッセージの方向 |
| **追跡 ID** | ロジック アプリ内のすべてのトリガーとアクションを関連付ける ID |
| **メッセージの種類** | EDI X12 メッセージの種類 |
| **ICN** | X12 メッセージのインターチェンジ制御番号 |
| **TSCN** | X12 メッセージのトランザクション セット制御番号 |
| **Timestamp** | X12 アクションがメッセージを処理した時間 |
|||

<!--
<a name="x12-folder-file-names"></a>

### X12 name formats for downloaded message files

Here are the name formats for each downloaded X12 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|||
-->

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>EDIFACT メッセージのプロパティの説明

EDIFACT メッセージごとのプロパティの説明を次に示します。

| プロパティ | 説明 |
|----------|-------------|
| **送信者** | **[受信設定]** で指定されているゲスト パートナー、または EDIFACT 契約の **[送信設定]** で指定されているホスト パートナー |
| **受信者** | **[受信設定]** で指定されているホスト パートナー、または EDIFACT 契約の **[送信設定]** で指定されているゲスト パートナー |
| **ロジック アプリ** | EDIFACT アクションが設定されているロジック アプリ |
| **状態** | EDIFACT メッセージの状態 <br>[成功] = 有効な EDIFACT メッセージを受信または送信しました。 機能確認が設定されていません。 <br>[成功] = 有効な EDIFACT メッセージを受信または送信しました。 機能確認が設定および受信されたか、または機能確認が送信されました。 <br>[失敗] = 無効な EDIFACT メッセージを受信または送信しました。 <br>[Pending] \(保留中) = 有効な EDIFACT メッセージを受信または送信しました。 機能確認が設定されており、機能確認が予測されています。 |
| **ACK** | 機能確認 (CONTRL) の状態 <br>[承認済み] = 肯定の機能確認を受信または送信しました。 <br>[拒否] = 否定の機能確認を受信または送信しました。 <br>[Pending] \(保留中) = 機能確認を予測していましたが、受信しませんでした。 <br>[Pending] \(保留中) = 機能確認を生成しましたが、パートナーに送信できません。 <br>[必要なし] = 機能確認が設定されていません。 |
| **方向** | EDIFACT メッセージの方向 |
| **追跡 ID** | ロジック アプリ内のすべてのトリガーとアクションを関連付ける ID |
| **メッセージの種類** | EDIFACT メッセージの種類 |
| **ICN** | EDIFACT メッセージのインターチェンジ制御番号 |
| **TSCN** | EDIFACT メッセージのトランザクション セット制御番号 |
| **Timestamp** | EDIFACT アクションがメッセージを処理した時間 |
|||

<!--
<a name="edifact-folder-file-names"></a>

### EDIFACT name formats for downloaded message files

Here are the name formats for each downloaded EDIFACT message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|||
-->

## <a name="next-steps"></a>次のステップ

* [監視および追跡クエリを作成する](../logic-apps/create-monitoring-tracking-queries.md)
