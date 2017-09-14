---
title: "Operations Management Suite で B2B メッセージを追跡する - Azure Logic Apps | Microsoft Docs"
description: "Azure Log Analytics を使用して Operations Management Suite (OMS) で統合アカウントおよびロジック アプリの B2B 通信を追跡する"
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
ms.openlocfilehash: 3ef7a4054be80547b0d91ad1f13777d915005f8b
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="track-b2b-communication-in-the-microsoft-operations-management-suite-oms"></a>Microsoft Operations Management Suite (OMS) で B2B 通信を追跡する

統合アカウント経由で 2 つの実行中のビジネス プロセスまたはアプリケーション間で B2B 通信を設定した後、これらのエンティティは互いにメッセージを交換できます。 これらのメッセージが正しく処理されているかどうかを確認するには、[Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) で [Azure Log Analytics](../log-analytics/log-analytics-overview.md) を使用して AS2、X12、および EDIFACT メッセージを追跡できます。 たとえば、メッセージを追跡するために次の Web ベースの追跡機能を使用できます。

* メッセージの数と状態
* 受信確認の状態
* メッセージと受信確認の関連付け
* エラーの詳細な説明
* 検索機能

## <a name="requirements"></a>必要条件

* 診断ログが設定されているロジック アプリ。 [ロジック アプリを作成する方法](logic-apps-create-a-logic-app.md)および[そのロジック アプリのログを設定する方法](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)を参照してください。

* 監視とログが設定されている統合アカウント。 [統合アカウントを作成する方法](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)および[そのアカウントの監視とログを設定する方法](../logic-apps/logic-apps-monitor-b2b-message.md)を参照してください。

* まだ実行していない場合は、OMS で [Log Analytics に診断データを発行](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)します。

> [!NOTE]
> 前の要件を満たした後、[Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) 内にワークスペースを用意する必要があります。 OMS で B2B 通信を追跡するための同じ OMS ワークスペースを使用する必要があります。 
>  
> OMS ワークスペースがない場合は、[OMS ワークスペースを作成する方法](../log-analytics/log-analytics-get-started.md)を参照してください。

## <a name="add-the-logic-apps-b2b-solution-to-the-operations-management-suite-oms"></a>Logic Apps B2B ソリューションを Operations Management Suite (OMS) に追加する

OMS でロジック アプリの B2B メッセージを追跡するには、OMS ポータルに **Logic Apps B2B** ソリューションを追加する必要があります。 [OMS へのソリューションの追加](../log-analytics/log-analytics-get-started.md)に関する詳細を参照してください。

1. [Azure Portal](https://portal.azure.com) で、**[その他のサービス]** を選択します。 次に示すように、"ログ分析" を検索してから、**[Log Analytics]** を選択します。

   ![Log Analytics を見つける](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)

2. **[Log Analytics]** で、OMS ワークスペースを見つけて選択します。 

   ![OMS ワークスペースを選択する](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. **[管理]** で、**[OMS ポータル]** を選択します。

   ![OMS ポータルを選択する](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. OMS ホーム ページが開いた後、**[ソリューション ギャラリー]** を選択します。    

   ![[ソリューション ギャラリー] を選択する](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. **[All solutions] \(すべてのソリューション)** で、**[Logic Apps B2B]** を見つけて選択します。     

   ![[Logic Apps B2B] を選択する](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. **[Logic Apps B2B]** で、**[追加]** を選択します。

   ![[追加] の選択](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

   OMS ホーム ページに、**[Logic Apps B2B メッセージ]** のタイルが表示されるようになります。 
   このタイルは、B2B メッセージが処理されたらメッセージ カウントを更新します。

   ![OMS ホーム ページの [Logic Apps B2B メッセージ] タイル](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

<a name="message-status-details"></a>

## <a name="track-message-status-and-details-in-the-operations-management-suite"></a>Operations Management Suite でメッセージの状態と詳細を追跡する

1. B2B メッセージが処理された後、それらのメッセージの状態と詳細を表示できます。 OMS ホーム ページで、**[Logic Apps B2B メッセージ]** タイルを選択します。

   ![更新されたメッセージ カウント](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

   > [!NOTE]
   > 既定では、**[Logic Apps B2B メッセージ]** タイルには 1 日に基づいたデータが表示されます。 データの範囲を異なる間隔に変更するには、OMS ページの上部にある範囲コントロールを選択します。
   > 
   > ![データの範囲を変更する](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)
   >

2. メッセージの状態ダッシュボードが表示された後、特定のメッセージの種類の詳細を表示できます。ここには、1 日に基づいたデータが表示されます。 **AS2**、**X12**、または **EDIFACT** のタイルを選択します。

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
   または、[Log Analytics でログ検索を使用してデータを見つける方法](../log-analytics/log-analytics-log-searches.md)に関する詳細を参照してください。

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
| 状態 | AS2 メッセージの状態 <br>[成功] = 有効な AS2 メッセージを受信または送信しました。 MDN が設定されていません。 <br>[成功] = 有効な AS2 メッセージを受信または送信しました。 MDN が設定および受信されたか、または MDN が送信されました。 <br>[失敗] = 無効な AS2 メッセージを受信しました。 MDN が設定されていません。 <br>[Pending] \(保留中) = 有効な AS2 メッセージを受信または送信しました。 MDN が設定されており、MDN が予測されています。 |
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
| 状態 | X12 メッセージの状態 <br>[成功] = 有効な X12 メッセージを受信または送信しました。 機能確認が設定されていません。 <br>[成功] = 有効な X12 メッセージを受信または送信しました。 機能確認が設定および受信されたか、または機能確認が送信されました。 <br>[失敗] = 無効な X12 メッセージを受信または送信しました。 <br>[Pending] \(保留中) = 有効な X12 メッセージを受信または送信しました。 機能確認が設定されており、機能確認が予測されています。 |
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
| 状態 | EDIFACT メッセージの状態 <br>[成功] = 有効な EDIFACT メッセージを受信または送信しました。 機能確認が設定されていません。 <br>[成功] = 有効な EDIFACT メッセージを受信または送信しました。 機能確認が設定および受信されたか、または機能確認が送信されました。 <br>[失敗] = 無効な EDIFACT メッセージを受信または送信しました。 <br>[Pending] \(保留中) = 有効な EDIFACT メッセージを受信または送信しました。 機能確認が設定されており、機能確認が予測されています。 |
| Ack | 機能確認 (997) の状態 <br>[承認済み] = 肯定の機能確認を受信または送信しました。 <br>[拒否] = 否定の機能確認を受信または送信しました。 <br>[Pending] \(保留中) = 機能確認を予測していましたが、受信しませんでした。 <br>[Pending] \(保留中) = 機能確認を生成しましたが、パートナーに送信できません。 <br>[必要なし] = 機能確認が設定されていません。 |
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

## <a name="next-steps"></a>次のステップ

* [Operations Management Suite で B2B メッセージのクエリを行う](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [AS2 の追跡スキーマ](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 の追跡スキーマ](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [カスタム追跡スキーマ](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
