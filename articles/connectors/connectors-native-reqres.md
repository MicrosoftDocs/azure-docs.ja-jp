---
title: HTTPS を使用して呼び出しを受信して応答する
description: Azure Logic Apps を使用して外部サービスからの受信 HTTPS 要求を処理する
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
tags: connectors
ms.openlocfilehash: ae34840c04c3a1d2fb3646046792c97ed6f521a0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289436"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Azure Logic Apps で受信 HTTPS 要求を受信して応答する

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) および組み込みの Request トリガーと Response アクションを使用すると、受信 HTTPS 要求を受け取ってそれに応答する、自動化されたタスクおよびワークフローを作成することができます。 たとえば、次のようなロジック アプリを作成できます。

* オンプレミス データベース内のデータに対する HTTPS 要求を受信して応答する。

* 外部 Webhook イベントが発生したときにワークフローをトリガーする。

* 別のロジック アプリからの HTTPS 呼び出しを受信して応答する。

Request トリガーは、ロジック アプリへの受信呼び出しの承認のために [Azure Active Directory Open Authentication](../active-directory/develop/index.yml) (Azure AD OAuth) をサポートしています。 この認証を有効にする方法の詳細については、[Azure Logic Apps でのセキュリティで保護されたアクセスとデータ - Azure AD OAuth 認証を有効にする](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth)方法に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)できます。

* [ロジック アプリ](../logic-apps/logic-apps-overview.md)に関する基本的な知識。 ロジック アプリを初めて使用する場合は、[初めてのロジック アプリを作成する方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)を学習してください。

<a name="tls-support"></a>

## <a name="transport-layer-security-tls"></a>トランスポート層セキュリティ (TLS)

* 受信呼び出しでは、トランスポート層セキュリティ (TLS) 1.2 *のみ*をサポートしています。 TLS ハンドシェイク エラーが発生する場合は、TLS 1.2 を使用していることを確認してください。 詳細については、[TLS 1.0 の問題の解決](/security/solving-tls1-problem) を参照してください。 送信呼び出しでは、ターゲット エンドポイントの機能に応じて TLS 1.0、1.1、1.2 をサポートしています。

* 受信呼び出しでは、次の暗号スイートをサポートしています。

  * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384

  * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256

  * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384

  * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256

  * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384

  * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256

  * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

  * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

<a name="add-request"></a>

## <a name="add-request-trigger"></a>要求トリガーの追加

この組み込みトリガーでは、受信 HTTPS 要求*のみ*を受け取ることができる、手動で呼び出し可能な HTTPS エンドポイントが作成されます。 このイベントが発生すると、トリガーが起動してロジック アプリが実行されます。 トリガーの基になる JSON 定義と、このトリガーの呼び出し方法の詳細については、[Request タイプのトリガー](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)に関するページ、および「[Azure Logic Apps で HTTPS エンドポイントを使用してワークフローを呼び出すか、トリガーするか、または入れ子にする](../logic-apps/logic-apps-http-endpoint.md)」を参照してください。

