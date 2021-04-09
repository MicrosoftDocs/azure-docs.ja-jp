---
title: Azure Functions でロジック アプリを呼び出す
description: Azure Functions と Azure Service Bus を使用してロジック アプリを呼び出すか、またはトリガーする
services: logic-apps
ms.suite: integration
ms.reviewer: jehollan, klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: a7df9ba1318f40de8af392cfaedbe51d7a5df755
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784936"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Azure Functions と Azure Service Bus を使用してロジック アプリを呼び出すか、またはトリガーする

[Azure Functions](../azure-functions/functions-overview.md) を使用すると、実行時間の長いリスナーまたはタスクをデプロイする必要がある場合にロジック アプリをトリガーできます。 たとえば、[Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) キューでリッスンする関数を作成し、ロジック アプリをプッシュ トリガーとして直ちに起動できます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* Azure Service Bus 名前空間。 名前空間がない場合は、[最初に名前空間を作成](../service-bus-messaging/service-bus-create-namespace-portal.md)します。

* 関数のコンテナーである関数アプリ。 関数アプリがない場合は、[最初に関数アプリを作成](../azure-functions/functions-get-started.md)し、ランタイム スタックとして .NET を選択するようにしてください。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

## <a name="create-logic-app"></a>ロジック アプリを作成する

このシナリオでは、トリガーする各ロジック アプリを実行する関数が用意されています。 まず、HTTP 要求トリガーで開始されるロジック アプリを作成します。 関数は、キュー メッセージが受信されたときに、そのエンドポイントを呼び出します。

1. [Azure portal](https://portal.azure.com) にサインインして、空のロジック アプリを作成します。

   ロジック アプリを初めて使用する場合は、[クイック スタート:初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

1. 検索ボックスに「 `http request`」と入力します。 トリガーの一覧から、 **[HTTP 要求の受信時]** というトリガーを選択します。

   ![トリガーの選択](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   要求トリガーでは、キュー メッセージで使用する JSON スキーマを必要に応じて入力できます。 JSON スキーマは、ロジック アプリ デザイナーが入力データの構造を理解できるよう支援すると共に、その出力をユーザーがワークフローで使用しやすくします。

1. スキーマを指定するには、 **[要求本文の JSON スキーマ]** ボックスにスキーマを入力します。その例を次に示します。

   ![JSON スキーマの指定](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   スキーマはなくても JSON 形式のサンプル ペイロードがあれば、そのペイロードからスキーマを生成することができます。

   1. 要求トリガーで **[サンプルのペイロードを使用してスキーマを生成する]** を選択します。

   1. **[サンプルの JSON ペイロードを入力するか、貼り付けます]** でサンプル ペイロードを入力し、 **[完了]** を選択します。

      ![サンプル ペイロードの入力](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   このサンプル ペイロードから生成されてトリガーに表示されるスキーマは次のとおりです。

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. キュー メッセージを受信した後に実行する他のアクションをすべて追加します。

   たとえば、Office 365 Outlook コネクタを使ってメールを送信できます。

1. ロジック アプリを保存します。これで、このロジック アプリのトリガーに使用するコールバック URL が生成されます。 このコールバック URL は、後で Azure Service Bus キューのトリガーのコードで使用します。

   このコールバック URL は、**HTTP POST URL** プロパティに表示されます。

   ![トリガー用に生成されたコールバック URL](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-a-function"></a>関数を作成する

次に、トリガーとして機能し、キューをリッスンする関数を作成します。

1. Azure portal で関数アプリを開いて展開します (まだ開いていない場合)。 

1. 該当する関数アプリ名の **[関数]** を展開します。 **[関数]** ウィンドウの **[新しい関数]** を選択します。

   ![[関数] を展開し、[新しい関数] を選択する](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. ランタイム スタックとして .NET を選択した新しい関数アプリを作成したか、または既存の関数アプリを使用しているかに基づいて、次のテンプレートを選択します。

   * 新しい関数アプリの場合は、次のテンプレートを選択します。**Service Bus キューのトリガー**

     ![新しい関数アプリのテンプレートを選択する](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * 既存の関数アプリの場合は、次のテンプレートを選択します。**Service Bus キューのトリガー - C#**

     ![既存の関数アプリのテンプレートを選択する](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. **[Azure Service Bus Queue trigger] (Azure Service Bus キューのトリガー)** ウィンドウで、トリガーの名前を指定し、キューの **Service Bus 接続** (これは Azure Service Bus SDK の `OnMessageReceive()` リスナーを使用します) を設定して **[作成]** を選択します。

1. キュー メッセージをトリガーとして使用して、前に作成したロジック アプリのエンドポイントを呼び出す基本的な関数を作成します。 関数を記述する前に、次の考慮事項を確認してください。

   * この例では、`application/json` メッセージのコンテンツの種類を使用していますが、必要に応じて種類を変更できます。
   
   * 関数が同時に実行される、ボリュームが大きくなる、あるいは負荷が高くなる可能性があるため、`using` ステートメントで [HTTPClient クラス](/dotnet/api/system.net.http.httpclient)をインスタンス化し、要求ごとに HTTPClient インスタンスを直接作成することは避けてください。 詳細については、「[HttpClientFactory を使用して回復力の高い HTTP 要求を実装する](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core)」を参照してください。
   
   * 可能な場合は、HTTP クライアントのインスタンスを再利用します。 詳細については、「[Azure Functions での接続の管理](../azure-functions/manage-connections.md)」を参照してください。

   この例では、[非同期](/dotnet/csharp/language-reference/keywords/async)モードで [`Task.Run` メソッド](/dotnet/api/system.threading.tasks.task.run)が使用されています。 詳細については、[Async および Await を使用した非同期プログラミング](/dotnet/csharp/programming-guide/concepts/async/)に関するページをご覧ください。

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Can also fetch from App Settings or environment variable
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible: https://docs.microsoft.com/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();

   public static async Task Run(string myQueueItem, TraceWriter log) 
   {
      log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
      var response = await httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")); 
   }
   ```

1. 関数をテストするには、[Service Bus エクスプ ローラー](https://github.com/paolosalvatori/ServiceBusExplorer)などのツールを使用してキュー メッセージを追加します。

   関数がメッセージを受信した直後にロジック アプリが起動します。

## <a name="next-steps"></a>次のステップ

* [HTTP エンドポイントを使用してワークフローを呼び出すか、トリガーするか、または入れ子にする](../logic-apps/logic-apps-http-endpoint.md)