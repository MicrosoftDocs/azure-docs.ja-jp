---
title: "HTTP エンドポイントを通じてロジック アプリを呼び出し、トリガーし、入れ子にする - Azure Logic Apps | Microsoft Docs"
description: "HTTP エンドポイントを追加して構成し、Azure のロジック アプリのワークフローを呼び出したり、トリガーしたり、入れ子にします"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 8c4cb0aefcd7241b17c6afcf33496c1fc2e6f675
ms.lasthandoff: 03/03/2017

---

# <a name="add-http-endpoints-to-call-trigger-or-nest-logic-app-workflows"></a>HTTP エンドポイントを追加して、ロジック アプリ ワークフローを呼び出したり、トリガーしたり、入れ子にする

ロジック アプリでは、同期 HTTP エンドポイントをトリガーとしてネイティブに公開することで、ロジック アプリを呼び出せるようにすることができます。 また、ロジック アプリに **[Logic Apps ワークフローを選択する]** アクションを追加すれば、呼び出し可能なエンドポイントのパターンを使用して、ロジック アプリを入れ子になったワークフローとして起動することもできます。

要求の受信には、次のトリガーを使用できます。

* 要求
* ApiConnectionWebhook
* HttpWebhook

このトピックでは、例として**要求**トリガーを使用していますが、原理はすべて、他の種類のトリガーにも同様に当てはまります。

## <a name="add-a-trigger-to-your-logic-app-definition"></a>ロジック アプリ定義にトリガーを追加する

1. ロジック アプリ デザイナーで、ロジック アプリ定義の受信要求を受信できるトリガーを追加します。 たとえば、ロジック アプリに**要求**トリガーを追加します。

2.    **[要求本文の JSON スキーマ]** で、受信するペイロードの JSON スキーマを入力できます。 スキーマが準備できていない場合は、**[サンプルのペイロードを使用してスキーマを生成する]** を選択して、サンプル ペイロードから JSON スキーマを生成できます。

    デザイナーは、このスキーマを使用してトークンを生成します。これらのトークンは、ワークフローを通じて手動トリガーからデータを消費したり、解析したり、渡したりするのに役立ちます。

    ![要求アクションを追加する][2]

2.    ロジック アプリ定義を保存した後、**[この URL に HTTP POST]** に、生成されたコールバック URL が表示されます。

    ``` text
    https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
    ```

    この URL には、認証に使用されるクエリ パラメーター内の Shared Access Signature (SAS) キーが含まれています。 
    このエンドポイントは、Azure Portal からも取得できます。

    ![URL エンドポイント][1]

    以下を呼び出して取得することもできます。

    ``` text
    POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
    ```

### <a name="change-the-http-method-for-your-trigger"></a>トリガーの HTTP メソッドを変更する

既定では、要求トリガーは HTTP POST 要求を待機します。 別の HTTP メソッドを構成するには、**[詳細オプションを表示する]** オプションを選択します。

> [!NOTE]
> 使用できるメソッドの種類は&1; つだけです。

### <a name="customize-the-relative-trigger-url"></a>相対 トリガー URL をカスタマイズする

パラメーターを受け取れるよう要求 URL の相対パスをカスタマイズすることもできます。

1. **要求**トリガーで、**[詳細オプションを表示する]** を選択します。 **[相対パス]** に、`customer/{customerId}` と入力します。

    ![相対 URL トリガー](./media/logic-apps-http-endpoint/relativeurl.png)

2.    パラメーターを使用するには、**応答**アクションを更新します。

    *    トークン ピッカーに `customerId` が表示されます。
    *    `Hello {customerId}` を返すには、**応答**アクションの本文を更新します。

    ![相対 URL 応答](./media/logic-apps-http-endpoint/relativeurlresponse.png)

3. ロジック アプリを保存し、 要求 URL が相対パスで更新されます。

4. 新しい要求 URL をコピーし、新しいブラウザー ウィンドウに貼り付けます。 `{customerId}` を `123` に置き換え、Enter キーを押します。

    次のテキストが返されます: `Your customer Id is 123`

### <a name="security-for-the-trigger-url"></a>トリガー URL のセキュリティ

ロジック アプリのコールバック URL は、Shared Access Signature (SAS) を使用して安全に生成されます。 この署名はクエリ パラメーターとして渡され、ロジック アプリが起動する前に検証される必要があります。 署名は、ロジック アプリごとの秘密キー、トリガー名、および実行される操作の一意の組み合わせによって生成されます。 ロジック アプリの秘密キーにアクセスできなければ、有効な署名を生成することはできません。

## <a name="call-your-logic-app-triggers-endpoint"></a>ロジック アプリ トリガーのエンドポイントを呼び出す

