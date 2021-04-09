---
title: HTTPS を使用して呼び出しを受信して応答する
description: Azure Logic Apps を使用して外部サービスからの受信 HTTPS 要求を処理する
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 11/19/2020
tags: connectors
ms.openlocfilehash: 83ffccb7bae4fabc10796c36e782e72c661bd346
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99063014"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Azure Logic Apps で受信 HTTPS 要求を受信して応答する

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) および組み込みの Request トリガーと Response アクションを使用すると、HTTPS 経由で受信要求を受信できる自動化されたタスクとワークフローを作成できます。 代わりに送信要求を送信するには、組み込みの [HTTP トリガーまたは HTTP アクション](../connectors/connectors-native-http.md)を使用します。

たとえば、次のようなロジック アプリを作成できます。

* オンプレミス データベース内のデータに対する HTTPS 要求を受信して応答する。

* 外部 Webhook イベントが発生したときにワークフローをトリガーする。

* 別のロジック アプリからの HTTPS 呼び出しを受信して応答する。

この記事では、ロジック アプリが受信呼び出しを受信してそれに応答できるように Request トリガーと Response アクションを使用する方法について説明します。

[トランスポート層セキュリティ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) (以前の Secure Sockets Layer (SSL))、[Azure Active Directory Open Authentication (Azure AD OAuth)](../active-directory/develop/index.yml)、Azure API Management によるロジック アプリの公開、または受信呼び出しを発信する IP アドレスの制限などの、ロジック アプリへの受信呼び出しのセキュリティ、認可、および暗号化の詳細については、[アクセスとデータのセキュリティ保護に関するページの「要求ベースのトリガーへの受信呼び出しへのアクセス」](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)できます。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

<a name="add-request"></a>

## <a name="add-request-trigger"></a>要求トリガーの追加

この組み込みトリガーは、HTTPS 経由で受信要求 *のみ* を処理できる、手動で呼び出し可能なエンドポイントを作成します。 呼び出し元がこのエンドポイントに要求を送信すると、[Request トリガー](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)が起動され、ロジック アプリが実行されます。 このトリガーを呼び出す方法の詳細については、[Azure Logic Apps での HTTPS エンドポイントを使用したワークフローの呼び出し、トリガー、または入れ子](../logic-apps/logic-apps-http-endpoint.md)に関するページを参照してください。

