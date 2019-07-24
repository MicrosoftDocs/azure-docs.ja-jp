---
title: Azure Functions を使用して Azure Logic Apps にカスタム コードを追加して実行する | Microsoft Docs
description: Azure Functions を使用して Azure Logic Apps にカスタム コード スニペットを追加して実行する方法について説明します
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 08/20/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 2bec33a4a8540f9599cf1d479f1f59c4cde39bd2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57861589"
---
# <a name="add-and-run-custom-code-snippets-in-azure-logic-apps-with-azure-functions"></a>Azure Functions を使用して Azure Logic Apps にカスタム コード スニペットを追加して実行する

ロジック アプリで特定のジョブを実行するための最小限のコードを実行する場合は、[Azure Functions](../azure-functions/functions-overview.md) を使用して独自の関数を作成することができます。 このサービスを利用して Node.js、C#、および F# コード スニペットを作成できるので、完全なアプリやコードを実行するためのインフラストラクチャを構築する必要はありません。 Azure Functions は、クラウド内でサーバーレス コンピューティングを実現します。次のようなタスクを実行するために便利です。

* Node.js または C# で、関数を使用してロジック アプリの動作を拡張します。
* ロジック アプリのワークフローで計算を実行します。
* ロジック アプリのフィールドで高度な書式設定や計算を行います。

