---
title: Azure IoT Hub の cloud-to-device メッセージの理解 | Microsoft Docs
description: この開発者ガイドでは、IoT ハブでの cloud-to-device メッセージングの使用方法について説明します。 これには、メッセージのライフサイクルおよび構成オプションに関する情報が含まれています。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.custom: mqtt, devx-track-azurecli
ms.openlocfilehash: 5515d1084b28091cf7d20958cfca8af3f2664563
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199494"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>IoT ハブから cloud-to-device メッセージを送信する

ソリューション バックエンドから、デバイス アプリに一方向の通知を送信するには、IoT ハブからデバイスに cloud-to-device メッセージを送信します。 Azure IoT Hub でサポートされるその他の cloud-to-device オプションの詳細については、「[cloud-to-device 通信に関するガイダンス](iot-hub-devguide-c2d-guidance.md)」を参照してください。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

cloud-to-device メッセージはサービス向けエンドポイントの */messages/devicebound* を介して送信します。 その後、デバイスではそのメッセージを、デバイス固有のエンドポイントの */devices/{deviceId}/messages/devicebound* を介して受信します。

各 cloud-to-device メッセージのターゲットを単一のデバイスにするために、IoT ハブでは、**to** プロパティが */devices/{deviceId}/messages/devicebound* に設定されます。

各デバイスのキューでは、最大 50 個の cloud-to-device メッセージが保持されます。 同じデバイスにそれ以上のメッセージを送信しようとすると、エラーが発生します。

## <a name="the-cloud-to-device-message-life-cycle"></a>cloud-to-device メッセージのライフ サイクル

少なくとも 1 回のメッセージ配信を保証するために、IoT ハブでは cloud-to-device メッセージがデバイスごとのキューに保持されます。 IoT ハブでキューからメッセージを削除するためには、デバイスで明示的に *完了* を確認する必要があります。 このような方法で、接続とデバイスの障害に対する復元性が保証されます。

次の図には、ライフ サイクルの状態グラフが示されています。

