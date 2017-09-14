---
title: "Azure Event Hubs で Azure Logic Apps 用イベント モニターを設定する | Microsoft Docs"
description: "データ ストリームを監視して、Azure Event Hubs で Azure Logic Apps のイベントを送受信します"
services: logic-apps
keywords: "データ ストリーム, イベント モニター, イベント ハブ"
author: ecfan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2017
ms.author: estfan; LADocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 2ca27fb8269d1796fb1181fc4d0a8744a592d548
ms.contentlocale: ja-jp
ms.lasthandoff: 05/26/2017

---

# <a name="monitor-receive-and-send-events-with-the-event-hubs-connector"></a>Event Hubs コネクタでのイベントの監視および送受信

ロジック アプリがイベントを検出および送受信できるようにイベント モニターを設定するには、ロジック アプリから [Azure Event Hub](https://azure.microsoft.com/services/event-hubs) に接続します。 詳細については、 [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)に関する情報を参照してください。

## <a name="requirements"></a>必要条件

* Azure に[Event Hubs 名前空間とイベント ハブ](../event-hubs/event-hubs-create.md)を用意する必要があります。 [Event Hubs 名前空間とイベント ハブを作成する方法](../event-hubs/event-hubs-create.md)に関するページをご覧ください。 

* ロジック アプリで[コネクタ](https://docs.microsoft.com/azure/connectors/apis-list)を使用するには、ロジック アプリを最初に作成する必要があります。 [ロジック アプリの作成方法](../logic-apps/logic-apps-create-a-logic-app.md)に関するページをご覧ください。

<a name="permissions-connection-string"></a>
## <a name="check-event-hubs-namespace-permissions-and-find-the-connection-string"></a>Event Hubs 名前空間を確認して、接続文字列を検索する

ロジック アプリがすべてのサービスにアクセスできるようにするには、ロジック アプリとサービスの間に "[*接続*](./connectors-overview.md)" を作成する必要があります (まだ作成していない場合)。 この接続により、ロジック アプリによるデータ アクセスが承認されます。
ロジック アプリがイベント ハブにアクセスできるようにするには、Event Hubs 名前空間に対する**管理**アクセス許可と接続文字列が必要です。

アクセス許可を確認し、接続文字列を取得するには、以下の操作を行います。

1.  [Azure Portal](https://portal.azure.com "Azure Portal") にサインインします。 

2.  特定のイベント ハブではなく、Event Hubs "*名前空間*" に移動します。 名前空間ブレードで **[設定]** の **[共有アクセス ポリシー]** を選択します。 **[要求]** で、その名前空間に対して**管理**アクセス許可が付与されていることを確認します。

    ![イベント ハブ名前空間のアクセス許可を管理する](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3.  Event Hubs 名前空間の接続文字列をコピーするには、**[RootManageSharedAccessKey]** を選択します。 主キー接続文字列の横にあるコピー ボタンを選択します。

    ![Event Hubs 名前空間の接続文字列をコピーする](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

    > [!TIP]
    > 接続文字列が Event Hubs 名前空間に関連付けられているか、特定のイベント ハブに関連付けられているかを確認するには、`EntityPath` パラメーターの接続文字列を確認します。 このパラメーターがある場合、接続文字列は特定のイベント ハブ "エンティティ" を対象としており、ロジック アプリで使用する正しい文字列ではありません。

4.  これで、Event Hubs のトリガーやアクションをロジック アプリに追加した後、資格情報の入力を求められたときに、Event Hubs 名前空間に接続できます。 接続に名前を付け、コピーした接続文字列を入力して、**[作成]** を選択します。

    ![Event Hubs 名前空間の接続文字列を入力する](./media/connectors-create-api-azure-event-hubs/event-hubs-connection.png)

    接続を作成したら、Event Hubs のトリガーやアクションに接続名が表示されます。 
    これで、ロジック アプリで他の手順に進むことができます。

    ![作成された Event Hubs 名前空間の接続文字列](./media/connectors-create-api-azure-event-hubs/event-hubs-connection-created.png)

## <a name="start-workflow-when-your-event-hub-receives-new-events"></a>イベント ハブが新しいイベントを受け取るときにワークフローを開始する

"[*トリガー*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)" は、ロジック アプリのワークフローを開始するイベントです。 新しいイベントがイベント ハブに送信されたときにワークフローを開始するには、次の手順に従って、このイベントを検出するトリガーを追加します。

1.  [Azure Portal](https://portal.azure.com "Azure Portal") で、既存のロジック アプリに移動するか、空のロジック アプリを作成します。

2.  ロジック アプリ デザイナーの検索ボックスに、フィルターの「`event hubs`」を入力します。 **[When events are available in Event Hub (イベント ハブでイベントを使用できるとき)]** トリガーを選択します

    !["イベント ハブが新しいイベントを受け取るとき" のトリガーを選択する](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

    Event Hubs 名前空間への接続がまだない場合は、この接続を作成するように求められます。 接続に名前を付け、Event Hubs 名前空間の接続文字列を入力します。 
    必要に応じて、[接続文字列を検索する方法](#permissions-connection-string)に関するセクションをご覧ください。

    ![Event Hubs 名前空間の接続文字列を入力する](./media/connectors-create-api-azure-event-hubs/event-hubs-connection.png)

    接続を作成したら、**[When an event in available in an Event Hub (イベント ハブでイベントを使用できるとき)]** トリガーの設定が表示されます。

    !["イベント ハブが新しいイベントを受け取るとき" の設定をトリガーする](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

3.  監視するイベント ハブの名前を入力または選択します。 イベントを確認する頻度と間隔を選択します。

    > [!TIP]
    > オプションでイベントを読み取るためのコンシューマー グループを選択するには、**[詳細オプションのの表示]** を選択します。 

    ![イベント ハブまたはコンシューマー グループを指定する](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-details.png)

    トリガーの設定が完了し、ロジック アプリのワークフローを開始できるようになりました。 
    ロジック アプリは、指定したイベント ハブを、設定したスケジュールに基づいて確認します。 
    アプリがイベント ハブで新しいイベントを検出すると、トリガーによって、ロジック アプリの他のアクションまたはトリガーが実行されます。

## <a name="send-events-to-your-event-hub-from-your-logic-app"></a>ロジック アプリからイベント ハブにイベントを送信する

"[*アクション*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)" は、ロジック アプリのワークフローによって実行されるタスクです。 ロジック アプリにトリガーを追加した後は、そのトリガーによって生成されたデータに対する操作を実行するアクションを追加できます。 ロジック アプリからイベント ハブにイベントを送信するには、以下の操作を行います。

1.  ロジック アプリ デザイナーのロジック アプリ トリガーで、**[新しいステップ]** > **[アクションの追加]** の順に選択します。

    ![[新しいステップ]、[アクションの追加] の順に選択する](./media/connectors-create-api-azure-event-hubs/add-action.png)

    これで、実行するアクションを検索して選択できます。 
    すべてのアクションを選択できますが、この例では、Event Hubs アクションでイベントを送信します。

2.  検索ボックスに、フィルターとして「`event hubs`」と入力します。
**[送信イベント]** アクションを選択します

    ![[Event Hubs - 送信イベント] アクションを選択する](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

3.  イベントを送信するイベント ハブの名前など、イベントに関する必要な情報を入力します。 必要に応じて、そのイベントのコンテンツなど、イベントに関するその他の詳細情報も入力します。

    > [!TIP]
    > イベントを送信するイベント ハブのパーティションをオプションで指定するには、**[詳細オプションの表示]** を選択します。 

    ![イベント ハブの名前とオプションのイベントの詳細を入力する](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

6.  変更を保存します。

    ![ロジック アプリを保存する](./media/connectors-create-api-azure-event-hubs/save-logic-app.png)

    これで、ロジック アプリからイベントを送信するアクションの設定が完了しました。 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/eventhubs/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。 

## <a name="get-help"></a>問い合わせ

[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)では、質問の投稿や質問への回答を行うことができるほか、他の Azure Logic Apps ユーザーがどのようなことを行っているかがわかります。

[Logic Apps ユーザー フィードバック サイト](http://aka.ms/logicapps-wish)でアイデアへの投票やアイデアの投稿を行って、Logic Apps とコネクタの改善にご協力ください。

## <a name="next-steps"></a>次のステップ

*  [Azure Logic Apps の他のコネクタを見つける](./apis-list.md)
