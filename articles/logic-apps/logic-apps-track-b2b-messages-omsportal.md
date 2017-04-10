---
title: "Operations Management Suite ポータルで B2B メッセージを追跡する - Azure | Microsoft Docs"
description: "Operations Management Suite ポータルで B2B メッセージを追跡する方法"
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
ms.date: 11/13/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 2aa32d692da81dc34fafbc9cb6ea72a806f31e5c
ms.openlocfilehash: a4068c70a01f80086ff3fb55f8e101c7dce95dea
ms.lasthandoff: 02/27/2017


---
# <a name="track-b2b-messages-in-the-operations-management-suite-portal"></a>Operations Management Suite ポータルで B2B メッセージを追跡する
B2B 通信では、実行中の&2; つのビジネス プロセスまたはアプリケーション間でメッセージ交換が行われます。 Operations Management Suite ポータルで次の Web ベースの追跡機能を使用して、メッセージが正しく処理されたかどうかを確認します。

* メッセージの数と状態
* 受信確認の状態
* メッセージと受信確認の関連付け
* エラーの詳細な説明
* 検索機能

## <a name="prerequisites"></a>前提条件
* Azure アカウント。 [無料アカウント](https://azure.microsoft.com/free)を作成できます。
* 統合アカウント。 [統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)を作成し、ログ記録を設定できます。 ログ記録を設定するには、[B2B メッセージの監視](logic-apps-monitor-b2b-message.md)に関するページを参照してください。
* ロジック アプリ。 [ロジック アプリ](../logic-apps/logic-apps-create-a-logic-app.md)を作成し、ログ記録を設定できます。 ログ記録を設定するには、[Azure 診断とアラート](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts)に関するページを参照してください。

## <a name="add-logic-apps-b2b-solution-to-the-operations-management-suite-portal"></a>Logic Apps B2B ソリューションを Operations Management Suite ポータルに追加する

1. Azure Portal で **［その他のサービス］** を選択し、**Log Analytics** を検索して選択します。   
![Log Analytics の検索](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)  

2. 自分の **Log Analytics** を選択します。  
![Log Analytics の選択](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. **［OMS ポータル］** を選択します。 Operations Management Suite ポータルのホーム ページが表示されます。   
![Operations Management Suite ポータルを参照する](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. **[ソリューション ギャラリー]** を選択します。    
![[ソリューション ギャラリー]](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png) を選択します。

5. **[Logic Apps B2B]** を選択します。     
![[Logic Apps B2B] の選択](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. **[追加]** を選択して、**[Logic Apps B2B メッセージ]** をホーム ページに追加します。  
![[追加] の選択](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

7. **［Logic Apps B2B メッセ―ジ］** がホームページに表示されます。   
![ホーム ページの選択](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

## <a name="track-data-in-the-operations-management-suite-portal"></a>Operations Management Suite ポータルでデータを追跡する

1. メッセージが処理されると、更新されたメッセージ数が表示されます。   
![更新されたメッセージ](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

2. AS2 および X12 のメッセージの状態を表示するには、ホーム ページで **[Logic Apps B2B メッセージ]** を選択します。  データは、1 日のものです。
![[Logic Apps B2B メッセージ] の選択](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

3. メッセージの一覧に移動するには、状態別の X12 または AS2 メッセージを選択します。 次のスクリーン ショットは、AS2 メッセージの状態を示しています。 AS2 および X12 メッセージの状態のプロパティについては、「メッセージの一覧のプロパティの説明」で説明します。  
![AS2 メッセージの状態の選択](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)
4. ログ検索に移動するには、AS2 または X12 メッセージの一覧で行を選択します。  ログ検索では、同じ実行 ID を持つすべてのアクションが一覧表示されます。
![メッセージの状態の選択](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

## <a name="message-list-property-descriptions"></a>メッセージの一覧のプロパティの説明

#### <a name="as2-message-list-property-descriptions"></a>AS2 メッセージの一覧のプロパティの説明

| プロパティ | 説明 |
| --- | --- |
| 送信者 | AS2 契約の受信設定で構成されているゲスト パートナーまたは送信設定で構成されているホスト パートナー。 |
| 受信者 | AS2 契約の受信設定で構成されているホスト パートナーまたは送信設定で構成されているゲスト パートナー。 |
| ロジック アプリ | AS2 アクションが構成されているロジック アプリ。 |
| 状態 | AS2 メッセージの状態 <br>成功 = 適切な AS2 メッセージ受信済みまたは送信済み、MDN 未構成 <br>成功 = 適切な AS2 メッセージ受信済みまたは送信済み、MDN 構成済みで受信済み、または MDN 送信済み <br>失敗 = 無効な AS2 メッセージ受信済み、MDN 未構成 <br>保留中 = 適切な AS2 メッセージ受信済みまたは送信済み、MDN 構成済みで機能確認待ち |
| Ack | MDN メッセージの状態 <br>承認済み = 肯定の MDN 受信済みまたは送信済み <br>保留中 = MDN の送受信待ち <br>拒否 = 否定の MDN 受信済みまたは送信済み <br>必要なし = 契約で MDN 未構成 |
| 方向 | AS2 メッセージの方向。 |
| 関連付け ID | ロジック アプリ内ですべてのトリガーとアクションを関連付けるための ID。 |
| メッセージ ID |  AS2 メッセージ ID (AS2 メッセージのヘッダーから取得)。 |
| タイムスタンプ | AS2 アクションがメッセージを処理した時刻。 |

#### <a name="x12-message-list-property-descriptions"></a>X12 メッセージの一覧のプロパティの説明

| プロパティ | 説明 |
| --- | --- |
| 送信者 | AS2 契約の受信設定で構成されているゲスト パートナーまたは送信設定で構成されているホスト パートナー。 |
| 受信者 | AS2 契約の受信設定で構成されているホスト パートナーまたは送信設定で構成されているゲスト パートナー。 |
| ロジック アプリ | AS2 アクションが構成されているロジック アプリ。 |
| 状態 | X12 メッセージの状態 <br>成功 = 適切な X12 メッセージ受信済みまたは送信済み、機能確認未構成 <br>成功 = 適切な X12 メッセージ受信済みまたは送信済み、機能確認構成済みで受信済み 、または機能確認送信済み <br>失敗 = 無効な X12 メッセージ受信済みまたは送信済み <br>保留中 = 適切な X12 メッセージ受信済みまたは送信済み、機能確認構成済みで機能確認待ち。 |
| Ack | 機能確認 (997) の状態 <br>承認済み = 肯定の機能確認受信済みまたは送信済み <br>拒否 = 否定の機能確認受信済みまたは送信済み <br>保留中 = 機能確認待ちだが未受信 <br>保留中 = 機能確認生成済みだが、パートナーに送信できなかった <br>必要なし = 機能確認未構成 |
| 方向 | X12 メッセージの方向。 |
| 関連付け ID | ロジック アプリ内ですべてのトリガーとアクションをアクションを関連付けるための ID。 |
| メッセージの種類 |  EDI X12 メッセージの種類。 |
| ICN | X12 メッセージのインターチェンジ制御番号。 |
| TSCN | X12 メッセージのトランザクション セット制御番号。 |
| タイムスタンプ | X12 アクションがメッセージを処理した時刻。 |

## <a name="queries-in-the-operations-management-suite-portal"></a>Operations Management Suite ポータルでのクエリ

[検索] ページで、クエリを作成することができます。 検索時に、ファセット コントロールを使用して結果をフィルター処理できます。

### <a name="create-a-query"></a>クエリを作成する

1. ログ検索で、クエリを記述して **[保存]** を選択します。 **[検索条件を保存]** が表示されます。 クエリを記述するには、[Operations Management Suite ポータルでのクエリを使用した B2B メッセージの追跡](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)に関するページを参照してください。
![ホーム ページの選択](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery.png)

2. **［検索条件を保存］** で、**［名前］** と **［カテゴリ］** を追加し、**［保存］** を選択します。   
![ホーム ページの選択](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery1.png)

3. クエリを表示するには、**[お気に入り]** を選択します。    
![ホーム ページの選択](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery3.png)

    ![ホーム ページの選択](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery4.png)

### <a name="use-a-saved-query"></a>保存されたクエリを使用する

* ログ検索で、**[お気に入り]** を選択し、保存されたクエリを表示します。  クエリの結果を表示するには、クエリを選択します。
![ホーム ページの選択](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery5.png)


## <a name="next-steps"></a>次のステップ
[カスタム追跡スキーマ](logic-apps-track-integration-account-custom-tracking-schema.md "Learn about Custom Tracking Schema")   
[AS2 の追跡スキーマ](logic-apps-track-integration-account-as2-tracking-schemas.md "Learn about AS2 Tracking Schema")    
[X12 の追跡スキーマ](logic-apps-track-integration-account-x12-tracking-schema.md "Learn about X12 Tracking Schema")  
[Enterprise Integration Pack についての詳細情報](../logic-apps/logic-apps-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")

