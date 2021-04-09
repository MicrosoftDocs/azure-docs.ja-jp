---
title: IoT Hub での TLS 1.0 と 1.1 の非推奨化 | Microsoft Docs
description: TLS 1.0 および1.1 の非推奨に関するガイドラインと IoT Hub でサポートされている暗号。
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: fcf7620f53c9bfdb51eb62598f2c8b441574eca6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90006082"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>IoT Hub での TLS 1.0 と 1.1 の非推奨化

クラス最高レベルの暗号化を提供するために、IoT Hub は、IoT デバイスとサービス用に選択された暗号化メカニズムとして Transport Layer Security (TLS) 1.2 に移行します。 

## <a name="timeline"></a>タイムライン

IoT Hub は、将来通知があるまで TLS 1.0/1.1 を引き続きサポートします。 ただし、すべてのお客様にできるだけ早く TLS 1.2 に移行することをお勧めします。

## <a name="deprecating-tls-11-ciphers"></a>非推奨となる TLS 1.1 暗号

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SH`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="deprecating-tls-10-ciphers"></a>非推奨となる TLS 1.0 暗号

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="tls-12-cipher-suites"></a>TLS 1.2 暗号スイート

[IoT Hub TLS 1.2 暗号スイート](iot-hub-tls-support.md#cipher-suites)に関するページをご覧ください。
 
## <a name="customer-feedback"></a>カスタマー フィードバック

TLS 1.2 強制は業界全体で最高クラスの暗号化の選択肢であり、計画どおりに有効になりますが、特定のデプロイおよび TLS 1.2 の導入に関する問題についてのご意見をお待ちしております。 このため、[iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com) にコメントを送信できます。