トリガーのエンドポイントを作成したら、完全な URL への `POST` を通じてロジック アプリをトリガーすることができます。 追加のヘッダーや、任意のコンテンツを本文に含めることもできます。 コンテンツの種類が `application/json` である場合は、要求内からプロパティを参照できます。 その他の場合、コンテンツは他の API に渡すことができる単一バイナリ ユニットとして扱われますが、コンテンツが変換されない限り、ワークフロー内部で参照することはできません。 たとえば、`application/xml` コンテンツを渡す場合は、`@xpath()` を使用して XPath 抽出を行ったり、`@json()` を使用して XML から JSON に変換することができます。 詳しくは、[コンテンツの種類](../logic-apps/logic-apps-content-type.md)に関する記事をご覧ください。

さらに、定義では JSON スキーマを指定できます。 このスキーマにより、デザイナーでトークンを生成し、それをステップに渡すことができます。 次の例では、`title` および `name` トークンをデザイナーで使用できるようにしています。

```
{
    "properties":{
        "title": {
            "type": "string"
        },
        "name": {
            "type": "string"
        }
    },
    "required": [
        "title",
        "name"
    ],
    "type": "object"
}
```

## <a name="reference-the-content-from-the-incoming-request"></a>受信要求からコンテンツを参照する

`@triggerOutputs()` 関数は、受信要求のコンテンツを出力します。 出力は次のようになります。

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

`body` プロパティだけにアクセスするには、`@triggerBody()` ショートカットを使用できます。 

## <a name="respond-to-the-request"></a>要求に応答する

ロジック アプリを起動する要求に対しては、何らかのコンテンツで呼び出し元に応答した方がよい場合があります。 応答の状態コード、本文、ヘッダーを作成するには、**応答**という種類のアクションを使用できます。 ただし、**応答**が含まれていない場合、ロジック アプリ エンドポイントは*直ちに* **202 Accepted** で応答します。

![HTTP 応答アクション][3]

``` json
"Response": {
        "conditions": [],
        "inputs": {
            "body": {
                "name": "@{triggerBody()['name']}", 
                    "title": "@{triggerBody()['title']}"
            },
            "headers": {
                "content-type": "application/json"
            },
            "statusCode": 200
        },
        "type": "Response"
}
```

応答には、次のプロパティがあります。

| プロパティ | Description |
| --- | --- |
| StatusCode |受信要求に応答するための HTTP 状態コード。 2xx、4xx、または 5xx で始まる任意の有効な状態コードを使用できます。 ただし、3xx 状態コードは指定できません。 |
| body |文字列、JSON オブジェクト、または前のステップから参照されるバイナリ コンテンツを指定できる body オブジェクト。 |
| headers |応答に含める任意の数のヘッダーを定義できます。 |

ロジック アプリでは、応答に必要なすべての手順が *60 秒*以内に完了しないと、元の要求が応答を受け取れません (*ワークフローを、入れ子になったロジック アプリとして呼び出した場合を除く*)。 60 秒以内に応答が返されなかった場合、受信要求はタイムアウトとなり、 **408 Client timeout** という HTTP 応答を受信します。 入れ子になったロジック アプリの場合は、待機時間にかかわらず、親ロジック アプリが応答の完了を待機します。

## <a name="advanced-endpoint-configuration"></a>エンドポイントの詳細構成

ロジック アプリでは、直接アクセス エンドポイントが組み込みでサポートされており、ロジック アプリの起動には常に `POST` メソッドが使用されます。 以前は、**HTTP リスナー**の API アプリで、URL セグメントと HTTP メソッドの変更もサポートされていました。 これらの項目を API アプリ ホスト (API アプリをホストしていた Web アプリ) に追加すれば、追加のセキュリティやカスタム ドメインをセットアップすることもできます。 

この機能は次の **API Management** を通じて利用できます。

* [要求メソッドを変更する](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [要求の URL セグメントを変更する](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* クラシック Azure ポータルの **[構成]** タブで API Management ドメインをセットアップする。
* 基本認証を確認するためのポリシーをセットアップする。

## <a name="summary-of-migration-from-2014-12-01-preview"></a>2014-12-01-preview からの移行の概要

| 2014-12-01-preview | 2016-06-01 |
| --- | --- |
| **HTTP リスナー** API アプリをクリックする |**[手動トリガー]** をクリックする (API アプリは不要) |
| HTTP リスナー設定 "*Sends response automatically*" |ワークフロー定義に**応答**アクションを含めるまたは含めない。 |
| 基本認証または OAuth 認証を構成する |API Management による |
| HTTP メソッドを構成する |API Management による |
| 相対パスを構成する |API Management による |
| `@triggerOutputs().body.Content` |`@triggerOutputs().body` |
| **Send HTTP response** アクション |**[HTTP 要求に応答]** をクリックする (API アプリは不要) |

[1]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[2]: ./media/logic-apps-http-endpoint/manualtrigger.png
[3]: ./media/logic-apps-http-endpoint/response.png

