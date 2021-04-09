---
title: Azure CDN from Verizon Premium ルール エンジンの機能
description: Azure CDN from Verizon Premium ルール エンジン機能に関するリファレンス ドキュメント。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 06/02/2020
ms.author: allensu
ms.openlocfilehash: 0ea4f167b992ccfbc4156ac06c8f636d2ef4a355
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020417"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Azure CDN from Verizon Premium ルール エンジンの機能

このトピックでは、Azure Content Delivery Network (CDN) [ルール エンジン](cdn-verizon-premium-rules-engine.md)で利用できる機能の詳しい説明を一覧表示します。

ルールの 3 番目の部分は、機能です。 機能では、一連の一致条件によって特定される要求の種類に適用されるアクションの種類を定義します。

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a><a name="top"></a>Azure CDN from Verizon Premium ルール エンジンの機能のリファレンス

使用できる機能の種類は次のとおりです。

* [Access (アクセス)](#access)
* [キャッシュ](#caching)
* [解説](#comment)
* [ヘッダー](#headers)
* [ログ](#logs)
* [最適化](#optimize)
* [元のドメイン](#origin)
* [Specialty (専門)](#specialty)
* [URL](#url)
* [Web アプリケーション ファイアウォール](#waf)

### <a name="access"></a><a name="access"></a>アクセス

以下の機能でコンテンツのアクセスが制御されます。

| 名前       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [Deny Access (403) (アクセス拒否 (403))](https://docs.vdms.com/cdn/Content/HRE/F/Deny-Access-403.htm) | すべての要求を拒否し、「403 Forbidden」応答を返すかどうかを決定します。 |
| [Token Auth (トークン認証)](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm) | トークン基準の認証を要求に適用するかどうかを決定します。 |
| [Token Auth Denial Code (トークン認証拒否コード)](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm) | トークン基準の認証により要求が拒否されるとき、ユーザーに返す応答の種類を決定します。 |
| [Token Auth Ignore URL Case (トークン認証の URL 大文字/小文字の無視)](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm) | トークン基準の認証で URL を比較するとき、大文字と小文字を区別するかどうかを決定します。 |
| [Token Auth Parameter (トークン認証パラメーター)](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm) | トークン基準の認証のクエリ文字列パラメーターの名前を変更するかどうかを決定します。 |

**[一番上に戻る](#top)**

### <a name="caching"></a><a name="caching"></a>キャッシュ

以下の機能でコンテンツをキャッシュするタイミングと方法をカスタマイズできます。

| 名前       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [Bandwidth Parameters (帯域幅パラメーター)](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Parameters.htm) | 帯域幅調整パラメーター (すなわち、ec_rate と ec_prebuf) が有効になっているかどうかを決定します。 |
| [Bandwidth Throttling (帯域幅調整)](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Throttling.htm) | エッジ サーバーからの応答の帯域幅を調整します。 |
| [Bypass Cache (キャッシュ バイパス)](https://docs.vdms.com/cdn/Content/HRE/F/Bypass-Cache.htm) | 要求がキャッシュ技術を活用できるかどうかを判断します。 |
| [Cache Control Header Treatment (Cache-Control ヘッダーの処理)](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Control-Header-Treatment.htm) |  外部最長有効期間機能がアクティブになっているとき、エッジ サーバーでキャッシュ制御ヘッダーの生成を制御します。 |
| [Cache-Key Query String (キャッシュキー クエリ文字列)](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Query-String.htm) | 要求に関連付けられているクエリ文字列パラメーターを **キャッシュキー** に含めるかどうかを決定します。 <br> キャッシュの目的で資産を一意に識別する相対パス。  キャッシュされたコンテンツの確認時に、エッジ サーバーはこの相対パスを使用します。  既定では、キャッシュキーにはクエリ文字列パラメーターは含まれません。 |
| [Cache-Key Rewrite (キャッシュキー書き換え)](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm) | 要求に関連付けられているキャッシュ キーを書き換えます。 |
| [Complete Cache Fill (完全キャッシュ入力)](https://docs.vdms.com/cdn/Content/HRE/F/Complete-Cache-Fill.htm) | 要求の結果、エッジ サーバーで一部のキャッシュが不足したときの動作を決定します。 |
| [Compress File Types (圧縮ファイルの種類)](https://docs.vdms.com/cdn/Content/HRE/F/Compress-File-Types.htm) | サーバーで圧縮されるファイル形式を定義します。 | 
| [Default Internal Max-Age (既定の内部最長有効期間)](https://docs.vdms.com/cdn/Content/HRE/F/Default-Internal-Max-Age.htm) | エッジ サーバーと配信元サーバーの間のキャッシュ再有効化の既定の最長有効期間を決定します。 |
| [Expires Header Treatment (Expires ヘッダーの処理)](https://docs.vdms.com/cdn/Content/HRE/F/Expires-Header-Treatment.htm) | 外部最長有効期間機能がアクティブになっているとき、エッジ サーバーで Expires ヘッダーの生成を制御します。 |
| [External Max-Age (外部最長有効期間)](https://docs.vdms.com/cdn/Content/HRE/F/External-Max-Age.htm) | ブラウザーとエッジ サーバーの間のキャッシュ再有効化の最長有効期間を決定します。 |
| [Force Internal Max-Age (内部最長有効期間の強制)](https://docs.vdms.com/cdn/Content/HRE/F/Force-Internal-Max-Age.htm) | エッジ サーバーと配信元サーバーの間のキャッシュ再有効化の最長有効期間を決定します。 |
| [H.264 Support (HTTP Progressive Download) (H.264 サポート (HTTP プログレッシブ ダウンロード))](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-HPD.htm) | コンテンツのストリーム配信に使用される H.264 ファイル形式の種類を決定します。 |
| [H.264 Support Video Seek Params (H.264 サポート ビデオ シーク パラメーター)](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-VSP-HPD.htm) | HTTP プログレッシブ ダウンロードを使用するときに、H.264 メディアの検索を制御するパラメーターに割り当てられた名前をオーバーライドします。 |
| [Honor no-cache request (キャッシュなし要求の許可)](https://docs.vdms.com/cdn/Content/HRE/F/Honor-No-Cache-Request.htm) | HTTP クライアントのキャッシュなし要求を配信元サーバーに転送するかどうかを決定します。 |
| [Ignore Origin no-cache (配信元のキャッシュなしを無視する)](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Origin-No-Cache.htm) | 配信元サーバーから提供された特定の指令を CDN が無視するかどうかを決定します。 |
| [Ignore Unsatisfiable Ranges (満たされない範囲を無視する)](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Unsatisfiable-Ranges.htm) | 要求で「416 Requested Range Not Satisfiable」ステータス コードが生成されるとき、クライアントに返される応答を決定します。 |
| [Internal Max-Stale (内部 Max-Stale)](https://docs.vdms.com/cdn/Content/HRE/F/Internal-Max-Stale.htm) | エッジ サーバーが配信元サーバーでキャッシュされたアセットを再有効化できないとき、通常の有効期間を過ぎてから、キャッシュされたアセットをエッジ サーバーから提供できる期間を制御します。 |
| [Partial Cache Sharing (部分キャッシュ共有)](https://docs.vdms.com/cdn/Content/HRE/F/Partial-Cache-Sharing.htm) | 部分的にキャッシュされたコンテンツを要求で生成できるかどうかを決定します。 |
| [Prevalidate Cached Content (キャッシュされたコンテンツの事前有効化)](https://docs.vdms.com/cdn/Content/HRE/F/Prevalidate-Cached-Content.htm) | TTL が期限切れになる前に、キャッシュされたコンテンツに早期検証の資格が与えられるかどうかを決定します。 |
| [Refresh Zero Byte Cache Files (ゼロバイト キャッシュ ファイルの更新)](https://docs.vdms.com/cdn/Content/HRE/F/Refresh-Zero-Byte-Cache-Files.htm) | ゼロバイトのキャッシュ アセットに対する HTTP クライアントの要求をエッジ サーバーが処理する方法を決定します。 |
| [Set Cacheable Status Codes (キャッシュ可能状態コードの設定)](https://docs.vdms.com/cdn/Content/HRE/F/Set-Cacheable-Status-Codes.htm) | 結果としてコンテンツがキャッシュされるステータス コードのセットを定義します。 |
| [Stale Content Delivery on Error (エラー時の古いコンテンツ配信)](https://docs.vdms.com/cdn/Content/HRE/F/Stale-Content-Delivery-on-Error.htm) | キャッシュ再検証中にエラーが発生したとき、または顧客の配信元サーバーから要求されたコンテンツを取得するとき、期限切れになったキャッシュ済みコンテンツを配信するかどうかを決定します。 | 
| [Stale While Revalidate (古いキャッシュを返し、同時に再有効化)](https://docs.vdms.com/cdn/Content/HRE/F/Stale-While-Revalidate.htm) | 古いクライアントを要求元に提供することをエッジ サーバーに許可し、同時に再検証する |

**[一番上に戻る](#top)**

### <a name="comment"></a><a name="comment"></a>Comment (コメント)

Comment (コメント) 機能では、ルール内にメモを追加できます。

**[一番上に戻る](#top)**

### <a name="headers"></a><a name="headers"></a>ヘッダー

以下の機能では、要求または応答のヘッダーを追加、修正、削除できます。

| 名前       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [Age Response Header (Age 応答ヘッダー)](https://docs.vdms.com/cdn/Content/HRE/F/Age-Response-Header.htm) | 要求元に送信する応答に Age 応答ヘッダーを追加するどうかを決定します。 |
| [Debug Cache Response Headers (キャッシュ応答ヘッダーのデバッグ)](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) | 要求された資産のキャッシュ ポリシーに関する情報を提供する [X-EC-Debug 応答ヘッダー](https://docs.vdms.com/cdn/Content/Knowledge_Base/X_EC_Debug.htm)を応答に追加できるかどうかを決定します。 |
| [Modify Client Request Header (クライアント要求ヘッダーの修正)](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm) | 要求のヘッダーを上書き、追加、削除します。 |
| [Modify Client Response Header (クライアント応答ヘッダーの修正)](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm) | 応答のヘッダーを上書き、追加、削除します。 |
| [Set Client IP Custom Header (クライアント IP カスタム ヘッダーの設定)](https://docs.vdms.com/cdn/Content/HRE/F/Set-Client-IP-Custom-Header.htm) | 要求側クライアントの IP アドレスをカスタム要求ヘッダーとして要求に追加することを許可します。 |

**[一番上に戻る](#top)**

### <a name="logs"></a><a name="logs"></a>ログ

以下の機能では、生ログ ファイルに保存されているデータをカスタマイズできます。

| 名前       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [Custom Log Field 1 (カスタム ログ フィールド 1)](https://docs.vdms.com/cdn/Content/HRE/F/Custom-Log-Field-1.htm) | 生ログ ファイルのカスタム ログ フィールドに割り当てられる形式とコンテンツを決定します。 |
| [Log Query String (ログ クエリ文字列)](https://docs.vdms.com/cdn/Content/HRE/F/Log-Query-String.htm) | アクセス ログに URL と共にクエリ文字列を保存するかどうかを決定します。 |

**[一番上に戻る](#top)**

### <a name="optimize"></a><a name="optimize"></a>最適化

以下の機能では、エッジ オプティマイザーによる最適化を要求に適用するかどうかが決定されます。

| 名前       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [Edge Optimizer (エッジ オプティマイザー)](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer.htm) | エッジ オプティマイザーを要求に適用できるかどうかを決定します。 |
| [Edge Optimizer – Instantiate Configuration (エッジ オプティマイザー - 構成のインスタンス化)](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer-Instantiate-Configuration.htm) | サイトに関連付けられているエッジ オプティマイザー構成をインスタンス化またはアクティベートします。 |

**[一番上に戻る](#top)**

### <a name="origin"></a><a name="origin"></a>元のドメイン

以下の機能では、CDN が配信元サーバーと通信する方法を制御できます。

| 名前       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [Maximum Keep-Alive Requests (最大キープアライブ要求)](https://docs.vdms.com/cdn/Content/HRE/F/Maximum-Keep-Alive-Requests.htm) | キープアライブ接続の最大要求数を定義します。この数に達すると終了となります。 |
| [Proxy Special Headers (プロキシの特殊ヘッダー)](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) | エッジ サーバーから配信元サーバーに転送される [CDN 固有要求ヘッダー](https://docs.vdms.com/cdn/Content/Knowledge_Base/Request-Format.htm#RequestHeaders)を定義します。 |

**[一番上に戻る](#top)**

### <a name="specialty"></a><a name="specialty"></a>Specialty (専門)

以下は、上級ユーザーのみご利用いただける高度な機能です。

| 名前       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [Cacheable HTTP Methods (キャッシュ可能 HTTP メソッド)](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-HTTP-Methods.htm) | ネットワークでキャッシュ可能な追加 HTTP メソッドのセットを決定します。 |
| [Cacheable Request Body Size (キャッシュ可能要求の本文サイズ)](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-Request-Body-Size.htm) | POST 応答でキャッシュ可能かどうかを決定するしきい値を定義します。 |
| [QUIC](https://docs.vdms.com/cdn/Content/HRE/F/QUIC.htm) | CDN サービスが QUIC をサポートしていることをクライアントに通知するかどうかを決定します。 |
| [Streaming Optimization (ビデオ ストリーミングの最適化)](https://docs.vdms.com/cdn/Content/HRE/F/Streaming-Optimization.htm) | ライブ ストリームのパフォーマンスを最適化し、配信元サーバーの負荷を軽減するように、キャッシュ構成を調整します。 |
| [User Variable (ユーザー変数)](https://docs.vdms.com/cdn/Content/HRE/F/User-Variable.htm) | 特注のトラフィック処理ソリューションに渡されるユーザー定義変数に値を割り当てます。 |

**[一番上に戻る](#top)**

### <a name="url"></a><a name="url"></a>URL

以下の機能では、要求を別の URL にリダイレクトするか、書き換えることができます。

| 名前       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [Follow Redirects (リダイレクトのフォロー)](https://docs.vdms.com/cdn/Content/HRE/F/Follow-Redirects.htm) | 顧客の配信元サーバーから返される Location (場所) ヘッダーに定義されているホスト名に要求をリダイレクトできるかどうかを決定します。 |
| [URL Redirect (URL リダイレクト)](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm) | Location (場所) ヘッダー経由で要求をリダイレクトします。 |
| [URL Rewrite (URL 書き換え)](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm) | 要求 URL を書き換えます。 |

**[一番上に戻る](#top)**

### <a name="web-application-firewall"></a><a name="waf"></a>Web Application Firewall

[Web アプリケーション ファイアウォール](https://docs.vdms.com/cdn/Content/HRE/F/Web_Application_Firewall.htm)機能は、要求が Web アプリケーション ファイアウォールにより選別されるかどうかを決定します。

**[一番上に戻る](#top)**

最新の機能については、[Verizon のルール エンジン](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions)に関するドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

- [ルール エンジンのリファレンス](cdn-verizon-premium-rules-engine-reference.md)
- [ルール エンジンの条件式](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [ルール エンジンの一致条件](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [ルール エンジンを使用して HTTP 動作をオーバーライドする](cdn-verizon-premium-rules-engine.md)
- [Azure CDN の概要](cdn-overview.md)
