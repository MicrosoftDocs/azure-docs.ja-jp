---
title: Azure CDN ルール エンジンの X-EC-Debug HTTP ヘッダー | Microsoft Docs
description: X-EC-Debug デバッグ キャッシュ要求ヘッダーでは、要求された資産に適用されているキャッシュ ポリシーに関する追加情報が提供されます。 これらのヘッダーは Verizon に固有です。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: allensu
ms.openlocfilehash: 4154c6a1e739f935022271e7a101f39d3ee5c500
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84343022"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>Azure CDN ルール エンジンの X-EC-Debug HTTP ヘッダー
デバッグ キャッシュ要求ヘッダー `X-EC-Debug` では、要求された資産に適用されているキャッシュ ポリシーに関する追加情報が提供されます。 これらのヘッダーは、**Azure CDN Premium from Verizon** 製品に固有です。

## <a name="usage"></a>使用法
POP サーバーからユーザーに送信される応答には、以下の条件を満たす場合にのみ、`X-EC-Debug` ヘッダーが含まれます。

- [Debug Cache Response Headers 機能](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm)が、指定された要求のルール エンジンで有効になっている。
- 指定された要求で、応答に含められるデバッグ キャッシュ応答ヘッダーのセットが定義されている。

## <a name="requesting-debug-cache-information"></a>デバッグ キャッシュ情報の要求
指定された要求で以下のディレクティブを使用して、応答に含められるデバッグ キャッシュ情報を定義します。

