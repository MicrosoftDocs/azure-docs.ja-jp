---
title: Azure API Management ポリシーにおけるエラー処理 | Microsoft Docs
description: Azure API Management 内の要求の処理中に発生するエラーに対応する方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: a3b6f90d0aa26b478c0f2fcefac55dcd509da437
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92070947"
---
# <a name="error-handling-in-api-management-policies"></a>API Management のポリシーにおけるエラー処理

Azure API Management では、パブリッシャーは `ProxyError` オブジェクトを指定することにより、要求の処理中に発生するエラーに対応することができます。 `ProxyError` オブジェクトは [context.LastError](api-management-policy-expressions.md#ContextVariables) プロパティからアクセスでき、`on-error` ポリシー セクションのポリシーで使用できます。 この記事では、Azure API Management におけるエラー処理機能向けの参考資料を提供します。

## <a name="error-handling-in-api-management"></a>API Management でのエラー処理

Azure API Management のポリシーは、次の例で示すとおり、`inbound`、`backend`、`outbound`、および `on-error` のセクションに分かれています。

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is
         forwarded to the backend service go here -->
    </backend>
    <outbound>
      <!-- statements to be applied to the response go here -->
    </outbound>
    <on-error>
        <!-- statements to be applied if there is an error
             condition go here -->
  </on-error>
</policies>
```

要求の処理中、要求のスコープ内にあるすべてのポリシーと共に組み込みの手順が実行されます。 エラーが発生すると、処理は直ちに `on-error` ポリシー セクションにジャンプします。
`on-error` ポリシー セクションは、任意のスコープで使用できます。 API パブリッシャーは、イベント ハブへのエラーの記録や、呼び出し元に戻るための新しい応答の作成などのカスタム動作を構成できます。

> [!NOTE]
> `on-error` セクションは、既定ではポリシーに存在しません。 `on-error` セクションをポリシーに追加するには、ポリシー エディターで目的のポリシーを参照し、このセクションを追加します。 ポリシーを構成する方法の詳細については、「[Azure API Management のポリシー](./api-management-howto-policies.md)」を参照してください。
>
> `on-error` セクションがない場合、エラーが発生すると、呼び出し元は 400 または 500 HTTP 応答メッセージを受信します。

### <a name="policies-allowed-in-on-error"></a>エラー状態で使用できるポリシー

次のポリシーは、`on-error` ポリシー セクションで使用できます。

-   [choose](api-management-advanced-policies.md#choose)
-   [set-variable](api-management-advanced-policies.md#set-variable)
-   [find-and-replace](api-management-transformation-policies.md#Findandreplacestringinbody)
-   [return-response](api-management-advanced-policies.md#ReturnResponse)
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)
-   [set-method](api-management-advanced-policies.md#SetRequestMethod)
-   [set-status](api-management-advanced-policies.md#SetStatus)
-   [send-request](api-management-advanced-policies.md#SendRequest)
-   [send-one-way-request](api-management-advanced-policies.md#SendOneWayRequest)
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)
-   [limit-concurrency](api-management-advanced-policies.md#LimitConcurrency)
-   [mock-response](api-management-advanced-policies.md#mock-response)
-   [retry](api-management-advanced-policies.md#Retry)
-   [trace](api-management-advanced-policies.md#Trace)

## <a name="lasterror"></a>lastError

エラーが発生し、コントロールが `on-error` ポリシー セクションにジャンプすると、エラーは [context.LastError](api-management-policy-expressions.md#ContextVariables) プロパティ内に格納されます。これには、`on-error` セクションにあるポリシーがアクセス可能です。 LastError のプロパティは次のとおりです。

| 名前       | Type   | 説明                                                                                               | 必須 |
| ---------- | ------ | --------------------------------------------------------------------------------------------------------- | -------- |
| `Source`   | string | エラーが発生した要素を指定します。 ポリシーまたは組み込みパイプライン ステップ名のいずれかになります。      | はい      |
| `Reason`   | string | エラー処理に使用できる、マシンに適したエラー コード。                                       | いいえ       |
| `Message`  | string | 人間が判読できるエラーの説明。                                                                         | はい      |
| `Scope`    | string | エラーが発生したスコープの名前 ("global"、"product"、"api"、または "operation") | いいえ       |
| `Section`  | string | エラーが発生したセッション名。 設定される可能性がある値: "inbound"、"backend"、"outbound"、"on-error"。      | いいえ       |
| `Path`     | string | 入れ子になったポリシー (例: "choose[3]/when[2]") を指定します。                                                 | いいえ       |
| `PolicyId` | string | エラーが発生したポリシーの `id` 属性の値 (顧客が指定した場合)             | いいえ       |

> [!TIP]
> context.Response.StatusCode によって状態コードにアクセスできます。

> [!NOTE]
> すべてのポリシーには、ポリシーのルート要素に追加できる、省略可能な `id` 属性があります。 エラー条件が発生したときにこの属性がポリシーに存在する場合、`context.LastError.PolicyId` プロパティを使用して、この属性の値を取得できます。

## <a name="predefined-errors-for-built-in-steps"></a>組み込みの手順向けの定義済みエラー

次のエラーは、組み込みの処理手順の評価時に発生する可能性があるエラー条件用に定義されています。

| source        | 条件                                 | 理由                  | Message                                                                                                                |
| ------------- | ----------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| configuration | API または操作に URI が一致しません | OperationNotFound       | 操作に受信要求を一致させることができません。                                                                      |
| authorization | サブスクリプション キーが指定されていません             | SubscriptionKeyNotFound | サブスクリプション キーがないため、アクセスが拒否されました。 この API に要求を行うときは、サブスクリプション キーを指定してください。 |
| authorization | サブスクリプション キー値が無効です         | SubscriptionKeyInvalid  | サブスクリプション キーが無効なため、アクセスが拒否されました。 アクティブなサブスクリプションへの有効なキーを指定してください。            |
| 複数 | 要求の保留中にクライアントによって (クライアントから API Management ゲートウェイへの) ダウンストリーム接続が中止されました | ClientConnectionFailure | 複数 |
| 複数 | (API Management ゲートウェイからバックエンド サービスへの) アップストリーム接続が確立されなかったか、バックエンドによって中断されました | BackendConnectionFailure | 複数 |
| 複数 | 特定の式の評価中にランタイム例外が発生しました | ExpressionValueEvaluationFailure | 複数 |

## <a name="predefined-errors-for-policies"></a>ポリシーの定義済みのエラー

次のエラーは、ポリシーの評価時に発生する可能性があるエラー条件用に定義されています。

| source       | 条件                                                       | 理由                    | Message                                                                                                                              |
| ------------ | --------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| rate-limit   | レート制限を超過                                             | RateLimitExceeded         | レート制限を超過しています。                                                                                                               |
| quota        | クォータを超過した                                                  | QuotaExceeded             | 呼び出しボリュームのクォータを超過しています。 クォータは xx:xx:xx 後に補充されます。 または、帯域幅クォータがありません。 クォータは xx:xx:xx 後に補充されます。 |
| jsonp        | コールバック パラメーターの値が無効です (不正な文字が含まれています) | CallbackParameterInvalid  | コールバック パラメーター {callback-parameter-name} の値が、有効な JavaScript 識別子ではありません。                                          |
| ip-filter    | 要求からの呼び出し元 IP の解析に失敗しました                          | FailedToParseCallerIP     | 呼び出し元の IP アドレスを確立できませんでした。 アクセスが拒否されました。                                                                        |
| ip-filter    | 呼び出し元 IP が許可リストにありません                                | CallerIpNotAllowed        | 呼び出し元 IP アドレス {ip-address} は許可されていません。 アクセスが拒否されました。                                                                        |
| ip-filter    | 呼び出し元 IP がブロック リストにあります                                    | CallerIpBlocked           | 呼び出し元 IP アドレスはブロックされています。 アクセスが拒否されました。                                                                                         |
| check-header | 必須のヘッダーが表示されない、または値がありません               | HeaderNotFound            | 要求にヘッダー {header-name} がありません。 アクセスが拒否されました。                                                                    |
| check-header | 必須のヘッダーが表示されない、または値がありません               | HeaderValueNotAllowed     | ヘッダー {header-name} 値 {header-value} は許可されていません。 アクセスが拒否されました。                                                          |
| validate-jwt | JWT トークンが要求にありません                                 | TokenNotFound             | JWT が要求にありません。 アクセスが拒否されました。                                                                                         |
| validate-jwt | 署名の検証に失敗しました                                     | TokenSignatureInvalid     | <JWT ライブラリからのメッセージ\>。 アクセスが拒否されました。                                                                                          |
| validate-jwt | 無効な対象者                                                | TokenAudienceNotAllowed   | <JWT ライブラリからのメッセージ\>。 アクセスが拒否されました。                                                                                          |
| validate-jwt | 無効な発行者                                                  | TokenIssuerNotAllowed     | <JWT ライブラリからのメッセージ\>。 アクセスが拒否されました。                                                                                          |
| validate-jwt | トークンの有効期限が切れています                                                   | TokenExpired              | <JWT ライブラリからのメッセージ\>。 アクセスが拒否されました。                                                                                          |
| validate-jwt | 署名キーを ID で解決できませんでした                            | TokenSignatureKeyNotFound | <JWT ライブラリからのメッセージ\>。 アクセスが拒否されました。                                                                                          |
| validate-jwt | 必要なクレームがトークンにありません                          | TokenClaimNotFound        | 次のクレームが JWT トークンにありません: <c1\>, <c2\>, … アクセスが拒否されました。                                                            |
| validate-jwt | クレームの値が一致しません                                           | TokenClaimValueNotAllowed | クレーム {claim-name} の値 {claim-value} は許可されていません。 アクセスが拒否されました。                                                             |
| validate-jwt | その他の検証の失敗                                       | JwtInvalid                | <JWT ライブラリからのメッセージ\>                                                                                                          |
| forward-request または send-request | HTTP 応答の状態コードとヘッダーが、構成されたタイムアウト期間内にバックエンドから受信されませんでした | タイムアウト | 複数 |

## <a name="example"></a>例

API ポリシーを次のように設定します。

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <set-header name="ErrorSource" exists-action="override">
            <value>@(context.LastError.Source)</value>
        </set-header>
        <set-header name="ErrorReason" exists-action="override">
            <value>@(context.LastError.Reason)</value>
        </set-header>
        <set-header name="ErrorMessage" exists-action="override">
            <value>@(context.LastError.Message)</value>
        </set-header>
        <set-header name="ErrorScope" exists-action="override">
            <value>@(context.LastError.Scope)</value>
        </set-header>
        <set-header name="ErrorSection" exists-action="override">
            <value>@(context.LastError.Section)</value>
        </set-header>
        <set-header name="ErrorPath" exists-action="override">
            <value>@(context.LastError.Path)</value>
        </set-header>
        <set-header name="ErrorPolicyId" exists-action="override">
            <value>@(context.LastError.PolicyId)</value>
        </set-header>
        <set-header name="ErrorStatusCode" exists-action="override">
            <value>@(context.Response.StatusCode.ToString())</value>
        </set-header>
        <base />
    </on-error>
</policies>
```

そして承認されていない要求を送信すると、結果として次のような応答があります。

![未承認エラーの応答](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>次のステップ

ポリシーを使用する方法の詳細については、次のトピックを参照してください。

-   [API Management のポリシー](api-management-howto-policies.md)
-   [API を変換する](transform-api.md)
-   ポリシー ステートメントとその設定の一覧に関する[ポリシー リファレンス](./api-management-policies.md)
-   [ポリシーのサンプル](./policy-reference.md)