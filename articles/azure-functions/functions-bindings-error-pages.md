---
title: Azure Functions エラー処理ガイダンス
description: Azure Functions のエラーを処理する方法を説明し、特定のバインド エラーへのリンクを示します。
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: d301940998175adb6469e46cff2de918b5cae9df
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155277"
---
# <a name="azure-functions-error-handling"></a>Azure Functions のエラー処理

Azure Functions でエラーを処理することは、データの喪失やイベントの漏れを防ぎ、アプリケーションの正常性を監視するために重要です。

この記事では、エラー処理の一般的な方法を説明し、バインド固有のエラーへのリンクを示します。

## <a name="handling-errors"></a>エラーを処理する

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>エラー コードのバインド

Azure サービスと統合すると、基盤となるサービスの API からのエラーが発生する場合があります。 バインド固有のエラーに関する情報は、以下の記事の「**例外とリターン コード**」セクションにあります。

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