また、[Azure 関数内からロジック アプリを呼び出す](#call-logic-app)こともできます。

## <a name="prerequisites"></a>前提条件

この記事に沿って作業を行うには、次の項目が必要です。

* Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。 

* Azure 関数のコンテナーである Azure 関数アプリと Azure 関数。 関数アプリを持っていない場合は、[まず関数アプリを作成します](../azure-functions/functions-create-first-azure-function.md)。 次に、ロジック アプリ デザイナーで、[ロジック アプリの外部に個別に](#create-function-external)、または[ロジック アプリの内部に](#create-function-designer)、関数を作成することができます。

  既存の場合でも新規の場合でも、関数アプリおよび関数がロジック アプリと連携するための要件は、次のとおりです。

  * 関数アプリは、ロジック アプリと同じ Azure サブスクリプションを保持している必要があります。

  * お使いの関数で、**JavaScript** または **C#** の **HTTP トリガー**関数テンプレートなどの HTTP トリガーを使用します。 

    HTTP トリガー テンプレートは、ロジック アプリから `application/json` 型のコンテンツを受け入れることができます。 
    Azure 関数をロジック アプリに追加した場合、Logic App Designer ではお使いの Azure サブスクリプション内にこのテンプレートから作成したカスタム関数が表示されます。 

  * 以前は [Swagger ファイル](https://swagger.io/)として知られていた [OpenAPI 定義](../azure-functions/functions-openapi-definition.md)を定義済みでない限り、お使いの関数ではカスタム ルートを使用しません。 
  
  * 関数の OpenAPI 定義を定義済みの場合、Logic Apps Designer では、関数パラメーターを操作するための豊富なエクスペリエンスが提供されます。 OpenAPI 定義を含む関数をロジック アプリが見つけてアクセスできるようにするには、[以下の手順に従って関数アプリを設定](#function-swagger)します。

* ロジック アプリの最初の手順として[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)を含む、関数の追加先となるロジック アプリ 

  関数を実行できるアクションを追加可能にする前に、ロジック アプリがトリガーで始まる必要があります。

  ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」と[クイック スタートの初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

<a name="create-function-external"></a>

## <a name="create-functions-outside-logic-apps"></a>ロジック アプリの外部に関数を作成する

<a href="https://portal.azure.com" target="_blank">Azure portal</a> で、Azure 関数アプリを作成します。このアプリは、ロジック アプリと同じ Azure サブスクリプションを持つ必要があります。その後、Azure 関数を作成します。
Azure 関数の初心者の方は、[Azure Portal で初めての関数を作成する](../azure-functions/functions-create-first-azure-function.md)方法を確認してください。ただし、ロジック アプリから呼び出しできる関数を作成するための次の要件に注意してください。

* **JavaScript** または **C#** のいずれかの **HTTP トリガー**関数テンプレートを選択していることを確認します。

  ![HTTP トリガー - JavaScript または C#](./media/logic-apps-azure-functions/http-trigger-function.png)

<a name="function-swagger"></a>

* 必要に応じて、関数のために、以前 [Swagger ファイル](https://swagger.io/)と呼ばれていた [API 定義を生成](../azure-functions/functions-openapi-definition.md)すると、ロジック アプリ デザイナーでより豊富なエクスペリエンスを使用して関数パラメーターを操作できます。 お使いのロジック アプリで Swagger の記述を含む関数を検索して使用できるように、関数アプリを設定するために、次の手順を実行します。

  1. 関数アプリがアクティブに実行されていることを確認します。

  2. お使いの関数アプリで、次の手順に従ってすべてのオリジンが許可されるように、[Cross-Origin Resource Sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) を設定します。

     1. **[Function App]** の一覧から、[関数アプリ]、**[プラットフォーム機能]** > **[CORS]** の順に選択します。

        ![関数アプリ、[プラットフォーム機能]、[CORS] の順に選択する](./media/logic-apps-azure-functions/function-platform-features-cors.png)

     2. **[CORS]** でワイルドカード文字 `*` を追加しますが、リスト内の他のすべてのオリジンを削除し、**[保存]** を選択します。

        !["CORS* にワイルドカード文字 "*" を設定します。](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

### <a name="access-property-values-inside-http-requests"></a>HTTP 要求の内部でプロパティ値にアクセスする

Webhook 関数は、入力として HTTP 要求を受け取り、それらの要求を他の関数に渡すことができます。 たとえば、Logic Apps には [DateTime 値を変換する関数](../logic-apps/workflow-definition-language-functions-reference.md)がありますが、この基本的なサンプル JavaScript 関数は、関数に渡された要求オブジェクト内のプロパティにアクセスしてそのプロパティ値を操作する方法を示しています。 オブジェクト内のプロパティにアクセスするために、この例では[ドット (.) 演算子](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)を使用しています。 

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

この関数の内部で何が起きるかを、次に示します。

1. 関数は `data` 変数を作成し、`request` オブジェクト内の `body` オブジェクトをその変数に割り当てます。 この関数はドット (.) 演算子を使用して、`request` オブジェクト内の `body` オブジェクトを参照します。 

   ```javascript
   var data = request.body;
   ```

2. これで、この関数は `data` 変数を介して `date` プロパティにアクセスし、`ToDateString()` 関数を呼び出すことによってそのプロパティ値を DateTime 型から DateString 型に変換することができます。 関数は、関数の応答の `body` プロパティを介して、結果も返します。 

   ```javascript
   body: data.date.ToDateString();
   ```

Azure 関数を作成できたので、[ロジック アプリに関数を追加する](#add-function-logic-app)方法の手順に従ってください。

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>ロジック アプリの内部で関数を作成する

ロジック アプリ デザイナーで、ロジック アプリ内から開始される Azure 関数を作成するには、まず Azure 関数アプリを用意する必要があります。このアプリは、関数のコンテナーです。 関数アプリを持っていない場合は、まず関数アプリを作成します。 「[Azure Portal で初めての関数を作成する](../azure-functions/functions-create-first-azure-function.md)」を参照してください。 

1. <a href="https://portal.azure.com" target="_blank">Azure portal</a> のロジック アプリ デザイナーでロジック アプリを開きます。 

2. 関数を作成して追加するには、自身のシナリオに適用されるステップに従います。

   * ロジック アプリのワークフローの最後のステップで、**[新しいステップ]** を選択します。

   * ロジック アプリのワークフローの既存のステップ間で、矢印の上にマウスを移動して、プラス (+) 記号を選択し、**[アクションの追加]** を選択します。

3. 検索ボックスで、フィルターとして「azure functions」と入力します。
アクションの一覧から、次のアクションを選択します。**[Azure 関数を選択する - Azure Functions]** アクションを選択します。 

   !["Azure functions" を探す](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. 関数アプリの一覧で、関数アプリを選択します。 アクションの一覧が開いたら、次のアクションを選択します。**Azure Functions - 新しい関数を作成する**

   ![関数アプリを選択する](./media/logic-apps-azure-functions/select-function-app-create-function.png)

5. 関数定義エディターで、関数を定義します。

   1. **[関数名]** ボックスで関数の名前を指定します。 

   2. **[コード]** ボックスで、コードを関数テンプレートに追加します。関数の実行が終了した後にロジック アプリに返す応答とペイロードも含めます。 

      ![関数を定義する](./media/logic-apps-azure-functions/function-definition.png)

      テンプレートのコードでは、*`context` オブジェクト*は、以降のステップでロジック アプリが **Request Body** フィールド経由で送信するメッセージを参照します。 
      関数の内部から `context` オブジェクトのプロパティにアクセスするには、次の構文を使用します。 

      `context.body.<property-name>`

      たとえば、`context` オブジェクト内の `content` プロパティを参照するには、次の構文を使用します。 

      `context.body.content`

      また、テンプレート コードには、`input` 変数が含まれます。この変数は、お使いの関数が該当の値に対する操作を実行できるように、`data` パラメーターからの値を格納します。 
      また、JavaScript 関数の中で、`data` 変数は `context.body` のショートカットです。

      > [!NOTE]
      > この場合の `body` プロパティは `context` オブジェクトに適用され、アクションの出力からの **Body** トークンと同じではありません。お使いの関数にも渡すことができます。 
 
   3. 操作が完了したら、**[作成]** を選択します。

6. **[要求本文]** ボックスで、関数の入力を指定します。書式は JavaScript Object Notation (JSON) オブジェクトにする必要があります。 

   この入力は、ロジック アプリが関数に送信する*コンテキスト オブジェクト*またはメッセージです。 **[要求本文]** フィールドをクリックすると、動的コンテンツの一覧が表示され、前のステップからの出力のトークンを選択できます。 この例では、コンテキスト ペイロードが、電子メール トリガーからの **From** トークンの値を保持する `content` という名前のプロパティを含んでいることを示しています。

   ![[要求本文] の例 - コンテキスト オブジェクトのペイロード](./media/logic-apps-azure-functions/function-request-body-example.png)

   ここで、コンテキスト オブジェクトは文字列としてキャストされないため、オブジェクトのコンテンツは JSON ペイロードに直接追加されます。 ただし、コンテキスト オブジェクトが文字列、JSON オブジェクト、または JSON 配列を渡す JSON トークンでない場合は、エラーが発生します。 そのため、この例で代わりに **Received Time** トークンを使用した場合は、二重引用符を追加することで、コンテキスト オブジェクトを文字列としてキャストできます。  

   ![オブジェクトを文字列としてキャストする](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

7. 使用するメソッド、要求ヘッダー、クエリ パラメーターなど、その他の詳細を指定するには、**[詳細オプションを表示する]** を選択します。

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>ロジック アプリに既存の関数を追加する

ロジック アプリから既存の Azure 関数を呼び出すには、ロジック アプリ デザイナーで他のアクションと同様に Azure 関数を追加します。 

1. <a href="https://portal.azure.com" target="_blank">Azure portal</a> のロジック アプリ デザイナーでロジック アプリを開きます。 

2. 関数を追加するステップの下で、**[新しいステップ]** > **[アクションの追加]** の順にクリックします。 

3. 検索ボックスで、フィルターとして「azure functions」と入力します。
アクションの一覧から、次のアクションを選択します。**[Azure 関数を選択する - Azure Functions]** アクションを選択します。 

   !["Azure functions" を探す](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. 関数アプリの一覧で、関数アプリを選択します。 関数の一覧が表示されたら、関数を選択します。 

   ![関数アプリと Azure 関数を選択する](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   API 定義 (Swagger 記述) を持ち、[ロジック アプリが探してアクセスできるように設定された](#function-swagger)関数の場合は、**[Swagger アクション]** を選択することができます。

   ![関数アプリ、[Swagger アクション]、および Azure 関数を選択する](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

5. **[要求本文]** ボックスで、関数の入力を指定します。書式は JavaScript Object Notation (JSON) オブジェクトにする必要があります。 

   この入力は、ロジック アプリが関数に送信する*コンテキスト オブジェクト*またはメッセージです。 **[要求本文]** フィールドをクリックすると、動的コンテンツの一覧が表示され、前のステップからの出力のトークンを選択できます。 この例では、コンテキスト ペイロードが、電子メール トリガーからの **From** トークンの値を保持する `content` という名前のプロパティを含んでいることを示しています。

   ![[要求本文] の例 - コンテキスト オブジェクトのペイロード](./media/logic-apps-azure-functions/function-request-body-example.png)

   ここで、コンテキスト オブジェクトは文字列としてキャストされないため、オブジェクトのコンテンツは JSON ペイロードに直接追加されます。 ただし、コンテキスト オブジェクトが文字列、JSON オブジェクト、または JSON 配列を渡す JSON トークンでない場合は、エラーが発生します。 そのため、この例で代わりに **Received Time** トークンを使用した場合は、二重引用符を追加することで、コンテキスト オブジェクトを文字列としてキャストできます。 

   ![オブジェクトを文字列としてキャストする](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

6. 使用するメソッド、要求ヘッダー、クエリ パラメーターなど、その他の詳細を指定するには、**[詳細オプションを表示する]** を選択します。

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>関数からロジック アプリを呼び出す

Azure 関数の内部からロジック アプリをトリガーする場合、ロジック アプリは、呼び出し可能なエンドポイントを提供するトリガーから始まる必要があります。 たとえば、**HTTP**、**Request**、**Azure Queues**、または **Event Grid** トリガーでロジック アプリを開始できます。 次に、関数の内部から、そのトリガーの URL に HTTP POST 要求を送信し、ロジック アプリで処理するペイロードを含めます。 詳細については、「[ロジック アプリを呼び出し、トリガーし、入れ子にする](../logic-apps/logic-apps-http-endpoint.md)」をご覧ください。 

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* [Logic Apps のコネクタ](../connectors/apis-list.md)について確認します。
