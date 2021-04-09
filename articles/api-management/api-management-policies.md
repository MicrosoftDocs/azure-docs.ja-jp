---
title: Azure API Management のポリシー | Microsoft Docs
description: Azure API Management で使用できるポリシーについて説明します。 ポリシーを使用すると、発行者は構成を通じて API の動作を変更できます。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/17/2021
ms.author: apimpm
ms.openlocfilehash: e809efa9da32da5fe9ca296608c602e770f78265
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103562350"
---
# <a name="api-management-policies"></a>API Management policies (API Management のポリシー)
このセクションでは、次の API Management ポリシーについて説明します。 ポリシーを追加および構成する方法については、「 [Azure API Management のポリシー](api-management-howto-policies.md)」をご覧ください。

 ポリシーは、発行者がその構成を通じて API の動作を変更できる、システムの強力な機能の 1 つです。 ポリシーは、API の要求または応答に対して順に実行される一連のステートメントのコレクションです。 代表的なステートメントとしては、XML 形式から JSON 形式への変換や、(開発者からの呼び出しの回数を制限する) 呼び出しレート制限が挙げられます。 他にも多数のポリシーが標準で提供されています。

 ポリシーの式は、ポリシーで特に指定されていない限り、任意の API Management ポリシーで属性値またはテキスト値として使用できます。 [制御フロー](api-management-advanced-policies.md#choose) ポリシーや[変数の設定](api-management-advanced-policies.md#set-variable)ポリシーなど、一部のポリシーはポリシーの式に基づいています。 詳細については、「[詳細なポリシー](api-management-advanced-policies.md#AdvancedPolicies)」と「[ポリシーの式](api-management-policy-expressions.md)」をご覧ください。

##  <a name="policies"></a><a name="ProxyPolicies"></a> ポリシー

-   [アクセス制限ポリシー](api-management-access-restriction-policies.md#AccessRestrictionPolicies)
    -   [HTTP ヘッダーを確認する](api-management-access-restriction-policies.md#CheckHTTPHeader) - HTTP ヘッダーの存在と値の両方、またはそのどちらかを適用します。
    -   [呼び出しレートをサブスクリプション別に制限する](api-management-access-restriction-policies.md#LimitCallRate) - 呼び出しレートをサブスクリプションに基づいて制限することで、API の使用量の急増を防ぎます。
    -   [呼び出しレートをキー別に制限する](api-management-access-restriction-policies.md#LimitCallRateByKey) - 呼び出しレートをキーに基づいて制限することで、API の使用量の急増を防ぎます。
    -   [呼び出し元 IP を制限する](api-management-access-restriction-policies.md#RestrictCallerIPs) - 特定の IP アドレスとアドレス範囲の両方、またはそのどちらかからの呼び出しをフィルター処理 (許可/拒否) します。
    -   [使用量のクォータをサブスクリプション別に設定する](api-management-access-restriction-policies.md#SetUsageQuota) - 更新可能な呼び出しまたは有効期間中の呼び出しのボリュームと帯域幅クォータの両方またはそのどちらかをサブスクリプションに基づいて適用できます。
    -   [使用量のクォータをキー別に設定する](api-management-access-restriction-policies.md#SetUsageQuotaByKey) - 更新可能な呼び出しまたは有効期間中の呼び出しのボリュームと帯域幅クォータの両方またはそのどちらかをキーに基づいて適用できます。
    -   [JWT を検証する](api-management-access-restriction-policies.md#ValidateJWT) - 指定された HTTP ヘッダーまたは指定されたクエリ パラメーターから抽出した JWT の存在と有効性を適用します。
-   [高度なポリシー](api-management-advanced-policies.md#AdvancedPolicies)
    -   [制御フロー](api-management-advanced-policies.md#choose) - ブール式の評価に基づいてポリシー ステートメントを条件付きで適用します。
    -   [要求を転送する](api-management-advanced-policies.md#ForwardRequest) - バックエンド サービスに要求を転送します。
    -   [コンカレンシーを制限する](api-management-advanced-policies.md#LimitConcurrency) - 含まれているポリシーが指定された数を超える要求によって同時に実行されないようにします。
    -   [Event Hub にログを記録する](api-management-advanced-policies.md#log-to-eventhub) - Logger エンティティによって定義されたメッセージ ターゲットに、指定された形式でメッセージを送信します。
    -   [Mock response (モック応答)](api-management-advanced-policies.md#mock-response) - パイプラインの実行を中止し、モック応答を呼び出し元に直接返します。
    -   [再試行](api-management-advanced-policies.md#Retry) - 条件が満たされるまで、囲まれたポリシー ステートメントの実行を再試行します。 実行は、指定された間隔で、指定された最大試行回数まで繰り返されます。
    -   [応答を返す](api-management-advanced-policies.md#ReturnResponse) - パイプラインの実行を中止し、指定された応答を呼び出し元に直接返します。
    -   [1 方向の要求を送信する](api-management-advanced-policies.md#SendOneWayRequest) - 指定された URL に要求を送信します。応答は待機しません。
    -   [要求を送信する](api-management-advanced-policies.md#SendRequest) - 指定された URL に要求を送信します。
    -   [HTTP プロキシを設定する](api-management-advanced-policies.md#SetHttpProxy) - HTTP プロキシ経由で、転送された要求をルーティングできます。
    -   [変数の設定](api-management-advanced-policies.md#set-variable) - 名前付きコンテキスト変数の値を、後でアクセスできるように保持します。
    -   [要求メソッドを設定する](api-management-advanced-policies.md#SetRequestMethod) - 要求の HTTP メソッドを変更できます。
    -   [状態コードを設定する](api-management-advanced-policies.md#SetStatus) - HTTP 状態コードを指定された値に変更します。
    -   [トレース](api-management-advanced-policies.md#Trace) - [API Inspector](./api-management-howto-api-inspector.md) 出力、Application Insights テレメトリ、およびリソース ログにカスタム トレースを追加します。
    -   [待機](api-management-advanced-policies.md#Wait) - 含まれている[要求を送信する](api-management-advanced-policies.md#SendRequest)、[キャッシュからの値の取得](api-management-caching-policies.md#GetFromCacheByKey)、または[制御フロー](api-management-advanced-policies.md#choose) ポリシーが完了するまで待機してから次に進みます。
-   [認証ポリシー](api-management-authentication-policies.md#AuthenticationPolicies)
    -   [基本認証](api-management-authentication-policies.md#Basic) -基本認証を使用してバックエンド サービスで認証します。
    -   [クライアント証明書による認証](api-management-authentication-policies.md#ClientCertificate) -クライアント証明書を使用してバックエンド サービスで認証します。
    -   [マネージド ID による認証](api-management-authentication-policies.md#ManagedIdentity) - [マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用してバックエンド サービスで認証します。
-   [キャッシュ ポリシー](api-management-caching-policies.md#CachingPolicies)
    -   [キャッシュから取得](api-management-caching-policies.md#GetFromCache) - キャッシュを検索して、キャッシュに格納された有効な応答があればそれを返します。
    -   [キャッシュに格納](api-management-caching-policies.md#StoreToCache) - 指定されたキャッシュ制御の構成に従って応答をキャッシュに格納します。
    -   [キャッシュから値を取得](api-management-caching-policies.md#GetFromCacheByKey) - キャッシュされたキー別の項目を取得します。
    -   [値をキャッシュに格納](api-management-caching-policies.md#StoreToCacheByKey) - 項目をキー別にキャッシュに格納します。
    -   [キャッシュから値を削除](api-management-caching-policies.md#RemoveCacheByKey) - キー別にキャッシュ内の項目を削除します。
-   [クロス ドメイン ポリシー](api-management-cross-domain-policies.md#CrossDomainPolicies)
    -   [クロスドメイン呼び出しを許可](api-management-cross-domain-policies.md#AllowCrossDomainCalls) - Adobe Flash や Microsoft Silverlight ブラウザーベースのクライアントから API を利用できるようにします。
    -   [CORS](api-management-cross-domain-policies.md#CORS) - クロス オリジン リソース共有 (CORS) のサポートを操作または API に追加して、ブラウザーベースのクライアントからのクロスドメイン呼び出しを可能にします。
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) - JSON with padding (JSONP) のサポートを操作または API に追加して、JavaScript ブラウザーベースのクライアントからのクロスドメイン呼び出しを可能にします。
-   [変換ポリシー](api-management-transformation-policies.md#TransformationPolicies)
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
- [Dapr 統合ポリシー](api-management-dapr-policies.md)
    - [サービスへの要求の送信](api-management-dapr-policies.md#invoke) - Dapr ランタイムを使用して Dapr マイクロサービスを検索し、このマイクロサービスとの通信を確実に行います。
    -  [Pub/Sub トピックへのメッセージの送信](api-management-dapr-policies.md#pubsub) - Dapr ランタイムを使用して、Publish/Subscribe トピックにメッセージを発行します。
    -  [トリガー出力バインド](api-management-dapr-policies.md#bind) - Dapr ランタイムを使用して、出力バインドを介して外部システムを呼び出します。
- [検証ポリシー](validation-policies.md)
    - [コンテンツを検証する](validation-policies.md#validate-content) - API スキーマに対して要求または応答の本文のサイズまたは JSON スキーマを検証します。
. 
    - [パラメーターを検証する](validation-policies.md#validate-parameters) - API スキーマに対して要求ヘッダー、クエリ、またはパス パラメーターを検証します。
    - [ヘッダーを検証する](validation-policies.md#validate-headers) - API スキーマに対して応答ヘッダーを検証します。
    - [状態コードを検証する](validation-policies.md#validate-status-code) - API スキーマに対して応答の HTTP 状態コードを検証します。

## <a name="next-steps"></a>次のステップ
ポリシーを使用する方法の詳細については、次のトピックを参照してください。

+ [API Management のポリシー](api-management-howto-policies.md)
+ [API を変換する](transform-api.md)
+ [ポリシーのサンプル](./policy-reference.md)
