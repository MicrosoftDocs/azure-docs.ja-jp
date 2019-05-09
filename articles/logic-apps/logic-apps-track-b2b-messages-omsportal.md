---
title: Azure Monitor ログを使用して B2B メッセージを追跡する - Azure Logic Apps | Microsoft Docs
description: Azure Log Analytics で統合アカウントと Azure Logic Apps の B2B 通信を追跡します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 8cf5d9f3ee1503769a2ec199847175899bcd86bf
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193213"
---
# <a name="track-b2b-messages-with-azure-monitor-logs"></a>Azure Monitor ログで B2B メッセージを追跡する

統合アカウントで取引先間の B2B 通信を設定すると、これらの取引先が AS2、X12、および EDIFACT などのプロトコルを使用してメッセージを交換できるようになります。 これらのメッセージが正しく処理されていることを確認するには、[Azure Monitor ログ](../log-analytics/log-analytics-overview.md)を使用してこれらのメッセージを追跡できます。 たとえば、メッセージを追跡するために次の Web ベースの追跡機能を使用できます。

* メッセージの数と状態
* 受信確認の状態
* メッセージと受信確認の関連付け
* エラーの詳細な説明
* 検索機能

> [!NOTE]
> このページでは、以前、Microsoft Operations Management Suite (OMS) を使用してこれらのタスクを実行する手順を説明していましたが、OMS は [2019 年 1 月に廃止される](../azure-monitor/platform/oms-portal-transition.md)ため、代わりに Azure Log Analytics を使用する手順に置き換えられています。 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>前提条件

* 診断ログが設定されているロジック アプリ。 [ロジック アプリを作成する方法](quickstart-create-first-logic-app-workflow.md)および[そのロジック アプリのログを設定する方法](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)を参照してください。

* 監視とログが設定されている統合アカウント。 [統合アカウントを作成する方法](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)および[そのアカウントの監視とログを設定する方法](../logic-apps/logic-apps-monitor-b2b-message.md)を参照してください。

* まだ実行していない場合は、[Azure Monitor ログに診断データを発行](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)します。

* 上記の要件を満たしたら、Log Analytics を使用して B2B 通信を追跡するために使用する Log Analytics ワークスペースも必要になります。 Log Analytics ワークスペースがない場合は、[Log Analytics ワークスペースの作成方法](../azure-monitor/learn/quick-create-workspace.md)に関するページを参照してください。

## <a name="install-logic-apps-b2b-solution"></a>Logic Apps B2B ソリューションをインストールする

Azure Monitor ログでロジック アプリの B2B メッセージを追跡するには、事前に Azure Monitor ログに **Logic Apps B2B** ソリューションを追加します。 [Azure Monitor ログへのソリューションの追加](../azure-monitor/learn/quick-create-workspace.md)に関する詳細を参照してください。

