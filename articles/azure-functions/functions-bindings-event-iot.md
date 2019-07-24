---
title: Azure Functions の Azure IoT Hub バインド
description: Azure Functions で Azure IoT Hub のバインドを使用する方法について説明します。
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 87a99651d17e9867130024a5f721c5488cd42a8c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59426105"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Azure Functions の Azure IoT Hub バインド

この記事では、IoT Hub で Azure Functions のバインドを使用する方法について説明します。 IoT Hub のサポートは、[Azure Event Hubs のバインド](functions-bindings-event-hubs.md)に基づいています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>パッケージ - Functions 1.x

Azure Functions バージョン 1.x の場合、IoT Hub バインディングは [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet パッケージ、バージョン 2.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) GitHub リポジトリにあります。

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>パッケージ - Functions 2.x

Functions 2.x の場合、[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) パッケージ、バージョン 3.x を使用します。 パッケージのソース コードは、[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) GitHub リポジトリにあります。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

> [!IMPORTANT]
> 次のコード サンプルでは、Event Hub API が使用されています (使用されている構文は IoT Hub の関数に適用されるものです)。

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)
