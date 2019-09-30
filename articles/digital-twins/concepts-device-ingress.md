---
title: Azure Digital Twins でのデバイスの接続とテレメトリのイングレス | Microsoft Docs
description: Azure Digital Twins を使用してデバイスをオンボードする方法の概要です
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: alinast
ms.openlocfilehash: 22ae7aeeff4542bee764e131f58eb115026a4fb3
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2019
ms.locfileid: "71177108"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>デバイスの接続性とテレメトリの受信

デバイスとセンサーによって送信されるテレメトリ データが、IoT ソリューションのバックボーンを形成します。 これらのさまざまなリソースを表し、場所のコンテキスト内でそれらを管理する方法は、IoT アプリの開発における主な考慮事項です。 Azure Digital Twins は、デバイスやセンサーと空間インテリジェンス グラフを統合することにより、IoT ソリューションの開発のプロセスを簡単にします。

まず、空間グラフのルートに Azure IoT Hub のリソースを作成します。 IoT Hub のリソースにより、メッセージを送信するルート空間の下ですべてのデバイスが許可されます。 IoT ハブが作成されたら、Digital Twins のインスタンス内のセンサーにデバイスを登録します。 デバイスは、[Azure IoT device SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) 経由で Digital Twins サービスにデータを送信することができます。

デバイスをオンボードする方法の詳しい手順については、[Digital Twins のデプロイと構成に関するチュートリアル](tutorial-facilities-setup.md)の記事をご覧ください。 手順の概要は次のとおりです。

- [Azure portal](https://portal.azure.com) から Digital Twins のインスタンスをデプロイします。
- グラフに空間を作成します。
- IoT Hub リソースを作成し、グラフ内の空間に割り当てます。
- グラフ内にデバイスとセンサーを作成し、前の手順で作成した空間に割り当てます。
- 条件に基づいてテレメトリ メッセージをフィルター処理するマッチャーを作成します。
- テレメトリ メッセージのカスタム処理用の[ユーザー定義関数](concepts-user-defined-functions.md)を作成し、グラフの空間に割り当てます。
- ユーザー定義関数がグラフ データにアクセスできるように、ロールを割り当てます。
- Digital Twins Management API から IoT Hub デバイスの接続文字列を取得します。
- Azure IoT device SDK を使用してデバイスにデバイス接続文字列を構成します。

次のセクションで、Digital Twins Management API から IoT Hub デバイスの接続文字列を取得する方法について説明します。 また、IoT Hub のテレメトリ メッセージの形式を使用してセンサー ベースのテレメトリを送信する方法を説明します。 Digital Twins は、受信したテレメトリの各部分が空間グラフ内のセンサーに関連付けられることを必要とします。 この要件によって、確実にデータが処理され、適切な空間コンテキスト内でルーティングされます。

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Management API から IoT Hub デバイスの接続文字列を取得する

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

`includes=ConnectionString` パラメーターを指定して Device API で GET 呼び出しを行い、IoT Hub デバイスの接続文字列を取得します。 デバイスの GUID またはハードウェアの ID でフィルター処理を行い、特定のデバイスを検索します。

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| パラメーター | 置換後の文字列 |
| --- | --- |
| *YOUR_DEVICE_GUID* | デバイス ID |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?HardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| パラメーター値 | 置換後の文字列 |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | デバイスのハードウェア ID |

応答ペイロードで、デバイスの **connectionString** プロパティをコピーします。 これは、Azure IoT device SDK を呼び出して Digital Twins にデータを送信する際に使用します。

## <a name="device-to-cloud-message"></a>デバイスからクラウドへのメッセージ

ソリューションのニーズに合わせて、デバイスのメッセージ形式とペイロードをカスタマイズできます。 [Azure IoT Device Client Message クラスの Message(byte[] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___) によってサポートされるバイト配列またはストリームにシリアル化できる任意のデータ コントラクトを使用します。 対応するユーザー定義関数でデータ コントラクトをデコードする限り、メッセージでは任意のカスタム バイナリ形式を使用できます。 デバイスからクラウドへのメッセージの要件は 1 つだけあります。 処理エンジンにメッセージが適切にルーティングされるようにプロパティのセットを維持する必要があります。

### <a name="telemetry-properties"></a>テレメトリのプロパティ

 **Message** のペイロードの内容はサイズが最大 256 KB の任意のデータです。 [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) 型のプロパティに必要ないくつかの要件があります。 システムによってサポートされる必須および省略可能なプロパティを表に示します。

| プロパティ名 | 値 | 必須 | 説明 |
|---|---|---|---|
| **DigitalTwins-Telemetry** | 1.0 | はい | システムに対してメッセージを示す定数値。 |
| **DigitalTwins-SensorHardwareId** | `string(72)` | はい | **Message** を送信しているセンサーの一意識別子。 この値は、それを処理するシステムでのオブジェクトの **HardwareId** プロパティと一致する必要があります。 たとえば、「 `00FF0643BE88-CO2` 」のように入力します。 |
| **CreationTimeUtc** | `string` | いいえ | ペイロードのサンプリング日時を示す [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 形式の日付文字列。 たとえば、「 `2018-09-20T07:35:00.8587882-07:00` 」のように入力します。 |
| **CorrelationId** | `string` | いいえ | システム全体でイベントをトレースするために使用される UUID。 たとえば、「 `cec16751-ab27-405d-8fe6-c68e1412ce1f` 」のように入力します。

### <a name="send-your-message-to-digital-twins"></a>Digital Twins へのメッセージの送信

DeviceClient の [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) または [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) の呼び出しを使用して、メッセージを Digital Twins に送信します。

## <a name="next-steps"></a>次の手順

- Azure Digital Twins のデータ処理およびユーザー定義関数の機能については、[Azure Digital Twins でのデータ処理とユーザー定義関数](concepts-user-defined-functions.md)に関するページをご覧ください。