ロジック アプリは、受信要求を[限られた時間](../logic-apps/logic-apps-limits-and-config.md#http-limits)だけ開いたままにします。 ロジック アプリに [Response アクション](#add-response)が含まれていると仮定すると、この時間が経過してもロジック アプリが呼び出し元に応答を戻さない場合、そのロジック アプリは呼び出し元に `504 GATEWAY TIMEOUT` 状態を返します。 ロジック アプリに Response アクションが含まれていない場合、ロジック アプリによって呼び出し元にすぐに `202 ACCEPTED` 状態が返されます。

1. [Azure portal](https://portal.azure.com) にサインインします。 空のロジック アプリを作成します。

1. ロジック アプリ デザイナーが開いたら、検索ボックスにフィルターとして「`http request`」と入力します。 トリガーの一覧から、 **[HTTP 要求の受信時]** トリガーを選択します。

   ![Request トリガーを選択する](./media/connectors-native-reqres/select-request-trigger.png)

   Request トリガーでは、次のプロパティが表示されます。

   ![Request トリガー](./media/connectors-native-reqres/request-trigger.png)

   | プロパティ名 | JSON プロパティ名 | 必須 | 説明 |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST の URL** | {なし} | はい | ロジック アプリを保存したら生成され、ご自分のロジック アプリの呼び出しに使用されるエンドポイント URL |
   | **要求本文の JSON スキーマ** | `schema` | いいえ | 受信要求本文内のプロパティと値を記述する JSON スキーマ |
   |||||

1. **[要求本文の JSON スキーマ]** ボックスに、必要に応じて、受信要求内の本文を記述する JSON スキーマを入力します。次に例を示します。

   ![JSON スキーマの例](./media/connectors-native-reqres/provide-json-schema.png)

   デザイナーでは、このスキーマを使用して、要求内のプロパティ用のトークンが生成されます。 このようにして、ご利用のロジック アプリでは要求からのデータを解析し、使用し、トリガー経由でご利用のワークフローに渡すことができます。

   スキーマのサンプルを次に示します。

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   JSON スキーマを入力すると、デザイナーによってリマインダーが表示され、ご自分の要求に `Content-Type` ヘッダーを含め、そのヘッダー値を `application/json` に設定するように促されます。 詳細については、[コンテンツ タイプの処理](../logic-apps/logic-apps-content-type.md)に関するページを参照してください。

   ![Content-Type ヘッダーを含めるように促すリマインダー](./media/connectors-native-reqres/include-content-type.png)

   JSON 形式では、このヘッダーは次のようになります。

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   予期されるペイロード (データ) に基づく JSON スキーマを生成するには、[JSONSchema.net](https://jsonschema.net) などのツールを使用するか、または次の手順を行います。

   1. 要求トリガーで **[サンプルのペイロードを使用してスキーマを生成する]** を選択します。

      ![[サンプルのペイロードを使用してスキーマを生成する] が選択されたスクリーンショット](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. サンプル ペイロードを入力して、 **[完了]** を選択します。

      ![スキーマを生成するサンプル ペイロードを入力する](./media/connectors-native-reqres/enter-payload.png)

      サンプル ペイロードを次に示します。

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": {
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. 指定したスキーマに一致する要求本文が受信呼び出しに含まれることを確認するには、次の手順に従います。

   1. 要求トリガーのタイトル バーにある省略記号 ( **...** ) ボタンを選択します。

   1. トリガーの設定で **[スキーマの検証]** をオンにし、 **[完了]** を選択します。

      受信呼び出しの要求本文がスキーマと一致しない場合、トリガーからは `HTTP 400 Bad Request` エラーが返されます。

1. 追加のプロパティを指定するには、 **[新しいパラメーターの追加]** リストを開き、追加するパラメーターを選択します。

   | プロパティ名 | JSON プロパティ名 | 必須 | 説明 |
   |---------------|--------------------|----------|-------------|
   | **方法** | `method` | いいえ | ロジック アプリを呼び出すために受信要求で使用する必要があるメソッド |
   | **相対パス** | `relativePath` | いいえ | ロジック アプリのエンドポイント URL で受け入れ可能なパラメーターの相対パス |
   |||||

   この例では、 **[メソッド]** プロパティを追加します。

   ![メソッド パラメーターを追加する](./media/connectors-native-reqres/add-parameters.png)

   **[メソッド]** プロパティがトリガー内に表示されるので、一覧からメソッドを選択できます。

   ![メソッドを選択する](./media/connectors-native-reqres/select-method.png)

1. ここで、ご利用のワークフローに次のステップとして別のアクションを追加します。 トリガーの下にある **[次のステップ]** を選択すると、追加するアクションを見つけることができます。

   たとえば、[Response アクションを追加](#add-response)することによって、要求に応答することができます。Response アクションは、カスタマイズした応答を返す場合に使用できます。これについては、このトピックの後半で説明します。

   ご利用のロジック アプリでは、[制限された時間](../logic-apps/logic-apps-limits-and-config.md#http-limits)のみ受信要求が開いたままになります。 ご利用のロジック アプリのワークフローに Response アクションが含まれていると仮定すると、この時間が経過してもロジック アプリから応答がない場合、ご利用のロジック アプリは呼び出し元に `504 GATEWAY TIMEOUT` を返します。 あるいは、ご利用のロジック アプリに Response アクションが含まれていない場合、ご利用のロジック アプリは呼び出し元にすぐに `202 ACCEPTED` 応答を返します。

1. 完了したら、ロジック アプリを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

   このステップでは、ロジック アプリをトリガーする要求を送信する場合に使用する URL を生成します。 この URL をコピーするには、URL の横にあるコピー アイコンを選択します。

   ![ご利用のロジック アプリをトリガーする際に使用する URL](./media/connectors-native-reqres/generated-url.png)

   > [!NOTE]
   > 要求トリガーを呼び出すとき、ハッシュまたはシャープ記号 ( **#** ) を URI に含める場合、エンコードされたバージョン `%25%23` を代わりに使用します。

1. ご利用のロジック アプリをテストするには、生成された URL に HTTP 要求を送信します。

   たとえば、[Postman](https://www.getpostman.com/) などのツールを使用して HTTP 要求を送信できます。 トリガーの基になる JSON 定義と、このトリガーの呼び出し方法の詳細については、このトピックス、[Request タイプのトリガー](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)に関するページ、および「[Azure Logic Apps で HTTP エンドポイントを使用してワークフローを呼び出すか、トリガーするか、または入れ子にする](../logic-apps/logic-apps-http-endpoint.md)」を参照してください。

[トランスポート層セキュリティ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) (以前の Secure Sockets Layer (SSL))、[Azure Active Directory Open Authentication (Azure AD OAuth)](../active-directory/develop/index.yml)、Azure API Management によるロジック アプリの公開、または受信呼び出しを発信する IP アドレスの制限などの、ロジック アプリへの受信呼び出しのセキュリティ、認可、および暗号化の詳細については、[アクセスとデータのセキュリティ保護に関するページの「要求ベースのトリガーへの受信呼び出しへのアクセス」](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)を参照してください。

## <a name="trigger-outputs"></a>トリガー出力

Request トリガーからの出力の詳細を次に示します。

| JSON プロパティ名 | データ型 | 説明 |
|--------------------|-----------|-------------|
| `headers` | Object | 要求のヘッダーを記述する JSON オブジェクト |
| `body` | Object | 要求の本文のコンテンツを記述する JSON オブジェクト |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Response アクションを追加する

Request トリガーを使用して受信要求を処理する場合は、応答をモデル化し、組み込みの [Response アクション](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action)を使用してペイロードの結果を呼び出し元に戻すことができます。 Response アクションは、Request トリガーと共に *のみ* 使用できます。 この Request トリガーと Response アクションの組み合わせによって、[要求 - 応答パターン](https://en.wikipedia.org/wiki/Request%E2%80%93response)が作成されます。 Response アクションは、Foreach ループと Until ループや並列分岐の内部を除き、ワークフロー内のどこにでも追加できます。

> [!IMPORTANT]
> Response アクションにこれらのヘッダーが含まれている場合、Logic Apps は警告もエラーも表示せずに、生成された応答メッセージからこれらのヘッダーを削除します。
>
> * `Allow`
> * `Content-Disposition`、`Content-Encoding`、および `Content-Type` を除く `Content-*` ヘッダー (POST と PUT 操作を使用する場合。ただし GET 操作については含まれない)
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Logic Apps では、これらのヘッダーのある Response アクションを持つロジック アプリを保存することは妨げられませんが、Logic Apps ではこれらのヘッダーは無視されます。

1. ロジック アプリ デザイナーで、Response アクションを追加するステップの下にある **[新しいステップ]** を選択します。

   たとえば、前述の Request トリガーを使用している場合は次のようになります。

   ![新規ステップの追加](./media/connectors-native-reqres/add-response.png)

   ステップの間にアクションを追加するには、該当するステップ間の矢印の上にポインターを移動します。 表示されるプラス記号 ( **+** ) を選択してから、 **[アクションの追加]** を選択します。

1. **[アクションの選択]** で、検索ボックスにフィルターとして「`response`」と入力し、 **[Response]** アクションを選択します。

   ![Response アクションを選択する](./media/connectors-native-reqres/select-response-action.png)

   この例では、わかりやすくするため Request トリガーは折りたたまれています。

1. 応答メッセージに必要なすべての値を追加します。

   フィールドによっては、ボックス内をクリックすると動的なコンテンツの一覧が表示されます。 次に、ワークフローの前のステップからの使用可能な出力を表すトークンを選択できます。 前の例で指定したスキーマからのプロパティは、動的コンテンツの一覧に表示されるようになりました。

   たとえば、 **[ヘッダー]** ボックスでは、キー名として `Content-Type` を指定し、キー値についてはこのトピックで既に説明したように `application/json` に設定します。 **[本文]** ボックスでは、動的コンテンツの一覧からトリガー本文の出力を選択できます。

   ![Response アクションの詳細](./media/connectors-native-reqres/response-details.png)

   ヘッダーを JSON 形式で表示するには、 **[Switch to text view]\(テキスト ビューに切り替え\)** を選択します。

   ![ヘッダー - テキスト ビューへの切り替え](./media/connectors-native-reqres/switch-to-text-view.png)

   Response アクション内に設定できるプロパティの詳細を次に示します。

   | プロパティ名 | JSON プロパティ名 | 必須 | 説明 |
   |---------------|--------------------|----------|-------------|
   | **状態コード** | `statusCode` | はい | 応答で返される状態コード |
   | **ヘッダー** | `headers` | いいえ | 応答に含める 1 つまたは複数のヘッダーを記述する JSON オブジェクト |
   | **本文** | `body` | いいえ | 応答本文 |
   |||||

1. 応答本文用の JSON スキーマなど、追加のプロパティを指定するには、 **[新しいパラメーターの追加]** リストを開いて、追加するパラメーターを選択します。

1. 完了したら、ロジック アプリを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

* [アクセスとデータのセキュリティ保護 - 要求ベースのトリガーへの受信呼び出しへのアクセス](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [Logic Apps のコネクタ](../connectors/apis-list.md)
