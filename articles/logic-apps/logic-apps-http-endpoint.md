---
title: Request トリガーを使用し、ロジック アプリを呼び出し、トリガーし、入れ子にする
description: Azure Logic Apps でロジック アプリ ワークフローを呼び出し、トリガーし、または入れ子にする HTTPS エンドポイントを設定する
services: logic-apps
ms.workload: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 11/19/2020
ms.openlocfilehash: b345168dad63b1846d46c12721587eaffb5f887e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94981206"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-https-endpoints-in-azure-logic-apps"></a>Azure Logic Apps で HTTPS エンドポイントを使用して、ロジック アプリの呼び出し、トリガー、または入れ子を行います

ロジック アプリを URL 経由で呼び出せて、他のサービスから入ってくる要求を受信できるようにするため、ロジック アプリで要求ベースのトリガーを使用し、同期 HTTPS エンドポイントをネイティブで公開できます。 この機能があれば、他のロジック アプリから自分のロジック アプリを呼び出し、呼び出し可能エンドポイントのパターンを作成できます。 入ってくる呼び出しを処理するための呼び出し可能エンドポイントを設定するには、次のいずれかのトリガー タイプを使用できます。

* [Request](../connectors/connectors-native-reqres.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)
* [ApiConnectionWebhook 型](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)であり、受信 HTTPS 要求を受信できるマネージド コネクタ トリガー

この記事では、Request トリガーを使用してロジック アプリで呼び出し可能エンドポイントを作成し、別のロジック アプリからそのエンドポイントを呼び出す方法について説明します。 原則はすべて、入ってくる要求を受け取るための他のトリガー タイプに同じように適用されます。


