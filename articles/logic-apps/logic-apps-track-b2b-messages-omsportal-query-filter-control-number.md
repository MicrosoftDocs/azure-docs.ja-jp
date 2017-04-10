---
title: "クエリを使用して AS2、X12、EDIFACT メッセージを追跡する - Azure Logic Apps | Microsoft Docs"
description: "クエリを使用して、Operations Management Suite ポータルで企業間メッセージを追跡します"
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
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 67313e4ff780e6c64dfc3d767ea49167c2a390f4
ms.openlocfilehash: 701a3bbadb340c9390b486a5295eced056db70a0


---
# <a name="track-b2b-messages-in-the-operations-management-suite-portal-by-using-a-query"></a>クエリを使用して、Operations Management Suite ポータルで B2B メッセージを追跡する
Operations Management Suite ポータルで企業間 (B2B) メッセージを追跡するには、データをフィルター処理して特定のインターチェンジ制御番号を取得するためのクエリを作成します。

## <a name="prereqs"></a>前提条件

デバッグを実行したり、詳細な診断情報を取得するには、X12 コネクタを使用した[ロジック アプリ](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts)用の[統合アカウント](logic-apps-monitor-b2b-message.md)で診断を有効にします。 その後、Operations Management Suite ポータルに[診断データを発行する](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)ための手順を実行します。

## <a name="search-for-an-interchange-control-number"></a>インターチェンジ制御番号の検索

1. スタート ページで、**[ログ検索]** を選択します。  
![ログ検索の選択](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

2. 検索ボックスに、「**Type="AzureDiagnostics"**」と入力します。 データをフィルター処理するには、**[追加]** を選択します。  
![クエリの選択](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

3. 「**interchange**」と入力し、**event_record_messageProperties_interchangeControlNumber_s** を選択して、**[追加]** をクリックします。  
![フィルターの追加](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query2.png)

4. フィルターで絞り込む制御番号を選択し、**[適用]** をクリックします。  
![制御番号の検索](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query3.png)

5. 選択した制御番号を検索するために作成したクエリを探します。   
![クエリを探す](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query4.png)

6. クエリの名前を入力し、クエリを保存します。   
![クエリの保存](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query5.png)

7. クエリを表示し、そのクエリを今後の検索で使用できるようにするには、**[お気に入り]** を選択します。  
![クエリの表示](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query7.png)

8. 新しいインターチェンジ制御番号を検索できるように、クエリを更新することもできます。  
![クエリの更新](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query6.png)


## <a name="next-steps"></a>次のステップ
* [カスタム追跡スキーマ](logic-apps-track-integration-account-custom-tracking-schema.md)についての詳細情報。   
* [AS2 追跡スキーマ](logic-apps-track-integration-account-as2-tracking-schemas.md)についての詳細情報。    
* [X12 追跡スキーマ](logic-apps-track-integration-account-x12-tracking-schema.md)についての詳細情報。  
* [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) についての詳細情報。



<!--HONumber=Jan17_HO5-->


