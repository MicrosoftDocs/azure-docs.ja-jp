---
title: Azure Event Hubs に接続する - Azure Logic Apps
description: Azure Event Hubs および Azure Logic Apps でイベントを管理および監視します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: a59f21478f85f238d91c01faed44d8e49cb15f0a
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310797"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Azure Event Hubs および Azure Logic Apps でイベントを監視および送受信する

この記事では、Azure Event Hubs コネクタを使用してロジック アプリの中から [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) に送信されたイベントを監視および管理する方法を示します。 このようにして、イベント ハブからイベントを確認および送受信するためのタスクとワークフローを自動化するロジック アプリを作成できます。

Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」と[クイック スタートの初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。
コネクタ固有の技術情報については、<a href="https://docs.microsoft.com/connectors/eventhubs/" target="blank">Azure Event Hubs コネクタ リファレンス</a>に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* [Azure Event Hubs 名前空間とイベント ハブ](../event-hubs/event-hubs-create.md)

* イベント ハブにアクセスするためのロジック アプリ。 Azure Event Hubs トリガーを使用してロジック アプリを起動するには、[空のロジック アプリ](../logic-apps/quickstart-create-first-logic-app-workflow.md)が必要です。

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>アクセス許可を確認し、接続文字列を取得する

ロジック アプリがイベント ハブにアクセスできるようにするには、アクセス許可を確認し、Event Hubs 名前空間の接続文字列を取得します。

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a> にサインインします。

2. 特定のイベント ハブではなく、Event Hubs "*名前空間*" に移動します。 名前空間ページで **[設定]** の **[共有アクセス ポリシー]** を選択します。 **[要求]** で、その名前空間に対して**管理**アクセス許可が付与されていることを確認します。

   ![イベント ハブ名前空間のアクセス許可を管理する](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. 後で手動で接続情報を入力する場合は、Event Hubs 名前空間の接続文字列を取得します。

   1. **[ポリシー]** で、**[RootManageSharedAccessKey]** を選択します。

   2. ご自身の主キーの接続文字列を検索します。 コピー ボタンを選択し、後で使用できるように接続文字列を保存します。

      ![Event Hubs 名前空間の接続文字列をコピーする](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > 接続文字列が Event Hubs 名前空間に関連付けられているか、特定のイベント ハブに関連付けられているかを確認するには、接続文字列に `EntityPath` パラメーターがないかどうかを確認します。 このパラメーターがある場合、接続文字列は特定のイベント ハブ "エンティティ" を対象としており、ロジック アプリで使用する正しい文字列ではありません。

4. 続いて、[Event Hubs トリガーを追加](#add-trigger)するか、[Event Hubs アクションを追加](#add-action)します。

<a name="add-trigger"></a>

## <a name="add-an-event-hubs-trigger"></a>Event Hubs トリガーを追加する

Azure Logic Apps では、すべてのロジック アプリは、必ず[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)から起動されます。トリガーは、特定のイベントが起こるか特定の条件が満たされたときに発生します。 トリガーが発生するたびに、Logic Apps エンジンによってロジック アプリ インスタンスが作成され、アプリのワークフローが開始されます。

この例では、新しいイベントがご自身のイベント ハブに送信されたときに、ロジック アプリ ワークフローを開始する方法を示します。

1. Azure Portal または Visual Studio で、Logic Apps デザイナーを開いて、空のロジック アプリを作成します。 この例では、Azure Portal を使用します。

2. 検索ボックスに、フィルターとして「イベント ハブ」と入力します。 トリガーの一覧から、目的のトリガーを選択します。

   この例では、次のトリガーを使用します。

   **[Event Hubs - When events are available in Event Hub]** \(Event Hubs - イベント ハブでイベントを使用できるとき\)

   ![トリガーの選択](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

3. 接続の詳細の入力を求められたら、[Event Hubs 接続をすぐに作成](#create-connection)します。 接続が既に存在する場合は、トリガーに必要な情報を指定します。

   1. **[イベント ハブの名前]** の一覧から、監視するイベント ハブを選択します。

      ![イベント ハブまたはコンシューマー グループを指定する](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

   2. トリガーがイベント ハブをチェックする間隔と頻度を選択します。

   3. 必要に応じていくつかの高度なトリガー オプションを選択するには、**[詳細オプションを表示する]** を選択します。

      ![トリガーの詳細オプション](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-advanced.png)

      | プロパティ | 詳細 | 
      |----------|---------| 
      | コンテンツの種類  | イベントのコンテンツの種類を選択します。 既定は "application/octet-stream" です。 |
      | コンテンツ スキーマ | Event Hub から読み取られるイベントのコンテンツ スキーマを JSON で入力します。 |
      | コンシューマー グループ名 | イベントを読み取るために、イベント ハブの[コンシューマー グループ名](../event-hubs/event-hubs-features.md#consumer-groups)を入力します。 指定されていない場合は、既定のコンシューマー グループが使用されます。 |
      | 最小パーティション キー | 読み取る最小の[パーティション](../event-hubs/event-hubs-features.md#partitions) ID を入力します。 既定では、すべてのパーティションが読み取られます。 |
      | 最大パーティション キー | 読み取る最大の[パーティション](../event-hubs/event-hubs-features.md#partitions) ID を入力します。 既定では、すべてのパーティションが読み取られます。 |
      | 最大イベント数 | イベントの最大数の値を入力します。 トリガーは、1 からこのプロパティによって指定されたイベントの数までの値を返します。 |
      |||

4. 操作が完了したら、デザイナーのツールバーで、**[保存]** を選択します。

5. トリガーの結果を使用して実行するタスクの 1 つまたは複数のアクションをロジック アプリに追加する操作に進みます。

> [!NOTE]
> イベント ハブ トリガーはすべて "*長いポーリング*" のトリガーです。起動するときにすべてのイベントを処理し、ご自身のイベント ハブにさらにイベントが表示されるまで 30 秒間待機します。
> 30 秒以内にイベントを受信しなかった場合、トリガーの実行はスキップされます。 そうでない場合、トリガーはご自身のイベント ハブが空になるまでイベントの読み取りを続けます。
> 次のトリガーのポーリングは、トリガーのプロパティで指定する繰り返し間隔に基づいて発生します。

<a name="add-action"></a>

## <a name="add-an-event-hubs-action"></a>Event Hubs アクションを追加する

Azure Logic Apps では、[アクション](../logic-apps/logic-apps-overview.md#logic-app-concepts)とは、トリガーまたは別のアクションに続くワークフロー内のステップです。 この例では、ロジック アプリは、イベント ハブで新しいイベントをチェックする Event Hubs トリガーを使用して起動されます。

1. Azure Portal または Visual Studio で、Logic Apps デザイナーでロジック アプリを開きます。 この例では、Azure Portal を使用します。

2. トリガーまたはアクションで、**[新しいステップ]** > **[アクションの追加]** を選択します。

   既存のステップの間にアクションを追加するには、接続矢印の上にマウスを移動します。 
   表示されるプラス記号 (**+**) を選択し、**[アクションの追加]** を選択します。

3. 検索ボックスに、フィルターとして「イベント ハブ」と入力します。
アクションの一覧から、目的のアクションを選択します。

   この例では、次のアクションを選択します。**[Event Hubs - 送信イベント]**

   ![[Event Hubs - 送信イベント] を選択する](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

4. 接続の詳細の入力を求められたら、[Event Hubs 接続をすぐに作成](#create-connection)します。 接続が既に存在する場合は、アクションに必要な情報を指定します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | イベント ハブ名 | はい | イベントの送信先イベント ハブを選択します |
   | イベント コンテンツ | いいえ  | 送信するイベントのコンテンツ |
   | Properties | いいえ  | 送信するアプリのプロパティと値 |
   ||||

   例: 

   ![イベント ハブ名を選択し、イベントの内容を入力する](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

5. 操作が完了したら、デザイナーのツールバーで、**[保存]** を選択します。

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>ご自身のイベント ハブに接続する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. 接続情報の入力を求められたら、次の詳細を入力します。

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | 接続名 | はい | <*connection-name*> | 作成する接続の名前 |
   | Event Hubs 名前空間 | はい | <*event-hubs-namespace*> | 使用する場合は、Event Hubs 名前空間を選択します。 |
   |||||  

   例: 

   ![イベント ハブ接続を作成する](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   手動で接続文字列を入力する場合は、**[接続情報を手動で入力する]** を選択します。 
   [接続文字列を検索する方法](#permissions-connection-string)に関するセクションを参照してください。

2. 使用する Event Hubs ポリシーを選択します (選択されていない場合)。 **[作成]** を選択します。

   ![イベント ハブ接続を作成する (パート 2)](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. 接続を作成したら、[Event Hubs トリガーを追加](#add-trigger)するか、[Event Hubs アクションを追加](#add-action)します。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの Swagger ファイルによって記述される、トリガー、アクション、制限などの技術的詳細については、[コネクタのリファレンス ページ](/connectors/eventhubs/)を参照してください。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。