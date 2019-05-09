---
title: Azure IoT Hub の cloud-to-device メッセージの理解 | Microsoft Docs
description: 開発者ガイド - IoT Hub での cloud-to-device メッセージの使用方法。 メッセージのライフサイクル、および構成オプションについて説明します。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: c8424743f30ec1bbf8d8096f6630c7451bc910c8
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010244"
---
# <a name="send-cloud-to-device-messages-from-iot-hub"></a>cloud-to-device メッセージを IoT Hub から送信する

ソリューション バックエンドから、デバイス アプリに一方向の通知を送信するには、IoT hub からデバイスに cloud-to-device メッセージを送信します。 IoT Hub でサポートされるその他の cloud-to-device オプションの詳細については、「[cloud-to-device 通信に関するガイダンス](iot-hub-devguide-c2d-guidance.md)」をご覧ください。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

C2D メッセージはサービス向けエンドポイント (**/messages/devicebound**) を介して送信できます。 デバイスは、次にそのメッセージをデバイス固有のエンドポイント (**/devices/{deviceId}/messages/devicebound**) を介して受信します。

各 C2D メッセージの宛先を単一のデバイスにするために、IoT Hub では、**to** プロパティが **/devices/{deviceId}/messages/devicebound** に設定されます。

各デバイスのキューで保持できる C2D メッセージは最大 50 個です。 同じデバイスにそれ以上のメッセージを送信しようとすると、エラーが発生します。

## <a name="the-cloud-to-device-message-lifecycle"></a>cloud-to-device メッセージのライフ サイクル

少なくとも 1 回のメッセージ配信を保証するために、IoT Hub は cloud-to-device デバイス メッセージをデバイスごとのキューに保持します。 IoT Hub がキューからメッセージを削除できるように、デバイスで明示的に *完了* を確認する必要があります。 このような方法で、接続とデバイスの障害に対する復元性が保証されます。

次の図に、IoT Hub の cloud-to-device メッセージのライフサイクルの状態に関するグラフを示します。

