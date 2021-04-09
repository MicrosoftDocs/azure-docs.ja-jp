---
title: Azure IoT Hub のエラー 429001 ThrottlingException のトラブルシューティング
description: エラー 429001 ThrottlingException を修正する方法の概要
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d2f12a6982886eeaa375151c5b8a73acc573aab9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92545363"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

この記事では、**429001 ThrottlingException** エラーの原因と解決策について説明します。

## <a name="symptoms"></a>現象

IoT Hub に対する要求がエラー **429001 ThrottlingException** で失敗します。

## <a name="cause"></a>原因

要求された操作で IoT Hub の[調整制限](./iot-hub-devguide-quotas-throttling.md)を超えました。

## <a name="solution"></a>解決策

"*Telemetry message send attempts*" (テレメトリ メッセージ送信試行数) メトリックを前述の指定した制限と比較して、調整制限に達しているかどうかを確認します。 また、"*Number of throttling errors*" (調整エラーの数) メトリックも確認できます。 これらのメトリックの詳細については、「[デバイス テレメトリのメトリック](monitor-iot-hub-reference.md#device-telemetry-metrics)」を参照してください。 メトリックを使用して IoT ハブを監視する方法の詳細については、「[IoT Hub の監視](monitor-iot-hub.md)」を参照してください。

IoT Hub から 429 ThrottlingException が返されるのは、制限に違反した期間が長すぎる場合のみです。 これは、IoT ハブのトラフィックがバーストした場合、メッセージが削除されないようにするためです。 一方、IoT Hub では操作スロットル レートでメッセージが処理されるため、バックログで大量のトラフィックがあると、速度が低下する可能性があります。 詳細については、[IoT Hub のトラフィック シェイプ](./iot-hub-devguide-quotas-throttling.md#traffic-shaping)に関するセクションを参照してください。

## <a name="next-steps"></a>次のステップ

クォータまたはスロットルの上限に達している場合は、[IoT ハブのスケールアップ](./iot-hub-scaling.md)を検討してください