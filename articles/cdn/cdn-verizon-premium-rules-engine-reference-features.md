---
title: Azure CDN from Verizon Premium ルール エンジンの機能 | Microsoft Docs
description: Azure CDN from Verizon Premium ルール エンジン機能に関するリファレンス ドキュメント。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 9177ac544c83305ae95ad681d3dc9f84ac64ea36
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593238"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Azure CDN from Verizon Premium ルール エンジンの機能

このトピックでは、Azure Content Delivery Network (CDN) [ルール エンジン](cdn-verizon-premium-rules-engine.md)で利用できる機能の詳しい説明を一覧表示します。

ルールの 3 番目の部分は、機能です。 機能では、一連の一致条件によって特定される要求の種類に適用されるアクションの種類を定義します。

## <a name="access-features"></a>アクセス機能

以下の機能でコンテンツのアクセスが制御されます。

Name | 目的
-----|--------
[Deny Access (403) (アクセス拒否 (403))](#deny-access-403) | すべての要求を拒否し、「403 Forbidden」応答を返すかどうかを決定します。
[Token Auth (トークン認証)](#token-auth) | トークン ベースの認証を要求に適用するかどうかを決定します。
[Token Auth Denial Code (トークン認証拒否コード)](#token-auth-denial-code) | トークン ベース認証によって要求が拒否されるときに、ユーザーに返される応答の種類を決定します。
[Token Auth Ignore URL Case (トークン認証の URL 大文字/小文字の無視)](#token-auth-ignore-url-case) | トークン ベースの認証で URL を比較するとき、大文字と小文字を区別するかどうかを決定します。
[Token Auth Parameter (トークン認証パラメーター)](#token-auth-parameter) | トークン基準の認証のクエリ文字列パラメーターの名前を変更するかどうかを決定します。

## <a name="caching-features"></a>キャッシュ機能

以下の機能でコンテンツをキャッシュするタイミングと方法をカスタマイズできます。

Name | 目的
-----|--------
[Bandwidth Parameters (帯域幅パラメーター)](#bandwidth-parameters) | 帯域幅調整パラメーター (たとえば ec_rate や ec_prebuf) を有効にするかどうかを決定します。
[Bandwidth Throttling (帯域幅調整)](#bandwidth-throttling) | ポイント オブ プレゼンス (POP) からの応答の帯域幅を調整します。
[Bypass Cache (キャッシュ バイパス)](#bypass-cache) | 要求がキャッシュをバイパスするかどうかを決定します。
[Cache Control Header Treatment (Cache-Control ヘッダーの処理)](#cache-control-header-treatment) | External Max-Age (外部最長有効期間) 機能がアクティブになっているとき、POP で `Cache-Control` ヘッダーの生成を制御します。
[Cache-Key Query String (キャッシュキー クエリ文字列)](#cache-key-query-string) | 要求に関連付けられているクエリ文字列パラメーターをキャッシュキーに含めるかどうかを決定します。
[Cache-Key Rewrite (キャッシュキー書き換え)](#cache-key-rewrite) | 要求に関連付けられているキャッシュ キーを書き換えます。
[Complete Cache Fill (完全キャッシュ入力)](#complete-cache-fill) | 要求の結果、POP で一部のキャッシュが不足したときの動作を決定します。
[Compress File Types (圧縮ファイルの種類)](#compress-file-types) | サーバーで圧縮されるファイルのファイル形式を定義します。
[Default Internal Max-Age (既定の内部最長有効期間)](#default-internal-max-age) | POP と配信元サーバーの間のキャッシュ再有効化の既定の最長有効期間を決定します。
[Expires Header Treatment (Expires ヘッダーの処理)](#expires-header-treatment) | External Max-Age (外部最長有効期間) 機能がアクティブになっているとき、POP で `Expires` ヘッダーの生成を制御します。
[External Max-Age (外部最長有効期間)](#external-max-age) | ブラウザーと POP の間のキャッシュ再有効化の最長有効期間を決定します。
[Force Internal Max-Age (内部最長有効期間の強制)](#force-internal-max-age) | POP と配信元サーバーの間のキャッシュ再有効化の最長有効期間を決定します。
[H.264 Support (HTTP Progressive Download) (H.264 サポート (HTTP プログレッシブ ダウンロード))](#h264-support-http-progressive-download) | コンテンツのストリーム配信に使用される H.264 ファイル形式の種類を決定します。
[Honor no-cache request (キャッシュなし要求の許可)](#honor-no-cache-request) | HTTP クライアントのキャッシュなし要求を配信元サーバーに転送するかどうかを決定します。
[Ignore Origin no-cache (配信元のキャッシュなしを無視する)](#ignore-origin-no-cache) | 配信元サーバーから提供された特定のディレクティブを CDN が無視するかどうかを決定します。
[Ignore Unsatisfiable Ranges (満たされない範囲を無視する)](#ignore-unsatisfiable-ranges) | 要求で「416 Requested Range Not Satisfiable」状態コードが生成されるとき、クライアントに返される応答を決定します。
[Internal Max-Stale (内部 Max-Stale)](#internal-max-stale) | POP が配信元サーバーでキャッシュされたアセットを再有効化できないとき、通常の有効期間を過ぎてから、キャッシュされたアセットを POP から提供できる期間を制御します。
[Partial Cache Sharing (部分キャッシュ共有)](#partial-cache-sharing) | 部分的にキャッシュされたコンテンツを要求で生成できるかどうかを決定します。
[Prevalidate Cached Content (キャッシュされたコンテンツの事前有効化)](#prevalidate-cached-content) | TTL が期限切れになる前に、キャッシュされたコンテンツが早期再検証の対象となるかどうかを決定します。
[Refresh Zero Byte Cache Files (ゼロバイト キャッシュ ファイルの更新)](#refresh-zero-byte-cache-files) | 0 バイトのキャッシュ資産に対する HTTP クライアントの要求を POP が処理する方法を決定します。
[Set Cacheable Status Codes (キャッシュ可能状態コードの設定)](#set-cacheable-status-codes) | 結果としてコンテンツがキャッシュされるステータス コードのセットを定義します。
[Stale Content Delivery on Error (エラー時の古いコンテンツ配信)](#stale-content-delivery-on-error) | キャッシュの再検証中にエラーが発生したとき、または顧客の配信元サーバーから要求されたコンテンツを取得するときに、期限切れのキャッシュされたコンテンツを配信するかどうかを決定します。
[Stale While Revalidate (古いキャッシュを返し、同時に再有効化)](#stale-while-revalidate) | 古いクライアントを要求元に提供することを POP に許可し、同時に再有効化することにより、パフォーマンスを改善します。

## <a name="comment-feature"></a>コメント機能

この機能は、ルール内で追加情報を指定するために設計されています。

Name | 目的
-----|--------
[Comment](#comment) | ルール内にメモを追加できるようにします。

## <a name="header-features"></a>ヘッダー機能

以下の機能では、要求または応答のヘッダーを追加、修正、削除できます。

Name | 目的
-----|--------
[Age Response Header (Age 応答ヘッダー)](#age-response-header) | 要求元に送信する応答に Age 応答ヘッダーを追加するどうかを決定します。
[Debug Cache Response Headers (キャッシュ応答ヘッダーのデバッグ)](#debug-cache-response-headers) | 要求された資産のキャッシュ ポリシーに関する情報を提供する X-EC-Debug 応答ヘッダーを応答に追加できるかどうかを決定します。
[Modify Client Request Header (クライアント要求ヘッダーの修正)](#modify-client-request-header) | 要求のヘッダーを上書き、追加、削除します。
[Modify Client Response Header (クライアント応答ヘッダーの修正)](#modify-client-response-header) | 応答のヘッダーを上書き、追加、削除します。
[Set Client IP Custom Header (クライアント IP カスタム ヘッダーの設定)](#set-client-ip-custom-header) | 要求側クライアントの IP アドレスをカスタム要求ヘッダーとして要求に追加することを許可します。

## <a name="logging-features"></a>ログ記録機能

以下の機能では、生ログ ファイルに保存されているデータをカスタマイズできます。

Name | 目的
-----|--------
[Custom Log Field 1 (カスタム ログ フィールド 1)](#custom-log-field-1) | 生ログ ファイルのカスタム ログ フィールドに割り当てる形式とコンテンツを決定します。
[Log Query String (ログ クエリ文字列)](#log-query-string) | アクセス ログに URL と共にクエリ文字列を保存するかどうかを決定します。


<!---
## Optimize

These features determine whether a request will undergo the optimizations provided by Edge Optimizer.

Name | Purpose
-----|--------
Edge Optimizer | Determines whether Edge Optimizer can be applied to a request.
Edge Optimizer – Instantiate Configuration | Instantiates or activates the Edge Optimizer configuration associated with a site.

### Edge Optimizer
**Purpose:** Determines whether Edge Optimizer can be applied to a request.

If this feature has been enabled, then the following criteria must also be met before the request will be processed by Edge Optimizer:

- The requested content must use an edge CNAME URL.
- The edge CNAME referenced in the URL must correspond to a site whose configuration has been activated in a rule.

This feature requires the ADN platform and the Edge Optimizer feature.

Value|Result
-|-
Enabled|Indicates that the request is eligible for Edge Optimizer processing.
Disabled|Restores the default behavior. The default behavior is to deliver content over the ADN platform without any additional processing.

**Default Behavior:** Disabled


### Edge Optimizer - Instantiate Configuration
**Purpose:** Instantiates or activates the Edge Optimizer configuration associated with a site.

This feature requires the ADN platform and the Edge Optimizer feature.

Key information:

- Instantiation of a site configuration is required before requests to the corresponding edge CNAME can be processed by Edge Optimizer.
- This instantiation only needs to be performed a single time per site configuration. A site configuration that has been instantiated will remain in that state until the Edge Optimizer – Instantiate Configuration feature that references it is removed from the rule.
- The instantiation of a site configuration does not mean that all requests to the corresponding edge CNAME will automatically be processed by Edge Optimizer. The Edge Optimizer feature determines whether an individual request will be processed.

If the desired site does not appear in the list, then you should edit its configuration and verify that the Active option has been marked.

**Default Behavior:** Site configurations are inactive by default.
--->

## <a name="origin-features"></a>配信元の機能

以下の機能では、CDN が配信元サーバーと通信する方法を制御できます。

Name | 目的
-----|--------
[Maximum Keep-Alive Requests (最大キープアライブ要求)](#maximum-keep-alive-requests) | キープアライブ接続の最大要求数を定義します。この数に達すると終了となります。
[Proxy Special Headers (プロキシの特殊ヘッダー)](#proxy-special-headers) | POP から配信元サーバーに転送される、CDN 固有の要求ヘッダーのセットを定義します。

## <a name="specialty-features"></a>特殊な機能

これらの機能は、上級ユーザー向けの高度な機能です。

Name | 目的
-----|--------
[Cacheable HTTP Methods (キャッシュ可能 HTTP メソッド)](#cacheable-http-methods) | ネットワークでキャッシュ可能な追加 HTTP メソッドのセットを決定します。
[Cacheable Request Body Size (キャッシュ可能要求の本文サイズ)](#cacheable-request-body-size) | POST 応答でキャッシュ可能かどうかを決定するしきい値を定義します。
[User Variable (ユーザー変数)](#user-variable) | 内部使用専用です。

## <a name="url-features"></a>URL 機能

以下の機能では、要求を別の URL にリダイレクトするか、書き換えることができます。

Name | 目的
-----|--------
[Follow Redirects (リダイレクトのフォロー)](#follow-redirects) | 顧客の配信元サーバーから返される Location (場所) ヘッダーに定義されているホスト名に要求をリダイレクトできるかどうかを決定します。
[URL Redirect (URL リダイレクト)](#url-redirect) | Location (場所) ヘッダー経由で要求をリダイレクトします。
[URL Rewrite (URL 書き換え)](#url-rewrite)  | 要求 URL を書き換えます。

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a>Azure CDN from Verizon Premium ルール エンジンの機能のリファレンス

---

### <a name="age-response-header"></a>Age Response Header (Age 応答ヘッダー)

**目的**: 要求元に送信する応答に Age 応答ヘッダーを追加するどうかを決定します。

値|結果
--|--
Enabled | 要求元に送信する応答に Age 応答ヘッダーが含められます。
Disabled | 要求元に送信する応答から Age 応答ヘッダーが除外されます。

**既定の動作**: 無効。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-parameters"></a>Bandwidth Parameters (帯域幅パラメーター)

**目的:** 帯域幅調整パラメーター (たとえば ec_rate や ec_prebuf) を有効にするかどうかを決定します。

帯域幅調整パラメーターは、クライアントの要求のデータ転送率をカスタムの比率に制限するかどうかを決定します。

値|結果
--|--
Enabled|POP で帯域幅調整要求が受け入れられます。
Disabled|POP で帯域幅調整要求が無視されます。 要求されたコンテンツは通常どおりに (つまり、帯域幅調整なしで) 提供されます。

**既定の動作:** 有効。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-throttling"></a>Bandwidth Throttling (帯域幅調整)

**目的:** POP からの応答の帯域幅を調整します。

帯域幅調整を正しく設定するには、次のオプションの両方を定義する必要があります。

オプション|説明
--|--
Kbytes per second (kb/秒)|このオプションは、応答を提供するために使用できる最大帯域幅 (Kb/秒) に設定します。
Prebuf seconds (Prebuf 秒)|このオプションを、帯域幅が調整されるまでに POP が待機する秒数に設定します。 帯域幅を制限しないこの期間の目的は、メディア プレーヤーで帯域幅調整によって途切れ途切れの再生やバッファリングの問題が発生するのを防ぐことです。

**既定の動作:** 無効。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bypass-cache"></a>Bypass Cache (キャッシュ バイパス)

**目的:** 要求がキャッシュをバイパスするかどうかを決定します。

値|結果
--|--
Enabled|コンテンツが以前に POP でキャッシュされた場合でも、すべての要求が配信元サーバーにフォールスルーされます。
Disabled|応答ヘッダーで定義されているキャッシュ ポリシーに従い、POP で資産がキャッシュされます。

**既定の動作:**

- **HTTP ラージ:** Disabled

<!---
- **ADN:** Enabled

--->

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-http-methods"></a>Cacheable HTTP Methods (キャッシュ可能 HTTP メソッド)

**目的:** ネットワークでキャッシュ可能な追加 HTTP メソッドのセットを決定します。

重要な情報:

- この機能では、GET 応答が常にキャッシュされると想定されています。 このため、この機能を設定する際に GET HTTP メソッドは含めないでください。
- この機能では POST HTTP メソッドのみがサポートされます。 POST 応答のキャッシュを有効にするには、この機能を `POST` に設定します。
- 既定では、本文が 14 Kb 未満の要求のみがキャッシュされます。 Cacheable Request Body Size (キャッシュ可能要求の本文サイズ) 機能を使用すると、最大の要求本文サイズを設定できます。

**既定の動作:** GET 応答のみがキャッシュされます。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-request-body-size"></a>Cacheable Request Body Size (キャッシュ可能要求の本文サイズ)

**目的:** POST 応答でキャッシュ可能かどうかを決定するしきい値を定義します。

このしきい値は、最大の要求本文サイズを指定することによって決定されます。 これより大きい要求本文が含まれる要求はキャッシュされません。

重要な情報:

- この機能が適用されるのは、POST 応答がキャッシュの対象になっている場合だけです。 POST 要求のキャッシュを有効にするには、Cacheable HTTP Methods (キャッシュ可能 HTTP メソッド) 機能を使用します。
- 要求本文は、次のものについて考慮されます。
    - x-www-form-urlencoded 値
    - 一意キャッシュキーの確認
- 最大の要求本文サイズを大きい値に定義すると、データ配信のパフォーマンスに影響する可能性があります。
    - **推奨値:** 14 KB
    - **最小値:** 1 KB

**既定の動作:** 14 KB

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-control-header-treatment"></a>Cache-Control Header Treatment (キャッシュ制御ヘッダーの処理)

**目的:** External Max-Age (外部最長有効期間) 機能がアクティブになっているとき、POP で `Cache-Control` ヘッダーの生成を制御します。

この種類の構成を実現する最も簡単な方法は、同じステートメントに External Max-Age (外部最長有効期間) と Cache-Control Header Treatment (Cache-Control ヘッダーの処理) 機能を配置することです。

値|結果
--|--
上書き|次のアクションが発生するようにします。<br/> - 配信元サーバーによって生成された `Cache-Control` ヘッダーを上書きします。 <br/>- External Max-Age (外部最長有効期間) 機能によって生成された `Cache-Control` ヘッダーを応答に追加します。
Pass Through (パススルー)|External Max-Age (外部最長有効期間) 機能によって生成された `Cache-Control` ヘッダーが応答に追加されないようにします。 <br/> 配信元サーバーで `Cache-Control` ヘッダーが生成された場合は、エンドユーザーにパススルーされます。 <br/> 配信元サーバーで `Cache-Control` ヘッダーが生成されない場合は、応答ヘッダーに `Cache-Control` ヘッダーが含まれなくなることがあります。
Add if Missing (ない場合に追加)|`Cache-Control` ヘッダーが配信元サーバーから受信されなかった場合、External Max-Age (外部最長有効期間) 機能によって生成された `Cache-Control` ヘッダーが追加されます。 このオプションは、`Cache-Control` ヘッダーがすべての資産に割り当てられるようにするのに役立ちます。
Remove| このオプションでは、`Cache-Control` ヘッダーがヘッダー応答に含まれないようにします。 `Cache-Control` ヘッダーが既に割り当てられている場合は、ヘッダー応答から取り除かれます。

**既定の動作:** 上書き。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-query-string"></a>Cache-Key Query String (キャッシュキー クエリ文字列)

**目的:** 要求に関連付けられているクエリ文字列パラメーターをキャッシュキーに含めるかどうかを決定します。

重要な情報:

- 1 つまたは複数のクエリ文字列パラメーター名を指定します。各パラメーター名は単一の空白で区切ります。
- この機能では、クエリ文字列パラメーターをキャッシュキーに含めるかどうかを決定します。 各オプションの追加情報を次の表に示します。

Type|説明
--|--
 Include (含める)|  各パラメーターをキャッシュキーに含めることを示します。 この機能で定義されているクエリ文字列パラメーターの一意の値を含む要求ごとに、一意のキャッシュキーが生成されます。
 Include All (すべて含める)  |一意のクエリ文字列を含む資産への要求ごとに、一意のキャッシュキーが作成されることを示します。 キャッシュ ヒットの割合が低くなる可能性があるため、通常、この種類の構成は推奨されません。 キャッシュ ヒットの数が少ないと、より多くの要求を処理する必要があるため、配信元サーバーの負荷が増加します。 この構成では、[クエリ文字列のキャッシュ] ページで "unique-cache" と呼ばれるキャッシュ動作が複製されます。
 Exclude (除外) | 指定したパラメーターのみがキャッシュキーから除外されることを示します。 他のすべてのクエリ文字列パラメーターはキャッシュキーに含まれます。
 Exclude All (すべて除外)  |すべてのクエリ文字列パラメーターがキャッシュキーから除外されることを示します。 この構成では、[クエリ文字列のキャッシュ] ページで既定のキャッシュ動作の "standard-cache" が複製されます。  

ルール エンジンを使用すると、クエリ文字列キャッシュの実装方法をカスタマイズできます。 たとえば、クエリ文字列キャッシュが特定の場所またはファイルの種類でのみ実行されるように指定できます。

[クエリ文字列のキャッシュ] ページでクエリ文字列キャッシュ動作の "no-cache" を複製するには、URL Query Wildcard (URL クエリ ワイルドカード) 一致条件と Bypass Cache (キャッシュのバイパス) 機能を含むルールを作成します。 URL Query Wildcard (URL クエリ ワイルドカード) 一致条件をアスタリスク (*) に設定します。

>[!IMPORTANT]
> このアカウントのいずれかのパスでトークン承認が有効になっている場合、クエリ文字列キャッシュに使用できるモードは standard-cache モードだけです。 詳細については、「[クエリ文字列による Azure CDN キャッシュ動作の制御](cdn-query-string-premium.md)」を参照してください。

#### <a name="sample-scenarios"></a>サンプル シナリオ

この機能の次の使用例は、サンプルの要求と既定の cache-key を示しています。

- **サンプル要求:** http://wpc.0001.&lt ;Domain&gt; /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01
- **既定のキャッシュキー:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Include (含める)

サンプル構成:

- **種類:** Include (含める)
- **パラメーター:** language

この種類の構成では、次のクエリ文字列パラメーター キャッシュキーが生成されます。

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Include All (すべて含める)

サンプル構成:

- **種類:** Include All (すべて含める)

この種類の構成では、次のクエリ文字列パラメーター キャッシュキーが生成されます。

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Exclude (除外)

サンプル構成:

- **種類:** Exclude (除外)
- **パラメーター:** セッションの対象のユーザーID

この種類の構成では、次のクエリ文字列パラメーター キャッシュキーが生成されます。

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Exclude All (すべて除外)

サンプル構成:

- **種類:** Exclude All (すべて除外)

この種類の構成では、次のクエリ文字列パラメーター キャッシュキーが生成されます。

    /800001/Origin/folder/asset.htm

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-rewrite"></a>Cache-Key Rewrite (キャッシュキー書き換え)

**目的:** 要求に関連付けられているキャッシュ キーを書き換えます。

キャッシュキーとは、キャッシュの目的で資産を識別する相対パスのことです。 つまり、サーバーは、キャッシュキーによって定義されたパスに従って、資産のキャッシュされたバージョンがないかどうかを確認します。

この機能は、次のオプションの両方を定義することによって構成します。

オプション|説明
--|--
Original Path (元のパス)| キャッシュキーが書き換えられる要求の種類への相対パスを定義します。 相対パスは、ベースとなる配信元のパスを選択してから正規表現パターンを定義することによって定義できます。
New Path (新しいパス)|新しいキャッシュキーの相対パスを定義します。 相対パスは、ベースとなる配信元のパスを選択してから正規表現パターンを定義することによって定義できます。 この相対パスは、[HTTP 変数](cdn-http-variables.md)を使用することで動的に構築できます。

**既定の動作:** 要求のキャッシュキーは要求 URI によって決定されます。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="comment"></a>Comment (コメント)

**目的:** ルール内にメモを追加できるようにします。

この機能の用途の 1 つは、ルールの一般的な用途や、特定の一致条件や機能がルールに追加された理由に関する追加情報を提供することです。

重要な情報:

- 最大で 150 文字を指定できます。
- 英数字のみを使用してください。
- この機能は、ルールの動作には影響しません。 将来参照したりルールのトラブルシューティングを行う際に役立つ可能性のある情報の入力場所を提供するだけです。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="complete-cache-fill"></a>Complete Cache Fill (完全キャッシュ入力)

**目的:** 要求の結果、POP で一部のキャッシュが不足したときの動作を決定します。

一部のキャッシュの不足は、資産に関するキャッシュが POP に完全にはダウンロードされなかった状態を示します。 資産が部分的にのみ POP でキャッシュされている場合、その資産に関する次の要求は配信元サーバーにもう一度転送されます。
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
通常、一部のキャッシュの不足は、ユーザーがダウンロードを中止した後や、HTTP 範囲要求を使用して単独で要求された資産について発生します。 この機能は、通常は最初から最後までダウンロードされない大きな資産 (ビデオなど) に役立ちます。 そのため、この機能は HTTP ラージ プラットフォームでは既定で有効になっています。 その他のすべてのプラットフォームでは無効になっています。

顧客の配信元サーバーの負荷を軽減し、顧客がコンテンツをダウンロードする速度を上げるため、HTTP ラージ プラットフォームでは既定の構成をそのまま使用します。

値|結果
--|--
Enabled|既定の動作を復元します。 既定の動作では、POP で配信元サーバーからの資産のバックグラウンド取得が開始されます。 その後、資産は POP のローカル キャッシュに入れられます。
Disabled|POP で資産のバックグラウンド取得が実行されないようにします。 これにより、そのリージョンからその資産が次に要求されたときに、POP は顧客の配信元サーバーにその資産を要求します。

**既定の動作:** 有効。

#### <a name="compatibility"></a>互換性

キャッシュ設定を追跡する方法が原因で、この機能を次の一致条件に関連付けることはできません。

- AS Number (AS 番号)
- Client IP Address (現在の IP アドレス)
- Cookie Parameter (Cookie パラメーター)
- Cookie Parameter Regex (Cookie パラメーターの正規表現)
- Country
- Device
- Microsoft Edge Cname (エッジ CNAME)
- Referring Domain (参照ドメイン)
- Request Header Literal (要求ヘッダーのリテラル)
- Request Header Regex (要求ヘッダーの正規表現)
- Request Header Wildcard (要求ヘッダーのワイルドカード)
- Request Method (要求メソッド)
- Request Scheme (要求スキーム)
- URL Query Literal (URL クエリ リテラル)
- URL Query Regex (URL クエリ正規表現)
- URL Query Wildcard (URL クエリ ワイルドカード)
- URL Query Parameter (URL クエリ パラメーター)

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="compress-file-types"></a>Compress File Types (圧縮ファイルの種類)

**目的:** サーバーで圧縮されるファイルのファイル形式を定義します。

ファイル形式はインターネット メディア タイプ (Content-Type など) を使用して指定できます。 インターネット メディア タイプは、サーバーが特定の資産のファイル形式を識別できるようにする、プラットフォームに依存しないメタデータです。 一般的なインターネット メディア タイプの一覧を下に示します。

インターネット メディア タイプ|説明
--|--
text/plain|プレーン テキスト ファイル (.txt)
text/html| HTML ファイル
text/css|カスケード スタイル シート (CSS)
application/x-javascript|JavaScript
application/javascript|JavaScript

重要な情報:

- 複数のインターネット メディア タイプを指定する場合は、単一の空白で区切ります。
- この機能では、サイズが 1 MB 未満の資産のみを圧縮します。 これより大きい資産はサーバーで圧縮されません。
- イメージ、ビデオ、オーディオ メディア資産などの特定の種類のコンテンツ (JPG、MP3、MP4 など) は既に圧縮されています。 これらの種類の資産をさらに圧縮しても、ファイル サイズが大幅に小さくなることはないため、これらの種類の資産では圧縮を有効にしないことをお勧めします。
- アスタリスクなどのワイルドカード文字はサポートされていません。
- ルールにこの機能を追加する前に、このルールの適用先となるプラットフォームの [圧縮] ページで、[Compression Disabled ]\(圧縮の無効化\) オプションを必ず設定してください。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="custom-log-field-1"></a>Custom Log Field 1 (カスタム ログ フィールド 1)

**目的:** 生ログ ファイルのカスタム ログ フィールドに割り当てられる形式とコンテンツを決定します。

このカスタム フィールドを使用して、ログ ファイルに保存される要求ヘッダーと応答ヘッダーの値を決定できます。

既定では、カスタム ログ フィールドは "x-ec_custom-1" という名前です。 このフィールドの名前は、[Raw Log Settings]\(生ログ設定\) ページでカスタマイズできます。

要求ヘッダーと応答ヘッダーを指定する際の形式は、次のように定義されています。

ヘッダーの種類|形式|例
-|-|-
要求ヘッダー|`%{[RequestHeader]()}[i]()` | %{Accept-Encoding}i <br/> {Referrer}i <br/> %{Authorization}i
応答ヘッダー|`%{[ResponseHeader]()}[o]()`| %{Age}o <br/> %{Content-Type}o <br/> %{Cookie}o

重要な情報:

- カスタム ログ フィールドには、ヘッダー フィールドとプレーン テキストの任意の組み合わせを含めることができます。
- このフィールドで有効な文字は、英数字 (0 ～ 9、a ～ z、A ～ Z)、ダッシュ、コロン、セミコロン、アポストロフィ、コンマ、ピリオド、アンダースコア、等号、かっこ、山かっこ、スペースです。 パーセント記号と中かっこは、ヘッダー フィールドを指定する際にのみ使用できます。
- 指定された各ヘッダー フィールドのスペルは、目的の要求/応答ヘッダー名と一致する必要があります。
- 複数のヘッダーを指定する場合は、区切り記号を使用して各ヘッダーを示します。 たとえば、各ヘッダーの省略形を使用することもできます。
    - AE: %{Accept-Encoding}i A: %{Authorization}i CT: %{Content-Type}o

**既定値:**  -

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Debug Cache Response Headers (キャッシュ応答ヘッダーのデバッグ)

**目的:** 要求された資産のキャッシュ ポリシーに関する情報を提供する [X-EC-Debug 応答ヘッダー](cdn-http-debug-headers.md)を応答に含めることができるかどうかを決定します。

デバッグ キャッシュ応答ヘッダーは、次の両方に該当する場合に応答に含められます。

- デバッグ キャッシュ応答ヘッダー機能が、指定された要求で有効になっている。
- 指定された要求で、応答に含められるデバッグ キャッシュ応答ヘッダーのセットが定義されている。

デバッグ キャッシュ応答ヘッダーは、要求に次のヘッダーと指定されたディレクティブを含めることによって要求できます。

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**例:**

X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

値|結果
-|-
Enabled|デバッグ キャッシュ応答ヘッダーに関する要求で、X-EC-Debug ヘッダーを含む応答が返されます。
Disabled|X-EC-Debug 応答ヘッダーが応答から除外されます。

**既定の動作:** 無効。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Default Internal Max-Age (既定の内部最長有効期間)

**目的:** POP と配信元サーバーの間のキャッシュ再有効化の既定の最長有効期間を決定します。 つまり、キャッシュされた資産が配信元サーバーに格納されている資産と一致するかどうかを POP が確認するまでの時間です。

重要な情報:

- このアクションは、`Cache-Control` ヘッダーまたは `Expires` ヘッダーで最長有効期間を割り当てていない配信元サーバーからの応答に対してのみ実行されます。
- このアクションは、キャッシュ可能と判断された資産については実行されません。
- このアクションは、ブラウザーと POP の間のキャッシュ再有効化には影響しません。 これらの種類の再有効化は、ブラウザーに送信された `Cache-Control` ヘッダーまたは `Expires` ヘッダーによって決定され、External Max-Age (外部最長有効期間) 機能によってカスタマイズできます。
- このアクションの結果は、POP から返される応答ヘッダーとコンテンツに対して目に見える影響はありませんが、POP から配信元サーバーに送信される再有効化トラフィックの量に影響する可能性があります。
- この機能は、次のように構成します。
    - 既定の内部最長有効期間を適用できる状態コードを選択します。
    - 整数値を指定してから、目的の時間単位 (秒、分、時間など) を選択します。 この値により、既定の内部最長有効期間が定義されます。

- 時間単位を "Off" に設定すると、`Cache-Control` ヘッダーまたは `Expires` ヘッダーで最長有効期間が割り当てられていない要求について、既定の内部最長有効期間である 7 日が割り当てられます。

**既定値:** 7 日

#### <a name="compatibility"></a>互換性

キャッシュ設定を追跡する方法が原因で、この機能を次の一致条件に関連付けることはできません。
- AS Number (AS 番号)
- Client IP Address (現在の IP アドレス)
- Cookie Parameter (Cookie パラメーター)
- Cookie Parameter Regex (Cookie パラメーターの正規表現)
- Country
- Device
- Edge Cname (エッジ CNAME)
- Referring Domain (参照ドメイン)
- Request Header Literal (要求ヘッダーのリテラル)
- Request Header Regex (要求ヘッダーの正規表現)
- Request Header Wildcard (要求ヘッダーのワイルドカード)
- Request Method (要求メソッド)
- Request Scheme (要求スキーム)
- URL Query Literal (URL クエリ リテラル)
- URL Query Regex (URL クエリ正規表現)
- URL Query Wildcard (URL クエリ ワイルドカード)
- URL Query Parameter (URL クエリ パラメーター)

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="deny-access-403"></a>Deny Access (403) (アクセス拒否 (403))

**目的**: すべての要求を拒否し、「403 Forbidden」応答を返すかどうかを決定します。

値 | 結果
------|-------
Enabled| 一致条件を満たすすべての要求を拒否し、「403 Forbidden」応答を返します。
Disabled| 既定の動作を復元します。 既定の動作では、返される応答の種類を配信元サーバーが決定できます。

**既定の動作**: Disabled

> [!TIP]
   > この機能の用途の 1 つは、コンテンツへのインライン リンクを使用している HTTP 参照元へのアクセスをブロックするために要求ヘッダーの一致条件に関連付けることです。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="expires-header-treatment"></a>Expires Header Treatment (Expires ヘッダーの処理)

**目的:** External Max-Age (外部最長有効期間) 機能がアクティブになっているとき、POP で `Expires` ヘッダーの生成を制御します。

この種類の構成を実現する最も簡単な方法は、同じステートメントに External Max-Age (外部最長有効期間) と Expires Header Treatment (Expires ヘッダーの処理) 機能を配置することです。

値|結果
--|--
上書き|次のアクションが行われるようにします。<br/>- 配信元サーバーによって生成された `Expires` ヘッダーを上書きします。<br/>- External Max-Age (外部最長有効期間) 機能によって生成された `Expires` ヘッダーを応答に追加します。
Pass Through (パススルー)|External Max-Age (外部最長有効期間) 機能によって生成された `Expires` ヘッダーが応答に追加されないようにします。 <br/> 配信元サーバーで `Expires` ヘッダーが生成された場合は、エンドユーザーにパススルーされます。 <br/>配信元サーバーで `Expires` ヘッダーが生成されない場合は、応答ヘッダーに `Expires` ヘッダーが含まれなくなることがあります。
Add if Missing (ない場合に追加)| `Expires` ヘッダーが配信元サーバーから受信されなかった場合、External Max-Age (外部最長有効期間) 機能によって生成された `Expires` ヘッダーが追加されます。 このオプションは、`Expires` ヘッダーがすべての資産に割り当てられるようにするのに役立ちます。
Remove| `Expires` ヘッダーがヘッダー応答に含まれないようにします。 `Expires` ヘッダーが既に割り当てられている場合は、ヘッダー応答から取り除かれます。

**既定の動作:** 上書き

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="external-max-age"></a>External Max-Age (外部最長有効期間)

**目的:** ブラウザーと POP の間のキャッシュ再有効化の最長有効期間を決定します。 つまり、ブラウザーが POP に対して資産の新しいバージョンを確認するまでの時間です。

この機能を有効にすると、POP から `Cache-Control: max-age` ヘッダーおよび `Expires` ヘッダーが生成され、HTTP クライアントに送信されます。 既定では、これらのヘッダーにより、配信元サーバーで作成されたヘッダーが上書きされます。 ただし、この動作は Cache-Control Header Treatment (Cache-Control ヘッダーの処理) および Expires Header Treatment (Expires ヘッダーの処理) 機能を使用して変更することができます。

重要な情報:

- このアクションは、POP と配信元サーバーの間のキャッシュ再有効化には影響しません。 これらの種類の再有効化は、配信元サーバーから受信された `Cache-Control` ヘッダーと `Expires` ヘッダーによって決定され、Default Internal Max-Age (既定の内部最長有効期間) および Force Internal Max-Age (内部最長有効期間の強制) 機能によってカスタマイズできます。
- この機能を構成するには、整数値を指定して目的の時間単位 (秒、分、時間など) を選択します。
- この機能を負の値に設定すると、POP からブラウザーへの各応答と共に、`Cache-Control: no-cache` と過去に設定された `Expires` 時間が送信されます。 応答は HTTP クライアントでキャッシュされませんが、この設定は配信元サーバーからの応答をキャッシュする POP の機能には影響しません。
- 時間単位を "Off" に設定すると、この機能は無効になります。 配信元サーバーの応答と共にキャッシュされる `Cache-Control` ヘッダーと `Expires` ヘッダーは、ブラウザーにパススルーされます。

**既定の動作:** オフ

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="follow-redirects"></a>Follow Redirects (リダイレクトのフォロー)

**目的:** 顧客の配信元サーバーから返される Location (場所) ヘッダーに定義されているホスト名に要求をリダイレクトできるかどうかを決定します。

重要な情報:

- 要求は、同じプラットフォームに対応するエッジ CNAME にのみリダイレクトできます。

値|結果
-|-
Enabled|要求をリダイレクトできます。
Disabled|要求はリダイレクトされません。

**既定の動作:** 無効。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="force-internal-max-age"></a>Force Internal Max-Age (内部最長有効期間の強制)

**目的:** POP と配信元サーバーの間のキャッシュ再有効化の最長有効期間を決定します。 つまり、キャッシュされた資産が配信元サーバーに格納されている資産と一致するかどうかを POP が確認できるようになるまでの時間です。

重要な情報:

- この機能では、配信元サーバーから生成された `Cache-Control` ヘッダーまたは `Expires` ヘッダーで定義された最長有効期間がオーバーライドされます。
- この機能は、ブラウザーと POP の間のキャッシュ再有効化には影響しません。 これらの種類の再有効化は、ブラウザーに送信された `Cache-Control` ヘッダーまたは `Expires` ヘッダーによって決定されます。
- この機能では、POP から要求元に配信される応答に対して目に見える影響はありません。 ただし、POP から配信元サーバーに送信される再有効化トラフィックの量に影響する可能性があります。
- この機能は、次のように構成します。
    - 内部最長有効期間が適用される状態コードを選択します。
    - 整数値を指定してから、目的の時間単位 (秒、分、時間など) を選択します。 この値により、要求の最長有効期間が定義されます。

- 時間単位を "Off" に設定すると、この機能は無効になります。 内部最長有効期間は、要求された資産に割り当てられません。 元のヘッダーにキャッシュに関する指示が含まれていない場合、資産は Default Internal Max-Age (既定の内部最長有効期間) 機能でアクティブな設定に従ってキャッシュされます。

**既定の動作:** オフ

#### <a name="compatibility"></a>互換性

キャッシュ設定を追跡する方法が原因で、この機能を次の一致条件に関連付けることはできません。
- AS Number (AS 番号)
- Client IP Address (現在の IP アドレス)
- Cookie Parameter (Cookie パラメーター)
- Cookie Parameter Regex (Cookie パラメーターの正規表現)
- Country
- Device
- Edge Cname (エッジ CNAME)
- Referring Domain (参照ドメイン)
- Request Header Literal (要求ヘッダーのリテラル)
- Request Header Regex (要求ヘッダーの正規表現)
- Request Header Wildcard (要求ヘッダーのワイルドカード)
- Request Method (要求メソッド)
- Request Scheme (要求スキーム)
- URL Query Literal (URL クエリ リテラル)
- URL Query Regex (URL クエリ正規表現)
- URL Query Wildcard (URL クエリ ワイルドカード)
- URL Query Parameter (URL クエリ パラメーター)

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="h264-support-http-progressive-download"></a>H.264 Support (HTTP Progressive Download) (H.264 サポート (HTTP プログレッシブ ダウンロード))

**目的:** コンテンツのストリーム配信に使用される H.264 ファイル形式の種類を決定します。

重要な情報:

- [File Extensions (ファイル拡張子)] オプションで許可される H.264 ファイル名拡張子の空白で区切られたセットを定義します。 [File Extensions (ファイル拡張子)] オプションにより、既定の動作がオーバーライドされます。 このオプションを設定する際は、MP4 と F4V のファイル名拡張子を含めることによってこれらのサポートを維持してください。
- 各ファイル名拡張子を指定する際はピリオドを含めます ( _.mp4_、 _.f4v_ など)。

**既定の動作:** HTTP プログレッシブ ダウンロードでは MP4 と F4V メディアが既定でサポートされます。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="honor-no-cache-request"></a>Honor No-Cache Request (キャッシュなし要求の許可)

**目的:** HTTP クライアントのキャッシュなし要求を配信元サーバーに転送するかどうかを決定します。

キャッシュなし要求は、HTTP クライアントが HTTP 要求で `Cache-Control: no-cache` ヘッダーや `Pragma: no-cache` ヘッダーを送信するときに発生します。

値|結果
--|--
Enabled|HTTP クライアントのキャッシュなし要求を配信元サーバーに転送することが可能となり、配信元サーバーは応答ヘッダーと本文を POP 経由で HTTP クライアントに戻します。
Disabled|既定の動作を復元します。 既定の動作では、キャッシュなし要求が配信元サーバーに転送されることが防止されます。

すべての実稼働トラフィックについて、この機能を既定の無効状態のままにしておくことをお勧めします。 そうしないと、配信元サーバーは Web ページの更新時に大量のキャッシュなし要求を誤ってトリガーする可能性があるエンド ユーザーや、すべてのビデオ要求で no-cache ヘッダーを送信するように設計された多くの一般的なメディア プレーヤーからシールドされません。 ただし、この機能は、配信元サーバーから最新のコンテンツをオンデマンドでプルできるようにするために、特定の非運用のステージングまたはテスト用のディレクトリに適用するのに役立つ可能性があります。

この機能によって配信元サーバーに転送可能な要求についてレポートされるキャッシュの状態は、`TCP_Client_Refresh_Miss` です。 コア レポート モジュールで使用できるキャッシュの状態レポートでは、キャッシュの状態ごとに統計情報が提供されます。 このレポートにより、この機能が原因で配信元サーバーに転送されている要求の数と割合を追跡できます。

**既定の動作:** 無効。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-origin-no-cache"></a>Ignore Origin No-Cache (配信元のキャッシュなしを無視する)

**目的:** 配信元サーバーから提供された次のディレクティブを CDN が無視するかどうかを決定します。

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

重要な情報:

- この機能を構成するには、上記のディレクティブが無視される状態コードを空白で区切った一覧を定義します。
- この機能について有効な状態コードのセットは、200、203、300、301、302、305、307、400、401、402、403、404、405、406、407、408、409、410、411、412、413、414、415、416、417、500、501、502、503、504、および 505 です。
- この機能を無効にするには、空白の値を設定します。

**既定の動作:** 既定の動作では、上記のディレクティブを許可します。

#### <a name="compatibility"></a>互換性

キャッシュ設定を追跡する方法が原因で、この機能を次の一致条件に関連付けることはできません。
- AS Number (AS 番号)
- Client IP Address (現在の IP アドレス)
- Cookie Parameter (Cookie パラメーター)
- Cookie Parameter Regex (Cookie パラメーターの正規表現)
- Country
- Device
- Edge Cname (エッジ CNAME)
- Referring Domain (参照ドメイン)
- Request Header Literal (要求ヘッダーのリテラル)
- Request Header Regex (要求ヘッダーの正規表現)
- Request Header Wildcard (要求ヘッダーのワイルドカード)
- Request Method (要求メソッド)
- Request Scheme (要求スキーム)
- URL Query Literal (URL クエリ リテラル)
- URL Query Regex (URL クエリ正規表現)
- URL Query Wildcard (URL クエリ ワイルドカード)
- URL Query Parameter (URL クエリ パラメーター)

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-unsatisfiable-ranges"></a>Ignore Unsatisfiable Ranges (満たされない範囲を無視する)

**目的:** 要求で「416 Requested Range Not Satisfiable」ステータス コードが生成されるとき、クライアントに返される応答を決定します。

既定では、この状態コードは、POP で指定されたバイト範囲要求を満たすことができず、If-Range 要求ヘッダー フィールドが指定されていないときに返されます。

値|結果
-|-
Enabled|POP が無効なバイト範囲の要求に対して「416 Requested Range Not Satisfiable」状態コードで応答しないようにします。 代わりに、サーバーは要求された資産を配信し、クライアントに「200 OK」を返します。
Disabled|既定の動作を復元します。 既定の動作では、「416 Requested Range Not Satisfiable」状態コードを許可します。

**既定の動作:** 無効。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="internal-max-stale"></a>Internal Max-Stale (内部 Max-Stale)

**目的:** POP が配信元サーバーでキャッシュされたアセットを再有効化できないとき、通常の有効期間を過ぎてから、キャッシュされたアセットを POP から提供できる期間を制御します。

通常は、資産の最長有効期間が経過すると、POP は再有効化要求を配信元サーバーに送信します。 配信元サーバーは、「304 Not Modified」で応答して、キャッシュされた資産に関する最新のリースを POP を提供するか、「200 OK」で応答して、キャッシュされた資産の更新されたバージョンを POP に提供します。

POP がこのような再有効化の試行時に配信元サーバーとの接続を確立できない場合は、この Internal Max-Stale (内部 Max-Stale) 機能により、POP が古くなった資産を提供し続けるかどうか、およびその期間が制御されます。

この期間は、失敗した再有効化の発生したときではなく、資産の最長有効期間が経過したときに開始されることに注意してください。 したがって、再有効化が成功しないうちに資産を提供できる最長期間は、最長有効期間と max-stale の組み合わせによって指定された時間です。 たとえば、30 分の最長有効期間と 15 分の max-stale を指定して資産が 9:00 にキャッシュされた場合、再有効化が 9:44 に失敗すると、エンドユーザーはキャッシュされた古い資産を受信しますが、最有効化が 9:46 に失敗すると、エンドユーザーは「504 Gateway Timeout」を受信します。

この機能用に構成されたすべての値は、配信元サーバーから受け取った `Cache-Control: must-revalidate` ヘッダーまたは `Cache-Control: proxy-revalidate` ヘッダーによって置き換えられます。 資産が最初にキャッシュされた配信元サーバーからこれらのヘッダーのいずれかが受信された場合、POP はキャッシュされた古い資産を提供しません。 その場合、資産の最長有効期間が経過したときに POP と配信元の間で再有効化を実行できないと、POP は「504 Gateway Timeout」エラーを返します。

重要な情報:

- この機能は、次のように構成します。
    - max-stale が適用される状態コードを選択します。
    - 整数値を指定してから、目的の時間単位 (秒、分、時間など) を選択します。 この値により、適用される内部 max-stale が定義されます。

- 時間単位を "Off" に設定すると、この機能は無効になります。 キャッシュされた資産が通常の有効期限を超えて提供されることはありません。

**既定の動作:** 2 分

#### <a name="compatibility"></a>互換性

キャッシュ設定を追跡する方法が原因で、この機能を次の一致条件に関連付けることはできません。
- AS Number (AS 番号)
- Client IP Address (現在の IP アドレス)
- Cookie Parameter (Cookie パラメーター)
- Cookie Parameter Regex (Cookie パラメーターの正規表現)
- Country
- Device
- Edge Cname (エッジ CNAME)
- Referring Domain (参照ドメイン)
- Request Header Literal (要求ヘッダーのリテラル)
- Request Header Regex (要求ヘッダーの正規表現)
- Request Header Wildcard (要求ヘッダーのワイルドカード)
- Request Method (要求メソッド)
- Request Scheme (要求スキーム)
- URL Query Literal (URL クエリ リテラル)
- URL Query Regex (URL クエリ正規表現)
- URL Query Wildcard (URL クエリ ワイルドカード)
- URL Query Parameter (URL クエリ パラメーター)

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="log-query-string"></a>Log Query String (ログ クエリ文字列)

**目的:** アクセス ログに URL と共にクエリ文字列を保存するかどうかを決定します。

値|結果
-|-
Enabled|アクセス ログで URL を記録するときにクエリ文字列を保存できるようにします。 URL にクエリ文字列が含まれていない場合、このオプションには効果がありません。
Disabled|既定の動作を復元します。 既定の動作では、アクセス ログで URL を記録する際にクエリ文字列を無視します。

**既定の動作:** 無効。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="maximum-keep-alive-requests"></a>Maximum Keep-Alive Requests (最大キープアライブ要求)

**目的:** キープアライブ接続の最大要求数を定義します。この数に達すると終了となります。

要求の最大数を低い値に設定することは、パフォーマンスの低下につながる場合があるため、お勧めしません。

重要な情報:

- この値は整数として指定します。
- 指定する値にコンマやピリオドは含めないでください。

**既定値:** 10,000 件の要求

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-request-header"></a>Modify Client Request Header (クライアント要求ヘッダーの修正)

**目的:** 各要求には、その要求について説明する要求ヘッダーのセットが含まれています。 この機能により、次のいずれかを実行できます。

- 要求ヘッダーに割り当てられた値を追加または上書きします。 指定された要求ヘッダーが存在しない場合、この機能はその要求ヘッダーを要求に追加します。
- 要求から要求ヘッダーを削除します。

配信元サーバーに転送される要求には、この機能によって行われた変更が反映されます。

要求ヘッダーには、次のアクションのいずれかを実行できます。

オプション|説明|例
-|-|-
Append|指定された値が、既存の要求ヘッダー値の末尾に追加されます。|**要求ヘッダー値 (クライアント):**<br/>Value1<br/>**要求ヘッダー値 (ルール エンジン):**<br/>Value2 <br/>**新しい要求ヘッダー値:** <br/>Value1Value2
上書き|要求ヘッダー値が、指定された値に設定されます。|**要求ヘッダー値 (クライアント):**<br/>Value1<br/>**要求ヘッダー値 (ルール エンジン):**<br/>Value2<br/>**新しい要求ヘッダー値:**<br/> Value2 <br/>
削除|指定された要求ヘッダーを削除します。|**要求ヘッダー値 (クライアント):**<br/>Value1<br/>**クライアント要求ヘッダー構成の修正:**<br/>問題の要求ヘッダーを削除します。<br/>**結果:**<br/>指定された要求ヘッダーは、配信元サーバーに転送されません。

重要な情報:

- [名前] オプションで指定されている値が目的の要求ヘッダーと正確に一致することを確認してください。
- ヘッダーを識別する際、大文字小文字は区別されません。 たとえば、次のバリエーションのいずれを使用しても、`Cache-Control` ヘッダー名を識別できます。
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- ヘッダー名を指定する際は、英数字、ハイフン、またはアンダー スコアのみを使用してください。
- ヘッダーを削除すると、そのヘッダーが POP によって配信元サーバーに転送されなくなります。
- 次のヘッダーは予約済みであり、この機能で変更することはできません。
    - forwarded
    - host
    - via
    - 警告
    - x-forwarded-for
    - "x-ec" で始まるすべてのヘッダー名は予約済みです。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-response-header"></a>Modify Client Response Header (クライアント応答ヘッダーの修正)

各応答には、その応答について説明する応答ヘッダーのセットが含まれています。 この機能により、次のいずれかを実行できます。

- 応答ヘッダーに割り当てられた値を追加または上書きします。 指定された応答ヘッダーが存在しない場合、この機能はその要求ヘッダーを応答に追加します。
- 応答から応答ヘッダーを削除します。

既定では、応答ヘッダー値は配信元サーバーと POP によって定義されます。

応答ヘッダーには、次のアクションのいずれかを実行できます。

オプション|説明|例
-|-|-
Append|指定された値が、既存の応答ヘッダー値の末尾に追加されます。|**応答ヘッダー値 (クライアント):**<br />Value1<br/>**応答ヘッダー値 (ルール エンジン):**<br/>Value2<br/>**新しい応答ヘッダー値:**<br/>Value1Value2
上書き|応答ヘッダー値が、指定された値に設定されます。|**応答ヘッダー値 (クライアント):**<br/>Value1<br/>**応答ヘッダー値 (ルール エンジン):**<br/>Value2 <br/>**新しい応答ヘッダー値:**<br/>Value2 <br/>
削除|指定された応答ヘッダーを削除します。|**応答ヘッダー値 (クライアント):**<br/>Value1<br/>**クライアント応答ヘッダー構成の修正:**<br/>問題の応答ヘッダーを削除します。<br/>**結果:**<br/>指定された応答ヘッダーは、要求元に転送されません。

重要な情報:

- [名前] オプションで指定されている値が目的の応答ヘッダーと正確に一致することを確認してください。
- ヘッダーを識別する際、大文字小文字は区別されません。 たとえば、次のバリエーションのいずれを使用しても、`Cache-Control` ヘッダー名を識別できます。
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- ヘッダーを削除すると、そのヘッダーが要求元に転送されなくなります。
- 次のヘッダーは予約済みであり、この機能で変更することはできません。
    - accept-encoding
    - age
    - connection
    - content-encoding
    - content-length
    - content-range
    - date
    - サーバー
    - trailer
    - transfer-encoding
    - upgrade
    - vary
    - via
    - warning
    - "x-ec" で始まるすべてのヘッダー名は予約済みです。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="partial-cache-sharing"></a>Partial Cache Sharing (部分キャッシュ共有)

**目的:** 部分的にキャッシュされたコンテンツを要求で生成できるかどうかを決定します。

その後は、要求されたコンテンツが完全にキャッシュされるまで、この部分的なキャッシュを使用してそのコンテンツに関する新しい要求を満たすことができます。

値|結果
-|-
Enabled|部分的にキャッシュされたコンテンツを要求で生成できます。
Disabled|要求されたコンテンツの完全にキャッシュされたバージョンのみを要求で生成できます。

**既定の動作:** 無効。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="prevalidate-cached-content"></a>Prevalidate Cached Content (キャッシュされたコンテンツの事前検証)

**目的:** TTL が期限切れになる前に、キャッシュされたコンテンツに早期検証の資格が与えられるかどうかを決定します。

要求されたコンテンツの TTL の有効期限が切れる前に早期再有効化の資格が与えられるまでの時間を定義します。

重要な情報:

- 時間単位として "Off" を選択すると、キャッシュされたコンテンツの TTL が期限切れになった後で再有効化が実行されます。 時間を指定する必要はなく、指定しても無視されます。

**既定の動作:** オフ。 再有効化は、キャッシュされたコンテンツの TTL が期限切れになった後にのみ実行されます。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="proxy-special-headers"></a>Proxy Special Headers (プロキシーの特殊ヘッダー)

**目的:** POP から配信元サーバーに転送される一連の [Verizon 固有 HTTP 要求ヘッダー](cdn-verizon-http-headers.md)を定義します。

重要な情報:

- この機能で定義される各 CDN 固有要求ヘッダーは、配信元サーバーに転送されます。 除外されたヘッダーは転送されません。
- CDN 固有要求ヘッダーが転送されないようにするには、ヘッダー リスト フィールドのスペースで区切られたリストから削除します。

既定のリストには、次の HTTP ヘッダーが含まれています。
- Via
- X-Forwarded-For
- X-Forwarded-Proto
- X-Host
- X-Midgress
- X-Gateway-List
- X-EC-Name
- Host

**既定の動作:** すべての CDN 固有要求ヘッダーが配信元サーバーに転送されます。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="refresh-zero-byte-cache-files"></a>Refresh Zero-Byte Cache Files (ゼロバイト キャッシュ ファイルの更新)

**目的:** 0 バイトのキャッシュ資産に対する HTTP クライアントの要求を POP が処理する方法を決定します。

有効な値は次のとおりです。

値|結果
--|--
Enabled|POP が資産を配信元サーバーから再取得します。
Disabled|既定の動作を復元します。 既定の動作では、要求時に有効なキャッシュされた資産が提供されます。

この機能は、正しいキャッシュおよびコンテンツ配信のために必要というわけではありませんが、回避策として役に立つ場合があります。 たとえば、配信元サーバー上の動的コンテンツ ジェネレーターにより、誤って 0 バイトの応答が POP に送信される可能性があります。 通常、これらの種類の応答は、POP によってキャッシュされます。 当該のコンテンツで 0 バイトの応答が有効でないことがわかっている場合は、この機能を使用してそれらの種類の資産がクライアントに提供されることを防止できます。

**既定の動作:** 無効。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-cacheable-status-codes"></a>Set Cacheable Status Codes (キャッシュ可能ステータス コードの設定)

**目的:** 結果としてコンテンツがキャッシュされるステータス コードのセットを定義します。

既定では、キャッシュは「200 OK」応答についてのみ有効です。

目的の状態コードを空白で区切ったセットを定義してください。

重要な情報:

- Ignore Origin No-Cache (配信元のキャッシュなしを無視する) 機能を有効にしてください。 この機能が有効でないと、200 OK 以外の応答はキャッシュされない可能性があります。
- この機能について有効な状態コードのセットは、203、300、301、302、305、307、400、401、402、403、404、405、406、407、408、409、410、411、412、413、414、415、416、417、500、501、502、503、504、および 505 です。
- この機能を使用して、「200 OK」状態コードを生成する応答についてキャッシュを無効にすることはできません。

**既定の動作:** キャッシュは「200 OK」状態コードを生成する応答についてのみ有効です。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-client-ip-custom-header"></a>Set Client IP Custom Header (クライアント IP カスタム ヘッダーの設定)

**目的:** IP アドレスによって要求元クライアントを識別するカスタム ヘッダーを要求に追加します。

[ヘッダー名] オプションにより、クライアントの IP アドレスが格納されるカスタム要求ヘッダーの名前が定義されます。

この機能により、顧客の配信元サーバーはカスタム要求ヘッダーを通じてクライアントの IP アドレスを知ることができます。 要求がキャッシュから提供された場合、配信元サーバーにクライアントの IP アドレスは通知されません。 したがって、キャッシュされない資産でこの機能を使用することをお勧めします。

指定されたヘッダーの名前が次のいずれの名前とも一致しないことを確認してください。

- 標準的な要求ヘッダー名。 標準的なヘッダー名の一覧は [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) に記載されています。
- 予約済みのヘッダー名:
    - forwarded-for
    - host
    - vary
    - via
    - 警告
    - x-forwarded-for
    - "x-ec" で始まるすべてのヘッダー名は予約済みです。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-content-delivery-on-error"></a>Stale Content Delivery on Error (エラー時の古いコンテンツ配信)

**目的:** キャッシュ再検証中にエラーが発生したとき、または顧客の配信元サーバーから要求されたコンテンツを取得するとき、期限切れになったキャッシュ済みコンテンツを配信するかどうかを決定します。

値|結果
-|-
Enabled|配信元サーバーへの接続中にエラーが発生したときに、古いコンテンツが要求元に提供されます。
Disabled|配信元サーバーのエラーが要求元に転送されます。

**既定の動作:** Disabled

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-while-revalidate"></a>Stale While Revalidate (古いキャッシュを返し、同時に再検証)

**目的:** 再有効化の実行中に古いコンテンツを要求元に提供することを POP に許可することにより、パフォーマンスを向上させます。

重要な情報:

- この機能の動作は、選択した時間単位によって異なります。
    - **時間単位:** 古いコンテンツの配信を許可する期間を指定し、時間単位 (秒、分、時間など) を選択します。 この種類の設定により、CDN は次の式に従って、有効化を要求する前にコンテンツを提供できる期間を拡張できます。**TTL** + **Stale While Revalidate Time (再検証中に古いコンテンツを提供する期間)**
    - **Off:** "Off" を選択すると、古いコンテンツに関する要求を実行する前に再有効化が要求されます。
        - 期間は適用されず、無視されるため、指定しないでください。

**既定の動作:** オフ。 要求されたコンテンツを提供する前に再有効化が実行される必要があります。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth"></a>Token Auth (トークン認証)

**目的:** トークン基準の認証を要求に適用するかどうかを決定します。

トークン基準の認証が有効になっている場合は、暗号化されたトークンを提供し、そのトークンで指定されている要件に準拠している要求だけが受け入れられます。

トークン値の暗号化と暗号化解除に使用される暗号化キーは、[Token Auth (トークン認証)] ページの [主キー] および [Backup Key (バックアップ キー)] オプションによって決まります。 暗号化キーはプラットフォームに固有であることに注意してください。

**既定の動作:** 無効。

この機能は、URL 書き換え機能を除くほとんどの機能より優先されます。

値 | 結果
------|---------
Enabled | 要求されたコンテンツをトークン基準の認証によって保護します。 有効なトークンを提供し、その要件を満たしているクライアントからの要求のみが受け入れられます。 FTP トランザクションはトークン基準の認証から除外されます。
Disabled| 既定の動作を復元します。 既定の動作では、要求が保護されるかどうかはトークン基準の認証構成によって決まります。

#### <a name="compatibility"></a>互換性

トークン認証は、[常に] 一致条件と一緒に使用しないでください。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-denial-code"></a>Token Auth Denial Code (トークン認証拒否コード)

**目的:** トークン基準の認証により要求が拒否されるとき、ユーザーに返す応答の種類を決定します。

使用可能な応答コードを次の表に示します。

応答コード|応答名|説明
-------------|-------------|--------
301|Moved Permanently|この状態コードでは、Location ヘッダーで指定された URL に未承認のユーザーがリダイレクトされます。
302|Found|この状態コードでは、Location ヘッダーで指定された URL に未承認のユーザーがリダイレクトされます。 この状態コードは、リダイレクトを実行する業界標準の方法です。
307|Temporary Redirect|この状態コードでは、Location ヘッダーで指定された URL に未承認のユーザーがリダイレクトされます。
401|権限がありません|この状態コードを WWW-Authenticate 応答ヘッダーと組み合わせることにより、認証を求めるプロンプトをユーザーに表示することができます。
403|許可されていません|このメッセージは、未認証のユーザーが保護されたコンテンツにアクセスしようとしたときに表示される標準の 403 Forbidden 状態メッセージです。
404|File Not Found|この状態コードは、HTTP クライアントがサーバーと通信できたが要求されたコンテンツは見つからなかったことを示します。

#### <a name="compatibility"></a>互換性

トークン認証拒否コードは、[常に] 一致条件と一緒に使用しないでください。 代わりに、**管理**ポータルの **[トークン認証]** ページの **[Custom Denial Handling]\(拒否のカスタム処理\)** セクションを使用します。 詳細については、「[トークン認証による Azure CDN 資産の保護](cdn-token-auth.md)」をご覧ください。

#### <a name="url-redirection"></a>URL Redirection (URL リダイレクト)

この機能では、3xx 状態コードを返すように構成された場合に、ユーザー定義 URL への URL リダイレクトがサポートされます。 このユーザー定義 URL は、次の手順を実行することによって指定できます。

1. Token Auth Denial Code (トークン認証拒否コード) 機能用の 3xx 応答コードを選択します。
2. [Optional Header Name (省略可能なヘッダー名)] オプションから "Location" を選択します。
3. [Optional Header Value (省略可能なヘッダー値)] オプションを目的の URL に設定します。

3xx 状態コード用の URL が定義されていない場合は、3xx 状態コード用の標準の応答ページがユーザーに返されます。

URL リダイレクトは 3xx 応答コードにのみ適用されます。

[Optional Header Value (省略可能なヘッダー値)] オプションでは、英数字、引用符、スペースがサポートされます。

#### <a name="authentication"></a>Authentication

この機能により、トークン基準の認証によって保護されたコンテンツに関する未承認の要求に応答するときに WWW-Authenticate ヘッダーを含めることが可能になります。 構成で WWW-Authenticate ヘッダーが "basic" に設定されている場合、未承認のユーザーにはアカウントの資格情報を入力するように求められます。

上記の構成は、次の手順を実行することによって実現できます。

1. Token Auth Denial Code (トークン認証拒否コード) 機能用の応答コードとして "401" を選択します。
2. [Optional Header Name (省略可能なヘッダー名)] オプションから "WWW-Authenticate" を選択します。
3. [Optional Header Value (省略可能なヘッダー値)] オプションを "basic" に設定します。

WWW-Authenticate ヘッダーは、401 応答コードでのみ適用されます。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-ignore-url-case"></a>Token Auth Ignore URL Case (トークン認証の URL 大文字/小文字の無視)

**目的:** トークン ベースの認証で URL を比較するとき、大文字と小文字を区別するかどうかを決定します。

この機能によって影響を受けるパラメーターは、次のとおりです。

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

有効な値は次のとおりです。

値|結果
---|----
Enabled|POP で、トークン ベースの認証パラメーターの URL が比較されるときに、大文字小文字が区別されなくなります。
Disabled|既定の動作を復元します。 既定の動作では、トークン認証で URL が比較されるときに大文字小文字が区別されます。

**既定の動作:** 無効。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-parameter"></a>Token Auth Parameter (トークン認証パラメーター)

**目的:** トークン基準の認証のクエリ文字列パラメーターの名前を変更するかどうかを決定します。

重要な情報:

- [Value (値)] オプションにより、トークンを指定するために使用できるクエリ文字列パラメーター名が定義されます。
- [Value (値)] オプションを "ec_token" に設定することはできません。
- [Value (値)] オプションで定義する名前には、有効な URL 文字のみを含めてください。

値|結果
----|----
Enabled|[Value (値)] オプションにより、トークンを定義するのに使用する必要のあるクエリ文字列パラメーター名が定義されます。
Disabled|要求 URL でトークンを未定義のクエリ文字列パラメーターとして指定することができます。

**既定の動作:** 無効。 要求 URL でトークンを未定義のクエリ文字列パラメーターとして指定することができます。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-redirect"></a>URL Redirect (URL リダイレクト)

**目的:** Location (場所) ヘッダー経由で要求をリダイレクトします。

この機能の構成では、次のオプションを設定する必要があります。

オプション|説明
-|-
コード|要求元に返される応答コードを選択します。
Source & Pattern (ソースとパターン)| これらの設定により、リダイレクトされる要求の種類を識別する要求 URI のパターンが定義されます。 URL が次の条件の両方を満たす要求だけがリダイレクトされます。 <br/> <br/> **ソース (またはコンテンツ アクセス ポイント):** 配信元サーバーを識別する相対パスを選択します。 このパスは、 _/XXXX/_ セクションとエンドポイント名です。 <br/><br/> **ソース (パターン):** 相対パスによって要求を識別するパターンを定義する必要があります。 この正規表現パターンでは、前に選択されていたコンテンツ アクセス ポイント (上記参照) の直後に始まるパスを定義する必要があります。 <br/> - 前に定義した要求 URI 条件 (つまり、[Source & Pattern]\(ソースとパターン\)) がこの機能について定義されている一致条件と競合しないことを確認してください。 <br/> - パターンを指定します。パターンとして空の値を指定すると、すべての文字列と一致します。
宛先| 上の要求がリダイレクトされる URL を定義します。 <br/><br/> この URL は、次のものを使用して動的に作成します。 <br/> - 正規表現パターン <br/>- [HTTP 変数](cdn-http-variables.md) <br/><br/> $_n_ を使用して、ソース パターンでキャプチャされた値を変換先パターンに置き換えます (_n_ はキャプチャされた順序で値を識別します)。 たとえば、$1 は、ソース パターンでキャプチャされた最初の値を表し、$2 は 2 番目の値を表します。 <br/>

絶対 URL を使用することを強くお勧めします。 相対 URL を使用すると、CDN の URL が無効なパスにリダイレクトされることがあります。

**サンプル シナリオ**

この例では、ベース CDN URL http:\//marketing.azureedge.net/brochures に解決されるエッジ CNAME URL をリダイレクトする方法について説明します

対象の要求は、ベース エッジ CNAME URL http:\//cdn.mydomain.com/resources にリダイレクトされます

この URL のリダイレクトは、次の構成によって達成できます。![URL Redirect (URL リダイレクト)](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**重要なポイント:**

- URL Redirect (URL リダイレクト) 機能では、リダイレクトされる要求 URL が定義されます。 このため、追加の一致条件は必要ありません。 一致条件を [Always (常に)] として定義しましたが、顧客の配信元 "marketing" にある "brochures" フォルダーを指定する要求だけがリダイレクトされます。
- 一致するすべての要求は、[Destination (送信先)] オプションで定義されているエッジ CNAME URL にリダイレクトされます。
    - サンプル シナリオ 1:
        - サンプルの要求 (CDN URL): http:\//marketing.azureedge.net/brochures/widgets.pdf
        - 要求 URL (リダイレクト後): http:\//cdn.mydomain.com/resources/widgets.pdf  
    - サンプル シナリオ 2:
        - サンプルの要求 (エッジ CNAME URL): http:\//marketing.mydomain.com/brochures/widgets.pdf
        - 要求 URL (リダイレクト後): http:\//cdn.mydomain.com/resources/widgets.pdf
    - サンプル シナリオ 3:
        - サンプルの要求 (エッジ CNAME URL): http:\//brochures.mydomain.com/campaignA/final/productC.ppt
        - 要求 URL (リダイレクト後): http:\//cdn.mydomain.com/resources/campaignA/final/productC.ppt 
- 要求スキーム (%{scheme}) 変数は送信先オプションで利用されるため、リダイレクト後に要求のスキームが変更されなくなります。
- 要求からキャプチャされた URL セグメントは、"$1" を使用して新しい URL に追加されます。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-rewrite"></a>URL Rewrite (URL 書き換え)

**目的:** 要求 URL を書き換えます。

重要な情報:

- この機能の構成では、次のオプションを設定する必要があります。

オプション|説明
-|-
 Source & Pattern (ソースとパターン) | これらの設定により、書き換えられる要求の種類を識別する要求 URI のパターンが定義されます。 URL が次の条件の両方を満たす要求だけが書き換えられます。 <br/><br/>  - **ソース (またはコンテンツ アクセス ポイント):** 配信元サーバーを識別する相対パスを選択します。 このパスは、 _/XXXX/_ セクションとエンドポイント名です。 <br/><br/> - **ソース (パターン):** 相対パスによって要求を識別するパターンを定義する必要があります。 この正規表現パターンでは、前に選択されていたコンテンツ アクセス ポイント (上記参照) の直後に始まるパスを定義する必要があります。 <br/> 前に定義した要求 URI 条件 (つまり、[Source & Pattern]\(ソースとパターン\)) がこの機能について定義されている一致条件と競合しないことを確認してください。 パターンを指定します。パターンとして空の値を指定すると、すべての文字列と一致します。
 宛先  |上の要求の書き換えに使用する相対 URL を定義します。 <br/>    1.配信元サーバーを識別するコンテンツ アクセス ポイントを選択します。 <br/>    2.次のものを使用して相対パスを定義します。 <br/>        - 正規表現パターン <br/>        - [HTTP 変数](cdn-http-variables.md) <br/> <br/> $_n_ を使用して、ソース パターンでキャプチャされた値を変換先パターンに置き換えます (_n_ はキャプチャされた順序で値を識別します)。 たとえば、$1 は、ソース パターンでキャプチャされた最初の値を表し、$2 は 2 番目の値を表します。

 この機能により、POP は従来のリダイレクトを実行せずに URL を書き換えることができます。 つまり、要求元は、書き換え後の URL を要求した場合と同じ応答コードを受信します。

**ンプル シナリオ 1**

この例では、ベース CDN URL http:\//marketing.azureedge.net/brochures/ に解決されるエッジ CNAME URL をリダイレクトする方法について説明します。

対象の要求は、ベース エッジ CNAME URL: http:\//MyOrigin.azureedge.net/resources/ にリダイレクトされます。

この URL のリダイレクトは、次の構成によって達成できます。![URL Redirect (URL リダイレクト)](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**サンプル シナリオ 2**

この例では、正規表現を使用して、エッジ CNAME URL を大文字から小文字にリダイレクトする方法について説明します。

この URL のリダイレクトは、次の構成によって達成できます。![URL Redirect (URL リダイレクト)](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)

**重要なポイント:**

- URL Rewrite (URL 書き換え) 機能では、書き換えられる要求 URL が定義されます。 このため、追加の一致条件は必要ありません。 一致条件を [Always (常に)] として定義しましたが、顧客の配信元 "marketing" にある "brochures" フォルダーを指定する要求だけが書き換えられます。

- 要求からキャプチャされた URL セグメントは、"$1" を使用して新しい URL に追加されます。

#### <a name="compatibility"></a>互換性

この機能には、要求に適用する前に満たす必要がある一致条件が含まれます。 競合する一致条件が設定されることのないよう、この機能は次の一致条件と互換性がありません。

- AS Number (AS 番号)
- CDN Origin (CDN 配信元)
- Client IP Address (現在の IP アドレス)
- Customer Origin (顧客配信元)
- Request Scheme (要求スキーム)
- URL Path Directory (URL パス ディレクトリ)
- URL Path Extension (URL パス拡張子)
- URL Path Filename (URL パス ファイル名)
- URL Path Literal (URL パス リテラル)
- URL Path Regex (URL パス正規表現)
- URL Path Wildcard (URL パス ワイルドカード)
- URL Query Literal (URL クエリ リテラル)
- URL Query Parameter (URL クエリ パラメーター)
- URL Query Regex (URL クエリ正規表現)
- URL Query Wildcard (URL クエリ ワイルドカード)

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="user-variable"></a>ユーザー変数

**目的:** 内部使用専用です。

[先頭に戻る](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

## <a name="next-steps"></a>次の手順

- [ルール エンジンのリファレンス](cdn-verizon-premium-rules-engine-reference.md)
- [ルール エンジンの条件式](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [ルール エンジンの一致条件](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [ルール エンジンを使用して HTTP 動作をオーバーライドする](cdn-verizon-premium-rules-engine.md)
- [Azure CDN の概要](cdn-overview.md)