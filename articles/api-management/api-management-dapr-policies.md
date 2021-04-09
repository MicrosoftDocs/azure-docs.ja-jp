---
title: Azure API Management の Dapr 統合ポリシー | Microsoft Docs
description: Dapr マイクロサービス拡張機能と対話するための Azure API Management のポリシーについて説明します。
author: vladvino
ms.author: vlvinogr
ms.date: 02/18/2021
ms.topic: article
ms.service: api-management
ms.openlocfilehash: 051bf4398555f318f613c66d58ec65be1d30e215
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646811"
---
# <a name="api-management-dapr-integration-policies"></a>API Management の Dapr 統合ポリシー

このトピックでは、API Management の Dapr 統合ポリシーについて説明します。 Dapr は、ステートレスおよびステートフル マイクロサービス ベースのアプリケーションを任意の言語またはフレームワークで構築するための移植可能なランタイムです。 ビルドイン再試行ロジックを使用したサービスの検出と呼び出し、1 回以上の配信セマンティクスを使用したパブリッシュとサブスクライブ、外部サービスを使用したコンポジションを容易にするプラグ可能なバインド リソースなど、一般的なマイクロサービス パターンが体系化されています。 Dapr の使用を開始する方法の詳細と手順については、[dapr.io](https://dapr.io) を参照してください。 ポリシーを追加および構成する方法については、「 [Azure API Management のポリシー](api-management-howto-policies.md)」をご覧ください。

> [!CAUTION]
> このトピックで参照されているポリシーはパブリック プレビュー段階であり、[Microsoft Azure プレビューの追加利用規約](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の対象となります。

> [!IMPORTANT]
> このトピックで参照されているポリシーは、Dapr サポートが有効になっている[セルフホステッド バージョンの API Management ゲートウェイ](self-hosted-gateway-overview.md)でのみ動作します。

## <a name="enable-dapr-support-in-the-self-hosted-gateway"></a>セルフホステッド ゲートウェイでの Dapr サポートの有効化

セルフホステッド ゲートウェイで Dapr サポートを有効にするには、以下の [Dapr 注釈](https://github.com/dapr/docs/blob/master/README.md)を [Kubernetes 展開テンプレート](how-to-deploy-self-hosted-gateway-kubernetes.md)に追加し、"app-name" を目的の名前に置き換えます。 Dapr での API Management の設定と使用に関する完全なチュートリアルは、[こちら](https://aka.ms/apim/dapr/walkthru)で参照できます。
```yml
template:
    metadata:
      labels:
        app: app-name
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "app-name"
```


## <a name="distributed-application-runtime-dapr-integration-policies"></a>分散型アプリケーション ランタイム (Dapr) 統合ポリシー

-  [サービスへの要求の送信](api-management-dapr-policies.md#invoke):Dapr ランタイムを使用して Dapr マイクロサービスを検索し、このマイクロサービスとの通信を確実に行います。 Dapr でのサービス呼び出しの詳細については、この [README](https://github.com/dapr/docs/blob/master/README.md#service-invocation) ファイルの説明を参照してください。
-  [Pub/Sub トピックへのメッセージの送信](api-management-dapr-policies.md#pubsub):Dapr ランタイムを使用して、Publish/Subscribe トピックにメッセージを発行します。 Dapr での Publish/Subscribe メッセージングの詳細については、この [README](https://github.com/dapr/docs/blob/master/README.md) ファイルの説明を参照してください。
-  [トリガー出力バインド](api-management-dapr-policies.md#bind):Dapr ランタイムを使用して、出力バインドを介して外部システムを呼び出します。 Dapr でのバインドの詳細については、この [README](https://github.com/dapr/docs/blob/master/README.md) ファイルの説明を参照してください。

## <a name="send-request-to-a-service"></a><a name="invoke"></a> サービスへの要求の送信

このポリシーは、現在の要求のターゲット URL を `http://localhost:3500/v1.0/invoke/{app-id}[.{ns-name}]/method/{method-name}` に設定します。この際、テンプレート パラメーターはポリシー ステートメントで指定された値に置き換えられます。

このポリシーでは、Dapr がゲートウェイと同じポッドのサイドカー コンテナーで実行されることを前提としています。 Dapr ランタイムは、要求を受け取ったときに、サービス検出と実際の呼び出しを実行します。これには、HTTP と gRPC の間のプロトコル変換、再試行、分散トレース、エラー処理が含まれます。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<set-backend-service backend-id="dapr" dapr-app-id="app-id" dapr-method="method-name" dapr-namespace="ns-name" />
```

### <a name="examples"></a>例

#### <a name="example"></a>例

次の例は、"echo" という名前のマイクロサービスで "back" という名前のメソッドを呼び出す方法を示しています。 `set-backend-service` ポリシーは、送信先 URL を `http://localhost:3500/v1.0/invoke/echo.echo-app/method/back` に設定します。 `forward-request` ポリシーによって、要求が Dapr ランタイムにディスパッチされ、そこからマイクロサービスに配信されます。

ここでは、わかりやすくするために `forward-request` ポリシーを示しています。 通常、このポリシーは `base` キーワードによってグローバル スコープから "継承" されます。

```xml
<policies>
    <inbound>
        <base />
        <set-backend-service backend-id="dapr" dapr-app-id="echo" dapr-method="back" dapr-namespace="echo-app" />
    </inbound>
    <backend>
        <forward-request />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

### <a name="elements"></a>要素

| 要素             | 説明  | 必須 |
|---------------------|--------------|----------|
| set-backend-service | ルート要素 | はい      |

### <a name="attributes"></a>属性

| 属性        | 説明                     | 必須 | Default |
|------------------|---------------------------------|----------|---------|
| backend-id       | "dapr" に設定する必要があります           | はい      | 該当なし     |
| dapr-app-id      | ターゲット マイクロサービスの名前。 Dapr の [appId](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) パラメーターを形成するために使用されます。| はい | 該当なし |
| dapr-method      | ターゲット マイクロサービスで呼び出すメソッドまたは URL の名前。 Dapr の [method-name](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) パラメーターにマップされます。| はい | 該当なし |
| dapr-namespace   | ターゲット マイクロサービスが存在する名前空間の名前。 Dapr の [appId](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) パラメーターを形成するために使用されます。| いいえ | 該当なし |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

- **ポリシー セクション:** inbound
- **ポリシー スコープ:** すべてのスコープ

## <a name="send-message-to-pubsub-topic"></a><a name="pubsub"></a> Pub/Sub トピックへのメッセージの送信

このポリシーは、Dapr Publish/Subscribe トピックにメッセージを送信するように API Management ゲートウェイに指示します。 このポリシーでは、`http://localhost:3500/v1.0/publish/{{pubsub-name}}/{{topic}}` への HTTP POST 要求を実行することによってこれを実現します。この際、テンプレート パラメーターが置き換えられ、ポリシー ステートメントで指定されたコンテンツが追加されます。

このポリシーでは、Dapr ランタイムがゲートウェイと同じポッドのサイドカー コンテナーで実行されていることを前提としています。 Dapr ランタイムは、Pub/Sub セマンティクスを実装しています。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<publish-to-dapr pubsub-name="pubsub-name" topic="topic-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template="Liquid" content-type="application/json">
    <!-- message content -->
</publish-to-dapr>
```

### <a name="examples"></a>例

#### <a name="example"></a>例

次の例では、現在の要求の本文を "orders" Pub/Sub [コンポーネント](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters)の "new" [トピック](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters)に送信する方法を示します。 Dapr ランタイムから受信された応答は、 [context](api-management-policy-expressions.md#ContextVariables) オブジェクトの Variables コレクションの "dapr-response" エントリに格納されます。

Dapr ランタイムがターゲット トピックを見つけられないなどでエラーを返した場合は、"on-error" セクションがトリガーされます。 Dapr ランタイムから受信された応答は、呼び出し元に逐語的に返されます。 それ以外の場合は、既定の `200 OK` 応答が返されます。

"backend" セクションは空で、要求はバックエンドに転送されません。

```xml
<policies>
     <inbound>
        <base />
        <publish-to-dapr
           pubsub-name="orders"
               topic="new"
               response-variable-name="dapr-response">
            @(context.Request.Body.As<string>())
        </publish-to-dapr>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="pubsub-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>要素

| 要素             | 説明  | 必須 |
|---------------------|--------------|----------|
| publish-to-dapr     | ルート要素 | はい      |

### <a name="attributes"></a>属性

| 属性        | 説明                     | 必須 | Default |
|------------------|---------------------------------|----------|---------|
| pubsub-name      | ターゲット PubSub コンポーネントの名前。 Dapr の [pubsubname](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) パラメーターにマップされます。 存在しない場合、__topic__ 属性値は `pubsub-name/topic-name` の形式である必要があります。    | いいえ       | なし    |
| topic            | トピックの名前。 Dapr の [topic](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) パラメーターにマップされます。               | はい      | 該当なし     |
| ignore-error     | `true` に設定すると、Dapr ランタイムからエラーを受信したときに ["on-error"](api-management-error-handling-policies.md) セクションをトリガーしないようにポリシーに指示します | いいえ | `false` |
| response-variable-name | Dapr ランタイムからの応答を格納するために使用する [Variables](api-management-policy-expressions.md#ContextVariables) コレクションのエントリの名前 | いいえ | なし |
| timeout | Dapr ランタイムが応答するのを待機する時間 (秒)。 範囲は 1 ～ 240 秒です。 | いいえ | 5 |
| template | メッセージの内容を変換するために使用するテンプレート エンジン。 サポートされている値は "Liquid" のみです。 | いいえ | なし |
| content-type | メッセージ コンテンツの種類。 現在、サポートされている値は "application/json" のみです。 | いいえ | なし |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

- **ポリシー セクション:** inbound、outbound、on-error
- **ポリシー スコープ:** すべてのスコープ

## <a name="trigger-output-binding"></a><a name="bind"></a> トリガー出力バインド

このポリシーは、送信 Dapr [バインド](https://github.com/dapr/docs/blob/master/README.md)をトリガーするように API Management ゲートウェイに指示します。 このポリシーでは、`http://localhost:3500/v1.0/bindings/{{bind-name}}` への HTTP POST 要求を実行することによってこれを実現します。この際、テンプレート パラメーターが置き換えられ、ポリシー ステートメントで指定されたコンテンツが追加されます。

このポリシーでは、Dapr ランタイムがゲートウェイと同じポッドのサイドカー コンテナーで実行されていることを前提としています。 Dapr ランタイムは、バインドによって表される外部リソースの呼び出しを担当します。

### <a name="policy-statement"></a>ポリシー ステートメント

```xml
<invoke-dapr-binding name="bind-name" operation="op-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template="Liquid" content-type="application/json">
    <metadata>
        <item key="item-name"><!-- item-value --></item>
    </metadata>
    <data>
        <!-- message content -->
    </data>
</invoke-dapr-binding>
```

### <a name="examples"></a>例

#### <a name="example"></a>例

次の例では、"external-systems" という名前の送信バインドのトリガーを示しています。操作名は "create" で、メタデータは "source" と "client-ip" という名前の 2 つのキー/値の項目で構成されており、本文は元の要求のものです。 Dapr ランタイムから受信された応答は、 [context](api-management-policy-expressions.md#ContextVariables) オブジェクトの Variables コレクションの "bind-response" エントリにキャプチャされます。

Dapr ランタイムがなんらかの理由で失敗してエラーを返した場合は、"on-error" セクションがトリガーされ、Dapr ランタイムから受信された応答が呼び出し元に逐語的に返されます。 それ以外の場合は、既定の `200 OK` 応答が返されます。

"backend" セクションは空で、要求はバックエンドに転送されません。

```xml
<policies>
     <inbound>
        <base />
        <invoke-dapr-binding
                      name="external-system"
                      operation="create"
                      response-variable-name="bind-response">
            <metadata>
                <item key="source">api-management</item>
                <item key="client-ip">@( context.Request.IpAddress )</item>
            </metadata>
            <data>
                @( context.Request.Body.As<string>() )
            </data>
        </invoke-dapr-binding>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="bind-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>要素

| 要素             | 説明  | 必須 |
|---------------------|--------------|----------|
| invoke-dapr-binding | ルート要素 | はい      |
| metadata            | キーと値のペアの形式による、バインド固有のメタデータ。 Dapr の [metadata](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) プロパティにマップされます。 | いいえ |
| [データ]            | メッセージの内容。 Dapr の [data](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) プロパティにマップされます。 | いいえ |


### <a name="attributes"></a>属性

| 属性        | 説明                     | 必須 | Default |
|------------------|---------------------------------|----------|---------|
| name            | ターゲット バインドの名前。 Dapr で[定義されている](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#bindings-structure)バインドの名前と一致している必要があります。           | はい      | 該当なし     |
| 操作       | ターゲット操作の名前 (バインディング固有)。 Dapr の [operation](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) プロパティにマップされます。 | いいえ | なし |
| ignore-error     | `true` に設定すると、Dapr ランタイムからエラーを受信したときに ["on-error"](api-management-error-handling-policies.md) セクションをトリガーしないようにポリシーに指示します | いいえ | `false` |
| response-variable-name | Dapr ランタイムからの応答を格納するために使用する [Variables](api-management-policy-expressions.md#ContextVariables) コレクションのエントリの名前 | いいえ | なし |
| timeout | Dapr ランタイムが応答するのを待機する時間 (秒)。 範囲は 1 ～ 240 秒です。 | いいえ | 5 |
| template | メッセージの内容を変換するために使用するテンプレート エンジン。 サポートされている値は "Liquid" のみです。 | いいえ | なし |
| content-type | メッセージ コンテンツの種類。 現在、サポートされている値は "application/json" のみです。 | いいえ | なし |

### <a name="usage"></a>使用法

このポリシーは、次のポリシー [セクション](./api-management-howto-policies.md#sections)と[スコープ](./api-management-howto-policies.md#scopes)で使用できます。

- **ポリシー セクション:** inbound、outbound、on-error
- **ポリシー スコープ:** すべてのスコープ
