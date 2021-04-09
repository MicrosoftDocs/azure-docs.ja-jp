---
title: シーケンシャルなコンボイを使用して、関連付けられたメッセージを順番に送信する
description: Azure Logic Apps のシーケンシャルなコンボイ パターンを使用して、関連するメッセージを Azure Service Bus で順番に送信する
services: logic-apps
ms.suite: integration
ms.reviewer: apseth, divswa, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 8c00d2e4f622bcfad7b2468013336f0d936e318c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87048669"
---
# <a name="send-related-messages-in-order-by-using-a-sequential-convoy-in-azure-logic-apps-with-azure-service-bus"></a>Azure Logic Apps のシーケンシャルなコンボイを使用して、関連するメッセージを Azure Service Bus で送信する

関連付けられたメッセージを特定の順序で送信する必要がある場合は、[Azure Service Bus コネクタ](../connectors/connectors-create-api-servicebus.md)を使用することによって、[Azure Logic Apps](../logic-apps/logic-apps-overview.md) を使用するときに "[*シーケンシャルなコンボイ*" パターン](/azure/architecture/patterns/sequential-convoy)に従うことができます。 関連付けられたメッセージには、Service Bus での[セッション](../service-bus-messaging/message-sessions.md)の ID など、それらのメッセージ間の関係を定義するプロパティがあります。

たとえば、"Session 1" という名前のセッションのメッセージが 10 個あり、"Session 2" という名前のセッションのメッセージが 5 個あり、すべてが同じ [Service Bus キュー](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)に送信されるとします。 キューにあるメッセージを処理するロジック アプリを作成し、"Session 1" のすべてのメッセージが 1 回のトリガー実行によって処理され、"Session 2" のすべてのメッセージが次のトリガー実行によって処理されるようにすることができます。

![一般的なシーケンシャルなコンボイ パターン](./media/send-related-messages-sequential-convoy/sequential-convoy-pattern-general.png)

この記事では、このパターンを実装するロジック アプリを、 **[Service Bus のセッションを使用した、相関した順次配信]** テンプレートを使用して作成する方法について説明します。 このテンプレートには、Service Bus コネクタの **[メッセージがキューに着信したとき (ピーク ロック)]** トリガーで始まるロジック アプリ ワークフローが定義されており、これによって [Service Bus キュー](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)からメッセージを受信します。 このロジック アプリで実行される大まかな手順を次に示します。

* トリガーによって Service Bus キューから読み取ったメッセージに基づいて、セッションを初期化します。

* 現在のワークフローの実行中に、キュー内の同じセッションからすべてのメッセージを読み取り、処理します。

