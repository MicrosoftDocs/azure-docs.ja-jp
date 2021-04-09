---
title: Azure CDN ルール エンジンの Verizon 固有 HTTP ヘッダー | Microsoft Docs
description: この記事では、Azure CDN ルール エンジンで Verizon 固有 HTTP ヘッダーを使用する方法について説明します。
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
ms.date: 04/16/2018
ms.author: allensu
ms.openlocfilehash: e20f6ce9540d357b61ae2cfdf0e8f96d127dc6c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84343219"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Azure CDN ルール エンジンの Verizon 固有 HTTP ヘッダー

**Verizon の Azure CDN Premium** 製品では、HTTP 要求が配信元サーバーに送信されるとき、ポイント オブジェクト プレゼンス (POP) サーバーは、POP へのクライアント要求内に 1 つ以上の予約済みヘッダー (またはプロキシの特殊ヘッダー) を追加できます。 これらのヘッダーは、受信された標準の転送ヘッダーに追加されます。 標準の要求ヘッダーについては、[要求フィールド](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields)に関するページを参照してください。

これらのいずれかの予約済みヘッダーが配信元サーバーへの Azure CDN (Content Delivery Network) POP 要求内に追加されないようにする場合は、ルール エンジン内に[プロキシの特殊ヘッダー機能](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm)に関するルールを作成する必要があります。 このルールでは、削除するヘッダーをヘッダー フィールド内の既定のヘッダーのリストから除外します。 [キャッシュ応答ヘッダーのデバッグ機能](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm)を有効にしている場合は、必要な `X-EC-Debug` ヘッダーを追加するようにしてください。 

たとえば、`Via` ヘッダーを削除するには、ルールのヘッダー フィールドに *X-Forwarded-For、X-Forwarded-Proto、X-Host、X-Midgress、X-Gateway-List、X-EC-Name、Host* のヘッダーのリストを含める必要があります。 

![プロキシの特殊ヘッダー ルール](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

次の表では、Verizon CDN POP が要求内に追加できるヘッダーについて説明します。

要求ヘッダー | 説明 | 例
---------------|-------------|--------
[Via](#via-request-header) | 配信元サーバーへの要求をプロキシ処理した POP サーバーを識別します。 | HTTP/1.1 ECS (dca/1A2B)
X-Forwarded-For | 要求者の IP アドレスを示します。| 10.10.10.10
X-Forwarded-Proto | 要求のプロトコルを示します。 | http
X-Host | 要求のホスト名を示します。 | cdn.mydomain.com
X-Midgress | 要求が追加の CDN サーバー経由でプロキシ処理されたかどうかを示します。 たとえば、POP サーバーから配信元シールド サーバーへ、または POP サーバーから ADN ゲートウェイ サーバーへです。 <br />このヘッダーは、midgress トラフィックが転送される場合にのみ要求に追加されます。 この場合、要求が追加の CDN サーバー経由でプロキシ処理されたことを示すために、ヘッダーは 1 に設定されます。| 1
[Host](#host-request-header) | 要求されたコンテンツが見つかる可能性のあるホストとポートを識別します。 | marketing.mydomain.com:80
[X-Gateway-List](#x-gateway-list-request-header) | ADN: 顧客配信元に割り当てられた ADN ゲートウェイ サーバーのフェールオーバー リストを識別します。 <br />Origin shield: 顧客配信元に割り当てられた配信元シールド サーバーのセットを示します。 | `icn1,hhp1,hnd1`
X-EC- _&lt;name&gt;_ | *X-EC* で始まる要求ヘッダー (X-EC-Tag や [X-EC-Debug](cdn-http-debug-headers.md) など) が、CDN で使用されるために予約されます。| waf-production

## <a name="via-request-header"></a>Via 要求ヘッダー
`Via` 要求ヘッダーが POP サーバーを識別するための形式は、次の構文で指定されます。

`Via: Protocol from Platform (POP/ID)` 

この構文で使用される用語は、次のように定義されます。
- Protocol: 要求をプロキシ処理するために使用されるプロトコルのバージョン (HTTP/1.1 など) を示します。 

- Platform: コンテンツが要求されたプラットフォームを示します。 このフィールドでは、次のコードが有効です。 

    コード | プラットフォーム
    -----|---------
    ECAcc | HTTP ラージ
    ECS   | HTTP スモール
    ECD   | Application Delivery Network (ADN)

- POP: 要求を処理した [POP](cdn-pop-abbreviations.md) を示します。 

- ID: 内部使用のみ。

### <a name="example-via-request-header"></a>Via 要求ヘッダーの例

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Host 要求ヘッダー
次の両方の条件が満たされた場合、POP サーバーは `Host` ヘッダーを上書きします。
- 要求されたコンテンツのソースが顧客配信元サーバーである。
- 対応する顧客配信元の [HTTP Host Header (HTTP ホスト ヘッダー)] オプションが空白ではない。

`Host` 要求ヘッダーは、[HTTP Host Header (HTTP ホスト ヘッダー)] オプションで定義された値を反映するように上書きされます。
顧客配信元の [HTTP Host Header (HTTP ホスト ヘッダー)] オプションが空白に設定されている場合は、要求者によって送信された `Host` 要求ヘッダーが顧客の配信元サーバーに転送されます。

## <a name="x-gateway-list-request-header"></a>X-Gateway-List 要求ヘッダー
次のいずれかの条件が満たされた場合、POP サーバーは X-Gateway-List 要求ヘッダーを追加および上書きします。
- 要求が ADN プラットフォームを指している。
- 要求が、配信元シールド機能によって保護されている顧客配信元サーバーに転送されている。

