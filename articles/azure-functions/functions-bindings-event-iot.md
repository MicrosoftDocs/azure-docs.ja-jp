---
title: Azure Functions の Azure IoT Hub バインド
description: Azure Functions で IoT Hub トリガーとバインドを使用する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1c25543b16c3486a8f6a445427346382faaaa09a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "77586133"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Azure Functions の Azure IoT Hub バインド

この一連の記事では、IoT Hub で Azure Functions のバインドを使用する方法について説明します。 IoT Hub のサポートは、[Azure Event Hubs のバインド](functions-bindings-event-hubs.md)に基づいています。

> [!IMPORTANT]
> 次のコード サンプルでは、Event Hub API が使用されています (使用されている構文は IoT Hub の関数に適用されるものです)。

| アクション | Type |
|--------|------|
| IoT Hub のイベント ストリームに送信されたイベントに応答する | [トリガー](./functions-bindings-event-iot-trigger.md) |
| IoT イベント ストリームにイベントを書き込む | [出力バインド](./functions-bindings-event-iot-output.md) |

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>次のステップ

- [イベント ハブのイベント ストリームに送信されたイベントに応答する (トリガー)](./functions-bindings-event-iot-trigger.md)
- [イベント ストリームにイベントを書き込む (出力バインド)](./functions-bindings-event-iot-output.md)