このテンプレートの JSON ファイルを確認するには、[GitHub: service-bus-sessions.json](https://github.com/Azure/logicapps/blob/master/templates/service-bus-sessions.json) を参照してください。

詳細については、[Azure アーキテクチャのクラウド設計パターンの「シーケンシャルなコンボイ パターン」](/azure/architecture/patterns/sequential-convoy)を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* Service Bus 名前空間と、[Service Bus キュー](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) (これは、ロジック アプリで使用するメッセージング エンティティです)。 項目とロジック アプリでは、同じ Azure サブスクリプションを使用する必要があります。 キューを作成するときに **[セッションの有効化]** を選択していることを確認します。 これらの項目がない場合は、[Service Bus 名前空間とキューの作成](../service-bus-messaging/service-bus-create-namespace-portal.md)方法を学習してください。

  [!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

* ロジック アプリの作成方法に関する基本的な知識。 Azure Logic Apps を初めて使用する場合は、[初めての自動化されたワークフローの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)のクイックスタートをお試しください。

<a name="permissions-connection-string"></a>

## <a name="check-access-to-service-bus-namespace"></a>Service Bus 名前空間へのアクセスを確認する

ご自身のロジック アプリに Service Bus 名前空間に対するアクセス許可があるかどうかわからない場合は、それらのアクセス許可を確認します。

1. [Azure portal](https://portal.azure.com) にサインインします。 Service Bus の "*名前空間*" を見つけて選択します。

1. 名前空間メニューで **[設定]** の **[共有アクセス ポリシー]** を選択します。 **[要求]** で、その名前空間に対して **管理** アクセス許可が付与されていることを確認します。

   ![Service Bus 名前空間のアクセス許可を管理する](./media/send-related-messages-sequential-convoy/check-service-bus-permissions.png)

1. 次に Service Bus 名前空間の接続文字列を取得します。 この文字列は、後でロジック アプリから名前空間への接続を作成するときに使用できます。

   1. **[共有アクセス ポリシー]** ウィンドウで、 **[ポリシー]** の **[RootManageSharedAccessKey]** を選択します。
   
   1. プライマリ接続文字列の横にあるコピー ボタンを選択します。 後で使用できるように接続文字列を保存します。

      ![Service Bus 名前空間の接続文字列をコピーする](./media/send-related-messages-sequential-convoy/copy-service-bus-connection-string.png)

   > [!TIP]
   > 接続文字列が Service Bus 名前空間に関連付けられているのか、キューのようなメッセージング エンティティに関連付けられているのかを確認するには、接続文字列で `EntityPath` パラメーターを探します。 このパラメーターがある場合、接続文字列は特定のエンティティを対象としています。これは、ロジック アプリで使用するのに適切な文字列ではありません。

## <a name="create-logic-app"></a>ロジック アプリを作成する

このセクションでは、このワークフロー パターンを実装するためのトリガーとアクションが含まれた **[Service Bus のセッションを使用した、相関した順次配信]** テンプレートを使用して、ロジック アプリを作成します。 また、Service Bus 名前空間への接続を作成し、使用する Service Bus キューの名前を指定します。

1. [Azure Portal](https://portal.azure.com) で、空のロジック アプリを作成します。 Azure のホーム ページで、 **[リソースの作成]**  >  **[統合]**  >  **[ロジック アプリ]** の順に選択します。

1. テンプレート ギャラリーが表示されたら、ビデオおよび一般的なトリガーのセクションの下までスクロールします。 **[テンプレート]** セクションで、 **[Service Bus のセッションを使用した、相関した順次配信]** テンプレートを選択します。

   !["Service Bus のセッションを使用した、相関した順次配信" テンプレートの選択](./media/send-related-messages-sequential-convoy/select-correlated-in-order-delivery-template.png)

1. 確認ボックスが表示されたら、 **[このテンプレートを使用]** を選択します。

1. ロジック アプリ デザイナーの **[Service Bus]** の図形で **[続行]** を選択し、図形に表示されるプラス記号 ( **+** ) を選択します。

   ![[続行] を選択して Azure Service Bus に接続する](./media/send-related-messages-sequential-convoy/connect-to-service-bus.png)

1. 次に、いずれかのオプションを選択して、Service Bus 接続を作成します。

   * 先ほど Service Bus 名前空間からコピーした接続文字列を使用するには、次の手順に従います。

     1. **[接続情報を手動で入力する]** を選択します。

     1. **[接続名]** には、接続の名前を指定します。 **[接続文字列]** には、名前空間の接続文字列を貼り付け、 **[作成]** を選択します。次に例を示します。

        ![接続名と Service Bus 接続文字列を入力する](./media/send-related-messages-sequential-convoy/provide-service-bus-connection-string.png)

        > [!TIP]
        > この接続文字列がない場合は、[Service Bus 名前空間の接続文字列を探してコピーする](#permissions-connection-string)方法を確認してください。

   * 現在の Azure サブスクリプションから Service Bus 名前空間を選択するには、次の手順に従います。

     1. **[接続名]** には、接続の名前を指定します。 **[Service Bus 名前空間]** には、使用する Service Bus 名前空間を選択します。たとえば、次のようになります。

        ![接続名を入力し、Service Bus 名前空間を選択する](./media/send-related-messages-sequential-convoy/create-service-bus-connection.png)

     1. 次のウィンドウが表示されたら、Service Bus ポリシーを選択し、 **[作成]** を選択します。

        ![Service Bus ポリシーを選択し、[作成] を選択する](./media/send-related-messages-sequential-convoy/create-service-bus-connection-2.png)

1. 終了したら、 **[続行]** を選択します。

   ロジック アプリ デザイナーに **[Service Bus のセッションを使用した、相関した順次配信]** テンプレートが表示されるようになりました。このテンプレートには、トリガーとアクションを使用した事前設定されたワークフローが含まれており、`Try-Catch` パターンに従ったエラー処理を実装する 2 つのスコープが組み込まれています。

これで、テンプレートに含まれるトリガーとアクションの詳細情報を確認するか、[ロジック アプリ テンプレートの値の指定](#complete-template)に進むことができます。

<a name="template-summary"></a>

## <a name="template-summary"></a>テンプレートの概要

次に示すのは、 **[Service Bus のセッションを使用した、相関した順次配信]** テンプレートにある、詳細が折りたたまれている場合の最上位レベルのワークフローです。

![テンプレートの最上位レベルのワークフロー](./media/send-related-messages-sequential-convoy/template-top-level-flow.png)

| 名前 | 説明 |
|------|-------------|
| **`When a message is received in a queue (peek-lock)`** | この Service Bus トリガーでは、指定された繰り返しに基づいて、指定された Service Bus キューでメッセージの有無を確認します。 キューにメッセージが存在する場合はトリガーを起動し、ワークフロー インスタンスを作成して実行します。 <p><p>"*ピーク ロック*" という用語は、キューからメッセージを取得する要求をトリガーによって送信することを意味します。 メッセージが存在する場合、トリガーによってメッセージを取得してロックし、ロック期間が終了するまでそのメッセージに他の処理が行われないようにします。 詳細については、「[セッションを初期化する](#initialize-session)」を参照してください。 |
| **`Init isDone`** | この [ **[変数を初期化する]** アクション](../logic-apps/logic-apps-create-variables-store-values.md#initialize-variable)で、`false` に設定され、次の条件が満たされたことを示すブール変数を作成します。 <p><p>- セッション内に、読み取りができるメッセージがこれ以上ない。 <br>- これ以上セッション ロックを更新する必要がなく、現在のワークフロー インスタンスを終了できる。 <p><p>詳細については、「[セッションを初期化する](#initialize-session)」を参照してください。 |
| **`Try`** | この [**スコープ** アクション](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)には、メッセージを処理するために実行されるアクションが含まれています。 `Try` スコープで問題が発生した場合は、後続の `Catch` **スコープ** アクションによってその問題が処理されます。 詳細については、「["Try" スコープ](#try-scope)」を参照してください。 |
| **`Catch`**| この [**スコープ** アクション](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)には、先行する `Try` スコープで問題が発生した場合に実行されるアクションが含まれています。 詳細については、「["Catch" スコープ](#catch-scope)」を参照してください。 |
|||

<a name="try-scope"></a>

### <a name="try-scope"></a>"Try" スコープ

`Try` [スコープ アクション](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)内の詳細が折りたたまれている場合の、最上位レベルのフローを次に示します。

!["Try" スコープ アクションのワークフロー](./media/send-related-messages-sequential-convoy/try-scope-action.png)

| 名前 | 説明 |
|------|-------------|
| **`Send initial message to topic`** | このアクションは、キュー内のセッションからの最初のメッセージを処理する任意のアクションに置き換えることができます。 セッション ID によってセッションを指定します。 <p><p>このテンプレートでは、最初のメッセージを Service Bus アクションによって Service Bus トピックに送信します。 詳細については、「[最初のメッセージを処理する](#handle-initial-message)」を参照してください。 |
| (並列分岐) | この[並列分岐アクション](../logic-apps/logic-apps-control-flow-branches.md)により、2 つのパスを作成します。 <p><p>- 分岐 #1: メッセージの処理を続行します。 詳細については、「[分岐 #1: キュー内の最初のメッセージを完了する](#complete-initial-message)」を参照してください。 <p><p>- 分岐 #2: 何か問題が発生した場合はメッセージを破棄し、別のトリガー実行で取得するために解放します。 詳細については、「[分岐 #2: キューから最初のメッセージを破棄する](#abandon-initial-message)」を参照してください。 <p><p>次の行に示すように、両方のパスは後で **[Close session in a queue and succeed]\(キュー内のセッションを閉じて成功させる\)** アクションに結合します。 |
| **`Close a session in a queue and succeed`** | この Service Bus アクションは、前述の分岐に結合し、次のいずれかのイベントが発生した後で、キュー内のセッションを閉じます。 <p><p>- ワークフローで、キュー内の使用可能なメッセージの処理が終了した。 <br>- 問題が発生したため、ワークフローで最初のメッセージが破棄された。 <p><p>詳細については、「[キュー内のセッションを閉じて成功させる](#close-session-succeed)」を参照してください。 |
|||

<a name="complete-initial-message"></a>

#### <a name="branch-1-complete-initial-message-in-queue"></a>分岐 #1: キュー内の最初のメッセージを完了する

| 名前 | 説明 |
|------|-------------|
| `Complete initial message in queue` | この Service Bus アクションでは、正常に取得されたメッセージを完了としてマークし、再処理を避けるためにキューからそのメッセージを削除します。 詳細については、「[最初のメッセージを処理する](#handle-initial-message)」を参照してください。 |
| `While there are more messages for the session in the queue` | この [**Until** ループ](../logic-apps/logic-apps-control-flow-loops.md#until-loop)では、メッセージが存在している間または 1 時間が経過するまで、メッセージを取得し続けます。 このループ内のアクションの詳細については、「[キューにセッションのその他のメッセージがある間](#while-more-messages-for-session)」を参照してください。 |
| **`Set isDone = true`** | これ以上メッセージが存在しない場合、この [**変数の設定** アクション](../logic-apps/logic-apps-create-variables-store-values.md#set-variable)で `isDone` を `true` に設定します。 |
| **`Renew session lock until cancelled`** | この [**Until** ループ](../logic-apps/logic-apps-control-flow-loops.md#until-loop)では、メッセージが存在している間または 1 時間が経過するまで、このロジック アプリによってセッション ロックが確実に保持されるようにします。 このループ内のアクションの詳細については、「[キャンセルされるまでセッション ロックを更新する](#renew-session-while-messages-exist)」を参照してください。 |
|||

<a name="abandon-initial-message"></a>

#### <a name="branch-2-abandon-initial-message-from-the-queue"></a>分岐 #2: キューから最初のメッセージを破棄する

最初のメッセージを処理するアクションが失敗した場合、別のワークフロー インスタンス実行で取得して処理できるように、Service Bus アクション **[Abandon initial message from the queue]\(キューから最初のメッセージを破棄する\)** でそのメッセージを解放します。 詳細については、「[最初のメッセージを処理する](#handle-initial-message)」を参照してください。

<a name="catch-scope"></a>

### <a name="catch-scope"></a>"Catch" スコープ

`Try` スコープ内のアクションが失敗した場合、やはりロジック アプリでセッションを閉じる必要があります。 `Catch` [スコープ アクション](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)は、`Try` スコープ アクションの結果が `Failed`、`Skipped`、または `TimedOut` になったときに実行されます。 問題が発生したセッション ID を含むエラー メッセージがスコープから返され、ロジック アプリが終了します。

`Catch` スコープ アクション内の詳細が折りたたまれている場合の、最上位レベルのフローを次に示します。

!["Catch" スコープ アクションのワークフロー](./media/send-related-messages-sequential-convoy/catch-scope-action.png)

| 名前 | 説明 |
|------|-------------|
| **`Close a session in a queue and fail`** | この Service Bus アクションでは、セッション ロックが開いたままにならないように、キュー内のセッションを閉じます。 詳細については、「[キュー内のセッションを閉じて失敗させる](#close-session-fail)」を参照してください。 |
| **`Find failure msg from 'Try' block`** | この [**配列のフィルター処理** アクション](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action)では、指定した条件に基づいて、`Try` スコープ内のすべてのアクションの入力と出力から配列を作成します。 この場合、このアクションでは、結果が `Failed` の状態になったアクションからの出力が返されます。 詳細については、「['Try' ブロックの失敗メッセージを検索する](#find-failure-message)」を参照してください。 |
| **`Select error details`** | この [**選択** アクション](../logic-apps/logic-apps-perform-data-operations.md#select-action)では、指定した条件に基づいて、JSON オブジェクトが含まれた配列を作成します。 これらの JSON オブジェクトは、前のアクションである `Find failure msg from 'Try' block` で作成された配列内の値から構築されます。 この場合、このアクションでは、前のアクションで返されたエラーの詳細から作成された JSON オブジェクトを含む配列を返します。 詳細については、「[エラーの詳細を選択する](#select-error-details)」を参照してください。 |
| **`Terminate`** | この [**終了** アクション](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action)では、ワークフローの実行を停止し、進行中のすべてのアクションをキャンセルし、残りのアクションをすべてスキップして、`Select error details` アクションから指定された状態、セッション ID、エラー結果を返します。 詳細については、「[ロジック アプリを終了する](#terminate-logic-app)」を参照してください。 |
|||

<a name="complete-template"></a>

## <a name="complete-the-template"></a>テンプレートを完成させる

**[Service Bus のセッションを使用した、相関した順次配信]** テンプレート内のトリガーとアクションの値を指定するには、次の手順に従います。 ロジック アプリを保存する前に、アスタリスク ( **\*** ) が付いているすべての必須値を指定する必要があります。

<a name="initialize-session"></a>

### <a name="initialize-the-session"></a>セッションの初期化

* **[メッセージがキューに着信したとき (ピーク ロック)]** トリガーに対して、この情報を指定し、テンプレートで **[セッション ID]** プロパティを使用してセッションを初期化できるようにします。次に例を示します。

  !["メッセージがキューに着信したとき (ピーク ロック)" の Service Bus トリガーの詳細](./media/send-related-messages-sequential-convoy/service-bus-check-message-peek-lock-trigger.png)

  > [!NOTE]
  > 最初は、ポーリング間隔を 3 分に設定して、ロジック アプリが必要以上に頻繁に実行され、予測しなかった請求料金が発生することがないようにします。 理想的には、間隔と頻度を 30 秒に設定して、メッセージが到着したら即時にロジック アプリがトリガーされるようにします。

  | プロパティ | このシナリオでは必須 | 値 | 説明 |
  |----------|----------------------------|-------|-------------|
  | **キュー名** | はい | <*queue-name*> | 作成済みの Service Bus キューの名前。 この例では、"Fabrikam-Service-Bus-Queue" を使用します。 |
  | **[キューの種類]** | はい | **メイン** | プライマリ Service Bus キュー |
  | **セッション ID** | はい | **Next available (次の利用可能)** | このオプションでは、Service Bus キュー内のメッセージからのセッション ID に基づいて、トリガー実行ごとにセッションを取得します。 また、このセッションに関連するメッセージを他のロジック アプリまたは他のクライアントが処理できないように、セッションはロックされます。 ワークフローの後続のアクションでは、この記事の後半で説明するように、そのセッションに関連付けられているすべてのメッセージを処理します。 <p><p>**[セッション ID]** のその他のオプションの詳細は次のとおりです。 <p>- **なし**: 既定のオプション。セッションは発生せず、シーケンシャルなコンボイ パターンの実装には使用できません。 <p>- **カスタム値の入力**: 使用するセッション ID がわかっていて、そのセッション ID に対して常にトリガーを実行する場合は、このオプションを使用します。 <p>**注**:Service Bus コネクタを使用すると、Service Bus からコネクタ キャッシュに、限られた数の一意のセッションを一度に保存できます。 セッション数がこの制限を超えると、古いセッションはキャッシュから削除されます。 詳細については、「[Azure Logic Apps と Azure Service Bus を使用してクラウド内でメッセージを交換する](../connectors/connectors-create-api-servicebus.md#connector-reference)」を参照してください。 |
  | **間隔** | はい | <*number-of-intervals*> | メッセージの有無を確認する前の繰り返しの間隔を表す時間単位数。 |
  | **頻度** | はい | **[秒]** 、 **[分]** 、 **[時間]** 、 **[日]** 、 **[週]** 、または **[月]** | メッセージの有無を確認するときに繰り返しで使用される時間の単位。 <p>**ヒント**:**タイム ゾーン** または **開始時刻** を追加するには、 **[新しいパラメーターの追加]** の一覧からこれらのプロパティを選択します。 |
  |||||

  トリガーの詳細については、「[Service Bus - メッセージがキューに着信したとき (ピーク ロック)](/connectors/servicebus/#when-a-message-is-received-in-a-queue-(peek-lock))」を参照してください。 このトリガーの出力は [ServiceBusMessage](/connectors/servicebus/#servicebusmessage) です。

セッション初期化の後、このワークフローでは **[変数を初期化する]** アクションを使用してブール変数を作成します。これは、最初は `false` に設定され、次の条件が満たされたことを示します。 

* セッション内に、読み取りができるメッセージがこれ以上ない。

* これ以上セッション ロックを更新する必要がなく、現在のワークフロー インスタンスを終了できる。

!["Init isDone" の "変数を初期化する" アクションの詳細](./media/send-related-messages-sequential-convoy/init-is-done-variable.png)

次に、**Try** ブロックで、読み取られた最初のメッセージに対してアクションを実行します。

<a name="handle-initial-message"></a>

### <a name="handle-the-initial-message"></a>最初のメッセージを処理する

最初のアクションは、プレースホルダーの Service Bus アクションである **[Send initial message to topic]\(最初のメッセージをトピックに送信する\)** です。これは、キュー内のセッションからの最初のメッセージを処理する他のアクションと置き換えることができます。 セッション ID により、メッセージ発信元のセッションを指定します。

プレースホルダーの Service Bus アクションで、 **[セッション ID]** プロパティで指定された Service Bus トピックに最初のメッセージを送信します。 これにより、特定のセッションに関連付けられているすべてのメッセージが同じトピックに送られます。 このテンプレート内のすべての後続アクションについて、 **[セッション ID]** プロパティに同じセッション ID 値が使用されます。

!["Send initial message to topic (最初のメッセージをトピックに送信する)" の Service Bus アクションの詳細](./media/send-related-messages-sequential-convoy/send-initial-message-to-topic-action.png)

1. Service Bus アクション **[Complete initial message in queue]\(キュー内の最初のメッセージを完了する\)** で、Service Bus キューの名前を指定し、アクションのその他すべての既定プロパティ値をそのまま使用します。

   !["Complete initial message in queue (キュー内の最初のメッセージを完了する)" の Service Bus アクションの詳細](./media/send-related-messages-sequential-convoy/complete-initial-message-queue.png)

1. Service Bus アクション **[Abandon initial message from the queue]\(キューから最初のメッセージを破棄する\)** で、Service Bus キューの名前を指定し、アクションのその他すべての既定プロパティ値をそのまま使用します。

   !["Abandon initial message from the queue (キューから最初のメッセージを破棄する)" の Service Bus アクションの詳細](./media/send-related-messages-sequential-convoy/abandon-initial-message-from-queue.png)

次に、 **[Complete initial message in queue]\(キュー内の最初のメッセージを完了する\)** アクションの後に続くアクションに必要な情報を指定します。 まず、 **[While there are more messages for the session in the queue]\(キューにセッションのその他のメッセージがある間\)** ループ内のアクションから開始します。

<a name="while-more-messages-for-session"></a>

### <a name="while-there-are-more-messages-for-the-session-in-the-queue"></a>キューにセッションのその他のメッセージがある間

この [**Until** ループ](../logic-apps/logic-apps-control-flow-loops.md#until-loop)では、キューにメッセージが存在する間または 1 時間が経過するまで、これらのアクションを実行します。 ループの時間制限を変更するには、ループの **[タイムアウト]** プロパティ値を編集します。

* メッセージが存在する間、キューから追加のメッセージを取得します。

* 残りのメッセージの数を確認します。 メッセージがまだ存在する場合は、メッセージの処理を続行します。 それ以上メッセージが存在しない場合、ワークフローで `isDone` 変数を `true` に設定し、ループを終了します。

![Until ループ - キューにある間メッセージを処理する](./media/send-related-messages-sequential-convoy/while-more-messages-for-session-in-queue.png)

1. Service Bus アクション **[Get additional messages from session]\(セッションから追加のメッセージを取得する\)** で、Service Bus キューの名前を指定します。 それ以外、アクションのその他すべての既定プロパティ値をそのまま使用します。

   > [!NOTE]
   > 既定では、メッセージの最大数は `175` に設定されていますが、この制限はメッセージ サイズと Service Bus の最大メッセージ サイズ プロパティによって影響されます。 詳細については、[キューのメッセージ サイズ](../service-bus-messaging/service-bus-quotas.md)に関するページを参照してください。

   ![Service Bus アクション - "Get additional messages from session (セッションから追加のメッセージを取得する)"](./media/send-related-messages-sequential-convoy/get-additional-messages-from-session.png)

   次に、ワークフローは次の並列分岐に分割されます。

   * 追加のメッセージの有無を確認しているときにエラーまたは失敗が発生した場合は、`isDone` 変数を `true` に設定します。

   * **[Process messages if we got any]\(メッセージがある場合は処理する\)** 条件により、残りのメッセージの数がゼロかどうかを確認します。 false でメッセージがまだ存在する場合は、処理を続行します。 true でそれ以上メッセージが存在しない場合、ワークフローで `isDone` 変数を `true` に設定します。

   ![条件 - Process messages if any (メッセージがある場合は処理する)](./media/send-related-messages-sequential-convoy/process-messages-if-any.png)

   **[false の場合]** セクションでは、**For each** ループで先入れ先出し (FIFO) 順に各メッセージを処理します。 ループの **[設定]** で、 **[同時実行制御]** の設定が `1` になっているので、一度に 1 つのメッセージだけが処理されます。

   !["For each" ループ - 各メッセージを一度に 1 つずつ処理する](./media/send-related-messages-sequential-convoy/for-each-additional-message.png)

1. **[キュー内のメッセージを完了する]** および **[キュー内のメッセージを破棄する]** の Service Bus アクションに対して、Service Bus キューの名前を指定します。

   ![Service Bus アクション-"キュー内のメッセージを完了する" および "キュー内のメッセージを破棄する"](./media/send-related-messages-sequential-convoy/abandon-or-complete-message-in-queue.png)

   **[While there are more messages for the session in the queue]\(キューにセッションのその他のメッセージがある間\)** の完了後、ワークフローで `isDone` 変数を `true` に設定します。

次に、 **[Renew session lock until cancelled]\(キャンセルされるまでセッション ロックを更新する\)** ループ内のアクションに必要な情報を指定します。

<a name="renew-session-while-messages-exist"></a>

### <a name="renew-session-lock-until-cancelled"></a>キャンセルされるまでセッション ロックを更新する

この [**Until** ループ](../logic-apps/logic-apps-control-flow-loops.md#until-loop)により、キューにメッセージが存在する間、またはこれらのアクションを実行して 1 時間が経過するまで、セッション ロックがこのロジック アプリによって確実に保持されます。 ループの時間制限を変更するには、ループの **[タイムアウト]** プロパティ値を編集します。

* 25 秒間の遅延、または処理されているキューのロック タイムアウト時間よりも短い時間。 最小ロック期間は 30 秒であるため、既定値で十分です。 ただし、適切に調整することによって、ループが実行される回数を最適化することができます。

* `isDone` 変数が `true` に設定されているかどうかを確認します。

  * `isDone` が `true` に設定されていない場合、まだメッセージがワークフローで処理中です。そのため、ワークフローでキュー内のセッションのロックを更新し、ループ条件を再度確認します。

    Service Bus アクション [ **[キュー内のセッションに対するロックを更新します]**](#renew-lock-on-session) で、Service Bus キューの名前を指定する必要があります。

  * `isDone` が `true` に設定されている場合、ワークフローでキュー内のセッションのロックは更新されず、ループは終了します。

  ![Until ループ - "Renew session lock until cancelled (キャンセルされるまでセッション ロックを更新する)"](./media/send-related-messages-sequential-convoy/renew-lock-until-session-cancelled.png)

<a name="renew-lock-on-session"></a>

#### <a name="renew-lock-on-the-session-in-a-queue"></a>キュー内のセッションに対するロックを更新します

この Service Bus アクションでは、メッセージがワークフローでまだ処理されている間、キュー内のセッションのロックを更新します。

* Service Bus アクション **[キュー内のセッションに対するロックを更新します]** で、Service Bus キューの名前を指定します。

  ![Service Bus アクション - "キュー内のセッションに対するロックを更新します"](./media/send-related-messages-sequential-convoy/renew-lock-on-session-in-queue.png)

次に、Service Bus アクション **[Close a session in a queue and succeed]\(キュー内のセッションを閉じて成功させる\)** に必要な情報を指定します。

<a name="close-session-succeed"></a>

### <a name="close-a-session-in-a-queue-and-succeed"></a>キュー内のセッションを閉じて成功させる

この Service Bus アクションでは、キュー内の使用可能なすべてのメッセージの処理がワークフローで終了した後、または最初のメッセージをワークフローで破棄した後に、キュー内のセッションを閉じます。

* Service Bus アクション **[Close a session in a queue and succeed]\(キュー内のセッションを閉じて成功させる\)** で、Service Bus キューの名前を指定します。

  ![Service Bus アクション - "Close a session in a queue and succeed (キュー内のセッションを閉じて成功させる)"](./media/send-related-messages-sequential-convoy/close-session-in-queue-succeed.png)

以下のセクションでは、ワークフローで発生するエラーと例外を処理する `Catch` セクション内のアクションについて説明します。

<a name="close-session-fail"></a>

### <a name="close-a-session-in-a-queue-and-fail"></a>キュー内のセッションを閉じて失敗させる

これは、常に `Catch` スコープ内の最初のアクションとして実行され、キュー内のセッションを終了する Service Bus アクションです。

* Service Bus アクション **[Close a session in a queue and fail]\(キュー内のセッションを閉じて失敗させる\)** で、Service Bus キューの名前を指定します。

  ![Service Bus アクション - "Close a session in a queue and fail (キュー内のセッションを閉じて失敗させる)"](./media/send-related-messages-sequential-convoy/close-session-in-queue-fail.png)

次に、ワークフローで、`Try` スコープ内のすべてのアクションからの入力と出力を含む配列を作成します。これにより、発生したエラーまたは失敗に関する情報にロジック アプリからアクセスできるようになります。

<a name="find-failure-message"></a>

### <a name="find-failure-msg-from-try-block"></a>'Try' ブロックの失敗メッセージを検索する

この [**配列のフィルター処理** アクション](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action)では、[`result()` 関数](../logic-apps/workflow-definition-language-functions-reference.md#result)を使用して指定した条件に基づいて、`Try` スコープ内のすべてのアクションの入力と出力を含む配列を作成します。 この場合、このアクションでは、[`equals()` 関数](../logic-apps/workflow-definition-language-functions-reference.md#equals)および [`item()` 関数](../logic-apps/workflow-definition-language-functions-reference.md#item)を使用して、`Failed` の状態を持つアクションからの出力が返されます。

![配列のフィルター処理アクション -"Find failure msg from 'Try' block ('Try' ブロックの失敗メッセージを検索する)"](./media/send-related-messages-sequential-convoy/find-failure-message.png)

このアクションの JSON 定義を次に示します。

```json
"Find_failure_msg_from_'Try'_block": {
   "inputs": {
      "from": "@Result('Try')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "Close_the_session_in_the_queue_and_fail": [
         "Succeeded"
      ]
   },
   "type": "Query"
},
```

次に、ワークフローでは、`Find failure msg from 'Try' block` ('Try' ブロックの失敗メッセージを検索する) アクションから返された配列にエラー情報が含まれた JSON オブジェクトを含む配列を作成します。

<a name="select-error-details"></a>

### <a name="select-error-details"></a>エラーの詳細を選択する

この [**選択** アクション](../logic-apps/logic-apps-perform-data-operations.md#select-action)では、前のアクションである `Find failure msg from 'Try' block` ('Try' ブロックの失敗メッセージを検索する) から出力された入力配列に基づいた JSON オブジェクトを含む配列を作成します。 具体的には、このアクションで返されるのは、配列内の各オブジェクトに対して指定されたプロパティのみを持つ配列です。 この場合、配列にはアクション名とエラー結果のプロパティが含まれています。

![選択アクション - "Select error details (エラーの詳細を選択する)"](./media/send-related-messages-sequential-convoy/select-error-details.png)

このアクションの JSON 定義を次に示します。

```json
"Select_error_details": {
   "inputs": {
      "from": "@body('Find_failure_msg_from_''Try''_block')[0]['outputs']",
      "select": {
         "action": "@item()['name']",
         "errorResult": "@item()"
      }
   },
   "runAfter": {
      "Find_failure_msg_from_'Try'_block": [
         "Succeeded"
      ]
   },
   "type": "Select"
},
```

次に、ワークフローでロジック アプリの実行を停止し、発生したエラーまたは失敗に関する詳細情報と共に実行状態を返します。

<a name="terminate-logic-app"></a>

### <a name="terminate-logic-app-run"></a>ロジック アプリ実行を終了する

この [**終了** アクション](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action)では、ロジック アプリの実行を停止し、ロジック アプリの実行の状態として `Failed` を返すと共に、`Select error details` アクションからのセッション ID およびエラー結果を返します。

![ロジック アプリ実行を停止する終了アクション](./media/send-related-messages-sequential-convoy/terminate-logic-app-run.png)

このアクションの JSON 定義を次に示します。

```json
"Terminate": {
   "description": "This Failure Termination only runs if the Close Session upon Failure action runs - otherwise the LA will be terminated as Success",
   "inputs": {
      "runError": {
         "code": "",
         "message": "There was an error processing messages for Session ID @{triggerBody()?['SessionId']}. The following error(s) occurred: @{body('Select_error_details')['errorResult']}"
         },
         "runStatus": "Failed"
      },
      "runAfter": {
         "Select_error_details": [
            "Succeeded"
         ]
      },
      "type": "Terminate"
   }
},
```

## <a name="save-and-run-logic-app"></a>ロジック アプリを保存して実行する

テンプレートが完了したら、ロジック アプリを保存できます。 デザイナーのツール バーで、 **[保存]** を選択します。

ロジック アプリをテストするには、Service Bus キューにメッセージを送信します。 

## <a name="next-steps"></a>次のステップ

* [Service Bus コネクタのトリガーとアクション](/connectors/servicebus/)についての詳細情報
