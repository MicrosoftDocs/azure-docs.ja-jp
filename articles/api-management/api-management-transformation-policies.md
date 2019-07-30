---
title: Azure API Management の変換ポリシー | Microsoft Docs
description: Azure API Management で使用できる変換ポリシーについて説明します。
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: b55502bbc24868b6d8b0352f581bbf4adc81e53a
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442246"
---
# <a name="api-management-transformation-policies"></a>API Management の変換ポリシー
このトピックでは、次の API Management ポリシーについて説明します。 ポリシーを追加および構成する方法については、「 [Azure API Management のポリシー](https://go.microsoft.com/fwlink/?LinkID=398186)」をご覧ください。

##  <a name="TransformationPolicies"></a> 変換ポリシー

-   [JSON から XML への変換](api-management-transformation-policies.md#ConvertJSONtoXML) - 要求本文または応答本文を JSON から XML に変換します。

-   [XML から JSON への変換](api-management-transformation-policies.md#ConvertXMLtoJSON) - 要求本文または応答本文を XML から JSON に変換します。

-   [本文内の文字列の検索および置換](api-management-transformation-policies.md#Findandreplacestringinbody) - 要求または応答の部分文字列を検索して、別の部分文字列に置き換えます。

-   [コンテンツ内の URL のマスク](api-management-transformation-policies.md#MaskURLSContent) - ゲートウェイを経由して同じリンクをポイントするよう、応答本文のリンクを書き換えます (マスクします)。

-   [バックエンド サービスの設定](api-management-transformation-policies.md#SetBackendService) -受信要求のバックエンド サービスを変更します。

-   [本文の設定](api-management-transformation-policies.md#SetBody) - 着信要求と発信要求のメッセージ本文を設定します。

-   [HTTP ヘッダーの設定](api-management-transformation-policies.md#SetHTTPheader) - 既存の応答または要求ヘッダーまたは新しい応答または要求ヘッダーに値を割り当てます。

-   [クエリ文字列パラメーターの設定](api-management-transformation-policies.md#SetQueryStringParameter) - 要求クエリ文字列パラメーターの追加、値の置換、または削除を行います。

-   [URL の書き換え](api-management-transformation-policies.md#RewriteURL) - 要求 URL をパブリックな形式から Web サービスで想定されている形式に変換します。

-   [XSLT を使用した XML の変換](api-management-transformation-policies.md#XSLTransform) - 要求本文または応答本文に含まれる XML に XSL 変換を適用します。

##  <a name="ConvertJSONtoXML"></a> JSON から XML への変換
 `json-to-xml` ポリシーは、要求本文または応答本文を JSON から XML に変換します。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<json-to-xml apply="always | content-type-json" consider-accept-header="true | false" parse-date="true | false"/>
```

### <a name="example"></a>例

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <json-to-xml apply="always" consider-accept-header="false" parse-date="false"/>
    </outbound>
</policies>
```

### <a name="elements"></a>要素

|EnableAdfsAuthentication|説明|必須|
|----------|-----------------|--------------|
|json-to-xml|ルート要素。|はい|

### <a name="attributes"></a>属性

|EnableAdfsAuthentication|説明|必須|既定値|
|----------|-----------------|--------------|-------------|
|apply|この属性の値は次のいずれかに設定する必要があります。<br /><br /> -   always - 常に変換を適用します。<br />-   content-type-json - 応答の Content-Type ヘッダーに JSON の存在が示されている場合のみ変換を行います。|はい|該当なし|
|consider-accept-header|この属性の値は次のいずれかに設定する必要があります。<br /><br /> -   true - 要求の Accept ヘッダーで JSON が要求されている場合に変換を適用します。<br />-   false - 常に変換を適用します。|いいえ|true|
|parse-date|`false` に設定すると、変換中、日付値がコピーされます。|いいえ|true|

### <a name="usage"></a>使用法
 このポリシーは、次のポリシー [セクション](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。

-   **ポリシー セクション:** inbound、outbound、on-error

-   **ポリシー スコープ:** すべてのスコープ

##  <a name="ConvertXMLtoJSON"></a> XML から JSON への変換
 `xml-to-json` ポリシーは、要求本文または応答方文を XML から JSON に変換します。 このポリシーを使用すると、XML のみのバックエンド Web サービスに基づく API を最新化することができます。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>
```

### <a name="example"></a>例

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
    </outbound>
</policies>
```

### <a name="elements"></a>要素

|EnableAdfsAuthentication|説明|必須|
|----------|-----------------|--------------|
|xml-to-json|ルート要素。|はい|

### <a name="attributes"></a>属性

|EnableAdfsAuthentication|説明|必須|既定値|
|----------|-----------------|--------------|-------------|
|kind|この属性の値は次のいずれかに設定する必要があります。<br /><br /> -   javascript-friendly - 変換後の JSON が JavaScript 開発者にとってわかりやすい形になります。<br />-   direct - 変換後の JSON に元の XML ドキュメントの構造が反映されます。|はい|該当なし|
|apply|この属性の値は次のいずれかに設定する必要があります。<br /><br /> -   always - 常に変換します。<br />-   content-type-xml - 応答の Content-Type ヘッダーに XML の存在が示されている場合のみ変換を行います。|はい|該当なし|
|consider-accept-header|この属性の値は次のいずれかに設定する必要があります。<br /><br /> -   true - 要求の Accept ヘッダーで XML が要求されている場合に変換を適用します。<br />-   false - 常に変換を適用します。|いいえ|true|

### <a name="usage"></a>使用法
 このポリシーは、次のポリシー [セクション](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。

-   **ポリシー セクション:** inbound、outbound、on-error

-   **ポリシー スコープ:** すべてのスコープ

##  <a name="Findandreplacestringinbody"></a> 本文内の文字列の検索および置換
 `find-and-replace` ポリシーは、要求または応答内の文字部分列を検索し、別の部分文字列で置き換えます。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<find-and-replace from="what to replace" to="replacement" />
```

### <a name="example"></a>例

```xml
<find-and-replace from="notebook" to="laptop" />
```

### <a name="elements"></a>要素

|EnableAdfsAuthentication|説明|必須|
|----------|-----------------|--------------|
|find-and-replace|ルート要素。|はい|

### <a name="attributes"></a>属性

|EnableAdfsAuthentication|説明|必須|既定値|
|----------|-----------------|--------------|-------------|
|from|検索する文字列。|はい|該当なし|
|to|置換する文字列。 長さゼロの置換文字列を指定すると、検索文字列を削除できます。|はい|該当なし|

### <a name="usage"></a>使用法
 このポリシーは、次のポリシー [セクション](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。

-   **ポリシー セクション:** inbound、outbound、backend、on-error

-   **ポリシー スコープ:** すべてのスコープ

##  <a name="MaskURLSContent"></a> コンテンツ内の URL のマスク
 `redirect-content-urls` ポリシーは、応答本文内のリンクを、ゲートウェイを経由して同じリンクをポイントするように書き換えます (マスクします)。 応答本文のリンクをゲートウェイにポイントさせる場合は outbound セクションで使用します。 反対の効果を生じさせる場合は inbound セクションで使用します。

> [!NOTE]
>  このポリシーでは、`Location` ヘッダーなどのヘッダー値は変更されません。 ヘッダーの値を変更するには、[set-header](api-management-transformation-policies.md#SetHTTPheader) ポリシーを使用します。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<redirect-content-urls />
```

### <a name="example"></a>例

```xml
<redirect-content-urls />
```

### <a name="elements"></a>要素

|EnableAdfsAuthentication|説明|必須|
|----------|-----------------|--------------|
|redirect-content-urls|ルート要素。|はい|

### <a name="usage"></a>使用法
 このポリシーは、次のポリシー [セクション](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。

-   **ポリシー セクション:** inbound、outbound

-   **ポリシー スコープ:** すべてのスコープ

##  <a name="SetBackendService"></a> バックエンド サービスの設定
 `set-backend-service` ポリシーを使用すると、該当する操作の API 設定で指定されているものとは異なるバックエンドに受信要求をリダイレクトできます。 このポリシーでは、受信要求のバックエンド サービスのベース URL をこのポリシーで指定した URL に変更します。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<set-backend-service base-url="base URL of the backend service" />
```

or

```xml
<set-backend-service backend-id="identifier of the backend entity specifying base URL of the backend service" />
```

> [!NOTE]
> バックエンド エンティティは、管理 [API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend) と [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement) を使用して管理できます。

### <a name="example"></a>例

```xml
<policies>
    <inbound>
        <choose>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2013-05")">
                <set-backend-service base-url="http://contoso.com/api/8.2/" />
            </when>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2014-03")">
                <set-backend-service base-url="http://contoso.com/api/9.1/" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
この例では、バックエンド サービスの設定ポリシーにより、クエリ文字列に渡されるバージョン値に基づいて、API で指定されているものとは異なるバックエンド サービスに要求をルーティングしています。

まず、API 設定からバックエンド サービスのベース URL が読み取られます。 これにより、要求 URL `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` が `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` になります。`http://contoso.com/api/10.4/` は API 設定で指定されているバックエンド サービスの URL です。

[<choose\>](api-management-advanced-policies.md#choose) ポリシー ステートメントを適用した場合、バックエンド サービスのベース URL は、要求クエリの version パラメーターの値に応じて `http://contoso.com/api/8.2` または `http://contoso.com/api/9.1` のどちらかに変更されます。 たとえば、このパラメーターの値が `"2013-15"` の場合、最終的な要求 URL は `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`になります。

さらに要求を変換する必要がある場合には、別の[変換ポリシー](api-management-transformation-policies.md#TransformationPolicies)を使用できます。 たとえば、要求をバージョンごとのバックエンドにルーティングしたので version クエリ パラメーターを削除するには、[クエリ文字列パラメーターの設定](api-management-transformation-policies.md#SetQueryStringParameter)ポリシーを使用して、余計になった version 属性を削除できます。

### <a name="example"></a>例

```xml
<policies>
    <inbound>
        <set-backend-service backend-id="my-sf-service" sf-partition-key="@(context.Request.Url.Query.GetValueOrDefault("userId","")" sf-replica-type="primary" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
この例では、ポリシーは、パーティション キーとして userId クエリ文字列を使い、パーティションのプライマリ レプリカを使って、サービス ファブリック バックエンドに要求をルーティングします。

### <a name="elements"></a>要素

|EnableAdfsAuthentication|説明|必須|
|----------|-----------------|--------------|
|set-backend-service|ルート要素。|はい|

### <a name="attributes"></a>属性

|EnableAdfsAuthentication|説明|必須|既定値|
|----------|-----------------|--------------|-------------|
|base-url|バックエンド サービスの新しいベース URL。|`base-url` または `backend-id` のいずれかが存在しなければなりません。|該当なし|
|backend-id|ルーティング先のバックエンドの識別子。 (バックエンド エンティティは、[API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend) と [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement) を使用して管理できます)。|`base-url` または `backend-id` のいずれかが存在しなければなりません。|該当なし|
|sf-partition-key|バックエンドが Service Fabric サービスで、'backend-id' を使って指定されている場合にのみ適用されます。 名前解決サービスからの特定のパーティションを解決するために使います。|いいえ|該当なし|
|sf-replica-type|バックエンドが Service Fabric サービスで、'backend-id' を使って指定されている場合にのみ適用されます。 要求をパーティションのプライマリ レプリカとセカンダリ レプリカのどちらに送信する必要があるかを制御します。 |いいえ|該当なし|
|sf-resolve-condition|バックエンドが Service Fabric サービスの場合にのみ適用されます。 Service Fabric バックエンドへの呼び出しを新しい解決で繰り返す必要があるかどうかを識別する条件です。|いいえ|該当なし|
|sf-service-instance-name|バックエンドが Service Fabric サービスの場合にのみ適用されます。 実行時にサービス インスタンスを変更できます。 |いいえ|該当なし|
|sf-listener-name|バックエンドが Service Fabric サービスで、"backend-id" を使って指定されている場合にのみ適用されます。 Service Fabric Reliable Services では、サービス内に複数のリスナーを作成できます。 この属性は、バックエンドのリライアブル サービスに複数のリスナーがある場合に、特定のリスナーを選択するために使用されます。 この属性が指定されていない場合、API Management によって名前のないリスナーの使用が試行されます。 リスナーが 1 つのみのリライアブル サービスでは、通常、リスナーに名前がありません。 |いいえ|該当なし|

### <a name="usage"></a>使用法
 このポリシーは、次のポリシー [セクション](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。

-   **ポリシー セクション:** inbound、backend

-   **ポリシー スコープ:** すべてのスコープ

##  <a name="SetBody"></a> 本文の設定
 着信要求と発信要求のメッセージ本文を設定するには、`set-body` ポリシーを使用します。 メッセージ本文へのアクセスには、ポリシーを inbound セクションと outbound セクションのどちらに記載するかに応じて `context.Request.Body` プロパティまたは `context.Response.Body` を使用します。

> [!IMPORTANT]
>  既定では、`context.Request.Body` または `context.Response.Body` を使用してメッセージ本文にアクセスした場合元のメッセージ本文は失われるため、この本文を式で返して設定する必要があります。 本文の内容を保持するには、メッセージへのアクセス時に `preserveContent` パラメーターを `true` に設定します。 `preserveContent` を `true` に設定している場合に式から別の本文が返されると、返された本文が使用されます。
>
>  `set-body` ポリシーを使用する際は次の考慮事項に注意してください。
>
> - `set-body` ポリシーを使用して新しい本文はまたは更新した本文を返す場合、新しい本文の内容を明示的に指定しているため、`preserveContent` を `true` に設定する必要はありません。
>   -   受信パイプラインには応答が存在しないため、このパイプラインで応答の内容を保持しても意味がありません。
>   -   送信パイプラインで要求の内容を保持しても、要求はこの時点で既にバックエンドに送信されているため意味がありません。
>   -   受信パイプラインの GET 内など、メッセージ本文がない場合にこのポリシーを使用すると、例外がスローされます。

 詳細については、[コンテキスト変数](api-management-policy-expressions.md#ContextVariables)に関する表の `context.Request.Body``context.Response.Body``IMessage` の各セクションを参照してください。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>例

#### <a name="literal-text-example"></a>リテラル テキストの例

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>文字列である本文にアクセスする例。 後からパイプラインでアクセスできるように、元の要求本文を保持していることに注意してください。

```xml
<set-body>
@{ 
    string inBody = context.Request.Body.As<string>(preserveContent: true); 
    if (inBody[0] =='c') { 
        inBody[0] = 'm'; 
    } 
    return inBody; 
}
</set-body>
```

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>JObject である本文にアクセスする例。 元の要求本文を予約していないため、後でパイプラインでそこにアクセスすると例外が発生することに注意してください。

```xml
<set-body> 
@{ 
    JObject inBody = context.Request.Body.As<JObject>(); 
    if (inBody.attribute == <tag>) { 
        inBody[0] = 'm'; 
    } 
    return inBody.ToString(); 
} 
</set-body>

```

#### <a name="filter-response-based-on-product"></a>製品に基づいて応答をフィルター処理する
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

### <a name="using-liquid-templates-with-set-body"></a>本文の設定がある Liquid テンプレートの使用
`set-body` ポリシーは、[Liquid](https://shopify.github.io/liquid/basics/introduction/) テンプレート作成言語を使用して要求または応答の本文を変換するように構成できます。 これは、メッセージの形式を完全に再構築する必要がある場合にとても効果的な場合があります。

> [!IMPORTANT]
> `set-body` ポリシーで使用される Liquid の実装は、「C# mode」で構成されます。 これは、フィルター処理などを実行する際に特に重要です。 たとえば、日付フィルターを使用するには、次のような Pascal 形式と C# date 形式を使用する必要があります。
>
> {{body.foo.startDateTime| Date:"yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Liquid テンプレートを使用して XML 本文に正しくバインドするには、`set-header` ポリシーを使用して、Content-Type を application/xml、text/xml (または任意の種類の終了 +xml) に設定します。JSON 本文の場合は、application/json、text/json (または任意の種類の終了 +json) にする必要があります。

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>Liquid テンプレートを使用して JSON を SOAP に変換する
```xml
<set-body template="liquid">
    <soap:Envelope xmlns="http://tempuri.org/" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
        <soap:Body>
            <GetOpenOrders>
                <cust>{{body.getOpenOrders.cust}}</cust>
            </GetOpenOrders>
        </soap:Body>
    </soap:Envelope>
</set-body>
```

#### <a name="transform-json-using-a-liquid-template"></a>Liquid テンプレートを使用した JSON の変換
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>要素

|EnableAdfsAuthentication|説明|必須|
|----------|-----------------|--------------|
|set-body|ルート要素。 本文のテキストか、または本文を返す式を記載します。|はい|

### <a name="properties"></a>properties

|EnableAdfsAuthentication|説明|必須|既定値|
|----------|-----------------|--------------|-------------|
|template|本文の設定ポリシーが実行されるテンプレート作成モードの変更に使用されます。 現在サポートされている値:<br /><br />- liquid - 本文の設定ポリシーでは、liquid テンプレート作成エンジンが使用されます |いいえ||

要求と応答に関する情報にアクセスするために、Liquid テンプレートは次のプロパティでコンテキスト オブジェクトにバインドできます。 <br />
<pre>context.
    Request.
        Url
        Method
        OriginalMethod
        OriginalUrl
        IpAddress
        MatchedParameters
        HasBody
        ClientCertificates
        Headers

    Response.
        StatusCode
        Method
        Headers
Url.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString

OriginalUrl.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString
</pre>



### <a name="usage"></a>使用法
 このポリシーは、次のポリシー [セクション](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。

-   **ポリシー セクション:** inbound、outbound、backend

-   **ポリシー スコープ:** すべてのスコープ

##  <a name="SetHTTPheader"></a> HTTP ヘッダーの設定
 `set-header` ポリシーは、既存の応答ヘッダーまたは要求ヘッダーに値を割り当てるか、新しい応答ヘッダーまたは要求ヘッダーを追加します。

 HTTP ヘッダーのリストを HTTP メッセージに挿入します。 受信パイプラインに配置した場合、このポリシーは、ターゲット サービスに渡される要求の HTTP ヘッダーを設定します。 送信パイプラインに配置した場合、このポリシーは、ゲートウェイのクライアントに送信される応答の HTTP ヘッダーを設定します。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>例

#### <a name="example"></a>例

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```

#### <a name="forward-context-information-to-the-backend-service"></a>バックエンド サービスにコンテキスト情報を転送する
 次の例では、API レベルでポリシーを適用して、バックエンド サービスにコンテキスト情報を提供する方法を示します。 このポリシーの構成と使用についてのデモは、「[Cloud Cover Episode 177:More API Management Features](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)」(クラウド カバー エピソード 177: その他の API Management 機能の紹介) を 10:30 まで早送りしてご覧ください。 12:10 に開発者ポータルで操作を呼び出すデモが示されており、このポリシーの動作を確認できます。

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 詳細については、[ポリシー式](api-management-policy-expressions.md)および[コンテキスト変数](api-management-policy-expressions.md#ContextVariables)に関する各ページを参照してください。

> [!NOTE]
> ヘッダーの複数の値が次のように CSV 文字列に連結されます。`headerName: value1,value2,value3`
>
> 値が次の性質を持つ標準化されたヘッダーは例外です。
> - コンマを含む可能性がある (`User-Agent`、`WWW-Authenticate`、`Proxy-Authenticate`)。
> - 日付を含む可能性がある (`Cookie`、`Set-Cookie`、`Warning`)。
> - 日付を含む (`Date`、`Expires`、`If-Modified-Since`、`If-Unmodified-Since`、`Last-Modified`、`Retry-After`)。
>
> これらの例外の場合は、複数のヘッダー値が 1 つの文字列に連結されることはなく、次のように個別のヘッダーとして渡されます。`User-Agent: value1`
>`User-Agent: value2`
>`User-Agent: value3`

### <a name="elements"></a>要素

|EnableAdfsAuthentication|説明|必須|
|----------|-----------------|--------------|
|set-header|ルート要素。|はい|
|value|設定するヘッダーの値を指定します。 同じ名前のヘッダーが複数ある場合は、`value` 要素をさらに追加します。|はい|

### <a name="properties"></a>properties

|EnableAdfsAuthentication|説明|必須|既定値|
|----------|-----------------|--------------|-------------|
|exists-action|対象のヘッダーが既に指定されている場合の操作を指定します。 この属性の値は次のいずれかに設定する必要があります。<br /><br /> -   override - 既存のヘッダーの値を置き換えます。<br />-   skip - 既存のヘッダーの値を置き換えません。<br />-   append - 既存のヘッダーの値に値を追加します。<br />-   delete - 要求からヘッダーを削除します。<br /><br /> `override` に設定した場合、同じ名前の複数のエントリを記載すると、すべてのエントリに従ってヘッダーが設定されます (複数回記載されます)。結果に設定されるのは記載した値のみです。|いいえ|override|
|名前|設定するヘッダーの名前を指定します。|はい|該当なし|

### <a name="usage"></a>使用法
 このポリシーは、次のポリシー [セクション](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。

-   **ポリシー セクション:** inbound、outbound、backend、on-error

-   **ポリシー スコープ:** すべてのスコープ

##  <a name="SetQueryStringParameter"></a> クエリ文字列パラメーターの設定
 `set-query-parameter` ポリシーは、要求クエリ文字列パラメーターの追加、値の置換、または削除を行います。 このポリシーを使用すると、オプションであるかまたは要求内に存在しない、バックエンド サービスで必要とされるクエリ パラメーターを渡すことができます。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<set-query-parameter name="param name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
</set-query-parameter>
```

### <a name="examples"></a>例

#### <a name="example"></a>例

```xml

<set-query-parameter>
  <parameter name="api-key" exists-action="skip">
    <value>12345678901</value>
  </parameter>
  <!-- for multiple parameters with the same name add additional value elements -->
</set-query-parameter>

```

#### <a name="forward-context-information-to-the-backend-service"></a>バックエンド サービスにコンテキスト情報を転送する
 次の例では、API レベルでポリシーを適用して、バックエンド サービスにコンテキスト情報を提供する方法を示します。 このポリシーの構成と使用についてのデモは、「[Cloud Cover Episode 177:More API Management Features](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)」(クラウド カバー エピソード 177: その他の API Management 機能の紹介) を 10:30 まで早送りしてご覧ください。 12:10 に開発者ポータルで操作を呼び出すデモが示されており、このポリシーの動作を確認できます。

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 詳細については、[ポリシー式](api-management-policy-expressions.md)および[コンテキスト変数](api-management-policy-expressions.md#ContextVariables)に関する各ページを参照してください。

### <a name="elements"></a>要素

|EnableAdfsAuthentication|説明|必須|
|----------|-----------------|--------------|
|set-query-parameter|ルート要素。|はい|
|value|設定するクエリ パラメーターの値を指定します。 同じ名前のクエリ パラメーターが複数ある場合は、`value` 要素をさらに追加します。|はい|

### <a name="properties"></a>properties

|EnableAdfsAuthentication|説明|必須|既定値|
|----------|-----------------|--------------|-------------|
|exists-action|対象のクエリ パラメーターが既に指定されている場合の操作を指定します。 この属性の値は次のいずれかに設定する必要があります。<br /><br /> -   override - 既存のパラメーターの値を置き換えます。<br />-   skip - 既存のクエリ パラメーターの値を置き換えません。<br />-   append - 既存のクエリ パラメーターの値に値を追加します。<br />-   delete - 要求からクエリ パラメーターを削除します。<br /><br /> `override` に設定した場合、同じ名前の複数のエントリを記載すると、すべてのエントリに従ってクエリ パラメーターが設定されます (複数回記載されます)。結果に設定されるのは記載した値のみです。|いいえ|override|
|名前|設定するクエリ パラメーターの名前を指定します。|はい|該当なし|

### <a name="usage"></a>使用法
 このポリシーは、次のポリシー [セクション](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。

-   **ポリシー セクション:** inbound、backend

-   **ポリシー スコープ:** すべてのスコープ

##  <a name="RewriteURL"></a> URL の書き換え
 `rewrite-uri` ポリシーは、次の例に示すように、要求 URL をパブリックな形式から Web サービスで想定されている形式に変換します。

- パブリック URL - `http://api.example.com/storenumber/ordernumber`

- 要求 URL - `http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

  このポリシーは、人間やブラウザーにとって使いやすい URL を Web サービスで求められる URL 形式に変換する必要がある場合に使用します。 このポリシーを適用する必要があるのは、クリーン URL、RESTful URL、ユーザーフレンドリ URL、SEO フレンドリ URL など、(スキーマと機関の後に) クエリ文字列を含まずリソースのパスのみを含む純粋に構造的な URL のような代替 URL 形式を公開する場合のみです。 これは、一般的に、美学上、使いやすさ、または検索エンジン最適化 (SEO) の目的で行われます。

> [!NOTE]
>  ポリシーを使用して追加できるのはクエリ文字列パラメーターのみです。 書き換え URL にさらにテンプレート パス パラメーターを追加することはできません。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<rewrite-uri template="uri template" copy-unmatched-params="true | false" />
```

### <a name="example"></a>例

```xml
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put?c=d -->
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" copy-unmatched-params="false" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put -->
```

### <a name="elements"></a>要素

|EnableAdfsAuthentication|説明|必須|
|----------|-----------------|--------------|
|rewrite-uri|ルート要素。|はい|

### <a name="attributes"></a>属性

|Attribute|説明|必須|既定値|
|---------------|-----------------|--------------|-------------|
|template|クエリ文字列パラメーターを設定した実際の Web サービス URL。 式を使用する場合は、値の全体が式である必要があります。|はい|該当なし|
|copy-unmatched-params|元の URL テンプレートに存在しない着信要求のクエリ パラメーターを、書き換えテンプレートで定義されている URL に追加するかどうかを指定します。|いいえ|true|

### <a name="usage"></a>使用法
 このポリシーは、次のポリシー [セクション](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。

-   **ポリシー セクション:** inbound

-   **ポリシー スコープ:** すべてのスコープ

##  <a name="XSLTransform"></a> XSLT を使用した XML の変換
 `Transform XML using an XSLT` ポリシーは、要求本文または応答本文に含まれる XML に XSL 変換を適用します。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<xsl-transform>
    <parameter name="User-Agent">@(context.Request.Headers.GetValueOrDefault("User-Agent","non-specified"))</parameter>
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:output method="xml" indent="yes" />
        <xsl:param name="User-Agent" />
        <xsl:template match="* | @* | node()">
            <xsl:copy>
                <xsl:if test="self::* and not(parent::*)">
                    <xsl:attribute name="User-Agent">
                        <xsl:value-of select="$User-Agent" />
                    </xsl:attribute>
                </xsl:if>
                <xsl:apply-templates select="* | @* | node()" />
            </xsl:copy>
        </xsl:template>
    </xsl:stylesheet>
  </xsl-transform>
```

### <a name="example"></a>例

```xml
<policies>
  <inbound>
      <base />
  </inbound>
  <outbound>
      <base />
      <xsl-transform>
        <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
            <xsl:output omit-xml-declaration="yes" method="xml" indent="yes" />
            <!-- Copy all nodes directly-->
            <xsl:template match="node()| @*|*">
                <xsl:copy>
                    <xsl:apply-templates select="@* | node()|*" />
                </xsl:copy>
            </xsl:template>
        </xsl:stylesheet>
    </xsl-transform>
  </outbound>
</policies>
```

### <a name="elements"></a>要素

|EnableAdfsAuthentication|説明|必須|
|----------|-----------------|--------------|
|xsl-transform|ルート要素。|はい|
|パラメーター|変換で使用する変数の定義に使用します。|いいえ|
|xsl:stylesheet|ルート スタイルシート要素。 この中で定義する要素と属性はすべて、標準的な [XSLT の仕様](https://www.w3.org/TR/xslt)に従う必要があります。|はい|

### <a name="usage"></a>使用法
 このポリシーは、次のポリシー [セクション](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。

-   **ポリシー セクション:** inbound、outbound

-   **ポリシー スコープ:** すべてのスコープ

## <a name="next-steps"></a>次の手順

詳細については、次のトピックを参照してください。

+ [API Management のポリシー](api-management-howto-policies.md)
+ ポリシー ステートメントとその設定の一覧に関する[ポリシー リファレンス](api-management-policy-reference.md)
+ [ポリシーのサンプル](policy-samples.md)
