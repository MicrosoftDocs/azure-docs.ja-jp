---
title: Azure Media Player のエラー コード
description: Azure Media Player のエラー コード リファレンス。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 89f063ab50cfd880552e80a77ba798e4d06de5b3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709582"
---
# <a name="error-codes"></a>エラー コード #

再生を開始できない場合、または再生が停止した場合、エラー イベントが発生して、`error()` 関数により、アプリ開発者が詳細を把握するために役立つコードとオプションのメッセージが返されます。 `error().message` は、ユーザーに表示されるメッセージではありません。  ユーザーに表示されるメッセージは、`error().code` のビット 27 から 20 に基づきます。次の表を参照してください。

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>エラー コード、ビット [31 から 28] (4 ビット) ##

エラーの領域を説明します。

- 0 - 不明
- 1 - AMP
- 2 - AzureHtml5JS
- 3 - FlashSS
- 4 - SilverlightSS
- 5 - HTML5
- 6 - Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>エラー コードビット [27 から 0] (28 ビット) ##

エラーの詳細を説明します。ビット 27 から 20 は概要で、ビット 19 から 0 は、使用可能な場合、より詳細な情報を提供します。


| amp.errorCode.[名前] | コード、ビット [27 から 0] (28 ビット) | 説明 |
|---|---:|---|
| **MEDIA_ERR_ABORTED エラー: 範囲 (0x0100000 から 0x01FFFFF)** | | |
| abortedErrUnknown | 0x0100000 | 一般的な中止エラー |
| abortedErrNotImplemented | 0x0100001 | 中止エラー、実装されていない |
| abortedErrHttpMixedContentBlocked | 0x0100002 | 中止エラー、混在したコンテンツがブロックされている - 通常、`https://` ページから `http://` ストリームを読み込んだときに発生します |
| **MEDIA_ERR_NETWORK エラー: 開始値 (0x0200000 から 0x02FFFFF)** | | |
| networkErrUnknown | 0x0200000 | 一般的なネットワーク エラー |
| networkErrHttpBadUrlFormat | 0x0200190 | HTTP 400 エラー応答 |
| networkErrHttpUserAuthRequired | 0x0200191 | HTTP 401 エラー応答 |
| networkErrHttpUserForbidden | 0x0200193 | HTTP 403 エラー応答 |
| networkErrHttpUrlNotFound | 0x0200194 | HTTP 404 エラー応答 |
| networkErrHttpNotAllowed | 0x0200195 | HTTP 405 エラー応答 |
| networkErrHttpGone | 0x020019A | HTTP 410 エラー応答 |
| networkErrHttpPreconditionFailed | 0x020019C | HTTP 412 エラー応答 |
| networkErrHttpInternalServerFailure | 0x02001F4 | HTTP 500 エラー応答
| networkErrHttpBadGateway | 0x02001F6 | HTTP 502 エラー応答 |
| networkErrHttpServiceUnavailable | 0x02001F7 | HTTP 503 エラー応答 |
| networkErrHttpGatewayTimeout | 0x02001F8 | HTTP 504 エラー応答 |
| networkErrTimeout | 0x0200258 | ネットワーク タイムアウト エラー
| networkErrErr | 0x0200259 | ネットワーク接続エラー応答 |
| **MEDIA_ERR_DECODE エラー (0x0300000 から 0x03FFFFF)** | | |
| decodeErrUnknown | 0x0300000 | 一般的なデコード エラー |
| **MEDIA_ERR_SRC_NOT_SUPPORTED エラー (0x0400000 から 0x04FFFFF)** | | |
| srcErrUnknown | 0x0400000 | サポートされていないソースに関する一般的なエラー |
| srcErrParsePresentation | 0x0400001 | プレゼンテーション解析エラー |
| srcErrParseSegment | 0x0400002 | セグメント解析エラー |
| srcErrUnsupportedPresentation | 0x0400003 | プレゼンテーションがサポートされていない |
| srcErrInvalidSegment | 0x0400004 | 無効なセグメント |
| **MEDIA_ERR_ENCRYPTED エラー: 開始値 (0x0500000 から 0x05FFFFF)** | | |
| encryptErrUnknown | 0x0500000 | 一般的な暗号化エラー | 
| encryptErrDecrypterNotFound | 0x0500001 | 暗号化解除が見つからない |
| encryptErrDecrypterInit | 0x0500002 | 暗号化解除の初期化エラー |
| encryptErrDecrypterNotSupported | 0x0500003 | 暗号化解除がサポートされていない |
| encryptErrKeyAcquire | 0x0500004 | キーの取得に失敗した |
| encryptErrDecryption | 0x0500005 | セグメントの暗号化解除に失敗した |
| encryptErrLicenseAcquire | 0x0500006 | ライセンスの取得に失敗した |
| **SRC_PLAYER_MISMATCH エラー: 開始値 (0x0600000 - 0x06FFFFF)** | | |
| srcPlayerMismatchUnknown | 0x0600000 | ソースを再生するための一致するテクノロジ プレーヤーがない場合の一般的なエラー |
| srcPlayerMismatchFlashNotInstalled | 0x0600001 |Flash プラグインがインストールされていません。インストールすれば、ソースが再生される可能性があります。 *または* Flash 30 がインストールされていて、AES コンテンツを再生しています。  この場合は、別のブラウザーをお試しください。 Flash 30 は、7 月 7 日の時点でサポートされていません。 詳細については、「[既知の問題](azure-media-player-known-issues.md)」を参照してください。 注:0x00600003 の場合、Flash と Silverlight の両方がインストールされていません (techOrder で指定されている場合)。|
| srcPlayerMismatchSilverlightNotInstalled | 0x0600002 | Silverlight プラグインがインストールされていません。インストールすれば、ソースが再生される可能性があります。 注:0x00600003 の場合、Flash と Silverlight の両方がインストールされていません (techOrder で指定されている場合)。 |
| | 0x00600003 | Flash と Silverlight の両方がインストールされていません (techOrder で指定されている場合)。 |
| **不明なエラー (0x0FF00000)** | | |
| errUnknown | 0xFF00000 | 不明なエラー |