1. [Azure portal](https://portal.azure.com) で **[すべてのサービス]** を選択します。 検索ボックスに「log analytics」と入力して検索し、**[Log Analytics]** を選択します。

   ![[Log Analytics] を選択する](media/logic-apps-track-b2b-messages-omsportal/find-log-analytics.png)

1. **[Log Analytics]** で、ご利用の Log Analytics ワークスペースを見つけて選択します。 

   ![Log Analytics ワークスペースを選択する](media/logic-apps-track-b2b-messages-omsportal/select-log-analytics-workspace.png)

1. **[Log Analytics の使用を開始する]** > **[監視ソリューションの構成]** の順に選択し、**[ソリューションの表示]** を選択します。

   ![[ソリューションの表示] を選択する](media/logic-apps-track-b2b-messages-omsportal/log-analytics-workspace.png)

1. [概要] ページで、**[追加]** を選択して、**[管理ソリューション]** の一覧を開きます。 その一覧から **[Logic Apps B2B]** を選択します。 

   ![Logic Apps B2B ソリューションを選択する](media/logic-apps-track-b2b-messages-omsportal/add-b2b-solution.png)

   ソリューションが見つからない場合は、ソリューションが表示されるまで一覧の下端にある **[さらに読み込む]** を選択します。

1. **[作成]** を選択し、ソリューションをインストールする Log Analytics ワークスペースを確認してから、もう一度 **[作成]** を選択します。   

   ![Logic Apps B2B の [作成] を選択する](media/logic-apps-track-b2b-messages-omsportal/create-b2b-solution.png)

   既存のワークスペースを使用しない場合は、この時点で新しいワークスペースを作成することもできます。

1. 完了したら、ワークスペースの **[概要]** ページに戻ります。 

   Logic Apps B2B ソリューションが、[概要] ページに表示されます。 
   B2B メッセージが処理されているときに、このページのメッセージ数が更新されます。

<a name="message-status-details"></a>

## <a name="view-b2b-message-information"></a>B2B メッセージ情報を表示する

B2B メッセージが処理された後、それらのメッセージの状態と詳細を **[Logic Apps B2B]** タイルに表示できます。

1. Logic Analytics ワークスペースに移動し、[概要] ページを開きます。 **[Logic Apps B2B]** を選択します。

   ![更新されたメッセージ カウント](media/logic-apps-track-b2b-messages-omsportal/b2b-overview-tile.png)

   > [!NOTE]
   > 既定では、**[Logic Apps B2B]** タイルには 1 日に基づいたデータが表示されます。 データの範囲を異なる間隔に変更するには、ページの上部にある範囲コントロールを選択します。
   > 
   > ![間隔を変更する](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)

1. メッセージの状態ダッシュボードが表示された後、特定のメッセージの種類の詳細を表示できます。ここには、1 日に基づいたデータが表示されます。 **AS2**、**X12**、または **EDIFACT** のタイルを選択します。

   ![メッセージの状態を表示する](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   選択されたタイルのメッセージの一覧が表示されます。 
   メッセージの種類ごとのプロパティの詳細については、次のメッセージのプロパティの説明を参照してください。

   * [AS2 メッセージのプロパティ](#as2-message-properties)
   * [X12 メッセージのプロパティ](#x12-message-properties)
   * [EDIFACT メッセージのプロパティ](#EDIFACT-message-properties)

   たとえば、AS2 メッセージの一覧が表示されるようすを次に示します。

   ![AS2 メッセージを表示する](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. 特定のメッセージの入力と出力を表示またはエクスポートするには、これらのメッセージを選択し、**[ダウンロード]** を選択します。 メッセージが表示されたら、.zip ファイルをローカル コンピューターに保存してから、そのファイルを抽出します。 

   抽出されたフォルダーには、選択されたメッセージごとのフォルダーが含まれています。 
   受信確認を設定した場合、メッセージ フォルダーには受信確認の詳細を含むファイルも含まれています。 
   各メッセージ フォルダーには、少なくとも次のファイルが存在します。 
   
   * 入力ペイロードと出力ペイロードの詳細を含む、人間が判読できるファイル
   * 入力と出力を含むエンコードされたファイル

   メッセージの種類ごとに、次のフォルダーとファイル名の形式を見つけることができます。

   * [AS2 のフォルダーとファイル名の形式](#as2-folder-file-names)
   * [X12 のフォルダーとファイル名の形式](#x12-folder-file-names)
   * [EDIFACT のフォルダーとファイル名の形式](#edifact-folder-file-names)

   ![メッセージ ファイルをダウンロードする](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. 同じ実行 ID を持つすべてのアクションを表示するには、**[ログ検索]** ページで、メッセージの一覧からメッセージを選択します。

   これらのアクションを列で並べ替えたり、特定の結果を検索したりすることができます。

   ![同じ実行 ID を持つアクション](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * 組み込みのクエリで結果を検索するには、**[お気に入り]** を選択します。

   * [フィルターを追加することによってクエリを構築する方法](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)を参照してください。 
   または、[Azure Monitor ログでログ検索を使用してデータを見つける方法](../log-analytics/log-analytics-log-searches.md)に関する詳細を参照してください。

   * 検索ボックス内のクエリを変更するには、フィルターとして使用する列と値でクエリを更新します。

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>AS2、X12、および EDIFACT メッセージのプロパティの説明と名前の形式

メッセージの種類ごとに、ダウンロードされたメッセージ ファイルのプロパティの説明と名前の形式を次に示します。

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>AS2 メッセージのプロパティの説明

AS2 メッセージごとのプロパティの説明を次に示します。

| プロパティ | 説明 |
| --- | --- |
| 送信者 | **[受信設定]** で指定されているゲスト パートナー、または AS2 契約の **[送信設定]** で指定されているホスト パートナー |
| 受信者 | **[受信設定]** で指定されているホスト パートナー、または AS2 契約の **[送信設定]** で指定されているゲスト パートナー |
| ロジック アプリ | AS2 アクションが設定されているロジック アプリ |
| Status | AS2 メッセージの状態 <br>[成功] = 有効な AS2 メッセージを受信または送信しました。 MDN が設定されていません。 <br>[成功] = 有効な AS2 メッセージを受信または送信しました。 MDN が設定および受信されたか、または MDN が送信されました。 <br>[失敗] = 無効な AS2 メッセージを受信しました。 MDN が設定されていません。 <br>[Pending] \(保留中) = 有効な AS2 メッセージを受信または送信しました。 MDN が設定されており、MDN が予測されています。 |
| Ack | MDN メッセージの状態 <br>[承認済み] = 肯定の MDN を受信または送信しました。 <br>[Pending] \(保留中) = MDN の受信または送信を待機しています。 <br>[拒否] = 否定の MDN を受信または送信しました。 <br>[必要なし] = MDN が契約で設定されていません。 |
| 方向 | AS2 メッセージの方向 |
| 関連付け ID | ロジック アプリ内のすべてのトリガーとアクションを関連付ける ID |
| メッセージ ID | AS2 メッセージ ヘッダーからの AS2 メッセージ ID |
| Timestamp | AS2 アクションがメッセージを処理した時間 |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>ダウンロードされたメッセージ ファイルの AS2 の名前の形式

ダウンロードされた AS2 メッセージ フォルダーおよびファイルごとの名前の形式を次に示します。

| フォルダーまたはファイル | 名前の形式 |
| :------------- | :---------- |
| メッセージ フォルダー | [送信者]\_[受信者]\_AS2\_[関連付け ID]\_[メッセージ ID]\_[タイムスタンプ] |
| 入力、出力、および (設定されている場合) 受信確認ファイル | **入力ペイロード**: [送信者]\_[受信者]\_AS2\_[関連付け ID]\_input_payload.txt </p>**出力ペイロード**: [送信者]\_[受信者]\_AS2\_[関連付け ID]\_出力\_payload.txt </p></p>**入力**: [送信者]\_[受信者]\_AS2\_[関連付け ID]\_inputs.txt </p></p>**出力**: [送信者]\_[受信者]\_AS2\_[関連付け ID]\_outputs.txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>X12 メッセージのプロパティの説明

X12 メッセージごとのプロパティの説明を次に示します。

| プロパティ | 説明 |
| --- | --- |
| 送信者 | **[受信設定]** で指定されているゲスト パートナー、または X12 契約の **[送信設定]** で指定されているホスト パートナー |
| 受信者 | **[受信設定]** で指定されているホスト パートナー、または X12 契約の **[送信設定]** で指定されているゲスト パートナー |
| ロジック アプリ | X12 アクションが設定されているロジック アプリ |
| Status | X12 メッセージの状態 <br>[成功] = 有効な X12 メッセージを受信または送信しました。 機能確認が設定されていません。 <br>[成功] = 有効な X12 メッセージを受信または送信しました。 機能確認が設定および受信されたか、または機能確認が送信されました。 <br>[失敗] = 無効な X12 メッセージを受信または送信しました。 <br>[Pending] \(保留中) = 有効な X12 メッセージを受信または送信しました。 機能確認が設定されており、機能確認が予測されています。 |
| Ack | 機能確認 (997) の状態 <br>[承認済み] = 肯定の機能確認を受信または送信しました。 <br>[拒否] = 否定の機能確認を受信または送信しました。 <br>[Pending] \(保留中) = 機能確認を予測していましたが、受信しませんでした。 <br>[Pending] \(保留中) = 機能確認を生成しましたが、パートナーに送信できません。 <br>[必要なし] = 機能確認が設定されていません。 |
| 方向 | X12 メッセージの方向 |
| 関連付け ID | ロジック アプリ内のすべてのトリガーとアクションを関連付ける ID |
| メッセージの種類 | EDI X12 メッセージの種類 |
| ICN | X12 メッセージのインターチェンジ制御番号 |
| TSCN | X12 メッセージのトランザクション セット制御番号 |
| Timestamp | X12 アクションがメッセージを処理した時間 |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>ダウンロードされたメッセージ ファイルの X12 の名前の形式

ダウンロードされた X12 メッセージ フォルダーおよびファイルごとの名前の形式を次に示します。

| フォルダーまたはファイル | 名前の形式 |
| :------------- | :---------- |
| メッセージ フォルダー | [送信者]\_[受信者]\_X12\_[インターチェンジ制御番号]\_[グローバル制御番号]\_[トランザクション セット制御番号]\_[タイムスタンプ] |
| 入力、出力、および (設定されている場合) 受信確認ファイル | **入力ペイロード**: [送信者]\_[受信者]\_X12\_[インターチェンジ制御番号]\_input_payload.txt </p>**出力ペイロード**: [送信者]\_[受信者]\_X12\_[インターチェンジ制御番号]\_出力\_payload.txt </p></p>**入力**: [送信者]\_[受信者]\_X12\_[インターチェンジ制御番号]\_inputs.txt </p></p>**出力**: [送信者]\_[受信者]\_X12\_[インターチェンジ制御番号]\_outputs.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>EDIFACT メッセージのプロパティの説明

EDIFACT メッセージごとのプロパティの説明を次に示します。

| プロパティ | 説明 |
| --- | --- |
| 送信者 | **[受信設定]** で指定されているゲスト パートナー、または EDIFACT 契約の **[送信設定]** で指定されているホスト パートナー |
| 受信者 | **[受信設定]** で指定されているホスト パートナー、または EDIFACT 契約の **[送信設定]** で指定されているゲスト パートナー |
| ロジック アプリ | EDIFACT アクションが設定されているロジック アプリ |
| Status | EDIFACT メッセージの状態 <br>[成功] = 有効な EDIFACT メッセージを受信または送信しました。 機能確認が設定されていません。 <br>[成功] = 有効な EDIFACT メッセージを受信または送信しました。 機能確認が設定および受信されたか、または機能確認が送信されました。 <br>[失敗] = 無効な EDIFACT メッセージを受信または送信しました。 <br>[Pending] \(保留中) = 有効な EDIFACT メッセージを受信または送信しました。 機能確認が設定されており、機能確認が予測されています。 |
| Ack | 機能確認 (997) の状態 <br>[承認済み] = 肯定の機能確認を受信または送信しました。 <br>[拒否] = 否定の機能確認を受信または送信しました。 <br>[Pending] \(保留中) = 機能確認を予測していましたが、受信しませんでした。 <br>[Pending] (保留中) = 機能確認を生成しましたが、パートナーに送信できません。 <br>[必要なし] = 機能確認が設定されていません。 |
| 方向 | EDIFACT メッセージの方向 |
| 関連付け ID | ロジック アプリ内のすべてのトリガーとアクションを関連付ける ID |
| メッセージの種類 | EDIFACT メッセージの種類 |
| ICN | EDIFACT メッセージのインターチェンジ制御番号 |
| TSCN | EDIFACT メッセージのトランザクション セット制御番号 |
| Timestamp | EDIFACT アクションがメッセージを処理した時間 |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>ダウンロードされたメッセージ ファイルの EDIFACT の名前の形式

ダウンロードされた EDIFACT メッセージ フォルダーおよびファイルごとの名前の形式を次に示します。

| フォルダーまたはファイル | 名前の形式 |
| :------------- | :---------- |
| メッセージ フォルダー | [送信者]\_[受信者]\_EDIFACT\_[インターチェンジ制御番号]\_[グローバル制御番号]\_[トランザクション セット制御番号]\_[タイムスタンプ] |
| 入力、出力、および (設定されている場合) 受信確認ファイル | **入力ペイロード**: [送信者]\_[受信者]\_EDIFACT\_[インターチェンジ制御番号]\_input_payload.txt </p>**出力ペイロード**: [送信者]\_[受信者]\_EDIFACT\_[インターチェンジ制御番号]\_出力\_payload.txt </p></p>**入力**: [送信者]\_[受信者]\_EDIFACT\_[インターチェンジ制御番号]\_inputs.txt </p></p>**出力**: [送信者]\_[受信者]\_EDIFACT\_[インターチェンジ制御番号]\_outputs.txt |
|          |             |

## <a name="next-steps"></a>次の手順

* [Azure Monitor ログで B2B メッセージのクエリを実行する](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [AS2 の追跡スキーマ](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 の追跡スキーマ](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [カスタム追跡スキーマ](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
