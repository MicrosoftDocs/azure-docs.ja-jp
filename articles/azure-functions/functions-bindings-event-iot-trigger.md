---
title: Azure Functions の Azure IoT Hub トリガー
description: Azure Functions で IoT Hub のイベント ストリームに送信されたイベントに応答する方法を学習します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 5c9309834b407ee56d29e38afd965ac947fc8a4f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612288"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Azure Functions の Azure IoT Hub トリガー

この記事では、IoT Hub で Azure Functions のバインドを使用する方法について説明します。 IoT Hub のサポートは、[Azure Event Hubs のバインド](functions-bindings-event-hubs.md)に基づいています。

セットアップと構成の詳細については、[概要](functions-bindings-event-iot.md)に関するページをご覧ください。

> [!IMPORTANT]
> 次のコード サンプルでは、Event Hub API が使用されています (使用されている構文は IoT Hub の関数に適用されるものです)。

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-properties"></a>host.json プロパティ

[host.json](functions-host-json.md#eventhub) ファイルには、Event Hub トリガーの動作を制御する設定が含まれています。 使用可能な設定の詳細については、「[host.json 設定](functions-bindings-event-iot.md#hostjson-settings)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [イベント ストリームにイベントを書き込む (出力バインド)](./functions-bindings-event-iot-output.md)