1. [Azure portal](https://portal.azure.com) にサインインします。 空のロジック アプリを作成します。

1. ロジック アプリ デザイナーが開いたら、検索ボックスにフィルターとして「`http request`」と入力します。 トリガーの一覧から、 **[HTTP 要求の受信時]** トリガーを選択します。これは、ロジック アプリ ワークフローでの最初のステップです。

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

      ![ペイロードからスキーマを生成する](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. サンプル ペイロードを入力して、 **[完了]** を選択します。

      ![ペイロードからスキーマを生成する](./media/connectors-native-reqres/enter-payload.png)

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

   ご利用のロジック アプリでは、[制限された時間](../logic-apps/logic-apps-limits-and-config.md#request-limits)のみ受信要求が開いたままになります。 ご利用のロジック アプリのワークフローに Response アクションが含まれていると仮定すると、この時間が経過してもロジック アプリから応答がない場合、ご利用のロジック アプリは呼び出し元に `504 GATEWAY TIMEOUT` を返します。 あるいは、ご利用のロジック アプリに Response アクションが含まれていない場合、ご利用のロジック アプリは呼び出し元にすぐに `202 ACCEPTED` 応答を返します。

1. 完了したら、ロジック アプリを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

   このステップでは、ロジック アプリをトリガーする要求を送信する場合に使用する URL を生成します。 この URL をコピーするには、URL の横にあるコピー アイコンを選択します。

   ![ご利用のロジック アプリをトリガーする際に使用する URL](./media/connectors-native-reqres/generated-url.png)

   > [!NOTE]
   > 要求トリガーを呼び出すとき、ハッシュまたはシャープ記号 ( **#** ) を URI に含める場合、エンコードされたバージョン `%25%23` を代わりに使用します。

1. ご利用のロジック アプリをトリガーするには、生成された URL に HTTP POST を送信します。

   たとえば、[Postman](https://www.getpostman.com/) などのツールを使用して HTTP POST を送信できます。 Request トリガーへの受信呼び出しを承認するために [Azure Active Directory Open Authentication](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth) (Azure AD OAuth) を有効にした場合は、[Shared Access Signature (SAS) URL](../logic-apps/logic-apps-securing-a-logic-app.md#sas) または認証トークンのどちらかを使用してトリガーを呼び出します。両方を使用することはできません。 認証トークンには、Authorization ヘッダーで `Bearer` 型を指定する必要があります。 詳細については、「[Azure Logic Apps におけるアクセスとデータのセキュリティ保護 - 要求ベースのトリガーへのアクセス](../logic-apps/logic-apps-securing-a-logic-app.md#secure-triggers)」を参照してください。

トリガーの基になる JSON 定義と、このトリガーの呼び出し方法の詳細については、このトピックス、[Request タイプのトリガー](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)に関するページ、および「[Azure Logic Apps で HTTP エンドポイントを使用してワークフローを呼び出すか、トリガーするか、または入れ子にする](../logic-apps/logic-apps-http-endpoint.md)」を参照してください。

### <a name="trigger-outputs"></a>トリガー出力

Request トリガーからの出力の詳細を次に示します。

| JSON プロパティ名 | データ型 | 説明 |
|--------------------|-----------|-------------|
| `headers` | Object | 要求のヘッダーを記述する JSON オブジェクト |
| `body` | Object | 要求の本文のコンテンツを記述する JSON オブジェクト |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Response アクションを追加する

Response アクションを使用すると、受信 HTTPS 要求に対してペイロード (データ) で応答することができますが、これは HTTPS 要求によってトリガーされるロジック アプリでのみ可能です。 Response アクションは、ご自分のワークフロー内の任意のポイントに追加できます。 このトリガーの基になる JSON 定義の詳細については、[Response タイプのアクション](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action)に関するページを参照してください。

ご利用のロジック アプリでは、[制限された時間](../logic-apps/logic-apps-limits-and-config.md#request-limits)のみ受信要求が開いたままになります。 ご利用のロジック アプリのワークフローに Response アクションが含まれていると仮定すると、この時間が経過してもロジック アプリから応答がない場合、ご利用のロジック アプリは呼び出し元に `504 GATEWAY TIMEOUT` を返します。 あるいは、ご利用のロジック アプリに Response アクションが含まれていない場合、ご利用のロジック アプリは呼び出し元にすぐに `202 ACCEPTED` 応答を返します。

> [!IMPORTANT]
> Response アクションにこれらのヘッダーが含まれている場合、Logic Apps は警告もエラーも表示せずに、生成された応答メッセージからこれらのヘッダーを削除します。
>
> * `Allow`
> * `Content-*`ただし、`Content-Disposition`、`Content-Encoding`、および `Content-Type` は例外です。
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

1. **[アクションの選択]** の下にある [検索] ボックスに、ご自分のフィルターとして「応答」と入力し、 **[Response]** アクションを選択します。

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

* [Logic Apps のコネクタ](../connectors/apis-list.md)

