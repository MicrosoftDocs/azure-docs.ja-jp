---
title: Azure IoT Hub メッセージについて | Microsoft Docs
description: 開発者ガイド - IoT Hub でのデバイスからクラウドおよびクラウドからデバイスへのメッセージ。 メッセージ形式とサポートされる通信プロトコルに関する情報が含まれています。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: e25b9d6cbe8446fb2f10150f17f7e2b0ccf85d3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92147628"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>IoT Hub で device-to-cloud および cloud-to-device メッセージを送信する

IoT Hub では、デバイスとの間で双方向通信を行うことができます。 デバイスからソリューション バックエンドにメッセージを送信し、IoT ソリューション バックエンドからデバイスにコマンドを送信することによって、デバイスと通信するには、IoT Hub メッセージングを使用します。 詳しくは、「[Azure IoT Hub メッセージの形式について](iot-hub-devguide-messages-construct.md)」をご覧ください。

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>IoT Hub への device-to-cloud メッセージの送信

IoT Hub に組み込まれているサービス エンドポイントを使用することで、バックエンド サービスはデバイスからテレメトリ メッセージを読み取ることができます。 このエンドポイントは [Event Hubs](../event-hubs/index.yml) と互換性があり、標準の IoT Hub SDK を使用して[この組み込みエンドポイントから読み取る](iot-hub-devguide-messages-read-builtin.md)ことができます。

また、IoT Hub では[カスタム エンドポイント](iot-hub-devguide-endpoints.md#custom-endpoints)もサポートされています。ユーザーはカスタム エンドポイントを定義することで、[メッセージ ルーティング](iot-hub-devguide-messages-d2c.md)を使用して、Azure サービスにデバイスのテレメトリ データとイベントを送信することができます。

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>IoT Hub からの cloud-to-device メッセージの送信

ソリューション バックエンドからデバイスに [cloud-to-device](iot-hub-devguide-messages-c2d.md) メッセージを送信することができます。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub のメッセージング機能の中心となる特性は、メッセージの信頼性と持続性です。 これらのプロパティにより、デバイス側では断続的な接続に対する復元性を、クラウド側ではイベント処理の負荷の急増に対する復元性を実現できます。 IoT Hub では、D2C および C2D メッセージングの両方について、 *少なくとも 1 回* の配信保証が実装されます。

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>適切な種類の IoT Hub メッセージングの選択

時系列のテレメトリとデバイス アプリからのアラートを送信するには device-to-cloud メッセージを使用し、デバイス アプリに一方向の通知を送信するには cloud-to-device メッセージを使用します。

* device-to-cloud メッセージ、報告プロパティ、ファイルのアップロードのどれを選択するかについては、「[device-to-cloud 通信に関するガイダンス](./iot-hub-devguide-d2c-guidance.md)」をご覧ください。

* cloud-to-device メッセージ、必要とされるプロパティ、ダイレクト メソッドのどれを選択するかについては、「[cloud-to-device 通信に関するガイダンス](./iot-hub-devguide-c2d-guidance.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

* IoT Hub の[メッセージ ルーティング](iot-hub-devguide-messages-d2c.md)について学習する。

* IoT Hub の [cloud-to-device メッセージング](iot-hub-devguide-messages-c2d.md)についてご確認ください。