要求ヘッダー | 説明 |
---------------|-------------|
X-EC-Debug: x-ec-cache | [キャッシュの状態コード](#cache-status-code-information)
X-EC-Debug: x-ec-cache-remote | [キャッシュの状態コード](#cache-status-code-information)
X-EC-Debug: x-ec-check-cacheable | [キャッシュ可能](#cacheable-response-header)
X-EC-Debug: x-ec-cache-key | [キャッシュ キー](#cache-key-response-header)
X-EC-Debug: x-ec-cache-state | [キャッシュの状態](#cache-state-response-header)

### <a name="syntax"></a>構文

デバッグ キャッシュ応答ヘッダーは、要求に次のヘッダーと指定されたディレクティブを含めることによって要求できます。

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>X-EC-Debug ヘッダーのサンプル

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>キャッシュの状態コード情報
X-EC-Debug 応答ヘッダーでは、以下のディレクティブを通じてサーバーとその応答の処理を識別することができます。

ヘッダー | 説明
-------|------------
X-EC-Debug: x-ec-cache | このヘッダーは、CDN からコンテンツがルーティングされるたびに報告されます。 要求を満たした POP サーバーを識別します。
X-EC-Debug: x-ec-cache-remote | このヘッダーは、要求されたコンテンツが配信元シールド サーバーまたは ADN ゲートウェイ サーバーにキャッシュされた場合にのみ報告されます。

### <a name="response-header-format"></a>応答ヘッダーの形式

X-EC-Debug ヘッダーでは、以下の形式でキャッシュの状態コード情報を報告します。

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

上記の応答ヘッダー構文で使用される用語は、次のように定義されています。
- StatusCode: 要求されたコンテンツが CDN で処理された方法を示します。これは、キャッシュの状態コードで表されます。
    
    TCP_DENIED 状態コードは、トークン ベース認証により承認されていない要求が拒否されたときに、NONE の代わりに報告される場合があります。 ただし、NONE 状態コードは、キャッシュの状態レポートまたは生ログ データを表示する際に引き続き使用されます。

- Platform: コンテンツが要求されたプラットフォームを示します。 このフィールドでは、次のコードが有効です。

    コード  | プラットフォーム
    ------| --------
    ECAcc | HTTP ラージ
    ECS   | HTTP スモール
    ECD   | Application Delivery Network (ADN)

- POP: 要求を処理した [POP](cdn-pop-abbreviations.md) を示します。 

### <a name="sample-response-headers"></a>応答ヘッダーのサンプル

次のサンプル ヘッダーでは、要求のキャッシュ状態コード情報が提供されます。

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>キャッシュ可能な応答ヘッダー
`X-EC-Debug: x-ec-check-cacheable` 応答ヘッダーは、要求されたコンテンツがキャッシュされた可能性があるかどうかを示します。

この応答ヘッダーでは、キャッシュが行われたかどうかは示されません。 代わりに、要求がキャッシュの対象だったかどうかが示されます。

### <a name="response-header-format"></a>応答ヘッダーの形式

要求がキャッシュされた可能性があるかどうかを報告する `X-EC-Debug` 応答ヘッダーの形式は次のとおりです。

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

上記の応答ヘッダー構文で使用される用語は、次のように定義されています。

値  | 説明
-------| --------
YES    | 要求されたコンテンツがキャッシュの対象だったことを示します。
NO     | 要求されたコンテンツがキャッシュの対象外だったことを示します。 この状態の場合、次のいずれかの理由が考えられます。 <br /> - 顧客に固有の構成: アカウントに固有の構成により、POP サーバーで資産をキャッシュできない場合があります。 たとえば、ルール エンジンで対象要求に対してバイパス キャッシュ機能を有効にすると、資産がキャッシュされなくなる場合があります。<br /> - キャッシュ応答ヘッダー: 要求された資産の Cache-Control および Expires ヘッダーにより、POP サーバーで資産をキャッシュできない場合があります。
UNKNOWN | サーバーで、要求された資産がキャッシュ可能かどうかを評価できなかったことを示します。 この状態は通常、要求がトークン ベース認証により拒否された場合に発生します。

### <a name="sample-response-header"></a>応答ヘッダーのサンプル

以下の応答ヘッダーのサンプルは、要求されたコンテンツがキャッシュされた可能性があるかどうかを示します。

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Cache-Key 応答ヘッダー
`X-EC-Debug: x-ec-cache-key` 応答ヘッダーは、要求されたコンテンツに関連付けられている物理キャッシュキーを示します。 物理キャッシュキーは、キャッシュの目的で資産を識別するパスで構成されます。 つまり、サーバーは、キャッシュキーによって定義されたパスに従って、資産のキャッシュされたバージョンがないかどうかを確認します。

この物理キャッシュキーは二重スラッシュ (//) で始まり、その後にコンテンツを要求するために使用されるプロトコル (HTTP または HTTPS) が続きます。 このプロトコルの後に、要求された資産の相対パスが続きます。このパスはコンテンツ アクセス ポイントで始まります (_/000001/_ など)。

既定では、HTTP プラットフォームは、*standard-cache* を使用するように構成されます。これは、キャッシュ メカニズムではクエリ文字列が無視されることを意味します。 この種の構成では、キャッシュキーにクエリ文字列データを含めることはできません。

クエリ文字列がキャッシュキーで記録されている場合、同等のハッシュに変換され、要求された資産の名前とそのファイル拡張子の間に挿入されます (例: 資産&lt;ハッシュ値&gt;.html)。

### <a name="response-header-format"></a>応答ヘッダーの形式

`X-EC-Debug` 応答ヘッダーでは、以下の形式で物理キャッシュキー情報が報告されます。

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>応答ヘッダーのサンプル

以下の応答ヘッダーのサンプルは、要求されたコンテンツに対する物理キャッシュキーを示しています。

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>キャッシュ状態応答ヘッダー
`X-EC-Debug: x-ec-cache-state` 応答ヘッダーは、コンテンツの要求時のキャッシュ状態を示します。

### <a name="response-header-format"></a>応答ヘッダーの形式

`X-EC-Debug` 応答ヘッダーでは、以下の形式でキャッシュ状態情報を報告します。

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

上記の応答ヘッダー構文で使用される用語は、次のように定義されています。

- MASeconds: 要求されたコンテンツの Cache-Control ヘッダーで定義されている最長有効期間 (秒単位) を示します。

- MATimePeriod: 最長有効期間の値 (つまり、MASeconds) を、ほぼ同等の大きい単位 (日数など) に変換します。 

- UnixTime: 要求されたコンテンツのキャッシュ タイムスタンプを Unix 時間 (POSIX 時間または Unix エポックとも呼ばれます) で示します。 キャッシュ タイムスタンプは、資産の TTL の計算元となる開始日付/時刻を示します。 

    配信元サーバーでサード パーティの HTTP キャッシュ サーバーが利用されていないか、そのサーバーが Age 応答ヘッダーを返さない場合、キャッシュ タイムスタンプは常に、資産が取得または再検証された日付/時刻になります。 それ以外の場合、POP サーバーでは Age フィールドを使用して、Retrieval/RevalidateDateTime - Age のように資産の TTL を計算します。

- ddd, dd MMM yyyy HH:mm:ss GMT: 要求されたコンテンツのキャッシュ タイムスタンプを示します。 詳細については、上記の UnixTime 用語を参照してください。

- CASeconds: キャッシュ タイムスタンプから経過した秒数を示します。

- RTSeconds: キャッシュされたコンテンツが新しいものと見なされる場合の残りの秒数を示します。 この値は、RTSeconds = 最長有効期間 - キャッシュ時間のように計算されます。

- RTTimePeriod: 残りの TTL 値 (つまり、RTSeconds) を、ほぼ同等の大きい単位 (日数など) に変換します。

- ExpiresSeconds: `Expires` 応答ヘッダーに指定された日付/時刻までの残りの秒数を示します。 `Expires` 応答ヘッダーが応答に含まれていなかった場合は、この用語の値は *none* になります。

### <a name="sample-response-header"></a>応答ヘッダーのサンプル

以下の応答ヘッダーのサンプルは、コンテンツの要求時のキャッシュ状態を示します。

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

