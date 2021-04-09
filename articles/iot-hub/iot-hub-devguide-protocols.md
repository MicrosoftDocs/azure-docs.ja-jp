---
title: Azure IoT Hub の通信プロトコルとポート | Microsoft Docs
description: 開発者ガイド - サポートされている device-to-cloud と cloud-to-device 通信の通信プロトコルと、開く必要があるポート番号について説明します。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: e2578b47d27ef062d83ba8621a49e9a8f439897c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919027"
---
# <a name="reference---choose-a-communication-protocol"></a>リファレンス - 通信プロトコルの選択

IoT Hub によって、デバイスはデバイス側の通信に次のプロトコルを使うことができるようになります。

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* WebSocket 経由の MQTT
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP over WebSocket
* HTTPS

これらのプロトコルがサポートしている具体的な IoT Hub 機能については、「[device-to-cloud 通信に関するガイダンス](iot-hub-devguide-d2c-guidance.md)」と「[cloud-to-device 通信に関するガイダンス](iot-hub-devguide-c2d-guidance.md)」をご覧ください。

プロトコル選択に関する高度な推奨事項には次のようなものがあります。

| Protocol | そのプロトコルを選択しなければならない場合 |
| --- | --- |
| MQTT <br> WebSocket 経由の MQTT |同じ TLS 接続で (それぞれがデバイス独自の資格情報を有する) 複数のデバイスに接続する必要がないすべてのデバイスで使用します。 |
| AMQP <br> WebSocket 経由の AMQP |デバイスを多重化する接続を活用するために、フィールド ゲートウェイとクラウド ゲートウェイで使用します。 |
| HTTPS |その他のプロトコルをサポートできないデバイスに使用します。 |

デバイス側の通信用プロトコルを選択する場合、次の点を考慮してください。

* **C2D のパターン**。 HTTPS には、サーバー プッシュを実装する効率的な方法がありません。 そのため、HTTPS を使うと、デバイスは IoT Hub に対して C2D メッセージのポーリングを行います。 この方法は、デバイスと IoT Hub の両方で非効率的です。 現在の HTTPS ガイドラインでは、各デバイスによるメッセージのポーリングの間隔は 25 分以上となっています。 HTTPS 受信の発行が多くなれば、IoT Hub で要求が調整されます。 MQTT と AMQP では、C2D メッセージを受信する場合のサーバー プッシュがサポートされています。 IoT Hub からデバイスへのメッセージも即座にプッシュできます。 配信の待機時間が問題となる場合は、MQTT または AMQP が使用に最適なプロトコルです。 頻繁に接続されないデバイスの場合は、HTTPS でも対応できます。

* **フィールド ゲートウェイ**。 MQTT と HTTPS でサポートされるデバイス ID (デバイス ID と資格情報) は、TLS 接続ごとに 1 つだけです。 このような理由から、IoT Hub への単一のアップストリーム接続またはそのプール全体で複数のデバイス ID を使用したメッセージの多重化を必要とする[フィールド ゲートウェイのシナリオ](iot-hub-devguide-endpoints.md#field-gateways)では、これらのプロトコルがサポートされません。 そのようなゲートウェイでは、アップストリーム トラフィックの接続ごとに複数のデバイス ID をサポートするプロトコル (AMQP など) を使用してください。

* **リソースの少ないデバイス**。 MQTT および HTTPS ライブラリのフットプリントは、AMQP ライブラリの場合より小さくなります。 そのため、リソースが限られたデバイス (RAM が 1 MB 未満など) の場合、MQTT と HTTP/1 が実装可能な唯一のプロトコルとなります。

* **ネットワーク トラバーサル**。 標準の AMQP プロトコルはポート 5671 を使用し、MQTT はポート 8883 をリッスンします。 これらのポートを使うと、ネットワークが HTTPS 以外のプロトコルに限定されている場合、問題が発生する可能性があります。 このようなシナリオの場合は、MQTT over WebSockets、AMQP over WebSockets、または HTTPS を使ってください。

* **ペイロードのサイズ**。 MQTT と AMQP はバイナリ プロトコルであり、HTTPS よりもペイロードが小さくなります。

> [!WARNING]
> HTTPS を利用する場合、各デバイスがクラウドからデバイスへのメッセージを確認する間隔はせいぜい 25 分に 1 回にしてください。 開発中は、必要に応じて、各デバイスでもっと頻繁にポーリングできます。

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="port-numbers"></a>ポート番号

デバイスは、さまざまなプロトコルを使用して Azure の IoT Hub と通信できます。 通常、プロトコルの選択は、ソリューションの特定の要件によって左右されます。 次の表に、デバイスが特定のプロトコルを使用できるようにするために開く必要がある送信ポートを示します。

| Protocol | Port |
| --- | --- |
| MQTT |8883 |
| WebSocket 経由の MQTT |443 |
| AMQP |5671 |
| AMQP over WebSocket |443 |
| HTTPS |443 |

Azure リージョンに IoT Hub を作成すると、その IoT Hub は IoT Hub の存続期間中同じ IP アドレスを保持します。 ただし、サービス品質を維持するため、Microsoft によって IoT Hub が別のスケール ユニットに移動されると、新しい IP アドレスが割り当てられます。

## <a name="next-steps"></a>次のステップ

IoT Hub による MQTT プロトコルの実装方法の詳細については、[「MQTT プロトコルを使用した IoT Hub との通信」をご覧ください](iot-hub-mqtt-support.md)。
