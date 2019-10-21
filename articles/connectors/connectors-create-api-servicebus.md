---
title: Azure Service Bus でメッセージを送受信する - Azure Logic Apps
description: Azure Logic Apps と Azure Service Bus を使用してエンタープライズ クラウド メッセージングを設定する
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 09/19/2019
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
tags: connectors
ms.openlocfilehash: f2034686e4a8de5e1ccc246f49337a6600bf441f
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121101"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-with-azure-service-bus"></a>Azure Logic Apps で Azure Service Bus を使用してクラウド内でメッセージを交換する

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

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* Service Bus 名前空間と、キューなどのメッセージング エンティティ。 項目とロジック アプリでは、同じ Azure サブスクリプションを使用する必要があります。 これらの項目がない場合は、[Service Bus 名前空間とキューの作成](../service-bus-messaging/service-bus-create-namespace-portal.md)方法を学習してください。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* ロジック アプリでは、Service Bus 名前空間とメッセージング エンティティを使用します。 ロジック アプリと Service Bus では、同じ Azure サブスクリプションを使用する必要があります。 ワークフローを Service Bus トリガーで開始するには、[空のロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。 ワークフローで Service Bus アクションを使用するには、[Recurrence](../connectors/connectors-native-recurrence.md) トリガーなど、別のトリガーでロジック アプリを開始します。

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>アクセス許可を確認する

ロジック アプリが Service Bus 名前空間にアクセスするためのアクセス許可を持っていることを確認します。

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. Service Bus "*名前空間*" に移動します。 名前空間ページで **[設定]** の **[共有アクセス ポリシー]** を選択します。 **[要求]** で、その名前空間に対して**管理**アクセス許可が付与されていることを確認します。

   ![Service Bus 名前空間のアクセス許可を管理する](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Service Bus 名前空間の接続文字列を取得します。 ロジック アプリで接続情報を入力するときに、この文字列が必要です。

   1. **[共有アクセス ポリシー]** ウィンドウで、 **[RootManageSharedAccessKey]** を選択します。
   
   1. プライマリ接続文字列の横にあるコピー ボタンを選択します。 後で使用できるように接続文字列を保存します。

      ![Service Bus 名前空間の接続文字列をコピーする](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > 接続文字列が Service Bus 名前空間に関連付けられているのか、キューのようなメッセージング エンティティに関連付けられているのかを確認するには、接続文字列で `EntityPath`  パラメーターを探します。 このパラメーターがある場合、接続文字列は特定のエンティティを対象としています。これは、ロジック アプリで使用するのに適切な文字列ではありません。

## <a name="add-service-bus-trigger"></a>Service Bus トリガーの追加

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーで空のロジック アプリを開きます。

1. 検索ボックスに、フィルターとして「azure service bus」と入力します。 トリガーの一覧から、使用するトリガーを選択します。

   たとえば、新しい項目が Service Bus キューに送信されたときにロジック アプリをトリガーするには、 **[メッセージがキューに着信したとき (オート コンプリート)]** トリガーを選択します。

   ![Service Bus トリガーを選択する](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   すべての Service Bus トリガーは、*長いポーリング*のトリガーです。 この説明はつまり、起動するときにすべてのメッセージを処理し、キューまたはトピック サブスクリプションにさらにメッセージが届くのを 30 秒間待機します。 30 秒以内にメッセージが届かなかった場合、トリガーの実行はスキップされます。 受信した場合、トリガーはキューまたはトピック サブスクリプションが空になるまでメッセージの読み取りを続けます。 次のトリガーのポーリングは、トリガーのプロパティで指定された繰り返し間隔に基づいています。

   **[1 つ以上のメッセージがキューに届いたとき (オート コンプリート)]** トリガーのように、1 つ以上のメッセージを返すトリガーもあります。 これらのトリガーが起動すると、1 からトリガーの **[最大メッセージ数]** プロパティで指定された数までのメッセージが返されます。

1. トリガーを Service Bus 名前空間に初めて接続する場合、接続情報の入力を求めるメッセージがロジック アプリ デザイナーによって表示された際は次の手順に従います。

   1. 接続の名前を指定し、Service Bus 名前空間を選択します。

      ![Service Bus 接続を作成する (パート 1)](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      手動で接続文字列を入力する場合は、 **[接続情報を手動で入力する]** を選択します。 接続文字列がない場合は、[接続文字列の検索方法](#permissions-connection-string)に関するセクションを参照してください。

   1. Service Bus ポリシーを選択し、 **[作成]** を選択します。

      ![Service Bus 接続を作成する (パート 2)](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. キューやトピックなどのメッセージング エンティティを選択します。 ここでは、Service Bus キューを選択します。
   
      ![Service Bus キューを選択する](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. 選択したトリガーで必要な情報を指定します。 その他の使用可能なプロパティをアクションに追加するには、 **[新しいパラメーターの追加]** リストを開き、必要なプロパティを選択します。

   この例のトリガーの場合、キューをチェックするためのポーリング間隔と頻度を選択します。

   ![ポーリング間隔を設定する](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   使用可能なトリガーとプロパティの詳細については、コネクタの[リファレンス ページ](/connectors/servicebus/)を参照してください。

1. 必要なアクションを追加して、ロジック アプリの構築を続けます。

   たとえば、新しいメッセージが届いたときにメールを送信するアクションを追加することができます。 トリガーがキューをチェックし、新しいメッセージを見つけると、見つかったメッセージに対して選択したアクションをロジック アプリが実行します。

## <a name="add-service-bus-action"></a>Service Bus アクションの追加

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーでお客様のロジック アプリを開きます。

1. アクションを追加するステップで、 **[新しいステップ]** を選択します。

   または、ステップの間にアクションを追加するには、該当するステップ間の矢印の上にポインターを移動します。 表示されるプラス記号 ( **+** ) を選択し、 **[アクションの追加]** を選択します。

1. **[アクションを選択してください]** の下の検索ボックス内に、フィルターとして「azure service bus」と入力します。 アクションの一覧から、目的のアクションを選択します。 

   この例では、 **[メッセージの送信]** というアクションを選択します。

   ![Service Bus アクションを選択する](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. アクションを Service Bus 名前空間に初めて接続する場合、接続情報の入力を求めるメッセージがロジック アプリ デザイナーによって表示された際は次のステップに従います。

   1. 接続の名前を指定し、Service Bus 名前空間を選択します。

      ![Service Bus 接続を作成する (パート 1)](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      手動で接続文字列を入力する場合は、 **[接続情報を手動で入力する]** を選択します。 接続文字列がない場合は、[接続文字列の検索方法](#permissions-connection-string)に関するセクションを参照してください。

   1. Service Bus ポリシーを選択し、 **[作成]** を選択します。

      ![Service Bus 接続を作成する (パート 2)](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. キューやトピックなどのメッセージング エンティティを選択します。 ここでは、Service Bus キューを選択します。

      ![Service Bus キューを選択する](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. 選択したアクションで必要な詳細を指定します。 その他の使用可能なプロパティをアクションに追加するには、 **[新しいパラメーターの追加]** リストを開き、必要なプロパティを選択します。

   たとえば、 **[コンテンツ]** および **[コンテンツ タイプ]** プロパティを選択してアクションに追加します。 次に、送信するメッセージのコンテンツを指定します。

   ![メッセージの内容と詳細を指定する](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   使用可能なアクションとプロパティの詳細については、コネクタの[リファレンス ページ](/connectors/servicebus/)を参照してください。

1. 他の必要なアクションを追加して、ロジック アプリの構築を続けます。

   たとえば、メッセージが送信されたことを確認するメールを送信するアクションを追加することができます。

1. ロジック アプリを保存し、 デザイナーのツール バーで、 **[保存]** を選択します。

## <a name="connector-reference"></a>コネクタのレファレンス

Service Bus コネクタを使用すると、Service Bus からコネクタ キャッシュまで最大 1500 個の一意のセッションを同時に保存できます。 セッション数がこの制限を超えると、古いセッションはキャッシュから削除されます。 コネクタの OpenAPI (以前の Swagger) の説明に記載されているトリガー、アクション、制限に関するその他の技術的な詳細については、コネクタの[リファレンス ページ](/connectors/servicebus/)を参照してください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
