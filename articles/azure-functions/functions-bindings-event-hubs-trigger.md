---
title: Azure Functions の Azure Event Hubs トリガー
description: Azure Functions で Azure Event Hubs トリガーを使用する方法を学習します。
author: craigshoemaker
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: c2b8302e64f7dcc657fd20ed5d918ed6816d750d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608915"
---
# <a name="azure-event-hubs-trigger-for-azure-functions"></a>Azure Functions の Azure Event Hubs トリガー

この記事では、Azure Functions で [Azure Event Hubs](../event-hubs/event-hubs-about.md) トリガーを使用する方法について説明します。 Azure Functions は、Event Hubs のトリガーおよび[出力バインド](functions-bindings-event-hubs-output.md)をサポートしています。

セットアップと構成の詳細については、[概要](functions-bindings-event-hubs.md)に関するページをご覧ください。

[!INCLUDE [functions-bindings-event-hubs-trigger](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-settings"></a>host.json 設定

[host.json](functions-host-json.md#eventhub) ファイルには、Event Hub トリガーの動作を制御する設定が含まれています。 使用可能な設定の詳細については、「[host.json 設定](functions-bindings-event-hubs.md#hostjson-settings)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [イベント ストリームにイベントを書き込む (出力バインド)](./functions-bindings-event-hubs-output.md)
