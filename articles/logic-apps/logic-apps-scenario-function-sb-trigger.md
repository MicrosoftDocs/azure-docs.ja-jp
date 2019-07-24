---
title: シナリオ - Azure Functions と Azure Service Bus でロジック アプリをトリガーする | Microsoft Docs
description: Azure Functions と Azure Service Bus を使ってロジック アプリをトリガーする関数を作成します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 08/25/2018
ms.openlocfilehash: 1d3c4039ae823d3797e768af5892333d4d925268
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57789943"
---
# <a name="scenario-trigger-logic-apps-with-azure-functions-and-azure-service-bus"></a>シナリオ: Azure Functions と Azure Service Bus を使用してロジック アプリをトリガーする

Azure Functions を使用して、実行時間の長いリスナーまたはタスクをデプロイする必要がある場合に、ロジック アプリのトリガーを作成できます。 たとえば、キューをリッスンする関数を作成し、プッシュ トリガーとしてすぐにロジック アプリを起動することができます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。 

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識 

* Azure 関数を作成する前に、[関数アプリを作成](../azure-functions/functions-create-function-app-portal.md)します。

## <a name="create-logic-app"></a>ロジック アプリを作成する

この例では、トリガーする必要のあるロジック アプリごとに関数を実行します。 最初に、HTTP 要求トリガーのあるロジック アプリを作成します。 関数は、キュー メッセージが受信されたときに、そのエンドポイントを呼び出します。  

1. [Azure portal](https://portal.azure.com) にサインインして、空のロジック アプリを作成します。 

   ロジック アプリを初めて使用する場合は、[クイック スタート:初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

1. 検索ボックスに「HTTP 要求」と入力します。 トリガーの一覧で、トリガー **HTTP 要求の受信時**

   ![トリガーの選択](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

1. **要求**トリガーでは、キュー メッセージで使用する JSON スキーマを必要に応じて入力できます。 ロジック アプリ デザイナーは、JSON スキーマを通じて、入力データの構造を解釈することができます。また、ユーザーは、JSON スキーマがあることで、ワークフロー全体にわたって出力を選択しやすくなります。 

   スキーマを指定するには、**[要求本文の JSON スキーマ]** ボックスにスキーマを入力します。その例を次に示します。 

   ![JSON スキーマの指定](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   スキーマはなくても JSON 形式のサンプル ペイロードがあれば、そのペイロードからスキーマを生成することができます。

   1. 要求トリガーで **[サンプルのペイロードを使用してスキーマを生成する]** を選択します。

   1. **[サンプルの JSON ペイロードを入力するか、貼り付けます]** でサンプル ペイロードを入力し、**[完了]** を選択します。
      
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

1. キュー メッセージの受信後に実行したいアクションが他にもあれば追加します。 

   たとえば、Office 365 Outlook コネクタを使ってメールを送信できます。

1. ロジック アプリを保存します。これで、このロジック アプリのトリガーに使用するコールバック URL が生成されます。 この URL は、**[HTTP POST の URL]** プロパティに表示されます。

   ![トリガー用に生成されたコールバック URL](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Azure 関数を作成する

次に、トリガーとして機能し、キューをリッスンする関数を作成します。 

1. Azure portal で関数アプリを開いて展開します (まだ開いていない場合)。 

1. 該当する関数アプリ名の **[関数]** を展開します。 **[関数]** ウィンドウの **[新しい関数]** を選択します。 次のテンプレートを使用します。**Service Bus キューのトリガー - C#**
   
   ![Azure Functions ポータルを選択](./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png)

1. トリガーの名前を入力し、Service Bus キューへの接続を構成します。Azure Service Bus SDK の `OnMessageReceive()` リスナーを使います。

1. キュー メッセージをトリガーとして、先ほど作成したロジック アプリのエンドポイントを呼び出す基本的な関数を作成します。その例を次に示します。 
   
   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   // Re-use instance of http clients if possible - https://docs.microsoft.com/en-us/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       var response = httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
   }
   ```

   この例では、`application/json` メッセージのコンテンツの種類を使用していますが、必要に応じて種類を変更できます。

1. 関数をテストするには、[Service Bus エクスプ ローラー](https://github.com/paolosalvatori/ServiceBusExplorer)などのツールを使用してキュー メッセージを追加します。 

   関数がメッセージを受信した直後にロジック アプリが起動します。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

