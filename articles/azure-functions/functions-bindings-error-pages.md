---
title: Azure Functions のエラー処理と再試行のガイダンス
description: Azure Functions のエラーと再試行イベントを処理する方法を説明し、特定のバインド エラーへのリンクを示します。
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: cshoe
ms.openlocfilehash: e52babd5b0612b4590f1422a941b25ba30c3f169
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93284457"
---
# <a name="azure-functions-error-handling-and-retries"></a>Azure Functions のエラー処理と再試行

Azure Functions でエラーを処理することは、データの喪失やイベントの漏れを防ぎ、アプリケーションの正常性を監視するために重要です。

この記事では、エラー処理の一般的な方法を説明し、バインド固有のエラーへのリンクを示します。

## <a name="handling-errors"></a>エラーの処理

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-retries.md)]

## <a name="binding-error-codes"></a>エラー コードのバインド

Azure サービスと統合すると、基盤となるサービスの API からのエラーが発生する場合があります。 バインド固有のエラーに関する情報は、以下の記事の「**例外とリターン コード**」セクションにあります。

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT Hub](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table-output.md#exceptions-and-return-codes)
