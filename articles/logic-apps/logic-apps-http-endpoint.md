---
title: HTTP エンドポイントを使用してワークフローを呼び出すか、トリガーするか、または入れ子にする - Azure Logic Apps
description: HTTP エンドポイントを設定し、Azure Logic Apps のワークフローを呼び出したり、トリガーしたり、入れ子にします
services: logic-apps
ms.service: logic-apps
ms.workload: integration
author: ecfan
ms.author: klam
ms.reviewer: jehollan, klam, LADocs
manager: carmonm
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.openlocfilehash: f93e90ef442740e4fb17f166023fbe3d5f0bae66
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875952"
---
# <a name="call-trigger-or-nest-workflows-with-http-endpoints-in-azure-logic-apps"></a>Azure Logic Apps で HTTP エンドポイントを使用してワークフローを呼び出すか、トリガーするか、または入れ子にする

ロジック アプリで同期 HTTP エンドポイントをトリガーとしてネイティブに公開することで、URL からロジック アプリをトリガーしたり呼び出したりすることができます。 呼び出し可能なエンドポイントのパターンを使用して、ロジック アプリのワークフローをネストすることもできます。

HTTP エンドポイントを作成するには、ロジック アプリで受信要求を受信できるように以下のトリガーを追加できます。

* [要求](../connectors/connectors-native-reqres.md)