![クラウドとデバイス間のメッセージのライフ サイクル](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

IoT Hub サービスは、デバイスにメッセージを送信するときに、メッセージの状態を **Enqueued** に設定します。 デバイスでメッセージを*受信*する場合、デバイス上の他のスレッドが他のメッセージの受信を開始できるようにするために、IoT Hub によりメッセージが*ロック*されます (状態を**非表示**に設定することによる)。 デバイスのスレッドがメッセージの処理を完了すると、メッセージを *完了* して IoT Hub にその旨を通知します。 IoT Hub は、次に状態を**完了**に設定します。

デバイスは次の選択をすることもあります。

* メッセージの*拒否*。この場合、IoT Hub によってメッセージの状態が **[配信不能]** に設定されます。 MQTT プロトコル経由で接続するデバイスは、cloud-to-device メッセージを拒否できません。

* メッセージの*破棄*。この場合、IoT Hub によってメッセージがキューに戻され、状態が**エンキュー**に設定されます。 MQTT プロトコル経由で接続するデバイスは、cloud-to-device メッセージを破棄できません。

スレッドが IoT Hub に通知することなく、メッセージの処理に失敗することもあります。 その場合、*表示 (またはロック) がタイムアウト*になった後で、メッセージの状態が自動的に**非表示**から**エンキュー**に切り替わります。 このタイムアウトの既定値は 1 分です。

メッセージの状態は**エンキュー**と**非表示**の間で切り替わりますが、この回数が IoT Hub の**最大配信数**のプロパティで指定された上限に達すると、 IoT Hub によってメッセージの状態が **[配信不能]** に設定されます。 同様に、有効期限 ([有効期限](#message-expiration-time-to-live)に関するセクションを参照) が切れた後も、IoT Hub によってメッセージの状態が **[配信不能]** に設定されます。

「[How to send cloud-to-device messages with IoT Hub (cloud-to-device メッセージを IoT Hub で送信する方法)](iot-hub-csharp-csharp-c2d.md)」では、cloud-to-device メッセージを、クラウドから送信してデバイスで受信する方法について説明します。

通常、メッセージの損失がアプリケーション ロジックに影響しない場合、デバイスは cloud-to-device メッセージを完了します。 たとえば、デバイスがメッセージの内容をローカルに保持していた場合、または正常に操作が実行されていた場合などです。 また、メッセージには、メッセージの損失がアプリケーションの機能に影響しないことを示す一時的な情報が含まれている場合もあります。 タスクの実行時間が長いときに、場合によっては次の操作を実行できます。

* タスクの説明をローカル ストレージに保持した後で C2D メッセージを完了する

* タスクの進行状況のさまざまな段階で、1 つ以上の D2C メッセージによりソリューション バックエンドに通知する

## <a name="message-expiration-time-to-live"></a>メッセージの有効期限

すべての C2D メッセージに有効期限があります。 この期間は、次のいずれかによって設定されます。

* サービスの **ExpiryTimeUtc** プロパティ
* IoT Hub のプロパティとして指定された既定の "*有効期限*" を使用している IoT Hub

「 [C2D の構成オプション](#cloud-to-device-configuration-options)」を参照してください。

メッセージの有効期限を利用し、接続されていないデバイスにメッセージが送信されないようにするには、一般的には、有効期限に短い値を設定します。 この方法を使用すると、デバイスの接続状態を維持するのと同じ結果が得られますが、より効率化されます。 メッセージの受信確認を要求すると、IoT Hub は、どのデバイスが次の状態であるかを通知します。

* メッセージを受信できる
* オンラインでないか、失敗した

## <a name="message-feedback"></a>メッセージのフィードバック

C2D メッセージを送信するときに、サービスは、そのメッセージの最終状態に関するメッセージごとのフィードバックの配信を要求できます。

| Ack プロパティ | 動作 |
| ------------ | -------- |
| **positive** | C2D メッセージの状態が**完了**に達した場合に、IoT Hub によりフィードバック メッセージが生成されます。 |
| **negative** | C2D メッセージの状態が **[配信不能]** に達した場合に、IoT Hub によりフィードバック メッセージが生成されます。 |
| **full**     | IoT Hub は、どちらの場合にも、フィードバック メッセージを生成します。 |

**Ack** が **full** の場合で、フィードバック メッセージを受信しない場合は、フィードバック メッセージの有効期限が切れたことを意味します。 このとき、元のメッセージがどうなったかをサービスが認識することはできません。 実際には、有効期限切れになる前にフィードバックをサービスが確実に処理できることが必要です。 有効期限は最長 2 日間であるため、障害が発生した場合も、サービスを再び稼働させる時間はあります。

「 [エンドポイント](iot-hub-devguide-endpoints.md)」で説明したように、IoT Hub はサービス向けエンドポイント (**/messages/servicebound/feedback**) 経由で、メッセージとしてフィードバックを配信します。 フィードバックを受信するためのセマンティクスは C2D メッセージの場合と同様です。 可能な限り、メッセージのフィードバックは、次の形式で 1 つのメッセージのバッチとして処理します。

| プロパティ     | 説明 |
| ------------ | ----------- |
| EnqueuedTime | フィードバック メッセージがハブで受信された日時を示すタイムスタンプ。 |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

本文はシリアル化された JSON レコードの配列で、それぞれ次のプロパティを持っています。

| プロパティ           | 説明 |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | メッセージの結果が発生した日時を示すタイムスタンプ。 たとえば、ハブがフィードバック メッセージを受信したときや、元のメッセージの有効期限が切れたときです。 |
| OriginalMessageId  | このフィードバック情報が関連する cloud-to-device メッセージの **MessageId** です。 |
| StatusCode         | 必須の文字列。 IoT Hub によって生成されたフィードバック メッセージで使用されます。 <br/> 'Success' <br/> 'Expired' <br/> 'DeliveryCountExceeded' <br/> 'Rejected' <br/> 'Purged' |
| 説明        | **StatusCode**の文字列値。 |
| deviceId           | フィードバックのこの要素が関連する cloud-to-device メッセージのターゲット デバイスの **DeviceId** です。 |
| DeviceGenerationId | フィードバックのこの要素が関連する cloud-to-device メッセージのターゲット デバイスの **DeviceGenerationId** です。 |

メッセージのフィードバックを元のメッセージと関連付けることができるように、サービスは C2D メッセージの **MessageId** を指定する必要があります。

次の例は、フィードバック メッセージの本文を示しています。

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

## <a name="cloud-to-device-configuration-options"></a>C2D の構成オプション

各 IoT hub では、cloud-to-device メッセージング用に次の構成オプションを公開しています。

| プロパティ                  | 説明 | 範囲と既定値 |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | C2D メッセージの TTL の既定値。 | 最大 2D の ISO_8601 書式による間隔 (最小 1 分)。 既定値は1 時間。 |
| maxDeliveryCount          | デバイスごとの C2D キューの最大配信数。 | 1 ～ 100。 既定値は10. |
| feedback.ttlAsIso8601     | サービス宛てのフィードバックのメッセージの保有期間。 | 最大 2D の ISO_8601 書式による間隔 (最小 1 分)。 既定値は1 時間。 |
| feedback.maxDeliveryCount |フィードバック キューの最大配信数。 | 1 ～ 100。 既定値は100。 |

これらの構成オプションを設定する方法の詳細については、[IoT ハブの作成](iot-hub-create-through-portal.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

cloud-to-device メッセージの受信に使用できる SDK については、[Azure IoT SDK](iot-hub-devguide-sdks.md) に関する記事をご覧ください。

cloud-to-device メッセージへの受信を試すには、[クラウドからデバイスへの送信](iot-hub-csharp-csharp-c2d.md) に関するチュートリアルをご覧ください。