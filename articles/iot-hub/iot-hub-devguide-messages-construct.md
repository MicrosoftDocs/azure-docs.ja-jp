---
title: Azure IoT Hub メッセージの形式について | Microsoft Docs
description: 開発者ガイド - IoT Hub メッセージの形式と予期される内容について説明します。
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: e2aafa195fa463a405e2132cd41fada8d6903961
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450082"
---
# <a name="create-and-read-iot-hub-messages"></a>IoT Hub メッセージを作成し、読み取る

プロトコル間でのシームレスな相互運用性をサポートするために、IoT Hub では、すべてのデバイス用プロトコルの共通のメッセージ形式が定義されています。 このメッセージ形式は、[device-to-cloud ルーティング](iot-hub-devguide-messages-d2c.md) メッセージと [cloud-to-device](iot-hub-devguide-messages-c2d.md) メッセージの両方で使用されます。 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub は、ストリーミング メッセージング パターンを使用して、D2C メッセージングを実装しています。 IoT Hub の device-to-cloud メッセージは、[Service Bus](/azure/service-bus-messaging/) の "*メッセージ*" というよりはむしろ [Event Hubs](/azure/event-hubs/) の "*イベント*" であり、複数のリーダーで読み取り可能なサービスを経由する、大量のイベントが存在します。

IoT Hub メッセージは、次のような構成です。

* 事前定義された一連の "*システム プロパティ*" を以下に示します。

* 一連の *アプリケーション プロパティ*。 メッセージ本文を逆シリアル化しなくてもアプリケーションが定義してアクセスできる、文字列プロパティのディクショナリです。 IoT Hub によってこれらのプロパティが変更されることはありません。

* 非透過的なバイナリ本文。