* [API 接続 Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [HTTP Webhook](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > 以下の例では**要求**トリガーを使用しますが、ここに示すどの HTTP トリガーでもご利用いただけます。また、すべての原則が他の種類のトリガーにも同様に適用されます。

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>ロジック アプリ用の HTTP エンドポイントを設定する

HTTP エンドポイントを作成するには、受信要求を受信できるトリガーを追加します。

1. [Azure Portal](https://portal.azure.com "Azure Portal") にサインインします。 ロジック アプリに移動して、ロジック アプリ デザイナーを開きます。

2. ロジック アプリに受信要求を受信させるトリガーを追加します。 たとえば、ロジック アプリに**要求**トリガーを追加します。

3.  **[要求本文の JSON スキーマ]** で、トリガーが受信するペイロード (データ) の JSON スキーマを必要に応じて入力できます。

    デザイナーは、このスキーマを使用してトークンを生成します。これらのトークンは、ロジック アプリで使用して、ワークフローを通じてトリガーからデータを消費したり、解析したり、渡したりすることができます。 
    詳細については、[JSON スキーマから生成されるトークン](#generated-tokens)に関するトピックを参照してください。

    この例では、デザイナーに表示される次のスキーマを入力します。

    ```json
    {
      "type": "object",
      "properties": {
        "address": {
          "type": "string"
        }
      },
      "required": [
        "address"
      ]
    }
    ```

    ![要求アクションを追加する][1]

    > [!TIP]
    > 
    > **[サンプルのペイロードを使用してスキーマを生成する]** を選択することで、[jsonschema.net](https://jsonschema.net/) や**要求**トリガーなどのツールから JSON のサンプル ペイロードのスキーマを生成することができます。 
    > サンプル ペイロードを入力し、 **[完了]** を選択します。

    たとえば、次のサンプル ペイロードを使用するとします。

    ```json
    {
       "address": "21 2nd Street, New York, New York"
    }
    ```

    これによって次のスキーマが生成されます。

    ```json
    {
       "type": "object",
       "properties": {
          "address": {
             "type": "string" 
          }
       }
    }
    ```

4.  ロジック アプリを保存し、 **[この URL に HTTP POST]** に、次の例のように、生成されたコールバック URL が表示されます。

    ![エンドポイント用に生成されたコールバック URL](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

    この URL には、認証に使用されるクエリ パラメーター内の Shared Access Signature (SAS) キーが含まれています。 
    Azure Portal で、ロジック アプリの概要から HTTP エンドポイントの URL を取得することもできます。 **[トリガーの履歴]** でトリガーを選択します。

    ![Azure Portal から HTTP エンドポイントの URL を取得する][2]

    または、次の呼び出しによって URL を取得できます。

    ```
    POST https://management.azure.com/{logic-app-resourceID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>トリガーの HTTP メソッドを変更する

既定では、**要求**トリガーには HTTP POST 要求が必要ですが、他の HTTP メソッドを使用することができます。 

> [!NOTE]
> 指定できるのは 1 つのメソッドの種類のみです。

1. **要求**トリガーで、 **[詳細オプションの表示]** を選択します。

2. **[メソッド]** 一覧を開きます。 この例では、後で HTTP エンドポイントの URL をテストできるように、 **[GET]** を選択します。

    > [!NOTE]
    > その他の HTTP メソッドを選択したり、ロジック アプリのカスタム メソッドを指定したりすることもできます。

    ![HTTP メソッドを変更する](./media/logic-apps-http-endpoint/change-method.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>HTTP エンドポイントの URL を使用してパラメーターを受け取る

HTTP エンドポイントの URL でパラメーターを受け取る場合は、トリガーの相対パスをカスタマイズします。

1. **要求**トリガーで、 **[詳細オプションの表示]** を選択します。 

2. **[メソッド]** に、要求で使用する HTTP メソッドを指定します。 この例では、メソッドをまだ選択していない場合は、HTTP エンドポイントの URL をテストできるように **GET** メソッドを選択します。

      > [!NOTE]
      > トリガーの相対パスを指定するときに、トリガーの HTTP メソッドも明示的に指定する必要があります。

3. **[相対パス]** に、`customers/{customerID}` など、URL で受け取るパラメーターの相対パスを指定します。

    ![HTTP メソッドとパラメーターの相対パスを指定する](./media/logic-apps-http-endpoint/relativeurl.png)

4. パラメーターを使用するには、**応答**アクションをロジック アプリに追加します (トリガーで、 **[新しい手順]**  >  **[アクションの追加]**  >  **[応答]** の順に選択します)。 

5. 応答の**本文**に、トリガーの相対パスで指定したパラメーターのトークンを入力します。

    たとえば、`Hello {customerID}` を返すには、応答の**本文**を `Hello {customerID token}` で更新します。 
    動的なコンテンツの一覧が表示され、`customerID` トークンが選択肢として表示されます。

    ![応答本文にパラメーターを追加する](./media/logic-apps-http-endpoint/relativeurlresponse.png)

    **本文**は次の例のようになります。

    ![パラメーターが追加された応答本文](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. ロジック アプリを保存し、 

    HTTP エンドポイントの URL に以下の例のような相対パスが含まれるようになりました。 

    https&#58;//prod-00.southcentralus.logic.azure.com/workflows/f90cb66c52ea4e9cabe0abf4e197deff/triggers/manual/paths/invoke/customers/{customerID}...

7. HTTP エンドポイントをテストするには、更新した URL をコピーして、別のブラウザー ウィンドウに貼り付けます。ただし、`{customerID}` を `123456` で置き換えて、Enter キーを押します。

    ブラウザーには次のテキストが表示されます。 

    `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>ロジック アプリ用に JSON スキーマから生成されるトークン

**要求**トリガーに JSON スキーマを指定すると、ロジック アプリ デザイナーでそのスキーマのプロパティ用のトークンが生成されます。 これらのトークンは、ロジック アプリのワークフローを介してデータを渡すために使用できます。

この例では、`title` プロパティと `name` プロパティを JSON スキーマに追加すると、そのトークンをワークフローの後の手順で使用できるようになります。 

完全な JSON スキーマを次に示します。

```json
{
   "type": "object",
   "properties": {
      "address": {
         "type": "string"
      },
      "title": {
         "type": "string"
      },
      "name": {
         "type": "string"
      }
   },
   "required": [
      "address",
      "title",
      "name"
   ]
}
```

## <a name="create-nested-workflows-for-logic-apps"></a>ロジック アプリ用に入れ子になったワークフローを作成する

要求を受信できる他のロジック アプリを追加することで、ロジック アプリでワークフローを入れ子にできます。 このようなロジック アプリを組み込むには、 **[Azure Logic Apps - Logic Apps ワークフローを選択する]** アクションをトリガーに追加します。 これで、対象のロジック アプリから選択できます。

![別のロジック アプリを追加する](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>HTTP エンドポイント経由でロジック アプリを呼び出しまたはトリガーする

HTTP エンドポイントを作成したら、完全な URL への `POST` メソッドを通じてロジック アプリをトリガーすることができます。 ロジック アプリでは、直接アクセス エンドポイントの組み込みがサポートされています。

> [!NOTE] 
> ロジック アプリを手動で実行するには、ロジック アプリ デザイナーまたはロジック アプリ コード ビューのツール バーで **[実行]** をクリックします。

## <a name="reference-content-from-an-incoming-request"></a>受信要求からコンテンツを参照する

コンテンツの種類が `application/json` である場合は、受信要求からプロパティを参照できます。 それ以外の場合は、コンテンツは他の API に渡すことができる単一バイナリ ユニットとして扱われます。 ワークフロー内でコンテンツを参照するには、このコンテンツを変換する必要があります。 たとえば、`application/xml` コンテンツを渡す場合は、`@xpath()` を使用して XPath 抽出を行ったり、`@json()` を使用して XML から JSON に変換することができます。 詳しくは、[コンテンツの種類](../logic-apps/logic-apps-content-type.md)に関する記事をご覧ください。

受信要求から出力を取得するには、`@triggerOutputs()` 関数を使用できます。 出力の例を次に示します。

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

`body` プロパティだけにアクセスするには、`@triggerBody()` ショートカットを使用できます。 

## <a name="respond-to-requests"></a>要求に応答する

ロジック アプリを起動する要求に対して、呼び出し元にコンテンツを返すことで応答した方がよい場合があります。 状態コード、ヘッダー、および応答の本文を構成するには、**応答**アクションを使用できます。 このアクションは、ワークフローの最後のみでなく、ロジック アプリの任意の場所に使用できます。

> [!NOTE] 
> ロジック アプリに**応答**が含まれていない場合は、HTTP エンドポイントが "*直ちに*" **202 Accepted** 状態で応答します。 また、元の要求で応答を受け取るには、応答に必要なすべての手順が[要求タイムアウト制限](./logic-apps-limits-and-config.md)以内に完了する必要があります (ワークフローを入れ子になったロジック アプリとして呼び出した場合を除く)。 この制限以内に応答が返されなかった場合、受信要求はタイムアウトになり、HTTP 応答 **408 Client timeout** を受信します。 入れ子になったロジック アプリの場合は、待機時間にかかわらず、親ロジック アプリが応答の完了を待機します。

### <a name="construct-the-response"></a>応答を作成する

応答本文には、複数のヘッダーと任意の種類のコンテンツを含めることができます。 この応答の例では、ヘッダーで、応答のコンテンツの種類として `application/json` を指定しています。 本文には、**要求**トリガー用に前もって更新した JSON スキーマに基づいて、`title` と `name` が含まれます。

![HTTP 応答アクション][3]

応答には、次のプロパティがあります。

| プロパティ | 説明 |
| --- | --- |
| StatusCode |受信要求に応答するための HTTP 状態コードを指定します。 2xx、4xx、または 5xx で始まる任意の有効な状態コードを使用できます。 ただし、3xx 状態コードは指定できません。 |
| headers |応答に含める任意の数のヘッダーを定義します。 |
| body |文字列、JSON オブジェクト、または前のステップから参照されるバイナリ コンテンツを指定できる body オブジェクトを指定します。 |

ここで、**応答**アクションの JSON スキーマの例を示します。

``` json
"Response": {
   "inputs": {
      "body": {
         "title": "@{triggerBody()?['title']}",
         "name": "@{triggerBody()?['name']}"
      },
      "headers": {
           "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {},
   "type": "Response"
}
```

> [!TIP]
> ロジック アプリの完全な JSON 定義を表示するには、ロジック アプリ デザイナーで、 **[コード ビュー]** を選択します。

## <a name="q--a"></a>Q & A

#### <a name="q-what-about-url-security"></a>Q:URL のセキュリティはどうなっていますか。

A:Azure では、ロジック アプリのコールバック URL が、Shared Access Signature (SAS) を使用して安全に生成されます。 この署名はクエリ パラメーターとして渡され、ロジック アプリが起動する前に検証される必要があります。 Azure では、ロジック アプリごとの秘密キー、トリガー名、および実行される操作の一意の組み合わせを使用して署名が生成されます。 そのため、ロジック アプリの秘密キーにアクセスできなければ、有効な署名を生成することはできません。

   > [!IMPORTANT]
   > 運用環境のセキュリティで保護されたシステムでは、次の理由により、ブラウザーから直接ロジック アプリを呼び出さないことを強くお勧めします。
   > 
   > * 共有アクセス キーが URL に表示されます。
   > * ロジック アプリのユーザー間でドメインが共有されるため、セキュリティで保護されたコンテンツ ポリシーを管理できません。

#### <a name="q-can-i-configure-http-endpoints-further"></a>Q:HTTP エンドポイントをさらに構成することは可能でしょうか。

A:はい、HTTP エンドポイントでは、[**API Management**](../api-management/api-management-key-concepts.md) を通じてより高度な構成をサポートしています。 このサービスでは、次のような、ロジック アプリを含むすべての API の一貫した管理、カスタム ドメイン名の設定、他の認証方法の使用などの機能も提供します。

* [要求メソッドを変更する](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#SetRequestMethod)
* [要求の URL セグメントを変更する](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#RewriteURL)
* [Azure Portal](https://portal.azure.com/ "Azure Portal") で API Management ドメインをセットアップする
* 基本認証を確認するためのポリシーをセットアップする

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>Q:2014 年 12 月 1 日のプレビューからスキーマが移行されたとき、どのように変更されましたか。

A:変更内容を以下にまとめました。

| 2014 年 12 月 1 日のプレビュー | 2016 年 6 月 1 日 |
| --- | --- |
| **HTTP リスナー** API アプリをクリックする |**[手動トリガー]** をクリックする (API アプリは不要) |
| HTTP リスナー設定 "*Sends response automatically*" |ワークフロー定義に**応答**アクションを含めるまたは含めない |
| 基本認証または OAuth 認証を構成する |API Management による |
| HTTP メソッドを構成する |**[詳細オプションの表示]** で HTTP メソッドを選択する |
| 相対パスを構成する |**[詳細オプションの表示]** で、相対パスを追加する |
| 受信本文を `@triggerOutputs().body.Content` を通じて参照する |`@triggerOutputs().body` を通じて参照する |
| **Send HTTP response** アクション |**[HTTP 要求に応答]** をクリックする (API アプリは不要) |

## <a name="get-help"></a>問い合わせ

[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)では、質問の投稿や質問への回答を行うことができるほか、他の Azure Logic Apps ユーザーがどのようなことを行っているかがわかります。

[Azure Logic Apps ユーザー フィードバック サイト](https://aka.ms/logicapps-wish)でアイデアへの投票やアイデアの投稿を行って、Azure Logic Apps とコネクタの改善にご協力ください。

## <a name="next-steps"></a>次の手順

* [ロジック アプリの定義を作成する](./logic-apps-author-definitions.md)
* [エラーと例外を処理する](./logic-apps-exception-handling.md)

[1]: ./media/logic-apps-http-endpoint/manualtrigger.png
[2]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[3]: ./media/logic-apps-http-endpoint/response.png