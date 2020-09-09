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
ms.openlocfilehash: a0d1de622eefad4ae5e55a427f8b0b1bf4360c0a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84792093"
---
REST API の [Get Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) を使用して、エンドポイントの正常性状態を取得できます。 エンドポイントの正常性が停止または異常である場合は、エンドポイントがこれらの状態にあるときは待機時間が長くなることが予測されるため、ルーティング メッセージ待機時間に関連した [IoT Hub メトリック](../articles/iot-hub/iot-hub-metrics.md)を使用してエラーを識別およびデバッグすることをお勧めします。


|正常性状態|説明|
|---|---|
|healthy|エンドポイントは想定どおりにメッセージを受け付けています。|
|unhealthy|エンドポイントはメッセージを受け付けておらず、IoT Hub はこのエンドポイントへのメッセージ送信を再試行しています。|
|unknown|IoT Hub はこのエンドポイントにメッセージを配信しようとしていません。|
|degraded|エンドポイントは、想定より遅くメッセージを受け入れているか、異常な状態から回復中です。|
|dead|IoT Hub は、このエンドポイントにメッセージを配信しなくなりました。 このエンドポイントへのメッセージ送信の再試行に失敗しました。|
