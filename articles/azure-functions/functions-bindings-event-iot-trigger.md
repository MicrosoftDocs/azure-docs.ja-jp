---
title: Azure Functions の Azure IoT Hub トリガー
description: Azure Functions で IoT Hub のイベント ストリームに送信されたイベントに応答する方法を学習します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 25396da3fb9a4293633308bf2e9d3c6b3d07265d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87041640"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Azure Functions の Azure IoT Hub トリガー

この記事では、IoT Hub で Azure Functions のバインドを使用する方法について説明します。 IoT Hub のサポートは、[Azure Event Hubs のバインド](functions-bindings-event-hubs.md)に基づいています。

セットアップと構成の詳細については、[概要](functions-bindings-event-iot.md)に関するページをご覧ください。

> [!IMPORTANT]
> 次のコード サンプルでは、Event Hub API が使用されています (使用されている構文は IoT Hub の関数に適用されるものです)。

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="next-steps"></a>次のステップ

- [イベント ストリームにイベントを書き込む (出力バインド)](./functions-bindings-event-iot-output.md)
