---
title: HTTP 要求を生成するための API Management サービスの使用
description: API で外部サービスを呼び出すための API Management での要求と応答ポリシーの使用方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 4539c0fa-21ef-4b1c-a1d4-d89a38c242fa
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 2f4bd040d7e5858fd561444f56dbce7b3f940d9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92742395"
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Azure API Management サービスからの外部サービスの使用
Azure API Management サービスに含まれるポリシーでは、着信要求、送信応答、および基本的な構成情報のみを使用した有用なさまざまな処理を実行できます。 一方、API Management ポリシーでは外部サービスと通信することもできるため、さらに可能性が広がります。

[Azure Event Hub サービスでログ記録、監視、および分析](api-management-log-to-eventhub-sample.md)を行う際の通信の方法については、既に学習しています。 この記事では、外部の任意の HTTP ベースのサービスと通信するポリシーのデモを行います。 こうしたポリシーは、リモート イベントをトリガーしたり、元の要求と応答を何らかの方法で操作する情報を取得したりする場合に使用できます。

## <a name="send-one-way-request"></a>Send-One-Way-Request
外部サービスに何らかの種類の重要なイベントを通知する、外部通信の最も単純な要求スタイルは、おそらくファイア アンド フォーゲットでしょう。 制御フロー ポリシー `choose` は、関心のある任意の種類の状態検出に使用できます。  条件が満たされると、[send-one-way-request](./api-management-advanced-policies.md#SendOneWayRequest) ポリシーを使用して外部 HTTP 要求を行うことができます。 これは、Hipchat、Slack などのメッセージング システム、SendGrid または MailChimp のようなメール API、または PagerDuty などの重要なサポート インシデントへの要求である場合があります。 こうしたメッセージング システムすべてにシンプルな HTTP API があり、呼び出すことができます。

### <a name="alerting-with-slack"></a>Slack を使用した警告
以下の例では、HTTP 応答のステータス コードが 500 以上の場合に、Slack チャット ルームにメッセージを送信する方法を示します。 500 の範囲エラーは、API のクライアントが自動的に解決できない、バックエンド API に問題があることを示します。 通常、これは API Management 側で何らかの介入が必要です。  

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

Slack には、着信 Web フックの概念があります。 着信 Web フックを構成するとき、Slack は、単純な POST 要求を行い Slack チャネルにメッセージを渡す、特殊な URL を生成します。 作成する JSON 本文は、Slack によって定義されている形式に基づきます。

![Slack Web フック](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>ファイア アンド フォーゲットは十分か
ファイア アンド フォーゲット スタイルの要求には、あるトレードオフがあります。 何らかの理由で要求が失敗しても、エラーは報告されません。 この特定の状況に対し、複雑な 2 次的なエラー レポート システムや、応答の待機に関わる追加のパフォーマンス コストに対する保証はありません。 応答の確認が重要なシナリオの場合、より適切なオプションは [send-request](./api-management-advanced-policies.md#SendRequest) ポリシーです。

## <a name="send-request"></a>send-request
`send-request` ポリシーは、外部サービスを使用し複雑な処理機能を実行したり、さらにポリシーを処理したりするために API Management サービスにデータを返すことに使用できます。

### <a name="authorizing-reference-tokens"></a>参照トークンの承認
API Management の主な機能には、バックエンド リソースの保護があります。 API によって使用される承認サーバーで、[Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md) のように、その OAuth2 フローの一部として [JWT トークン](https://jwt.io/)が作成される場合、`validate-jwt` ポリシーを使用すると、そのトークンの有効性を検証できます。 一部の承認サーバーでは、承認サーバーへのコールバックを行わなければ検証できない[参照トークン](https://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/)と呼ばれるものが作成されます。

### <a name="standardized-introspection"></a>標準化されたイントロスペクション
これまで、承認サーバーで参照トークンを検証するための標準的な方法はありませんでした。 ただし、最近 IETF から、トークンの有効性をリソース サーバーが検証する方法を定義した標準 [RFC 7662](https://tools.ietf.org/html/rfc7662) の提案が公開されました。

### <a name="extracting-the-token"></a>トークンの抽出
まず、トークンを Authorization ヘッダーから抽出します。 ヘッダー値は、[RFC 6750](https://tools.ietf.org/html/rfc6750#section-2.1) に従って、`Bearer` 承認スキーム、単一スペース、および承認トークンを使用して形式を指定する必要があります。 残念ながら、認証スキームが省略されている場合もあります。 これに解析時に対応するため、API Management は、スペースでヘッダー値を分割し、返された文字列の配列から最後の文字を選択します。 これにより、不適切な形式の承認ヘッダーに対応できます。

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>検証要求の実行
承認トークンを取得した API Management は、トークンの検証要求を実行できます。 RFC 7662 では、この処理をイントロスペクションと呼び、イントロスペクション リソースに HTML フォームを `POST` することを求めています。 HTML フォームには、キー `token`と共にキーと値のペアが少なくとも 1 つ含まれている必要があります。 悪意のあるクライアントが有効なトークンを探さないよう、承認サーバーへのこの要求も認証される必要があります。

```xml
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
```

### <a name="checking-the-response"></a>応答の確認
`response-variable-name` 属性は、返された応答へのアクセスを提供するために使用されます。 このプロパティで定義されている名前は、`IResponse` オブジェクトにアクセスする `context.Variables` ディクショナリへのキーとして使用できます。

応答オブジェクトから本文を取得できます。RFC 7622 は、応答は JSON オブジェクトである必要があり、ブール値である `active` というプロパティが少なくとも 1 つ必要であることを API Management に示します。 `active` が true の場合、トークンは有効であるとみなされます。

または、承認サーバーにトークンが有効であるかどうかを示す "アクティブ" フィールドが含まれていない場合は、Postman などのツールを使用して、有効なトークンに設定されているプロパティを確認します。 たとえば、有効なトークン応答に "expires_in" というプロパティが含まれている場合は、次のように、このプロパティ名が承認サーバーの応答に存在するかどうかを確認します。

<when condition="@(((IResponse)context.Variables["tokenstate"]).Body.As<JObject>().Property("expires_in") == null)">

### <a name="reporting-failure"></a>レポートのエラー
`<choose>` ポリシーを使用すると、トークンの有効性を検出できます。無効である場合は、401 応答が返されます。

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

`bearer` トークンの使用方法が記載されている [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) に従い、API Management は、401 応答と共に `WWW-Authenticate` ヘッダーも返します。 WWW-Authenticate は、正しく認証される要求を構築する方法をクライアントに指示することを目的としています。 OAuth2 フレームワークで実行可能なアプローチは多岐にわたるため、必要なすべての情報を通信することは困難です。 さいわいにも、 [クライアントが正しくリソース サーバーに要求を承認させる方法](https://tools.ietf.org/html/draft-jones-oauth-discovery-00)を支援する取り組みが進行中です。

### <a name="final-solution"></a>最終的なソリューション
これらをすべてまとめると、次のポリシーがあることになります。

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
              <return-response response-variable-name="existing response variable">
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

これは、有用な外部サービスを API Management サービスを経由する要求および応答の処理に統合する `send-request` ポリシーを使用する数多い例の中のほんの 1 つです。

## <a name="response-composition"></a>応答の構成
`send-request` ポリシーを使用すると、前述の例のようにバックエンド システムにプライマリ要求を拡張できるほか、バックエンド呼び出しに完全に置き換わるものとして使用することができます。 このテクニックを使用すると、複数の異なるシステムからまとめた複合リソースを簡単に作成できます。

### <a name="building-a-dashboard"></a>ダッシュボードの構築
ダッシュボードを動かすためなど、複数のバックエンド システムに存在する情報を公開したい場合があります。 KPI はすべて異なるバックエンドから得られますが、それらに直接アクセスできないようにし、1 つの要求で、すべての情報を取得できるようになると都合がよい場合があります。 一部のバックエンド情報は、細分化したり、好ましくない部分は削除したりする必要があるでしょう。 その複合リソースをキャッシュできると、パフォーマンスが悪いメトリックの変化を確認するためにユーザーはよく F5 キーを打つため、バックエンドに対する負荷を軽減できるでしょう。    

### <a name="faking-the-resource"></a>リソースのフェイク
ダッシュボード リソースを構築するには、まず Azure Portal で新しい操作を構成します。 これは、動的なリソースを構築する複合ポリシーを構成するプレースホルダー操作です。

![ダッシュボード操作](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>要求の作成
操作を作成したら、その操作に特化したポリシーを構成できます。 

![[ポリシー スコープ] 画面を示すスクリーンショット。](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

このためには、バックエンドに送信できるよう、まず受信要求のすべてのクエリ パラメーターを抽出します。 この例では、ダッシュボードには、期間に基づいて情報が表示されているので、`fromDate` と `toDate` のパラメーターがあります。 要求 URL から情報を抽出するには、`set-variable` ポリシーを使用します。

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

この情報を入手できたら、バックエンド システムすべてに要求を実行できます。 各要求は、パラメーター情報を使用して新しい URL を構築し、それぞれのサーバーを呼び出し、コンテキスト変数に応答を格納します。

```xml
<send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
  <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
  <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

これらの要求は、不適切にも連続して実行されます。 

### <a name="responding"></a>応答
複合応答を構築するには、[return-response](./api-management-advanced-policies.md#ReturnResponse) ポリシーを使用します。 `set-body` 要素では式を使用して、すべてのコンポーネント表現がプロパティとして埋め込まれた、新しい `JObject` を構築できます。

```xml
<return-response response-variable-name="existing response variable">
  <set-status code="200" reason="OK" />
  <set-header name="Content-Type" exists-action="override">
    <value>application/json</value>
  </set-header>
  <set-body>
    @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                  new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                  new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                  new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                  ).ToString())
  </set-body>
</return-response>
```

完全なポリシーは以下のようになります。

```xml
<policies>
    <inbound>

  <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
  <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
</policies>
```

プレースホルダー操作の構成で、ダッシュボード リソースが少なくとも 1 時間キャッシュされるように構成できます。 

## <a name="summary"></a>まとめ
Azure API Management サービスには、HTTP トラフィックに選択的に適用できる、バックエンド サービスの構成に使用できる、柔軟なポリシーがあります。 警告機能、確認、検証機能で API ゲートウェイを拡張したい場合、または複数のバックエンド サービスを使用し新しい複合リソースを作成したい場合のために、 `send-request` と関連ポリシーはさまざまな可能性を開きます。
