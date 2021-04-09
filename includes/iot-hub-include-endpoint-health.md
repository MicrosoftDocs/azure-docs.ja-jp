---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 24a07109fc8f4d6ebd283dee7ee00107f0eb49b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95564177"
---
REST API の [Get Endpoint Health](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) を使用して、エンドポイントの正常性状態を取得できます。 エンドポイントの正常性が停止または異常である場合、エンドポイントがこれらの状態にあるときは待機時間が長くなることが予測されるため、ルーティング メッセージ待機時間に関連した [IoT Hub ルーティング メトリック](../articles/iot-hub/monitor-iot-hub-reference.md#routing-metrics)を使用して、エラーを識別およびデバッグすることをお勧めします。 IoT Hub メトリックの使用に関する詳細については、[IoT Hub の監視](../articles/iot-hub/monitor-iot-hub.md)に関する記事を参照してください。

|正常性状態|説明|
|---|---|
|healthy|エンドポイントは想定どおりにメッセージを受け付けています。|
|unhealthy|エンドポイントはメッセージを受け付けておらず、IoT Hub はこのエンドポイントへのメッセージ送信を再試行しています。|
|unknown|IoT Hub はこのエンドポイントにメッセージを配信しようとしていません。|
|degraded|エンドポイントは、想定より遅くメッセージを受け入れているか、異常な状態から回復中です。|
|dead|IoT Hub は、このエンドポイントにメッセージを配信しなくなりました。 このエンドポイントへのメッセージ送信の再試行に失敗しました。|