HTTPS プロトコルを使用して device-to-cloud メッセージを送信するとき、または cloud-to-device メッセージを送信するときに、プロパティ名と値に含めることができるのは、ASCII 英数字と ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` のみです。

IoT Hub を使用した device-to-cloud メッセージングには、次のような特徴があります。

* device-to-cloud メッセージには持続性があり、最長で 7 日間、IoT hub の既定の **messages/events** エンドポイントに保持されます。

* device-to-cloud メッセージは最大 256 KB で、バッチとしてグループ化して送信を最適化できます。 バッチは最大で 256 KB です。

* IoT Hub では、任意のパーティション分割は許可されていません。 D2C メッセージは、発信元の **deviceId**に基づいてパーティション分割されます。

* 「[IoT Hub へのアクセスの制御](iot-hub-devguide-security.md)」で説明されているように、IoT Hub ではデバイスごとに認証とアクセス制御を行うことができます。

* アプリケーション プロパティに移動する情報をメッセージにスタンプできます。 詳細については、[メッセージ エンリッチメント](iot-hub-message-enrichments-overview.md)に関するページを参照してください。

各種プロトコルを使用したメッセージのエンコードとデコードの方法の詳細については、[Azure IoT SDK](iot-hub-devguide-sdks.md) に関するページをご覧ください。

次のテーブルは、IoT Hub メッセージ内の一連のシステム プロパティです。

| プロパティ | 説明 | ユーザーが設定可能 |
| --- | --- | --- |
| message-id |要求/応答パターンに使用する、メッセージのユーザー設定 ID。 形式:ASCII 7 ビット英数字の大文字と小文字が区別される文字列 (最大 128 文字) + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`。 | はい |
| sequence-number |IoT Hub によって各 C2D メッセージに割り当てられる数値 (デバイスとキューごとに一意)。 | C2D メッセージの場合は「いいえ」。それ以外の場合は「はい」。 |
| to |[C2D](iot-hub-devguide-c2d-guidance.md) メッセージで指定される宛先。 | C2D メッセージの場合は「いいえ」。それ以外の場合は「はい」。 |
| absolute-expiry-time |メッセージの有効期限の日時。 | はい |
| iothub-enqueuedtime |IoT Hub が [Device-to-Cloud](iot-hub-devguide-d2c-guidance.md) メッセージを受信した日時。 | D2C メッセージの場合は「いいえ」。それ以外の場合は「はい」。 |
| correlation-id |通常、要求/応答パターンで要求の MessageId を格納する、応答メッセージの文字列プロパティ。 | はい |
| user-id |メッセージの送信元を指定するために使用される ID。 IoT Hub でメッセージが生成されると、 `{iot hub name}`に設定されます。 | いいえ |
| iothub-ack |フィードバック メッセージのジェネレーター。 このプロパティは、デバイスがメッセージを使用した結果としてのフィードバック メッセージの生成を IoT Hub に要求するために、C2D メッセージで使用されます。 使用可能な値: **none** (既定値): フィードバック メッセージは生成されません。**positive**: メッセージが完了した場合にフィードバック メッセージを受信します。**negative**: デバイスでメッセージが完了しないまま、メッセージの有効期限が切れた場合 (または最大配信数に達した場合) にフィードバック メッセージを受信します。**full**: positive と negative の両方の値を意味します。 <!-- robinsh For more information, see [Message feedback][lnk-feedback].--> | はい |
| iothub-connection-device-id |IoT Hub で D2C メッセージに対して設定される ID。 メッセージを送信したデバイスの **deviceId** が含まれます。 | D2C メッセージの場合は「いいえ」。それ以外の場合は「はい」。 |
| iothub-connection-auth-generation-id |IoT Hub で D2C メッセージに対して設定される ID。 ( **デバイス ID のプロパティ** に従って) メッセージを送信したデバイスの [generationId](iot-hub-devguide-identity-registry.md#device-identity-properties)が含まれています。 | D2C メッセージの場合は「いいえ」。それ以外の場合は「はい」。 |
| iothub-connection-auth-method |IoT Hub で D2C メッセージに対して設定される認証方法。 このプロパティには、メッセージを送信するデバイスの認証に使用する認証方法に関する情報が含まれます。 <!-- ROBINSH For more information, see [Device to cloud anti-spoofing][lnk-antispoofing].--> | D2C メッセージの場合は「いいえ」。それ以外の場合は「はい」。 |
| iothub-creation-time-utc | デバイスでメッセージが作成された日時。 デバイスでこの値を明示的に設定する必要があります。 | はい |

## <a name="message-size"></a>メッセージ サイズ

IoT Hub では、メッセージのサイズは、プロトコルに関係なく実際のペイロードのみを考慮して測定されます。 バイト単位のサイズは次の和として計算されます。

* 本文のサイズ (バイト単位)
* メッセージ システム プロパティの全値のサイズ (バイト単位)。
* すべてのユーザー プロパティの名前と値のサイズ (バイト単位)

プロパティの名前と値は ASCII 文字に制限されているため、文字列の長さがバイト単位のサイズと等しくなります。

## <a name="anti-spoofing-properties"></a>なりすまし対策のプロパティ

D2C メッセージでのデバイスのなりすましを回避するために、IoT Hub では、すべてのメッセージに次のプロパティを持つスタンプが使用されます。

* **iothub-connection-device-id**
* **iothub-connection-auth-generation-id**
* **iothub-connection-auth-method**

最初の 2 つには、「[デバイス ID プロパティ](iot-hub-devguide-identity-registry.md#device-identity-properties)」で説明されている発信元デバイスの **deviceId** と **generationId** が含まれています。

**iothub-connection-auth-method** プロパティには、次のプロパティを使用してシリアル化された JSON オブジェクトが含まれています。

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>次の手順

* IoT Hub でのメッセージ サイズの制限については、[IoT Hub のクォータと調整](iot-hub-devguide-quotas-throttling.md)に関するページをご覧ください。

* IoT Hub メッセージをさまざまなプログラミング言語で作成し、読み取る方法については、[クイック スタート](quickstart-send-telemetry-node.md)をご覧ください。