## <a name="user-error-messages"></a>ユーザー エラー メッセージ ##

表示されるユーザー メッセージは、エラー コードのビット 27 から 20 に基づきます。

- MEDIA_ERR_ABORTED (1) - "You aborted the video playback" \(ビデオの再生を中止しました\)
- MEDIA_ERR_NETWORK (2) - "ネットワーク エラーのためにビデオのダウンロードが途中で失敗しました。"
- MEDIA_ERR_DECODE (3) - "ビデオが破損しているか、ブラウザーがサポートしていない機能をビデオが使用しているため、ビデオの再生が中断しました。"
- MEDIA_ERR_SRC_NOT_SUPPORTED (4) - "ビデオを読み込めませんでした。サーバーまたはネットワークの障害のためか、あるいはフォーマットがサポートされていません。"
- MEDIA_ERR_ENCRYPTED (5) - "The video is encrypted and we do not have the keys to decrypt it." \(ビデオが暗号化されています。暗号化を解除するためのキーがありません。\)
- SRC_PLAYER_MISMATCH (6) - "No compatible source was found for this video." \(このビデオと互換性のあるソースが見つかりませんでした。\)
- MEDIA_ERR_UNKNOWN (0xFF) - "不明なエラーが発生しました。"

## <a name="examples"></a>例 ##

### <a name="0x10600001"></a>0x10600001 ##

"No compatible source was found for this video." \(このビデオと互換性のあるソースが見つかりませんでした。\)  がエンド ユーザーに表示されます。

要求されたソースを再生できる技術プレーヤーがありません。ただし、Flash プラグインをインストールすれば、ソースが再生される可能性があります。  

### <a name="0x20200194"></a>0x20200194 ###

"ネットワーク エラーのためにビデオのダウンロードが途中で失敗しました。"  がエンド ユーザーに表示されます。

AzureHtml5JS が HTTP 404 応答から再生できませんでした。

### <a name="categorizing-errors"></a>エラーの分類 ###

```javascript
    if(myPlayer.error().code & amp.errorCode.abortedErrStart) {
        // MEDIA_ERR_ABORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.networkErrStart) {
        // MEDIA_ERR_NETWORK errors
    }
    else if(myPlayer.error().code & amp.errorCode.decodeErrStart) {
        // MEDIA_ERR_DECODE errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcErrStart) {
        // MEDIA_ERR_SRC_NOT_SUPPORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.encryptErrStart) {
        // MEDIA_ERR_ENCRYPTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcPlayerMismatchStart) {
        // SRC_PLAYER_MISMATCH errors
    }
    else {
        // unknown errors
    }
```

### <a name="catching-a-specific-error"></a>特定のエラーのキャッチ ###

次のコードは、404 エラーをキャッチします。

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>次のステップ ##

- [Azure Media Player クイックスタート](azure-media-player-quickstart.md)