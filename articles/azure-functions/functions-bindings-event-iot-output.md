---
title: Azure Functions の Azure IoT Hub 出力バインド
description: Azure Functions を使用して Azure IoT Hub ストリームにメッセージを書き込む方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91871781"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>Azure Functions の Azure IoT Hub 出力バインド

この記事では、IoT Hub 用に Azure Functions 出力バインドを使用する方法について説明します。 IoT Hub のサポートは、[Azure Event Hubs のバインド](functions-bindings-event-hubs.md)に基づいています。

セットアップと構成の詳細については、[概要](functions-bindings-event-iot.md)に関するページをご覧ください。

> [!IMPORTANT]
> 次のコード サンプルでは、Event Hub API が使用されています (使用されている構文は IoT Hub の関数に適用されるものです)。

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>次のステップ

- [イベント ハブのイベント ストリームに送信されたイベントに応答する (トリガー)](./functions-bindings-event-iot-trigger.md)
