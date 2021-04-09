---
author: baanders
description: Azure Digital Twins エンドポイントを作成するために必要なリソースのファイルを含める
ms.service: digital-twins
ms.topic: include
ms.date: 1/26/2021
ms.author: baanders
ms.openlocfilehash: 58c90bae3dea0f3a47489ea7d8de6a79f823dcab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "99054511"
---
### <a name="prerequisite-create-endpoint-resources"></a>前提条件:エンドポイント リソースを作成する

エンドポイントを Azure Digital Twins にリンクするには、エンドポイントで使用するイベント グリッド トピック、イベント ハブ、または Service Bus トピックが既に存在している必要があります。

次の表を使用して、エンドポイントを作成する前に設定する必要があるリソースを確認してください。

| エンドポイントの種類 | 必要なリソース (作成手順にリンクされています) |
| --- | --- |
| Event Grid エンドポイント | [イベント グリッド トピック](../articles/event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) |
| Event Hubs エンドポイント | [Event&nbsp;Hubs&nbsp;名前空間](../articles/event-hubs/event-hubs-create.md)<br/><br/>[イベント ハブ](../articles/event-hubs/event-hubs-create.md)<br/><br/>(省略可能) キーベースの認証用の[承認規則](../articles/event-hubs/authorize-access-shared-access-signature.md) | 
| Service Bus エンドポイント | [Service Bus 名前空間](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[Service Bus トピック](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> (省略可能) キーベースの認証用の[承認規則](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature)|
