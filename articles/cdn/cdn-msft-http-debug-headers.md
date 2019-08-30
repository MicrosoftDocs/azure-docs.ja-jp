---
title: Microsoft からの Azure CDN に対するデバッグ HTTP ヘッダー | Microsoft Docs
description: デバッグ キャッシュ要求ヘッダーでは、要求された資産に適用されるキャッシュ ポリシーに関する追加情報が提供されます。 これらのヘッダーは、Microsoft からの Azure CDN に固有のものです。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: magattus
ms.openlocfilehash: 297c65c1cd89163b8663819f844dc6c2a83fd1bf
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814080"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Microsoft からの Azure CDN に対するデバッグ HTTP ヘッダー
デバッグ応答ヘッダー `X-Cache` では、コンテンツ提供元の CDN スタックのレイヤーに関する詳細が提供されます。 このヘッダーは、Microsoft からの Azure CDN に固有のものです。

### <a name="response-header-format"></a>応答ヘッダーの形式

ヘッダー | 説明
-------|------------
X-Cache: TCP_HIT | このヘッダーは、コンテンツが CDN エッジ キャッシュから提供されているときに返されます。 
X-Cache: TCP_REMOTE_HIT | このヘッダーは、コンテンツが CDN リージョン キャッシュ (配信元シールド レイヤー) から提供されているときに返されます。
X-Cache: TCP_MISS | このヘッダーは、キャッシュ ミスがあり、コンテンツが配信元から提供されているときに返されます。 


