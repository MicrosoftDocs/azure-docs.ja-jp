---
title: Azure Service Bus を使用したメッセージ交換
description: Azure Logic Apps で Azure Service Bus を使用してメッセージを送受信する自動化されたタスクとワークフローを作成する
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm, azla
ms.topic: conceptual
ms.date: 02/10/2021
tags: connectors
ms.openlocfilehash: 98d2ee8a85d25065c0021841a9b99a6d616a35d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367420"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Azure Logic Apps と Azure Service Bus を使用してクラウド内でメッセージを交換する

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) と [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) コネクタを使用すると、販売および発注書、仕訳帳、組織のアプリケーション間の在庫移動などのデータを転送する、自動化されたタスクとワークフローを作成することができます。 コネクタは、メッセージを監視、送信、および管理するだけでなく、たとえば次のような、キュー、セッション、トピック、サブスクリプションなどに対するアクションも実行します。

* キュー、トピック、およびトピック サブスクリプションにメッセージが届く (オートコンプリート) またはメッセージを受信する (ピークロック) のを監視します。
* メッセージを送信します。
* トピック サブスクリプションを作成および削除します。
* キューおよびトピック サブスクリプション内のメッセージを管理します。たとえば、取得、遅延の取得、完了、延期、破棄、配信不能などです。
* キューおよびトピック サブスクリプション内のメッセージとセッションに対するロックを更新します。
* キューとトピック内のセッションを閉じる。