[トランスポート層セキュリティ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) (以前の Secure Sockets Layer (SSL))、[Azure Active Directory Open Authentication (Azure AD OAuth)](../active-directory/develop/index.yml)、Azure API Management によるロジック アプリの公開、または受信呼び出しを発信する IP アドレスの制限などの、ロジック アプリへの受信呼び出しのセキュリティ、認可、および暗号化の詳細については、[アクセスとデータのセキュリティ保護に関するページの「要求ベースのトリガーへの受信呼び出しへのアクセス」](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* 呼び出し可能なエンドポイントを作成するトリガーを使用するロジック アプリ。 空のロジック アプリか、現在のトリガーを置き換える既存のロジック アプリから始めることができます。 この例では、空のロジック アプリから始めます。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」および[クイックスタート: 初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

## <a name="create-a-callable-endpoint"></a>呼び出し可能なエンドポイントを作成する

1. [Azure portal](https://portal.azure.com) にサインインします。 ロジック アプリ デザイナーで、空のロジック アプリを作成して開きます。

1. 検索ボックスで、 **[組み込み]** を選択します。 検索ボックスに、フィルターとして「`request`」と入力します。 トリガーの一覧から、 **[HTTP 要求の受信時]** を選択します。

   ![要求トリガーを見つけて選択する](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. 必要に応じて、 **[要求本文の JSON スキーマ]** ボックスで、トリガーで受信することが予測されるペイロードまたはデータが記述された JSON スキーマを入力できます。

   デザイナーは、このスキーマを使用して、トリガー出力を表すトークンを生成します。 その後は、これらの出力をロジック アプリのワークフロー全体で容易に参照できます。 詳細については、[JSON スキーマから生成されるトークン](#generated-tokens)に関するセクションを参照してください。

   この例では、次のスキーマを入力します。

   ```json
      {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "streetNumber": {
                  "type": "string"
               },
               "streetName": {
                  "type": "string"
               },
               "town": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "string"
               }
            }
         }
      }
   }
    ```

   ![要求アクションのための JSON スキーマを指定する](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   または、サンプル ペイロードを指定することによって JSON スキーマを生成できます。

   1. **[要求]** トリガーで、 **[サンプルのペイロードを使用してスキーマを生成する]** を選択します。

   1. **[サンプルの JSON ペイロードを入力するか、貼り付けます]** ボックスで、サンプル ペイロードを入力します。たとえば、次のようにします。

      ```json
      {
         "address": {
            "streetNumber": "00000",
            "streetName": "AnyStreet",
            "town": "AnyTown",
            "postalCode": "11111-1111"
        }
      }
      ```

   1. 準備ができたら、 **[完了]** を選択します。

      **[要求本文の JSON スキーマ]** ボックスに、生成されたスキーマが表示されます。

1. ロジック アプリを保存します。

   **[HTTP POST の URL]** ボックスに、他のサービスがロジック アプリの呼び出しやトリガーに使用できる生成されたコールバック URL が表示されます。 この URL には、認証に使用される Shared Access Signature (SAS) キーを指定するクエリ パラメーターが含まれています。

   ![エンドポイント用に生成されたコールバック URL](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

1. コールバック URL をコピーするには、次のオプションがあります。

   * **[HTTP POST の URL]** ボックスの右で **[URL のコピー]** (ファイル コピー アイコン) を選択します。

   * この呼び出しは、要求トリガーが想定するメソッドを使用して行います。 この例では、`POST` メソッドを使用します。

     `POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01`

   * ロジック アプリの **[概要]** ペインからコールバック URL をコピーします。

     1. ロジック アプリのメニューで、 **[概要]** を選択します。

     1. **[概要]** セクションで、 **[See trigger history] (トリガー履歴を表示する)** を選択します。

        ![Azure portal からエンドポイントの URL を取得する](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

     1. **[Callback url [POST]] (コールバック URL [POST])** で、URL をコピーします。

        ![Azure portal からエンドポイントの URL をコピーする](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url-post.png)

<a name="select-method"></a>

## <a name="select-expected-request-method"></a>待機する要求メソッドの選択

既定では、要求トリガーは `POST` 要求を待機します。 呼び出し元が使用する必要のある別のメソッドを指定できますが、指定できるメソッドは 1 つだけです。

1. 要求トリガーで、 **[新しいパラメーターの追加]** 一覧を開き、 **[メソッド]** を選択します。それにより、トリガーにこのプロパティが追加されます。

   ![トリガーに [メソッド] プロパティを追加する](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. **[メソッド]** 一覧から、代わりにトリガーで待機する別のメソッドを選択します。 または、カスタム メソッドを指定できます。

   たとえば、後でエンドポイントの URL をテストできるように **[GET]** メソッドを選択します。

   ![トリガーで待機する要求メソッドを選択する](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

<a name="endpoint-url-parameters"></a>

## <a name="pass-parameters-through-endpoint-url"></a>エンドポイント URL を使用してパラメーターを渡す

エンドポイントの URL 経由でパラメーター値を受け取るとき、次の選択肢が与えられます。

* [GET パラメーター経由で値を受け取る](#get-parameters)か、URL パラメーター経由で値を受け取ります。

  これらの値は、エンドポイントの URL で名前と値のペアとして渡されます。 このオプションでは、要求トリガーで GET メソッドを使用する必要があります。 後続のアクションでは、式で `triggerOutputs()` 関数を使用することで、パラメーター値をトリガー出力として取得できます。

* 要求トリガーのパラメーターに対して[相対パス経由で値を受け取ります](#relative-path)。

  これらの値は、エンドポイントの URL で相対パスから渡されます。 また、明示的に、トリガーで待機する[メソッドを選択する](#select-method)必要があります。 後続のアクションでは、これらの出力を直接参照することで、パラメーター値をトリガー出力として取得できます。

<a name="get-parameters"></a>

### <a name="accept-values-through-get-parameters"></a>GET パラメーター経由で値を受け取る

1. 要求トリガーで、 **[新しいパラメーターの追加]** 一覧を開き、 **[メソッド]** プロパティをトリガーに追加し、**GET** メソッドを選択します。

   詳細については、「[待機する要求メソッドの選択](#select-method)」を参照してください。

1. 要求トリガーの下で、パラメーター値を使用するアクションを追加します。 この例では、 **[応答]** アクションを追加します。

   1. 要求トリガーで、 **[新しいステップ]**  >  **[アクションの追加]** の順に選択します。
   
   1. **[アクションを選択してください]** の下の検索ボックス内に、フィルターとして「`response`」と入力します。 アクションの一覧から、 **[応答]** アクションを選択します。

1. パラメーター値を取得する `triggerOutputs()` 式を作成するには、次の手順を実行します。

   1. [応答] アクションの **Body** プロパティ内をクリックして動的コンテンツの一覧を表示し、 **[式]** を選択します。

   1. **[式]** ボックスにこの式を入力します。式の `parameter-name` は、自分のパラメーターの名前に変更します。 **[OK]** を選択します。

      `triggerOutputs()['queries']['parameter-name']`

      ![トリガーする "triggerOutputs()" 式を追加する](./media/logic-apps-http-endpoint/trigger-outputs-expression.png)

      **Body** プロパティでは、式が `triggerOutputs()` トークンに解決されます。

      ![解決された "triggerOutputs()" 式](./media/logic-apps-http-endpoint/trigger-outputs-expression-token.png)

      ロジック アプリを保存し、デザイナーから抜け、デザイナーに戻ると、たとえば次のように、指定したパラメーター名がトークンに表示されます。

      ![パラメーター名に対する解決された式](./media/logic-apps-http-endpoint/resolved-expression-parameter-token.png)

      コード ビューでは、**Body** プロパティが [応答] アクションの定義に次のように表示されます。

      `"body": "@{triggerOutputs()['queries']['parameter-name']}",`

      たとえば、`postalCode` という名前のパラメーターに値を渡すとします。 **Body** プロパティでは、文字列、`Postal Code: ` と末尾のスペース、それに続く対応する式が指定されます。

      ![トリガーする例 "triggerOutputs()" 式を追加する](./media/logic-apps-http-endpoint/trigger-outputs-expression-postal-code.png)

1. 呼び出し可能なエンドポイントをテストするには、要求トリガーからコールバック URL をコピーし、その URL を別のブラウザー ウィンドウに貼り付けます。 URL で、次の形式の URL の疑問符 (`?`) の後ろにパラメーターの名前と値を追加し、Enter キーを押します。

   `...?{parameter-name=parameter-value}&api-version=2016-10-01...`

   `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?{parameter-name=parameter-value}&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   ブラウザーから応答が返され、`Postal Code: 123456` というテキストが表示されます。

   ![コールバック URL に要求を送信したときの応答](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

1. URL 内の別の位置にパラメーターの名前と値を置くには、たとえば次のように、プレフィックスとして必ずアンパサンド (`&`) を使用します。

   `...?api-version=2016-10-01&{parameter-name=parameter-value}&...`

   次の例のコールバック URL では、サンプル パラメーターの名前と値 `postalCode=123456` が URL 内の別の位置にあります。

   * 1 つ目の位置: `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?postalCode=123456&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   * 2 つ目の位置: `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?api-version=2016-10-01&postalCode=123456&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

> [!NOTE]
> ハッシュまたはシャープ記号 ( **#** ) を URI に含める場合、エンコードされたバージョン `%25%23` を代わりに使用します。

<a name="relative-path"></a>

### <a name="accept-values-through-a-relative-path"></a>相対パス経由で値を受け取る

1. 要求トリガーで、 **[新しいパラメーターの追加]** 一覧を開き、 **[相対パス]** を選択します。それにより、トリガーにこのプロパティが追加されます。

   ![トリガーに [相対パス] プロパティを追加する](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. **[相対パス]** プロパティで、URL で受け入れるようにする JSON スキーマ内のパラメーターの相対パス (`/address/{postalCode}` など) を指定します。

   ![パラメーターの相対パスを指定する](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. 要求トリガーの下で、パラメーター値を使用するアクションを追加します。 この例では、 **[応答]** アクションを追加します。

   1. 要求トリガーで、 **[新しいステップ]**  >  **[アクションの追加]** の順に選択します。

   1. **[アクションを選択してください]** の下の検索ボックス内に、フィルターとして「`response`」と入力します。 アクションの一覧から、 **[応答]** アクションを選択します。

1. 応答アクションの **[本文]** プロパティで、トリガーの相対パスで指定したパラメーターを表すトークンを含めます。

   たとえば、応答アクションで `Postal Code: {postalCode}` を返すようにするとします。

   1. **[本文]** プロパティで、`Postal Code: ` を末尾にスペースを付けて入力します。 動的コンテンツの一覧が開いたままでいるように、編集ボックスの中にカーソルを置いたままにします。

   1. 動的コンテンツの一覧で、 **[HTTP 要求の受信時]** セクションから **postalCode** トークンを選択します。

      ![応答本文に指定したパラメーターを追加する](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

      **[本文]** プロパティに、選択されたパラメーターが含まれています。

      ![パラメーターを含む応答本文の例](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. ロジック アプリを保存します。

   要求トリガーでコールバック URL が更新され、たとえば次のように、相対パスが含まれます。

   `https://prod-07.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

1. 呼び出し可能なエンドポイントをテストするには、要求トリガーから更新後のコールバック URL をコピーし、その URL を別のブラウザー ウィンドウに貼り付け、URL の `{postalCode}` は `123456` に変更し、Enter キーを押します。

   ブラウザーから応答が返され、`Postal Code: 123456` というテキストが表示されます。

   ![コールバック URL に要求を送信したときの応答](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

> [!NOTE]
> ハッシュまたはシャープ記号 ( **#** ) を URI に含める場合、エンコードされたバージョン `%25%23` を代わりに使用します。

## <a name="call-logic-app-through-endpoint-url"></a>エンドポイント URL 経由でロジック アプリを呼び出す

エンドポイントを作成したら、そのエンドポイントの完全な URL に HTTPS 要求を送信することによってロジック アプリをトリガーできます。 ロジック アプリでは、直接アクセス エンドポイントの組み込みがサポートされています。

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>スキーマから生成されるトークン

要求トリガーで JSON スキーマを指定すると、ロジック アプリ デザイナーは、そのスキーマ内のプロパティ用のトークンを生成します。 これらのトークンは、ロジック アプリのワークフローを介してデータを渡すために使用できます。

たとえば、JSON スキーマにさらに多くのプロパティ (`"suite"` など) を追加した場合は、それらのプロパティ用のトークンをロジック アプリの後の手順で使用できます。 完全な JSON スキーマを次に示します。

```json
   {
   "type": "object",
   "properties": {
      "address": {
         "type": "object",
         "properties": {
            "streetNumber": {
               "type": "string"
            },
            "streetName": {
               "type": "string"
            },
            "suite": {
               "type": "string"
            },
            "town": {
               "type": "string"
            },
            "postalCode": {
               "type": "string"
            }
         }
      }
   }
}
```

## <a name="create-nested-logic-apps"></a>入れ子になったロジック アプリを作成する

要求を受信できる他のロジック アプリを追加することで、ロジック アプリにワークフローを入れ子にできます。 これらのロジック アプリを含めるには、次の手順に従います。

1. 別のロジック アプリを呼び出すステップで、 **[新しいステップ]**  >  **[アクションの追加]** の順に選択します。

1. **[アクションを選択してください]** で **[Built-in]\(組み込み\)** を選択します。 検索ボックスに、フィルターとして「`logic apps`」と入力します。 アクションの一覧から、 **[Logic Apps ワークフローを選択する]** を選択します。

   ![ロジック アプリを現在のロジック アプリの内部で入れ子にする](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   デザイナーには、選択の対象として有効なロジック アプリが表示されます。

1. 現在のロジック アプリから呼び出すロジック アプリを選択します。

   ![現在のロジック アプリから呼び出すロジック アプリを選択する](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>受信要求からコンテンツを参照する

受信要求のコンテンツの種類が `application/json` である場合は、受信要求内のプロパティを参照できます。 そうでない場合、このコンテンツは、他の API に渡すことができる 1 つのバイナリ ユニットとして扱われます。 このコンテンツをロジック アプリのワークフローの内部で参照するには、まず、そのコンテンツを変換する必要があります。

たとえば、`application/xml` の種類を持つコンテンツを渡す場合は、[`@xpath()` 式](../logic-apps/workflow-definition-language-functions-reference.md#xpath)を使用して XPath 抽出を実行するか、または [`@json()` 式](../logic-apps/workflow-definition-language-functions-reference.md#json)を使用して XML を JSON に変換することができます。 詳細については、サポートされている[コンテンツの種類](../logic-apps/logic-apps-content-type.md)を参照してください。

受信要求から出力を取得するには、[`@triggerOutputs` 式](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs)を使用できます。 たとえば、次の例のような出力があるとします。

```json
{
   "headers": {
      "content-type" : "application/json"
   },
   "body": {
      "myProperty" : "property value"
   }
}
```

明確に `body` プロパティにアクセスするには、[`@triggerBody()` 式](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody)をショートカットとして使用できます。

## <a name="respond-to-requests"></a>要求に応答する

ロジック アプリをトリガーする特定の要求に、呼び出し元にコンテンツを返すことによって応答したい場合があります。 応答の状態コード、ヘッダー、および本文を構築するには、応答アクションを使用します。 このアクションは、ワークフローの最後のみでなく、ロジック アプリの任意の場所に使用できます。 ロジック アプリに応答アクションが含まれていない場合、エンドポイントは **202 Accepted** 状態で *直ちに* 応答します。

元の呼び出し元が応答を正常に取得するには、トリガーされたロジック アプリが入れ子になったロジック アプリとして呼び出されていない限り、応答のためにすべての必要な手順が[要求タイムアウト制限](./logic-apps-limits-and-config.md)内に完了する必要があります。 この制限内に応答が返されない場合、受信要求はタイムアウトし、**408 Client timeout** 応答を受信します。

入れ子になったロジック アプリの場合は、必要な時間の長さには関係なく、親ロジック アプリが引き続きすべての手順が完了するまで応答を待ちます。

### <a name="construct-the-response"></a>応答を作成する

応答本文には、複数のヘッダーと任意の種類のコンテンツを含めることができます。 たとえば、次の応答のヘッダーは、このトピックで要求トリガーについて先に説明した JSON スキーマに基づいて、この応答のコンテンツの種類が `application/json` であり、本文に `town` および `postalCode` プロパティの値が含まれることを指定しています。

![HTTPS 応答アクションの応答コンテンツを指定する](./media/logic-apps-http-endpoint/content-for-response-action.png)

応答には、次のプロパティがあります。

| プロパティ (表示) | プロパティ (JSON) | 説明 |
|--------------------|-----------------|-------------|
| **状態コード** | `statusCode` | 受信要求への応答で使用する HTTPS 状態コード。 2xx、4xx、または 5xx で始まる任意の有効な状態コードを使用できます。 ただし、3xx 状態コードは指定できません。 |
| **ヘッダー** | `headers` | 応答に含める 1 つ以上のヘッダー |
| **本文** | `body` | 文字列、JSON オブジェクト、場合によっては前のステップから参照されるバイナリ コンテンツから成る本文オブジェクト |
||||

応答アクションの JSON 定義とロジック アプリの完全な JSON 定義を表示するには、ロジック アプリ デザイナーのツールバーで、 **[コード ビュー]** を選択します。

``` json
"Response": {
   "type": "Response",
   "kind": "http",
   "inputs": {
      "body": {
         "postalCode": "@triggerBody()?['address']?['postalCode']",
         "town": "@triggerBody()?['address']?['town']"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

## <a name="q--a"></a>Q & A

#### <a name="q-what-about-url-security"></a>Q:URL のセキュリティはどうなっていますか。

**A**: Azure では、[Shared Access Signature (SAS)](/rest/api/storageservices/delegate-access-with-shared-access-signature) を使用してロジック アプリのコールバック URL を安全に生成します。 この署名はクエリ パラメーターとして渡され、ロジック アプリを実行する前に検証される必要があります。 Azure では、ロジック アプリごとの秘密キー、トリガー名、および実行される操作の一意の組み合わせを使用して署名が生成されます。 そのため、ロジック アプリの秘密キーにアクセスできなければ、有効な署名を生成することはできません。

> [!IMPORTANT]
> セキュリティの強化された運用環境のシステムでは、次の理由により、ブラウザーから直接ロジック アプリを呼び出さないことを強くお勧めします。
>
> * 共有アクセス キーが URL に表示されます。
> * Azure Logic Apps の顧客にまたがる共有ドメインのために、セキュリティ コンテンツ ポリシーを管理できません。

[トランスポート層セキュリティ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) (以前の Secure Sockets Layer (SSL))、[Azure Active Directory Open Authentication (Azure AD OAuth)](../active-directory/develop/index.yml)、Azure API Management によるロジック アプリの公開、または受信呼び出しを発信する IP アドレスの制限などの、ロジック アプリへの受信呼び出しのセキュリティ、認可、および暗号化の詳細については、[アクセスとデータのセキュリティ保護に関するページの「要求ベースのトリガーへの受信呼び出しへのアクセス」](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)を参照してください。

#### <a name="q-can-i-configure-callable-endpoints-further"></a>Q:呼び出し可能なエンドポイントをさらに構成することは可能でしょうか。

**A**: はい。HTTPS エンドポイントは、[Azure API Management](../api-management/api-management-key-concepts.md) を通してより高度な構成をサポートしています。 このサービスでは、次のような、ロジック アプリを含むすべての API の一貫した管理、カスタム ドメイン名の設定、他の認証方法の使用などの機能も提供します。

* [要求メソッドを変更する](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [要求の URL セグメントを変更する](../api-management/api-management-transformation-policies.md#RewriteURL)
* [Azure portal](https://portal.azure.com/) で API Management ドメインをセットアップする
* 基本認証を確認するためのポリシーをセットアップする

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps を使用して、HTTPS 呼び出しを受信して応答する](../connectors/connectors-native-reqres.md)
* [Azure Logic Apps におけるアクセスとデータのセキュリティ保護 - 要求ベースのトリガーへの受信呼び出しへのアクセス](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
