---
title: Azure IoT Hub のエラー 403004 DeviceMaximumQueueDepthExceeded のトラブルシューティング
description: エラー 403004 DeviceMaximumQueueDepthExceeded を修正する方法の概要
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 421066ef30e23a79b26f97939cdfffb5be83afb5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148245"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

この記事では、**403004 DeviceMaximumQueueDepthExceeded** エラーの原因と解決策について説明します。

## <a name="symptoms"></a>現象

cloud-to-device メッセージを送信しようとすると、要求がエラー **403004** または **DeviceMaximumQueueDepthExceeded** で失敗します。

## <a name="cause"></a>原因

根本的な原因は、デバイスにエンキューされたメッセージの数が[キュー制限 (50)](./iot-hub-devguide-quotas-throttling.md#other-limits) を超えていることです。

この制限に達している最も可能性の高い理由は、HTTPS を使用してメッセージを受信しているためです。これにより、`ReceiveAsync` を使用した継続的なポーリングが行われ、結果として IoT Hub で要求が調整されます。

## <a name="solution"></a>解決策

HTTPS を使用するクラウドからデバイスへのメッセージに関してサポートされているパターンは、メッセージを低頻度 (25 分未満の間隔) でチェックするデバイスに断続的に接続されます。 キューの上限に達する可能性を低くするには、cloud-to-device メッセージに対して AMQP または MQTT に切り替えます。

または、デバイス側のロジックを強化して、キューに格納されたメッセージの完了、拒否、または破棄を迅速に行うこと、有効期限を短縮すること、または送信するメッセージ数を減らすことを検討します。 [C2D メッセージの有効期限](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live)に関するセクションを参照してください。

最後に、[Purge Queue API](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletedevice) を使用して、上限に達する前に保留中のメッセージを定期的にクリーンアップすることを検討してください。