Service Bus から応答を取得し、その出力をロジック アプリ内の他のアクションが使用できるようにするトリガーを使用できます。 他のアクションに Service Bus アクションからの出力を使用させることもできます。 Service Bus と Logic Apps を初めて使用する場合は、「[Azure Service Bus とは](../service-bus-messaging/service-bus-messaging-overview.md)」および「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* Service Bus 名前空間と、キューなどのメッセージング エンティティ。 項目とロジック アプリでは、同じ Azure サブスクリプションを使用する必要があります。 これらの項目がない場合は、[Service Bus 名前空間とキューの作成](../service-bus-messaging/service-bus-create-namespace-portal.md)方法を学習してください。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* ロジック アプリでは、Service Bus 名前空間とメッセージング エンティティを使用します。 ロジック アプリと Service Bus では、同じ Azure サブスクリプションを使用する必要があります。 ワークフローを Service Bus トリガーで開始するには、[空のロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。 ワークフローで Service Bus アクションを使用するには、[Recurrence](../connectors/connectors-native-recurrence.md) トリガーなど、別のトリガーでロジック アプリを開始します。

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>アクセス許可を確認する

ロジック アプリが Service Bus 名前空間にアクセスするためのアクセス許可を持っていることを確認します。

1. [Azure portal](https://portal.azure.com) で、Azure アカウントを使ってサインインします。

1. Service Bus "*名前空間*" に移動します。 名前空間ページで **[設定]** の **[共有アクセス ポリシー]** を選択します。 **[要求]** で、その名前空間に対して **管理** アクセス許可が付与されていることを確認します。

   ![Service Bus 名前空間のアクセス許可を管理する](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Service Bus 名前空間の接続文字列を取得します。 ロジック アプリで接続情報を入力するときに、この文字列が必要です。

   1. **[共有アクセス ポリシー]** ウィンドウで、 **[RootManageSharedAccessKey]** を選択します。

   1. プライマリ接続文字列の横にあるコピー ボタンを選択します。 後で使用できるように接続文字列を保存します。

      ![Service Bus 名前空間の接続文字列をコピーする](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > 接続文字列が Service Bus 名前空間に関連付けられているのか、キューのようなメッセージング エンティティに関連付けられているのかを確認するには、接続文字列で `EntityPath` パラメーターを探します。 このパラメーターがある場合、接続文字列は特定のエンティティを対象としています。これは、ロジック アプリで使用するのに適切な文字列ではありません。

## <a name="add-service-bus-trigger"></a>Service Bus トリガーの追加

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーで空のロジック アプリを開きます。

1. ポータルの検索ボックスに、「`azure service bus`」と入力します。 表示されたトリガー一覧から、使用するトリガーを選択します。

   たとえば、新しい項目が Service Bus キューに送信されたときにロジック アプリをトリガーするには、 **[メッセージがキューに着信したとき (オート コンプリート)]** トリガーを選択します。

   ![Service Bus トリガーを選択する](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Service Bus トリガーを使用する場合の考慮事項を次に示します。

   * すべての Service Bus トリガーは、*長いポーリング* のトリガーです。 この説明はつまり、起動するときにすべてのメッセージを処理し、キューまたはトピック サブスクリプションにさらにメッセージが届くのを 30 秒間待機します。 30 秒以内にメッセージが届かなかった場合、トリガーの実行はスキップされます。 受信した場合、トリガーはキューまたはトピック サブスクリプションが空になるまでメッセージの読み取りを続けます。 次のトリガーのポーリングは、トリガーのプロパティで指定された繰り返し間隔に基づいています。

   * **[1 つ以上のメッセージがキューに届いたとき (オート コンプリート)]** トリガーのように、1 つ以上のメッセージを返すトリガーもあります。 これらのトリガーが起動すると、1 からトリガーの **[最大メッセージ数]** プロパティで指定された数までのメッセージが返されます。

     > [!NOTE]
     > オートコンプリートのトリガーでメッセージが自動的に完成しますが、これは次回の Service Bus 呼び出し時にのみ発生します。 このビヘイビアーはロジック アプリの設計に影響を与える可能性があります。 たとえば、オートコンプリート トリガーの同時実行を変更しないようにします。変更すると、ロジック アプリが調整状態になったときに、メッセージが重複する可能性があります。 同時実行制御を変更すると、次のような状況になります。調整されたトリガーは `WorkflowRunInProgress` コードでスキップされ、完了操作は行われず、ポーリング間隔の後で次のトリガー実行が発生します。 ポーリング間隔より長い値にサービス バス ロック期間を設定する必要があります。 ただし、このように設定しても、次のポーリング間隔でもロジック アプリが調整状態のままである場合、メッセージはまだ完了していない可能性があります。

   * Service Bus トリガーに対して[同時実行の設定を有効](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)にした場合、`maximumWaitingRuns` プロパティの既定値は 10 です。 Service Bus エンティティのロック期間の設定とロジック アプリ インスタンスの実行時間によっては、この既定値は大きすぎて、"ロックが失われました" という例外が発生する可能性があります。 ご自分のシナリオに最適な値を確認するには、`maximumWaitingRuns` プロパティに値 1 または 2 を指定してテストを開始します。 待機中の実行の最大値を変更するには、「[実行待機の制限を変更する](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs)」を参照してください。

1. トリガーを Service Bus 名前空間に初めて接続する場合、接続情報の入力を求めるメッセージがロジック アプリ デザイナーによって表示された際は次の手順に従います。

   1. 接続の名前を指定し、Service Bus 名前空間を選択します。

      ![接続名の指定と Service Bus 名前空間の選択を示すスクリーンショット](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      手動で接続文字列を入力する場合は、 **[接続情報を手動で入力する]** を選択します。 接続文字列がない場合は、[接続文字列の検索方法](#permissions-connection-string)に関するセクションを参照してください。

   1. Service Bus ポリシーを選択し、 **[作成]** を選択します。

      ![Service Bus ポリシーの選択を示すスクリーンショット](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. キューやトピックなどのメッセージング エンティティを選択します。 ここでは、Service Bus キューを選択します。
   
      ![Service Bus キューの選択を示すスクリーンショット](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. 選択したトリガーで必要な情報を指定します。 その他の使用可能なプロパティをアクションに追加するには、 **[新しいパラメーターの追加]** リストを開き、必要なプロパティを選択します。

   この例のトリガーの場合、キューをチェックするためのポーリング間隔と頻度を選択します。

   ![Service Bus トリガーのポーリング間隔設定を示すスクリーンショット](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   使用可能なトリガーとプロパティの詳細については、コネクタの[リファレンス ページ](/connectors/servicebus/)を参照してください。

1. 必要なアクションを追加して、ロジック アプリの構築を続けます。

   たとえば、新しいメッセージが届いたときにメールを送信するアクションを追加することができます。 トリガーがキューをチェックし、新しいメッセージを見つけると、見つかったメッセージに対して選択したアクションをロジック アプリが実行します。

## <a name="add-service-bus-action"></a>Service Bus アクションの追加

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. アクションを追加するステップで、 **[新しいステップ]** を選択します。

   または、ステップの間にアクションを追加するには、該当するステップ間の矢印の上にポインターを移動します。 表示されるプラス記号 ( **+** ) を選択し、 **[アクションの追加]** を選択します。

1. **[アクションを選択してください]** で、検索ボックスに「`azure service bus`」と入力します。 表示されるアクションの一覧から、目的のアクションを選択します。 

   この例では、 **[メッセージの送信]** というアクションを選択します。

   ![Service Bus アクションの選択を示すスクリーンショット](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. アクションを Service Bus 名前空間に初めて接続する場合、接続情報の入力を求めるメッセージがロジック アプリ デザイナーによって表示された際は次のステップに従います。

   1. 接続の名前を指定し、Service Bus 名前空間を選択します。

      ![接続名の指定と Service Bus 名前空間の選択を示すスクリーンショット](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      手動で接続文字列を入力する場合は、 **[接続情報を手動で入力する]** を選択します。 接続文字列がない場合は、[接続文字列の検索方法](#permissions-connection-string)に関するセクションを参照してください。

   1. Service Bus ポリシーを選択し、 **[作成]** を選択します。

      ![Service Bus ポリシーの選択と [作成] ボタンの選択を示すスクリーンショット](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. キューやトピックなどのメッセージング エンティティを選択します。 ここでは、Service Bus キューを選択します。

      ![Service Bus キューの選択を示すスクリーンショット](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. 選択したアクションで必要な詳細を指定します。 その他の使用可能なプロパティをアクションに追加するには、 **[新しいパラメーターの追加]** リストを開き、必要なプロパティを選択します。

   たとえば、 **[コンテンツ]** および **[コンテンツ タイプ]** プロパティを選択してアクションに追加します。 次に、送信するメッセージのコンテンツを指定します。

   ![メッセージのコンテンツの種類と詳細を示すスクリーンショット](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   使用可能なアクションとプロパティの詳細については、コネクタの[リファレンス ページ](/connectors/servicebus/)を参照してください。

1. 他の必要なアクションを追加して、ロジック アプリの構築を続けます。

   たとえば、メッセージが送信されたことを確認するメールを送信するアクションを追加することができます。

1. ロジック アプリを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

<a name="sequential-convoy"></a>

## <a name="send-correlated-messages-in-order"></a>相互関係のあるメッセージを順番に送信する

相互関係のあるメッセージを特定の順序で送信する必要がある場合は、[Azure Service Bus コネクタ](../connectors/connectors-create-api-servicebus.md)を使用することによって、["*シーケンシャルなコンボイ*" パターン](/azure/architecture/patterns/sequential-convoy)に従うことができます。 関連付けられたメッセージには、Service Bus での[セッション](../service-bus-messaging/message-sessions.md)の ID など、それらのメッセージ間の関係を定義するプロパティがあります。

ロジック アプリを作成するとき、シーケンシャルなコンボイ パターンを実装する **Correlated in-order delivery using service bus sessions** テンプレートを選択できます。 詳細については、[関連のあるメッセージを順番に送信する](../logic-apps/send-related-messages-sequential-convoy.md)方法に関するページを参照してください。

## <a name="delays-in-updates-to-your-logic-app-taking-effect"></a>ロジック アプリの更新の有効化が遅延する

Service Bus トリガーのポーリング間隔が短い場合 (10 秒など)、ロジック アプリの更新が最大 10 分間有効にならないことがあります。 この問題を回避するために、ロジック アプリを無効にし、変更を加えてから、ロジック アプリを再度有効することができます。

<a name="connector-reference"></a>

## <a name="connector-reference"></a>コネクタのレファレンス

サービス バスでは、Service Bus コネクタにより、[サブスクリプションやトピックなどの Service Bus メッセージング エンティティ](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)ごとに、コネクタ キャッシュに最大 1,500 の一意のセッションを一度に保存できます。 セッション数がこの制限を超えると、古いセッションはキャッシュから削除されます。 詳細については、[メッセージ セッション](../service-bus-messaging/message-sessions.md)に関するページを参照してください。

コネクタの Swagger の説明に記載されているトリガー、アクション、制限に関するその他の技術的な詳細については、[コネクタのリファレンス ページ](/connectors/servicebus/)を確認してください。 Azure Service Bus メッセージングの詳細については、「[Azure Service Bus とは](../service-bus-messaging/service-bus-messaging-overview.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
