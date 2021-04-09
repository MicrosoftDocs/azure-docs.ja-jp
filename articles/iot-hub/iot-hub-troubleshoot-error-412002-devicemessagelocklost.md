---
title: Azure IoT Hub のエラー 412002 DeviceMessageLockLost のトラブルシューティング
description: エラー 412002 DeviceMessageLockLost を修正する方法の概要
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 53364009f9b9c041c39728e438c3e24eacfd1665
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435479"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

この記事では、**412002 DeviceMessageLockLost** エラーの原因と解決策について説明します。

## <a name="symptoms"></a>現象

cloud-to-device メッセージを送信しようとすると、要求がエラー **412002 DeviceMessageLockLost** で失敗します。

## <a name="cause"></a>原因

デバイスが (たとえば [`ReceiveAsync()`](/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync) を使用して) cloud-to-device メッセージをキューから受信すると、1 分間のロック タイムアウト期間、IoT Hub によってメッセージはロックされます。 ロック タイムアウトが終了した後にデバイスでメッセージの完了が試行されると、IoT Hub からこの例外がスローされます。

## <a name="solution"></a>解決策

1 分間のロック タイムアウト期間内に IoT Hub が通知を受け取らないと、メッセージは、"*エンキュー済み*" の状態に戻されます。 デバイスは、メッセージの受信を再試行できます。 今後このエラーが発生しないようにするには、メッセージを受け取ってから 1 分以内にメッセージを完了するようにデバイス側のロジックを実装します。 この 1 分間のタイムアウトを変更することはできません。