![cloud-to-device メッセージのライフ サイクル](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

IoT ハブ サービスでは、デバイスにメッセージを送信するときに、メッセージの状態が *エンキュー* に設定されます。 デバイスでメッセージを *受信する* 必要がある場合、IoT ハブで、状態が *非表示* に設定されると、メッセージが *ロック* されます。 この状態では、デバイス上の他のスレッドで他のメッセージの受信を開始することが許可されます。 デバイスのスレッドでは、メッセージの処理を完了するときに、メッセージを *完了* することで IoT ハブに通知します。 その後、IoT ハブで状態が *完了* に設定されます。

デバイスは次の動作をすることもあります。

* メッセージの *拒否*。この場合、IoT ハブによってメッセージの状態が *配信不能* に設定されます。 Message Queuing Telemetry Transport (MQTT) プロトコル経由で接続するデバイスでは、cloud-to-device メッセージを拒否することはできません。

* メッセージの *破棄*。この場合、IoT ハブによってメッセージがキューに戻され、状態が *エンキュー* に設定されます。 MQTT プロトコル経由で接続するデバイスでは、cloud-to-device メッセージを破棄することはできません。

スレッドでは、IoT ハブに通知することなく、メッセージの処理に失敗することもあります。 この場合、*表示* がタイムアウト (または *ロック* がタイムアウト) になった後で、メッセージの状態が自動的に *非表示* から *エンキュー* に戻ります。 このタイムアウトの値は 1 分であり、変更することはできません。

IoT ハブの **最大配信数** プロパティによって、メッセージの状態を *エンキュー* と *非表示* の間で切り替えることができる最大数が決まります。 その切り替えの回数に達した後、IoT ハブによってメッセージの状態が *配信不能* に設定されます。 同様に、有効期限が切れた後も、IoT ハブによってメッセージの状態が *配信不能* に設定されます。 詳細については、[有効期限](#message-expiration-time-to-live)に関する記述を参照してください。

[cloud-to-device メッセージを IoT Hub で送信する方法](iot-hub-csharp-csharp-c2d.md)に関する記事では、cloud-to-device メッセージを、クラウドから送信してデバイスで受信する方法が示されています。

メッセージの損失がアプリケーション ロジックに影響しない場合は、通常、デバイスで cloud-to-device メッセージが完了されます。 たとえば、デバイスでメッセージの内容がローカルに保持された場合や、正常に操作が実行された場合などです。 また、メッセージには、メッセージの損失がアプリケーションの機能に影響しないことを示す一時的な情報が含まれている場合もあります。 タスクの実行時間が長いときに、場合によっては次の操作を実行できます。

* デバイスでタスクの説明がローカル ストレージに保持された後で、cloud-to-device メッセージを完了する。

* タスクの進行状況のさまざまな段階で、1 つ以上の D2C メッセージによりソリューション バックエンドに通知する

## <a name="message-expiration-time-to-live"></a>メッセージの有効期限

すべての C2D メッセージに有効期限があります。 この期限は、次のいずれかで設定されます。

* サービスの **ExpiryTimeUtc** プロパティ
* IoT ハブ。IoT ハブ プロパティとして指定された既定の *有効期限* を使用します

「[C2D の構成オプション](#cloud-to-device-configuration-options)」を参照してください。

メッセージの有効期限を利用し、接続されていないデバイスにメッセージが送信されないようにする一般的な方法は、短い *有効期限* の値を設定することです。 この方法を使用すると、デバイスの接続状態を維持するのと同じ結果が得られますが、より効率的です。 メッセージの受信確認を要求すると、IoT ハブによって、どのデバイスが次の状態であるかが通知されます。

* メッセージを受信できる
* オンラインでないか、失敗した

## <a name="message-feedback"></a>メッセージのフィードバック

cloud-to-device メッセージを送信するときに、サービスでは、そのメッセージの最終状態についてのメッセージごとのフィードバックの配信を要求できます。 これを行うには、以下の 4 つの値のいずれかに、送信される cloud-to-device メッセージの **iothub-ack** アプリケーション プロパティを設定します。

| Ack プロパティ値 | 動作 |
| ------------ | -------- |
| なし     | IoT ハブでは、フィードバック メッセージは生成されません (既定の動作)。 |
| 肯定的 | cloud-to-device メッセージの状態が *完了* に達した場合に、IoT ハブによってフィードバック メッセージが生成されます。 |
| 否定的 | cloud-to-device メッセージの状態が *配信不能* に達した場合に、IoT ハブによってフィードバック メッセージが生成されます。 |
| フル     | IoT ハブでは、いずれの場合もフィードバック メッセージが生成されます。 |

**Ack** 値が *フル* のときに、フィードバック メッセージを受信しなかった場合、フィードバック メッセージの有効期限が切れていることを意味します。 このとき、元のメッセージがどうなったかをサービスが認識することはできません。 実際には、有効期限切れになる前にフィードバックをサービスが確実に処理できることが必要です。 有効期限は最長 2 日間であるため、障害が発生した場合も、サービスを再び稼働させる時間はあります。

[エンドポイント](iot-hub-devguide-endpoints.md)に関するページで説明されているように、IoT ハブではサービス向けエンドポイントの */messages/servicebound/feedback* 経由でメッセージとしてフィードバックを配信します。 フィードバックを受信するためのセマンティクスは C2D メッセージの場合と同様です。 可能な限り、メッセージのフィードバックは、次の形式で 1 つのメッセージのバッチとして処理します。

| プロパティ     | 説明 |
| ------------ | ----------- |
| EnqueuedTime | フィードバック メッセージがハブで受信された日時を示すタイムスタンプ |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

本文はシリアル化された JSON レコードの配列で、それぞれ次のプロパティを持っています。

| プロパティ           | 説明 |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | メッセージの結果が発生した日時を示すタイムスタンプ (たとえば、ハブでフィードバック メッセージが受信されたときや、元のメッセージの期限が切れたとき) |
| OriginalMessageId  | このフィードバック情報が関連する cloud-to-device メッセージの *MessageId* |
| StatusCode         | IoT ハブによって生成されるフィードバック メッセージで使用される、必須の文字列。 <br/> *Success* <br/> *Expired* <br/> *DeliveryCountExceeded* <br/> *拒否* <br/> *Purged* |
| 説明        | *StatusCode* の文字列値 |
| deviceId           | フィードバックのこの要素が関連する cloud-to-device メッセージのターゲット デバイスの *DeviceId* |
| DeviceGenerationId | フィードバックのこの要素が関連する cloud-to-device メッセージのターゲット デバイスの *DeviceGenerationId* |

cloud-to-device メッセージのフィードバックを元のメッセージと関連付けるには、サービスで *MessageId* を指定する必要があります。

次のコードには、フィードバック メッセージの本文が示されています。

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

**削除されたデバイスに対する保留中のフィードバック**

デバイスが削除されると、保留中のフィードバックも削除されます。 デバイスのフィードバックはバッチで送信されます。 デバイスがメッセージの受信を確認してから次のフィードバック バッチが準備されるまでの短い期間 (多くの場合、1 秒未満) の間にデバイスが削除された場合、フィードバックは発生しません。

デバイスを削除する前に、保留中のフィードバックが到着するまでしばらく待つことによって、この動作に対処できます。 デバイスが削除されると、関連するメッセージ フィードバックは失われたと見なされます。

## <a name="cloud-to-device-configuration-options"></a>C2D の構成オプション

各 IoT hub では、cloud-to-device メッセージング用に次の構成オプションを公開しています。

| プロパティ                  | 説明 | 範囲と既定値 |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | cloud-to-device メッセージの既定の TTL | 最大 2 日の ISO_8601 書式による間隔 (最小 1 分); 既定値: 1 時間 |
| maxDeliveryCount          | デバイスごとの cloud-to-device キューの最大配信数 | 1 から 100; 既定値: 10 |
| feedback.ttlAsIso8601     | サービス宛てのフィードバック メッセージの保有期間 | 最大 2 日の ISO_8601 書式による間隔 (最小 1 分); 既定値: 1 時間 |
| feedback.maxDeliveryCount | フィードバック キューの最大配信数 | 1 から 100; 既定値: 10 |
| feedback.lockDurationAsIso8601 | フィードバック キューの最大配信数 | 5 から 300 秒の ISO_8601 書式による間隔 (最短 5 秒)。既定値: 60 秒。 |

構成オプションは、次のいずれかの方法で設定できます。

* **Azure portal**: IoT ハブの **[設定]** で、 **[組み込みのエンドポイント]** を選択し、 **[cloud-to-device メッセージング]** を展開します。 (**feedback.maxDeliveryCount** および **feedback.lockDurationAsIso8601** プロパティの設定は、Azure portal では現在サポートされていません。)

    ![ポータルでの cloud-to-device メッセージングの構成オプションの設定](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Azure CLI**: [az iot hub update](/cli/azure/iot/hub#az-iot-hub-update) コマンドを使用します。

    ```azurecli
    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.defaultTtlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.ttlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.lockDurationAsIso8601=PT0H1M0S
    ```

## <a name="next-steps"></a>次のステップ

cloud-to-device メッセージの受信に使用できる SDK については、[Azure IoT SDK](iot-hub-devguide-sdks.md) に関する記事をご覧ください。

cloud-to-device メッセージへの受信を試すには、[クラウドからデバイスへの送信](iot-hub-csharp-csharp-c2d.md) に関するチュートリアルをご覧ください。
