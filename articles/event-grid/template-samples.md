---
title: Azure Resource Manager テンプレートのサンプル - Event Grid | Microsoft Docs
description: この記事では、GitHub にある一連の Azure Event Grid 用 Azure Resource Manager テンプレート サンプルを紹介しています。
ms.topic: sample
ms.date: 07/07/2020
ms.openlocfilehash: 910012adf2dc930e6f1a26f1a7fc41f5ed0580c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86119057"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Event Grid 用の Azure Resource Manager テンプレート

テンプレートで使用する JSON の構文とプロパティについては、「[Microsoft.EventGrid resource types (Microsoft.EventGrid のリソースの種類)](/azure/templates/microsoft.eventgrid/allversions)」を参照してください。 次の表は、Event Grid 用の Azure Resource Manager テンプレートのリンク一覧です。

## <a name="event-grid-subscriptions"></a>Event Grid サブスクリプション
- [Webhook エンドポイントを使用するカスタム トピックとサブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) - Event Grid のカスタム トピックをデプロイします。 WebHook エンドポイントを使用するカスタム トピックへのサブスクリプションを作成します。 
- [EventHub エンドポイントを使用するカスタム トピック サブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler) - カスタム トピックへの Event Grid サブスクリプションを作成します。 このサブスクリプションでは、エンドポイントに Event Hub を使用します。 
- [Azure サブスクリプションまたはリソース グループ サブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) - リソース グループまたは Azure サブスクリプションのイベントをサブスクライブします。 デプロイ時にターゲットとして指定するリソース グループがイベントのソースになります。 このサブスクリプションでは、エンドポイントに Webhook を使用します。 
- [BLOB ストレージ アカウントとサブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) - Azure Blob Storage アカウントをデプロイし、そのストレージ アカウントのイベントをサブスクライブします。 

## <a name="next-steps"></a>次のステップ
次のサンプルを参照してください。

- [PowerShell のサンプル](powershell-samples.md)
- [CLI のサンプル](cli-samples.md)
