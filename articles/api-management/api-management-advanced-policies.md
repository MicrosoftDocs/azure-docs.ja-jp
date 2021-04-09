---
title: Azure API Management の高度なポリシー | Microsoft Docs
description: Azure API Management で使用できる高度なポリシーについて説明します。 例を参照し、使用可能なその他のリソースを確認します。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/13/2020
ms.author: apimpm
ms.openlocfilehash: 03529fd3c0231617c477f4f16773039a02386683
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103562486"
---
# <a name="api-management-advanced-policies"></a>API Management の高度なポリシー

このトピックでは、次の API Management ポリシーについて説明します。 ポリシーを追加および構成する方法については、「 [Azure API Management のポリシー](./api-management-policies.md)」をご覧ください。

## <a name="advanced-policies"></a><a name="AdvancedPolicies"></a> 高度なポリシー

-   [制御フロー](api-management-advanced-policies.md#choose) - ブール[式](api-management-policy-expressions.md)の評価の結果に基づいてポリシー ステートメントを条件付きで適用します。
-   [要求を転送する](#ForwardRequest) - バックエンド サービスに要求を転送します。
-   [コンカレンシーを制限する](#LimitConcurrency) - 含まれているポリシーが指定された数を超える要求によって同時に実行されないようにします。
-   [イベント ハブにログを記録する](#log-to-eventhub) - 指定された形式のメッセージを Logger エンティティによって定義されたイベント ハブに送信します。
-   [Mock response (モック応答)](#mock-response) - パイプラインの実行を中止し、モック応答を呼び出し元に直接返します。
-   [再試行](#Retry) - 条件が満たされるまで、囲まれたポリシー ステートメントの実行を再試行します。 実行は、指定された間隔で、指定された最大試行回数まで繰り返されます。
-   [応答を返す](#ReturnResponse) - パイプラインの実行を中止し、指定された応答を呼び出し元に直接返します。
-   [1 方向の要求を送信する](#SendOneWayRequest) - 指定された URL に要求を送信します。応答は待機しません。
-   [要求を送信する](#SendRequest) - 指定された URL に要求を送信します。
-   [HTTP プロキシを設定する](#SetHttpProxy) - HTTP プロキシ経由で、転送された要求をルーティングできます。
-   [要求メソッドを設定する](#SetRequestMethod) - 要求の HTTP メソッドを変更できます。
-   [状態コードを設定する](#SetStatus) - HTTP 状態コードを指定された値に変更します。
-   [変数の設定](api-management-advanced-policies.md#set-variable) - 名前付き[コンテキスト](api-management-policy-expressions.md#ContextVariables)変数の値を、後でアクセスできるように保持します。
-   [トレース](#Trace) - [API Inspector](./api-management-howto-api-inspector.md) 出力、Application Insights テレメトリ、リソース ログにカスタム トレースを追加します。
-   [待機](#Wait) - 含まれている[要求を送信する](api-management-advanced-policies.md#SendRequest)、[キャッシュからの値の取得](api-management-caching-policies.md#GetFromCacheByKey)、または[制御フロー](api-management-advanced-policies.md#choose) ポリシーが完了するまで待機してから次に進みます。

## <a name="control-flow"></a><a name="choose"></a> 制御フロー

`choose` ポリシーは、プログラミング言語の if-then-else や switch 構造のように、ブール式の評価結果に基づいて、含まれているポリシー ステートメントを適用します。

### <a name="policy-statement"></a><a name="ChoosePolicyStatement"></a> ポリシー ステートメント

```xml
<choose>
    <when condition="Boolean expression | Boolean constant">
        <!— one or more policy statements to be applied if the above condition is true  -->
    </when>
    <when condition="Boolean expression | Boolean constant">
        <!— one or more policy statements to be applied if the above condition is true  -->
    </when>
    <otherwise>
        <!— one or more policy statements to be applied if none of the above conditions are true  -->
</otherwise>
</choose>
```

制御フロー ポリシーには、1 つ以上の `<when/>` 要素が含まれている必要があります。 `<otherwise/>` 要素は省略可能です。 `<when/>` 要素内の条件は、ポリシーに記述されている順序で評価されます。 条件属性が `true` と等しい最初の `<when/>` 要素に含まれているポリシー ステートメントが適用されます。 `<otherwise/>` 要素 (存在する場合) に含まれているポリシーは、`<when/>` 要素の条件属性がすべて `false` の場合に適用されます。

### <a name="examples"></a>例

#### <a name="example"></a><a name="ChooseExample"></a> 例

次の例は、[set-variable](api-management-advanced-policies.md#set-variable) ポリシーと 2 つの制御フローのポリシーを示しています。

変数設定ポリシーは受信セクションにあり、`isMobile` 要求ヘッダーに `User-Agent` または `iPad` のテキストが含まれている場合に true に設定される `iPhone` ブール[コンテキスト](api-management-policy-expressions.md#ContextVariables)変数を作成します。

最初の制御フロー ポリシーも inbound セクションにあり、`isMobile` コンテキスト変数の値に応じて 2 つの[クエリ文字列パラメーターの設定](api-management-transformation-policies.md#SetQueryStringParameter)ポリシーのうち 1 つを条件付きで適用します。

2 番目の制御フロー ポリシーは outbound セクションにあり、`isMobile` が `true` に設定されている場合に [XML から JSON への変換](api-management-transformation-policies.md#ConvertXMLtoJSON)ポリシーを条件付きで適用します。

```xml
<policies>
    <inbound>
        <set-variable name="isMobile" value="@(context.Request.Headers.GetValueOrDefault("User-Agent","").Contains("iPad") || context.Request.Headers.GetValueOrDefault("User-Agent","").Contains("iPhone"))" />
        <base />
        <choose>
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <set-query-parameter name="mobile" exists-action="override">
                    <value>true</value>
                </set-query-parameter>
            </when>
            <otherwise>
                <set-query-parameter name="mobile" exists-action="override">
                    <value>false</value>
                </set-query-parameter>
            </otherwise>
        </choose>
    </inbound>
    <outbound>
        <base />
        <choose>
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <xml-to-json kind="direct" apply="always" consider-accept-header="false"/>
            </when>
        </choose>
    </outbound>
</policies>
```

#### <a name="example"></a>例

次の例に、バックエンド サービスから受信した応答で `Starter` 製品が使用されている場合にデータ要素を削除して、内容のフィルター処理を行う方法を示します。 このポリシーの構成と使用についてのデモは、「[Cloud Cover Episode 177:More API Management Features](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)」(クラウド カバー エピソード 177: その他の API Management 機能の紹介) を 34:30 まで早送りしてご覧ください。 このデモで使用されている [Dark Sky Forecast API](https://developer.forecast.io/) の概要について確認する場合は、31:50 から再生してください。

```xml
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->
<choose>
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">
    <set-body>@{
        var response = context.Response.Body.As<JObject>();
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {
          response.Property (key).Remove ();
        }
        return response.ToString();
      }
    </set-body>
  </when>
</choose>
```

### <a name="elements"></a>要素

| 要素   | 説明                                                                                                                                                                                                                                                               | 必須 |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| choose    | ルート要素。                                                                                                                                                                                                                                                             | はい      |
| when      | `choose` ポリシーの `if` または `ifelse` の部分に使用する条件。 `choose` ポリシーに複数の `when` セクションがある場合、これらのセクションは順番に評価されます。 when 要素のいずれかの `condition` が `true` に評価されると、それ以降の `when` 条件は評価されません。 | Yes      |
| otherwise | `when` 条件のいずれも `true` に評価されない場合に使用されるポリシー スニペットが含まれます。                                                                                                                                                                               | No       |

### <a name="attributes"></a>属性

| 属性                                              | 説明                                                                                               | 必須 |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| condition="ブール式 &#124; ブール型定数" | 含んでいる `when` ポリシー ステートメントが評価されるときに評価されるブール式または定数。 | Yes      |

### <a name="usage"></a><a name="ChooseUsage"></a> 使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound、outbound、backend、on-error

-   **ポリシー スコープ:** すべてのスコープ

## <a name="forward-request"></a><a name="ForwardRequest"></a> 要求を転送する

`forward-request` ポリシーは、要求[コンテキスト](api-management-policy-expressions.md#ContextVariables)に指定されたバックエンド サービスに要求を転送します。 バックエンド サービスの URL は API [設定](./import-and-publish.md)で指定され、[バックエンド サービスの設定](api-management-transformation-policies.md)ポリシーを使用して変更できます。

> [!NOTE]
> このポリシーを削除すると、要求はバックエンド サービスに転送されず、inbound セクションのポリシーが正常に完了した時点で outbound セクションのポリシーが即座に評価されます。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<forward-request timeout="time in seconds" follow-redirects="false | true" buffer-request-body="false | true" buffer-response="true | false" fail-on-error-status-code="false | true"/>
```

### <a name="examples"></a>例

#### <a name="example"></a>例

次の API レベル ポリシーは、バックエンド サービスに転送されたすべての API 要求が 60 秒でタイムアウトすることを示します。

```xml
<!-- api level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="60"/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>例

この操作レベル ポリシーは、`base` 要素を使用して、親 API レベル スコープからバックエンド ポリシーを継承します。

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <base/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>例

この操作レベル ポリシーは、バックエンド サービスに転送された要求が 120 秒でタイムアウトすることを示し、親 API レベル バックエンド ポリシーを継承しません。 バックエンド サービスが 400 から 599 まで (両端の値を含む) のエラー状態コードで応答する場合は、[on-error](api-management-error-handling-policies.md) セクションがトリガーされます。

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="120" fail-on-error-status-code="true" />
        <!-- effective policy. note the absence of <base/> -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>例

この操作レベル ポリシーは、バックエンド サービスに要求を転送しません。

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <!-- no forwarding to backend -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

### <a name="elements"></a>要素

| 要素         | 説明   | 必須 |
| --------------- | ------------- | -------- |
| forward-request | ルート要素。 | はい      |

### <a name="attributes"></a>属性

| 属性                                     | 説明                                                                                                                                                                                                                                                                                                    | 必須 | Default |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| timeout="整数"                             | バックエンド サービスによって返される HTTP 応答ヘッダーを待機する秒単位の時間。この時間を過ぎると、タイムアウト エラーが発生します。 最小値は 0 秒です。 この時間を過ぎると基盤となるネットワーク インフラストラクチャによってアイドル接続がドロップされる可能性があるため、240 秒より大きい値は受け入れられません。 | いいえ       | なし    |
| follow-redirects="false &#124; true"          | バックエンド サービスからのリダイレクトについて、その後にゲートウェイが続くか、それとも呼び出し元に返されるかを指定します。                                                                                                                                                                                                    | いいえ       | false   |
| buffer-request-body="false &#124; true"       | "true" に設定した場合、要求がバッファーされ、[再試行](api-management-advanced-policies.md#Retry)で再利用されます。                                                                                                                                                                                               | いいえ       | false   |
| buffer-response="false &#124; true" | チャンクされた応答の処理に影響します。 "false" に設定すると、バックエンドから受け取った各チャンクが即時に呼び出し元に返されます。 "true" に設定すると、チャンクがバッファーされます (ストリームの末尾が検出されない場合は 8 KB)。その後にのみ、呼び出し元に返されます。 | いいえ | true |
| fail-on-error-status-code="false &#124; true" | true に設定すると、400 から 599 まで (両端の値を含む) の範囲の応答コードについて、[on-error](api-management-error-handling-policies.md) セクションがトリガーされます。                                                                                                                                                                      | いいえ       | false   |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** backend
-   **ポリシー スコープ:** すべてのスコープ

## <a name="limit-concurrency"></a><a name="LimitConcurrency"></a>コンカレンシーを制限する

`limit-concurrency` ポリシーは、含まれているポリシーがいずれかの時点で指定された数を超える要求によって実行されないようにします。 その数を超えた場合は、新しい要求は 429 Too Many Requests (要求が多すぎます) のステータス コードですぐに失敗します。

### <a name="policy-statement"></a><a name="LimitConcurrencyStatement"></a> ポリシー ステートメント

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>例

#### <a name="example"></a>例

次の例は、コンテキスト変数の値に基づいてバックエンドに転送される要求の数を制限する方法を示しています。

```xml
<policies>
  <inbound>…</inbound>
  <backend>
    <limit-concurrency key="@((string)context.Variables["connectionId"])" max-count="3">
      <forward-request timeout="120"/>
    </limit-concurrency>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>要素

| 要素           | 説明   | 必須 |
| ----------------- | ------------- | -------- |
| limit-concurrency | ルート要素。 | はい      |

### <a name="attributes"></a>属性

| 属性 | 説明                                                                                        | 必須 | Default |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| key       | 文字列。 式を使用できます。 コンカレンシー スコープを指定します。 複数のポリシーで共有できます。 | はい      | 該当なし     |
| max-count | 整数。 ポリシーに入力できる要求の最大数を指定します。           | はい      | 該当なし     |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound、outbound、backend、on-error

-   **ポリシー スコープ:** すべてのスコープ

## <a name="log-to-event-hub"></a><a name="log-to-eventhub"></a> イベント ハブにログを記録する

`log-to-eventhub` ポリシーは、指定された形式のメッセージを Logger エンティティによって定義されたイベント ハブに送信します。 その名前が示すように、このポリシーは、オンラインまたはオフライン分析のために、選択された要求または応答コンテキスト情報を保存するために使用します。

> [!NOTE]
> イベント ハブの構成とイベントのログ記録に関する詳細な手順については、[Azure Event Hubs で API Management イベントを記録する方法](./api-management-howto-log-event-hubs.md)に関するページを参照してください。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>例

イベント ハブに記録する値として、任意の文字列を使用できます。 この例では、すべての着信コールの日付と時刻、デプロイ サービス名、要求 ID、IP アドレス、および操作名が、`contoso-logger` ID で登録されたイベント ハブ ロガーに記録されます

```xml
<policies>
  <inbound>
    <log-to-eventhub logger-id ='contoso-logger'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name) )
    </log-to-eventhub>
  </inbound>
  <outbound>
  </outbound>
</policies>
```

### <a name="elements"></a>要素

| 要素         | 説明                                                                     | 必須 |
| --------------- | ------------------------------------------------------------------------------- | -------- |
| log-to-eventhub | ルート要素。 この要素の値は、イベント ハブに記録する文字列です。 | はい      |

### <a name="attributes"></a>属性

| 属性     | 説明                                                               | 必須                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| logger-id     | API Management サービスに登録されているロガーの ID。         | Yes                                                                  |
| partition-id  | メッセージが送信されるパーティションのインデックスを指定します。             | 省略可能。 `partition-key` を使用する場合はこの属性を使用できません。 |
| partition-key | メッセージの送信時にパーティション割り当てに使用される値を指定します。 | 省略可能。 `partition-id` を使用する場合はこの属性を使用できません。  |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound、outbound、backend、on-error

-   **ポリシー スコープ:** すべてのスコープ

## <a name="mock-response"></a><a name="mock-response"></a> モック応答

`mock-response` は名前が示すとおり、API と操作の模擬テストを実行するために使用します。 通常のパイプライン実行を中止し、モック応答を呼び出し元に返します。 ポリシーは常に、再現性が最も高い応答を返そうとします。 使用可能な場合は常に、応答コンテキストの例が優先されます。 スキーマが提供され、例が提供されていない場合、ポリシーはスキーマからサンプルの応答を生成します。 例もスキーマも見つからない場合、コンテキストなしの応答が返されます。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<mock-response status-code="code" content-type="media type"/>

```

### <a name="examples"></a>例

```xml
<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this
status code. First found content type is used. If no example or schema is found, the content is empty. -->
<mock-response/>

<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this
status code and media type. If no example or schema found, the content is empty. -->
<mock-response status-code='200' content-type='application/json'/>
```

### <a name="elements"></a>要素

| 要素       | 説明   | 必須 |
| ------------- | ------------- | -------- |
| mock-response | ルート要素。 | はい      |

### <a name="attributes"></a>属性

| 属性    | 説明                                                                                           | 必須 | Default |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| status-code  | 応答の状態コードを指定し、対応する例またはスキーマを選択するために使用します。                 | いいえ       | 200     |
| content-type | `Content-Type` 応答のヘッダー値を指定し、対応する例またはスキーマを選択するために使用します。 | いいえ       | なし    |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound、outbound、on-error

-   **ポリシー スコープ:** すべてのスコープ

## <a name="retry"></a><a name="Retry"></a> 再試行

`retry` ポリシーは子ポリシーを 1 回実行し、再試行 `condition` が `false` になるか再試行 `count` に達するまで、実行を再試行します。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml

<retry
    condition="boolean expression or literal"
    count="number of retry attempts"
    interval="retry interval in seconds"
    max-interval="maximum retry interval in seconds"
    delta="retry interval delta in seconds"
    first-fast-retry="boolean expression or literal">
        <!-- One or more child policies. No restrictions -->
</retry>

```

### <a name="example"></a>例

次の例では、要求の転送が、指数再試行アルゴリズムを使用して 10 回まで再試行されます。 `first-fast-retry` が false に設定されているため、すべての再試行が指数再試行アルゴリズムの対象になります。

```xml

<retry
    condition="@(context.Response.StatusCode == 500)"
    count="10"
    interval="10"
    max-interval="100"
    delta="10"
    first-fast-retry="false">
        <forward-request buffer-request-body="true" />
</retry>

```

### <a name="elements"></a>要素

| 要素 | 説明                                                         | 必須 |
| ------- | ------------------------------------------------------------------- | -------- |
| retry   | ルート要素。 他のポリシーを子要素として含めることができます。 | はい      |

### <a name="attributes"></a>属性

| 属性        | 説明                                                                                                                                           | 必須 | Default |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| condition        | 再試行を停止する (`false`) か続行する (`true`) かを指定するブール型リテラルまたは[式](api-management-policy-expressions.md)。      | はい      | 該当なし     |
| count            | 最大再試行回数を指定する正の数。                                                                                | はい      | 該当なし     |
| interval         | 再試行の間の待機間隔を指定する正の数 (秒単位)。                                                                 | はい      | 該当なし     |
| max-interval     | 再試行の間の最大待機間隔を指定する正の数 (秒単位)。 指数再試行アルゴリズムを実装するために使用されます。 | いいえ       | 該当なし     |
| delta            | 待機間隔の増分値を指定する正の数 (秒単位)。 線形再試行アルゴリズムと指数再試行アルゴリズムを実装するために使用されます。             | いいえ       | 該当なし     |
| first-fast-retry | `true` に設定した場合、最初の再試行がすぐに実行されます。                                                                                  | No       | `false` |

> [!NOTE]
> `interval` のみを指定した場合、再試行は **固定** 間隔で実行されます。
> `interval` と `delta` のみを指定した場合、**線形** 間隔の再試行アルゴリズムが使用されます。この場合の再試行間の待機時間は、次の式に従って計算されます: `interval + (count - 1)*delta`。
> `interval`、`max-interval`、および `delta` を指定した場合、**指数** 間隔の再試行アルゴリズムが適用されます。この場合の再試行間の待機時間は、次の式に従って `interval` の値から値 `max-interval` まで指数的に大きくなります: `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`。

### <a name="usage"></a>使用

このポリシーは、ポリシーの以下の[セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。 子ポリシーの使用に関する制限がこのポリシーに継承されることに注意してください。

-   **ポリシー セクション:** inbound、outbound、backend、on-error

-   **ポリシー スコープ:** すべてのスコープ

## <a name="return-response"></a><a name="ReturnResponse"></a> 応答を返す

`return-response` ポリシーは、パイプラインの実行を中止し、既定またはカスタムの応答を呼び出し元に返します。 既定の応答は、本文のない `200 OK` です。 コンテキスト変数またはポリシー ステートメントを使用して、カスタムの応答を指定できます。 その両方を指定した場合、コンテキスト変数に含まれる応答が、呼び出し元に返される前にポリシー ステートメントによって変更されます。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<return-response response-variable-name="existing context variable">
  <set-header/>
  <set-body/>
  <set-status/>
</return-response>

```

### <a name="example"></a>例

```xml
<return-response>
   <set-status code="401" reason="Unauthorized"/>
   <set-header name="WWW-Authenticate" exists-action="override">
      <value>Bearer error="invalid_token"</value>
   </set-header>
</return-response>

```

### <a name="elements"></a>要素

| 要素         | 説明                                                                               | 必須 |
| --------------- | ----------------------------------------------------------------------------------------- | -------- |
| return-response | ルート要素。                                                                             | はい      |
| set-header      | [set-header](api-management-transformation-policies.md#SetHTTPheader) ポリシー ステートメント。 | No       |
| set-body        | [set-body](api-management-transformation-policies.md#SetBody) ポリシー ステートメント。         | No       |
| set-status      | [set-status](api-management-advanced-policies.md#SetStatus) ポリシー ステートメント。           | No       |

### <a name="attributes"></a>属性

| 属性              | 説明                                                                                                                                                                          | 必須  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| response-variable-name | たとえば、アップストリームの [send-request](api-management-advanced-policies.md#SendRequest) ポリシーから参照され、`Response` オブジェクトを含むコンテキスト変数の名前 | 省略可能。 |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound、outbound、backend、on-error

-   **ポリシー スコープ:** すべてのスコープ

## <a name="send-one-way-request"></a><a name="SendOneWayRequest"></a> 1 方向の要求を送信する

`send-one-way-request` ポリシーは、指定された URL に指定された要求を送信します。応答は待機しません。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<send-one-way-request mode="new | copy">
  <url>...</url>
  <method>...</method>
  <header name="" exists-action="override | skip | append | delete">...</header>
  <body>...</body>
  <authentication-certificate thumbprint="thumbprint" />
</send-one-way-request>

```

### <a name="example"></a>例

このサンプル ポリシーでは、`send-one-way-request` ポリシーを使用して、HTTP 応答コードが 500 以上の場合に Slack チャット ルームにメッセージを送信します。 このサンプルの詳細については、「[Azure API Management サービスからの外部サービスの使用](./api-management-sample-send-request.md)」を参照してください。

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>

```

### <a name="elements"></a>要素

| 要素                    | 説明                                                                                                 | 必須                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| send-one-way-request       | ルート要素。                                                                                               | はい                             |
| url                        | 要求の URL。                                                                                     | いいえ (mode=copy の場合)。はい (それ以外の場合)。 |
| メソッド                     | 要求の HTTP メソッド。                                                                            | いいえ (mode=copy の場合)。はい (それ以外の場合)。 |
| header                     | 要求ヘッダー。 複数の要求ヘッダーには複数のヘッダー要素を使用します。                                  | No                              |
| body                       | 要求本文。                                                                                           | No                              |
| authentication-certificate | [クライアントの認証に使用する証明書](api-management-authentication-policies.md#ClientCertificate) | No                              |

### <a name="attributes"></a>属性

| 属性     | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | 必須 | Default  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="文字列" | これが新しい要求であるか現在の要求のコピーであるかを判定します。 送信モードでの mode=copy の場合、要求本文は初期化されません。                                                                                                                                                                                                                                                                                                                                                                                                                                                                | No       | 新規      |
| name          | 設定するヘッダーの名前を指定します。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | はい      | 該当なし      |
| exists-action | 対象のヘッダーが既に指定されている場合の操作を指定します。 この属性の値は次のいずれかに設定する必要があります。<br /><br /> - override - 既存のヘッダーの値を置き換えます。<br />- skip - 既存のヘッダーの値を置き換えません。<br />- append - 既存のヘッダーの値に値を追加します。<br />- delete - 要求からヘッダーを削除します。<br /><br /> `override` に設定した場合、同じ名前の複数のエントリを記載すると、すべてのエントリに従ってヘッダーが設定されます (複数回記載されます)。結果に設定されるのは記載した値のみです。 | いいえ       | override |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound、outbound、backend、on-error

-   **ポリシー スコープ:** すべてのスコープ

## <a name="send-request"></a><a name="SendRequest"></a> 要求を送信する

`send-request` ポリシーは、設定されたタイムアウト値以内の待機時間で、指定された要求を指定された URL に送信します。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<send-request mode="new|copy" response-variable-name="" timeout="60 sec" ignore-error
="false|true">
  <set-url>...</set-url>
  <set-method>...</set-method>
  <set-header name="" exists-action="override|skip|append|delete">...</set-header>
  <set-body>...</set-body>
  <authentication-certificate thumbprint="thumbprint" />
</send-request>

```

### <a name="example"></a>例

この例は、承認サーバーを使用して参照トークンを検証する 1 つの方法を示しています。 このサンプルの詳細については、「[Azure API Management サービスからの外部サービスの使用](./api-management-sample-send-request.md)」を参照してください。

```xml
<inbound>
  <!-- Extract Token from Authorization header parameter -->
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

  <!-- Send request to Token Server to validate token (see RFC 7662) -->
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
    <set-method>POST</set-method>
    <set-header name="Authorization" exists-action="override">
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
    </set-header>
    <set-header name="Content-Type" exists-action="override">
      <value>application/x-www-form-urlencoded</value>
    </set-header>
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
  </send-request>

  <choose>
        <!-- Check active property in response -->
        <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
            <!-- Return 401 Unauthorized with http-problem payload -->
            <return-response>
                <set-status code="401" reason="Unauthorized" />
                <set-header name="WWW-Authenticate" exists-action="override">
                    <value>Bearer error="invalid_token"</value>
                </set-header>
            </return-response>
        </when>
    </choose>
  <base />
</inbound>

```

### <a name="elements"></a>要素

| 要素                    | 説明                                                                                                 | 必須                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| send-request               | ルート要素。                                                                                               | はい                             |
| url                        | 要求の URL。                                                                                     | いいえ (mode=copy の場合)。はい (それ以外の場合)。 |
| メソッド                     | 要求の HTTP メソッド。                                                                            | いいえ (mode=copy の場合)。はい (それ以外の場合)。 |
| header                     | 要求ヘッダー。 複数の要求ヘッダーには複数のヘッダー要素を使用します。                                  | No                              |
| body                       | 要求本文。                                                                                           | No                              |
| authentication-certificate | [クライアントの認証に使用する証明書](api-management-authentication-policies.md#ClientCertificate) | No                              |

### <a name="attributes"></a>属性

| 属性                       | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | 必須 | Default  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="文字列"                   | これが新しい要求であるか現在の要求のコピーであるかを判定します。 送信モードでの mode=copy の場合、要求本文は初期化されません。                                                                                                                                                                                                                                                                                                                                                                                                                                                                | No       | 新規      |
| response-variable-name="文字列" | 応答オブジェクトを受信するコンテキスト変数の名前。 この変数が存在しない場合は、ポリシーの正常な実行時に作成され、[`context.Variable`](api-management-policy-expressions.md#ContextVariables) コレクション経由でアクセス可能になります。                                                                                                                                                                                                                                                                                                                          | はい      | 該当なし      |
| timeout="整数"               | URL の呼び出しが失敗するまでのタイムアウト間隔 (秒単位)。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | いいえ       | 60       |
| ignore-error                    | true に設定され、要求の結果がエラーになった場合:<br /><br /> - response-variable-name が指定されている場合、null 値が格納されます。<br />- response-variable-name が指定されていない場合、context.Request は更新されません。                                                                                                                                                                                                                                                                                                                                                                                   | いいえ       | false    |
| name                            | 設定するヘッダーの名前を指定します。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | はい      | 該当なし      |
| exists-action                   | 対象のヘッダーが既に指定されている場合の操作を指定します。 この属性の値は次のいずれかに設定する必要があります。<br /><br /> - override - 既存のヘッダーの値を置き換えます。<br />- skip - 既存のヘッダーの値を置き換えません。<br />- append - 既存のヘッダーの値に値を追加します。<br />- delete - 要求からヘッダーを削除します。<br /><br /> `override` に設定した場合、同じ名前の複数のエントリを記載すると、すべてのエントリに従ってヘッダーが設定されます (複数回記載されます)。結果に設定されるのは記載した値のみです。 | いいえ       | override |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound、outbound、backend、on-error

-   **ポリシー スコープ:** すべてのスコープ

## <a name="set-http-proxy"></a><a name="SetHttpProxy"></a> HTTP プロキシの設定

`proxy` ポリシーにより、HTTP プロキシ経由でバックエンドに転送されるように要求をルーティングできます。 ゲートウェイとプロキシ間は、HTTP (HTTPS ではなく) のみがサポートされます。 基本認証と NTLM 認証のみ。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>例

ポリシー ドキュメントに機密情報を保存しないようにするため、ユーザー名とパスワードの値としての[プロパティ](api-management-howto-properties.md)の使用に注意してください。

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>要素

| 要素 | 説明  | 必須 |
| ------- | ------------ | -------- |
| proxy   | ルート要素 | はい      |

### <a name="attributes"></a>属性

| 属性         | 説明                                            | 必須 | Default |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| url="string"      | http://host:port の形式のプロキシ URL。             | はい      | 該当なし     |
| username="string" | プロキシで認証に使用するユーザー名。 | いいえ       | 該当なし     |
| password="string" | プロキシで認証に使用するパスワード。 | いいえ       | 該当なし     |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound

-   **ポリシー スコープ:** すべてのスコープ

## <a name="set-request-method"></a><a name="SetRequestMethod"></a> 要求メソッドを設定する

`set-method` ポリシーでは、要求の HTTP 要求メソッドを変更できます。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>例

このサンプル ポリシーでは、`set-method` ポリシーを使用して、HTTP 応答コードが 500 以上の場合に Slack チャット ルームにメッセージを送信します。 このサンプルの詳細については、「[Azure API Management サービスからの外部サービスの使用](./api-management-sample-send-request.md)」を参照してください。

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>

```

### <a name="elements"></a>要素

| 要素    | 説明                                                       | 必須 |
| ---------- | ----------------------------------------------------------------- | -------- |
| set-method | ルート要素。 要素の値は、HTTP メソッドを指定します。 | はい      |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound、on-error

-   **ポリシー スコープ:** すべてのスコープ

## <a name="set-status-code"></a><a name="SetStatus"></a> 状態コードを設定する

`set-status` ポリシーは、HTTP 状態コードを指定された値に変更します。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>例

この例は、承認トークンが無効な場合に 401 応答を返す方法を示しています。 詳細については、「[Azure API Management サービスからの外部サービスの使用](./api-management-sample-send-request.md)」を参照してください。

```xml
<choose>
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
    <return-response response-variable-name="existing response variable">
      <set-status code="401" reason="Unauthorized" />
      <set-header name="WWW-Authenticate" exists-action="override">
        <value>Bearer error="invalid_token"</value>
      </set-header>
    </return-response>
  </when>
</choose>

```

### <a name="elements"></a>要素

| 要素    | 説明   | 必須 |
| ---------- | ------------- | -------- |
| set-status | ルート要素。 | はい      |

### <a name="attributes"></a>属性

| 属性       | 説明                                                | 必須 | Default |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| code="整数"  | 返される HTTP 状態コード。                            | はい      | 該当なし     |
| reason="文字列" | 状態コードを返す理由の説明。 | はい      | 該当なし     |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** outbound、backend、on-error
-   **ポリシー スコープ:** すべてのスコープ

## <a name="set-variable"></a><a name="set-variable"></a> 変数の設定

`set-variable` ポリシーは、[コンテキスト](api-management-policy-expressions.md#ContextVariables)変数を宣言し、[式](api-management-policy-expressions.md)または文字列リテラルによって指定された値をこの変数に割り当てます。 リテラルが含まれている式は文字列に変換され、値の型は `System.String` になります。

### <a name="policy-statement"></a><a name="set-variablePolicyStatement"></a> ポリシー ステートメント

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="example"></a><a name="set-variableExample"></a> 例

次の例は、inbound セクションの変数の設定ポリシーを示しています。 この変数設定ポリシーは、`isMobile` の要求ヘッダーに `User-Agent` または `iPad` のテキストが含まれている場合に true に設定される `iPhone` ブール [コンテキスト](api-management-policy-expressions.md#ContextVariables)変数を作成します。

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers.GetValueOrDefault("User-Agent","").Contains("iPad") || context.Request.Headers.GetValueOrDefault("User-Agent","").Contains("iPhone"))" />
```

### <a name="elements"></a>要素

| 要素      | 説明   | 必須 |
| ------------ | ------------- | -------- |
| set-variable | ルート要素。 | はい      |

### <a name="attributes"></a>属性

| 属性 | 説明                                                              | 必須 |
| --------- | ------------------------------------------------------------------------ | -------- |
| name      | 変数の名前。                                                | はい      |
| value     | 変数の値。 式またはリテラル値を指定できます。 | はい      |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound、outbound、backend、on-error
-   **ポリシー スコープ:** すべてのスコープ

### <a name="allowed-types"></a><a name="set-variableAllowedTypes"></a> 使用できる型

`set-variable` ポリシーで使用する式は、次の基本的な型のいずれかを返す必要があります。

-   System.Boolean
-   System.SByte
-   System.Byte
-   System.UInt16
-   System.UInt32
-   System.UInt64
-   System.Int16
-   System.Int32
-   System.Int64
-   System.Decimal
-   System.Single
-   System.Double
-   System.Guid
-   System.String
-   System.Char
-   System.DateTime
-   System.TimeSpan
-   System.Byte?
-   System.UInt16?
-   System.UInt32?
-   System.UInt64?
-   System.Int16?
-   System.Int32?
-   System.Int64?
-   System.Decimal?
-   System.Single?
-   System.Double?
-   System.Guid?
-   System.String?
-   System.Char?
-   System.DateTime?

## <a name="trace"></a><a name="Trace"></a> トレース

`trace` ポリシーによって、API Inspector の出力、Application Insights テレメトリ、リソース ログにカスタム トレースが追加されます。

-   トレースがトリガーされたときに、ポリシーによって [API Inspector](./api-management-howto-api-inspector.md) の出力にカスタム トレースが追加されます。つまり、`Ocp-Apim-Trace` 要求ヘッダーが存在し、true に設定され、`Ocp-Apim-Subscription-Key` 要求ヘッダーが存在し、トレースを許可する有効なキーが保持されます。
-   [Application Insights の統合](./api-management-howto-app-insights.md)が有効で、ポリシーに指定されている `severity` が診断設定に指定されている `verbosity` 以上である場合、このポリシーによって Application Insights に[トレース](../azure-monitor/app/data-model-trace-telemetry.md) テレメトリが作成されます。
-   [リソース ログ](./api-management-howto-use-azure-monitor.md#activity-logs)が有効で、ポリシーに指定されている重大度レベルが診断設定に指定されている詳細レベル以上である場合、このポリシーによってログ エントリにプロパティが追加されます。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml

<trace source="arbitrary string literal" severity="verbose|information|error">
    <message>String literal or expressions</message>
    <metadata name="string literal or expressions" value="string literal or expressions"/>
</trace>

```

### <a name="example"></a><a name="traceExample"></a> 例

```xml
<trace source="PetStore API" severity="verbose">
    <message>@((string)context.Variables["clientConnectionID"])</message>
    <metadata name="Operation Name" value="New-Order"/>
</trace>
```

### <a name="elements"></a>要素

| 要素  | 説明                                                                                                                                          | 必須 |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| trace    | ルート要素。                                                                                                                                        | はい      |
| message  | ログに記録される文字列または式。                                                                                                                 | Yes      |
| metadata | カスタム プロパティを Application Insights の[トレース](../azure-monitor/app/data-model-trace-telemetry.md) テレメトリに追加します。 | No       |

### <a name="attributes"></a>属性

| 属性 | 説明                                                                                                               | 必須 | Default |
| --------- | ------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| source    | メッセージのソースを指定する、トレース ビューアーにとって意味のある文字列リテラル。                                   | はい      | 該当なし     |
| severity  | トレースの重大度レベルを指定します。 使用できる値は、`verbose`、`information`、`error` (最低から最高) です。 | No       | "詳細" |
| name      | プロパティ名。                                                                                                     | はい      | 該当なし     |
| value     | プロパティの値。                                                                                                    | はい      | 該当なし     |

### <a name="usage"></a>使用法

このポリシーは、ポリシーの以下の[セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound、outbound、backend、on-error

-   **ポリシー スコープ:** すべてのスコープ

## <a name="wait"></a><a name="Wait"></a> 待機

`wait` ポリシーは、直接の子ポリシーを並列で実行し、その直接の子ポリシーのすべてまたはいずれかが完了するまで完了を待機します。 待機ポリシーには、[要求を送信する](api-management-advanced-policies.md#SendRequest)、[キャッシュからの値の取得](api-management-caching-policies.md#GetFromCacheByKey)、および[制御フロー](api-management-advanced-policies.md#choose)のポリシーを直接の子ポリシーとして含めることができます。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>例

次の例では、`wait` ポリシーの直接の子ポリシーとして 2 つの `choose` ポリシーがあります。 これらの `choose` ポリシーはそれぞれ並列に実行されます。 各 `choose` ポリシーは、キャッシュされた値を取得しようとします。 キャッシュ ミスがある場合は、バックエンド サービスが呼び出されて値を提供します。 この例では、`for` 属性が `all` に設定されているため、すべての直接の子ポリシーが完了するまで、`wait` ポリシーは完了しません。 この例のコンテキスト変数 (`execute-branch-one`、`value-one`、`execute-branch-two`、および `value-two`) は、このサンプル ポリシーのスコープ外で宣言されています。

```xml
<wait for="all">
  <choose>
    <when condition="@((bool)context.Variables["execute-branch-one="])">
      <cache-lookup-value key="key-one" variable-name="value-one" />
      <choose>
        <when condition="@(!context.Variables.ContainsKey("value-one="))">
          <send-request mode="new" response-variable-name="value-one">
            <set-url>https://backend-one</set-url>
            <set-method>GET</set-method>
          </send-request>
        </when>
      </choose>
    </when>
  </choose>
  <choose>
    <when condition="@((bool)context.Variables["execute-branch-two="])">
      <cache-lookup-value key="key-two" variable-name="value-two" />
      <choose>
        <when condition="@(!context.Variables.ContainsKey("value-two="))">
          <send-request mode="new" response-variable-name="value-two">
            <set-url>https://backend-two</set-url>
            <set-method>GET</set-method>
          </send-request>
        </when>
      </choose>
    </when>
  </choose>
</wait>

```

### <a name="elements"></a>要素

| 要素 | 説明                                                                                                   | 必須 |
| ------- | ------------------------------------------------------------------------------------------------------------- | -------- |
| wait    | ルート要素。 `send-request` ポリシー、`cache-lookup-value` ポリシー、および `choose` ポリシーのみを子要素として含めることができます。 | はい      |

### <a name="attributes"></a>属性

| 属性 | 説明                                                                                                                                                                                                                                                                                                                                                                                                            | 必須 | Default |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| 対象       | `wait` ポリシーがすべての直接の子ポリシーが完了するまで待機するか、1 つが完了するまで待機するかを決定します。 使用できる値は、以下のとおりです。<br /><br /> - `all` - すべての直接の子ポリシーが完了するまで待機します。<br />- any - いずれかの直接の子ポリシーが完了するまで待機します。 最初の直接の子ポリシーが完了すると、`wait` ポリシーが完了し、他の直接の子ポリシーの実行が終了します。 | No       | all     |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

-   **ポリシー セクション:** inbound、outbound、backend
-   **ポリシー スコープ:** すべてのスコープ

## <a name="next-steps"></a>次のステップ

ポリシーを使用する方法の詳細については、次のトピックを参照してください。

-   [API Management のポリシー](api-management-howto-policies.md)
-   [ポリシー式](api-management-policy-expressions.md)
-   ポリシー ステートメントとその設定の一覧に関する[ポリシー リファレンス](./api-management-policies.md)
-   [ポリシーのサンプル](./policy-reference.md)
