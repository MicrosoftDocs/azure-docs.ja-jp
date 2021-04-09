---
title: Device Update for IoT Hub ネットワークの要件 |Microsoft Docs
description: Device Update for IoT Hub は、さまざまなネットワーク ポートをそれぞれの目的で使用します。
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e72ff144a56f44ccaa695b7dab328e42052fce39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101678650"
---
# <a name="ports-used-with-device-update-for-iot-hub"></a>Device Update for IoT Hub が使用するポート
ADU は、さまざまなネットワーク ポートをそれぞれの目的で使用します。

## <a name="default-ports"></a>既定のポート

目的|ポート番号 |
---|---
ネットワーク/CDN からのダウンロード  | 80 (HTTP プロトコル)
MCC/CDN からのダウンロード | 80 (HTTP プロトコル)
Azure IoT Hub への ADU エージェント接続  | 8883 (MQTT プロトコル)

## <a name="use-azure-iot-hub-supported-protocols"></a>IoT Hub でサポートされるプロトコルの使用
ADU エージェントは、サポートされている Azure IoT Hub プロトコルを使用するように変更することができます。

サポートされているプロトコルの現在の一覧については、[こちら](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols#:~:text=Table%202%20%20%20,%201%20more%20rows)を参照してください。
