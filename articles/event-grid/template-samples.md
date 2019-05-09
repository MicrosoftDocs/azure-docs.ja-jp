---
title: Azure Resource Manager テンプレートのサンプル - Event Grid | Microsoft Docs
description: Event Grid 用の Azure Resource Manager テンプレートのサンプル
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 01/04/2019
ms.author: tomfitz
ms.openlocfilehash: 788d23c7bddd90c1e12a118742c651eb9759529c
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062963"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Event Grid 用の Azure Resource Manager テンプレート

テンプレートで使用する JSON の構文とプロパティについては、「[Microsoft.EventGrid resource types (Microsoft.EventGrid のリソースの種類)](/azure/templates/microsoft.eventgrid/allversions)」を参照してください。 次の表は、Event Grid 用の Azure Resource Manager テンプレートのリンク一覧です。

| | |
|-|-|
|**Event Grid サブスクリプション**||
| [WebHook エンドポイントを使用するカスタム トピックとサブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Event Grid のカスタム トピックをデプロイします。 WebHook エンドポイントを使用するカスタム トピックへのサブスクリプションを作成します。 |
| [EventHub エンドポイントを使用するカスタム トピック サブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| カスタム トピックへの Event Grid サブスクリプションを作成します。 このサブスクリプションでは、エンドポイントに Event Hub を使用します。 |
| [Azure サブスクリプションまたはリソース グループ サブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| リソース グループまたは Azure サブスクリプションのイベントにサブスクライブします。 デプロイ時にターゲットとして指定するリソース グループがイベントのソースになります。 このサブスクリプションでは、エンドポイントに Webhook を使用します。 |
| [BLOB ストレージ アカウントとサブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Azure Blob Storage アカウントをデプロイし、そのストレージ アカウントのイベントをサブスクライブします。 |
| | |
